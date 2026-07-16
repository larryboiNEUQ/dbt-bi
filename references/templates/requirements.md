# Compatibility redirect: requirements

此文件保留仅为兼容历史链接。

先检查仓库 setup 是否已完成：未完成时才检查当前 Agent 是否安装 `$setup-matt-pocock-skills`，缺失则提示安装并暂停；可用后显式调用并按其当前版本配置仓库。setup 已完成则不重复运行，直接进入运行时 skill 发现与路由。
setup 完成后，由 Coding Agent 在自己的当前环境中动态发现 Matt Pocock engineering skills，读取候选 skill 的元数据与完整指令后按任务路由；本文件不提供固定列表。

不要复制本文件启动一套独立的 DBT-BI spec workflow。

DBT-BI 的四件套与 PBIP 规范仍以本仓库的 Part A / Part C 和 references 文档为准。
