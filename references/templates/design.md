# Compatibility redirect: design

此文件保留仅为兼容历史链接。

先检查当前 Agent 是否安装 `$setup-matt-pocock-skills`；未安装时暂停并提示用户按当前 Agent 平台的安装方式安装，不能用本机绝对路径替代。已安装后再显式调用 `$setup-matt-pocock-skills`，按 Explore → Present findings and ask → Confirm and edit → Write 配置仓库。
按该 skill 的 Explore → Present findings and ask → Confirm and edit → Write 流程完成仓库配置，再遵循仓库已配置的 engineering skills。

不要复制本文件启动一套独立的 DBT-BI spec workflow。

设计完成后，按仓库已配置的 engineering skills 执行实现与验证；不要把本文件视为 DBT-BI 的独立 design 模板。
