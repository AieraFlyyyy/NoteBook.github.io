# 清除Git中的缓存凭据

### 你是否有过这样的困扰！

![](../.gitbook/assets/image%20%2816%29.png)

一旦拉取项目时，输错了账号密码，就不知道如何重新输入了！

接下来教大家如何清除git中的的缓存凭据 — （方法来自[官方文档](https://docs.github.com/cn/free-pro-team@latest/github/using-git/caching-your-github-credentials-in-git)）

{% tabs %}
{% tab title="Mac" %}
如果您使用 SSH 克隆 GitHub 仓库，则可使用 SSH 密钥进行身份验证，而不是使用其他凭据。 有关设置 SSH 连接的信息，请参阅“[生成 SSH 密钥](https://docs.github.com/cn/free-pro-team@latest/articles/generating-an-ssh-key)”。

**提示：**

* 您需要 Git **1.7.10** 或更高版本才能使用 osxkeychain 凭据小助手。
* 如果您使用 [Homebrew](http://brew.sh/) 安装了 Git，则已经安装了 `osxkeychain 助手`。
* 如果您运行 Mac OS X 10.7 及更高版本，并且通过 Apple 的 Xcode 命令行工具安装了 Git，则 `osxkeychain 助手`自动包含在您的 Git 安装中。

安装 Git 和 `osxkeychain 助手`并告诉 Git 使用它。

1. 核实是否已安装 Git 和 `osxkeychain 助手`：

   ```text
   $ git credential-osxkeychain
   # Test for the cred helper
   > Usage: git credential-osxkeychain <get|store|erase>
   ```

2. 如果 `osxkeychain helpper` 尚未安装，而您使用的是 OS X 10.9 或更高版本，您的计算机会提示您将其下载为 Xcode Command Line 工具的一部分：

   ```text
   $ git credential-osxkeychain
    > xcode-select: note: no developer tools were found at '/Applications/Xcode.app',
    > requesting install. Choose an option in the dialog to download the command line developer tools.
   ```

   或者，您也可以使用 [Homebrew](http://brew.sh/) 安装 Git 和 `osxkeychain helper`：

   ```text
   $ brew install git
   ```

3. 使用 global `credential.helper` config 指示 Git 使用 `osxkeychain helper`：

   ```text
   $ git config --global credential.helper osxkeychain
   # Set git to use the osxkeychain credential helper
   ```

下次克隆需要身份验证的 HTTPS URL 时，Git 会提示您输入用户名和密码。 When Git prompts you for your password, enter your personal access token \(PAT\) instead. Password-based authentication for Git is deprecated, and using a PAT is more secure. For more information, see "[Creating a personal access token](https://docs.github.com/cn/free-pro-team@latest/github/authenticating-to-github/creating-a-personal-access-token)."

验证成功后，您的凭据存储在 macOS 密钥链中，每次克隆 HTTPS URL 时都会使用。 除非更改凭据，否则无需在 Git 中再次键入凭据。
{% endtab %}

{% tab title="Windows" %}
如果您使用 SSH 克隆 GitHub 仓库，则可使用 SSH 密钥进行身份验证，而不是使用其他凭据。 有关设置 SSH 连接的信息，请参阅“[生成 SSH 密钥](https://docs.github.com/cn/free-pro-team@latest/articles/generating-an-ssh-key)”。

**提示：**您需要 Git **1.7.10** 或更高版本才能使用凭据小助手。

您还可以安装本机 Git shell，例如 [Windows 版 Git](https://git-for-windows.github.io/)。 使用 Windows 版 Git 时，在命令行中运行以下内容将存储凭据：

```text
$ git config --global credential.helper wincred
```
{% endtab %}

{% tab title="Linux" %}
如果您使用 SSH 克隆 GitHub 仓库，则可使用 SSH 密钥进行身份验证，而不是使用其他凭据。 有关设置 SSH 连接的信息，请参阅“[生成 SSH 密钥](https://docs.github.com/cn/free-pro-team@latest/articles/generating-an-ssh-key)”。

**提示：**您需要 Git **1.7.10** 或更高版本才能使用凭据小助手。

开启凭据小助手使 Git 将您的密码在内存中保存一段时间。 默认情况下，Git 会缓存密码 15 分钟。

1. 在终端，输入以下命令：

   ```text
   $ git config --global credential.helper cache
   # Set git to use the credential memory cache
   ```

2. 要更改默认的密码缓存时限，请输入以下命令：

   ```text
   $ git config --global credential.helper 'cache --timeout=3600'
   # Set the cache to timeout after 1 hour (setting is in seconds)
   ```
{% endtab %}
{% endtabs %}

