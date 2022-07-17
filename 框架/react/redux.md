# Redux

### Redux原则
 - 单一数据源
  - 整个应用程序的State应该存储在一个Object Tree中,并且这个Object Tree只存储于一个Store中
  - Redux不推荐创建多个Store
  - 单一数据源可以使整个应用程序的state更方便维护、追踪、修改
 - state是只读的
  - 唯一修改state的方式一定是触发action,不要试图通过其他方式来修改state
  - 所有修改都通过action进行集中化处理, 避免多个地方同时修改导致竞态问题
 - 纯函数
  - reducer将旧的state和action联系在一起, 并且返回一个新的state
  - reducer可以根据需求进行拆分
  - reducer应该是纯函数,不能产生副作用并保证输入对应唯一输出