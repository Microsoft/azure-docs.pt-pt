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
ms.custom: seodec18, devx-track-java
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 5d94da91428da2270e0f690df4dcd43ae43d8597
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961657"
---
# <a name="configure-a-java-app-for-azure-app-service"></a>Configure um aplicativo Java para o Azure App Service

::: zone pivot="platform-windows"  

O Azure App Service permite que os desenvolvedores de Java construam, implementem e escalam rapidamente as suas aplicações web Tomcat num serviço totalmente gerido baseado no Windows. Implementar aplicações com plugins Maven da linha de comando ou em editores como IntelliJ, Eclipse ou Visual Studio Code.

Este guia fornece conceitos e instruções fundamentais para os desenvolvedores de Java que usam no Serviço de Aplicações. Se nunca usou o Azure App Service, deve ler primeiro o [quickstart java.](quickstart-java.md) As questões gerais sobre a utilização do Serviço de Aplicações que não são específicas para o desenvolvimento de Java são respondidas no [Serviço de Aplicações Windows FAQ](faq-configuration-and-management.md).

## <a name="deploying-your-app"></a>Implementação da sua app

Pode utilizar [o Azure Web App Plugin para a Maven](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) para implementar os seus ficheiros .war. A implementação com iDEs populares também é suportada com [Azure Toolkit para IntelliJ](/azure/developer/java/toolkit-for-intellij/) ou [Azure Toolkit para Eclipse](/azure/developer/java/toolkit-for-eclipse).

Caso contrário, o seu método de implantação dependerá do seu tipo de arquivo:

