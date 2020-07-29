---
title: 'Quickstart: Criar uma aplicação Linux Java'
description: Começa com as aplicações Linux no Azure App Service, implementando a sua primeira aplicação Java num contentor Linux no Serviço de Aplicações.
keywords: azure, serviço de aplicações, web app, linux, java, maven, quickstart
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/27/2019
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 92bfaa7718a8e094fcfc1479e9dba6790438f7a7
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87322708"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-linux"></a>Quickstart: Criar uma aplicação Java no Azure App Service em Linux

O [Serviço de Aplicações no Linux](app-service-linux-intro.md) oferece um serviço de alojamento na Web altamente dimensionável e com aplicação de patches automática através do sistema operativo Linux. Este quickstart mostra como usar o [CLI Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) com o [Azure Web App Plugin para Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) para implementar um ficheiro java web archive (WAR) no sistema operativo Linux.

> [!NOTE]
>
> O mesmo também pode ser feito usando IDEs populares como IntelliJ, Eclipse e VS Code. Consulte os nossos documentos semelhantes no [Azure Toolkit para IntelliJ Quickstart](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app), [Azure Toolkit para Eclipse Quickstart](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app) ou [VS Code Quickstart](https://code.visualstudio.com/docs/java/java-webapp).
>
![App de amostra em execução no Azure App Service](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Criar uma aplicação em Java

Execute o seguinte comando Maven no pedido da Cloud Shell para criar uma nova aplicação chamada `helloworld` :

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```
Em seguida, mude o seu diretório de trabalho para a pasta do projeto:

```bash
cd helloworld
```

## <a name="configure-the-maven-plugin"></a>Configurar o plug-in do Maven

O processo de implementação para o Azure App Service pode recolher automaticamente as suas credenciais Azure do CLI Azure. Se não tiver o Azure CLI instalado, o plugin Maven iniciará sessão com Oauth ou início de sessão do dispositivo. Verifique os detalhes da [autenticação com plugins Maven,](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication) se necessário.

Para configurar a implementação, executar o comando maven no Pedido de Comando e utilizar as configurações predefinidas premindo **ENTER** até obter o pedido **de Confirmação (Y/N)** e, em seguida, prima **'y'** e a configuração é feita. 
```cmd
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```
Um processo de amostra parece:

```cmd
~@Azure:~/helloworld$ mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.9.1:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use:
Define value for javaVersion(Default: jre8):
1. Java 11
2. Java 8 [*]
Enter index to use:
Define value for runtimeStack(Default: TOMCAT 8.5):
1. TOMCAT 9.0
2. TOMCAT 8.5 [*]
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1558400876966
ResourceGroup : helloworld-1558400876966-rg
Region : westeurope
PricingTier : Premium_P1V2
OS : Linux
RuntimeStack : TOMCAT 8.5-jre8
Deploy to slot : false
Confirm (Y/N)? : Y
```

> [!NOTE]
> Neste artigo, estamos a trabalhar apenas com aplicações Java em pacotes de ficheiros WAR. O plug-in também suporta aplicações Web JAR, visite [Implementar um ficheiro JAR do Java SE no Serviço de Aplicações no Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) para experimentar.

Aberto `pom.xml` para ver a configuração atualizada.

```bash
code pom.xml
```

Pode modificar as configurações do Serviço de Aplicações diretamente no seu ficheiro pom, se necessário, algumas comuns estão listadas abaixo:

 Propriedade | Obrigatório | Descrição | Versão
---|---|---|---
`<schemaVersion>` | false | Especifique a versão do esquema de configuração. Os valores suportados são: `v1` `v2` . . | 1.5.2
`<resourceGroup>` | true | Grupo de Recursos Azure para a sua Aplicação Web. | 0.1.0+
`<appName>` | true | O nome da sua Aplicação Web. | 0.1.0+
`<region>` | true | Especifica a região onde a sua Web App será hospedada; o valor predefinido é **westeurope**. Todas as regiões válidas na secção [Regiões Apoiadas.](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) | 0.1.0+
`<pricingTier>` | false | O nível de preços da sua Web App. O valor predefinido é **P1V2**.| 0.1.0+
`<runtime>` | true | A configuração do ambiente de tempo de execução, pode ver o detalhe [aqui.](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) | 0.1.0+
`<deployment>` | true | A configuração de implementação, pode ver os detalhes [aqui.](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) | 0.1.0+

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=config)

## <a name="deploy-the-app"></a>Implementar a aplicação

Implemente a sua aplicação Java no Azure com o seguinte comando:

```bash
mvn package azure-webapp:deploy
```

Uma vez concluída a implementação, navegue para a aplicação implementada com o seguinte URL no seu browser, por exemplo `http://<webapp>.azurewebsites.net`. 

![App de amostra em execução no Azure App Service](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Parabéns!** Implementou a sua primeira aplicação Java no Serviço de Aplicações no Linux.

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Limpar os recursos

Nos passos anteriores, criou os recursos do Azure num grupo de recursos. Se não espera precisar destes recursos no futuro, elimine o grupo de recursos do portal ou executando o seguinte comando na Cloud Shell:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Este comando pode demorar alguns minutos a ser executado.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ligue à Base de Dados Azure SQL com a Java](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Ligue-se ao Azure DB para o MySQL com o Java](/azure/mysql/connect-java)

> [!div class="nextstepaction"]
> [Ligue-se ao Azure DB para PostgreSQL com Java](/azure/postgresql/connect-java)

> [!div class="nextstepaction"]
> [Configure a app Java](configure-language-java.md)

> [!div class="nextstepaction"]
> [CI/CD com o Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Outro Azure para Java Developers Resources](/java/azure/)

> [!div class="nextstepaction"]
> [Saiba mais sobre os plugins maven para Azure](https://github.com/microsoft/azure-maven-plugins)
