## Go-Crontab

> 基于 golang 的 crontab 定时任务管理器，解决在win环境开发下没有好用的定时任务管理器的囧境。

### 特点

- 支持分钟跟秒级
- 自动识别当前系统 unix系统内部调用 `bash -c` 命令，windows 系统内部调用  `cmd /c`
- 支持指定 bash 或 cmd 命令
- 自动生成log日志文件记录  
- 理论上跨平台支持 Windows 、Linux、MacOs

### 使用

自定义json配置文件
```
# 参数说明
{
  # 支持定义多个定时任务，直接在 crontab_cmd 追加对象即可
  "app": {
    "model": "s", # 默认 s 秒级 如需使用分钟级改为 m
    "exec_mode":"bash", # 默认空字符串,程序会根据当前操作系统自动区分执行bash还是cmd命令，支持指定命令执行，可选参数 bash/cmd
    "crontab_cmd": [
      {
        "crontab": "0/1 * * * * ?", #crontab 表达式
        "cmd": "go version" # 要执行的命令 
      }
    ]
  }
}

# windows 实现 laravel 的任务调度 schedule:run (只支持分钟级别，不能定义秒级的！！！)
# app.model 可以定义为 s ,但是对应的 cron表达式 必须为每分钟运行！可参考 Java(Quartz) 表达式书写
# 或直接定义 app.model 为 m ,则 cron表达式 为 "* * * * *"
# 命令表达式需要与对应环境匹配
# 如 bash 环境下的命令为：cd /e/www/project/dexter/laravel-test-demo && php artisan schedule:run 
# 那么 cmd 对应的命令就为：cd /d E:\www\project\dexter\laravel-test-demo && php artisan schedule:run


{
  "app": {
    "model": "s",
    "exec_mode":"bash",
    "crontab_cmd": [
      {
        "crontab": "0 0/1 * * * ?",
        "cmd": "go version"
      },
      {
        "crontab": "0 0/1 * * * ?",
        "cmd": "cd /e/www/project/dexter/laravel-test-demo && php artisan schedule:run"
      }
    ]
  }
}


```

#### [执行路径名中存在空格的解决方案](https://github.com/hezhizheng/go-crontab/issues/2#issuecomment-932979020)



### 编译

windows提供编译好的文件下载 [releases](https://github.com/SScrew/win-crontab/releases)
```
go build -ldflags "-s -w" -gcflags="all=-trimpath=${PWD}" -asmflags="all=-trimpath=${PWD}"
```

### 运行

- 保证编译的文件与 config.json 在同级目录

- 执行 ./go-crontab.exe (不要关闭终端)
![free-pic](https://cdn.jsdelivr.net/gh/hezhizheng/static-image-hosting@main/image-hosting/20210518172138_ARNYYAIVNXHJPYKX.png)

- 执行过程会自动生成log文件（默认保存一周，会定期清理）
```log
{"level":"info","msg":"执行命令： go version 输出： go version go1.15.2 windows/386\n 执行耗时： 0.10 s","time":"2021-07-24 10:39:00"}
{"level":"info","msg":"执行命令： php -f q.php 输出： sleep done 执行耗时： 5.15 s","time":"2021-07-24 10:39:05"}
```
