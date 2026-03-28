# minimax-agent-docs

MiniMax Agent 客服知识库文档仓库。通过 agent-admin 的「文档同步」功能自动同步到知识库。

## 目录结构

```
minimax-agent-docs/
├── README.md
└── packages/
    └── docs-zh/              ← 同步时指定的子目录
        ├── getting-started.mdx
        ├── faq.mdx
        ├── features/
        │   ├── voice-call.mdx
        │   └── text-chat.mdx
        └── troubleshooting/
            ├── common-errors.mdx
            └── network-issues.mdx
```

## 文档格式

所有文档使用 `.mdx` 格式（Markdown + 可选 JSX）。同步系统会自动：
- 从 frontmatter 提取 `title` 作为文档标题
- 去除 `import`/`export` 语句和 JSX 标签
- 保留纯文本和 Markdown 内容
- 基于内容 MD5 做增量同步（内容不变则跳过）

### 必须遵守的规则

1. **文件必须是 `.mdx` 后缀** — `.md` 文件不会被同步
2. **必须有 frontmatter** — 以 `---` 包裹，至少包含 `title` 字段
3. **title 不能为空** — 否则会用文件路径作为标题

### 基本模板

```mdx
---
title: "文档标题"
---

文档正文内容，支持标准 Markdown 语法。
```

## 如何添加新文档

### 1. 创建 MDX 文件

在 `packages/docs-zh/` 目录下创建 `.mdx` 文件，可以放在子目录中。

```bash
# 直接放在 packages/docs-zh/ 下
touch packages/docs-zh/my-new-doc.mdx

# 或放在子目录中
mkdir -p packages/docs-zh/guides
touch packages/docs-zh/guides/my-guide.mdx
```

### 2. 编写内容

```mdx
---
title: "我的新文档"
---

## 概述

这里是文档内容...

## 详细说明

- 支持列表
- 支持 **加粗** 和 *斜体*
- 支持 `代码` 和代码块
```

### 3. 提交并推送

```bash
git add packages/docs-zh/my-new-doc.mdx
git commit -m "add: 我的新文档"
git push
```

### 4. 在 admin 后台同步

进入客服 admin → 文档同步 tab → 手动输入仓库 URL → 点击「手动同步」。

同步配置：
- **GitHub 仓库 URL**: `https://github.com/KrisLiu16/minimax-agent-docs`
- **子目录**: `packages/docs-zh`
- **分类名**: `custom-zh`（已在 admin 下拉框中预设）

## 同步机制

| 场景 | 触发条件 |
|------|----------|
| **新增** | 本地有文件，知识库中没有对应记录 |
| **更新** | 文件内容 MD5 与知识库记录不一致 |
| **跳过** | 文件内容 MD5 一致，无变化 |
| **删除** | 知识库有同步记录，但本地文件已删除 |

## 注意事项

- 修改文件后必须 `git push`，同步时会先 `git pull` 拉取最新
- 文件重命名 = 删除旧的 + 新增新的（同步 key 基于文件路径）
- 首次同步自动 `git clone`，后续增量 `git pull`
- 所有仓库共用一个 `GITHUB_TOKEN` 认证
