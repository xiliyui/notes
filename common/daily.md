1. `patch-package`   
修改node_modules依赖包源码，修改完成后执行 `npx patch-package <node_modules_patch>` ,拉取代码后重新 `yarn install`。
2. 打开模拟器：`emulator -avd <adb_name>`
3. 修改模拟器dns：
- `adb root `
- `adb shell` `getprop`查看dns名称
- `setprop <dns_name> <dns_ip>(ipconfig -all查询)`修改dns ip
- 关闭模拟器网络再重新打开

### SMS
`https://sms-activate.org`
- 账号：paojie2333@gmail.com
- 密码：!Paojie233

### OpenAI
`https://beta.openai.com/account/api-keys`
- 账号：paojie2333@gmail.com
- 密码：!Paojie2333
- key: `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`
