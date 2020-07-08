---
title: Configurar aplicativos Windows Java
description: Saiba como configurar aplicações Java para executar nas instâncias Do Windows VM no Azure App Service. Este artigo mostra as tarefas de configuração mais comuns.
keywords: serviço de aplicativos azure, web app, janelas, oss, java
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: 1e42096e7ab950e5d8046ec6140c01b24643cb87
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82891474"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Configure uma aplicação Windows Java para o Azure App Service

O Azure App Service permite que os desenvolvedores de Java construam, implementem e escalam rapidamente as suas aplicações web Tomcat num serviço totalmente gerido baseado no Windows. Implementar aplicações com plugins Maven da linha de comando ou em editores como IntelliJ, Eclipse ou Visual Studio Code.

Este guia fornece conceitos e instruções fundamentais para os desenvolvedores de Java que usam no Serviço de Aplicações. Se nunca usou o Azure App Service, deve ler primeiro o [quickstart java.](app-service-web-get-started-java.md) As questões gerais sobre a utilização do Serviço de Aplicações que não são específicas para o desenvolvimento de Java são respondidas no [Serviço de Aplicações Windows FAQ](faq-configuration-and-management.md).

## <a name="deploying-your-app"></a>Implementação da sua app

Pode utilizar [o Azure Web App Plugin para a Maven](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) para implementar os seus ficheiros .war. A implementação com iDEs populares também é suportada com [Azure Toolkit para IntelliJ](/azure/developer/java/toolkit-for-intellij/) ou [Azure Toolkit para Eclipse](/azure/developer/java/toolkit-for-eclipse).

Caso contrário, o seu método de implantação dependerá do seu tipo de arquivo:

