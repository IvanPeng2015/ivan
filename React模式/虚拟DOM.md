# virtual dom

1. 提供一种方便的工具，f(data) => view 状态到视图的映射, 组件化开发, 使得开发效率得到保证
2. 保证最小化的DOM操作带来的重新渲染（包括layout、paint和composite）, 使得执行效率得到保证

如何最小化渲染消耗呢?  diff 算法 + render, 将优化步骤统一发那个在 render 这个函数中去处理;

# diffing 算法设计

  由virtual dom(ReactElement<any, any> | null)组成的树, 映射出DOM树, 基于React元素树的变更差异来更新DOM树.
