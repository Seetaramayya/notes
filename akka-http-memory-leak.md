# Memory leak analysis

### Tools required

- [jcmd](https://docs.oracle.com/javase/8/docs/technotes/guides/troubleshoot/tooldescr006.html) is used for multiple tasks
  such as taking heapdumps, print threads along with stack traces, etc... 
  __It was working fine, but now getting Permission denied__ for `GC.heap_dump` rest are working
  
  ```
    sudo -u <user-id> jcmd <pid> GC.heap_dump heapdump.hprof (not working)
    sudo -u <user-id> jcmd <pid> GC.heap_info
    sudo -u <user-id> jcmd <pid> Thread.print
  ```
  `user-id` with which processing is running.
  
- [jmap](https://docs.oracle.com/javase/7/docs/technotes/tools/share/jmap.html) is another tool to take heap dumps
  
  ```
    sudo -u <user-id> jmap -dump:file=./heapdump.hprof <pid>
  ```

- [VisualVM](https://visualvm.github.io/) is used to analyze heap dumps
  - VisualVM can be used to analyze above taken heap dumps 
  - VisualVM can also be used to attach to existing PID if JMX is enabled
     - create a ssh tunnel
     - connect to local port using jmx


- With AkkaHttp 10.0.13 and Akka 2.4.20 combination, Source.queue left some garbage which is not cleaned ![img.png](visualvm.png)


### Useful commands 

- Take only live heap dump (before taking heap dump full GC will be triggered) 

```shell
sudo jmap -dump:live,format=b,file=/tmp/heapdump.hprof $pid
```

- `jcmd` with optional `-all` dumps all objects, including unreachable objects

### Resources

- [jmap](https://docs.oracle.com/en/java/javase/11/tools/jmap.html#GUID-D2340719-82BA-4077-B0F3-2803269B7F41) documentation
- [jcmd](https://docs.oracle.com/en/java/javase/14/docs/specs/man/jcmd.html) documentation
- [jcmd dzone blog](https://dzone.com/articles/jcmd-one-jdk-command-line-tool-to-rule-them-all)
- [AkkaHttp Memory leak](https://github.com/akka/akka-http/issues/1637) looks interesting, but it is with `10.0.11` and 
  `akka-2.5.7`, in my case my akka http version is `10.0.13` and `akka-2.4.20` 
