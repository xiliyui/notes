## 创建项目

`npm i -g expo-cli` or `yarn add global expo-cli`  
`npx create-expo-app my-app` or  
*`expo init MyTSProject` 选择 `tabs (TypeScript)`  
expo官网注册账号

```powershell
# 使用官网注册的账号登录
eas login
# 验证是否登录
eas whoami
eas build:configure
eas build --platform android
```

下载Expo Go调试

### 相关依赖

1. `react-native-safe-area-context`  
   处理异形屏适配，`React Navigation`的适配就是使用该组件进行处理的
2. `expo-status-bar`  
   控制设备状态栏属性  

- `backgroundColor` 设备状态栏的背景色
- `hidden` 显示隐藏设备状态栏
- `animated` 改变设备状态栏时，立即变化还是动画过渡
- `hideTransitionAnimation`（iOS） 显示隐藏设备状态栏时，立即变化还是动画过渡
- `networkActivityIndicatorVisible`（iOS） 设备状态栏网络流量是否显示

3. `expo-web-browser`  
   打开外部链接，需要携带用户信息时使用`WebBrowser.openAuthSessionAsync`，否则使用`WebBrowser.openBrowserAsync`
4. `expo-splash-screen`  
   启动屏幕配置  

- `SplashScreen.preventAutoHideAsync()` 直到 `SplashScreen.hideAsync()` 触发，停留在启动屏幕
- `SplashScreen.hideAsync()` 关闭启动屏幕

## react-navigation

1. `NavigationContainer`  

- `ref`
- `initialState`  
- `onStateChange `  

2. `createNativeStackNavigator`  
   `createNativeStackNavigator`函数返回两个元素 `Navigator` 和 `Screen`，`Navigator` 包含多个 `Screen` 作为子元素

```ts
const Stack = createNativeStackNavigator()
<Stack.Navigator>
  <Stack.Screen name="Home" component={HomeScreen} />
</Stack.Navigator>
```

3. 在 `NavigationContainer` 外调用

```ts
// rootNavigation.ts
import { createNavigationContainerRef } from '@react-navigation/native';

export const navigationRef = createNavigationContainerRef()

export function navigate(name: any, params: any) {
  if (navigationRef.isReady()) {
    navigationRef.navigate(name, params);
  }
}
// index.tsx
import { NavigationContainer } from '@react-navigation/native';
import { navigationRef } from './rootNavigation';
<NavigationContainer ref={navigationRef}>
  <RootNavigator />
</NavigationContainer>
```
