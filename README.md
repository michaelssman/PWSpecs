# PWSpecs

`PWSpecs` 是 PW 系列组件使用的自定义 CocoaPods Specs 源，目前用于发布
`PWUtils`。源码仓库为私有仓库，Specs 仓库可公开读取。

## 使用 PWUtils

### 前置条件

- iOS 12.0+
- 已安装 CocoaPods
- 开发机和 CI 已配置可读取 `git@github.com:michaelssman/PWUtils.git` 的 SSH Key

### Podfile

```ruby
source 'https://github.com/michaelssman/PWSpecs.git'
source 'https://cdn.cocoapods.org/'

platform :ios, '12.0'
use_modular_headers!

target 'YourApp' do
  pod 'PWUtils', '~> 0.5.4'
end
```

声明自定义 source 后，CocoaPods 不再隐式添加官方 source，因此 `PWSpecs` 和
CocoaPods CDN 都需要保留。`use_modular_headers!` 用于兼容 PWUtils 中的
Objective-C 依赖。

安装或更新依赖：

```shell
pod install --repo-update
```

完成 source 配置后，业务 target 中只需要声明 `pod 'PWUtils'`。

## 本地注册 Specs

需要发布组件时，先将仓库注册到本机 CocoaPods：

```shell
pod repo add PWSpecs https://github.com/michaelssman/PWSpecs.git
pod repo lint PWSpecs
```

日常消费不必预先执行 `pod repo add`，Podfile 中的 `source` 会负责获取 Specs。

## 发布新版本

1. 修改 `PWUtils.podspec` 的版本及发布内容。
2. 运行本地 lint 和 Example 构建。
3. 提交源码，创建与 Podspec 版本完全一致的 Git tag，并先推送源码和 tag。
4. 将 Podspec 推送到 `PWSpecs`。

```shell
pod lib lint PWUtils.podspec \
  --private \
  --allow-warnings \
  --use-libraries \
  --use-modular-headers

pod repo push PWSpecs PWUtils.podspec \
  --use-json \
  --allow-warnings \
  --use-libraries \
  --use-modular-headers
```

已发布的版本目录视为不可变；后续修改必须提升版本号，不能覆盖旧 Spec。

## HHUtils 迁移

`HHUtils` 与 `PWUtils` 是两个独立的 Pod 名称。旧 `HHSpecs` 只用于存量项目迁移，
不要把旧版本 JSON 直接改名复制到本仓库。下游迁移完成后可单独删除 `HHSpecs`。
