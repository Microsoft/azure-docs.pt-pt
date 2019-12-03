---
title: Configurar aplicativos Java do Windows
description: Saiba como configurar aplicativos Java para serem executados em instâncias de VM do Windows no serviço Azure App. Este artigo mostra as tarefas de configuração mais comuns.
keywords: serviço de aplicativo do Azure, aplicativo Web, Windows, OSS, Java
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: dec5d1c7c7664bf72d92e5aca4333ba64db26d02
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671913"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Configurar um aplicativo Java do Windows para o serviço Azure App

Azure App serviço permite que os desenvolvedores de Java compilem, implantem e dimensionem rapidamente seus aplicativos Web Tomcat em um serviço totalmente gerenciado baseado no Windows. Implante aplicativos com plug-ins do Maven a partir da linha de comando ou em editores como IntelliJ, Eclipse ou Visual Studio Code.

Este guia fornece os principais conceitos e instruções para desenvolvedores Java usando o no serviço de aplicativo. Se você nunca usou Azure App serviço, leia primeiro o [início rápido do Java](app-service-web-get-started-java.md) . As perguntas gerais sobre como usar o serviço de aplicativo que não são específicas do desenvolvimento do Java são respondidas nas [perguntas frequentes do Windows do serviço de aplicativo](faq-configuration-and-management.md).

## <a name="deploying-your-app"></a>Implantando seu aplicativo

