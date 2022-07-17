# Rustling

## 安装配置

1.） 在网络浏览器中用自己的 github id 登录 github.com

2.） 接收 [Rust-lang Lab Test based on Rustlings 的github classroom在线邀请](https://classroom.github.com/a/YTNg1dEH) ，根据提示一路选择OK即可。

3.） 完成第二步后，你的rustings实验练习 的 github repository 会被自动建立好，点击此[github repository](https://github.com/LearningOS/learn_rust_rustlings-Yuukina)的链接，就可看到你要完成的实验了。

4.） 在你的第一个实验练习的网页的中上部可以看到一个醒目的 `code` 绿色按钮，点击后，可以进一步看到 `codespace` 标签和醒目的 `create codesapce on edu` 绿色按钮。请点击这个绿色按钮，就可以进入到在线的ubuntu +vscode环境中

5.） 再按照下面的环境安装提示在vscode的 `console` 中安装配置开发环境：rustc等工具。注：也可在vscode的 `console` 中执行 `make codespaces_setenv` 来自动安装配置开发环境（执行`sudo`需要root权限，仅需要执行一次）。

![image-20220716142706286](https://raw.githubusercontent.com/Yuukina/XSJ-Img/main/img/image-20220716142706286.png)

6.） 在vscode的 `console` 中执行 `make setupclassroom` （该命令仅执行一次）配置githubclassroom 自动评分功能。

![image-20220716142819159](https://raw.githubusercontent.com/Yuukina/XSJ-Img/main/img/image-20220716142819159.png)

7.） 然后就可以基于在线vscode进行测试 (执行命令 `rustlings watch` ），编辑代码的循环实验过程了。

上述的3，4，5步不是必须的，你也可以线下本地开发。如果是本地的ubuntu中建立开发环境，可在shell中执行 `make ubuntu_local_setenv` 来自动安装配置开发环境。

配置完成后，按照rustling提示完成训练即可。