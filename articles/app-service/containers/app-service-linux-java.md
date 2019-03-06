---
title: Guia de programação Java para o serviço de aplicações no Linux - Azure | Documentos da Microsoft
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
ms.date: 12/10/2018
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: b28316242f608fcfc2b368170190a771c4abbc8e
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57456113"
---
# <a name="java-developers-guide-for-app-service-on-linux"></a>Guia de programação Java para o serviço de aplicações no Linux

Serviço de aplicações do Azure no Linux permite aos programadores de Java para rapidamente criar, implementar e dimensionar seus Tomcat ou aplicativos de web num serviço baseado em Linux totalmente gerido de pacote de Java Standard Edition (SE). Implemente aplicações com o plug-ins do Maven na linha de comandos ou em editores de como o IntelliJ, o Eclipse ou o Visual Studio Code.

Este guia fornece os conceitos chave e instruções para programadores de Java no serviço de aplicações a utilizar para Linux. Se nunca tiver utilizado o serviço de aplicações do Azure para Linux, deve ler os [início rápido de Java](quickstart-java.md) primeiro. Perguntas gerais sobre como utilizar o serviço de aplicações para Linux que não sejam específicas para o desenvolvimento de Java são respondidas no [FAQ de Linux do serviço de aplicações](app-service-linux-faq.md).

## <a name="deploying-your-app"></a>Implementar a sua aplicação

