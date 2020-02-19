---
title: Configure aplicativos Linux Java
description: Aprenda a configurar um recipiente Java pré-construído para a sua aplicação. Este artigo mostra as tarefas de configuração mais comuns.
keywords: serviço de aplicações azure, web app, linux, oss, java, java ee, jee, javaee
author: bmitchell287
manager: barbkess
ms.devlang: java
ms.topic: article
ms.date: 11/22/2019
ms.author: brendm
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: 1b9db20da58f50c91da88c2f9f890623b741f10a
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443878"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Configure um aplicativo Linux Java para o Azure App Service

O Azure App Service no Linux permite que os desenvolvedores da Java construam, implementem e dimensionem rapidamente as suas aplicações web embaladas tomcat, ou Java Standard Edition (SE) num serviço totalmente gerido com base em Linux. Implemente aplicações com plugins Maven da linha de comando ou em editores como IntelliJ, Eclipse ou Visual Studio Code.

Este guia fornece conceitos e instruções fundamentais para os desenvolvedores java que usam um recipiente Linux incorporado no Serviço de Aplicações. Se nunca usou o Azure App Service, siga o início rápido de [Java.](quickstart-java.md)

## <a name="deploying-your-app"></a>Implementação da sua aplicação

Pode utilizar [o Maven Plugin para](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) o Azure App Service para implementar ficheiros .jar e .war. A implantação com IDEs populares também é suportada com [O Kit de Ferramentas Azure para IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) ou [Azure Toolkit para Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

Caso contrário, o seu método de implantação dependerá do seu tipo de arquivo:

- Para implementar ficheiros de guerra para o Tomcat, utilize o ponto final `/api/wardeploy/` para publicar o seu ficheiro de arquivo. Para mais informações sobre esta API, consulte [esta documentação.](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file)
- Para implementar ficheiros .jar nas imagens Java SE, utilize o ponto final `/api/zipdeploy/` do site Kudu. Para mais informações sobre esta API, consulte [esta documentação.](https://docs.microsoft.com/azure/app-service/deploy-zip#rest)

Não desloque a sua .war ou .jar utilizando FTP. A ferramenta FTP foi concebida para carregar scripts de arranque, dependências ou outros ficheiros de tempo de execução. Não é a escolha ideal para implementar aplicações web.

## <a name="logging-and-debugging-apps"></a>Aplicativos de registo e depuração

Relatórios de desempenho, visualizações de tráfego e check-ups de saúde estão disponíveis para cada aplicação através do portal Azure. Para mais informações, consulte a visão geral dos diagnósticos do Serviço de [Aplicações Azure](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>Acesso à consola SSH

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Transmitir registos de diagnóstico

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Para mais informações, consulte os [registos do Stream na Cloud Shell](../troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Registo de aplicações

Ative o [registo de aplicações](../troubleshoot-diagnostic-logs.md?toc=/azure/app-service/containers/toc.json#enable-application-logging-windows) através do portal Azure ou [do Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) para configurar o App Service para escrever a saída padrão da consola da sua aplicação e os fluxos de erros padrão da consola para o sistema de ficheiros local ou para o Armazenamento De Blob Azure. O registo na instância do sistema de ficheiros do Serviço de Aplicações local é desativado 12 horas após a sua configuração. Se necessitar de retenção mais longa, configure a aplicação para escrever saída para um recipiente de armazenamento Blob. Os registos das suas aplicações Java e Tomcat podem ser encontrados no */home/LogFiles/Application/diretório.*

Se a sua aplicação utilizar [logback](https://logback.qos.ch/) ou [Log4j](https://logging.apache.org/log4j) para rastreio, pode reencaminhar estes vestígios para revisão em Insights de Aplicação Azure utilizando as instruções de configuração de configuração de configuração de login em [Explore Java tracelogs in Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Ferramentas de resolução de problemas

As imagens java incorporadas baseiam-se no sistema operativo [Alpine Linux.](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) Utilize o gestor de pacotes `apk` para instalar quaisquer ferramentas ou comandos de resolução de problemas.

### <a name="flight-recorder"></a>Gravador de voo

Todas as imagens do Linux Java no Serviço de Aplicações têm o Zulu Flight Recorder instalado para que possa ligar-se facilmente ao JVM e iniciar uma gravação de perfis ou gerar uma lixeira de heap.

#### <a name="timed-recording"></a>Gravação cronometrada

Para começar, sSH no seu Serviço de Aplicações e executar o comando `jcmd` para ver uma lista de todos os processos java em execução. Além da própria JCMD, deve ver a sua aplicação Java a funcionar com um número de ID de processo (pid).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Execute o comando abaixo para iniciar uma gravação de 30 segundos do JVM. Isto irá perfilar o JVM e criar um ficheiro JFR chamado *jfr_example.jfr* no diretório inicial. (Substitua 116 pelo pid da sua aplicação Java.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Durante o intervalo de 30 segundos, pode validar que a gravação está a decorrer executando `jcmd 116 JFR.check`. Isto mostrará todas as gravações para o processo de Java.

#### <a name="continuous-recording"></a>Gravação Contínua

Pode utilizar o Gravador de Voo Zulu para perfilar continuamente a sua aplicação Java com o mínimo impacto no desempenho do tempo de funcionamento[(fonte).](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf) Para tal, execute o seguinte comando Azure CLI para criar uma Definição de Aplicações chamada JAVA_OPTS com a configuração necessária. Os conteúdos da Definição de Aplicações JAVA_OPTS são transmitidos ao comando `java` quando a sua aplicação é iniciada.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Uma vez iniciada a gravação, pode despejar os dados de gravação atuais a qualquer momento utilizando o comando `JFR.dump`.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

Para mais informações, por favor leia as informações sobre o [comando da JCMD.](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190)

### <a name="analyzing-recordings"></a>Análise de Gravações

Utilize [FTPS](../deploy-ftp.md) para transferir o seu ficheiro JFR para a sua máquina local. Para analisar o ficheiro JFR, descarregue e instale o [Controlo da Missão Zulu.](https://www.azul.com/products/zulu-mission-control/) Para obter instruções sobre o Controlo da Missão Zulu, consulte a [documentação Azul](https://docs.azul.com/zmc/) e as instruções de [instalação](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

## <a name="customization-and-tuning"></a>Personalização e afinação

O Azure App Service para o Linux suporta a afinação e personalização da caixa através do portal Azure e do CLI. Reveja os seguintes artigos para a configuração de aplicações web não específicas de Java:

- [Configurar as definições de aplicativos](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings)
- [Configurar um domínio personalizado](../app-service-web-tutorial-custom-domain.md?toc=/azure/app-service/containers/toc.json)
- [Configure as ligações SSL](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json)
- [Adicione um CDN](../../cdn/cdn-add-to-web-app.md?toc=/azure/app-service/containers/toc.json)
- [Configure o site kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Definir opções de tempo de execução de Java

Para definir a memória atribuída ou outras opções de tempo de execução JVM nos ambientes Tomcat e Java SE, crie uma definição de [aplicação](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings) chamada `JAVA_OPTS` com as opções. O Serviço de Aplicações Linux passa esta configuração como uma variável ambiental para o tempo de funcionação de Java quando começa.

No portal Azure, no âmbito das Definições de **Aplicação** para a aplicação web, criar uma nova definição de aplicação chamada `JAVA_OPTS` que inclua as configurações adicionais, como `-Xms512m -Xmx1204m`.

Para configurar a definição da aplicação a partir do plugin Maven, adicione etiquetas de definição/valor na secção plugin Azure. O exemplo que se segue define um tamanho mínimo e máximo de monte java específico:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Os desenvolvedores que executam uma única aplicação com uma ranhura de implementação no seu plano de Serviço de Aplicações podem usar as seguintes opções:

- B1 e S1 casos: `-Xms1024m -Xmx1024m`
- Casos B2 e S2: `-Xms3072m -Xmx3072m`
- Casos B3 e S3: `-Xms6144m -Xmx6144m`

Ao afinar as definições de pilhas de aplicações, reveja os detalhes do plano do App Service e tenha em conta várias aplicações e o slot de implementação precisa para encontrar a melhor alocação de memória.

Se estiver a implementar uma aplicação JAR, deve ser nomeada *app.jar* para que a imagem incorporada possa identificar corretamente a sua aplicação. (O plugin Maven faz este renomeamento automaticamente.) Se não quiser mudar o nome do seu JAR para *app.jar,* pode carregar um script de concha com o comando para executar o seu JAR. Em seguida, cole o caminho completo para este script na caixa de texto [Ficheiro de Arranque](app-service-linux-faq.md#built-in-images) na secção Configuração do portal. O script de arranque não é executado a partir do diretório no qual é colocado. Por isso, utilize sempre caminhos absolutos para referenciar ficheiros no script de arranque (por exemplo: `java -jar /home/myapp/myapp.jar`).

### <a name="turn-on-web-sockets"></a>Ligue as tomadas web

Ligue o suporte para tomadas web no portal Azure nas definições de **Aplicação** para a aplicação. Terá de reiniciar a aplicação para que a definição faça efeito.

Ligue o suporte da tomada web utilizando o Azure CLI com o seguinte comando:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Em seguida, reinicie a sua aplicação:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Definir codificação de caracteres padrão

No portal Azure, no âmbito das Definições de **Aplicação** para a aplicação web, criar uma nova configuração de aplicação chamada `JAVA_OPTS` com valor `-Dfile.encoding=UTF-8`.

Em alternativa, pode configurar a definição da aplicação utilizando o plugin Maven do Serviço de Aplicações. Adicione o nome de definição e as etiquetas de valor na configuração do plugin:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="adjust-startup-timeout"></a>Ajuste o tempo de arranque

Se a sua aplicação Java for particularmente grande, deverá aumentar o prazo de arranque. Para tal, crie uma definição de aplicação, `WEBSITES_CONTAINER_START_TIME_LIMIT` e ajuste-a para o número de segundos que o Serviço de Aplicações deve esperar antes de o cronometrar. O valor máximo é `1800` segundos.

### <a name="pre-compile-jsp-files"></a>Pré-compilação de ficheiros JSP

Para melhorar o desempenho das aplicações Tomcat, pode compilar os seus ficheiros JSP antes de ser implementado no App Service. Pode utilizar o [plugin Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) fornecido pela Apache Sling, ou utilizar este [ficheiro de construção Ant](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Aplicações seguras

As aplicações Java que executam o Serviço de Aplicações para o Linux têm o mesmo conjunto de [boas práticas](/azure/security/security-paas-applications-using-app-services) de segurança que outras aplicações.

### <a name="authenticate-users-easy-auth"></a>Utilizadores autenticados (Easy Auth)

Configurar a autenticação da aplicação no portal Azure com a opção **Autenticação e Autorização.** A partir daí, pode ativar a autenticação utilizando o Diretório Ativo do Azure ou logins sociais como facebook, Google ou GitHub. A configuração do portal Azure só funciona quando configurar um único fornecedor de autenticação. Para mais informações, consulte [a configuração da sua app App Service para utilizar o login do Azure Ative Directory](../configure-authentication-provider-aad.md?toc=/azure/app-service/containers/toc.json) e os artigos relacionados para outros fornecedores de identidade. Se precisar de ativar vários fornecedores de sessão, siga as instruções no artigo de autenticação do Serviço de [Aplicações personalizado.](../app-service-authentication-how-to.md?toc=/azure/app-service/containers/toc.json)

#### <a name="tomcat"></a>Tomcat

A sua aplicação Tomcat pode aceder às reclamações do utilizador diretamente a partir do servlet, lançando o objeto principal para um objeto Map. O objeto do Mapa irá mapear cada tipo de reclamação para uma coleção das reivindicações desse tipo. No código abaixo, `request` é um caso de `HttpServletRequest`.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Agora pode inspecionar o objeto `Map` para qualquer reclamação específica. Por exemplo, o código seguinte itera através de todos os tipos de reclamação e imprime o conteúdo de cada coleção.

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

Para contratar os utilizadores, utilize o caminho `/.auth/ext/logout`. Para realizar outras ações, consulte a documentação sobre [autenticação e utilização](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to)de autorização de serviço de aplicação. Existe também documentação oficial sobre a interface Tomcat [HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) e os seus métodos. Os seguintes métodos de servlet também são hidratados com base na configuração do seu Serviço de Aplicações:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Para desativar esta funcionalidade, crie uma Definição de Aplicação chamada `WEBSITE_AUTH_SKIP_PRINCIPAL` com um valor de `1`. Para desativar todos os filtros de servlet adicionados pelo App Service, crie uma definição chamada `WEBSITE_SKIP_FILTERS` com um valor de `1`.

#### <a name="spring-boot"></a>Spring Boot

Os desenvolvedores de spring boot podem usar o arranque de bota de [mola azure ative diretório](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) para garantir aplicações usando anotações familiares de Segurança da primavera e APIs. Certifique-se de aumentar o tamanho máximo do cabeçalho no ficheiro *application.properties.* Sugerimos um valor de `16384`.

### <a name="configure-tlsssl"></a>Configurar TLS/SSL

Siga as instruções no [nome 'Secure' um DNS personalizado com uma ligação SSL no Serviço de Aplicações Azure](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json) para fazer o upload de um certificado SSL existente e ligá-lo ao nome de domínio da sua aplicação. Por defeito, a sua aplicação continuará a permitir que as ligações HTTP sigam os passos específicos do tutorial para impor sSL e TLS.

### <a name="use-keyvault-references"></a>Utilizar referências keyVault

[O Azure KeyVault](../../key-vault/key-vault-overview.md) fornece uma gestão secreta centralizada com políticas de acesso e histórico de auditoria. Pode armazenar segredos (como palavras-passe ou cordas de ligação) no KeyVault e aceder a estes segredos na sua aplicação através de variáveis ambientais.

Em primeiro lugar, siga as instruções para [conceder o acesso da sua aplicação ao Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) e fazer uma referência ao [KeyVault ao seu segredo numa Definição](../app-service-key-vault-references.md#reference-syntax)de Aplicação . Pode validar que a referência resolve o segredo imprimindo a variável ambiental ao aceder remotamente ao terminal do Serviço de Aplicações.

Para injetar estes segredos no seu ficheiro de configuração Spring ou Tomcat, utilize sintaxe de injeção variável ambiente (`${MY_ENV_VAR}`). Para os ficheiros de configuração da Mola, consulte esta documentação sobre [configurações externas](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

### <a name="using-the-java-key-store"></a>Usando a Loja chave Java

Por predefinição, quaisquer certificados públicos ou privados [enviados para o App Service Linux](../configure-ssl-certificate.md) serão carregados nas respetivas Lojas Chave Java à medida que o recipiente começa. Depois de fazer o upload do seu certificado, terá de reiniciar o seu Serviço de Aplicações para que seja carregado na Java Key Store. Os certificados públicos são carregados na Key Store em `$JAVA_HOME/jre/lib/security/cacerts`, e os certificados privados são armazenados em `$JAVA_HOME/lib/security/client.jks`.

Pode ser necessária uma configuração adicional para encriptar a sua ligação JDBC com certificados na Loja chave Java. Consulte a documentação para o seu motorista jDBC escolhido.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initializing-the-java-key-store"></a>Inicializando a Loja chave Java

Para inicializar o objeto `import java.security.KeyStore`, carregue o ficheiro da loja de chaves com a palavra-passe. A palavra-passe padrão para ambas as lojas chave é "changeit".

```java
KeyStore keyStore = KeyStore.getInstance("jks");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/cacets"),
    "changeit".toCharArray());

KeyStore keyStore = KeyStore.getInstance("pkcs12");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/client.jks"),
    "changeit".toCharArray());
```

#### <a name="manually-load-the-key-store"></a>Carregue manualmente a loja-chave

Pode carregar os certificados manualmente na loja de chaves. Crie uma definição de aplicação, `SKIP_JAVA_KEYSTORE_LOAD`, com um valor de `1` para desativar o Serviço de Aplicações de carregar automaticamente os certificados na loja-chave. Todos os certificados públicos enviados para o Serviço de Aplicações através do portal Azure são armazenados em `/var/ssl/certs/`. Os certificados privados são armazenados em `/var/ssl/private/`.

Pode interagir ou depurar a Ferramenta Chave Java [abrindo uma ligação SSH](app-service-linux-ssh-support.md) ao seu Serviço de Aplicações e executando o comando `keytool`. Consulte a [documentação](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) da Ferramenta Chave para obter uma lista de comandos. Para mais informações sobre a KeyStore API, consulte [a documentação oficial](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

## <a name="configure-apm-platforms"></a>Configure plataformas APM

Esta secção mostra como ligar as aplicações java implantadas no Serviço de Aplicações Azure em Linux com as plataformas de monitorização de desempenho da aplicação NewRelic e AppDynamics (APM).

### <a name="configure-new-relic"></a>Configurar nova relíquia

1. Criar uma conta NewRelic em [NewRelic.com](https://newrelic.com/signup)
2. Download the Java agent from NewRelic, it will have a file name similar to *newrelic-java-x.x.x.zip*.
3. Copie a chave da sua licença, terá de a configurar para configurar o agente mais tarde.
4. [SSH na sua instância](app-service-linux-ssh-support.md) de Serviço de Aplicações e crie um novo diretório */home/site/wwwroot/apm*.
5. Faça upload dos ficheiros do agente NewRelic Java desembalados num diretório em */home/site/wwwroot/apm*. Os ficheiros do seu agente devem estar em */home/site/wwwroot/apm/newrelic*.
6. Modifique o ficheiro YAML em */home/site/wwwroot/apm/newrelic/newrelic.yml* e substitua o valor da licença de proprietário por conta própria.
7. No portal Azure, navegue para a sua aplicação no Serviço de Aplicações e crie uma nova Definição de Aplicações.
    - Se a sua aplicação estiver a utilizar **o Java SE,** crie uma variável ambiental chamada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se estiver a usar **o Tomcat,** crie uma variável ambiental chamada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.

### <a name="configure-appdynamics"></a>Configure AppDynamics

1. Criar uma conta AppDynamics em [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Descarregue o agente Java a partir do website appDynamics, o nome do ficheiro será semelhante ao *AppServerAgent-x.x.x.xxxxx.zip*
3. [SSH na sua instância](app-service-linux-ssh-support.md) de Serviço de Aplicações e crie um novo diretório */home/site/wwwroot/apm*.
4. Faça upload dos ficheiros do agente Java para um diretório em */home/site/wwwroot/apm*. Os ficheiros do seu agente devem estar em */home/site/wwwroot/apm/appdynamics*.
5. No portal Azure, navegue para a sua aplicação no Serviço de Aplicações e crie uma nova Definição de Aplicações.
    - Se estiver a usar **o Java SE,** crie uma variável ambiental chamada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` onde `<app-name>` é o seu nome de Serviço de Aplicações.
    - Se estiver a usar **o Tomcat,** crie uma variável ambiental chamada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` onde `<app-name>` é o seu nome de Serviço de Aplicações.

> [!NOTE]
> Se já tiver uma variável ambiental para `JAVA_OPTS` ou `CATALINA_OPTS`, apreenda a opção `-javaagent:/...` até ao fim do valor atual.

## <a name="configure-jar-applications"></a>Configure aplicações jar

### <a name="starting-jar-apps"></a>Aplicações DE JARRO de início

Por padrão, o Serviço de Aplicações espera que a sua aplicação JAR seja nomeada *app.jar*. Se tiver este nome, será executado automaticamente. Para os utilizadores da Maven, pode definir o nome JAR, incluindo `<finalName>app</finalName>` na secção `<build>` do seu *pom.xml*. [Você pode fazer o mesmo em Gradle,](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) definindo a propriedade `archiveFileName`.

Se pretender utilizar um nome diferente para o seu JAR, deve também fornecer o Comando de [Arranque](app-service-linux-faq.md#built-in-images) que executa o seu ficheiro JAR. Por exemplo, `java -jar my-jar-app.jar`. Pode definir o valor para o seu Comando de Arranque no Portal, em Definições Gerais de Configuração > ou com uma Definição de Aplicação denominada `STARTUP_COMMAND`.

### <a name="server-port"></a>Porta do servidor

Serviço de aplicações O Linux encaminha os pedidos de entrada para o porto 80, pelo que a sua aplicação deve ouvir também na porta 80. Pode fazê-lo na configuração da sua aplicação (como o ficheiro *aplicação.properties* da Spring), ou no seu Comando de Arranque (por exemplo, `java -jar spring-app.jar --server.port=80`). Consulte a seguinte documentação para os quadros comuns de Java:

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [Faísca Java](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Quadro de Jogo](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>Origens de dados

### <a name="tomcat"></a>Tomcat

Estas instruções aplicam-se a todas as ligações de base de dados. Terá de preencher os espaços reservados com o nome da classe de motorista da sua base de dados escolhida e o ficheiro JAR. Fornecido é uma tabela com nomes de classe e transferências de motorista para bases de dados comuns.

| Base de Dados   | Nome da classe do condutor                             | Condutor jDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Transferência](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Baixar](https://dev.mysql.com/downloads/connector/j/) (Selecione "Platform Independent") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Transferência](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Para configurar o Tomcat para utilizar a Conectividade da Base de Dados Java (JDBC) ou a Java Persistence API (JPA), primeiro personalize a variável ambiente `CATALINA_OPTS` que é lida pela Tomcat no arranque. Defina estes valores através de uma definição de aplicação no [plugin Maven Service:](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Ou definir as variáveis ambientais na página definição de **Configuração** > Definições de **Aplicação** no portal Azure.

Em seguida, determine se a fonte de dados deve estar disponível para uma aplicação ou para todas as aplicações em execução no servlet Tomcat.

#### <a name="application-level-data-sources"></a>Fontes de dados ao nível da aplicação

1. Crie um ficheiro *context.xml* no *meta-INF/diretório* do seu projeto. Crie o *meta-INF/diretório* se não existir.

2. No *contexto.xml,* adicione um elemento `Context` para ligar a fonte de dados a um endereço JNDI. Substitua o espaço reservado `driverClassName` pelo nome da classe do condutor na tabela acima.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. Atualize o *web.xml* da sua aplicação para utilizar a fonte de dados na sua aplicação.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Recursos partilhados ao nível do servidor

A adição de uma fonte de dados partilhada ao nível do servidor exigirá que edite o servidor do Tomcat.xml. Em primeiro lugar, faça upload de um script de [arranque](app-service-linux-faq.md#built-in-images) e desloque o caminho para o script em **Configuração** > **Comando de Arranque**. Pode fazer o upload do script de arranque utilizando [FTP](../deploy-ftp.md).

O seu script de arranque fará com que uma [transformação xsl](https://www.w3schools.com/xml/xsl_intro.asp) para o ficheiro server.xml e produza o ficheiro xml resultante para `/usr/local/tomcat/conf/server.xml`. O script de arranque deve instalar libxslt via apk. O seu ficheiro xsl e o seu script de arranque podem ser carregados via FTP. Abaixo está um roteiro de arranque de exemplo.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Um ficheiro xsl exemplo é fornecido abaixo. O ficheiro xsl exemplo adiciona um novo nó de conector ao servidor Tomcat.xml.

```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:output method="xml" indent="yes"/>

  <xsl:template match="@* | node()" name="Copy">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()"/>
    </xsl:copy>
  </xsl:template>

  <xsl:template match="@* | node()" mode="insertConnector">
    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                 contains(., '&lt;Connector') and
                                 (contains(., 'scheme=&quot;https&quot;') or
                                  contains(., &quot;scheme='https'&quot;))]">
    <xsl:value-of select="." disable-output-escaping="yes" />
  </xsl:template>

  <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                   comment()[contains(., '&lt;Connector') and
                                             (contains(., 'scheme=&quot;https&quot;') or
                                              contains(., &quot;scheme='https'&quot;))]
                                  )]
                      ">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()" mode="insertConnector" />
    </xsl:copy>
  </xsl:template>

  <!-- Add the new connector after the last existing Connnector if there is one -->
  <xsl:template match="Connector[last()]" mode="insertConnector">
    <xsl:call-template name="Copy" />

    <xsl:call-template name="AddConnector" />
  </xsl:template>

  <!-- ... or before the first Engine if there is no existing Connector -->
  <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                mode="insertConnector">
    <xsl:call-template name="AddConnector" />

    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template name="AddConnector">
    <!-- Add new line -->
    <xsl:text>&#xa;</xsl:text>
    <!-- This is the new connector -->
    <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
               maxThreads="150" scheme="https" secure="true" 
               keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
               clientAuth="false" sslProtocol="TLS" />
  </xsl:template>
  
</xsl:stylesheet>
```

#### <a name="finalize-configuration"></a>Finalizar a configuração

Por fim, coloque os JARs do condutor na classe Tomcat e reinicie o seu Serviço de Aplicações.

1. Certifique-se de que os ficheiros do condutor JDBC estão disponíveis para o classloader Tomcat colocando-os no diretório */casa/tomcat/lib.* (Crie este diretório se já não existir.) Para fazer o upload destes ficheiros para a sua instância do Serviço de Aplicações, execute os seguintes passos:

    1. Na [Cloud Shell,](https://shell.azure.com)instale a extensão webapp:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Executar o seguinte comando CLI para criar um túnel SSH do seu sistema local para o Serviço de Aplicações:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Ligue-se à porta de túneis local com o seu cliente SFTP e faça o upload dos ficheiros para a pasta */home/tomcat/lib.*

    Em alternativa, pode utilizar um cliente FTP para carregar o condutor da JDBC. Siga estas [instruções para obter as suas credenciais FTP](../deploy-configure-credentials.md?toc=/azure/app-service/containers/toc.json).

2. Se criou uma fonte de dados ao nível do servidor, reinicie a aplicação Linux do Serviço de Aplicações. O Tomcat redefinirá `CATALINA_BASE` para `/home/tomcat` e utilizará a configuração atualizada.

### <a name="spring-boot"></a>Spring Boot

Para ligar a fontes de dados nas aplicações Spring Boot, sugerimos criar cordas de ligação e injetá-las no ficheiro *aplicação.properties.*

1. Na secção "Configuração" da página do Serviço de Aplicações, delineie um nome para a cadeia, colhe a sua cadeia de ligação JDBC no campo de valor e delineie o tipo para "Custom". Pode configurar opcionalmente esta cadeia de ligação como definição de ranhura.

    Esta cadeia de ligação é acessível à nossa aplicação como uma variável ambiental chamada `CUSTOMCONNSTR_<your-string-name>`. Por exemplo, a cadeia de ligação que criamos acima será chamada `CUSTOMCONNSTR_exampledb`.

2. No ficheiro *aplicação.properties,* faça referência a esta cadeia de ligação com o nome variável ambiental. Para o nosso exemplo, usaríamos o seguinte.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Consulte a documentação da [Bota de primavera sobre o acesso](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) de dados e [configurações externas](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) para obter mais informações sobre este tema.

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>Use redis como cache de sessão com Tomcat

Pode configurar o Tomcat para utilizar uma loja de sessão externa, como [o Azure Cache for Redis](/azure/azure-cache-for-redis/). Isto permite-lhe preservar o estado da sessão do utilizador (como dados do carrinho de compras) quando um utilizador é transferido para outra instância da sua aplicação, por exemplo, quando ocorre uma autoescala, reinício ou falha.

Para utilizar o Tomcat com o Redis, tem de configurar a sua aplicação para utilizar uma implementação [do PersistentManager.](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) Os seguintes passos explicam este processo utilizando o [Pivotal Session Manager: redis-store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) como exemplo.

1. Abra um terminal Bash e use `<variable>=<value>` para definir cada uma das seguintes variáveis ambientais.

    | Variável                 | Valor                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | O nome do grupo de recursos que contém a sua instância de Serviço de Aplicações.       |
    | WEBAPP_NAME              | O nome da sua instância de Serviço de Aplicações.                                     |
    | WEBAPP_PLAN_NAME         | O nome do seu plano de Serviço de Aplicações.                                         |
    | REGIÃO                   | O nome da região onde a sua aplicação está hospedada.                           |
    | REDIS_CACHE_NAME         | O nome do seu Azure Cache para redis exemplo.                           |
    | REDIS_PORT               | A porta SSL que a sua cache Redis ouve.                             |
    | REDIS_PASSWORD           | A chave de acesso principal para o seu caso.                                  |
    | REDIS_SESSION_KEY_PREFIX | Um valor que especifica para identificar as chaves da sessão que vêm da sua aplicação. |

    ```bash
    RESOURCEGROUP_NAME=<resource group>
    WEBAPP_NAME=<web app>
    WEBAPP_PLAN_NAME=<App Service plan>
    REGION=<region>
    REDIS_CACHE_NAME=<cache>
    REDIS_PORT=<port>
    REDIS_PASSWORD=<access key>
    REDIS_SESSION_KEY_PREFIX=<prefix>
    ```

    Pode encontrar o nome, porta e informações chave de acesso no portal Azure, procurando nas secções de **Propriedades** ou Chaves de **Acesso** da sua instância de serviço.

2. Crie ou atualize o ficheiro *src/main/webapp da sua aplicação/META-INF/context.xml* com o seguinte conteúdo:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <Context path="">
        <!-- Specify Redis Store -->
        <Valve className="com.gopivotal.manager.SessionFlushValve" />
        <Manager className="org.apache.catalina.session.PersistentManager">
            <Store className="com.gopivotal.manager.redis.RedisStore"
                   connectionPoolSize="20"
                   host="${REDIS_CACHE_NAME}.redis.cache.windows.net"
                   port="${REDIS_PORT}"
                   password="${REDIS_PASSWORD}"
                   sessionKeyPrefix="${REDIS_SESSION_KEY_PREFIX}"
                   timeout="2000"
            />
        </Manager>
    </Context>
    ```

    Este ficheiro especifica e configura a implementação do gestor de sessão para a sua aplicação. Utiliza as variáveis ambientais que definiu no passo anterior para manter a informação da sua conta fora dos seus ficheiros de origem.

3. Utilize o FTP para fazer o upload do ficheiro JAR do gestor da sessão para a sua instância de Serviço de Aplicações, colocando-o no diretório */home/tomcat/lib.* Para mais informações, consulte [A implementação da sua aplicação para o Serviço de Aplicações Azure utilizando FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

4. Desative o cookie de [afinidade](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) da sessão para a sua instância de Serviço de Aplicações. Pode fazê-lo a partir do portal Azure navegando até à sua app e, em seguida, definindo **Configuração > Configurações gerais > afinidade ARR** para **Off**. Alternadamente, pode utilizar o seguinte comando:

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    Por padrão, o Serviço de Aplicações utilizará cookies de afinidade de sessão para garantir que os pedidos dos clientes com sessões existentes são encaminhados para a mesma instância da sua aplicação. Este comportamento predefinido não requer configuração, mas não pode preservar o estado da sessão do utilizador quando a instância da sua aplicação é reiniciada ou quando o tráfego é reencaminhado para outra instância. Quando [desativa risa a](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) configuração de Afinidade DE CASO SEm ACORDO existente para desativar o encaminhamento baseado em cookies da sessão, permite que a loja de sessão configurada funcione sem interferências.

5. Navegue para a secção **Propriedades** da sua instância de Serviço de Aplicações e encontre **endereços IP adicionais de saída**. Estes representam todos os possíveis endereços IP de saída para a sua aplicação. Copie-os para utilização no próximo passo.

6. Para cada endereço IP, crie uma regra de firewall no seu Azure Cache para o caso Redis. Pode fazê-lo no portal Azure a partir da secção **Firewall** da sua instância Redis. Forneça um nome único para cada regra e coloque os valores de **endereço IP iniciar** e endereço IP **final** no mesmo endereço IP.

7. Navegue para a secção de **definições avançadas** da sua instância Redis e ajuste **o acesso apenas através do SSL** para **O .** Isto permite que a sua instância de Serviço de Aplicações se comunique com a sua cache Redis através da infraestrutura Azure.

8. Atualize a configuração `azure-webapp-maven-plugin` no ficheiro *pom.xml* da sua aplicação para consultar as informações da sua conta Redis. Este ficheiro utiliza as variáveis ambientais que definiu anteriormente para manter a informação da sua conta fora dos seus ficheiros de origem.

    Se for necessário, mude `1.7.0` para a versão atual do [Maven Plugin for Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
        <configuration>

            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}-${REGION}</appServicePlanName>
            <appName>${WEBAPP_NAME}-${REGION}</appName>
            <region>${REGION}</region>
            <linuxRuntime>tomcat 9.0-jre8</linuxRuntime>

            <appSettings>
                <property>
                    <name>REDIS_CACHE_NAME</name>
                    <value>${REDIS_CACHE_NAME}</value>
                </property>
                <property>
                    <name>REDIS_PORT</name>
                    <value>${REDIS_PORT}</value>
                </property>
                <property>
                    <name>REDIS_PASSWORD</name>
                    <value>${REDIS_PASSWORD}</value>
                </property>
                <property>
                    <name>REDIS_SESSION_KEY_PREFIX</name>
                    <value>${REDIS_SESSION_KEY_PREFIX}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Xms2048m -Xmx2048m -DREDIS_CACHE_NAME=${REDIS_CACHE_NAME} -DREDIS_PORT=${REDIS_PORT} -DREDIS_PASSWORD=${REDIS_PASSWORD} IS_SESSION_KEY_PREFIX=${REDIS_SESSION_KEY_PREFIX}</value>
                </property>

            </appSettings>

        </configuration>
    </plugin>
    ```

9. Reconstruir e recolocar a sua aplicação.

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

A sua aplicação irá agora utilizar o seu cache Redis para gestão de sessões.

Para obter uma amostra que possa utilizar para testar estas instruções, consulte o repo de [escala-stateful-java-web-web-on-azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) no GitHub.

## <a name="docker-containers"></a>Contentores do Docker

Para utilizar o Zulu JDK suportado pelo Azure nos seus recipientes, certifique-se de puxar e utilizar as imagens pré-construídas como documentado [saqueado](https://www.azul.com/downloads/azure-only/zulu/) a partir da página de descarregamento do Azure suportada ou utilize os `Dockerfile` exemplos do repo Do [Microsoft Java GitHub.](https://github.com/Microsoft/java/tree/master/docker)

## <a name="statement-of-support"></a>Declaração de apoio

### <a name="runtime-availability"></a>Disponibilidade de tempo de execução

O Serviço de Aplicações para Linux suporta dois prazos de execução para hospedagem gerida de aplicações web Java:

- O [recipiente de servlet Tomcat](https://tomcat.apache.org/) para executar aplicações embalados como ficheiros de arquivo web (WAR). As versões suportadas são 8.5 e 9.0.
- Ambiente de tempo de funcionamento da Java SE para aplicações em funcionamento embalados como ficheiros Java archive (JAR). As versões suportadas são Java 8 e 11.

### <a name="jdk-versions-and-maintenance"></a>Versões jdk e manutenção

As construções da Blue Zulu Enterprise da OpenJDK são uma distribuição sem custos, multiplataforma, pronta para a produção do OpenJDK para Azure e Azure Stack apoiado pela Microsoft e pela Azul Systems. Contêm todos os componentes para a construção e execução de aplicações Java SE. Pode instalar o JDK a partir da [instalação Java JDK](https://aka.ms/azure-jdks).

Os JDKs suportados são automaticamente corrigidos trimestralmente em janeiro, abril, julho e outubro de cada ano.

### <a name="security-updates"></a>Atualizações de segurança

Patches e correções para grandes vulnerabilidades de segurança serão lançados assim que ficarem disponíveis na Azul Systems. Uma vulnerabilidade "importante" é definida por uma pontuação base de 9.0 ou superior no Sistema de Pontuação de [Vulnerabilidade Comum NIST, versão 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Depreciação e reforma

Se um tempo de execução apoiado em Java for retirado, os desenvolvedores da Azure que utilizem o tempo de execução afetado receberão um aviso de depreciação pelo menos seis meses antes do tempo de execução ser retirado.

## <a name="next-steps"></a>Passos seguintes

Visite o [Centro Azure para Java Developers](/java/azure/) para encontrar quickstarts, tutoriais e documentação de referência java.

Perguntas gerais sobre a utilização do Serviço de Aplicações para o Linux que não são específicas para o desenvolvimento de Java são respondidas no [App Service Linux FAQ](app-service-linux-faq.md).
