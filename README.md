# GeoLite2 数据库

自动从 MaxMind 官网下载和发布 GeoLite2 数据库（ASN、City 和 Country）

## 🚀 功能特性

### ⏰ 自动化调度
- **定时更新**: 每周四 00:00 UTC 自动执行
- **手动触发**: 支持手动触发以便测试和紧急更新
- **强制更新**: 可选择忽略版本检查强制更新

### 📊 智能版本管理
- **版本检查**: 仅在有新版本时才执行下载
- **自动版本号**: 基于数据库发布日期生成版本号
- **版本历史**: 自动保留最近 3 个版本

### 🔐 安全与完整性
- **文件校验**: MD5 和 SHA256 完整性验证
- **敏感数据保护**: 使用 GitHub Secrets 存储 API 密钥
- **权限控制**: 最小必要权限原则
- **自动清理**: 执行后自动清理敏感数据

### 📦 文件处理
- **多格式支持**: 提供 tar.gz 和 zip 两种格式
- **压缩优化**: 使用 gzip 压缩节省存储空间
- **完整打包**: 将三个数据库文件打包为单一归档

### 📝 详细日志
- **执行状态**: 清晰的步骤状态显示
- **错误处理**: 完善的错误处理和日志记录
- **通知功能**: 工作流执行状态通知

## 🛠️ 设置说明

### 1. 获取 MaxMind 许可证密钥

1. 访问 [MaxMind 官网](https://www.maxmind.com/en/geolite2/signup)
2. 注册免费账户
3. 登录后进入 "My License Key" 页面
4. 生成新的许可证密钥

### 2. 配置 GitHub Secrets

在您的 GitHub 仓库中设置以下 Secrets：

1. 进入仓库的 **Settings** → **Secrets and variables** → **Actions**
2. 点击 **New repository secret**
3. 添加以下密钥：

| 密钥名称 | 描述 | 必需 |
|---------|------|------|
| `MAXMIND_LICENSE_KEY` | MaxMind 许可证密钥 | ✅ |

### 3. 启用 GitHub Actions

1. 确保仓库启用了 GitHub Actions
2. 工作流文件位于 `.github/workflows/update-geolite2.yml`
3. 首次运行可以手动触发进行测试

## 📋 使用方法

### 自动执行
工作流将在每周四 00:00 UTC 自动执行，无需手动干预。

### 手动触发
1. 进入仓库的 **Actions** 标签页
2. 选择 "Update GeoLite2 Databases" 工作流
3. 点击 **Run workflow**
4. 可选择以下选项：
   - **强制更新**: 忽略版本检查，强制下载最新数据
   - **跳过清理**: 保留所有历史版本，不执行自动清理

### 下载数据库
1. 进入仓库的 **Releases** 页面
2. 选择最新版本
3. 下载适合您系统的归档文件：
   - `GeoLite2-Databases-vYYYYMMDD.tar.gz` (Linux/macOS)
   - `GeoLite2-Databases-vYYYYMMDD.zip` (Windows)

## 📁 文件结构

下载的归档文件包含以下内容：

```
GeoLite2-Databases-vYYYYMMDD/
├── GeoLite2-ASN.mmdb          # ASN 数据库
├── GeoLite2-City.mmdb         # 城市数据库
├── GeoLite2-Country.mmdb      # 国家数据库
├── checksums.txt              # 文件校验和
└── README.md                  # 使用说明
```

## 🔍 文件完整性验证

每个发布包都包含 `checksums.txt` 文件，您可以使用它来验证下载文件的完整性：

### Linux/macOS
```bash
# 验证 MD5
md5sum -c checksums.txt

# 验证 SHA256
sha256sum -c checksums.txt
```

### Windows (PowerShell)
```powershell
# 验证 MD5
Get-FileHash -Algorithm MD5 *.mmdb

# 验证 SHA256
Get-FileHash -Algorithm SHA256 *.mmdb
```

## 🔧 工作流配置

### 修改执行时间
要更改自动执行时间，请编辑 `.github/workflows/update-geolite2.yml` 文件中的 cron 表达式：

```yaml
schedule:
  - cron: '0 0 * * 4'  # 每周四 00:00 UTC
```

### 修改保留版本数量
要更改保留的版本数量，请修改清理步骤中的数组索引：

```bash
# 保留最近 3 个版本（删除第 4 个及以后的版本）
RELEASES=$(gh release list --json tagName,createdAt --jq 'sort_by(.createdAt) | reverse | .[3:] | .[].tagName')
```

## 📊 监控和故障排除

### 查看执行状态
1. 进入仓库的 **Actions** 标签页
2. 查看最近的工作流运行记录
3. 点击具体运行查看详细日志

### 常见问题

#### 1. 许可证密钥错误
**错误**: `❌ 错误: MAXMIND_LICENSE_KEY 未设置`
**解决**: 确保在 GitHub Secrets 中正确设置了 `MAXMIND_LICENSE_KEY`

#### 2. 下载失败
**错误**: `❌ [数据库名] 下载失败`
**解决**: 
- 检查许可证密钥是否有效
- 确认 MaxMind 服务状态
- 检查网络连接

#### 3. 权限错误
**错误**: 无法创建 Release
**解决**: 确保工作流具有 `contents: write` 权限

## 🔗 相关链接

- [MaxMind GeoLite2 文档](https://dev.maxmind.com/geoip/geolite2-free-geolocation-data)
- [MaxMind DB 格式规范](https://maxmind.github.io/MaxMind-DB/)
- [GitHub Actions 文档](https://docs.github.com/en/actions)

## 📄 许可证

本项目中的工作流代码采用 MIT 许可证。

GeoLite2 数据库由 MaxMind 提供，遵循 [MaxMind GeoLite2 许可证](https://dev.maxmind.com/geoip/geolite2-free-geolocation-data)。

---

> 🤖 由 GitHub Actions 自动维护和执行