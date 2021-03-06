---
title: 解决 Mac 邮件正文变成 HTML 附件的问题
tags: Mac Windows IMAP
---

Harttle 最近解决了使用 Mac 发送带附件的邮件时，正文的一部分变成 HTML 附件的问题。
也顺带解决了服务器端重复保存已发送邮件的问题（126 邮箱）。

> 当然我尝试过转而使用 Foxmail 和 Thunderbird，然而 Mail 和 Mac 系统的集成还是让我不忍抛弃 Mail。

<!--more-->

## 问题描述

在使用 Mac 自带的邮件客户端发送邮件时，如果邮件包含附件，
附件后面的正文会被截掉，并形成一个 HTML 附件。
名字类似是 `ATT00004.html`，见图：

[Mac Mail HTML Attachment][mac-mail-html-attr]

其实我发送的邮件是：

```
Hi

<附件：jap.zip>

This is my attachment!
```

附件后面的正文被截掉了，并自动形成了 `ATT00004.html`（其中包含了后半部分正文）！

## 问题原因

Mac 中编写邮件时，会将附件与邮件正文混编。然后发送这样混编的邮件至服务器。

而有些邮件服务器则坚持附件必须位于正文之后，比如 126 邮箱。
因此出现在附件之后的正文也会被当做附件对待，因此生成了额外的附件来包含后半部分正文，这就是上面的 `AT00004.html`。

## 解决问题

在 Mail（9.3）中，点击菜单栏『编辑』->『附件』，勾选『始终在邮件结尾处插入附件』。

> 注意，此处的『始终发送与 Windows 兼容的附件』也要勾选，否则很多邮件服务器都无法识别邮件附件。

现在发送邮件正文就不会被截断了，会完整地显示在对方的正文中。
然而 `AT00004.html` 仍然会出现，只不过其内容为空。解决该问题也容易：

在 Mail（9.3）中，点击菜单栏『邮件』->『偏好设置』->『编写』，选择邮件格式为『纯文本』即可。注意：确保勾选了『始终在邮件结尾处插入附件』，否则附件会变成正文中的文本。

## 重复的已发邮件

Mac Mail 中使用 126 邮箱的 IMAP 服务时，每次发送带附件的邮件都会在服务器生成两封已发邮件，这是由已发邮件同步策略的 Bug 导致的：

1. Mail 客户端使用 126 的 SMTP 发信时，126 会保存一份邮件。
2. Mail 客户端对此不知情，会要求 126 服务器再保存一份已发送邮件。

于是服务器就出现了两份。直接禁用 Mail 的同步已发送邮件即可：

在 Mail（9.3）中，点击『邮件』->『偏好设置』->『账户』->『126 IMAP』->『邮箱行为』，去掉『将已发出邮件存储在服务器上』的勾选。

> 对于 126 服务器而言，默认设置为只保存 1M 以下的邮件附件，
> SMTP 发信时自动保存的那一份可能会不保存附件。
> 可以在 126 邮箱的『发送邮件后设置』中，选择『邮件保存规则』为
> 『全部保存到已发送文件夹』便可解决！

[mac-mail-html-attr]: /assets/img/blog/mac/mail-html-attachment.jpeg
