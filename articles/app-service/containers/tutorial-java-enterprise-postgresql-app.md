---
title: Criar aplicação web empresarial de Java no Linux - serviço de aplicações do Azure | Documentos da Microsoft
description: Saiba como colocar uma aplicação de Java Enterprise funcionar no Wildfly no serviço de aplicações do Azure no Linux.
author: JasonFreeberg
manager: routlaw
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 11/13/2018
ms.author: jafreebe
ms.custom: seodec18
ms.openlocfilehash: a4bf2ef252b5a948f2e3614e3e7cf64a4cb19277
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772063"
---
# <a name="tutorial-build-a-java-ee-and-postgres-web-app-in-azure"></a>Tutorial: Criar uma aplicação de web de Java EE e Postgres no Azure

Neste tutorial mostram-lhe como criar uma aplicação web de Java Enterprise Edition (EE) no serviço de aplicações do Azure e ligá-la a uma base de dados Postgres. Quando tiver terminado, terá uma [WildFly](https://www.wildfly.org/about/) aplicação armazena os dados na [base de dados do Azure para Postgres](https://azure.microsoft.com/services/postgresql/) em execução no Azure [serviço de aplicações no Linux](app-service-linux-intro.md).

Neste tutorial, vai aprender a:
> [!div class="checklist"]
> * Implementar uma aplicação de Java EE para o Azure com o Maven
> * Criar uma base de dados Postgres no Azure
> * Configurar o servidor de WildFly utilizar Postgres
> * Atualizar e reimplementar a aplicação
> * Executar testes de unidade no WildFly

## <a name="prerequisites"></a>Pré-requisitos

1. [Transferir e instalar o Git](https://git-scm.com/)
2. [Transferir e instalar o Maven 3](https://maven.apache.org/install.html)
3. [Transferir e instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="clone-and-edit-the-sample-app"></a>Clonar e editar a aplicação de exemplo

Neste passo, irá clonar a aplicação de exemplo e configurar o modelo de objeto de projeto Maven (POM ou pom. xml) para a implementação.

### <a name="clone-the-sample"></a>Clonar o exemplo

Na janela de terminal, navegue para um diretório de trabalho e clone [o repositório de exemplo](https://github.com/Azure-Samples/wildfly-petstore-quickstart).

```bash
git clone https://github.com/Azure-Samples/wildfly-petstore-quickstart.git
```

### <a name="update-the-maven-pom"></a>Atualizar o Maven POM

Atualize o plug-in do Maven do Azure com o desejado nome e grupo de recursos do seu serviço de aplicações. Não é necessário criar o plano do serviço de aplicações ou a instância previamente. O plug-in do Maven irá criar o grupo de recursos e o serviço de aplicações, caso ainda não exista. 

Pode rolar para baixo para o `<plugins>` secção do _pom_, 200, para fazer as alterações de linha. 

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
Substitua `YOUR_APP_NAME` e `YOUR_RESOURCE_GROUP` com os nomes do seu grupo de serviço de aplicações e recursos.

## <a name="build-and-deploy-the-application"></a>Criar e implementar a aplicação

Agora, irá utilizar o Maven para criar nosso aplicativo e implantá-lo no serviço de aplicações.

### <a name="build-the-war-file"></a>Criar o ficheiro. War

O POM neste projeto está configurado para empacotar a aplicação para um ficheiro WAR (arquivo Web). Crie a aplicação com o Maven:

```bash
mvn clean install -DskipTests
```

Os casos de teste neste aplicativo são criados para serem executados quando a aplicação é implementada em WildFly. Podemos irá ignorar os testes para criar localmente e execute os testes, uma vez que a aplicação é implementada no serviço de aplicações.

### <a name="deploy-to-app-service"></a>Implementar no Serviço de Aplicações

Agora que o WAR está pronta, podemos usar o plug-in do Azure para implementar no serviço de aplicações:

```bash
mvn azure-webapp:deploy
```

Quando termina, a implementação, avance para o passo seguinte.

### <a name="create-a-record"></a>Criar um registo

Abra um browser e navegue para `https://<your_app_name>.azurewebsites.net/`. Parabéns, implementou uma aplicação de Java EE para o serviço de aplicações do Azure!

Neste momento, a aplicação está a utilizar uma base de dados de H2 dentro da memória. Clique em "admin", na barra de navegação e crie uma nova categoria. O registo na base de dados na memória serão perdido se reiniciar a instância de serviço de aplicações. Nos passos seguintes, irá corrigir este problema através do aprovisionamento de uma base de dados de Postgres no Azure e configurar WildFly usá-lo.

![Localização do botão de administrador](media/tutorial-java-enterprise-postgresql-app/admin_button.JPG)

## <a name="provision-a-postgres-database"></a>Aprovisionar uma base de dados Postgres

Para aprovisionar um servidor de base de dados Postgres, abra um terminal e execute o seguinte comando com os seus valores pretendidos para o nome do servidor, o nome de utilizador, a palavra-passe e a localização. Utilize o mesmo grupo de recursos que pertença a seu serviço de aplicações. Mantenha uma nota da palavra-passe para utilizar mais tarde!

```bash
az postgres server create -n <desired-name> -g <same-resource-group> --sku-name GP_Gen4_2 -u <desired-username> -p <desired-password> -l <location>
```

Navegue para o Portal e pesquisa da base de dados Postgres. Quando o painel está a funcionar, copie os valores de "Nome de início de sessão de administrador do servidor" e "Nome do servidor", que irá precisar delas mais tarde.

### <a name="allow-access-to-azure-services"></a>Permitir acesso aos serviços do Azure

Na **segurança de ligação** painel do painel base de dados do Azure, ativar/desativar o botão "Permitir acesso aos serviços do Azure" para o **ON** posição.

![Permitir o acesso aos serviços do Azure](media/tutorial-java-enterprise-postgresql-app/postgress_button.JPG)

## <a name="update-your-java-app-for-postgres"></a>Atualizar a sua aplicação de Java para Postgres

Agora, faremos algumas alterações para a aplicação de Java para ativá-la utilizar a nossa base de dados Postgres.

### <a name="add-postgres-credentials-to-the-pom"></a>Adicionar credenciais de Postgres para o POM

Na _pom_, substitua os valores de marcador de posição com iniciais maiúsculas com o nome do servidor Postgres, o nome de início de sessão de administrador e a palavra-passe. Estes campos são dentro do plug-in do Azure Maven. (Não se esqueça de substituir `YOUR_SERVER_NAME`, `YOUR_PG_USERNAME`, e `YOUR_PG_PASSWORD` no `<value>` etiquetas... não está dentro do `<name>` etiquetas!)

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

### <a name="update-the-java-transaction-api"></a>Atualizar a transação de Java API

Em seguida, é necessário editar nossa configuração de transação de Java API (JPA) para que nosso aplicativo Java irá comunicar com Postgres em vez da base de dados na memória H2 que estivéssemos a utilizar anteriormente. Abra um editor _src/main/resources/META-INF/persistence.xml_. Substitua o valor de `<jta-data-source>` por `java:jboss/datasources/postgresDS`. O XML de JTA agora deve ter esta definição:

```xml
...
<jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
...
```

## <a name="configure-the-wildfly-application-server"></a>Configurar o servidor de aplicações WildFly

Antes de implantar nosso aplicativo reconfigurado, podemos tem de atualizar o servidor de aplicações WildFly com o módulo de Postgres e as respetivas dependências. Para configurar o servidor, precisamos quatro arquivos no `wildfly_config/` diretório:

- **postgresql-42.2.5.jar**: Este ficheiro. JAR é o controlador JDBC para Postgres. Para obter mais informações, consulte a [site oficial](https://jdbc.postgresql.org/index.html).
- **postgres-module.xml**: Esse arquivo XML declara um nome para o módulo de Postgres (org.postgres). Também especifica os recursos e as dependências necessários para o módulo a ser utilizado.
- **jboss_cli_commands.cl**: Este ficheiro contém comandos de configuração que serão executados para a CLI JBoss. Os comandos adicionar o módulo de Postgres para o servidor de aplicações WildFly, fornecem as credenciais, declare um nome JNDI, defina o limiar de tempo limite, etc. Se não estiver familiarizado com a CLI JBoss, consulte a [documentação oficial](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- **startup_script.sh**: Por fim, este script de shell será executado sempre que for iniciada a sua instância do serviço de aplicações. O script só executa uma função: encaminhando os comandos `jboss_cli_commands.cli` para a CLI JBoss.

Sugerimos altamente ler o conteúdo desses arquivos, especialmente _jboss_cli_commands.cli_.

### <a name="ftp-the-configuration-files"></a>Os ficheiros de configuração do FTP

Temos do conteúdo de FTP `wildfly_config/` para nossa instância de serviço de aplicações. Para obter as suas credenciais FTP, clique a **obter perfil de publicação** botão no painel do serviço de aplicações no portal do Azure. O nome de utilizador FTP e a palavra-passe será no documento XML transferido. Para obter mais informações sobre o perfil de publicação, consulte [este documento](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials).

Utilizar uma ferramenta FTP à sua escolha, transferir os ficheiros de quatro no `wildfly_config/` para `/home/site/deployments/tools/`. (Observe que não deve transferir o diretório, apenas os próprios arquivos.)


### <a name="finalize-app-service"></a>Finalizar o serviço de aplicações

No painel do serviço de aplicações, navegue para o painel "Definições da aplicação". Em "Runtime", defina o campo de "Arquivo de inicialização" `/home/site/deployments/tools/startup_script.sh`. Isto irá garantir que o script de shell é executado depois da instância de serviço de aplicações é criada, mas antes do WildFly servidor é iniciado.

Por fim, reinicie o serviço de aplicações. O botão é no painel de "Descrição geral".

## <a name="redeploy-the-application"></a>Voltar a implementar a aplicação

Numa janela do terminal, reconstruir e Reimplementar a sua aplicação.

```bash
mvn clean install -DskipTests azure-webapp:deploy
```

Parabéns! A aplicação está agora a utilizar uma base de dados Postgres e quaisquer registos criado na aplicação serão armazenados na Postgres, em vez do anterior H3 dentro da memória da base de dados. Para confirmar, pode fazer um registo e reinicie o serviço de aplicações. Os registos ainda estará lá quando seu aplicativo for reiniciado.

## <a name="clean-up"></a>Limpeza

Se não precisa destes recursos para outro tutorial (consulte os passos seguintes), pode eliminá-los ao executar o seguinte comando:

```bash
az group delete --name <your_resource_group> 
```

## <a name="next-steps"></a>Passos Seguintes

Agora que tem uma aplicação de Java EE implementada no serviço de aplicações, consulte a [Guia do Programador de Java Enterprise](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-java) para obter mais informações sobre a configuração de serviços de resolução de problemas e dimensionar a sua aplicação.
