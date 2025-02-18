

**这是基于DanhengWebTools修改的DanhengServer的控制台分支，功能正在积极开发中，若你想要参与开发，请提交pr**  
# 📣📣重要通知
**使用本工具时，请遵守相关的开源协议。您可以任意修改项目内的任何代码，但是，请不要修改项目内指向本仓库的Github徽标，否则，您将会出现在本项目的耻辱柱上**

# 💡 功能

## 目前功能

- [√] **代码生成**
- [√] **远程执行**
- [√] **远程执行密码**
- [√] **服务器状态查看**
- 
## 待实现功能

- [ ] **用户查询**
- [ ] **执行密码管理后台**

# web版
## 多语言
页面会根据当前浏览器语言自动调整语言，目前仅支持中文，英文（不完全），且部分(bushi)翻译工作尚未完成，若您想为项目的国际化做出贡献，请提交pr。
The page will automatically adjust the language according to the current browser language, currently only supports Chinese, English, and some translation work has not yet been completed, if you want to contribute to the internationalization of the project, please submit a PR.

## 运行环境
*node 18*，其他版本不保证能稳定运行.

# 🍗 使用与安装
### 1.安装依赖
进入解压后的文件目录
```
npm install
```
### 2.打包build
控制台输入
```
npm run build
```
`
npm install http-server  
`
`
cd dist  
`
`
http-server
`
# 远程命令
**为确保数据传输的安全性，请使用[api](https://github.com/lctoolsweb/DanhengWebTools-transmit)转发请求.请在`.env`文件中填入api地址**
例如
```
VITE_DHWT_API_SERVER=https://ooo.00x00.ooo
```
**前端与api都需要启用https协议,否则会出现请求异常**

# Ciallo～(∠・ω )⌒☆
点击特效与音效可以在` .env`文件中开启/关闭
```
VITE_CLICK_AUDIO=true
VITE_CLICK_TEXT=true
```

# 免责声明

本项目只是用于学习TypeScript与vue相关的知识，切勿用于非法用途。请不要在无关的地方宣传。  
本项目完全遵守相关的开源协议，您可以根据自己的意愿自由地更改源码  
因用于商业用途而导致的任何法律后果，包括但不限于损失、责任、索赔或其他相关事宜，本项目的开发者概不负责。  
使用者需自行承担使用本项目的风险，并在遵循 MIT 许可证的前提下自行评估和处理相关的法律责任。  
**因不符合规定的商业行为，我们已经暂停示例页面的访问，使用者需自行搭建并承担可能带来的相关法律责任。**

## 耻辱柱
### 本项目及其他任何衍生工具都是免费软件。
+ 中国商人1:Q群377591124 Q号2914592234
+ 中国商人李贞璟 Q号57366054 yile一乐圈服，四处倒卖圈钱

## 🙌 鸣谢
- [DanhengWebTools](https://github.com/lctoolsweb/DanhengWebTools) - 原版程序
