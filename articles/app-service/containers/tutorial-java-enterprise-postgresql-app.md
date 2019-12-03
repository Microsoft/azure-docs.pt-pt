---
title: 'Tutorial: aplicativo empresarial Java no Linux'
description: Saiba como obter um aplicativo empresarial Java trabalhando em Wildfly no serviço de Azure App no Linux, com conexão a um banco de dados PostgreSQL no Azure.
author: JasonFreeberg
ms.devlang: java
ms.topic: tutorial
ms.date: 11/13/2018
ms.author: jafreebe
ms.custom: seodec18
ms.openlocfilehash: 84f22d52e9a92707a26a4e64f194e82cca87757d
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687444"
---
# <a name="tutorial-build-a-java-ee-and-postgres-web-app-in-azure"></a>Tutorial: compilar um aplicativo Web Java EE e postgres no Azure

Este tutorial mostra como criar um aplicativo Web Java Enterprise Edition (EE) no serviço Azure App e conectá-lo a um banco de dados Postgres. Quando tiver terminado, você terá um aplicativo [WildFly](https://www.wildfly.org/about/) armazenando dados no [banco de dado do Azure para postgres](https://azure.microsoft.com/services/postgresql/) em execução no [serviço de aplicativo do Azure no Linux](app-service-linux-intro.md).

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Implantar um aplicativo Java EE no Azure usando o Maven
> * Criar uma base de dados Postgres no Azure
> * Configurar o servidor WildFly para usar o postgres
> * Atualizar e reimplementar a aplicação
> * Executar testes de unidade em WildFly

## <a name="prerequisites"></a>Pré-requisitos

1. [Transferir e instalar o Git](https://git-scm.com/)
2. [Baixar e instalar o Maven 3](https://maven.apache.org/install.html)
3. [Baixar e instalar o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="clone-and-edit-the-sample-app"></a>Clonar e editar o aplicativo de exemplo

Nesta etapa, você clonará o aplicativo de exemplo e configurará o modelo de objeto de projeto Maven (POM ou *pom. xml*) para implantação.

### <a name="clone-the-sample"></a>Clonar o exemplo

Na janela do terminal, navegue até um diretório de trabalho e clone [o repositório de exemplo](https://github.com/Azure-Samples/wildfly-petstore-quickstart).

```bash
git clone https://github.com/Azure-Samples/wildfly-petstore-quickstart.git
```

### <a name="update-the-maven-pom"></a>Atualizar o POM Maven

Atualize o plug-in Maven Azure com o nome e o grupo de recursos desejados do seu serviço de aplicativo. Você não precisa criar o plano ou instância do serviço de aplicativo com antecedência. O plug-in do Maven criará o grupo de recursos e o serviço de aplicativo se ele ainda não existir.

Você pode rolar para baixo até a seção `<plugins>` de *pom. xml*, linha 200, para fazer as alterações.

```xml
<!-- Azure App Service Maven plugin for deployment -->
<plugin>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-webapp-maven-plugin</artifactId>
  <version>${version.maven.azure.plugin}</version>
  <configuration>
    <appName>YOUR_APP_NAME</appName>
    <resourceGroup>YOUR_RESOURCE_GROUP</resourceGroup>
    <linuxRuntime>wildfly 14-jre8</linuxRuntime>
  ...
</plugin>  
```

Substitua `YOUR_APP_NAME` e `YOUR_RESOURCE_GROUP` pelos nomes do seu serviço de aplicativo e do grupo de recursos.

## <a name="build-and-deploy-the-application"></a>Criar e implementar a aplicação

Agora, usaremos o Maven para criar nosso aplicativo e implantá-lo no serviço de aplicativo.

### <a name="build-the-war-file"></a>Compilar o arquivo. War

O POM neste projeto está configurado para empacotar o aplicativo em um arquivo Web (WAR). Compile o aplicativo usando o Maven:

```bash
mvn clean install -DskipTests
```

Os casos de teste neste aplicativo são projetados para serem executados quando o aplicativo é implantado no WildFly. Ignoraremos os testes para compilação local e você executará os testes depois que o aplicativo for implantado no serviço de aplicativo.

### <a name="deploy-to-app-service"></a>Implementar no Serviço de Aplicações

Agora que o WAR está pronto, podemos usar o plug-in do Azure para implantar no serviço de aplicativo:

```bash
mvn azure-webapp:deploy
```

Quando a implantação for concluída, vá para a próxima etapa.

### <a name="create-a-record"></a>Criar um registro

Abra um browser e navegue para `https://<your_app_name>.azurewebsites.net/`. Parabéns, você implantou um aplicativo Java EE para Azure App serviço!

Neste ponto, o aplicativo está usando um banco de dados H2 na memória. Clique em "admin" na barra de navegação e crie uma nova categoria. O registro no banco de dados na memória será perdido se você reiniciar sua instância do serviço de aplicativo. Nas etapas a seguir, você corrigirá isso provisionando um banco de dados Postgres no Azure e configurará o WildFly para usá-lo.

![Local do botão admin](media/tutorial-java-enterprise-postgresql-app/admin_button.JPG)

## <a name="provision-a-postgres-database"></a>Provisionar um banco de dados Postgres

Para provisionar um servidor de banco de dados Postgres, abra um terminal e use o comando [AZ postgres Server CREATE](https://docs.microsoft.com/cli/azure/postgres/server) , conforme mostrado no exemplo a seguir. Substitua os espaços reservados (incluindo os colchetes) pelos valores de sua escolha, usando o mesmo grupo de recursos que você forneceu anteriormente para sua instância do serviço de aplicativo. As credenciais de administrador que você fornecer permitirão o acesso futuro, portanto, lembre-se de manter uma observação delas para uso posterior.

```bash
az postgres server create \
    --name <server name> \
    --resource-group <resource group> \
    --location <location>
    --sku-name GP_Gen5_2 \
    --admin-user <administrator username> \
    --admin-password <administrator password> \
```

Depois de executar esse comando, navegue até a portal do Azure e navegue até o banco de dados Postgres. Quando a folha estiver ativa, copie os valores "nome do servidor" e "nome de logon do administrador do servidor". você precisará deles mais tarde.

### <a name="allow-access-to-azure-services"></a>Permitir acesso aos serviços do Azure

No painel **segurança de conexão** da folha banco de dados do Azure, alterne o botão "permitir acesso aos serviços do Azure" na posição **ligado** .

![Permitindo o acesso aos serviços do Azure](media/tutorial-java-enterprise-postgresql-app/postgress_button.JPG)

## <a name="update-your-java-app-for-postgres"></a>Atualizar seu aplicativo Java para postgres

Agora vamos fazer algumas alterações no aplicativo Java para habilitá-lo a usar nosso banco de dados Postgres.

### <a name="add-postgres-credentials-to-the-pom"></a>Adicionar credenciais do postgres ao POM

No *pom. xml*, substitua os valores de espaço reservado em maiúsculas pelo nome do servidor Postgres, nome de logon do administrador e senha. Esses campos estão dentro do plug-in do Azure Maven. (Certifique-se de substituir `YOUR_SERVER_NAME`, `YOUR_PG_USERNAME`e `YOUR_PG_PASSWORD` nas marcas de `<value>`... Não dentro das marcas de `<name>`!)

```xml
<plugin>
      ...
      <appSettings>
      <property>
        <name>POSTGRES_CONNECTIONURL</name>
        <value>jdbc:postgresql://YOUR_SERVER_NAME:5432/postgres?ssl=true</value>
      </property>
      <property>
        <name>POSTGRES_USERNAME</name>
        <value>YOUR_PG_USERNAME</value>
      </property>
      <property>
        <name>POSTGRES_PASSWORD</name>
        <value>YOUR_PG_PASSWORD</value>
      </property>
    </appSettings>
  </configuration>
</plugin>
```

### <a name="update-the-java-transaction-api"></a>Atualizar a API de transação Java

Em seguida, precisamos editar nossa configuração do JPA (API de transação Java) para que nosso aplicativo Java se comunique com o postgres em vez do banco de dados H2 na memória que estávamos usando anteriormente. Abra um editor para *src/main/resources/META-INF/persistence. xml*. Substitua o valor de `<jta-data-source>` por `java:jboss/datasources/postgresDS`. O XML JTA agora deve ter essa configuração:

```xml
<jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
```

## <a name="configure-the-wildfly-application-server"></a>Configurar o servidor de aplicativos WildFly

Antes de implantar nosso aplicativo reconfigurado, devemos atualizar o servidor de aplicativos WildFly com o módulo postgres e suas dependências. Mais informações de configuração podem ser encontradas em [Configurar o servidor WildFly](configure-language-java.md#configure-java-ee-wildfly).

Para configurar o servidor, precisaremos dos quatro arquivos no diretório *wildfly_config/* :

- **PostgreSQL-42.2.5. jar**: esse arquivo JAR é o driver JDBC para Postgres. Para obter mais informações, consulte o [site oficial](https://jdbc.postgresql.org/index.html).
- **postgres-Module. xml**: esse arquivo XML declara um nome para o módulo postgres (org. Postgres). Ele também especifica os recursos e as dependências necessárias para o módulo a ser usado.
- **jboss_cli_commands. CLI**: este arquivo contém comandos de configuração que serão executados para o pela CLI do JBoss. Os comandos adicionam o módulo postgres ao servidor de aplicativos do WildFly, fornecem as credenciais, declaram um nome JNDI, definem o tempo limite, etc. Se você não estiver familiarizado com a CLI do JBoss, consulte a [documentação oficial](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- **startup_script. sh**: por fim, esse script de shell será executado sempre que sua instância do serviço de aplicativo for iniciada. O script executa apenas uma função: canalizando os comandos em *jboss_cli_commands. CLI* para a CLI do JBoss.

É altamente recomendável ler o conteúdo desses arquivos, especialmente *jboss_cli_commands. CLI*.

### <a name="ftp-the-configuration-files"></a>Arquivos de configuração do FTP

Precisaremos de FTP o conteúdo de *wildfly_config/* para nossa instância do serviço de aplicativo. Para obter suas credenciais de FTP, clique no botão **obter perfil de publicação** na folha serviço de aplicativo na portal do Azure. O nome de usuário e a senha do FTP estarão no documento XML baixado. Para obter mais informações sobre o perfil de publicação, consulte [este documento](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials).

Usando uma ferramenta de FTP de sua escolha, transfira os quatro arquivos em *wildfly_config/* para */Home/site/Deployments/Tools/* . (Observe que você não deve transferir o diretório, apenas os próprios arquivos.)

### <a name="finalize-app-service"></a>Finalizar serviço de aplicativo

Na folha serviço de aplicativo, navegue até o painel "configurações do aplicativo". Em "tempo de execução", defina o campo "arquivo de inicialização" como */home/site/deployments/tools/startup_script. sh*. Isso garantirá que o script do shell seja executado depois que a instância do serviço de aplicativo for criada, mas antes do início do servidor WildFly.

Por fim, reinicie o serviço de aplicativo. O botão está no painel "visão geral".

## <a name="redeploy-the-application"></a>Reimplantar o aplicativo

Em uma janela de terminal, recompile e reimplante seu aplicativo.

```bash
mvn clean install -DskipTests azure-webapp:deploy
```

Parabéns! Seu aplicativo agora está usando um banco de dados Postgres e todos os registros criados no aplicativo serão armazenados em Postgres, e não no banco de dados H2 na memória anterior. Para confirmar isso, você pode fazer um registro e reiniciar o serviço de aplicativo. Os registros ainda estarão lá quando o aplicativo for reiniciado.

## <a name="clean-up"></a>Limpeza

Se você não precisar desses recursos para outro tutorial (consulte as próximas etapas), poderá excluí-los executando o seguinte comando:

```bash
az group delete --name <your-resource-group>
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Implantar um aplicativo Java EE no Azure usando o Maven
> * Criar uma base de dados Postgres no Azure
> * Configurar o servidor WildFly para usar o postgres
> * Atualizar e reimplementar a aplicação
> * Executar testes de unidade em WildFly

Avance para o próximo tutorial para saber como mapear um nome DNS personalizado para seu aplicativo.

> [!div class="nextstepaction"]
> [Tutorial: mapear o nome DNS personalizado para seu aplicativo](../app-service-web-tutorial-custom-domain.md)

Ou então, confira outros recursos:

> [!div class="nextstepaction"]
> [Configurar aplicativo Java](configure-language-java.md)