- Para implementar ficheiros de .war para Tomcat, use o `/api/wardeploy/` ponto final para registar o seu ficheiro de arquivo. Para mais informações sobre esta API, consulte [esta documentação.](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file)
- Para implementar ficheiros .jar para Java SE, utilize o `/api/zipdeploy/` ponto final do site Kudu. Para mais informações sobre esta API, consulte [esta documentação.](https://docs.microsoft.com/azure/app-service/deploy-zip#rest)

Não desloque a sua .war utilizando FTP. A ferramenta FTP foi concebida para carregar scripts de arranque, dependências ou outros ficheiros de tempo de execução. Não é a escolha ideal para implementar aplicações web.

## <a name="logging-and-debugging-apps"></a>Aplicativos de registo e depuragem

Relatórios de desempenho, visualizações de tráfego e check-ups de saúde estão disponíveis para cada aplicação através do portal Azure. Para mais informações, consulte [a visão geral do Serviço de Aplicações Azure](overview-diagnostics.md).

### <a name="use-flight-recorder"></a>Use gravador de voo

Todos os tempos de execução java no Serviço de Aplicações utilizando os JVMs Azuis vêm com o Gravador de Voo Zulu. Você pode usar isto para gravar eventos de nível JVM, sistema e java para monitorizar o comportamento e problemas de resolução de problemas nas suas aplicações Java.

Para fazer uma gravação cronometrada, necessitará do PID (ID de processo) da aplicação Java. Para encontrar o PID, abra um navegador para o site SCM da sua aplicação web em https://<o seu nome de site>.scm.azurewebsites.net/ProcessExplorer/. Esta página mostra os processos de execução na sua aplicação web. Encontre o processo denominado "java" na tabela e copie o PID correspondente (ID de processo).

Em seguida, abra a **Consola Debug** na barra de ferramentas superior do site SCM e executar o seguinte comando. `<pid>`Substitua-o pelo ID de processo que copiou anteriormente. Este comando iniciará uma gravação de 30 segundos da sua aplicação Java e gerará um ficheiro nomeado `timed_recording_example.jfr` no `D:\home` diretório.

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

Para mais informações, consulte a Referência de [Comando Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

#### <a name="analyze-jfr-files"></a>Analisar `.jfr` ficheiros

Utilize [o FTPS](deploy-ftp.md) para descarregar o seu ficheiro JFR para a sua máquina local. Para analisar o ficheiro JFR, descarregue e instale [o Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/). Para obter instruções sobre o Controlo da Missão Zulu, consulte a [documentação azul](https://docs.azul.com/zmc/) e as [instruções de instalação.](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)

### <a name="stream-diagnostic-logs"></a>Transmitir registos de diagnóstico em fluxo

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Para obter mais informações, consulte [os registos de streaming em Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Registo de aplicativos

Habilitar o [registo de aplicações](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) através do portal Azure ou [do Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) para configurar o Serviço de Aplicações para escrever a saída padrão da consola da sua aplicação e fluxos de erro de consola padrão para o sistema de ficheiros local ou para o Armazenamento Azure Blob. O registo na instância do sistema de ficheiros do Serviço de Aplicações local é desativado 12 horas após a sua configuração. Se necessitar de uma retenção mais longa, configuure a aplicação para escrever a saída num recipiente de armazenamento Blob. Os registos de aplicações Java e Tomcat podem ser encontrados no *diretório /LogFiles/Application/.*

Se a sua aplicação utilizar [Logback](https://logback.qos.ch/) ou [Log4j](https://logging.apache.org/log4j) para rastreio, pode encaminhar estes vestígios para revisão em Azure Application Insights utilizando as instruções de configuração de quadro de registo em [Explore Java trace logs in Application Insights](/azure/application-insights/app-insights-java-trace-logs).


## <a name="customization-and-tuning"></a>Personalização e afinação

O Azure App Service suporta a afinação e personalização da caixa através do portal Azure e CLI. Reveja os seguintes artigos para configuração de aplicações web não específicas de Java:

- [Configurar as definições da aplicação](configure-common.md#configure-app-settings)
- [Configurar um domínio personalizado](app-service-web-tutorial-custom-domain.md)
- [Configurar encadernações TLS](configure-ssl-bindings.md)
- [Adicione um CDN](../cdn/cdn-add-to-web-app.md)
- [Configure o site kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Definir opções de tempo de execução de Java

Para definir a memória atribuída ou outras opções de tempo de execução JVM, crie uma [definição de aplicação](configure-common.md#configure-app-settings) nomeada `JAVA_OPTS` com as opções. O Serviço de Aplicações passa esta definição como uma variável ambiental para o tempo de execução java quando começa.

No portal Azure, em **Definições de Aplicação** para a aplicação web, crie uma nova definição de aplicação com o nome `JAVA_OPTS` que inclui as definições adicionais, tais como `-Xms512m -Xmx1204m` .

Para configurar a definição da aplicação a partir do plugin Maven, adicione etiquetas de definição/valor na secção de plugins Azure. O exemplo a seguir define um tamanho mínimo e máximo de pilha de Java específico:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Os desenvolvedores que executam uma única aplicação com uma ranhura de implementação no seu plano de Serviço de Aplicações podem utilizar as seguintes opções:

- Casos B1 e S1:`-Xms1024m -Xmx1024m`
- Casos B2 e S2:`-Xms3072m -Xmx3072m`
- Casos B3 e S3:`-Xms6144m -Xmx6144m`

Ao sintonizar as definições da pilha de aplicações, reveja os detalhes do seu plano de App Service e tenha em conta várias aplicações e slots de implementação para encontrar a alocação ideal de memória.

### <a name="turn-on-web-sockets"></a>Ligue as tomadas web

Ligue o suporte para tomadas web no portal Azure nas **definições de Aplicação** para a aplicação. Terá de reiniciar a aplicação para a definição para fazer efeito.

Ligue o suporte da tomada web utilizando o CLI Azure com o seguinte comando:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Em seguida, reinicie a sua aplicação:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Definir codificação de caracteres predefinidos

No portal Azure, em **Definições de Aplicação** para a aplicação web, crie uma nova configuração de aplicação com `JAVA_OPTS` o nome de valor `-Dfile.encoding=UTF-8` .

Em alternativa, pode configurar a definição da aplicação utilizando o plugin Maven do Serviço de Aplicações. Adicione o nome de definição e as etiquetas de valor na configuração do plugin:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="pre-compile-jsp-files"></a>Pré-Compilar ficheiros JSP

Para melhorar o desempenho das aplicações Tomcat, pode compilar os seus ficheiros JSP antes de ser implantado no Serviço de Aplicações. Pode utilizar o [plugin Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) fornecido pela Apache Sling ou utilizar este [ficheiro de construção de Formigas](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Aplicações seguras

As aplicações java em execução no Serviço de Aplicações têm o mesmo conjunto de [boas práticas](/azure/security/security-paas-applications-using-app-services) de segurança que outras aplicações.

### <a name="authenticate-users-easy-auth"></a>Autenticar utilizadores (Easy Auth)

Configurar a autenticação da aplicação no portal Azure com a opção **De autenticação e Autorização.** A partir daí, pode ativar a autenticação utilizando o Azure Ative Directory ou logins sociais como Facebook, Google ou GitHub. A configuração do portal Azure só funciona quando configurar um único fornecedor de autenticação. Para obter mais informações, consulte a sua app App Service para utilizar o [login do Azure Ative Directory](configure-authentication-provider-aad.md) e os artigos relacionados para outros fornecedores de identidade. Se precisar de ativar vários fornecedores de inscrição, siga as instruções no artigo [de autenticação do Serviço de Aplicações personalizado.](app-service-authentication-how-to.md)

#### <a name="tomcat"></a>Tomcat

A sua aplicação Tomcat pode aceder diretamente às reclamações do utilizador a partir do servlet, lançando o objeto principal para um objeto de Mapa. O objeto mapa irá mapear cada tipo de reivindicação para uma coleção das reclamações para esse tipo. No código abaixo, `request` é um exemplo de `HttpServletRequest` .

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Agora pode inspecionar o `Map` objeto para obter qualquer reclamação específica. Por exemplo, o seguinte código corta-se através de todos os tipos de reclamação e imprime o conteúdo de cada coleção.

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

Para assinar os utilizadores, use o `/.auth/ext/logout` caminho. Para realizar outras ações, consulte a documentação sobre [a autenticação e utilização da Autorização do Serviço de Aplicações.](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to) Há também documentação oficial sobre a interface Tomcat [HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) e seus métodos. Os seguintes métodos de servidão também são hidratados com base na configuração do seu Serviço de Aplicações:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Para desativar esta funcionalidade, crie uma Definição de Aplicação `WEBSITE_AUTH_SKIP_PRINCIPAL` com o valor de `1` . Para desativar todos os filtros de servidão adicionados pelo App Service, crie uma definição `WEBSITE_SKIP_FILTERS` com o valor de `1` .

### <a name="configure-tlsssl"></a>Configurar TLS/SSL

Siga as instruções no [Secure um nome DNS personalizado com uma ligação TLS no Serviço de Aplicações Azure](configure-ssl-bindings.md) para carregar um certificado TLS/SSL existente e ligá-lo ao nome de domínio da sua aplicação. Por predefinição, a sua aplicação continuará a permitir que as ligações HTTP sigam os passos específicos do tutorial para impor SSL e TLS.

### <a name="use-keyvault-references"></a>Utilizar referências KeyVault

[A Azure KeyVault](../key-vault/general/overview.md) fornece uma gestão secreta centralizada com políticas de acesso e histórico de auditoria. Pode armazenar segredos (como palavras-passe ou cadeias de ligação) no KeyVault e aceder a estes segredos na sua aplicação através de variáveis ambientais.

Em primeiro lugar, siga as instruções para [conceder acesso à sua aplicação ao Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) e fazer uma referência [KeyVault ao seu segredo numa Definição de Aplicação.](app-service-key-vault-references.md#reference-syntax) Pode validar que a referência se resolve ao segredo imprimindo a variável ambiente ao aceder remotamente ao terminal de Serviço de Aplicações.

Para injetar estes segredos no seu ficheiro de configuração primavera ou Tomcat, utilize a sintaxe de injeção variável do ambiente `${MY_ENV_VAR}` (). Para ficheiros de configuração da primavera, consulte esta documentação sobre [configurações externas](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).


## <a name="configure-apm-platforms"></a>Configurar plataformas APM

Esta secção mostra como ligar as aplicações java implementadas no Azure App Service em Linux com as plataformas de monitorização do desempenho da aplicação NewRelic e AppDynamics (APM).

### <a name="configure-new-relic"></a>Configure nova relíquia

1. Criar uma nova conta relíquia na [NewRelic.com](https://newrelic.com/signup)
2. Descarregue o agente Java da NewRelic, terá um nome de ficheiro semelhante ao *newrelic-java-x.x.x.zip*.
3. Copie a chave da sua licença e vai precisar dela para configurar o agente mais tarde.
4. Utilize a [consola Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) para criar um novo diretório */home/site/wwwroot/apm*.
5. Faça o upload dos ficheiros de novo agente java desembalado para um diretório em */home/site/wwwroot/apm*. Os ficheiros do seu agente devem estar em */home/site/wwwroot/apm/newrelic*.
6. Modifique o ficheiro YAML em */home/site/wwwroot/apm/newrelic/newrelic.yml* e substitua o valor da licença de espaço reservado pela sua própria chave de licença.
7. No portal Azure, navegue pela sua aplicação no Serviço de Aplicações e crie uma nova Definição de Aplicação.
    - Se a sua aplicação estiver a utilizar **a Java SE,** crie uma variável ambiental com `JAVA_OPTS` o `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` valor.
    - Se estiver a utilizar o **Tomcat,** crie uma variável ambiental com `CATALINA_OPTS` o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

### <a name="configure-appdynamics"></a>Configurar a AppDynamics

1. Criar uma conta AppDynamics na [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Descarregue o agente Java a partir do site appDynamics, o nome do ficheiro será semelhante ao *AppServerAgent-x.x.x.xxxxx.zip*
3. Utilize a [consola Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) para criar um novo diretório */home/site/wwwroot/apm*.
4. Faça o upload dos ficheiros do agente Java para um diretório em */home/site/wwwroot/apm*. Os ficheiros do seu agente devem estar em */home/site/wwwroot/apm/appdynamics*.
5. No portal Azure, navegue pela sua aplicação no Serviço de Aplicações e crie uma nova Definição de Aplicação.
    - Se estiver a utilizar **a Java SE,** crie uma variável ambiental com `JAVA_OPTS` o valor onde está o nome do Serviço de `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` Aplicações.
    - Se estiver a utilizar o **Tomcat,** crie uma variável ambiental com `CATALINA_OPTS` o valor onde está o nome do Serviço de `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` Aplicações.

>  Se já tiver uma variável ambiental para `JAVA_OPTS` `CATALINA_OPTS` ou, anexar `-javaagent:/...` a opção até ao fim do valor atual.

## <a name="data-sources"></a>Origens de dados

### <a name="tomcat"></a>Tomcat

Estas instruções aplicam-se a todas as ligações de base de dados. Terá de preencher os espaços reservados com o nome da classe de condutor e o ficheiro JAR da sua base de dados escolhida. Fornecido é uma tabela com nomes de classe e downloads de motorista para bases de dados comuns.

| Base de Dados   | Nome da classe do motorista                             | Condutor JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Transferência](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Download](https://dev.mysql.com/downloads/connector/j/) (Selecione "Plataforma Independente") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Transferência](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Para configurar o Tomcat para utilizar a Conectividade da Base de Dados de Java (JDBC) ou a API de Persistência de Java (JPA), primeiro personalize a `CATALINA_OPTS` variável ambiental que é lida pela Tomcat no arranque. Defina estes valores através de uma definição de aplicação no [plugin Maven do Serviço de Aplicações:](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Ou definir as variáveis **Configuration**de ambiente na página  >  **Configurações de Configuração** do portal Azure.

Em seguida, determine se a fonte de dados deve estar disponível para uma aplicação ou para todas as aplicações em execução na servidão Tomcat.

#### <a name="application-level-data-sources"></a>Fontes de dados ao nível da aplicação

1. Crie um ficheiro *context.xml* no *meta-INF/diretório* do seu projeto. Crie o *meta-INF/diretório* se não existir.

2. Em *context.xml*, adicione um `Context` elemento para ligar a fonte de dados a um endereço JNDI. Substitua o `driverClassName` espaço reservado pelo nome da classe do seu condutor na tabela acima.

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

3. Atualize as *web.xml* da sua aplicação para utilizar a fonte de dados na sua aplicação.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Finalizar a configuração

Finalmente, colocaremos os JARs do condutor no apêndice Tomcat e reiniciaremos o seu Serviço de Aplicações. Certifique-se de que os ficheiros do controlador JDBC estão disponíveis para o carregador de classe Tomcat colocando-os no diretório */home/tomcat/lib.* (Criar este diretório se já não existir.) Para fazer o upload destes ficheiros para a sua instância do Serviço de Aplicações, execute os seguintes passos:

1. No [Cloud Shell](https://shell.azure.com), instale a extensão do webapp:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Executar o seguinte comando CLI para criar um túnel SSH do seu sistema local para o Serviço de Aplicações:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Ligue-se à porta de túneis local com o seu cliente SFTP e faça o upload dos ficheiros para a pasta */home/tomcat/lib.*

Em alternativa, pode utilizar um cliente FTP para carregar o controlador JDBC. Siga estas [instruções para obter as suas credenciais FTP](deploy-configure-credentials.md).

## <a name="configuring-tomcat"></a>Configurar Tomcat

Para editar os ficheiros de configuração do Tomcat `server.xml` ou outros, tome primeiro nota da sua versão principal do Tomcat no portal.

1. Encontre o diretório de casa tomcat para a sua versão executando o `env` comando. Procure a variável ambiental que começa `AZURE_TOMCAT` e corresponde à sua versão principal. Por exemplo, `AZURE_TOMCAT85_HOME` aponta para o diretório Tomcat para Tomcat 8.5.
1. Depois de ter identificado o diretório de casa tomcat para a sua versão, copie o diretório de configuração para `D:\home` . Por exemplo, se `AZURE_TOMCAT85_HOME` tivesse um valor `D:\Program Files (x86)\apache-tomcat-8.5.37` de, o novo caminho do diretório copiado seria `D:\home\apache-tomcat-8.5.37` .

Finalmente, reinicie o seu Serviço de Aplicações. Os vossos destacamentos devem ir para `D:\home\site\wwwroot\webapps` tal como antes.

## <a name="configure-java-se"></a>Configure Java SE

Ao executar um . A aplicação JAR no Java SE no `server.port` Windows, é passada como uma opção de linha de comando à medida que a sua aplicação começa. Pode resolver manualmente a porta HTTP a partir da variável ambiente, `HTTP_PLATFORM_PORT` . O valor desta variável ambiente será a porta HTTP que a sua aplicação deve ouvir. 

## <a name="java-runtime-statement-of-support"></a>Declaração de apoio em tempo de execução de Java

### <a name="jdk-versions-and-maintenance"></a>Versões e manutenção JDK

O Kit de Desenvolvimento java suportado pela Azure (JDK) é [o Zulu](https://www.azul.com/downloads/azure-only/zulu/) fornecido através [da Azul Systems.](https://www.azul.com/)

As principais atualizações da versão serão fornecidas através de novas opções de tempo de execução no Azure App Service para windows. Os clientes atualizam estas versões mais recentes da Java configurando a sua implementação do Serviço de Aplicações e são responsáveis por testar e garantir que a grande atualização atende às suas necessidades.

Os JDKs suportados são automaticamente corrigidos trimestralmente em janeiro, abril, julho e outubro de cada ano. Para mais informações sobre Java on Azure, consulte [este documento de suporte](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support).

### <a name="security-updates"></a>Atualizações de segurança

Patches e correções para grandes vulnerabilidades de segurança serão lançados assim que forem disponibilizados pela Azul Systems. Uma vulnerabilidade "importante" é definida por uma pontuação base de 9.0 ou superior no [Sistema de Pontuação Comum de Vulnerabilidade NIST, versão 2](https://nvd.nist.gov/cvss.cfm).

Tomcat 8.0 chegou ao [Fim da Vida (EOL) a 30 de setembro de 2018](https://tomcat.apache.org/tomcat-80-eol.html). Embora o tempo de funcionamento ainda seja avialável no Azure App Service, o Azure não aplicará atualizações de segurança ao Tomcat 8.0. Se possível, migrar as suas aplicações para Tomcat 8.5 ou 9.0. Tanto o Tomcat 8.5 como o 9.0 estão disponíveis no Azure App Service. Consulte o site oficial do [Tomcat](https://tomcat.apache.org/whichversion.html) para mais informações. 

### <a name="deprecation-and-retirement"></a>Depreciação e reforma

Se um tempo de execução java suportado for retirado, os desenvolvedores Azure que usam o tempo de execução afetado receberão um aviso de depreciação pelo menos seis meses antes do tempo de execução ser retirado.

### <a name="local-development"></a>Desenvolvimento local

Os desenvolvedores podem descarregar a Edição de Produção da Azul Zulu Enterprise JDK para o desenvolvimento local a partir do [site de descarregamento da Azul.](https://www.azul.com/downloads/azure-only/zulu/)

### <a name="development-support"></a>Apoio ao desenvolvimento

O suporte do produto para o [Azul Zulu JDK suportado pelo Azure](https://www.azul.com/downloads/azure-only/zulu/) está disponível através da Microsoft quando se desenvolve para Azure ou [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) com um [plano de suporte Azure qualificado.](https://azure.microsoft.com/support/plans/)

### <a name="runtime-support"></a>Suporte ao tempo de execução

Os desenvolvedores podem [abrir um problema](/azure/azure-portal/supportability/how-to-create-azure-support-request) com os JDKs Azul Zulu através do Suporte Azure se tiverem um plano de apoio [qualificado.](https://azure.microsoft.com/support/plans/)

## <a name="next-steps"></a>Próximos passos

Este tópico fornece a declaração de suporte do Java Runtime para o Serviço de Aplicações Azure no Windows.

- Para saber mais sobre hospedagem de aplicações web com o Azure App Service consulte a [visão geral do Serviço de Aplicações.](overview.md)
- Para obter informações sobre o desenvolvimento de Java on Azure consulte [Azure para o Java Dev Center](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
