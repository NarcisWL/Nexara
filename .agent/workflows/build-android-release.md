---
description: Build Android Release APK (避免路径限制)
---

# Android Release APK 编译流程

## 前置条件

- ✅ 所有 TypeScript 错误已修复（运行 `tsc --noEmit` 验证）
- ✅ Android SDK 和 Gradle 已正确安装
- ✅ 确保项目路径长度 < 50 字符

## 标准流程（项目路径短）

如果项目已在短路径（如 `C:\Nx`），直接执行：

```powershell
# 1. 清理缓存
npm run clean  # 或手动删除 node_modules, .expo, android/.gradle

# 2. 安装依赖
npm install

# 3. 生成原生代码
npx expo prebuild --platform android --clean

# 4. 编译 Release APK
// turbo
cd android
.\gradlew.bat assembleRelease
```

APK 输出位置: `android\app\build\outputs\apk\release\app-release.apk`

## 路径过长流程（G: 盘等深层路径）

如果项目在 `G:\Dev\NeuralFlow` 等长路径，**必须**迁移：

```powershell
# 1. 迁移项目到短路径
robocopy G:\Dev\NeuralFlow C:\Nx /E /XD node_modules android\build .git .expo

# 2. 切换到新路径
cd C:\Nx

# 3. 安装依赖
npm install

# 4. 生成原生代码
npx expo prebuild --platform android --clean

# 5. 编译 Release APK
// turbo
cd android
.\gradlew.bat assembleRelease

# 6. 复制 APK 回原目录（可选）
copy android\app\build\outputs\apk\release\app-release.apk G:\Dev\NeuralFlow\Nexara-release.apk
```

## 常见错误处理

### 错误: ninja: Filename longer than 260 characters

**解决**: 使用上述"路径过长流程"迁移到 `C:\Nx`

### 错误: this and base files have different roots

**解决**: 
```powershell
# 删除所有缓存和软链接
rm -r node_modules, .metro, android\.gradle, android\build
npm install
npx expo prebuild --clean
```

### 错误: package.json does not exist

**解决**: 确保不在虚拟驱动器根目录（如 `X:\`），使用物理路径

## 发布检查

- [ ] APK 文件大小正常（~90MB）
- [ ] 应用图标正确显示
- [ ] 应用名称为 "Nexara"
- [ ] 在真机上安装测试基本功能
