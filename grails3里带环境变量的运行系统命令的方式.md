# grails3里带环境变量的运行系统命令的方式

## 实现

```java
    /**
     * @param cmd
     * @return
     */
    String runCommand(String cmd) {
        Scanner s = null;

        def wdir = new File(Holders.getGrailsApplication().config.getProperty('sonar.cc.homedir', "/home/sonarcc/sonar65cc_ifx_c3t1")).getAbsoluteFile();
        def env = System.getenv();
        def envlist = [];
        env.each() { k, v -> envlist.push("$k=$v") }

        log.info(cmd)
        Process proc = ["bash", "-c", cmd].execute([], wdir);
        s = new Scanner(proc.getInputStream());
        StringBuilder builder = new StringBuilder();
        while (s.hasNextLine()) {
            builder.append(s.nextLine() + "\n");
        }
        return builder.toString()
    }
```

## 其实就是利用execute的第二个参数，可以传一个内部元素为"key=value"形式的数组，来设置当前的全部环境变量。

### 后面有其他实现，我再补进来
