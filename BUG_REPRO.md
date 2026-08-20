# Bug Reproduction

## 包的性质

当前 test_model_fix 保存的是被测模型修复后的结果源码，不是初始含 Bug 源码。要复现原始缺陷，必须检出下面固定的 parent SHA；不要在当前修复结果源码上期待重新出现修复前失败。生成系统使用的可信验证补丁和完整验证日志仅在本地留存，不提交到结果分支。

## 问题现象

到达生效时间的泊位窗口状态已经变化，但调度器没有计入激活数量。请修复后台任务的结果确认。

## 含 Bug 版本

- 仓库：zhanglei10281852-gif/portcoord-backend-qa-18
- 仓库地址：https://github.com/zhanglei10281852-gif/portcoord-backend-qa-18.git
- parent SHA：4e37e16757b29e37dae9996f4ab5409cba91290e

## 复现步骤

```bash
git clone -- https://github.com/zhanglei10281852-gif/portcoord-backend-qa-18.git bug-repro
cd bug-repro
git checkout --detach 4e37e16757b29e37dae9996f4ab5409cba91290e
go test ./internal/engine -run "^TestEngine_ActivateWindow$" -count=1
```

## 双架构完整错误信息

### linux/amd64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/engine -run "^TestEngine_ActivateWindow$" -count=1
--- FAIL: TestEngine_ActivateWindow (0.21s)
    engine_test.go:207: expected at least 1 activation
FAIL
FAIL	portcoord/internal/engine	0.214s
FAIL

```

stderr：

```text
warning: internal/engine/engine_test.go has type 100755, expected 100644
warning: internal/engine/engine_test.go has type 100755, expected 100644

```

### linux/arm64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/engine -run "^TestEngine_ActivateWindow$" -count=1
--- FAIL: TestEngine_ActivateWindow (0.45s)
    engine_test.go:207: expected at least 1 activation
FAIL
FAIL	portcoord/internal/engine	0.698s
FAIL

```

stderr：

```text
warning: internal/engine/engine_test.go has type 100755, expected 100644
warning: internal/engine/engine_test.go has type 100755, expected 100644

```

## 通过条件

在题面触发条件下，公开行为必须恢复且原始异常不再出现；定向命令 go test ./internal/engine -run ^TestEngine_ActivateWindow$ -count=1、相关包测试、全量测试、race、vet 和 build 必须通过；不得删除或跳过测试，也不得绕过目标逻辑。
