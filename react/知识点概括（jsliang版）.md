#### 前端历史演练
- jQuery
    ajax获取数据，通过jquery操作DOM更新到页面
- MVVM
    双向数据绑定 降低了手动更新DOM
- 虚拟DOM
    通过虚拟DOM对比收集一箩筐更新后才会去操作真实DOM
- diff
    比较虚拟DOM的算法 节点类型 属性 文本 子节点
- setState
    更新
- Redux
    状态管理中心store 解决了子组件之间数据传递问题 reducer.js action.js
#### React相比原生的好处
- 组件化
- 天然分层 MVVM
- 生态 成熟的解决方案
- 开发效率 不用手动更新DOM
#### React与Vue对比
vue暂不了解 先不做对比
### react15核心架构
- reconciler(协调器)React.createElement
1. 调用组件的render方法将jsx转换成虚拟dom
2. 将虚拟DOM与上次更新的虚拟DOM进行对比
3. 找出变化的虚拟DOM
4. 通知render将虚拟DOM渲染到页面上
    - 缺点：是递归去更新组件的
- renderer(渲染器)ReactDom.render
- 缺点：
    - reconciler递归更新的缺点：
        中途无法终端 如果递归层级很深占用时间过长会产生掉帧卡顿（同步不可中断）
    - reconciler与render交替同步进行
        如果中途reconciler中断则会导致用户看到没有完全更新的结果
### react16核心架构（任务分割时间分片）
- Scheduler（调度器）
任务调度（当前帧是否有剩余时间有的话通知reconciler）
- Reconciler（协调器）（Fiber架构）
1. 可中断的循环更新组件(这样依赖于fiber)（由递归变成链表）
2. 只有当reconciler找到所有要更新的组件并为组件打上标记才去通知renderer
- Renderer（渲染器）
#### ReactFiber
总结：有优先级的任务分割异步可中断渲染
- 为什么需要fiber
    为了解决大量组件更新进程长时间被占用出现饿一些丢帧卡顿情况
    而关键点便是同步阻塞
    解决同步阻塞有两种方法：异步与任务分割
    而fiber就是为了实现任务分割而诞生的
- fiber简述
    - 具有链表和指针的单链表树遍历算法。通过指针映射，每个单元格都记录着遍历当下的上一步与下一步，从而使遍历变得可以被暂停和重启。
    - 可以理解为一种任务分割调度算法。主要是将原来同步更新渲染的任务分割成一个个独立的小任务单元，根据不同优先级，将小任务分散到浏览器的空闲时间去执行。
- fiber核心
    - 可以具象为一个数据结构
    - 链表树遍历算法
    - 任务分割分散执行优先级
#### react生命周期
#### 组件通讯
    - props - context - redux

....未完成......

#### 日常遇到问题的补充
- props变化子组件渲染 原因：每次父组件render的时候都会给子组件传递新的props导致子组件做一些无效的更新（不去进行fiber等对比 只能让state/props不去变化或者用 shouldComponentUpdate阻止进行）
    - 如何避免无效渲染
        - props.children 
        - React.memo() 不会因为props地址变化而更新
        - 类组建shouldComponentUpdate
        - useMemo  这个不行 组件还是会执行fiber对比根据
- usecallback并不能真正实现render次数减小
- 海量数据优化
    - 时间分片
    - 虚拟列表