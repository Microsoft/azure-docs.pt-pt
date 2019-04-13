---
title: Configurar aplicações de Java do Linux - serviço de aplicações do Azure | Documentos da Microsoft
description: Saiba como configurar as aplicações de Java em execução no serviço de aplicações do Azure no Linux.
keywords: serviço de aplicações do Azure, aplicação web, linux, oss, java
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 03/28/2019
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: b659c076974b0659c645c9b6460e458dfac8974a
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551236"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Configurar uma aplicação Java do Linux para o serviço de aplicações do Azure

Serviço de aplicações do Azure no Linux permite aos programadores de Java para rapidamente criar, implementar e dimensionar seus Tomcat ou aplicativos de web num serviço baseado em Linux totalmente gerido de pacote de Java Standard Edition (SE). Implemente aplicações com o plug-ins do Maven na linha de comandos ou em editores de como o IntelliJ, o Eclipse ou o Visual Studio Code.

Este guia fornece os conceitos chave e instruções para desenvolvedores de Java que utilizam um contentor do Linux incorporado no serviço de aplicações. Se nunca tiver utilizado o serviço de aplicações do Azure, siga os [início rápido de Java](quickstart-java.md) e [Java com o tutorial do PostgreSQL](tutorial-java-enterprise-postgresql-app.md) primeiro.

## <a name="logging-and-debugging-apps"></a>Registo e depuração de aplicativos

