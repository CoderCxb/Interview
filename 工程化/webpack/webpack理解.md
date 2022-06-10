# webpack理解
 - webpack配置文件中的属性会被解析并注入到编译器上(compiler),compiler贯穿整个webpack生命周期且唯一


###### 生命周期
before -> run -> beforeCompile -> compile -> make -> fnishMake -> afterCompile -> done


###### compiler 
在webpack构建之初就会创建的对象, 并且在整个生命周期都会存在,整个构建只会对应一个compiler


###### comopilation
编译产物, 主要存在于compiler -> 创建compilation -> make 阶段, 整个构建会存在多个compilation