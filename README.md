# GitPic

- 利用github作图床而制作的一个图片上传工具。
## 部署
- 可部署在github pages，gitee pages，cloudflare pages等平台，方便在线访问。

## 关于
- 本项目利用github api实现图片的管理功能，因此首先需要到[GitHub](https://github.com/settings/tokens)上获取`token`。
- 本项目不会将`token`等信息保存在云端，因此需要你提前备份好相关的信息，以免丢失。
- 本项目将`token`等信息保存在`localStorage`中，因此请注意保护你的浏览器隐私安全。

## 提醒
- 本项目中部分功能需要获取你的`email`信息，如果你的`email`信息未公开的话，请按以下步骤进行配置。
    1. 打开此[链接](https://github.com/settings/emails)，取消勾选`Keep my email addresses private`;
    2. 再打开此[链接](https://github.com/settings/profile)，设置`Public email`，选择你的邮箱。