# PostgreSQL 自动化构建器



## ✨ 项目特性

-   **自动化构建**: 完全自动化的编译和打包流程，确保每次构建的一致性。
-   **版本高度可定制**: 您可以在触发构建时，自由选择 PostgreSQL、pgBackRest 等组件的版本。
-   **环境隔离**: 所有编译工作都在 Docker 容器内完成，保持构建环境的纯净和一致。
-   **轻松分发**: 构建产物会自动上传到 GitHub Releases，方便下载和部署。
-   **生态集成**: 默认集成了 pgBackRest、pg_rman 和 repmgr 等关键工具，满足备份恢复和高可用的需求。



---

## 📦 构建产物

下载的 `postgresql-VERSION-OS.tar.gz` 文件解压后，包含了完整的 PostgreSQL 运行环境，目录结构如下：

```
.
├── bin/      # (psql, pg_ctl, pg_basebackup, pgbackrest, repmgr 等可执行文件)
├── include/  # (头文件)
├── lib/      # (动态链接库)
├── share/    # (文档、区域设置等共享文件)
└── ...       # (其他相关目录)
```

您可以将解压后的整个目录移动到您服务器的任意位置（例如 `/usr/local/pgsql`），然后配置环境变量并初始化数据库即可开始使用。