Pode utilizar o plug-in do Maven para implementar ficheiros. JAR e. War. Veja [esta documentação](https://docs.microsoft.com/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme?view=azure-java-stable) para obter mais informações sobre o plug-in do Maven.

Se não estiver a utilizar o Maven, seu método de implementação irá depender do tipo de arquivo:

- Para implementar ficheiros. War para Tomcat, utilize o `/api/wardeploy/` ponto final para publicar o seu ficheiro de arquivo. Para obter mais informações sobre esta API, veja [esta documentação](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Para implementar ficheiros. JAR nas imagens de Java SE, utilize o `/api/zipdeploy/` ponto final do Kudu site. Para obter mais informações sobre esta API, veja [esta documentação](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Não implemente sua. War ou. JAR utilizando o FTP. A ferramenta FTP foi criada para carregar scripts de inicialização, dependências ou outros arquivos de tempo de execução. Não é a escolha ideal para a implementação de aplicações web.

## <a name="logging-and-debugging-apps"></a>Registo e depuração de aplicativos

Relatórios de desempenho, visualizações de tráfego e exames abrangentes de estado de funcionamento estão disponíveis para cada aplicação através do portal do Azure. Consulte a [descrição geral de diagnóstico do serviço de aplicações do Azure](/azure/app-service/overview-diagnostics) para obter mais informações sobre como aceder e utilizar estas ferramentas de diagnóstico.

## <a name="application-performance-monitoring"></a>Monitorização de desempenho de aplicações

Ver [ferramentas com aplicações Java no serviço de aplicações do Azure no Linux de monitorização do desempenho de aplicações](how-to-java-apm-monitoring.md) para instruções sobre como proceder configurar o New Relic e AppDynamics com aplicações Java em execução no serviço de aplicações no Linux.

### <a name="ssh-console-access"></a>Acesso à consola SSH

Conectividade SSH para o ambiente do Linux com a sua aplicação está disponível. Ver [suporte SSH para o serviço de aplicações do Azure no Linux](/azure/app-service/containers/app-service-linux-ssh-support) para obter instruções completas ligar ao sistema Linux por meio de seu navegador da web ou um local terminal.

### <a name="streaming-logs"></a>Registos de transmissão em fluxo

Para rápida de depuração e resolução de problemas, pode transmitir em fluxo registos para a consola com a CLI do Azure. Configurar a CLI com o `az webapp log config` para ativar o registo:

```azurecli-interactive
az webapp log config --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME} \
 --web-server-logging filesystem
```

Em seguida, transmita registos para o seu console com `az webapp log tail`:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

Para obter mais informações, consulte [transmissão em fluxo registos com a CLI do Azure](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli).

### <a name="app-logging"></a>Registo de aplicação

Ativar [registo de aplicações](/azure/app-service/troubleshoot-diagnostic-logs#enablediag) através do portal do Azure ou [CLI do Azure](/cli/azure/webapp/log#az-webapp-log-config) para configurar o serviço de aplicações para escrever a saída do console padrão e os fluxos de erro de console padrão da sua aplicação no local sistema de ficheiros ou armazenamento de Blobs do Azure. O registo para o sistema de ficheiros local do serviço de aplicações instância está desativada 12 horas depois de estar configurada. Se precisar de retenção mais longa, configure a aplicação para escrever a saída para um contentor de armazenamento de Blobs.

Se o aplicativo usar [Logback](https://logback.qos.ch/) ou [Log4j](https://logging.apache.org/log4j) para o rastreio, pode reencaminhar estes rastreios para revisão para o Azure Application Insights com as instruções de configuração do Registro em log framework [Registos de rastreio de explorar o Java no Application Insights](/azure/application-insights/app-insights-java-trace-logs).

## <a name="customization-and-tuning"></a>Personalização e Otimização

Serviço de aplicações do Azure para Linux oferece imediatamente suporte para a caixa de ajuste e a personalização através do Portal do Azure e a CLI. Reveja os seguintes artigos para a configuração de aplicações web específica de não-Java:

- [Configurar as definições do serviço de aplicações](/azure/app-service/web-sites-configure?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Configurar um domínio personalizado](/azure/app-service/app-service-web-tutorial-custom-domain?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Ativar o SSL](/azure/app-service/app-service-web-tutorial-custom-ssl?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Adicionar uma CDN](/azure/cdn/cdn-add-to-web-app?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)

### <a name="set-java-runtime-options"></a>Definir opções de tempo de execução de Java

Para definir a memória alocada ou outras opções de tempo de execução JVM nos ambientes do Tomcat e Java SE, defina o JAVA_OPTS conforme mostrado abaixo, como um [definição de aplicação](/azure/app-service/web-sites-configure#app-settings). Serviço de aplicações Linux passa esta definição como uma variável de ambiente para o tempo de execução Java quando ele é iniciado.

No portal do Azure, em **as configurações do aplicativo** da aplicação web, criar uma nova definição de aplicação com o nome `JAVA_OPTS` que inclui as definições adicionais, tais como `$JAVA_OPTS -Xms512m -Xmx1204m`.

Para configurar a definição de aplicação o plug-in do Maven de Linux do Azure App Service, adicione marcas de definição/valor na secção de plug-in do Azure. O exemplo seguinte define uma heapsize específico de Java de máxima e mínima:

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
az webapp config set -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} --web-sockets-enabled true
```

Em seguida, reinicie a aplicação:

```azurecli-interactive
az webapp stop -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME}
az webapp start -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME}  
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

Configurar a autenticação de aplicação no Portal do Azure com o **autenticação e autorização** opção. A partir daí, pode ativar a autenticação com o Azure Active Directory ou inícios de sessão sociais como o Facebook, Google ou GitHub. Configuração do portal do Azure só funciona quando configurar um fornecedor de autenticação único.  Para obter mais informações, consulte [configurar a aplicação de serviço de aplicações para utilizar o início de sessão do Azure Active Directory](/azure/app-service/configure-authentication-provider-aad) e os artigos relacionados para outros fornecedores de identidade.

Se precisar de vários fornecedores de início de sessão, siga as instruções no [personalizar a autenticação do serviço de aplicações](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to) artigo.

Os programadores de arranque de Spring podem utilizar o [do Azure Active Directory Spring Boot starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) para proteger aplicações através de anotações de segurança de Spring familiares e APIs. Certifique-se de que aumente o tamanho máximo do cabeçalho no seu `application.properties` ficheiro. Sugerimos que um valor de `16384`.

### <a name="configure-tlsssl"></a>Configurar TLS/SSL

Siga as instruções no [vincular um certificado SSL personalizado existente](/azure/app-service/app-service-web-tutorial-custom-ssl) para carregar um certificado SSL existente e vinculá-lo para o nome de domínio da sua aplicação. Por predefinição seu aplicativo ainda permitirá HTTP ligações-siga específica os passos no tutorial para impor SSL e TLS.

## <a name="data-sources"></a>Origens de dados

### <a name="tomcat"></a>Tomcat

>[!NOTE]
> Se a sua aplicação utilizar o Spring Framework ou o Spring Boot, pode definir as informações de ligação de base de dados para a Primavera dados JPA como variáveis de ambiente [em seu arquivo de propriedades da aplicação]. Em seguida, utilize [as definições da aplicação](/azure/app-service/web-sites-configure#app-settings) para definir esses valores para a sua aplicação no portal do Azure ou na CLI.

Estas instruções aplicam-se a todas as ligações de base de dados. Precisará preencher espaços reservados com um nome de classe do controlador da base de dados escolhido e JAR do ficheiro. Fornecida é uma tabela com nomes de classes e downloads de driver para bases de dados comuns.

| Base de Dados   | Nome da classe de controlador                             | JDBC Driver                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Drvier`                        | [Transferência](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Transferir](https://dev.mysql.com/downloads/connector/j/) (selecione "Independente de plataforma") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Transferência](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Para configurar o Tomcat para utilizar a conectividade de banco de dados de Java (JDBC) ou a API de persistência de Java (JPA), personalizar o `CATALINA_OPTS` variável de ambiente que é lidos por Tomcat início a cópia de segurança. Definir esses valores por meio de uma definição de aplicação no [Plug-in Maven do serviço de aplicações](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

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

#### <a name="shared-server-level-resources"></a>Recursos partilhados, ao nível do servidor

1. Copie o conteúdo do `/usr/local/tomcat/conf` em `/home/tomcat/conf` no seu Linux do serviço de aplicações de instância através de SSH se ainda não tiver uma configuração de existir.

    ```bash
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

#### <a name="finally-place-the-driver-jars-in-the-tomcat-classpath-and-restart-your-app-service"></a>Por fim, coloque os JARs de driver em Tomcat classpath e reinicie o serviço de aplicações

1. Certifique-se de que os ficheiros de controlador JDBC estão disponíveis para o classloader Tomcat, colocando-os no `/home/tomcat/lib` diretório. (Criar este diretório se ainda não exista.) Para carregar esses arquivos à sua instância do serviço de aplicações, execute os seguintes passos:  
    1. Instale a extensão de webpp do App Service do Azure:

      ```azurecli-interactive
      az extension add –name webapp
      ```

    1. Execute o seguinte comando da CLI para criar um túnel SSH do seu sistema local no serviço de aplicações:

      ```azurecli-interactive
      az webapp remote-connection create –g [resource group] -n [app name] -p [local port to open]
      ```

    1. Ligue à porta de túnel local com o cliente SFTP e carregar os ficheiros para o `/home/tomcat/lib` pasta.

    Em alternativa, pode utilizar um cliente de FTP para carregar o controlador JDBC. Siga estes [instruções para obter as suas credenciais FTP](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials).

2. Se tiver criado uma origem de dados ao nível do servidor, reinicie a aplicação do Linux do serviço de aplicações. Tomcat redefinirá `CATALINA_HOME` para `/home/tomcat/conf` e utilizar a configuração atualizada.

### <a name="spring-boot"></a>Spring Boot

Para ligar a origens de dados em aplicativos de Spring Boot, sugerimos que criar cadeias de ligação e injetá-los para sua `application.properties` ficheiro.

1. Na secção "Definições da aplicação" do painel do serviço de aplicações, defina um nome para a cadeia de caracteres, em seguida, cole a cadeia de ligação de JDBC no campo valor e definir o tipo para "Personalizado". Opcionalmente, pode definir esta cadeia de ligação como definição de ranhura.

    ![Criar uma cadeia de ligação no Portal.][1]

    Esta cadeia de ligação está acessível ao nosso aplicativo como uma variável de ambiente com o nome `CUSTOMCONNSTR_<your-string-name>`. Por exemplo, a cadeia de ligação que criámos acima será nomeada `CUSTOMCONNSTR_exampledb`.

2. No seu `application.properties` de ficheiros, essa cadeia de caracteres de connction com o nome da variável de ambiente de referência. No nosso exemplo, utilizamos o seguinte.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Consulte a [documentação de Spring Boot no acesso a dados](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html
) e [externalized configurações](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) para obter mais informações sobre esse tópico.

## <a name="docker-containers"></a>Contentores do Docker

Para utilizar o JDK de Zulu suportada pelo Azure em seus contentores, certifique-se obter e utilizar as imagens pré-criadas, conforme documentado do [suportado Azul Zulu Enterprise para a página de transferência do Azure](https://www.azul.com/downloads/azure-only/zulu/) ou utilizar o `Dockerfile` exemplos da [Repositório do GitHub de Java do Microsoft](https://github.com/Microsoft/java/tree/master/docker).

## <a name="runtime-availability-and-statement-of-support"></a>Disponibilidade de tempo de execução e a declaração de suporte

Serviço de aplicações para Linux suporta dois runtimes de hospedagem gerenciada de aplicativos da web de Java:

- O [contentor servlet do Tomcat](https://tomcat.apache.org/) para a execução de aplicativos empacotados como arquivos mortos (WAR) da web. Versões suportadas são 8.5 e 9.0.
- Ambiente de tempo de execução Java SE para executar aplicações em pacote como arquivo de Java ficheiros (JAR). A única versão suportada de principais é Java 8.

## <a name="java-runtime-statement-of-support"></a>Instrução de tempo de execução Java de suporte

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

<!--Image references-->
[1]: ./media/app-service-linux-java/connection-string.png
