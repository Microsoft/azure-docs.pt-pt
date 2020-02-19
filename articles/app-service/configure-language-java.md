---
title: Configure aplicativos Do Windows Java
description: Saiba como configurar aplicações Java para executar nas instâncias Do Windows VM no Serviço de Aplicações Azure. Este artigo mostra as tarefas de configuração mais comuns.
keywords: serviço de aplicativos azure, web app, janelas, oss, java
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: f6f334ed6b84d4688849b6dfd8cb1f79f8db57bf
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443899"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Configure um aplicativo Windows Java para o Serviço de Aplicações Azure

O Azure App Service permite que os desenvolvedores de Java construam, implementem e dimensionem rapidamente as suas aplicações Web Tomcat num serviço totalmente gerido com base no Windows. Implemente aplicações com plugins Maven da linha de comando ou em editores como IntelliJ, Eclipse ou Visual Studio Code.

Este guia fornece conceitos e instruções fundamentais para os desenvolvedores java que utilizam no Serviço de Aplicações. Se nunca usou o Azure App Service, deve ler primeiro o [Java quickstart.](app-service-web-get-started-java.md) Perguntas gerais sobre a utilização do Serviço de Aplicações que não são específicas para o desenvolvimento de Java são respondidas no Serviço de [Aplicações Windows FAQ](faq-configuration-and-management.md).

## <a name="deploying-your-app"></a>Implementação da sua aplicação

