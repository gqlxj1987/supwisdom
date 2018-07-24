1. 在 prospectors 字段下的 paths里有读取的日志文件名和目录
```
filebeat.prospectors:
   paths:
     -   e:\sjj\supwisdom\YKT\ELK\jsonLog-tutorial.log
```

2.  Multiline options
    加入多行异常堆栈的读取参数
```   
     multiline.pattern: '^[[:space:]]'  
     multiline.negate: false
     multiline.match: after
```

