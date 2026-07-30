---
name: fontize
description: fontize 字体子集工具链的集成与用法指引：把 @fontize/nuxt、@fontize/vite 接入 Nuxt / 纯 Vite 项目，按声明的文本把 CJK 字体裁到实际渲染字符（约 17MB → 50KB 量级）；纠正 useText 写法、排查构建期缺字。当用户说「给 xxx 接入 @fontize/nuxt」「缩小标题字体体积」「字体子集」「裁剪字体」「useText 怎么用」「模板里 useText 不自动解 ref」「SPA 构建缺字」「生成后字体没裁」时触发
argument-hint: "[接入 | 用法 | 排错] [项目路径]"
metadata:
  version: 0.1.0
---

# fontize：字体子集集成与用法指引

## 要求

* 版本先行：诊断前确认消费端 @fontize/* 版本，低于功能下限（见 references/troubleshoot.md）先升级再排查。
* 声明优先于种子：useText 文本声明是第一公民，include 只作兜底，禁止用 include 绕过声明改造。
* 渐进披露：按文末「References 地图」在 Workflow 阶段中按需加载，禁止初始化时一口气全读。
* 强调纪律：非必要不使用「**」着重号。
* 输出中文。

## 意图路由

- 「接入 / 集成 / 安装 fontize」「缩小字体体积」「字体子集」「裁剪字体」：A 接入；Workflow A
- 「useText 怎么用 / 写法」「不自动解 ref」「.value 不优雅」：B 用法指引；Workflow B
- 「缺字」「构建后字体没裁」「SPA 不收字」「报错」：C 排错；Workflow C

与相邻技能的边界：

- 本技能只覆盖消费端（在任意项目里接入与使用 fontize）；fontize 仓库本体的源码开发走 flow-dev。
- 纯 Vite 路径与 Nuxt 路径共用同一套 useText 声明 API，差别只在装配层，分流由 Workflow A Step 0 完成。

## Workflow A：接入

0. 摸底
   - [ ] 判定路径：`nuxt.config.ts` 存在 → Nuxt；仅 `vite.config.ts` → 纯 Vite
   - [ ] 确认包管理器与既有 fontize 依赖版本（`pnpm why @fontize/nuxt` 等）
1. 安装与配置
   - [ ] 读 `references/integrate.md` 对应路径小节，安装并注册字体（fonts 首条即默认字体）
2. 文本声明改造
   - [ ] 读 `references/usage.md` 形态决策，逐处声明；静态字面量就近内联，动态源 setup getter
3. 验证
   - [ ] 按 `references/integrate.md` 验证清单逐项过：dev 端点、build 产物、HTML 注入、目检缺字

## Workflow B：用法指引

1. 形态判定
   - [ ] 读 `references/usage.md`：形态决策、模板 ref 解包规则、默认字体规则
2. 纠错与重构
   - [ ] 对照现状逐项给出好/坏改造，说明为什么（就近原则、静态可求值性、解包不对称）

## Workflow C：排错

1. 定位通道
   - [ ] 确认症状发生在 dev / SSG generate / SPA build 哪条通道（三者收集机制不同）
   - [ ] 读 `references/troubleshoot.md` 通道模型与静态提取边界
2. 对表排查
   - [ ] 版本低于功能下限时先升级；再按坑与修法逐条比对
3. 验证修复
   - [ ] 重新构建，检查产物 woff2 体积与关键文本无缺字

## 红线与故障恢复

- 禁止用 include 绕过文本声明改造；include 的唯一正当用途是纯 SPA 运行时动态文本（接口/CMS 数据）与静态托管种子的兜底。
- 不动消费者的 ssr 主流程：SPA 验证走 env 开关（如 `NUXT_SPA=1`）加独立 output 目录，见 references/integrate.md。
- 未注册的 alias 不进子集：先查 fontize.fonts 注册表，再怀疑提取通道。
- 生产浏览器端 useText 是 noop：静态托管站点的动态文本必须构建期进子集，否则线上缺字。

## References 地图

- `references/integrate.md`：两条接入路径（Nuxt 模块 / 纯 Vite 插件）、配置项全量、验证清单、SPA 并存验证法；Workflow A Step 1 与 Step 3 读
- `references/usage.md`：useText 形态决策（静态内联 / setup 声明 / getter / 数组源）、模板 ref 解包规则与 slot 重构、默认字体规则与反模式；Workflow A Step 2 与 Workflow B 读
- `references/troubleshoot.md`：dev / SSG / SPA 三通道收集模型、静态提取边界、SPA 动态文本处置、坑与修法、版本功能下限；Workflow C 读