Relatórios de desempenho, visualizações de tráfego e exames abrangentes de estado de funcionamento estão disponíveis para cada aplicação através do portal do Azure. Para obter mais informações, consulte [descrição geral de diagnóstico do serviço de aplicações do Azure](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>Acesso à consola SSH

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Transmitir registos de diagnóstico em fluxo

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Para obter mais informações, consulte [transmissão em fluxo registos com a CLI do Azure](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli).

### <a name="app-logging"></a>Registo de aplicação

Ativar [registo de aplicações](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enablediag) através do portal do Azure ou [CLI do Azure](/cli/azure/webapp/log#az-webapp-log-config) para configurar o serviço de aplicações para escrever a saída do console padrão e os fluxos de erro de console padrão da sua aplicação no local sistema de ficheiros ou armazenamento de Blobs do Azure. O registo para o sistema de ficheiros local do serviço de aplicações instância está desativada 12 horas depois de estar configurada. Se precisar de retenção mais longa, configure a aplicação para escrever a saída para um contentor de armazenamento de Blobs.

Se o aplicativo usar [Logback](https://logback.qos.ch/) ou [Log4j](https://logging.apache.org/log4j) para o rastreio, pode reencaminhar estes rastreios para revisão para o Azure Application Insights com as instruções de configuração do Registro em log framework [Registos de rastreio de explorar o Java no Application Insights](/azure/application-insights/app-insights-java-trace-logs).

## <a name="customization-and-tuning"></a>Personalização e Otimização

Serviço de aplicações do Azure para Linux oferece imediatamente suporte para a caixa de ajuste e a personalização através do portal do Azure e CLI. Reveja os seguintes artigos para a configuração da aplicação web de não-Java-específicas:

- [Configurar as definições do serviço de aplicações](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Configurar um domínio personalizado](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Ativar o SSL](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Adicionar uma CDN](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)

### <a name="set-java-runtime-options"></a>Definir opções de tempo de execução de Java

Para definir a memória alocada ou outras opções de tempo de execução JVM nos ambientes do Tomcat e Java SE, defina o JAVA_OPTS conforme mostrado abaixo, como um [definição de aplicação](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings). Serviço de aplicações Linux passa esta definição como uma variável de ambiente para o tempo de execução Java quando ele é iniciado.

No portal do Azure, em **as configurações do aplicativo** da aplicação web, criar uma nova definição de aplicação com o nome `JAVA_OPTS` que inclui as definições adicionais, tais como `$JAVA_OPTS -Xms512m -Xmx1204m`.

Para configurar a definição de aplicação o plug-in do Maven de Linux do Azure App Service, adicione marcas de definição/valor na secção de plug-in do Azure. O exemplo seguinte define um mínimo e máximo Java heap tamanho específico:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>$JAVA_OPTS -Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Os desenvolvedores com um único aplicativo bloco de implementação de um em seu plano do serviço de aplicações podem utilizar as seguintes opções:

- Instâncias B1 e S1:-Xms1024m-Xmx1024m
- Instâncias de B2 e S2:-Xms3072m-Xmx3072m
- Instâncias B3 e S3:-Xms6144m-Xmx6144m


Quando o ajuste definições de área dinâmica para dados de aplicação, reveja os detalhes do seu plano de serviço de aplicações e levar em conta vários aplicativos e o bloco de implementação tem de localizar a alocação ideal de memória.

### <a name="turn-on-web-sockets"></a>Ativar o web sockets

Ativar o suporte de web sockets no portal do Azure no **as definições da aplicação** para a aplicação. Terá de reiniciar a aplicação para a definição tenha efeito.

Ative o suporte de socket web com a CLI do Azure com o seguinte comando:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Em seguida, reinicie a aplicação:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Definir a codificação de caracteres padrão

No portal do Azure, em **as configurações do aplicativo** da aplicação web, criar uma nova definição de aplicação com o nome `JAVA_OPTS` com o valor `$JAVA_OPTS -Dfile.encoding=UTF-8`.

Em alternativa, pode configurar a definição de aplicação com o plug-in do Maven do serviço de aplicações. Adicione as etiquetas de nome e valor de definição na configuração do plug-in:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>$JAVA_OPTS -Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

## <a name="secure-applications"></a>Aplicações seguras

Aplicações de Java em execução no serviço de aplicações para Linux tem o mesmo conjunto de [melhores práticas de segurança](/azure/security/security-paas-applications-using-app-services) como outras aplicações. 

### <a name="authenticate-users"></a>Autenticar utilizadores

Configurar a autenticação de aplicação no portal do Azure com o **autenticação e autorização** opção. A partir daí, pode ativar a autenticação com o Azure Active Directory ou inícios de sessão sociais como o Facebook, Google ou GitHub. Configuração do portal do Azure só funciona quando configurar um fornecedor de autenticação único. Para obter mais informações, consulte [configurar a aplicação de serviço de aplicações para utilizar o início de sessão do Azure Active Directory](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) e os artigos relacionados para outros fornecedores de identidade.

Se precisar de vários fornecedores de início de sessão, siga as instruções no [personalizar a autenticação do serviço de aplicações](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) artigo.

 Os programadores de arranque de Spring podem utilizar o [do Azure Active Directory Spring Boot starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) para proteger aplicações através de anotações de segurança de Spring familiares e APIs.

### <a name="configure-tlsssl"></a>Configurar TLS/SSL

Siga as instruções no [vincular um certificado SSL personalizado existente](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) para carregar um certificado SSL existente e vinculá-lo para o nome de domínio da sua aplicação. Por predefinição seu aplicativo ainda permitirá HTTP ligações-siga específica os passos no tutorial para impor SSL e TLS.

## <a name="configure-apm-platforms"></a>Configurar as plataformas APM

Esta secção mostra como ligar aplicações de Java implementadas no serviço de aplicações do Azure no Linux com o desempenho da aplicação da NewRelic e AppDynamics monitorização plataformas (APM).

[Configurar o New Relic](#configure-new-relic)
[configurar AppDynamics](#configure-appdynamics)

### <a name="configure-new-relic"></a>Configurar o New Relic

1. Criar uma conta da NewRelic no [NewRelic.com](https://newrelic.com/signup)
2. Transferir o agente Java a partir da NewRelic, ele terá um nome de ficheiro semelhante a `newrelic-java-x.x.x.zip`.
3. Copiar a chave de licença, precisará das mesmas para configurar o agente mais tarde.
4. [SSH para a instância de serviço de aplicações](app-service-linux-ssh-support.md) e criar um novo diretório `/home/site/wwwroot/apm`.
5. Carregar os ficheiros de agente NewRelic Java descompactados para um diretório na `/home/site/wwwroot/apm`. Os ficheiros para o agente devem estar no `/home/site/wwwroot/apm/newrelic`.
6. Modificar o ficheiro YAML em `/home/site/wwwroot/apm/newrelic/newrelic.yml` e substitua o valor de licença de marcador de posição pela sua chave de licença.
7. No portal do Azure, navegue para a sua aplicação no serviço de aplicações e criar uma nova definição de aplicação.
    - Se seu aplicativo está usando **Java SE**, criar uma variável de ambiente chamada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se estiver a utilizar **Tomcat**, criar uma variável de ambiente chamada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se estiver a utilizar **WildFly**, consulte a documentação do New Relic [aqui](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) para obter orientações sobre como instalar o agente Java e a configuração de JBoss.
    - Se já tiver uma variável de ambiente para `JAVA_OPTS` ou `CATALINA_OPTS`, acrescentar o `javaagent` opção ao final do valor atual.

### <a name="configure-appdynamics"></a>Configurar o AppDynamics

1. Criar uma conta do AppDynamics no [AppDynamics.com](https://www.appdynamics.com/community/register/)
1. Transferir o agente de Java a partir do site do AppDynamics, o nome do ficheiro será semelhante a `AppServerAgent-x.x.x.xxxxx.zip`
1. [SSH para a instância de serviço de aplicações](app-service-linux-ssh-support.md) e criar um novo diretório `/home/site/wwwroot/apm`.
1. Carregar os ficheiros de agente Java para um diretório na `/home/site/wwwroot/apm`. Os ficheiros para o agente devem estar no `/home/site/wwwroot/apm/appdynamics`.
1. No portal do Azure, navegue para a sua aplicação no serviço de aplicações e criar uma nova definição de aplicação.
    - Se estiver a utilizar **Java SE**, criar uma variável de ambiente chamada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` onde `<app-name>` é o seu nome de serviço de aplicações.
    - Se estiver a utilizar **Tomcat**, criar uma variável de ambiente chamada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` onde `<app-name>` é o seu nome de serviço de aplicações.
    - Se estiver a utilizar **WildFly**, consulte a documentação do AppDynamics [aqui](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) para obter orientações sobre como instalar o agente Java e a configuração de JBoss.

## <a name="configure-tomcat"></a>Configurar o Tomcat

### <a name="connect-to-data-sources"></a>Ligar a origens de dados

>[!NOTE]
> Se a sua aplicação utilizar o Spring Framework ou o Spring Boot, pode definir as informações de ligação de base de dados para a Primavera dados JPA como variáveis de ambiente [em seu arquivo de propriedades da aplicação]. Em seguida, utilize [as definições da aplicação](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) para definir esses valores para a sua aplicação no portal do Azure ou na CLI.

Estas instruções aplicam-se a todas as ligações de base de dados. Precisará preencher espaços reservados com um nome de classe do controlador da base de dados escolhido e JAR do ficheiro. Fornecida é uma tabela com nomes de classes e downloads de driver para bases de dados comuns.

| Base de Dados   | Nome da classe de controlador                             | JDBC Driver                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Transferência](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Transferir](https://dev.mysql.com/downloads/connector/j/) (selecione "Independente de plataforma") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Transferência](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Para configurar o Tomcat para utilizar a conectividade de banco de dados de Java (JDBC) ou a API de persistência de Java (JPA), personalizar o `CATALINA_OPTS` variável de ambiente é lida por Tomcat na inicialização. Definir esses valores por meio de uma definição de aplicação no [Plug-in Maven do serviço de aplicações](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Ou definir as variáveis de ambiente no painel "Definições de aplicação" no portal do Azure.

Em seguida, determine se a origem de dados deve estar disponível para um aplicativo ou a todas as aplicações em execução no Tomcat servlet.

#### <a name="application-level-data-sources"></a>Origens de dados de nível de aplicativo

1. Criar uma `context.xml` de ficheiros a `META-INF/` diretório do seu projeto. Criar o `META-INF/` directory se não existir.

2. Na `context.xml`, adicione um `Context` elemento para ligar à origem de dados para um endereço JNDI. Substitua o `driverClassName` marcador de posição com um nome de classe do seu controlador da tabela acima.

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

3. Atualizar a sua aplicação `web.xml` para utilizar a origem de dados na sua aplicação.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Recursos partilhados de ao nível do servidor

1. Copie o conteúdo do `/usr/local/tomcat/conf` em `/home/tomcat/conf` no seu Linux do serviço de aplicações de instância através de SSH se ainda não tiver uma configuração de existir.
    ```
    mkdir -p /home/tomcat
    cp -a /usr/local/tomcat/conf /home/tomcat/conf
    ```

2. Adicionar um elemento de contexto no seu `server.xml` dentro do `<Server>` elemento.

    ```xml
    <Server>
    ...
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
    ...
    </Server>
    ```

3. Atualizar a sua aplicação `web.xml` para utilizar a origem de dados na sua aplicação.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Finalizar a configuração

Por fim, colocar os JARs de driver em Tomcat classpath e reinicie o serviço de aplicações.

1. Certifique-se de que os ficheiros de controlador JDBC estão disponíveis para o classloader Tomcat, colocando-os no `/home/tomcat/lib` diretório. (Criar este diretório se ainda não exista.) Para carregar esses arquivos à sua instância do serviço de aplicações, execute os seguintes passos:
    1. Na [Cloud Shell](https://shell.azure.com), instalar a extensão de aplicação Web:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Execute o seguinte comando da CLI para criar um túnel SSH do seu sistema local no serviço de aplicações:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Ligue à porta de túnel local com o cliente SFTP e carregar os ficheiros para o `/home/tomcat/lib` pasta.

    Em alternativa, pode utilizar um cliente de FTP para carregar o controlador JDBC. Siga estes [instruções para obter as suas credenciais FTP](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

2. Se tiver criado uma origem de dados ao nível do servidor, reinicie a aplicação do Linux do serviço de aplicações. Tomcat redefinirá `CATALINA_HOME` para `/home/tomcat/conf` e utilizar a configuração atualizada.

## <a name="configure-wildfly-server"></a>Configurar o servidor de WildFly

[Dimensionamento com o serviço de aplicações](#scale-with-app-service)
[configuração de servidor da aplicação de personalizar](#customize-application-server-configuration)
[módulos e dependências](#modules-and-dependencies)
[dados origens](#data-sources)
[os fornecedores de mensagens](#enable-messaging-providers)
[configurar a colocação em cache da gestão de sessão](#configure-session-management-caching)

### <a name="scale-with-app-service"></a>Dimensionamento com o serviço de aplicações

O servidor de aplicações de WildFly em execução no serviço de aplicações no Linux é executado no modo autónomo, não numa configuração de domínio. Ao aumentar horizontalmente o plano do serviço de aplicações, cada instância de WildFly está configurada como um servidor autónomo.

 Dimensionar vertical ou horizontalmente a sua aplicação com [Dimensionar regras](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md) e por [aumentar sua contagem de instâncias](../web-sites-scale.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="customize-application-server-configuration"></a>Personalizar a configuração do servidor de aplicação

Instâncias de aplicações Web são sem monitoração de estado, portanto, cada nova instância iniciada tem de ser configurada no arranque para suportar a configuração de Wildfly aplicação precisa.
Pode escrever um script de Bash para chamar a CLI WildFly para de arranque:

- Configurar origens de dados
- Configurar fornecedores de mensagens
- Adicione outros módulos e dependências para a configuração do servidor Wildfly.

 O script é executado quando Wildfly está em execução, mas antes de iniciar a aplicação. O script deve utilizar o [JBOSS CLI](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) chamado a partir de `/opt/jboss/wildfly/bin/jboss-cli.sh` para configurar o servidor de aplicações com qualquer configuração ou as alterações necessárias depois do servidor é iniciado.

Não utilize o modo interativo da CLI para configurar Wildfly. Em vez disso, pode fornecer um script de comandos para a CLI JBoss usando o `--file` comando, por exemplo:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Carregar o script de inicialização para `/home/site/deployments/tools` na sua instância do serviço de aplicações. Ver [este documento](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#userscope) para obter instruções sobre como obter as suas credenciais FTP.

Definir o **Script de inicialização** campo no portal do Azure para a localização do seu script de shell de inicialização, por exemplo `/home/site/deployments/tools/your-startup-script.sh`.

Fornecer [as definições da aplicação](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) na configuração da aplicação para passar as variáveis de ambiente para utilização no script. Definições da aplicação tenha cadeias de ligação e outros segredos necessários para configurar o aplicativo fora do controle de versão.

### <a name="modules-and-dependencies"></a>Módulos e dependências

Para instalar os módulos e suas dependências no caminho da classe Wildfly através da CLI JBoss, terá de criar os seguintes ficheiros no seu próprio diretório. Alguns módulos e dependências poderão ter uma configuração adicional, como JNDI de nomenclatura ou outra configuração de API específicas, para que esta lista é um conjunto mínimo de que vai precisar configurar uma dependência na maioria dos casos.

- Uma [descritor de módulo XML](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). Esse arquivo XML define o nome, atributos e as dependências do seu módulo. Isso [Module. XML de exemplo de ficheiro](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) define um módulo de Postgres, sua dependência JDBC do ficheiro JAR e outras dependências do módulo necessárias.
- Quaisquer dependências de ficheiro JAR necessárias para seu módulo.
- Um script com seus comandos do JBoss CLI para configurar o módulo de novo. Este ficheiro irá conter os comandos a serem executados pela CLI JBoss para configurar o servidor para utilizar a dependência. Para obter documentação sobre os comandos para adicionar módulos, origens de dados e provedores de mensagens, consulte [este documento](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- Um script de inicialização do Bash para chamar a CLI JBoss e executar o script no passo anterior. Este ficheiro será executado quando sua instância do serviço de aplicações é reiniciada ou quando novas instâncias são aprovisionadas durante um Escalamento horizontal. Este script de inicialização é onde pode realizar quaisquer outras configurações para a sua aplicação à medida que os comandos de JBoss são transmitidos para a CLI JBoss. No mínimo, este ficheiro pode ser um único comando para passar o script do comando CLI de JBoss para a CLI JBoss:

```bash
`/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli`
```

Assim que tiver os ficheiros e conteúdo para o seu módulo, siga os passos abaixo para adicionar o módulo para o servidor de aplicações Wildfly.

1. Os ficheiros para de FTP `/home/site/deployments/tools` na sua instância do serviço de aplicações. Veja este documento para obter instruções sobre como obter as suas credenciais FTP.
2. No painel de definições de aplicação do portal do Azure, defina o campo "Script de inicialização" para a localização do seu script de shell de inicialização, por exemplo `/home/site/deployments/tools/your-startup-script.sh` .
3. Reinicie a instância de serviço de aplicações ao premir o **reinicie** botão no **descrição geral** seção do Portal ou através da CLI do Azure.

### <a name="data-sources"></a>Origens de dados

Para configurar Wildfly para uma ligação de origem de dados, siga o mesmo processo descrito acima na seção instalar módulos e dependências. Pode seguir os mesmos passos para qualquer serviço de base de dados do Azure.

1. Baixe o JDBC driver para seu sabor de base de dados. Para sua comodidade, aqui estão os controladores para [Postgres](https://jdbc.postgresql.org/download.html) e [MySQL](https://dev.mysql.com/downloads/connector/j/). Descompacte o download para obter o ficheiro. JAR.
2. Siga o contorno de etapas em "Módulos e dependências" para criar e carregar o descritor de módulo XML, script da CLI de JBoss, script de inicialização e dependência. JAR do JDBC.

Obter mais informações sobre como configurar Wildfly com [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7) , [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource), e [base de dados SQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898) está disponível. Pode utilizar estas instruções personalizadas, juntamente com a abordagem generalizada acima para adicionar definições de origem de dados ao seu servidor.

### <a name="enable-messaging-providers"></a>Os fornecedores de mensagens

Para ativar Beans mensagem controlado por utilizar o Service Bus como o mecanismo de mensagens:

1. Utilize o [biblioteca de mensagens do Apache QPId JMS](https://qpid.apache.org/proton/index.html). Inclua esta dependência em sua pom. XML (ou outro arquivo de compilação) para a aplicação.

2. Crie [recursos do Service Bus](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp). Criar um espaço de nomes do Service bus do Azure e a fila dentro desse espaço de nomes e uma política de acesso partilhado com o envie e receba recursos.

3. Passar a chave de política de acesso partilhado para o seu código com codificação do URL da chave primária da sua política ou [utilize o SDK do Service Bus](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory).

4. Siga os passos descritos na seção instalar módulos e as dependências com o descritor XML do módulo,. JAR dependências, comandos da CLI de JBoss e script de inicialização para o fornecedor JMS. Além dos quatro arquivos, também terá de criar um arquivo XML que define o nome JNDI para a fila JMS e tópico. Ver [este repositório](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig) para ficheiros de configuração de referência.

### <a name="configure-session-management-caching"></a>Configurar a colocação em cache da gestão de sessão

Por predefinição, o serviço de aplicações no Linux irá utilizar cookies de afinidade de sessão para se certificar de que os pedidos de cliente com sessões existentes são encaminhados a mesma instância do seu aplicativo. Este comportamento predefinido não requer configurações, mas tem algumas limitações:

- Se uma instância de aplicação é reiniciada ou reduzida verticalmente, o estado de sessão do utilizador no servidor de aplicativos serão perdido.
- Se as aplicações têm definições de tempo limite da sessão de longa ou um número fixo de utilizadores, pode demorar algum tempo para dimensionado automaticamente novas instâncias para receber a carga, uma vez que apenas novas sessões serão encaminhados para as instâncias recentemente iniciadas.

Pode configurar Wildfly a usar um armazenamento de sessão externo, como [a Cache de Redis do Azure](/azure/azure-cache-for-redis/). Precisará [desativar a afinidade de instância existente do ARR](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) configuração para desativar a sessão com base no cookie encaminhamento e permitir que o arquivo de sessão Wildfly configurado operar sem interferência.

## <a name="docker-containers"></a>Contentores do Docker

Para utilizar o JDK de Zulu suportada pelo Azure em seus contentores, certifique-se obter e utilizar as imagens pré-criadas, conforme documentado do [suportado Azul Zulu Enterprise para a página de transferência do Azure](https://www.azul.com/downloads/azure-only/zulu/) ou utilizar o `Dockerfile` exemplos da [Repositório do GitHub de Java do Microsoft](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Declaração de suporte

### <a name="runtime-availability"></a>Disponibilidade de tempo de execução

Serviço de aplicações para Linux suporta dois runtimes de hospedagem gerenciada de aplicativos da web de Java:

- O [contentor servlet do Tomcat](https://tomcat.apache.org/) para a execução de aplicativos empacotados como arquivos mortos (WAR) da web. Versões suportadas são 8.5 e 9.0.
- Ambiente de tempo de execução Java SE para executar aplicações em pacote como arquivo de Java ficheiros (JAR). Versões suportadas são Java 8 e no 11.

### <a name="jdk-versions-and-maintenance"></a>Versões do JDK e manutenção

Suportados Java Development Kit (JDK do Azure) é [Zulu](https://www.azul.com/downloads/azure-only/zulu/) fornecido por meio [Azul Systems](https://www.azul.com/).

Atualizações de versão principal serão fornecidas através de novas opções de tempo de execução no serviço de aplicações do Azure para Linux. Atualizar para estas versões mais recentes do Java ao configurar a sua implementação do serviço de aplicações de clientes e são responsáveis por testar e garantir que a atualização importante atende às suas necessidades.

JDKs suportados sejam automaticamente corrigidos trimestralmente em Janeiro, Abril, Julho e Outubro de cada ano.

### <a name="security-updates"></a>Atualizações de segurança

Patches e correções para as vulnerabilidades de segurança importantes serão lançadas assim que estiverem disponíveis da Azul Systems. Uma vulnerabilidade de "maior" é definida por uma pontuação básica de 9.0 ou superior no [NIST vulnerabilidade de classificação sistema comum, versão 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Preterição e retirada

Se um runtime de Java suportada será extinto, os programadores do Azure com o tempo de execução afetado terá a chance um aviso de preterição, pelo menos, seis meses antes do tempo de execução foi descontinuado.

### <a name="local-development"></a>Desenvolvimento local

Os desenvolvedores podem transferir a produção edição de Azul Zulu Enterprise JDK para desenvolvimento local do [site de download do Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Suporte ao desenvolvimento

Suporte de produto para o [suportada pelo Azure JDK do Azul Zulu](https://www.azul.com/downloads/azure-only/zulu/) está disponível por meio de quando desenvolver para o Azure ou [do Azure Stack](https://azure.microsoft.com/overview/azure-stack/) com um [qualificado plano de suporte do Azure](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Suporte de tempo de execução

Os programadores podem [abra um problema](/azure/azure-supportability/how-to-create-azure-support-request) com o JDKs de Zulu da Azul através do suporte do Azure se tiverem um [plano de suporte qualificado](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Passos Seguintes

Visite o [do Azure para programadores de Java](/java/azure/) center para encontrar inícios rápidos do Azure, tutoriais e documentação de referência de Java.

Perguntas gerais sobre como utilizar o serviço de aplicações para Linux que não sejam específicas para o desenvolvimento de Java são respondidas no [FAQ de Linux do serviço de aplicações](app-service-linux-faq.md).