## redux

### redux-toolkit

#### configureStore

1. 集成了 `redux-thunk` 中间件，支持  dispatch action 异步调用。
2. `combineReducer`

#### createSlice

1. 它负责生成 action 类型字符串、action creator 函数和 action 对象。你所要做的就是为这个 slice 定义一个名称，编写一个包含 reducer 函数的对象，它会自动生成相应的 action 代码。`name` 选项的字符串用作每个 action 类型的第一部分，每个 reducer 函数的键名用作第二部分。
2. Reducer 中必需要先创建原始值的副本，然后可以改变副本。

```ts
// ✅ 这样操作是安全的，因为创建了副本
return {
  ...state,
  value: 123
}
```

> createSlice 内部使用了一个名为 Immer 的库。 Immer 使用一种称为 `Proxy` 的特殊 JS 工具来包装你提供的数据，当你尝试更改这些数据的时候，`Immer` 会跟踪你尝试进行的所有更改，然后使用该更改列表返回一个安全的、不可变的更新值，就好像你手动编写了所有不可变的更新逻辑一样。

```ts
// old
function handwrittenReducer(state, action) {
  return {
    ...state,
    first: {
      ...state.first,
      second: {
        ...state.first.second,
        [action.someId]: {
          ...state.first.second[action.someId],
          fourth: action.someValue
        }
      }
    }
  }
}

// new
function reducerWithImmer(state, action) {
  state.first.second[action.someId].fourth = action.someValue
}
```

3. createSlice 允许我们在编写 reducer 时定义一个 `prepare` 函数。 `prepare` 函数可以接受多个参数，生成诸如唯一 ID 之类的随机值，并运行需要的任何其他同步逻辑来决定哪些值进入 action 对象。然后它应该返回一个包含 payload 字段的对象。

```ts
const postsSlice = createSlice({
  name: 'posts',
  initialState,
  reducers: {
    postAdded: {
      reducer(state, action) {
        state.push(action.payload)
      },
      prepare(title, content) {
        return {
          payload: {
            id: nanoid(),
            title,
            content
          }
        }
      }
    }
    // other reducers here
  }
})
```

4. `extraReducers`  
   有时 slice 的 reducer 需要响应 没有 定义到该 slice 的 reducers 字段中的 action。这个时候就需要使用 slice 中的 extraReducers 字段。

```ts
export const fetchPosts = createAsyncThunk('posts/fetchPosts', async () => {
  const response = await client.get('/fakeApi/posts')
  return response.data
})

const postsSlice = createSlice({
  name: 'posts',
  initialState,
  reducers: {
    // omit existing reducers here
  },
  extraReducers(builder) {
    builder
      .addCase(fetchPosts.pending, (state, action) => {
        state.status = 'loading'
      })
      .addCase(fetchPosts.fulfilled, (state, action) => {
        state.status = 'succeeded'
        // Add any fetched posts to the array
        state.posts = state.posts.concat(action.payload)
      })
      .addCase(fetchPosts.rejected, (state, action) => {
        state.status = 'failed'
        state.error = action.error.message
      })
  }
})
```

#### createAsyncThunk

`createAsyncThunk` 接受一个 payload creator 回调函数，它应该返回一个 Promise，并自动生成 pending/fulfilled/rejected action 类型。  
接收 2 个参数:

- 将用作生成的 action 类型的前缀的字符串
- 一个 payload creator 回调函数，它应该返回一个包含一些数据的 Promise，或者一个被拒绝的带有错误的 Promise

```ts
export const fetchPosts = createAsyncThunk('posts/fetchPosts', async () => {
  const response = await client.get('/fakeApi/posts')
  return response.data
})
```

### redux-persist

配置可持续化

```ts
// store.ts
import { persistReducer, persistStore } from "redux-persist";
const persitConfig = {
  key:"root",
  storage: storage,
  // 如果不想将部分state持久化，可以将其放入黑名单(blacklist)中.黑名单是设置
  // blacklist: []
}
const persist_reducer = persistReducer(persitConfig, reducer)
const store =  configureStore({
  reducer: {
    root: persist_reducer,
  }
})
export const persistor =  persistStore(store);

// App.tsx
import { Provider } from 'react-redux';
import { PersistGate } from 'redux-persist/lib/integration/react';
import store, { persistor } from '@redux/store'
<Provider store={store}>
  <PersistGate loading={null} persistor={persistor}>
    // ...
  </PersistGate>
</Provider>
```

### RTK Query

#### 核心api

- `createApi()`  
  RTK Query 功能的核心。它允许你定义一组请求接口来描述如何从一系列请求接口检索数据，包括如何获取和转换该数据的配置。
- `fetchBaseQuery()`  
  fetch 的一个小包装 -US/docs/Web/API/Fetch_API），旨在简化请求。旨在为大多数用户在 createApi 中使用推荐的 baseQuery。

#### 定义 API Slice

```ts
// 从特定于 React 的入口点导入 RTK Query 方法
import { createApi, fetchBaseQuery } from '@reduxjs/toolkit/query/react'

// 定义我们的单个 API Slice 对象
export const apiSlice = createApi({
  // 缓存减速器预计将添加到 `state.api` （已经默认 - 这是可选的）
  reducerPath: 'api',
  // 我们所有的请求都有以 “/fakeApi” 开头的 URL
  baseQuery: fetchBaseQuery({ baseUrl: '/fakeApi' }),
  // “endpoints” 代表对该服务器的操作和请求
  endpoints: builder => ({
    // `getPosts` endpoint 是一个返回数据的 “Query” 操作
    getPosts: builder.query({
      // 请求的 URL 是“/fakeApi/posts”
      query: () => '/posts'
    })
  })
})

// 为 `getPosts` Query endpoint 导出自动生成的 hooks
export const { useGetPostsQuery } = apiSlice
```

1. API Slice 参数  

- `reducerPath`: 默认为 `api`,所有 RTKQ 缓存数据都将存储在 state.api 下
- 定义 Endpoints  
  + `builder.query()` 用于缓存的数据, `builder.mutation()` 向服务器发送更新;
  + 默认情况下，查询请求接口将使用 GET HTTP 请求，但你可以通过返回类似 `{url: '/posts', method: 'POST', body: newPost, header: header}` 修改。
- `keepUnusedDataFor`: 配置未使用的数据会多久后从缓存中删除

2. RTK Query 的 React 集成会自动为我们定义的 每个 请求接口生成 React hooks！  
   hooks 根据标准约定自动命名：  

- use，任何 React hooks 的正常前缀
- 请求接口名称，大写
- 请求接口的类型，Query 或 Mutation

> query hooks: useQueryUsersQuery()  
> mutation hooks: const [addUsers, { isLoading }] = useAddUsersMutation()  
> RTK Query 使用 `tag` 来定义 query 和 mutations 之间的关系，实现 mutation 后自动执行对应的 query。

3. `apiSlice.injectEndpoints()`  
>    拆分请求接口定义,我们仍然可以拥有一个带有单个 middleware 和 cache reducer 的 API slice，但我们可以将一些请求接口的定义移动到其他文件中。