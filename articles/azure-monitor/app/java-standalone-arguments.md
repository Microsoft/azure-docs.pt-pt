---
title: Adicionar o JVM arg - Azure Monitor Application Insights for Java
description: Como adicionar o arg JVM que permite insights de aplicação do monitor Azure para Java
ms.topic: conceptual
ms.date: 04/16/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 21465305e635cba7d8d4f912bcca2b0306b294e9
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2021
ms.locfileid: "98233700"
---
# <a name="adding-the-jvm-arg---azure-monitor-application-insights-for-java"></a>Adicionar o JVM arg - Azure Monitor Application Insights for Java



## <a name="azure-environments"></a>Ambientes azuis

Configure [os Serviços de Aplicações.](../../app-service/configure-language-java.md#set-java-runtime-options)

## <a name="spring-boot"></a>Spring Boot

Adicione o arg JVM `-javaagent:path/to/applicationinsights-agent-3.0.1.jar` em algum lugar antes , por `-jar` exemplo:

```
java -javaagent:path/to/applicationinsights-agent-3.0.1.jar -jar <myapp.jar>
```

## <a name="spring-boot-via-docker-entry-point"></a>Bota de primavera via ponto de entrada do Docker

Se estiver a utilizar o formulário *executivo,* adicione o parâmetro `"-javaagent:path/to/applicationinsights-agent-3.0.1.jar"` à lista de parâmetros algures antes do `"-jar"` parâmetro, por exemplo:

```
ENTRYPOINT ["java", "-javaagent:path/to/applicationinsights-agent-3.0.1.jar", "-jar", "<myapp.jar>"]
```

Se estiver a utilizar o *formulário de concha,* adicione o arg JVM `-javaagent:path/to/applicationinsights-agent-3.0.1.jar` em algum lugar antes , por `-jar` exemplo:

```
ENTRYPOINT java -javaagent:path/to/applicationinsights-agent-3.0.1.jar -jar <myapp.jar>
```

## <a name="tomcat-8-linux"></a>Tomcat 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>Tomcat instalado via `apt-get` ou `yum`

Se instalou o Tomcat via `apt-get` `yum` ou, então deve ter um ficheiro `/etc/tomcat8/tomcat8.conf` .  Adicione esta linha ao fim do ficheiro:

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.1.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>Tomcat instalado via download e unzip

Se instalou o Tomcat através do download e desapertar [https://tomcat.apache.org](https://tomcat.apache.org) de , então deve ter um ficheiro `<tomcat>/bin/catalina.sh` .  Criar um novo ficheiro no mesmo diretório nomeado `<tomcat>/bin/setenv.sh` com o seguinte conteúdo:

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.1.jar"
```

Se o ficheiro `<tomcat>/bin/setenv.sh` já existir, então modifique esse ficheiro e adicione `-javaagent:path/to/applicationinsights-agent-3.0.1.jar` a `CATALINA_OPTS` .


## <a name="tomcat-8-windows"></a>Tomcat 8 (Janelas)

### <a name="running-tomcat-from-the-command-line"></a>Executando Tomcat da linha de comando

Localize o ficheiro `<tomcat>/bin/catalina.bat` .  Criar um novo ficheiro no mesmo diretório nomeado `<tomcat>/bin/setenv.bat` com o seguinte conteúdo:

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.1.jar
```

As cotações não são necessárias, mas se quiser incluí-las, a colocação adequada é:

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.1.jar"
```

Se o ficheiro `<tomcat>/bin/setenv.bat` já existir, basta modificar esse ficheiro e adicionar `-javaagent:path/to/applicationinsights-agent-3.0.1.jar` a `CATALINA_OPTS` .

### <a name="running-tomcat-as-a-windows-service"></a>Executar Tomcat como um serviço Windows

Localize o ficheiro `<tomcat>/bin/tomcat8w.exe` .  Execute-o executável e adicione `-javaagent:path/to/applicationinsights-agent-3.0.1.jar` a parte de baixo da `Java Options` `Java` conta.


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>Servidor autónomo

Adicione `-javaagent:path/to/applicationinsights-agent-3.0.1.jar` à variável ambiente existente `JAVA_OPTS` no ficheiro `JBOSS_HOME/bin/standalone.conf` (Linux) ou `JBOSS_HOME/bin/standalone.conf.bat` (Windows):

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.1.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>Servidor de domínio

Adicione `-javaagent:path/to/applicationinsights-agent-3.0.1.jar` ao existente `jvm-options` `JBOSS_HOME/domain/configuration/host.xml` em:

```xml
...
<jvms>
    <jvm name="default">
        <heap size="64m" max-size="256m"/>
        <jvm-options>
            <option value="-server"/>
            <!--Add Java agent jar file here-->
            <option value="-javaagent:path/to/applicationinsights-agent-3.0.1.jar"/>
            <option value="-XX:MetaspaceSize=96m"/>
            <option value="-XX:MaxMetaspaceSize=256m"/>
        </jvm-options>
    </jvm>
</jvms>
...
```

Se estiver a executar vários servidores geridos num único anfitrião, terá de adicionar `applicationinsights.agent.id` ao `system-properties` para `server` cada:

```xml
...
<servers>
    <server name="server-one" group="main-server-group">
        <!--Edit system properties for server-one-->
        <system-properties> 
            <property name="applicationinsights.agent.id" value="..."/>
        </system-properties>
    </server>
    <server name="server-two" group="main-server-group">
        <socket-bindings port-offset="150"/>
        <!--Edit system properties for server-two-->
        <system-properties>
            <property name="applicationinsights.agent.id" value="..."/> 
        </system-properties>
    </server>
</servers>
...
```

O valor especificado `applicationinsights.agent.id` deve ser único. É utilizado para criar um subdiretório no âmbito do diretório de aplicaçõesinsights, uma vez que cada processo JVM precisa das suas próprias aplicações locais intimula o ficheiro de registo de aplicações config e aplicações locais. Além disso, se reportar ao coletor central, o `applicationinsights.properties` ficheiro é partilhado pelos múltiplos servidores geridos, pelo que o especificado é necessário para anular a `applicationinsights.agent.id` `agent.id` definição nesse ficheiro partilhado. `applicationinsights.agent.rollup.id` pode ser especificado de forma semelhante no do servidor `system-properties` se precisar de anular a `agent.rollup.id` definição por servidor gerido.


## <a name="jetty-9"></a>Cais 9

Adicione estas linhas para `start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.1.jar
```


## <a name="payara-5"></a>Payara 5

Adicione `-javaagent:path/to/applicationinsights-agent-3.0.1.jar` ao existente `jvm-options` `glassfish/domains/domain1/config/domain.xml` em:

```xml
...
<java-config ...>
    <!--Edit the JVM options here-->
    <jvm-options>
        -javaagent:path/to/applicationinsights-agent-3.0.1.jar>
    </jvm-options>
        ...
</java-config>
...
```

## <a name="websphere-8"></a>WebSphere 8

O Console de Gestão Aberta vai para **servidores > servidores de aplicações webSphere > servidores de aplicação**, escolha os servidores de aplicações apropriados e clique em: 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
Em "Argumentos Genéricos JVM" adicione o seguinte:
```
-javaagent:path/to/applicationinsights-agent-3.0.1.jar
```
Depois disso, guarde e reinicie o servidor de aplicações.


## <a name="openliberty-18"></a>OpenLiberty 18

Crie um novo ficheiro `jvm.options` no diretório do servidor (por `<openliberty>/usr/servers/defaultServer` exemplo), e adicione esta linha:
```
-javaagent:path/to/applicationinsights-agent-3.0.1.jar
```
