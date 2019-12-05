---
title: Configurar aplicativos Java do Linux
description: Saiba como configurar um contêiner Java predefinido para seu aplicativo. Este artigo mostra as tarefas de configuração mais comuns.
keywords: serviço de aplicativo do Azure, aplicativo Web, Linux, OSS, Java, Java EE, JEE, Java
author: bmitchell287
manager: barbkess
ms.devlang: java
ms.topic: article
ms.date: 11/22/2019
ms.author: brendm
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: 571d4cd395cd0cec0982fedf267a88143fd73872
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805744"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Configurar um aplicativo Java do Linux para o serviço Azure App

Azure App serviço no Linux permite aos desenvolvedores de Java compilar, implantar e dimensionar rapidamente seus aplicativos Web empacotados Tomcat, WildFly ou Java Standard Edition (SE) em um serviço totalmente gerenciado baseado em Linux. Implante aplicativos com plug-ins do Maven a partir da linha de comando ou em editores como IntelliJ, Eclipse ou Visual Studio Code.

Este guia fornece os principais conceitos e instruções para desenvolvedores de Java que usam um contêiner do Linux interno no serviço de aplicativo. Se você nunca usou Azure App serviço, siga o tutorial [início rápido do Java](quickstart-java.md) e [Java com PostgreSQL](tutorial-java-enterprise-postgresql-app.md) primeiro.

## <a name="deploying-your-app"></a>Implantando seu aplicativo