Pode utilizar o [Plugin da Aplicação Web Azure para a Maven](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) implementar os seus ficheiros .war. A implantação com IDEs populares também é suportada com [O Kit de Ferramentas Azure para IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) ou [Azure Toolkit para Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

Caso contrário, o seu método de implantação dependerá do seu tipo de arquivo:

- Para implementar ficheiros de guerra para o Tomcat, utilize o ponto final `/api/wardeploy/` para publicar o seu ficheiro de arquivo. Para mais informações sobre esta API, consulte [esta documentação.](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file)

Não implante a sua .guerra usando FTP. A ferramenta FTP foi concebida para carregar scripts de arranque, dependências ou outros ficheiros de tempo de execução. Não é a escolha ideal para implementar aplicações web.

## <a name="logging-and-debugging-apps"></a>Aplicativos de registo e depuração

Relatórios de desempenho, visualizações de tráfego e check-ups de saúde estão disponíveis para cada aplicação através do portal Azure. Para mais informações, consulte a visão geral dos diagnósticos do Serviço de [Aplicações Azure](overview-diagnostics.md).

### <a name="stream-diagnostic-logs"></a>Transmitir registos de diagnóstico

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Para mais informações, consulte os [registos do Stream na Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Registo de aplicações

Ative o [registo de aplicações](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) através do portal Azure ou [do Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) para configurar o App Service para escrever a saída padrão da consola da sua aplicação e os fluxos de erros padrão da consola para o sistema de ficheiros local ou para o Armazenamento De Blob Azure. O registo na instância do sistema de ficheiros do Serviço de Aplicações local é desativado 12 horas após a sua configuração. Se necessitar de retenção mais longa, configure a aplicação para escrever saída para um recipiente de armazenamento Blob. Os registos das suas aplicações Java e Tomcat podem ser encontrados no */LogFiles/Application/diretório.*

Se a sua aplicação utilizar [logback](https://logback.qos.ch/) ou [Log4j](https://logging.apache.org/log4j) para rastreio, pode reencaminhar estes vestígios para revisão em Insights de Aplicação Azure utilizando as instruções de configuração de configuração de configuração de login em [Explore Java tracelogs in Application Insights](/azure/application-insights/app-insights-java-trace-logs).


## <a name="customization-and-tuning"></a>Personalização e afinação

O Azure App Service suporta a afinação e personalização da caixa através do portal Azure e clI. Reveja os seguintes artigos para a configuração de aplicações web não específicas de Java:

- [Configurar as definições de aplicativos](configure-common.md#configure-app-settings)
- [Configurar um domínio personalizado](app-service-web-tutorial-custom-domain.md)
- [Configure as ligações SSL](configure-ssl-bindings.md)
- [Adicione um CDN](../cdn/cdn-add-to-web-app.md)
- [Configure o site kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Definir opções de tempo de execução de Java

Para definir a memória atribuída ou outras opções de prazo de execução JVM, crie uma definição de [aplicação](configure-common.md#configure-app-settings) chamada `JAVA_OPTS` com as opções. O Serviço de Aplicações passa esta definição como uma variável ambiental para o tempo de funcionação de Java quando começa.

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

### <a name="pre-compile-jsp-files"></a>Pré-compilação de ficheiros JSP

Para melhorar o desempenho das aplicações Tomcat, pode compilar os seus ficheiros JSP antes de ser implementado no App Service. Pode utilizar o [plugin Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) fornecido pela Apache Sling, ou utilizar este [ficheiro de construção Ant](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Aplicações seguras

As aplicações Java em execução no App Service têm o mesmo conjunto de [boas práticas](/azure/security/security-paas-applications-using-app-services) de segurança que outras aplicações.

### <a name="authenticate-users-easy-auth"></a>Utilizadores autenticados (Easy Auth)

Configurar a autenticação da aplicação no portal Azure com a opção **Autenticação e Autorização.** A partir daí, pode ativar a autenticação utilizando o Diretório Ativo do Azure ou logins sociais como facebook, Google ou GitHub. A configuração do portal Azure só funciona quando configurar um único fornecedor de autenticação. Para mais informações, consulte [a configuração da sua app App Service para utilizar o login do Azure Ative Directory](configure-authentication-provider-aad.md) e os artigos relacionados para outros fornecedores de identidade. Se precisar de ativar vários fornecedores de sessão, siga as instruções no artigo de autenticação do Serviço de [Aplicações personalizado.](app-service-authentication-how-to.md)

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

### <a name="configure-tlsssl"></a>Configurar TLS/SSL

Siga as instruções no [nome 'Secure' um DNS personalizado com uma ligação SSL no Serviço de Aplicações Azure](configure-ssl-bindings.md) para fazer o upload de um certificado SSL existente e ligá-lo ao nome de domínio da sua aplicação. Por defeito, a sua aplicação continuará a permitir que as ligações HTTP sigam os passos específicos do tutorial para impor sSL e TLS.

### <a name="use-keyvault-references"></a>Utilizar referências keyVault

[O Azure KeyVault](../key-vault/key-vault-overview.md) fornece uma gestão secreta centralizada com políticas de acesso e histórico de auditoria. Pode armazenar segredos (como palavras-passe ou cordas de ligação) no KeyVault e aceder a estes segredos na sua aplicação através de variáveis ambientais.

Em primeiro lugar, siga as instruções para [conceder o acesso da sua aplicação ao Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) e fazer uma referência ao [KeyVault ao seu segredo numa Definição](app-service-key-vault-references.md#reference-syntax)de Aplicação . Pode validar que a referência resolve o segredo imprimindo a variável ambiental ao aceder remotamente ao terminal do Serviço de Aplicações.

Para injetar estes segredos no seu ficheiro de configuração Spring ou Tomcat, utilize sintaxe de injeção variável ambiente (`${MY_ENV_VAR}`). Para os ficheiros de configuração da Mola, consulte esta documentação sobre [configurações externas](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).


## <a name="configure-apm-platforms"></a>Configure plataformas APM

Esta secção mostra como ligar as aplicações java implantadas no Serviço de Aplicações Azure em Linux com as plataformas de monitorização de desempenho da aplicação NewRelic e AppDynamics (APM).

### <a name="configure-new-relic"></a>Configurar nova relíquia

1. Criar uma conta New Relic no [NewRelic.com](https://newrelic.com/signup)
2. Download the Java agent from NewRelic, it will have a file name similar to *newrelic-java-x.x.x.zip*.
3. Copie a chave da sua licença, terá de a configurar para configurar o agente mais tarde.
4. Utilize a [consola Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) para criar um novo diretório */home/site/wwwroot/apm*.
5. Faça upload dos ficheiros do agente New Relic Java desembalados num diretório em */home/site/wwwroot/apm*. Os ficheiros do seu agente devem estar em */home/site/wwwroot/apm/newrelic*.
6. Modifique o ficheiro YAML em */home/site/wwwroot/apm/newrelic/newrelic.yml* e substitua o valor da licença de proprietário por conta própria.
7. No portal Azure, navegue para a sua aplicação no Serviço de Aplicações e crie uma nova Definição de Aplicações.
    - Se a sua aplicação estiver a utilizar **o Java SE,** crie uma variável ambiental chamada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se estiver a usar **o Tomcat,** crie uma variável ambiental chamada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.

### <a name="configure-appdynamics"></a>Configure AppDynamics

1. Criar uma conta AppDynamics em [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Descarregue o agente Java a partir do website appDynamics, o nome do ficheiro será semelhante ao *AppServerAgent-x.x.x.xxxxx.zip*
3. Utilize a [consola Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) para criar um novo diretório */home/site/wwwroot/apm*.
4. Faça upload dos ficheiros do agente Java para um diretório em */home/site/wwwroot/apm*. Os ficheiros do seu agente devem estar em */home/site/wwwroot/apm/appdynamics*.
5. No portal Azure, navegue para a sua aplicação no Serviço de Aplicações e crie uma nova Definição de Aplicações.
    - Se estiver a usar **o Java SE,** crie uma variável ambiental chamada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` onde `<app-name>` é o seu nome de Serviço de Aplicações.
    - Se estiver a usar **o Tomcat,** crie uma variável ambiental chamada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` onde `<app-name>` é o seu nome de Serviço de Aplicações.

>  Se já tiver uma variável ambiental para `JAVA_OPTS` ou `CATALINA_OPTS`, apreenda a opção `-javaagent:/...` até ao fim do valor atual.

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

#### <a name="finalize-configuration"></a>Finalizar a configuração

Finalmente, colocaremos os JARs do condutor na classe Tomcat e reiniciaremos o seu Serviço de Aplicações. Certifique-se de que os ficheiros do condutor JDBC estão disponíveis para o classloader Tomcat colocando-os no diretório */casa/tomcat/lib.* (Crie este diretório se já não existir.) Para fazer o upload destes ficheiros para a sua instância do Serviço de Aplicações, execute os seguintes passos:

1. Na [Cloud Shell,](https://shell.azure.com)instale a extensão webapp:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Executar o seguinte comando CLI para criar um túnel SSH do seu sistema local para o Serviço de Aplicações:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Ligue-se à porta de túneis local com o seu cliente SFTP e faça o upload dos ficheiros para a pasta */home/tomcat/lib.*

Em alternativa, pode utilizar um cliente FTP para carregar o condutor da JDBC. Siga estas [instruções para obter as suas credenciais FTP](deploy-configure-credentials.md).

## <a name="configuring-tomcat"></a>Configurar tomcat

Para editar os `server.xml` ou outros ficheiros de configuração da Tomcat, tome primeiro uma nota da sua versão principal tomcat no portal.

1. Encontre o diretório da casa tomcat para a sua versão executando o comando `env`. Procure a variável ambiental que começa com `AZURE_TOMCAT`e corresponda à sua versão principal. Por exemplo, `AZURE_TOMCAT85_HOME` aponta para o diretório Tomcat para o Tomcat 8.5.
1. Depois de ter identificado o diretório caseiro tomcat para a sua versão, copie o diretório de configuração para `D:\home`. Por exemplo, se `AZURE_TOMCAT85_HOME` tivesse um valor de `D:\Program Files (x86)\apache-tomcat-8.5.37`, o novo caminho do diretório copiado seria `D:\home\apache-tomcat-8.5.37`.

Finalmente, reinicie o seu Serviço de Aplicações. Os seus destacamentos devem ir para `D:\home\site\wwwroot\webapps` como antes.

## <a name="java-runtime-statement-of-support"></a>Declaração de apoio em tempo de execução de Java

### <a name="jdk-versions-and-maintenance"></a>Versões jdk e manutenção

O Kit de Desenvolvimento Java (JDK) suportado pela Azure é [zulu](https://www.azul.com/downloads/azure-only/zulu/) fornecido através da [Azul Systems.](https://www.azul.com/)

As principais atualizações de versão serão fornecidas através de novas opções de tempo de execução no Serviço de Aplicações Azure para Windows. Os clientes atualizam estas versões mais recentes da Java configurando a sua implementação do Serviço de Aplicações e são responsáveis por testar e garantir que a grande atualização satisfaz as suas necessidades.

Os JDKs suportados são automaticamente corrigidos trimestralmente em janeiro, abril, julho e outubro de cada ano. Para mais informações sobre Java no Azure, consulte este documento de [suporte.](https://docs.microsoft.com/azure/java/jdk/)

### <a name="security-updates"></a>Atualizações de segurança

Patches e correções para grandes vulnerabilidades de segurança serão lançados assim que ficarem disponíveis na Azul Systems. Uma vulnerabilidade "importante" é definida por uma pontuação base de 9.0 ou superior no Sistema de Pontuação de [Vulnerabilidade Comum NIST, versão 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Depreciação e reforma

Se um tempo de execução apoiado em Java for retirado, os desenvolvedores da Azure que utilizem o tempo de execução afetado receberão um aviso de depreciação pelo menos seis meses antes do tempo de execução ser retirado.

### <a name="local-development"></a>Desenvolvimento local

Os desenvolvedores podem baixar a Edição de Produção da Blue Zulu Enterprise JDK para desenvolvimento local a partir do [site de descarregamento da Azul.](https://www.azul.com/downloads/azure-only/zulu/)

### <a name="development-support"></a>Apoio ao desenvolvimento

O suporte ao produto para o [Azul Zulu JDK suportado](https://www.azul.com/downloads/azure-only/zulu/) pelo Azure está disponível através da Microsoft no desenvolvimento para Azure ou [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) com um plano de suporte [azure qualificado.](https://azure.microsoft.com/support/plans/)

### <a name="runtime-support"></a>Suporte de tempo de execução

Os desenvolvedores podem [abrir um problema](/azure/azure-portal/supportability/how-to-create-azure-support-request) com os JDKs Azul Zulu através do Suporte Azure se tiverem um plano de suporte [qualificado.](https://azure.microsoft.com/support/plans/)

## <a name="next-steps"></a>Passos seguintes

Este tópico fornece a declaração de suporte java Runtime para o Serviço de Aplicações Azure no Windows.

- Para saber mais sobre hospedagem de aplicações web com o Azure App Service consulte a visão geral do Serviço de [Aplicações](overview.md).
- Para obter informações sobre Java sobre o desenvolvimento do Azure consulte [Azure para Java Dev Center](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