Você pode usar o [plug-in do Maven para Azure app serviço](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) para implantar seus arquivos. War. A implantação com IDEs populares também tem suporte com [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) ou [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

Caso contrário, o método de implantação dependerá do tipo de arquivo morto:

- Para implantar arquivos. War no Tomcat, use o ponto de extremidade `/api/wardeploy/` para postar o arquivo morto. Para obter mais informações sobre essa API, consulte [esta documentação](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).

Não implante seu. War usando FTP. A ferramenta de FTP foi projetada para carregar scripts de inicialização, dependências ou outros arquivos de tempo de execução. Não é a opção ideal para a implantação de aplicativos Web.

## <a name="logging-and-debugging-apps"></a>Log e depuração de aplicativos

Relatórios de desempenho, visualizações de tráfego e verificação de integridade estão disponíveis para cada aplicativo por meio do portal do Azure. Para obter mais informações, consulte [visão geral do diagnóstico de serviço Azure app](overview-diagnostics.md).

### <a name="stream-diagnostic-logs"></a>Transmitir registos de diagnóstico em fluxo

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Para obter mais informações, consulte [Stream logs in Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Log de aplicativo

Habilite o [log do aplicativo](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) por meio do portal do Azure ou [CLI do Azure](/cli/azure/webapp/log#az-webapp-log-config) para configurar o serviço de aplicativo para gravar a saída do console padrão do aplicativo e os fluxos de erro do console padrão no sistema de arquivos local ou no armazenamento de BLOBs do Azure. O registro em log na instância do sistema de arquivos do serviço de aplicativo local é desabilitado 12 horas após sua configuração. Se você precisar de mais retenção, configure o aplicativo para gravar a saída em um contêiner de armazenamento de BLOBs. Seus logs de aplicativo Java e Tomcat podem ser encontrados no diretório */LogFiles/Application/*

Se seu aplicativo usar [Logback](https://logback.qos.ch/) ou [Log4J](https://logging.apache.org/log4j) para rastreamento, você poderá encaminhar esses rastreamentos para análise em aplicativo Azure insights usando as instruções de configuração da estrutura de registro em [explorar logs de rastreamento do Java no Application insights](/azure/application-insights/app-insights-java-trace-logs).


## <a name="customization-and-tuning"></a>Personalização e ajuste

O serviço de Azure App dá suporte ao ajuste e à personalização prontos para uso por meio do portal do Azure e da CLI. Examine os seguintes artigos para configuração de aplicativo Web não específica para Java:

- [Definir configurações de aplicativo](configure-common.md#configure-app-settings)
- [Configurar um domínio personalizado](app-service-web-tutorial-custom-domain.md)
- [Configurar associações SSL](configure-ssl-bindings.md)
- [Adicionar uma CDN](../cdn/cdn-add-to-web-app.md)
- [Configurar o site kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Definir opções de tempo de execução Java

Para definir a memória alocada ou outras opções de tempo de execução da JVM, crie uma [configuração de aplicativo](configure-common.md#configure-app-settings) chamada `JAVA_OPTS` com as opções. O serviço de aplicativo passa essa configuração como uma variável de ambiente para o tempo de execução Java quando ele é iniciado.

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

### <a name="pre-compile-jsp-files"></a>Pré-compilar arquivos JSP

Para melhorar o desempenho de aplicativos Tomcat, você pode compilar seus arquivos JSP antes de implantar no serviço de aplicativo. Você pode usar o [plug-in do Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) fornecido pelo Apache sling ou usando esse [arquivo de compilação Ant](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Proteger aplicativos

Aplicativos Java em execução no serviço de aplicativo têm o mesmo conjunto de [práticas recomendadas de segurança](/azure/security/security-paas-applications-using-app-services) que outros aplicativos.

### <a name="authenticate-users-easy-auth"></a>Autenticar usuários (fácil autenticação)

Configure a autenticação de aplicativo no portal do Azure com a opção **autenticação e autorização** . A partir daí, você pode habilitar a autenticação usando Azure Active Directory ou logons sociais, como Facebook, Google ou GitHub. Portal do Azure configuração só funciona ao configurar um único provedor de autenticação. Para obter mais informações, consulte [configurar seu aplicativo do serviço de aplicativo para usar Azure Active Directory logon](configure-authentication-provider-aad.md) e os artigos relacionados para outros provedores de identidade. Se você precisar habilitar vários provedores de entrada, siga as instruções no artigo personalizar a [autenticação do serviço de aplicativo](app-service-authentication-how-to.md) .

#### <a name="tomcat-and-wildfly"></a>Tomcat e Wildfly

Seu aplicativo Tomcat ou Wildfly pode acessar as declarações do usuário diretamente do servlet, convertendo o objeto principal em um objeto Map. O objeto MAP mapeará cada tipo de declaração para uma coleção de declarações para esse tipo. No código abaixo, `request` é uma instância de `HttpServletRequest`.

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

### <a name="configure-tlsssl"></a>Configurar TLS/SSL

Siga as instruções em [proteger um nome DNS personalizado com uma associação SSL no serviço Azure app](configure-ssl-bindings.md) para carregar um certificado SSL existente e associá-lo ao nome de domínio do seu aplicativo. Por padrão, seu aplicativo ainda permitirá conexões HTTP – siga as etapas específicas no tutorial para impor SSL e TLS.

### <a name="use-keyvault-references"></a>Usar referências do keyvault

O [Azure keyvault](../key-vault/key-vault-overview.md) fornece gerenciamento de segredos centralizado com políticas de acesso e histórico de auditoria. Você pode armazenar segredos (como senhas ou cadeias de conexão) no keyvault e acessar esses segredos em seu aplicativo por meio de variáveis de ambiente.

Primeiro, siga as instruções para [conceder acesso ao aplicativo para Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) e [fazer uma referência de keyvault para seu segredo em uma configuração de aplicativo](app-service-key-vault-references.md#reference-syntax). Você pode validar que a referência seja resolvida para o segredo imprimindo a variável de ambiente ao acessar remotamente o terminal do serviço de aplicativo.

Para injetar esses segredos em seu arquivo de configuração Spring ou Tomcat, use a sintaxe de injeção de variável de ambiente (`${MY_ENV_VAR}`). Para arquivos de configuração do Spring, consulte esta documentação sobre [configurações externas](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).


## <a name="configure-apm-platforms"></a>Configurar plataformas APM

Esta seção mostra como conectar aplicativos Java implantados no serviço de Azure App no Linux com as plataformas de monitoramento de desempenho de aplicativos NewRelic e AppDynamics (APM).

### <a name="configure-new-relic"></a>Configurar novo Relic

1. Criar uma nova conta do Relic em [NewRelic.com](https://newrelic.com/signup)
2. Baixe o agente Java de NewRelic, ele terá um nome de arquivo semelhante a *newrelic-Java-x. x. x. zip*.
3. Copie sua chave de licença, você precisará dela para configurar o agente mais tarde.
4. Use o [console do kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) para criar um novo diretório */Home/site/wwwroot/APM*.
5. Carregue os arquivos do novo agente Java Relic desempacotados em um diretório em */Home/site/wwwroot/APM*. Os arquivos do seu agente devem estar no */Home/site/wwwroot/APM/newrelic*.
6. Modifique o arquivo YAML em */Home/site/wwwroot/APM/newrelic/newrelic.yml* e substitua o valor da licença de espaço reservado por sua própria chave de licença.
7. Na portal do Azure, navegue até seu aplicativo no serviço de aplicativo e crie uma nova configuração de aplicativo.
    - Se seu aplicativo estiver usando **Java se**, crie uma variável de ambiente chamada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se você estiver usando o **tomcat**, crie uma variável de ambiente chamada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.

### <a name="configure-appdynamics"></a>Configurar o AppDynamics

1. Criar uma conta do AppDynamics em [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Baixe o agente Java do site AppDynamics, o nome do arquivo será semelhante a *AppServerAgent-x. x. x. xxxxx. zip*
3. Use o [console do kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) para criar um novo diretório */Home/site/wwwroot/APM*.
4. Carregue os arquivos do agente Java em um diretório em */Home/site/wwwroot/APM*. Os arquivos do seu agente devem estar no */Home/site/wwwroot/APM/AppDynamics*.
5. Na portal do Azure, navegue até seu aplicativo no serviço de aplicativo e crie uma nova configuração de aplicativo.
    - Se você estiver usando **Java se**, crie uma variável de ambiente chamada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` em que `<app-name>` é o nome do serviço de aplicativo.
    - Se você estiver usando o **tomcat**, crie uma variável de ambiente chamada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` em que `<app-name>` é o nome do serviço de aplicativo.

>  Se você já tiver uma variável de ambiente para `JAVA_OPTS` ou `CATALINA_OPTS`, acrescente a opção `-javaagent:/...` ao final do valor atual.

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

#### <a name="finalize-configuration"></a>Finalizar configuração

Por fim, colocaremos os JARs do driver no classpath do Tomcat e reiniciaremos o serviço de aplicativo. Certifique-se de que os arquivos do driver JDBC estejam disponíveis para o carregador de diretório do Tomcat colocando-os no */Home/Tomcat/lib* Directory. (Crie esse diretório se ele ainda não existir.) Para carregar esses arquivos em sua instância do serviço de aplicativo, execute as seguintes etapas:

1. No [Cloud Shell](https://shell.azure.com), instale a extensão webapp:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Execute o seguinte comando da CLI para criar um túnel SSH do seu sistema local para o serviço de aplicativo:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Conecte-se à porta de túnel local com seu cliente SFTP e carregue os arquivos na pasta */Home/Tomcat/lib*

Como alternativa, você pode usar um cliente FTP para carregar o driver JDBC. Siga estas [instruções para obter suas credenciais de FTP](deploy-configure-credentials.md).

## <a name="configuring-tomcat"></a>Configurando o Tomcat

Para editar `server.xml` do Tomcat ou outros arquivos de configuração, primeiro anote a sua versão principal do Tomcat no Portal.

1. Localize o diretório base do Tomcat para sua versão executando o comando `env`. Procure a variável de ambiente que começa com `AZURE_TOMCAT`e corresponde à sua versão principal. Por exemplo, `AZURE_TOMCAT85_HOME` aponta para o diretório Tomcat para Tomcat 8,5.
1. Depois de identificar o diretório base do Tomcat para sua versão, copie o diretório de configuração para `D:\home`. Por exemplo, se `AZURE_TOMCAT85_HOME` tiver um valor de `D:\Program Files (x86)\apache-tomcat-8.5.37`, o novo caminho do diretório copiado será `D:\home\apache-tomcat-8.5.37`.

Por fim, reinicie o serviço de aplicativo. Suas implantações devem ir para `D:\home\site\wwwroot\webapps` assim como antes.

## <a name="java-runtime-statement-of-support"></a>Declaração de suporte do Java Runtime

### <a name="jdk-versions-and-maintenance"></a>Versões e manutenção do JDK

O Java Development Kit (JDK) com suporte do Azure é [Zulu](https://www.azul.com/downloads/azure-only/zulu/) fornecido por meio de [sistemas azul](https://www.azul.com/).

As atualizações da versão principal serão fornecidas por meio de novas opções de tempo de execução no serviço Azure App para Windows. Os clientes atualizam para essas versões mais recentes do Java Configurando sua implantação do serviço de aplicativo e são responsáveis por testar e garantir que a atualização principal atenda às suas necessidades.

Os JDKs com suporte são automaticamente corrigidos em uma base trimestral em Janeiro, abril, julho e outubro de cada ano. Para saber mais sobre Java no Azure, confira [este documento de suporte](https://docs.microsoft.com/azure/java/jdk/).

### <a name="security-updates"></a>Atualizações de segurança

Patches e correções para vulnerabilidades de segurança principais serão lançados assim que forem disponibilizados por meio de sistemas azul. Uma vulnerabilidade "principal" é definida por uma pontuação base de 9,0 ou superior no [sistema de Pontuação de vulnerabilidade comum do NIST, versão 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Reprovação e aposentadoria

Se um tempo de execução de Java com suporte for desativado, os desenvolvedores do Azure que usam o tempo de execução afetado receberão um aviso de substituição pelo menos seis meses antes de o tempo de execução ser desativado.

### <a name="local-development"></a>Desenvolvimento local

Os desenvolvedores podem baixar a edição de produção do azul Zulu Enterprise JDK para o desenvolvimento local do [site de download do azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Suporte a desenvolvimento

O suporte ao produto para o [azul Zulu com suporte do Azure](https://www.azul.com/downloads/azure-only/zulu/) está disponível por meio da Microsoft ao desenvolver para o azure ou [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) com um [plano de suporte qualificado do Azure](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Suporte a tempo de execução

Os desenvolvedores podem [abrir um problema](/azure/azure-supportability/how-to-create-azure-support-request) com o azul Zulu JDKs por meio do suporte do Azure se eles tiverem um [plano de suporte qualificado](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Passos seguintes

Este tópico fornece a declaração de suporte do Java em tempo de execução para Azure App serviço no Windows.

- Para saber mais sobre como hospedar aplicativos Web com o serviço Azure App, consulte [visão geral do serviço de aplicativo](overview.md).
- Para obter informações sobre o Java no desenvolvimento do Azure, consulte [Azure para Java dev Center](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