Você pode usar o [plug-in do Maven para Azure app serviço](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) para implantar arquivos. jar e. War. A implantação com IDEs populares também tem suporte com [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) ou [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

Caso contrário, o método de implantação dependerá do tipo de arquivo morto:

- Para implantar arquivos. War no Tomcat, use o ponto de extremidade `/api/wardeploy/` para postar o arquivo morto. Para obter mais informações sobre essa API, consulte [esta documentação](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Para implantar arquivos. jar nas imagens Java SE, use o ponto de extremidade `/api/zipdeploy/` do site kudu. Para obter mais informações sobre essa API, consulte [esta documentação](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Não implante seu. War ou. jar usando FTP. A ferramenta de FTP foi projetada para carregar scripts de inicialização, dependências ou outros arquivos de tempo de execução. Não é a opção ideal para a implantação de aplicativos Web.

## <a name="logging-and-debugging-apps"></a>Log e depuração de aplicativos

Relatórios de desempenho, visualizações de tráfego e verificação de integridade estão disponíveis para cada aplicativo por meio do portal do Azure. Para obter mais informações, consulte [visão geral do diagnóstico de serviço Azure app](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>Acesso ao console SSH

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Transmitir registos de diagnóstico em fluxo

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Para obter mais informações, consulte [Stream logs in Cloud Shell](../troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Log de aplicativo

Habilite o [log do aplicativo](../troubleshoot-diagnostic-logs.md?toc=/azure/app-service/containers/toc.json#enable-application-logging-windows) por meio do portal do Azure ou [CLI do Azure](/cli/azure/webapp/log#az-webapp-log-config) para configurar o serviço de aplicativo para gravar a saída do console padrão do aplicativo e os fluxos de erro do console padrão no sistema de arquivos local ou no armazenamento de BLOBs do Azure. O registro em log na instância do sistema de arquivos do serviço de aplicativo local é desabilitado 12 horas após sua configuração. Se você precisar de mais retenção, configure o aplicativo para gravar a saída em um contêiner de armazenamento de BLOBs. Seus logs de aplicativo Java e Tomcat podem ser encontrados no diretório */Home/LogFiles/Application/*

Se seu aplicativo usar [Logback](https://logback.qos.ch/) ou [Log4J](https://logging.apache.org/log4j) para rastreamento, você poderá encaminhar esses rastreamentos para análise em aplicativo Azure insights usando as instruções de configuração da estrutura de registro em [explorar logs de rastreamento do Java no Application insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Ferramentas de resolução de problemas

As imagens Java internas são baseadas no sistema operacional [alpineum Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) . Use o Gerenciador de pacotes `apk` para instalar qualquer ferramenta ou comando de solução de problemas.

### <a name="flight-recorder"></a>Gravador de voo

Todas as imagens Java do Linux no serviço de aplicativo têm o Zulu Flight Recorder instalado para que você possa se conectar facilmente à JVM e iniciar uma gravação do criador de perfil ou gerar um despejo de heap.

#### <a name="timed-recording"></a>Gravação cronometrada

Para começar, use o SSH em seu serviço de aplicativo e execute o comando `jcmd` para ver uma lista de todos os processos Java em execução. Além do próprio jcmd, você deve ver seu aplicativo Java em execução com um PID (número de ID de processo).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Execute o comando a seguir para iniciar uma gravação de 30 segundos da JVM. Isso fará o perfil da JVM e criará um arquivo JFR chamado *jfr_example. JFR* no diretório base. (Substitua 116 pelo PID do seu aplicativo Java.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Durante o intervalo de 30 segundos, você pode validar se a gravação está ocorrendo executando `jcmd 116 JFR.check`. Isso mostrará todas as gravações para o processo Java fornecido.

#### <a name="continuous-recording"></a>Gravação contínua

Você pode usar o gravador de vôo Zulu para criar o perfil de seu aplicativo Java com impacto mínimo sobre o desempenho do tempo de execução ([origem](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)). Para fazer isso, execute o seguinte comando CLI do Azure para criar uma configuração de aplicativo chamada JAVA_OPTS com a configuração necessária. O conteúdo da configuração JAVA_OPTS aplicativo é passado para o comando `java` quando seu aplicativo é iniciado.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Depois que a gravação for iniciada, você poderá despejar os dados de gravação atuais a qualquer momento usando o comando `JFR.dump`.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

Para obter mais informações, consulte a [referência do comando Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Analisando gravações

Use [FTPS](../deploy-ftp.md) para baixar o arquivo JFR para o computador local. Para analisar o arquivo JFR, baixe e instale o [controle de missão Zulu](https://www.azul.com/products/zulu-mission-control/). Para obter instruções sobre o controle de missão Zulu, consulte a [documentação azul](https://docs.azul.com/zmc/) e as [instruções de instalação](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

## <a name="customization-and-tuning"></a>Personalização e ajuste

O serviço de Azure App para Linux dá suporte ao ajuste e à personalização prontos para uso por meio do portal do Azure e da CLI. Examine os seguintes artigos para configuração de aplicativo Web não específica para Java:

- [Definir configurações de aplicativo](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings)
- [Configurar um domínio personalizado](../app-service-web-tutorial-custom-domain.md?toc=/azure/app-service/containers/toc.json)
- [Configurar associações SSL](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json)
- [Adicionar uma CDN](../../cdn/cdn-add-to-web-app.md?toc=/azure/app-service/containers/toc.json)
- [Configurar o site kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Definir opções de tempo de execução Java

Para definir a memória alocada ou outras opções de tempo de execução da JVM nos ambientes Tomcat e Java SE, crie uma [configuração de aplicativo](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings) chamada `JAVA_OPTS` com as opções. O serviço de aplicativo Linux passa essa configuração como uma variável de ambiente para o tempo de execução Java quando ele é iniciado.

Na portal do Azure, em **configurações do aplicativo** para o aplicativo Web, crie uma nova configuração de aplicativo chamada `JAVA_OPTS` que inclui as configurações adicionais, como `-Xms512m -Xmx1204m`.

Para definir a configuração do aplicativo do plug-in do Maven, adicione marcas de configuração/valor na seção plug-in do Azure. O exemplo a seguir define um tamanho de heap de Java mínimo e máximo específico:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Os desenvolvedores que executam um único aplicativo com um slot de implantação em seu plano do serviço de aplicativo podem usar as seguintes opções:

- Instâncias B1 e S1: `-Xms1024m -Xmx1024m`
- Instâncias B2 e S2: `-Xms3072m -Xmx3072m`
- Instâncias B3 e S3: `-Xms6144m -Xmx6144m`

Ao ajustar as configurações de heap de aplicativo, examine os detalhes do plano do serviço de aplicativo e leve em conta vários aplicativos e o slot de implantação precisa encontrar a alocação ideal de memória.

Se você estiver implantando um aplicativo JAR, ele deverá ser nomeado *app. jar* para que a imagem interna possa identificar corretamente seu aplicativo. (O plug-in do Maven faz isso renomeando automaticamente.) Se você não quiser renomear o JAR para *app. jar*, poderá carregar um script de shell com o comando para executar o Jar. Em seguida, Cole o caminho completo para esse script na caixa de texto [arquivo de inicialização](app-service-linux-faq.md#built-in-images) na seção configuração do Portal. O script de inicialização não é executado a partir do diretório no qual ele é colocado. Portanto, sempre use caminhos absolutos para fazer referência a arquivos em seu script de inicialização (por exemplo: `java -jar /home/myapp/myapp.jar`).

### <a name="turn-on-web-sockets"></a>Ativar o Web Sockets

Ative o suporte para Web Sockets na portal do Azure nas **configurações do aplicativo** para o aplicativo. Você precisará reiniciar o aplicativo para que a configuração entre em vigor.

Ative o suporte de soquete da Web usando o CLI do Azure com o seguinte comando:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Em seguida, reinicie o aplicativo:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Definir codificação de caractere padrão

Na portal do Azure, em **configurações do aplicativo** para o aplicativo Web, crie uma nova configuração de aplicativo chamada `JAVA_OPTS` com o valor `-Dfile.encoding=UTF-8`.

Como alternativa, você pode definir a configuração do aplicativo usando o plug-in Maven do serviço de aplicativo. Adicione o nome da configuração e as marcas de valor na configuração do plug-in:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="adjust-startup-timeout"></a>Ajustar tempo limite de inicialização

Se seu aplicativo Java for particularmente grande, você deverá aumentar o limite de tempo de inicialização. Para fazer isso, crie uma configuração de aplicativo, `WEBSITES_CONTAINER_START_TIME_LIMIT` e defina-a como o número de segundos que o serviço de aplicativo deve aguardar antes de atingir o tempo limite. O valor máximo é de `1800` segundos.

### <a name="pre-compile-jsp-files"></a>Pré-compilar arquivos JSP

Para melhorar o desempenho de aplicativos Tomcat, você pode compilar seus arquivos JSP antes de implantar no serviço de aplicativo. Você pode usar o [plug-in do Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) fornecido pelo Apache sling ou usando esse [arquivo de compilação Ant](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Proteger aplicativos

Os aplicativos Java em execução no serviço de aplicativo para Linux têm o mesmo conjunto de [práticas recomendadas de segurança](/azure/security/security-paas-applications-using-app-services) que outros aplicativos.

### <a name="authenticate-users-easy-auth"></a>Autenticar usuários (fácil autenticação)

Configure a autenticação de aplicativo no portal do Azure com a opção **autenticação e autorização** . A partir daí, você pode habilitar a autenticação usando Azure Active Directory ou logons sociais, como Facebook, Google ou GitHub. Portal do Azure configuração só funciona ao configurar um único provedor de autenticação. Para obter mais informações, consulte [configurar seu aplicativo do serviço de aplicativo para usar Azure Active Directory logon](../configure-authentication-provider-aad.md?toc=/azure/app-service/containers/toc.json) e os artigos relacionados para outros provedores de identidade. Se você precisar habilitar vários provedores de entrada, siga as instruções no artigo personalizar a [autenticação do serviço de aplicativo](../app-service-authentication-how-to.md?toc=/azure/app-service/containers/toc.json) .

#### <a name="tomcat-and-wildfly"></a>Tomcat e WildFly

Seu aplicativo Tomcat ou WildFly pode acessar as declarações do usuário diretamente do servlet, convertendo o objeto principal em um objeto Map. O objeto MAP mapeará cada tipo de declaração para uma coleção de declarações para esse tipo. No código abaixo, `request` é uma instância de `HttpServletRequest`.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Agora você pode inspecionar o objeto `Map` para qualquer declaração específica. Por exemplo, o trecho de código a seguir itera em todos os tipos de declaração e imprime o conteúdo de cada coleção.

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

Para desconectar os usuários, use o caminho `/.auth/ext/logout`. Para executar outras ações, consulte a documentação sobre o [uso de autenticação e autorização do serviço de aplicativo](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to). Também há documentação oficial na [interface HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) do Tomcat e seus métodos. Os seguintes métodos servlets também são alimentados com base na configuração do serviço de aplicativo:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Para desabilitar esse recurso, crie uma configuração de aplicativo chamada `WEBSITE_AUTH_SKIP_PRINCIPAL` com um valor de `1`. Para desabilitar todos os filtros de servlet adicionados pelo serviço de aplicativo, crie uma configuração chamada `WEBSITE_SKIP_FILTERS` com um valor de `1`.

#### <a name="spring-boot"></a>Spring Boot

Os desenvolvedores do Spring boot podem usar o [iniciador do Spring boot Azure Active Directory](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) para proteger aplicativos usando anotações e APIs familiares de segurança do Spring. Certifique-se de aumentar o tamanho máximo do cabeçalho em seu arquivo *Application. Properties* . Sugerimos um valor de `16384`.

### <a name="configure-tlsssl"></a>Configurar TLS/SSL

Siga as instruções em [proteger um nome DNS personalizado com uma associação SSL no serviço Azure app](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json) para carregar um certificado SSL existente e associá-lo ao nome de domínio do seu aplicativo. Por padrão, seu aplicativo ainda permitirá conexões HTTP – siga as etapas específicas no tutorial para impor SSL e TLS.

### <a name="use-keyvault-references"></a>Usar referências do keyvault

O [Azure keyvault](../../key-vault/key-vault-overview.md) fornece gerenciamento de segredos centralizado com políticas de acesso e histórico de auditoria. Você pode armazenar segredos (como senhas ou cadeias de conexão) no keyvault e acessar esses segredos em seu aplicativo por meio de variáveis de ambiente.

Primeiro, siga as instruções para [conceder acesso ao aplicativo para Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) e [fazer uma referência de keyvault para seu segredo em uma configuração de aplicativo](../app-service-key-vault-references.md#reference-syntax). Você pode validar que a referência seja resolvida para o segredo imprimindo a variável de ambiente ao acessar remotamente o terminal do serviço de aplicativo.

Para injetar esses segredos em seu arquivo de configuração Spring ou Tomcat, use a sintaxe de injeção de variável de ambiente (`${MY_ENV_VAR}`). Para arquivos de configuração do Spring, consulte esta documentação sobre [configurações externas](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

### <a name="using-the-java-key-store"></a>Usando o repositório de chaves Java

Por padrão, todos os certificados públicos ou privados [carregados no serviço de aplicativo Linux](../configure-ssl-certificate.md) serão carregados no repositório de chaves Java à medida que o contêiner for iniciado. Isso significa que os certificados carregados estarão disponíveis no contexto de conexão ao fazer conexões TLS de saída. Depois de carregar seu certificado, você precisará reiniciar o serviço de aplicativo para que ele seja carregado no repositório de chaves Java.

Você pode interagir ou depurar a ferramenta de chave do Java [abrindo uma conexão SSH](app-service-linux-ssh-support.md) para o serviço de aplicativo e executando o comando `keytool`. Consulte a [documentação da ferramenta de chave](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) para obter uma lista de comandos. Os certificados são armazenados no local do arquivo de keystore padrão do Java, `$JAVA_HOME/jre/lib/security/cacerts`.

A configuração adicional pode ser necessária para criptografar sua conexão JDBC. Consulte a documentação para o driver JDBC escolhido.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/developer/java-driver/4.3/)


#### <a name="manually-initialize-and-load-the-key-store"></a>Inicializar e carregar manualmente o repositório de chaves

Você pode inicializar o repositório de chaves e adicionar certificados manualmente. Crie uma configuração de aplicativo, `SKIP_JAVA_KEYSTORE_LOAD`, com um valor de `1` para desabilitar o serviço de aplicativo de carregar os certificados no repositório de chaves automaticamente. Todos os certificados públicos carregados no serviço de aplicativo por meio do portal do Azure são armazenados em `/var/ssl/certs/`. Os certificados privados são armazenados em `/var/ssl/private/`.

Para obter mais informações sobre a API do keystore, consulte [a documentação oficial](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

## <a name="configure-apm-platforms"></a>Configurar plataformas APM

Esta seção mostra como conectar aplicativos Java implantados no serviço de Azure App no Linux com as plataformas de monitoramento de desempenho de aplicativos NewRelic e AppDynamics (APM).

### <a name="configure-new-relic"></a>Configurar novo Relic

1. Criar uma conta do NewRelic em [NewRelic.com](https://newrelic.com/signup)
2. Baixe o agente Java de NewRelic, ele terá um nome de arquivo semelhante a *newrelic-Java-x. x. x. zip*.
3. Copie sua chave de licença, você precisará dela para configurar o agente mais tarde.
4. Use [SSH em sua instância do serviço de aplicativo](app-service-linux-ssh-support.md) e crie um novo diretório */Home/site/wwwroot/APM*.
5. Carregue os arquivos do agente Java NewRelic desempacotados em um diretório em */Home/site/wwwroot/APM*. Os arquivos do seu agente devem estar no */Home/site/wwwroot/APM/newrelic*.
6. Modifique o arquivo YAML em */Home/site/wwwroot/APM/newrelic/newrelic.yml* e substitua o valor da licença de espaço reservado por sua própria chave de licença.
7. Na portal do Azure, navegue até seu aplicativo no serviço de aplicativo e crie uma nova configuração de aplicativo.
    - Se seu aplicativo estiver usando **Java se**, crie uma variável de ambiente chamada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se você estiver usando o **tomcat**, crie uma variável de ambiente chamada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se você estiver usando o **WildFly**, consulte a nova documentação do Relic [aqui](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) para obter orientação sobre como instalar o agente Java e a configuração do JBoss.

### <a name="configure-appdynamics"></a>Configurar o AppDynamics

1. Criar uma conta do AppDynamics em [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Baixe o agente Java do site AppDynamics, o nome do arquivo será semelhante a *AppServerAgent-x. x. x. xxxxx. zip*
3. Use [SSH em sua instância do serviço de aplicativo](app-service-linux-ssh-support.md) e crie um novo diretório */Home/site/wwwroot/APM*.
4. Carregue os arquivos do agente Java em um diretório em */Home/site/wwwroot/APM*. Os arquivos do seu agente devem estar no */Home/site/wwwroot/APM/AppDynamics*.
5. Na portal do Azure, navegue até seu aplicativo no serviço de aplicativo e crie uma nova configuração de aplicativo.
    - Se você estiver usando **Java se**, crie uma variável de ambiente chamada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` em que `<app-name>` é o nome do serviço de aplicativo.
    - Se você estiver usando o **tomcat**, crie uma variável de ambiente chamada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` em que `<app-name>` é o nome do serviço de aplicativo.
    - Se você estiver usando o **WildFly**, consulte a documentação do AppDynamics [aqui](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) para obter orientação sobre como instalar o agente Java e a configuração do JBoss.

> [!NOTE]
> Se você já tiver uma variável de ambiente para `JAVA_OPTS` ou `CATALINA_OPTS`, acrescente a opção `-javaagent:/...` ao final do valor atual.

## <a name="configure-jar-applications"></a>Configurar aplicativos JAR

### <a name="starting-jar-apps"></a>Iniciando aplicativos JAR

Por padrão, o serviço de aplicativo espera que seu aplicativo JAR seja nomeado *app. jar*. Se ele tiver esse nome, ele será executado automaticamente. Para usuários do Maven, você pode definir o nome do JAR incluindo `<finalName>app</finalName>` na seção `<build>` do seu *pom. xml*. [Você pode fazer o mesmo no gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) definindo a propriedade `archiveFileName`.

Se você quiser usar um nome diferente para o JAR, também deverá fornecer o comando de [inicialização](app-service-linux-faq.md#built-in-images) que executa o arquivo jar. Por exemplo, `java -jar my-jar-app.jar`. Você pode definir o valor para o comando de inicialização no portal, em Configuração > configurações gerais ou com uma configuração de aplicativo chamada `STARTUP_COMMAND`.

### <a name="server-port"></a>Porta do Servidor

O serviço de aplicativo Linux roteia solicitações de entrada para a porta 80, de modo que seu aplicativo também deve escutar na porta 80. Você pode fazer isso na configuração do seu aplicativo (como o arquivo *Application. Properties* da Spring) ou no comando Startup (por exemplo, `java -jar spring-app.jar --server.port=80`). Consulte a seguinte documentação para estruturas Java comuns:

- [Spring boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Play Framework](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>Origens de dados

### <a name="tomcat"></a>Tomcat

Essas instruções se aplicam a todas as conexões de banco de dados. Você precisará preencher espaços reservados com o nome da classe de driver e o arquivo JAR do banco de dados escolhido. Fornecido é uma tabela com nomes de classe e downloads de driver para bancos de dados comuns.

| Base de Dados   | Nome da classe do driver                             | JDBC Driver                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Transferência](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Baixar](https://dev.mysql.com/downloads/connector/j/) (selecione "plataforma independente") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Transferência](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Para configurar o Tomcat para usar Java Database Connectivity (JDBC) ou a API de persistência Java (JPA), primeiro Personalize a variável de ambiente `CATALINA_OPTS` que é lida no pelo Tomcat na inicialização. Defina esses valores por meio de uma configuração de aplicativo no [plug-in Maven do serviço de aplicativo](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Ou defina as variáveis de ambiente na página **configurações do aplicativo** > de **configuração** no portal do Azure.

Em seguida, determine se a fonte de dados deve estar disponível para um aplicativo ou para todos os aplicativos em execução no servlet Tomcat.

#### <a name="application-level-data-sources"></a>Fontes de dados em nível de aplicativo

1. Crie um arquivo *Context. xml* no diretório *meta-inf/* do seu projeto. Crie o diretório *meta-inf/* se ele não existir.

2. Em *Context. xml*, adicione um elemento `Context` para vincular a fonte de dados a um endereço JNDI. Substitua o espaço reservado `driverClassName` pelo nome da classe do driver da tabela acima.

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

3. Atualize o *Web. xml* do seu aplicativo para usar a fonte de dados em seu aplicativo.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Recursos de nível de servidor compartilhados

A adição de uma fonte de dados compartilhada no nível do servidor exigirá que você edite o Server. XML do Tomcat. Primeiro, carregue um [script de inicialização](app-service-linux-faq.md#built-in-images) e defina o caminho para o script na **configuração** > **comando de inicialização**. Você pode carregar o script de inicialização usando [FTP](../deploy-ftp.md).

O script de inicialização fará uma [transformação XSL](https://www.w3schools.com/xml/xsl_intro.asp) para o arquivo Server. xml e produzirá o arquivo XML resultante para `/usr/local/tomcat/conf/server.xml`. O script de inicialização deve instalar o libxslt via apk. O arquivo XSL e o script de inicialização podem ser carregados via FTP. Veja abaixo um exemplo de script de inicialização.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /usr/local/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /home/tomcat/conf/server.xml
```

Um exemplo de arquivo XSL é fornecido abaixo. O arquivo XSL de exemplo adiciona um novo nó de conector ao servidor Tomcat. xml.

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

#### <a name="finalize-configuration"></a>Finalizar configuração

Por fim, coloque os JARs do driver no classpath do Tomcat e reinicie o serviço de aplicativo.

1. Certifique-se de que os arquivos do driver JDBC estejam disponíveis para o carregador de diretório do Tomcat colocando-os no */Home/Tomcat/lib* Directory. (Crie esse diretório se ele ainda não existir.) Para carregar esses arquivos em sua instância do serviço de aplicativo, execute as seguintes etapas:

    1. No [Cloud Shell](https://shell.azure.com), instale a extensão webapp:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Execute o seguinte comando da CLI para criar um túnel SSH do seu sistema local para o serviço de aplicativo:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Conecte-se à porta de túnel local com seu cliente SFTP e carregue os arquivos na pasta */Home/Tomcat/lib*

    Como alternativa, você pode usar um cliente FTP para carregar o driver JDBC. Siga estas [instruções para obter suas credenciais de FTP](../deploy-configure-credentials.md?toc=/azure/app-service/containers/toc.json).

2. Se você criou uma fonte de dados no nível do servidor, reinicie o aplicativo Linux do serviço de aplicativo. O Tomcat irá redefinir `CATALINA_BASE` para `/home/tomcat` e usar a configuração atualizada.

### <a name="spring-boot"></a>Spring Boot

Para se conectar a fontes de dados em aplicativos Spring boot, sugerimos criar cadeias de conexão e injeta-las em seu arquivo *Application. Properties* .

1. Na seção "configuração" da página Serviço de aplicativo, defina um nome para a cadeia de caracteres, Cole sua cadeia de conexão JDBC no campo valor e defina o tipo como "personalizado". Opcionalmente, você pode definir essa cadeia de conexão como configuração de slot.

    Essa cadeia de conexão é acessível ao nosso aplicativo como uma variável de ambiente chamada `CUSTOMCONNSTR_<your-string-name>`. Por exemplo, a cadeia de conexão que criamos acima será nomeada `CUSTOMCONNSTR_exampledb`.

2. No arquivo *Application. Properties* , referencie essa cadeia de conexão com o nome da variável de ambiente. Para nosso exemplo, usaremos o seguinte.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Consulte a [documentação do Spring boot sobre acesso a dados](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) e [configurações externas](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) para obter mais informações sobre este tópico.

## <a name="configure-java-ee-wildfly"></a>Configurar Java EE (WildFly)

> [!NOTE]
> O Java Enterprise Edition no serviço de aplicativo Linux está atualmente em versão prévia. Essa pilha **não** é recomendada para trabalho voltado para produção.

O serviço de Azure App no Linux permite que os desenvolvedores de Java compilem, implantem e dimensionem aplicativos Java Enterprise (Java EE) em um serviço totalmente gerenciado baseado em Linux.  O Java Enterprise Runtime Environment subjacente é o servidor de aplicativos [WildFly](https://wildfly.org/) de código aberto.

Esta seção contém as seguintes subseções:

- [Dimensionar com o serviço de aplicativo](#scale-with-app-service)
- [Personalizar a configuração do servidor de aplicativos](#customize-application-server-configuration)
- [Instalar módulos e dependências](#install-modules-and-dependencies)
- [Configurar fontes de dados](#configure-data-sources)
- [Usar o barramento de serviço como um agente de mensagem](#use-service-bus-as-a-message-broker)

### <a name="scale-with-app-service"></a>Dimensionar com o serviço de aplicativo

O servidor de aplicativos WildFly em execução no serviço de aplicativo no Linux é executado no modo autônomo, não em uma configuração de domínio. Quando você escala horizontalmente o plano do serviço de aplicativo, cada instância de WildFly é configurada como um servidor autônomo.

Dimensione seu aplicativo vertical ou horizontalmente com [as regras de escala](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md) e [aumentando sua contagem de instâncias](../manage-scale-up.md?toc=/azure/app-service/containers/toc.json).

### <a name="customize-application-server-configuration"></a>Personalizar a configuração do servidor de aplicativos

As instâncias do aplicativo Web são sem estado, portanto, cada nova instância iniciada deve ser configurada na inicialização para dar suporte à configuração WildFly necessária para o aplicativo.
Você pode escrever um script de bash de inicialização para chamar a CLI do WildFly para:

- Configurar fontes de dados
- Configurar provedores de mensagens
- Adicione outros módulos e dependências à configuração do servidor WildFly.

O script é executado quando o WildFly está em execução, mas antes de o aplicativo ser iniciado. O script deve usar a [CLI do JBoss](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) chamada de */opt/JBoss/wildfly/bin/JBoss-CLI.sh* para configurar o servidor de aplicativos com qualquer configuração ou alteração necessária depois que o servidor for iniciado.

Não use o modo interativo da CLI para configurar o WildFly. Em vez disso, você pode fornecer um script de comandos para a CLI do JBoss usando o comando `--file`, por exemplo:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Use o FTP para carregar o script de inicialização em um local em sua instância do serviço de aplicativo em seu diretório */Home* , como */Home/site/Deployments/Tools*. Para obter mais informações, consulte [implantar seu aplicativo no serviço de Azure App usando FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

Defina o campo **script de inicialização** no portal do Azure para o local do script do Shell de inicialização, por exemplo */Home/site/Deployments/Tools/Your-Startup-script.sh*.

Forneça [configurações](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings) de aplicativo na configuração do aplicativo para passar variáveis de ambiente para uso no script. As configurações do aplicativo mantêm cadeias de conexão e outros segredos necessários para configurar seu aplicativo fora do controle de versão.

### <a name="install-modules-and-dependencies"></a>Instalar módulos e dependências

Para instalar os módulos e suas dependências no classpath do WildFly por meio da CLI do JBoss, você precisará criar os seguintes arquivos em seu próprio diretório. Alguns módulos e dependências podem precisar de configuração adicional, como nomenclatura de JNDI ou outra configuração específica de API, para que essa lista seja um conjunto mínimo de do que você precisará para configurar uma dependência na maioria dos casos.

- Um [descritor de módulo XML](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). Esse arquivo XML define o nome, os atributos e as dependências do seu módulo. Este [arquivo module. XML de exemplo](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) define um módulo Postgres, sua dependência JDBC de arquivo JAR e outras dependências de módulo necessárias.
- Quaisquer dependências de arquivo JAR necessárias para seu módulo.
- Um script com seus comandos da CLI do JBoss para configurar o novo módulo. Esse arquivo conterá os comandos a serem executados pela CLI do JBoss para configurar o servidor para usar a dependência. Para obter a documentação sobre os comandos para adicionar módulos, fontes de documentos e provedores de mensagens, consulte [este documento](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- Um script de inicialização bash para chamar a CLI do JBoss e executar o script na etapa anterior. Esse arquivo será executado quando a instância do serviço de aplicativo for reiniciada ou quando novas instâncias forem provisionadas durante uma expansão. Esse script de inicialização é onde você pode executar qualquer outra configuração para seu aplicativo, pois os comandos do JBoss são passados para a CLI do JBoss. No mínimo, esse arquivo pode ser um único comando para passar o script de comando da CLI do JBoss para a CLI do JBoss:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Depois de ter os arquivos e o conteúdo do seu módulo, siga as etapas abaixo para adicionar o módulo ao servidor de aplicativos WildFly.

1. Use o FTP para carregar seus arquivos em um local em sua instância do serviço de aplicativo em seu diretório */Home* , como */Home/site/Deployments/Tools*. Para obter mais informações, consulte [implantar seu aplicativo no serviço de Azure App usando FTP/S](../deploy-ftp.md).
2. Na página **configuração** > **configurações gerais** do portal do Azure, defina o campo **script de inicialização** como o local do script do Shell de inicialização, por exemplo, */Home/site/Deployments/Tools/Startup.sh*.
3. Reinicie a instância do serviço de aplicativo pressionando o botão **reiniciar** na seção **visão geral** do portal ou usando o CLI do Azure.

### <a name="configure-data-sources"></a>Configurar fontes de dados

Para configurar o WildFly/JBoss para acessar uma fonte de dados, use o processo geral descrito acima na seção "instalar módulos e dependências". A seção a seguir fornece detalhes específicos sobre esse processo para as fontes de dados PostgreSQL, MySQL e SQL Server.

Esta seção pressupõe que você já tem um aplicativo, uma instância do serviço de aplicativo e uma instância do serviço de banco de dados do Azure. As instruções a seguir referem-se ao nome do serviço de aplicativo, seu grupo de recursos e suas informações de conexão de banco de dados. Você pode encontrar essas informações no portal do Azure.

Se você preferir percorrer todo o processo desde o início usando um aplicativo de exemplo, consulte [tutorial: compilar um aplicativo Web Java EE e postgres no Azure](tutorial-java-enterprise-postgresql-app.md).

As etapas a seguir explicam os requisitos para conectar seu serviço de aplicativo e banco de dados existentes.

1. Baixe o driver JDBC para [PostgreSQL](https://jdbc.postgresql.org/download.html), [MySQL](https://dev.mysql.com/downloads/connector/j/)ou [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server). Descompacte o arquivo baixado para obter o arquivo. jar do driver.

2. Crie um arquivo com um nome como *Module. xml* e adicione a marcação a seguir. Substitua o espaço reservado `<module name>` (incluindo os colchetes angulares) por `org.postgres` para PostgreSQL, `com.mysql` para MySQL ou `com.microsoft` para SQL Server. Substitua `<JDBC .jar file path>` pelo nome do arquivo. jar da etapa anterior, incluindo o caminho completo para o local em que você colocará o arquivo em sua instância do serviço de aplicativo. Pode ser qualquer local no diretório */Home*

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="<module name>">
        <resources>
           <resource-root path="<JDBC .jar file path>" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

3. Crie um arquivo com um nome como *DataSource-Commands. CLI* e adicione o código a seguir. Substitua `<JDBC .jar file path>` pelo valor usado na etapa anterior. Substitua `<module file path>` pelo nome do arquivo e pelo caminho do serviço de aplicativo da etapa anterior, por exemplo, */Home/Module.xml*.

    **PostgreSQL**

    ```console
    module add --name=org.postgres --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name=postgres,driver-module-name=org.postgres,driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=$DATABASE_CONNECTION_URL --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker

    reload --use-current-server-config=true
    ```

    **MySQL**

    ```console
    module add --name=com.mysql --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=mysql:add(driver-name=mysql,driver-module-name=com.mysql,driver-class-name=com.mysql.cj.jdbc.Driver)

    data-source add --name=mysqlDS --jndi-name=java:jboss/datasources/mysqlDS --connection-url=$DATABASE_CONNECTION_URL --driver-name=mysql --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=com.mysql.cj.jdbc.Driver --jta=true --use-java-context=true --exception-sorter-class-name=com.mysql.cj.jdbc.integration.jboss.ExtendedMysqlExceptionSorter

    reload --use-current-server-config=true
    ```

    **SQL Server**

    ```console
    module add --name=com.microsoft --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=sqlserver:add(driver-name=sqlserver,driver-module-name=com.microsoft,driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver,driver-datasource-class-name=com.microsoft.sqlserver.jdbc.SQLServerDataSource)

    data-source add --name=sqlDS --jndi-name=java:jboss/datasources/sqlDS --driver-name=sqlserver --connection-url=$DATABASE_CONNECTION_URL --validate-on-match=true --background-validation=false --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLValidConnectionChecker --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLExceptionSorter

    reload --use-current-server-config=true
    ```

    Esse arquivo é executado pelo script de inicialização descrito na próxima etapa. Ele instala o driver JDBC como um módulo WildFly, cria a fonte de dados WildFly correspondente e recarrega o servidor para garantir que as alterações entrarão em vigor.

4. Crie um arquivo com um nome como *Startup.sh* e adicione o código a seguir. Substitua `<JBoss CLI script>` pelo nome do arquivo que você criou na etapa anterior. Certifique-se de incluir o caminho completo para o local em que você posicionará o arquivo em sua instância do serviço de aplicativo, por exemplo, */Home/DataSource-Commands.CLI*.

    ```bash
    #!/usr/bin/env bash
    /opt/jboss/wildfly/bin/jboss-cli.sh -c --file=<JBoss CLI script>
    ```

5. Use o FTP para carregar o arquivo JDBC. jar, o arquivo XML do módulo, o script da CLI do JBoss e o script de inicialização para sua instância do serviço de aplicativo. Coloque esses arquivos no local especificado nas etapas anteriores, como */Home*. Para obter mais informações sobre o FTP, consulte [implantar seu aplicativo no serviço de Azure App usando FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

6. Use o CLI do Azure para adicionar configurações ao serviço de aplicativo que contêm suas informações de conexão de banco de dados. Substitua `<resource group>` e `<webapp name>` pelos valores que seu serviço de aplicativo usa. Substitua `<database server name>`, `<database name>`, `<admin name>`e `<admin password>` pelas informações de conexão do banco de dados. Você pode obter as informações do serviço de aplicativo e do banco de dados do portal do Azure.

    **PostgreSQL:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:postgresql://<database server name>:5432/<database name>?ssl=true \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **MySQL:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **SQL Server:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
    ```

    Os valores de DATABASE_CONNECTION_URL são diferentes para cada servidor de banco de dados e diferentes dos valores na portal do Azure. Os formatos de URL mostrados aqui (e nos trechos de código acima) são necessários para o uso do WildFly:

    * **PostgreSQL:** `jdbc:postgresql://<database server name>:5432/<database name>?ssl=true`
    * **MySQL:** `jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT`
    * **SQL Server:** `jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;`

7. Na portal do Azure, navegue até o serviço de aplicativo e localize a página **configuração** > **configurações gerais** . Defina o campo **script de inicialização** como o nome e o local do script de inicialização, por exemplo, */Home/Startup.sh*.

Na próxima vez em que o serviço de aplicativo for reiniciado, ele executará o script de inicialização e executará as etapas de configuração necessárias. Para testar se essa configuração ocorre corretamente, você pode acessar o serviço de aplicativo usando o SSH e, em seguida, executar o script de inicialização por conta própria no prompt do bash. Você também pode examinar os logs do serviço de aplicativo. Para obter mais informações sobre essas opções, consulte [log and Debugging apps](#logging-and-debugging-apps).

Em seguida, você precisará atualizar a configuração do WildFly para seu aplicativo e reimplantá-lo. Utilize os passos seguintes:

1. Abra o arquivo *src/main/resources/META-INF/persistence. xml* para seu aplicativo e localize o elemento `<jta-data-source>`. Substitua seu conteúdo, como mostrado aqui:

    **PostgreSQL**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

    **MySQL**

    ```xml
    <jta-data-source>java:jboss/datasources/mysqlDS</jta-data-source>
    ```

    **SQL Server**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

2. Recompile e reimplante seu aplicativo usando o seguinte comando no prompt do bash:

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

3. Reinicie a instância do serviço de aplicativo pressionando o botão **reiniciar** na seção **visão geral** do portal do Azure ou usando o CLI do Azure.

Sua instância do serviço de aplicativo agora está configurada para acessar seu banco de dados.

Para obter mais informações sobre como configurar a conectividade de banco de dados com o WildFly, consulte [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7), [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource)ou [SQL Server](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898).

### <a name="use-service-bus-as-a-message-broker"></a>Usar o barramento de serviço como um agente de mensagem

Você pode configurar o WildFly e seus Beans orientados a mensagens para usar o [barramento de serviço do Azure](/azure/service-bus-messaging) como seu agente de mensagens. Após a configuração, você pode enviar e receber mensagens usando o [Apache QPID](https://qpid.apache.org) como seu cliente Java Message Service (JMS). Há algumas etapas para configurar um adaptador de recurso JMS (JMS RA) que habilitará EJBs (Enterprise Java Beans) para configurar uma fábrica e uma fila de conexão JMS remota. Esta configuração remota apontará para o barramento de serviço do Azure e usará o provedor Apache QPID JMS para o protocolo AMQP.

As etapas a seguir descrevem a configuração e o código necessários. Essas etapas pressupõem que você criou uma instância do serviço de aplicativo para hospedar seu Bean, um namespace do barramento de serviço, uma fila e um tópico com uma assinatura. Para obter informações sobre como criar esses recursos, consulte:

- [Início rápido: criar um aplicativo Java no serviço de Azure App no Linux](/azure/app-service/containers/quickstart-java)
- [Início rápido: usar o CLI do Azure para criar uma fila do barramento de serviço](/azure/service-bus-messaging/service-bus-quickstart-cli)
- [Início rápido: Use a portal do Azure para criar um tópico e assinaturas do barramento de serviço para o tópico](/azure/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal)

1. Abra um terminal Bash e use os comandos a seguir para salvar suas informações de recurso do Azure em variáveis de ambiente. Substitua os espaços reservados (incluindo os colchetes angulares) pelos valores indicados.

    | Variável            | Valor                                                                      |
    |---------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME  | O nome do grupo de recursos que contém a instância do serviço de aplicativo.       |
    | WEBAPP_NAME         | O nome da instância do serviço de aplicativo.                                     |
    | REGIÃO              | O nome da região em que seu aplicativo está hospedado.                           |
    | DEFAULT_SBNAMESPACE | O nome do seu namespace do barramento de serviço.                                    |
    | SB_SAS_POLICY       | O nome da política de SAS (assinatura de acesso compartilhado) para seu namespace.   |
    | SB_SAS_KEY          | A chave primária ou secundária para a política de SAS da fila.                  |
    | SB_QUEUE            | O nome da sua fila do barramento de serviço.                                        |
    | SB_TOPIC            | O nome do tópico do barramento de serviço.                                        |
    | SB_SUBSCRIPTION     | O nome da assinatura para seu tópico.                                |

    ```bash
    RESOURCEGROUP_NAME=<resource group>
    WEBAPP_NAME=<web app>
    WEBAPP_PLAN_NAME=${WEBAPP_NAME}-appservice-plan
    REGION=<region>
    DEFAULT_SBNAMESPACE=<namespace>
    SB_SAS_POLICY=<SAS policy>
    SB_SAS_KEY=<SAS key>
    SB_QUEUE=<queue>
    SB_TOPIC=<topic>
    SB_SUBSCRIPTION=<subscription>
    PROVIDER_URL=amqps://${DEFAULT_SBNAMESPACE}.servicebus.windows.net?amqp.idleTimeout=120000
    ```

    Você pode encontrar essas informações no portal do Azure. Para a política e a chave de SAS, certifique-se de usar os valores para o namespace para que seu aplicativo possa acessar a sua fila e sua assinatura de tópico. Para localizar esses valores no portal do Azure, navegue até o recurso de namespace, selecione **políticas de acesso compartilhado**e, em seguida, selecione a política **RootManageSharedAccessKey** .

2. Baixe o [provedor Apache QPID JMS](https://qpid.apache.org/components/jms/index.html). Localize os arquivos. jar nos diretórios *lib* e *lib/optional* .

3. Crie um arquivo chamado *Module. xml* e adicione a marcação a seguir. Substitua cada instância do espaço reservado `<version>` (incluindo os colchetes angulares) pela versão correta para cada arquivo. jar, de modo que os nomes dos arquivos correspondam aos que você extraiu na etapa 1.

    ```xml
    <module xmlns="urn:jboss:module:1.1" name="org.jboss.genericjms.provider">
        <resources>
            <resource-root path="proton-j-<version>.jar"/>
            <resource-root path="qpid-jms-client-<version>.jar"/>
            <resource-root path="slf4j-log4j12-<version>.jar"/>
            <resource-root path="slf4j-api-<version>.jar"/>
            <resource-root path="log4j-<version>.jar"/>
            <resource-root path="netty-buffer-<version>.jar" />
            <resource-root path="netty-codec-<version>.jar" />
            <resource-root path="netty-codec-http-<version>.jar" />
            <resource-root path="netty-common-<version>.jar" />
            <resource-root path="netty-handler-<version>.jar" />
            <resource-root path="netty-resolver-<version>.jar" />
            <resource-root path="netty-transport-<version>.jar" />
            <resource-root path="netty-transport-native-epoll-<version>-linux-x86_64.jar" />
            <resource-root path="netty-transport-native-kqueue-<version>-osx-x86_64.jar" />
            <resource-root path="netty-transport-native-unix-common-<version>.jar" />
            <resource-root path="qpid-jms-discovery-<version>jar" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.jms.api"/>
        </dependencies>
    </module>
    ```

4. Crie um arquivo chamado *Startup.sh* e adicione o código a seguir.

    ```bash
    echo "Generating jndi.properties file in /home/site/deployments/tools directory"
    echo "connectionfactory.mymdbconnection=amqps://${DEFAULT_SBNAMESPACE}.servicebus.windows.net?amqp.idleTimeout=120000&jms.username=${SB_SAS_POLICY}&jms.password=${SB_SAS_KEY}" > /home/site/deployments/tools/jndi.properties
    echo "queue.mymdbqueue=${SB_QUEUE}" >> /home/site/deployments/tools/jndi.properties
    echo "topic.mymdbtopic=${SB_TOPIC}" >> /home/site/deployments/tools/jndi.properties
    echo "queue.mymdbsubscription=${SB_TOPIC}/Subscriptions/${SB_SUBSCRIPTION}" >> /home/site/deployments/tools/jndi.properties
    echo "====== contents of /home/site/deployments/tools/jndi.properties ======"
    cat /home/site/deployments/tools/jndi.properties
    echo "====== EOF /home/site/deployments/tools/jndi.properties ======"
    echo "Generating commands.cli file for /home/site/deployments/tools directory"
    echo "# Start batching commands" > /home/site/deployments/tools/commands.cli
    echo "batch" >> /home/site/deployments/tools/commands.cli
    echo "# Configure the ee subsystem to enable MDB annotation property substitution" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=ee:write-attribute(name=annotation-property-replacement,value=true)" >> /home/site/deployments/tools/commands.cli
    echo "# Define system properties to be used in the substititution" >> /home/site/deployments/tools/commands.cli
    echo "/system-property=property.mymdb.queue:add(value=java:global/remoteJMS/mymdbqueue})" >> /home/site/deployments/tools/commands.cli
    echo "/system-property=property.mymdb.topic:add(value=java:global/remoteJMS/mymdbsubscription)" >> /home/site/deployments/tools/commands.cli
    echo "/system-property=property.connection.factory:add(value=java:global/remoteJMS/mymdbconnection)" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=ee:list-add(name=global-modules, value={\"name\" => \"org.jboss.genericjms.provider\", \"slot\" =>\"main\"}" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=naming/binding=\"java:global/remoteJMS\":add(binding-type=external-context,module=org.jboss.genericjms.provider,class=javax.naming.InitialContext,environment=[java.naming.factory.initial=org.apache.qpid.jms.jndi.JmsInitialContextFactory,org.jboss.as.naming.lookup.by.string=true,java.naming.provider.url=/home/site/deployments/tools/jndi.properties])" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=resource-adapters/resource-adapter=generic-ra:add(module=org.jboss.genericjms,transaction-support=XATransaction)" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd:add(class-name=org.jboss.resource.adapter.jms.JmsManagedConnectionFactory, jndi-name=java:/jms/mymdbconnection)" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd/config-properties=ConnectionFactory:add(value=mymdbconnection)" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd/config-properties=JndiParameters:add(value=\"java.naming.factory.initial=org.apache.qpid.jms.jndi.JmsInitialContextFactory;java.naming.provider.url=/home/site/deployments/tools/jndi.properties\")" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd:write-attribute(name=security-application,value=true)" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=ejb3:write-attribute(name=default-resource-adapter-name, value=generic-ra)" >> /home/site/deployments/tools/commands.cli
    echo "# Run the batch commands" >> /home/site/deployments/tools/commands.cli
    echo "run-batch" >> /home/site/deployments/tools/commands.cli
    echo "reload" >> /home/site/deployments/tools/commands.cli
    echo "====== contents of /home/site/deployments/tools/commands.cli ======"
    cat /home/site/deployments/tools/commands.cli
    echo "======= EOF /home/site/deployments/tools/commands.cli ========"
    mkdir /opt/jboss/wildfly/modules/system/layers/base/org/jboss/genericjms/provider
    mkdir /opt/jboss/wildfly/modules/system/layers/base/org/jboss/genericjms/provider/main
    cp  /home/site/deployments/tools/*.jar /opt/jboss/wildfly/modules/system/layers/base/org/jboss/genericjms/provider/main/
    cp /home/site/deployments/tools/module.xml /opt/jboss/wildfly/modules/system/layers/base/org/jboss/genericjms/provider/main/
    cp /home/site/deployments/tools/jndi.properties /opt/jboss/wildfly/standalone/configuration/
    /opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/home/site/deployments/tools/commands.cli
    echo "Startup Run done"
    ```

    Sua instância do serviço de aplicativo executará esse script sempre que ele for iniciado, fornecendo configuração adicional necessária pelo WildFly. Esse script copia suas dependências de aplicativo para os locais necessários. Ele também gera arquivos *JNDI. Properties* e *Commands. CLI* , que usam as variáveis de ambiente mostradas na etapa 1. Esses valores também são passados para sua instância do serviço de aplicativo em uma etapa posterior.

    O arquivo *Commands. CLI* é um script da [CLI do Wildfly](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) que é iniciado pelo script de inicialização. Os comandos neste arquivo configuram JMS e JNDI, fazendo uso do arquivo *JNDI. Properties* . Esses comandos criam uma conexão entre seu aplicativo e sua fila ou tópico do barramento de serviço.

5. Use o FTP para carregar os arquivos. jar, o arquivo *Module. xml* e o arquivo *Startup.sh* para a instância do serviço de aplicativo. Coloque *Startup.sh* no diretório */Home* e coloque os outros arquivos no diretório */Home/site/Deployments/Tools* . Certifique-se de carregar todos os arquivos. jar listados no arquivo *Module. xml* para obter um fechamento transitivo de dependências. Para obter mais informações sobre o FTP, consulte [implantar seu aplicativo no serviço de Azure App usando FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

6. Atualize sua implementação do MessageListener para adicionar as seguintes instruções de `import`:

    ```java
    import javax.ejb.ActivationConfigProperty;
    import javax.ejb.MessageDriven;
    import javax.ejb.TransactionAttribute;
    import javax.ejb.TransactionAttributeType;
    import javax.ejb.TransactionManagement;
    import javax.ejb.TransactionManagementType;
    import javax.jms.JMSException;
    import javax.jms.Message;
    import javax.jms.MessageListener;
    import javax.jms.TextMessage;
    ```

7. Em seguida, atualize suas anotações de classe de ouvinte para corresponder ao exemplo a seguir. Essa classe fornece uma implementação de exemplo que registra o recebimento de mensagens.

    ```java
    @TransactionManagement(TransactionManagementType.BEAN)
    @TransactionAttribute(TransactionAttributeType.NOT_SUPPORTED)
    @MessageDriven(name = "MyQueueListener", activationConfig = {
            @ActivationConfigProperty(propertyName = "connectionFactory", propertyValue = "${property.connection.factory}"),
            @ActivationConfigProperty(propertyName = "destinationLookup", propertyValue = "${property.mymdb.queue}"),
            @ActivationConfigProperty(propertyName = "destinationType", propertyValue = "javax.jms.Queue"),
            @ActivationConfigProperty(propertyName = "acknowledgeMode", propertyValue = "Auto-acknowledge") })
    public class MyQueueListener implements MessageListener {

        private static final Logger LOGGER = Logger.getLogger(TopicListener.class.toString());

        public void onMessage(Message rcvMessage) {
            TextMessage msg = null;
            try {
                if (rcvMessage instanceof TextMessage) {
                    msg = (TextMessage) rcvMessage;
                    LOGGER.info("Received Message from topic: " + msg.getText());
                } else {
                    LOGGER.warning("Message of wrong type: " + rcvMessage.getClass().getName());
                }
            } catch (JMSException e) {
                LOGGER.warning("Exception on message : " + e.getMessage());
                throw new RuntimeException(e);
            }
        }
    }
    ```

    Os valores `connectionFactory` e `destinationLookup` referem-se aos valores de Propriedade do sistema WildFly configurados pelo script *Startup.sh* . O valor de `destinationType` é `javax.jms.Queue`, indicando que você está se conectando a uma instância de fila do barramento de serviço. Esse valor deve ser `javax.jms.Topic` quando você se conecta a um tópico do barramento de serviço, como mostrado aqui:

    ```java
    @TransactionManagement(TransactionManagementType.BEAN)
    @TransactionAttribute(TransactionAttributeType.NOT_SUPPORTED)
    @MessageDriven(name = "MyTopicListener", activationConfig = {
            @ActivationConfigProperty(propertyName = "connectionFactory", propertyValue = "${property.connection.factory}"),
            @ActivationConfigProperty(propertyName = "destinationLookup", propertyValue = "${property.mymdb.topic}"),
            @ActivationConfigProperty(propertyName = "destinationType", propertyValue = "javax.jms.Topic"),
            @ActivationConfigProperty(propertyName = "acknowledgeMode", propertyValue = "Auto-acknowledge") })
        public class MyTopicListener implements MessageListener {
        // ...
    }
    ```

8. Atualize a seção `dependencies` do arquivo *pom. xml* para adicionar as seguintes dependências:

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.qpid</groupId>
            <artifactId>qpid-jms-client</artifactId>
            <version>0.40.0</version>
        </dependency>
        <dependency>
            <groupId>org.apache.qpid</groupId>
            <artifactId>proton-j</artifactId>
            <version>0.31.0</version>
        </dependency>
        <dependency>
            <groupId>javax.enterprise</groupId>
            <artifactId>cdi-api</artifactId>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.jboss.spec.javax.ejb</groupId>
            <artifactId>jboss-ejb-api_3.2_spec</artifactId>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.jboss.spec.javax.jms</groupId>
            <artifactId>jboss-jms-api_2.0_spec</artifactId>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.jboss.spec.javax.servlet</groupId>
            <artifactId>jboss-servlet-api_4.0_spec</artifactId>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.jboss.spec.javax.annotation</groupId>
            <artifactId>jboss-annotations-api_1.3_spec</artifactId>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

9. Atualize a configuração de `azure-webapp-maven-plugin` no arquivo *pom. xml* para consultar as informações da conta do barramento de serviço. Se necessário, altere `1.7.0` para a versão atual do [plug-in do Maven para Azure app Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
        <configuration>

            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}</appServicePlanName>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>

            <!-- Java Runtime Stack for Web App on Linux-->
            <linuxRuntime>wildfly 14-jre8</linuxRuntime>

            <appSettings>
                <property>
                    <name>DEFAULT_SBNAMESPACE</name>
                    <value>${DEFAULT_SBNAMESPACE}</value>
                </property>
                <property>
                    <name>SB_SAS_POLICY</name>
                    <value>${SB_SAS_POLICY}</value>
                </property>
                <property>
                    <name>SB_SAS_KEY</name>
                    <value>${SB_SAS_KEY}</value>
                </property>
                <property>
                    <name>PROVIDER_URL</name>
                    <value>${PROVIDER_URL}</value>
                </property>
                <property>
                    <name>SB_QUEUE</name>
                    <value>${SB_QUEUE}</value>
                </property>
                <property>
                    <name>SB_TOPIC</name>
                    <value>${SB_TOPIC}</value>
                </property>
                <property>
                    <name>SB_SUBSCRIPTION</name>
                    <value>${SB_SUBSCRIPTION}</value>
                </property>
            </appSettings>
        </configuration>
    </plugin>
    ```

    Essas configurações configuram sua instância do serviço de aplicativo para que ela tenha as mesmas variáveis de ambiente que você definiu localmente. Ele usa variáveis de ambiente para manter as informações de sua conta dos arquivos de origem.

10. Recompile e reimplante seu aplicativo.

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

Seu Bean controlado por mensagem agora está configurado para usar o barramento de serviço como o mecanismo de mensagens.

Na próxima vez em que o serviço de aplicativo for reiniciado, ele executará o script de inicialização e executará as etapas de configuração necessárias. Para testar se essa configuração ocorre corretamente, você pode acessar o serviço de aplicativo usando o SSH e, em seguida, executar o script de inicialização por conta própria no prompt do bash. Você também pode examinar os logs do serviço de aplicativo. Para obter mais informações sobre essas opções, consulte [log and Debugging apps](#logging-and-debugging-apps).

Para obter um exemplo que você pode usar para testar essas instruções, consulte o repositório [Migrate-Java-EE-App-to-Azure-2](https://github.com/Azure-Samples/migrate-java-ee-app-to-azure-2) no GitHub e procure o exemplo de `helloworld-mdb-propertysubstitution`.

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>Usar Redis como um cache de sessão com Tomcat

Você pode configurar o Tomcat para usar um repositório de sessão externa, como o [cache do Azure para Redis](/azure/azure-cache-for-redis/). Isso permite preservar o estado da sessão do usuário (como dados do carrinho de compras) quando um usuário é transferido para outra instância do aplicativo, por exemplo, quando o dimensionamento automático, reinicialização ou failover ocorre.

Para usar o Tomcat com Redis, você deve configurar seu aplicativo para usar uma implementação [persistentemanager](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) . As etapas a seguir explicam esse processo usando o [Gerenciador de sessão dinâmica: Redis-Store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) como um exemplo.

1. Abra um terminal Bash e use `<variable>=<value>` para definir cada uma das variáveis de ambiente a seguir.

    | Variável                 | Valor                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | O nome do grupo de recursos que contém a instância do serviço de aplicativo.       |
    | WEBAPP_NAME              | O nome da instância do serviço de aplicativo.                                     |
    | WEBAPP_PLAN_NAME         | O nome do plano do serviço de aplicativo.                                         |
    | REGIÃO                   | O nome da região em que seu aplicativo está hospedado.                           |
    | REDIS_CACHE_NAME         | O nome do seu cache do Azure para a instância Redis.                           |
    | REDIS_PORT               | A porta SSL que o cache Redis escuta.                             |
    | REDIS_PASSWORD           | A chave de acesso primária para sua instância.                                  |
    | REDIS_SESSION_KEY_PREFIX | Um valor que você especifica para identificar as chaves de sessão que vêm do seu aplicativo. |

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

    Você pode encontrar as informações de nome, porta e chave de acesso na portal do Azure examinando as seções **Propriedades** ou **chaves de acesso** da sua instância de serviço.

2. Crie ou atualize o arquivo *src/main/webapp/META-INF/context. xml* do seu aplicativo com o seguinte conteúdo:

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

    Esse arquivo especifica e configura a implementação do Gerenciador de sessão para seu aplicativo. Ele usa as variáveis de ambiente que você definiu na etapa anterior para manter suas informações de conta fora dos seus arquivos de origem.

3. Use o FTP para carregar o arquivo JAR do Gerenciador de sessão em sua instância do serviço de aplicativo, colocando-o no diretório */Home/Tomcat/lib* Para obter mais informações, consulte [implantar seu aplicativo no serviço de Azure App usando FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

4. Desabilite o [cookie de afinidade de sessão](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) para sua instância do serviço de aplicativo. Você pode fazer isso na portal do Azure navegando até seu aplicativo e, em seguida, definindo **configuração > configurações gerais > afinidade arr** como **desativado**. Como alternativa, você pode usar o seguinte comando:

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    Por padrão, o serviço de aplicativo usará cookies de afinidade de sessão para garantir que as solicitações de cliente com sessões existentes sejam roteadas para a mesma instância do seu aplicativo. Esse comportamento padrão não requer nenhuma configuração, mas não pode preservar o estado da sessão do usuário quando a instância do aplicativo é reiniciada ou quando o tráfego é redirecionado para outra instância. Ao [desabilitar a configuração de afinidade da instância arr existente](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) para desativar o roteamento baseado em cookie de sessão, você permite que o repositório de sessão configurado opere sem interferência.

5. Navegue até a seção **Propriedades** da instância do serviço de aplicativo e localize **endereços IP de saída adicionais**. Elas representam todos os endereços IP de saída possíveis para seu aplicativo. Copie-os para uso na próxima etapa.

6. Para cada endereço IP, crie uma regra de firewall em seu cache do Azure para a instância Redis. Você pode fazer isso no portal do Azure da seção de **Firewall** da instância do Redis. Forneça um nome exclusivo para cada regra e defina os valores de endereço IP **inicial** e **endereço IP final** como o mesmo endereço IP.

7. Navegue até a seção **Configurações avançadas** da instância do Redis e defina **permitir acesso somente via SSL** para **não**. Isso permite que sua instância do serviço de aplicativo se comunique com o cache Redis por meio da infraestrutura do Azure.

8. Atualize a configuração de `azure-webapp-maven-plugin` no arquivo *pom. xml* do seu aplicativo para se referir às informações da sua conta do Redis. Esse arquivo usa as variáveis de ambiente que você definiu anteriormente para manter suas informações de conta fora dos seus arquivos de origem.

    Se necessário, altere `1.7.0` para a versão atual do [plug-in do Maven para Azure app Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

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

9. Recompile e reimplante seu aplicativo.

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

Agora, seu aplicativo usará o cache Redis para o gerenciamento de sessão.

Para obter um exemplo que você pode usar para testar essas instruções, consulte o repositório [Dimensioning-stateful-Java-Web-App-on-Azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) no github.

## <a name="docker-containers"></a>Contentores do Docker

Para usar o Zulu JDK com suporte do Azure em seus contêineres, certifique-se de efetuar pull e usar as imagens predefinidas, conforme documentado na [página de download com suporte do azul Zulu Enterprise para Azure](https://www.azul.com/downloads/azure-only/zulu/) , ou use os exemplos de `Dockerfile` do [repositório GitHub do Microsoft Java](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Declaração de suporte

### <a name="runtime-availability"></a>Disponibilidade de tempo de execução

O serviço de aplicativo para Linux dá suporte a dois tempos de execução para hospedagem gerenciada de aplicativos Web Java:

- O [contêiner do servlet Tomcat](https://tomcat.apache.org/) para executar aplicativos empacotados como arquivos War (arquivo da Web). As versões com suporte são 8,5 e 9,0.
- Ambiente de tempo de execução Java SE para executar aplicativos empacotados como arquivos de arquivo Java (JAR). As versões com suporte são Java 8 e 11.

### <a name="jdk-versions-and-maintenance"></a>Versões e manutenção do JDK

Os OpenJDKs empresariais do azul Zulu são uma distribuição pronta para produção, sem custo, de várias plataformas, do OpenJDK para o Azure e Azure Stack com suporte dos sistemas Microsoft e azul. Eles contêm todos os componentes para criar e executar aplicativos Java SE. Você pode instalar o JDK da [instalação do Java JDK](https://aka.ms/azure-jdks).

Os JDKs com suporte são automaticamente corrigidos em uma base trimestral em Janeiro, abril, julho e outubro de cada ano.

### <a name="security-updates"></a>Atualizações de segurança

Patches e correções para vulnerabilidades de segurança principais serão lançados assim que forem disponibilizados por meio de sistemas azul. Uma vulnerabilidade "principal" é definida por uma pontuação base de 9,0 ou superior no [sistema de Pontuação de vulnerabilidade comum do NIST, versão 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Reprovação e aposentadoria

Se um tempo de execução de Java com suporte for desativado, os desenvolvedores do Azure que usam o tempo de execução afetado receberão um aviso de substituição pelo menos seis meses antes de o tempo de execução ser desativado.

## <a name="next-steps"></a>Passos seguintes

Visite o centro [de desenvolvedores do Azure para Java](/java/azure/) para localizar guias de início rápido, tutoriais e documentação de referência de Java do Azure.

As perguntas gerais sobre como usar o serviço de aplicativo para Linux que não são específicas do desenvolvimento do Java são respondidas nas [perguntas frequentes](app-service-linux-faq.md)sobre o Linux do serviço de aplicativo.
