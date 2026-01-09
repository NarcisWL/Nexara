# Android Release Build Protocol (NeuralFlow)

为了确保开发环境与发行环境的物理隔离，并保护签名密钥安全，特制定以下协议：

## 1. 物理隔离 (Physical Isolation)
*   **主工作区 (Root - `main`)**: 仅用于开发和测试包编译。严禁在此包含正式签名配置。
*   **发行工作区 (Worktree - `release-production`)**: 专用发行环境。位于 `worktrees/release`。所有的正式发布包 (`Signed-Release`) 必须在此目录下编译。

## 2. 签名策略
*   **Root**: `android/app/build.gradle` 中的 `signingConfigs.release` 已移除或禁用。
*   **Worktree**: `android/app/build.gradle` 包含完整的 `signingConfigs.release` 逻辑，并指向 `../../secure_env/`。

## 3. 操作指令 (Standard Operating Procedures)

### 编译开发包 (Dev Build)
在 **Root** 目录执行：
```bash
./gradlew assembleDebug
```

### 编译发行包 (Release Build)
在 **Worktree** 目录执行：
```bash
cd worktrees/release/android && ./gradlew assembleRelease
```

## 4. 依赖管理
*   ** gradle.properties**: 两边独立维护代理及编译参数。
*   ** 权限恢复**: 每次清理 node_modules 后，需确保 `linux64-bin/hermesc` 具备执行权限 (`chmod +x`)。
