# 🚀 innovative-agent-ideas

## 💡 项目简介

这是一个基于 Camel-AI 和 OWL 的创新项目，旨在实现任务并行和浏览器操作等智能功能。该项目由我们3人小团队共同提出，致力于打造一个既具备 OWL 特性又能高效处理任务并行的智能 Agent。希望能吸引更多开发者一起参与和探讨！

## 🌟 创意背景

在研究 OWL Agent 的过程中，我们发现它虽然具有出色的浏览器操作和编程能力，但在任务并行性方面存在不足。我们的想法是将这两方面能力结合起来，开发出一个兼具 OWL 特性和高效任务并行处理能力的 Agent，实现更加智能和灵活的任务执行。

## 💭 初步想法
 1. **任务并行性**：增强 Agent 之间的协同能力，使其能高效完成复杂任务链。
 2. **浏览器操作**：继续保留 OWL 的浏览器操作能力，提升自动化水平。
 3. **可扩展性**：支持插件式架构，方便后续添加新功能模块。

## 🔧 预期的实现路径

### 主要思想：任务拆分与调度优化

引入任务依赖图思想，对任务进行更细粒度的拆解和调度。具体做法是：在 OWL 接到复杂任务后，由协调 Agent（类似 Camel 中的 task_agent）将其分解为若干可并行的子任务，并明确各子任务的依赖关系（形成 DAG）。Workforce 无需再假定所有任务线性依赖，而是根据依赖图选择“就绪”的任务并行发布。

例如，可以实现一个改进的 `_post_ready_tasks` 方法，扫描 `_pending_tasks` 列表中所有子任务，凡其所依赖的前置任务已完成（或无依赖）的，都通过 `post_task` 推送到通道中，让不同 Worker 同时开工：

```python
# 示例伪代码
class ParallelWorkforce(Workforce):
    async def _post_ready_tasks(self) -> None:
        if not self._pending_tasks:
            return
        ready_tasks = [t for t in self._pending_tasks if t.dependencies_resolved()]
        for task in ready_tasks:
            assignee = self._find_assignee(task)
            # 将任务发送给选定的 Worker，无需等待其他任务发送完毕
            await self._channel.post_task(task, self.node_id, assignee)
            self._pending_tasks.remove(task)
```
            
在上述伪代码中，`dependencies_resolved()` 用于判断任务依赖是否都已完成。这样一来，Workforce 一次可以发布多个彼此独立的子任务，多个 Agent Worker 会并行领取并执行各自任务，而不再局限于队首一个。  

为了实现依赖追踪，可以扩展 `Task` 对象，增加诸如 `depends_on` 列表以及依赖计数等属性，动态更新任务的就绪状态。  

学术上已有类似设计：如 DynTaskMAS 框架使用动态图调度子任务，并由异步并行执行引擎根据任务 DAG 最大化并行度。采用这种任务图调度后，当任务可并行时就不必串行等待，能够大幅减少整体完成时间。  

## 🧑‍🤝‍🧑 团队成员

本项目由三位创始人共同发起，致力于打造具有任务并行和浏览器操作能力的创新型 Agent。  
## 🧑‍🤝‍🧑 团队成员

本项目由三位创始人共同发起，致力于打造具有任务并行和浏览器操作能力的创新型 Agent。
 - **[@XinqiLiucute](https://github.com/XinqiLiucute)**
 - **[@YuchenGe-AM](https://github.com/YuchenGe-AM)**  
 - **[@zxqing-python](https://github.com/zxqing-python)**   

我们期待更多志同道合的开发者加入，共同推动项目发展！

我们期待更多志同道合的开发者加入，共同推动项目发展！  

## 📝 下一步计划
 1. 完善想法细节，形成可执行的开发文档。  
 2. 进行原型开发，验证任务并行能力。  
 3. 邀请有兴趣的小伙伴一起开发。  

## 🙌 如何参与

如果你有任何建议或点子，欢迎提 Issue 或提交 Pull Request！让我们一起来探索多智能体系统的新可能性！
