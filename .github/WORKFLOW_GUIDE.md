# GitHub Actions 构建配置指南

## 概述

本项目为SPT 4.0模组配置了完整的自动化构建、测试和发布工作流。

## 工作流说明

### 1. 构建工作流 (`build-spt40.yml`)

**触发条件：**
- 推送到 `main` 或 `develop` 分支（仅当SPT 4.0相关文件改动）
- 提交Pull Request到 `main` 或 `develop` 分支
- 手动触发工作流

**功能：**
- ✅ 恢复NuGet依赖
- ✅ 编译Release版本
- ✅ 提取版本号
- ✅ 打包模组文件（ZIP格式）
- ✅ 上传构建产物

**工作流步骤：**
```
Setup .NET 9.0
  ↓
Restore NuGet Packages
  ↓
Build Release
  ↓
Extract Version
  ↓
Create Release Package
  ↓
Upload Artifacts
```

### 2. 发布工作流 (`release.yml`)

**触发条件：**
- 创建标签版本 (格式: `v*`，例如 `v1.1.5`)

**功能：**
- ✅ 完整编译构建
- ✅ 创建GitHub Release
- ✅ 上传发布包到Release
- ✅ 自动生成发布说明

**使用方式：**
```bash
# 创建版本标签并推送
git tag v1.1.5
git push origin v1.1.5
```

### 3. 代码质量检查 (`code-quality.yml`)

**触发条件：**
- 推送到 `main` 或 `develop` 分支（仅当SPT 4.0相关文件改动）
- 提交Pull Request到 `main` 或 `develop` 分支

**功能：**
- ✅ 代码编译检查
- ✅ 运行单元测试（如果存在）
- ✅ 生成构建报告
- ✅ 上传构建日志（失败时）

## 构建产物

### 输出目录结构

```
yellowdoge-tarkovrarecollectibles/
├── TarkovRareCollectibles.dll      # 编译后的模组DLL
├── package.json                    # 模组配置文件
├── bundles.json                    # 资源包配置
├── bundles/                        # 模组资源包
├── config/                         # 模组配置文件
├── db/                             # 数据库文件
└── LICENSE                         # 许可证
```

### ZIP包格式

发布包命名格式：`TarkovRareCollectibles-{version}.zip`

例如：`TarkovRareCollectibles-1.1.5.zip`

## 版本管理

### 自动版本提取

版本号从 `TarkovRareCollectibles.csproj` 文件的 `<Version>` 标签自动读取：

```xml
<PropertyGroup>
  <Version>1.1.5</Version>
</PropertyGroup>
```

### 更新版本步骤

1. 编辑 `SPT 4.0/TarkovRareCollectibles.csproj`
2. 修改 `<Version>` 值
3. 提交更改
4. 创建版本标签：`git tag v{new-version}`
5. 推送标签以触发发布工作流

## GitHub Secrets

确保以下密钥已在仓库设置中配置（大多数GitHub Actions已内置）：

- `GITHUB_TOKEN` - 自动提供，用于创建Release和上传产物

## 示例：完整发布流程

```bash
# 1. 更新版本号
# 编辑 SPT 4.0/TarkovRareCollectibles.csproj 的 <Version> 标签

# 2. 提交更改
git add "SPT 4.0/TarkovRareCollectibles.csproj"
git commit -m "chore: bump version to v1.2.0"
git push origin main

# 3. 创建版本标签
git tag v1.2.0
git push origin v1.2.0

# 4. GitHub Actions自动执行：
#    - 编译代码
#    - 打包文件
#    - 创建Release
#    - 上传产物
```

## 工作流状态监控

访问仓库的 **Actions** 标签页查看：
- ✅ 成功的构建
- ❌ 失败的构建
- ⏳ 正在进行的工作流
- 构建日志和产物下载

## 常见问题

### Q: 如何重新运行工作流？
A: 在Actions页面点击失败的工作流，选择"Re-run jobs"

### Q: 如何跳过工作流执行？
A: 在提交信息中添加 `[skip ci]`：
```bash
git commit -m "docs: update readme [skip ci]"
```

### Q: 如何手动触发构建？
A: 在Actions页面点击"Build SPT 4.0 Mod"工作流，选择"Run workflow"

### Q: 产物保留多长时间？
A: 
- 普通构建：30天
- 发布构建：90天

## 许可证

MIT License - 详见LICENSE文件
