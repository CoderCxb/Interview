# 持续集成(CI)



# 持续部署(CD)


# GitLab
 - GitLab和Runner建议部署在不同的服务器


###### Runner
Runner中

###### 缓存 - cache

###### 流水线 - pipeline
 - 工作(Job)，定义做什么。例如，编译或测试代码的作业。
 - 阶段(Stage)，定义何时运行作业。例如，在编译代码的阶段之后运行测试的阶段。
 - 变量(Variables), 定义全局变量
 - 缓存(Cache), 设置缓存


###### job
 - rules: 配置触发条件
 - scripts: 配置job触发时执行的脚本

###### beforeScript


###### afterScript

###### stage


###### 流程
 - 代码推送会触发 pipeline