---
title: Monitor aplicativos Java em execução em qualquer ambiente - Azure Monitor Application Insights
description: Monitorização do desempenho da aplicação para aplicações Java em execução em qualquer ambiente com agente autónomo java sem instrumentar a aplicação. Mapa de rastreio e aplicação distribuído.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 08a83fbc05276808b62a0391a5c4217cc09f6d00
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641878"
---
# <a name="configuring-jvm-args-java-standalone-agent-for-azure-monitor-application-insights"></a>Configurar o agente autónomo da JVM args Java para insights de aplicação do Monitor Azure



## <a name="azure-environments"></a>Ambientes azure

Configure [Serviços de Aplicações](https://docs.microsoft.com/azure/app-service/configure-language-java#set-java-runtime-options).

## <a name="spring-boot"></a>Spring Boot

Adicione o jVM `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` `-jar <myapp.jar>`arg em algum lugar antes, por exemplo:

```
java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

> [!NOTE]
> Args colocados `-jar <myapp.jar>` depois são passados para a app como args do programa.


## <a name="tomcat-8-linux"></a>Tomcat 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>Tomcat instalado `apt-get` via ou`yum`

Se instalou o Tomcat via `apt-get` ou, `yum` `/etc/tomcat8/tomcat8.conf`então deve ter um ficheiro .  Adicione esta linha ao fim desse ficheiro:

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>Tomcat instalado via download e unzip

Se instalou o Tomcat através [https://tomcat.apache.org](https://tomcat.apache.org)do download e desapertar, então deve ter um ficheiro `<tomcat>/bin/catalina.sh`.  Crie um novo ficheiro no `<tomcat>/bin/setenv.sh` mesmo diretório com o seguinte conteúdo:

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Se o `<tomcat>/bin/setenv.sh` ficheiro já existir, então modifique esse ficheiro e adicione `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` a `CATALINA_OPTS`.


## <a name="tomcat-8-windows"></a>Tomcat 8 (Windows)

### <a name="running-tomcat-from-the-command-line"></a>Correndo Tomcat da linha de comando

Localize `<tomcat>/bin/catalina.bat`o ficheiro .  Crie um novo ficheiro no `<tomcat>/bin/setenv.bat` mesmo diretório com o seguinte conteúdo:

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

As cotações não são necessárias, mas se quiser incluí-las, a colocação adequada é:

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Se o `<tomcat>/bin/setenv.bat` ficheiro já existir, apenas modifique esse ficheiro e adicione `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` a `CATALINA_OPTS`.

### <a name="running-tomcat-as-a-windows-service"></a>Executando o Tomcat como um serviço Windows

Localize `<tomcat>/bin/tomcat8w.exe`o ficheiro .  Execute isso executável `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` e `Java Options` adicione `Java` ao separador.


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>Servidor autónomo

Adicione `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` à variável ambiental `JAVA_OPTS` existente `JBOSS_HOME/bin/standalone.conf` no ficheiro `JBOSS_HOME/bin/standalone.conf.bat` (Linux) ou (Windows):

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>Servidor de domínio

Adicione `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` ao existente `jvm-options` `JBOSS_HOME/domain/configuration/host.xml`em:

```xml
...
<jvms>
    <jvm name="default">
        <heap size="64m" max-size="256m"/>
        <jvm-options>
            <option value="-server"/>
            <!--Add Java agent jar file here-->
            <option value="-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"/>
            <option value="-XX:MetaspaceSize=96m"/>
            <option value="-XX:MaxMetaspaceSize=256m"/>
        </jvm-options>
    </jvm>
</jvms>
...
```

Se estiver a executar vários servidores geridos num `applicationinsights.agent.id` único `system-properties` anfitrião, terá de adicionar ao para cada um: `server`

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

O valor `applicationinsights.agent.id` especificado deve ser único. É usado para criar um subdiretório sob o diretório de aplicações insights, uma vez que cada processo JVM precisa dos seus próprios ficheiros de registo de aplicações locais config e aplicações locais. Além disso, se reportar `applicationinsights.properties` ao colecionador central, o ficheiro é partilhado pelos `applicationinsights.agent.id` múltiplos servidores geridos, pelo que o especificado é necessário para anular a `agent.id` definição nesse ficheiro partilhado. `applicationinsights.agent.rollup.id`pode ser especificado de forma `system-properties` semelhante no servidor se `agent.rollup.id` precisar de anular a definição por servidor gerido.


## <a name="jetty-9"></a>Cais 9

Adicione estas linhas a`start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```


## <a name="payara-5"></a>Payara 5

Adicione `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` ao existente `jvm-options` `glassfish/domains/domain1/config/domain.xml`em:

```xml
...
<java-config ...>
    <!--Edit the JVM options here-->
    <jvm-options>
        -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar>
    </jvm-options>
        ...
</java-config>
...
```

## <a name="websphere-8"></a>WebSphere 8

A Consola de Gestão Aberta vai para **servidores > servidores de aplicações WebSphere > servidores de aplicações,** escolha os servidores de aplicação apropriados e clique em: 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
Em "Argumentos Genéricos JVM" adicione o seguinte:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
Depois disso, guarde e reinicie o servidor de aplicações.


## <a name="openliberty-18"></a>OpenLiberty 18

Crie um `jvm.options` novo ficheiro no diretório do servidor (por exemplo), `<openliberty>/usr/servers/defaultServer`e adicione esta linha:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
