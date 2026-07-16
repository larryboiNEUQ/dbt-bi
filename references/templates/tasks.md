# Compatibility redirect: tasks

此文件保留仅为兼容历史链接。

先检查当前 Agent 是否安装 `$setup-matt-pocock-skills`；未安装时暂停并提示用户按当前 Agent 平台的安装方式安装，不能用本机绝对路径替代。已安装后再显式调用 `$setup-matt-pocock-skills`，按 Explore → Present findings and ask → Confirm and edit → Write 配置仓库。
setup 完成后，由 Coding Agent 在自己的当前环境中动态发现 Matt Pocock engineering skills，读取候选 skill 的元数据与完整指令后按任务路由；本文件不提供固定列表。

不要复制本文件启动一套独立的 DBT-BI spec workflow。

实现与验证遵循Coding Agent 动态发现并选中的 Matt Pocock skill 和 issue tracker；DBT-BI 只负责四件套、PBIP 以及相关验数规范。