- Para implementar ficheiros de .war para Tomcat, use o `/api/wardeploy/` ponto final para registar o seu ficheiro de arquivo. Para mais informações sobre esta API, consulte [esta documentação.](./deploy-zip.md#deploy-war-file)
- Para implementar ficheiros .jar para Java SE, utilize o `/api/zipdeploy/` ponto final do site Kudu. Para mais informações sobre esta API, consulte [esta documentação.](./deploy-zip.md#rest)

Não desloque o seu .war ou .jar utilizando FTP. A ferramenta FTP foi concebida para carregar scripts de arranque, dependências ou outros ficheiros de tempo de execução. Não é a escolha ideal para implementar aplicações web.

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

Utilize [o FTPS](deploy-ftp.md) para descarregar o seu ficheiro JFR para a sua máquina local. Para analisar o ficheiro JFR, descarregue e instale [o Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/). Para obter instruções sobre o Controlo da Missão Zulu, consulte a [documentação azul](https://docs.azul.com/zmc/) e as [instruções de instalação.](/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)

### <a name="stream-diagnostic-logs"></a>Transmitir registos de diagnóstico em fluxo

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Para obter mais informações, consulte [os registos de streaming em Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Registo de aplicativos

Habilitar o [registo de aplicações](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) através do portal Azure ou [do Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) para configurar o Serviço de Aplicações para escrever a saída padrão da consola da sua aplicação e fluxos de erro de consola padrão para o sistema de ficheiros local ou para o Armazenamento Azure Blob. O registo na instância do sistema de ficheiros do Serviço de Aplicações local é desativado 12 horas após a sua configuração. Se necessitar de uma retenção mais longa, configuure a aplicação para escrever a saída num recipiente de armazenamento Blob. Os registos de aplicações Java e Tomcat podem ser encontrados no *diretório /LogFiles/Application/.*

Se a sua aplicação utilizar [Logback](https://logback.qos.ch/) ou [Log4j](https://logging.apache.org/log4j) para rastreio, pode encaminhar estes vestígios para revisão em Azure Application Insights utilizando as instruções de configuração de quadro de registo em [Explore Java trace logs in Application Insights](../azure-monitor/app/java-trace-logs.md).


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

- Casos B1 e S1: `-Xms1024m -Xmx1024m`
- Casos B2 e S2: `-Xms3072m -Xmx3072m`
- Casos B3 e S3: `-Xms6144m -Xmx6144m`

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

As aplicações java em execução no Serviço de Aplicações têm o mesmo conjunto de [boas práticas](../security/fundamentals/paas-applications-using-app-services.md) de segurança que outras aplicações.

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

Para assinar os utilizadores, use o `/.auth/ext/logout` caminho. Para realizar outras ações, consulte a documentação sobre [a autenticação e utilização da Autorização do Serviço de Aplicações.](./app-service-authentication-how-to.md) Há também documentação oficial sobre a interface Tomcat [HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) e seus métodos. Os seguintes métodos de servidão também são hidratados com base na configuração do seu Serviço de Aplicações:

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
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Transferência](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

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

Os JDKs suportados são automaticamente corrigidos trimestralmente em janeiro, abril, julho e outubro de cada ano. Para mais informações sobre Java on Azure, consulte [este documento de suporte](/azure/developer/java/fundamentals/java-jdk-long-term-support).

### <a name="security-updates"></a>Atualizações de segurança

Patches e correções para grandes vulnerabilidades de segurança serão lançados assim que forem disponibilizados pela Azul Systems. Uma vulnerabilidade "importante" é definida por uma pontuação base de 9.0 ou superior no [Sistema de Pontuação Comum de Vulnerabilidade NIST, versão 2](https://nvd.nist.gov/vuln-metrics/cvss).

Tomcat 8.0 chegou ao [Fim da Vida (EOL) a 30 de setembro de 2018](https://tomcat.apache.org/tomcat-80-eol.html). Embora o tempo de execução ainda esteja disponível no Azure App Service, o Azure não aplicará atualizações de segurança ao Tomcat 8.0. Se possível, migrar as suas aplicações para Tomcat 8.5 ou 9.0. Tanto o Tomcat 8.5 como o 9.0 estão disponíveis no Azure App Service. Consulte o site oficial do [Tomcat](https://tomcat.apache.org/whichversion.html) para mais informações. 

### <a name="deprecation-and-retirement"></a>Depreciação e reforma

Se um tempo de execução java suportado for retirado, os desenvolvedores Azure que usam o tempo de execução afetado receberão um aviso de depreciação pelo menos seis meses antes do tempo de execução ser retirado.

### <a name="local-development"></a>Desenvolvimento local

Os desenvolvedores podem descarregar a Edição de Produção da Azul Zulu Enterprise JDK para o desenvolvimento local a partir do [site de descarregamento da Azul.](https://www.azul.com/downloads/azure-only/zulu/)

### <a name="development-support"></a>Apoio ao desenvolvimento

O suporte do produto para o [Azul Zulu JDK suportado pelo Azure](https://www.azul.com/downloads/azure-only/zulu/) está disponível através da Microsoft quando se desenvolve para Azure ou [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) com um [plano de suporte Azure qualificado.](https://azure.microsoft.com/support/plans/)

### <a name="runtime-support"></a>Suporte ao tempo de execução

Os desenvolvedores podem [abrir um problema](../azure-portal/supportability/how-to-create-azure-support-request.md) com os JDKs Azul Zulu através do Suporte Azure se tiverem um plano de apoio [qualificado.](https://azure.microsoft.com/support/plans/)

## <a name="next-steps"></a>Passos seguintes

Este tópico fornece a declaração de suporte do Java Runtime para o Serviço de Aplicações Azure no Windows.

- Para saber mais sobre hospedagem de aplicações web com o Azure App Service consulte a [visão geral do Serviço de Aplicações.](overview.md)
- Para obter informações sobre o desenvolvimento de Java on Azure consulte [Azure para o Java Dev Center](/java/azure/?view=azure-java-stable).

::: zone-end

::: zone pivot="platform-linux"

O Azure App Service no Linux permite que os desenvolvedores de Java construam, implementem e escalam rapidamente as suas aplicações web Tomcat, ou Java Standard Edition (SE) num serviço totalmente gerido baseado em Linux. Implementar aplicações com plugins Maven da linha de comando ou em editores como IntelliJ, Eclipse ou Visual Studio Code.

Este guia fornece conceitos e instruções fundamentais para os desenvolvedores de Java que usam um recipiente Linux incorporado no Serviço de Aplicações. Se nunca usou o Azure App Service, siga o [quickstart Java](quickstart-java.md).

## <a name="deploying-your-app"></a>Implementação da sua app

Pode utilizar [o Maven Plugin para o Serviço de Aplicações Azure](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) para implementar ficheiros .jar e .war. A implementação com iDEs populares também é suportada com [Azure Toolkit para IntelliJ](/azure/developer/java/toolkit-for-intellij/) ou [Azure Toolkit para Eclipse](/azure/developer/java/toolkit-for-eclipse).

Caso contrário, o seu método de implantação dependerá do seu tipo de arquivo:

- Para implementar ficheiros de .war para Tomcat, use o `/api/wardeploy/` ponto final para registar o seu ficheiro de arquivo. Para mais informações sobre esta API, consulte [esta documentação.](./deploy-zip.md#deploy-war-file)
- Para implementar ficheiros .jar nas imagens Java SE, utilize o `/api/zipdeploy/` ponto final do site Kudu. Para mais informações sobre esta API, consulte [esta documentação.](./deploy-zip.md#rest)

Não desloque o seu .war ou .jar utilizando FTP. A ferramenta FTP foi concebida para carregar scripts de arranque, dependências ou outros ficheiros de tempo de execução. Não é a escolha ideal para implementar aplicações web.

## <a name="logging-and-debugging-apps"></a>Aplicativos de registo e depuragem

Relatórios de desempenho, visualizações de tráfego e check-ups de saúde estão disponíveis para cada aplicação através do portal Azure. Para mais informações, consulte [a visão geral do Serviço de Aplicações Azure](overview-diagnostics.md).

### <a name="ssh-console-access"></a>Acesso à consola SSH

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Transmitir registos de diagnóstico em fluxo

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

Para obter mais informações, consulte [os registos de streaming em Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Registo de aplicativos

Habilitar o [registo de aplicações](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) através do portal Azure ou [do Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) para configurar o Serviço de Aplicações para escrever a saída padrão da consola da sua aplicação e fluxos de erro de consola padrão para o sistema de ficheiros local ou para o Armazenamento Azure Blob. O registo na instância do sistema de ficheiros do Serviço de Aplicações local é desativado 12 horas após a sua configuração. Se necessitar de uma retenção mais longa, configuure a aplicação para escrever a saída num recipiente de armazenamento Blob. Os seus registos de aplicações Java e Tomcat podem ser encontrados no *diretório /home/LogFiles/Application/.*

>[!NOTE]
>O registo no sistema de ficheiros local do Serviço de Aplicações que está a ser desativado após 12 horas aplica-se apenas aos Serviços de Aplicações baseados no Windows. O registo de armazenamento Azure Blob para Serviços de Aplicações baseados em Linux só pode ser configurado usando [o Azure Monitor (pré-visualização)](./troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview) 

Se a sua aplicação utilizar [Logback](https://logback.qos.ch/) ou [Log4j](https://logging.apache.org/log4j) para rastreio, pode encaminhar estes vestígios para revisão em Azure Application Insights utilizando as instruções de configuração de quadro de registo em [Explore Java trace logs in Application Insights](../azure-monitor/app/java-trace-logs.md).

### <a name="troubleshooting-tools"></a>Ferramentas de resolução de problemas

As imagens de Java incorporadas são baseadas no sistema operativo [Alpine Linux.](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) Utilize o `apk` gestor de pacotes para instalar quaisquer ferramentas ou comandos de resolução de problemas.

### <a name="flight-recorder"></a>Gravador de Voo

Todas as imagens Linux Java no App Service têm o Zulu Flight Recorder instalado para que possa ligar-se facilmente ao JVM e iniciar uma gravação de perfis ou gerar uma lixeira de pilhas.

#### <a name="timed-recording"></a>Gravação cronometrada

Para começar, sSH no seu Serviço de Aplicações e executar o `jcmd` comando para ver uma lista de todos os processos java em execução. Além do próprio jcmd, deverá ver a sua aplicação Java a funcionar com um número de ID de processo (pid).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Execute o comando abaixo para iniciar uma gravação de 30 segundos do JVM. Isto irá perfilar o JVM e criar um ficheiro JFR chamado *jfr_example.jfr* no diretório doméstico. (Substitua 116 pela pid da sua app Java.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Durante o intervalo de 30 segundos, pode validar a gravação funcionando `jcmd 116 JFR.check` . Isto mostrará todas as gravações para o processo java dado.

#### <a name="continuous-recording"></a>Gravação Contínua

Pode utilizar o Gravador de Voo Zulu para perfilar continuamente a sua aplicação Java com o mínimo impacto no desempenho do tempo de[execução (fonte).](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf) Para tal, execute o seguinte comando Azure CLI para criar uma Definição de Aplicação com o nome JAVA_OPTS com a configuração necessária. Os conteúdos da JAVA_OPTS Definição de Aplicações são transmitidos ao comando quando a `java` sua aplicação é iniciada.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Uma vez iniciada a gravação, pode despejar os dados de gravação atuais a qualquer momento utilizando o `JFR.dump` comando.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

Para mais informações, consulte a Referência de [Comando Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Análise de Gravações

Utilize [o FTPS](deploy-ftp.md) para descarregar o seu ficheiro JFR para a sua máquina local. Para analisar o ficheiro JFR, descarregue e instale [o Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/). Para obter instruções sobre o Controlo da Missão Zulu, consulte a [documentação azul](https://docs.azul.com/zmc/) e as [instruções de instalação.](/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)

## <a name="customization-and-tuning"></a>Personalização e afinação

O Azure App Service for Linux suporta a afinação e personalização da caixa através do portal Azure e CLI. Reveja os seguintes artigos para configuração de aplicações web não específicas de Java:

- [Configurar as definições da aplicação](configure-common.md#configure-app-settings)
- [Configurar um domínio personalizado](app-service-web-tutorial-custom-domain.md)
- [Encostados SSL](configure-ssl-bindings.md)
- [Adicione um CDN](../cdn/cdn-add-to-web-app.md)
- [Configure o site kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Definir opções de tempo de execução de Java

Para definir a memória alocada ou outras opções de tempo de execução JVM tanto nos ambientes Tomcat como Java SE, crie uma [definição de aplicação](configure-common.md#configure-app-settings) nomeada `JAVA_OPTS` com as opções. O Serviço de Aplicações Linux passa esta definição como uma variável ambiental para o tempo de execução java quando começa.

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

- Casos B1 e S1: `-Xms1024m -Xmx1024m`
- Casos B2 e S2: `-Xms3072m -Xmx3072m`
- Casos B3 e S3: `-Xms6144m -Xmx6144m`

Ao sintonizar as definições da pilha de aplicações, reveja os detalhes do seu plano de App Service e tenha em conta várias aplicações e slots de implementação para encontrar a alocação ideal de memória.

Se estiver a implementar uma aplicação JAR, deve ser nomeada *app.jar* para que a imagem incorporada possa identificar corretamente a sua aplicação. (O plugin Maven faz este renomeamento automaticamente.) Se não desejar mudar o nome do seu JAR para *app.jar,* pode carregar um script de concha com o comando para executar o seu JAR. Em seguida, cole o caminho completo para este script na caixa de texto [Ficheiro de Arranque](faq-app-service-linux.md#built-in-images) na secção Configuração do portal. O script de arranque não é executado a partir do diretório no qual é colocado. Por isso, utilize sempre caminhos absolutos para referenciar ficheiros no script de arranque (por exemplo: `java -jar /home/myapp/myapp.jar`).

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

### <a name="adjust-startup-timeout"></a>Ajustar o tempo de arranque

Se a sua aplicação Java for particularmente grande, deverá aumentar o prazo de arranque. Para isso, crie uma definição de aplicação `WEBSITES_CONTAINER_START_TIME_LIMIT` e defina-a para o número de segundos que o Serviço de Aplicações deve esperar antes de cronometrar. O valor máximo é `1800` de segundos.

### <a name="pre-compile-jsp-files"></a>Pré-Compilar ficheiros JSP

Para melhorar o desempenho das aplicações Tomcat, pode compilar os seus ficheiros JSP antes de ser implantado no Serviço de Aplicações. Pode utilizar o [plugin Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) fornecido pela Apache Sling ou utilizar este [ficheiro de construção de Formigas](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Aplicações seguras

As aplicações java em execução no App Service para o Linux têm o mesmo conjunto de [boas práticas](../security/fundamentals/paas-applications-using-app-services.md) de segurança que outras aplicações.

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

Para assinar os utilizadores, use o `/.auth/ext/logout` caminho. Para realizar outras ações, consulte a documentação sobre [a autenticação e utilização da Autorização do Serviço de Aplicações.](./app-service-authentication-how-to.md) Há também documentação oficial sobre a interface Tomcat [HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) e seus métodos. Os seguintes métodos de servidão também são hidratados com base na configuração do seu Serviço de Aplicações:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Para desativar esta funcionalidade, crie uma Definição de Aplicação `WEBSITE_AUTH_SKIP_PRINCIPAL` com o valor de `1` . Para desativar todos os filtros de servidão adicionados pelo App Service, crie uma definição `WEBSITE_SKIP_FILTERS` com o valor de `1` .

#### <a name="spring-boot"></a>Spring Boot

Os desenvolvedores de Boot de mola podem usar o [arranque Azure Ative Directory Spring Boot](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) para garantir aplicações usando anotações e APIs familiares de Segurança da primavera. Certifique-se de que aumenta o tamanho máximo do cabeçalho no ficheiro *application.properties.* Sugerimos um valor `16384` de.

### <a name="configure-tlsssl"></a>Configurar TLS/SSL

Siga as instruções no [Secure um nome DNS personalizado com uma ligação SSL no Azure App Service](configure-ssl-bindings.md) para carregar um certificado SSL existente e contê-lo ao nome de domínio da sua aplicação. Por predefinição, a sua aplicação continuará a permitir que as ligações HTTP sigam os passos específicos do tutorial para impor SSL e TLS.

### <a name="use-keyvault-references"></a>Utilizar referências KeyVault

[A Azure KeyVault](../key-vault/general/overview.md) fornece uma gestão secreta centralizada com políticas de acesso e histórico de auditoria. Pode armazenar segredos (como palavras-passe ou cadeias de ligação) no KeyVault e aceder a estes segredos na sua aplicação através de variáveis ambientais.

Em primeiro lugar, siga as instruções para [conceder acesso à sua aplicação ao Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) e fazer uma referência [KeyVault ao seu segredo numa Definição de Aplicação.](app-service-key-vault-references.md#reference-syntax) Pode validar que a referência se resolve ao segredo imprimindo a variável ambiente ao aceder remotamente ao terminal de Serviço de Aplicações.

Para injetar estes segredos no seu ficheiro de configuração primavera ou Tomcat, utilize a sintaxe de injeção variável do ambiente `${MY_ENV_VAR}` (). Para ficheiros de configuração da primavera, consulte esta documentação sobre [configurações externas](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

### <a name="using-the-java-key-store"></a>Utilização da Loja de Chaves Java

Por predefinição, quaisquer certificados públicos ou privados enviados para o [Serviço de Aplicações Linux](configure-ssl-certificate.md) serão carregados nas respetivas Lojas de Chaves Java à medida que o contentor começar. Depois de carregar o seu certificado, terá de reiniciar o seu Serviço de Aplicações para que seja carregado na Loja de Chaves Java. Os certificados públicos são carregados na Loja-Chave `$JAVA_HOME/jre/lib/security/cacerts` em , e os certificados privados são armazenados em `$JAVA_HOME/lib/security/client.jks` .

Poderá ser necessária uma configuração adicional para encriptar a sua ligação JDBC com certificados na Loja de Chaves Java. Consulte a documentação do seu motorista JDBC escolhido.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initializing-the-java-key-store"></a>Inicialização da Loja de Chaves Java

Para inicializar o `import java.security.KeyStore` objeto, carregue o ficheiro da loja de chaves com a palavra-passe. A palavra-passe padrão para ambas as lojas chave é "changeit".

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

#### <a name="manually-load-the-key-store"></a>Carregue manualmente a loja de chaves

Pode carregar os certificados manualmente na loja de chaves. Crie uma configuração de aplicação, `SKIP_JAVA_KEYSTORE_LOAD` com o valor de `1` desativar o Serviço de Aplicações de carregamento dos certificados na loja de chaves automaticamente. Todos os certificados públicos enviados para o Serviço de Aplicações através do portal Azure são armazenados sob `/var/ssl/certs/` . Os certificados privados são armazenados em `/var/ssl/private/` .

Pode interagir ou depurar a Ferramenta chave Java [abrindo uma ligação SSH](configure-linux-open-ssh-session.md) ao seu Serviço de Aplicações e executando o comando `keytool` . Consulte a [documentação da Ferramenta Chave](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) para obter uma lista de comandos. Para mais informações sobre a API keyStore, consulte [a documentação oficial.](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html)

## <a name="configure-apm-platforms"></a>Configurar plataformas APM

Esta secção mostra como ligar as aplicações java implementadas no Azure App Service em Linux com as plataformas de monitorização do desempenho da aplicação NewRelic e AppDynamics (APM).

### <a name="configure-new-relic"></a>Configure nova relíquia

1. Criar uma conta NewRelic na [NewRelic.com](https://newrelic.com/signup)
2. Descarregue o agente Java da NewRelic, terá um nome de ficheiro semelhante ao *newrelic-java-x.x.x.zip*.
3. Copie a chave da sua licença e vai precisar dela para configurar o agente mais tarde.
4. [SSH na sua instância de Serviço de Aplicações](configure-linux-open-ssh-session.md) e criar um novo diretório */home/site/wwwroot/apm*.
5. Faça o upload dos ficheiros de agente Java NewRelic desembalados para um diretório em */home/site/wwwroot/apm*. Os ficheiros do seu agente devem estar em */home/site/wwwroot/apm/newrelic*.
6. Modifique o ficheiro YAML em */home/site/wwwroot/apm/newrelic/newrelic.yml* e substitua o valor da licença de espaço reservado pela sua própria chave de licença.
7. No portal Azure, navegue pela sua aplicação no Serviço de Aplicações e crie uma nova Definição de Aplicação.
    - Se a sua aplicação estiver a utilizar **a Java SE,** crie uma variável ambiental com `JAVA_OPTS` o `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` valor.
    - Se estiver a utilizar o **Tomcat,** crie uma variável ambiental com `CATALINA_OPTS` o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

### <a name="configure-appdynamics"></a>Configurar a AppDynamics

1. Criar uma conta AppDynamics na [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Descarregue o agente Java a partir do site appDynamics, o nome do ficheiro será semelhante ao *AppServerAgent-x.x.x.xxxxx.zip*
3. [SSH na sua instância de Serviço de Aplicações](configure-linux-open-ssh-session.md) e criar um novo diretório */home/site/wwwroot/apm*.
4. Faça o upload dos ficheiros do agente Java para um diretório em */home/site/wwwroot/apm*. Os ficheiros do seu agente devem estar em */home/site/wwwroot/apm/appdynamics*.
5. No portal Azure, navegue pela sua aplicação no Serviço de Aplicações e crie uma nova Definição de Aplicação.
    - Se estiver a utilizar **a Java SE,** crie uma variável ambiental com `JAVA_OPTS` o valor onde está o nome do Serviço de `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` Aplicações.
    - Se estiver a utilizar o **Tomcat,** crie uma variável ambiental com `CATALINA_OPTS` o valor onde está o nome do Serviço de `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` Aplicações.

    > [!NOTE]
    > Se já tiver uma variável ambiental para `JAVA_OPTS` `CATALINA_OPTS` ou, anexar `-javaagent:/...` a opção até ao fim do valor atual.
    
## <a name="configure-jar-applications"></a>Configure aplicações JAR

### <a name="starting-jar-apps"></a>Aplicativos JAR ini iniciar

Por predefinição, o Serviço de Aplicações espera que a sua aplicação JAR seja nomeada *app.jar*. Se tiver este nome, será executado automaticamente. Para os utilizadores de Maven, pode definir o nome JAR, incluindo `<finalName>app</finalName>` na secção do seu `<build>` *pom.xml*. [Você pode fazer o mesmo em Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) definindo a `archiveFileName` propriedade.

Se pretender utilizar um nome diferente para o seu JAR, também tem de fornecer o [Comando de Arranque](faq-app-service-linux.md#built-in-images) que executa o seu ficheiro JAR. Por exemplo, `java -jar my-jar-app.jar`. Pode definir o valor do seu Comando de Arranque no Portal, em Configuração > Definições Gerais ou com uma Definição de Aplicação nomeada `STARTUP_COMMAND` .

### <a name="server-port"></a>Porta de servidor

O Serviço de Aplicações Linux encaminha os pedidos de entrada para o porto 80, pelo que a sua aplicação também deve ouvir na porta 80. Pode fazê-lo na configuração da sua aplicação (como o ficheiro *application.properties* da primavera), ou no seu Comando de Arranque (por exemplo, `java -jar spring-app.jar --server.port=80` ). Consulte a seguinte documentação para quadros comuns de Java:

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronauta](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Enquadramento de jogo](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Verxo](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Rio Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>Origens de dados

### <a name="tomcat"></a>Tomcat

Estas instruções aplicam-se a todas as ligações de base de dados. Terá de preencher os espaços reservados com o nome da classe de condutor e o ficheiro JAR da sua base de dados escolhida. Fornecido é uma tabela com nomes de classe e downloads de motorista para bases de dados comuns.

| Base de Dados   | Nome da classe do motorista                             | Condutor JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Transferência](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Download](https://dev.mysql.com/downloads/connector/j/) (Selecione "Plataforma Independente") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Transferência](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

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

#### <a name="shared-server-level-resources"></a>Recursos ao nível do servidor partilhados

A adição de uma fonte de dados partilhada e ao nível do servidor exigirá que edite a server.xml do Tomcat. Em primeiro lugar, faça o upload de um [script de arranque](faq-app-service-linux.md#built-in-images) e desemcora o caminho para o script no Comando de Arranque de **Configuração**  >  **Startup Command**. Pode carregar o script de arranque utilizando [FTP](deploy-ftp.md).

O seu script de arranque fará uma [transformação de xsl](https://www.w3schools.com/xml/xsl_intro.asp) para o ficheiro server.xml e descolou o ficheiro xml resultante para `/usr/local/tomcat/conf/server.xml` . O script de arranque deve instalar libxslt via apk. O seu ficheiro xsl e o script de arranque podem ser carregados via FTP. Abaixo está um script de arranque exemplo.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Um ficheiro xsl de exemplo é fornecido abaixo. O ficheiro xsl exemplo adiciona um novo nó de conector ao tomcat server.xml.

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

Por fim, coloque os JARs do condutor no apêndice tomcat e reinicie o seu Serviço de Aplicações.

1. Certifique-se de que os ficheiros do controlador JDBC estão disponíveis para o carregador de classe Tomcat colocando-os no diretório */home/tomcat/lib.* (Criar este diretório se já não existir.) Para fazer o upload destes ficheiros para a sua instância do Serviço de Aplicações, execute os seguintes passos:

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

2. Se criou uma fonte de dados ao nível do servidor, reinicie a aplicação Linux do Serviço de Aplicações. O Tomcat irá reiniciar `CATALINA_BASE` `/home/tomcat` e utilizar a configuração atualizada.

### <a name="spring-boot"></a>Spring Boot

Para ligar a fontes de dados em aplicações Spring Boot, sugerimos criar cadeias de ligação e injetá-las no ficheiro *aplicações.properties.*

1. Na secção "Configuração" da página Serviço de Aplicações, desaprova um nome para a cadeia, cole a sua cadeia de ligação JDBC no campo de valor e desaver o tipo de "Personalizado". Pode configurar opcionalmente esta cadeia de ligação como definição de ranhura.

    Esta cadeia de conexão é acessível à nossa aplicação como uma variável ambiental chamada `CUSTOMCONNSTR_<your-string-name>` . Por exemplo, a cadeia de ligação que criamos acima será nomeada `CUSTOMCONNSTR_exampledb` .

2. No ficheiro *application.properties,* refira esta cadeia de ligação com o nome variável ambiente. Pelo nosso exemplo, usaríamos o seguinte.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Consulte a [documentação do Boot de Mola sobre o acesso aos dados](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) e [configurações externas](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) para obter mais informações sobre este tópico.

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>Use redis como cache de sessão com Tomcat

Pode configurar o Tomcat para utilizar uma loja de sessão externa, como [a Azure Cache for Redis.](../azure-cache-for-redis/index.yml) Isto permite-lhe preservar o estado de sessão do utilizador (como dados de carrinhos de compras) quando um utilizador é transferido para outra instância da sua aplicação, por exemplo quando ocorre autoscaling, reinício ou falha.

Para utilizar o Tomcat com o Redis, tem de configurar a sua aplicação para utilizar uma implementação [PersistenteManager.](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) Os seguintes passos explicam este processo usando [o Pivotal Session Manager: redis-store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) como exemplo.

1. Abra um terminal Bash e use `<variable>=<value>` para definir cada uma das seguintes variáveis ambientais.

    | Variável                 | Valor                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | O nome do grupo de recursos que contém a sua instância do Serviço de Aplicações.       |
    | WEBAPP_NAME              | O nome da sua instância do Serviço de Aplicações.                                     |
    | WEBAPP_PLAN_NAME         | O nome do seu plano de Serviço de Aplicações.                                         |
    | REGIÃO                   | O nome da região onde a sua aplicação está hospedada.                           |
    | REDIS_CACHE_NAME         | O nome do seu Azure Cache para o caso Redis.                           |
    | REDIS_PORT               | A porta SSL que a sua cache Redis ouve.                             |
    | REDIS_PASSWORD           | A chave de acesso primária para o seu exemplo.                                  |
    | REDIS_SESSION_KEY_PREFIX | Um valor que especifica para identificar as chaves de sessão que vêm da sua aplicação. |

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

    Pode encontrar o nome, porta e aceder às informações chave do portal Azure, olhando para as secções **propriedades** ou **teclas** de acesso da sua instância de serviço.

2. Crie ou atualize o ficheiro *src/main/webapp/META-INF/context.xml* da sua aplicação com o seguinte conteúdo:

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

    Este ficheiro especifica e configura a implementação do gestor de sessão para a sua aplicação. Utiliza as variáveis ambientais que definiu no passo anterior para manter as informações da sua conta fora dos seus ficheiros de origem.

3. Utilize o FTP para fazer o upload do ficheiro JAR do gestor da sessão para a sua instância do Serviço de Aplicações, colocando-o no diretório */home/tomcat/lib.* Para obter mais informações, consulte [implementar a sua aplicação no Azure App Service utilizando FTP/S](./deploy-ftp.md).

4. Desative o [cookie de afinidade da sessão](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) para a sua instância do Serviço de Aplicações. Pode fazê-lo a partir do portal Azure navegando na sua aplicação e, em seguida, definindo **configurações de Configuração > Configurações Gerais > afinidade ARR** para **Off**. Alternadamente, pode utilizar o seguinte comando:

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    Por padrão, o Serviço de Aplicações utilizará cookies de afinidade de sessão para garantir que os pedidos do cliente com as sessões existentes são encaminhados para a mesma instância da sua aplicação. Este comportamento predefinido não requer nenhuma configuração, mas não pode preservar o estado de sessão do utilizador quando a sua aplicação é reiniciada ou quando o tráfego é reencaminhado para outra instância. Quando [desativar a](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) configuração de ARR Instance Affinity existente para desligar o roteamento baseado em cookies da sessão, permite que a loja de sessão configurada funcione sem interferências.

5. Navegue na secção **Propriedades** da sua instância do Serviço de Aplicações e encontre **endereços IP adicionais de saída.** Estes representam todos os possíveis endereços IP de saída para a sua aplicação. Copie-os para utilização no próximo passo.

6. Para cada endereço IP, crie uma regra de firewall na sua Cache Azure para a instância Redis. Pode fazê-lo no portal Azure a partir da secção **Firewall** da sua instância Redis. Forneça um nome único para cada regra e desa estacie os valores do **endereço IP inicial** e do endereço IP **final** para o mesmo endereço IP.

7. Navegue na secção de **definições Avançadas** da sua instância Redis e defina **Deixe o acesso apenas via SSL** a **Nº**. Isto permite que a sua instância de Serviço de Aplicações comunique com a sua cache Redis através da infraestrutura Azure.

8. Atualize a `azure-webapp-maven-plugin` configuração no ficheiro *pom.xml* da sua aplicação para se referir às informações da sua conta Redis. Este ficheiro utiliza as variáveis ambientais que definiu anteriormente para manter as informações da sua conta fora dos seus ficheiros de origem.

    Se for necessário, mude `1.9.1` para a versão atual do [Maven Plugin for Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.9.1</version>
        <configuration>            
            <!-- Web App information -->
            <schemaVersion>v2</schemaVersion>
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}-${REGION}</appServicePlanName>
            <appName>${WEBAPP_NAME}-${REGION}</appName>
            <region>${REGION}</region>            
            <runtime>
                <os>linux</os>
                <javaVersion>jre8</javaVersion>
                <webContainer>tomcat 9.0</webContainer>
            </runtime>

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

A sua aplicação irá agora utilizar o seu cache Redis para gestão de sessão.

Para obter uma amostra que possa utilizar para testar estas instruções, consulte o [repo-web-on-azure de escala-stateful-java-web-on-azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) no GitHub.

## <a name="docker-containers"></a>Contentores do Docker

Para utilizar o Zulu JDK apoiado pelo Azure nos seus contentores, certifique-se de puxar e utilizar as imagens pré-construídas como documentado a partir da página de [descarregamento suportada do Azul Zulu Enterprise para a Azure](https://www.azul.com/downloads/azure-only/zulu/) ou use os `Dockerfile` exemplos do [repo Microsoft Java GitHub.](https://github.com/Microsoft/java/tree/master/docker)

## <a name="statement-of-support"></a>Declaração de apoio

### <a name="runtime-availability"></a>Disponibilidade de tempo de execução

O Serviço de Aplicações para Linux suporta dois tempos de execução para hospedagem gerida de aplicações web java:

- O [recipiente de servidão Tomcat](https://tomcat.apache.org/) para executar aplicações embaladas como ficheiros de arquivo web (WAR). As versões suportadas são 8.5 e 9.0.
- Ambiente de execução Java SE para executar aplicações embaladas como ficheiros de arquivo Java (JAR). As versões suportadas são Java 8 e 11.

### <a name="jdk-versions-and-maintenance"></a>Versões e manutenção JDK

As construções da Azul Zulu Enterprise da OpenJDK são uma distribuição sem custos, multi-plataforma, pronta para a produção do OpenJDK para Azure e Azure Stack, apoiado pela Microsoft e pela Azul Systems. Contêm todos os componentes para compilar e executar aplicações Java SE. Pode instalar o JDK a partir da [Instalação Java JDK.](https://aka.ms/azure-jdks)

Os JDKs suportados são automaticamente corrigidos trimestralmente em janeiro, abril, julho e outubro de cada ano.

### <a name="security-updates"></a>Atualizações de segurança

Patches e correções para grandes vulnerabilidades de segurança serão lançados assim que forem disponibilizados pela Azul Systems. Uma vulnerabilidade "importante" é definida por uma pontuação base de 9.0 ou superior no [Sistema de Pontuação Comum de Vulnerabilidade NIST, versão 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Depreciação e reforma

Se um tempo de execução java suportado for retirado, os desenvolvedores Azure que usam o tempo de execução afetado receberão um aviso de depreciação pelo menos seis meses antes do tempo de execução ser retirado.

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Passos seguintes

Visite o [centro de Azure para Java Developers](/java/azure/) para encontrar quickstarts, tutoriais e documentação de referência java.

As questões gerais sobre a utilização do Serviço de Aplicações para o Linux que não sejam específicas para o desenvolvimento de Java são respondidas no [Serviço de Aplicações Linux FAQ](faq-app-service-linux.md).

::: zone-end