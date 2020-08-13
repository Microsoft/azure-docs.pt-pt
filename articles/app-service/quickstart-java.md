---
title: 'Quickstart: Criar uma aplicação Java no Azure App Service'
description: Implemente o seu primeiro Serviço de Aplicações Java Hello World para Azure em minutos. O Azure Web App Plugin para Maven torna conveniente implementar aplicações Java.
keywords: azure, serviço de aplicações, web app, janelas, linux, java, maven, quickstart
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 274228ea5aa9ac9de9725176c8b6221ee9e9542e
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88182702"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Quickstart: Criar uma aplicação Java no Azure App Service

O [Serviço de Aplicações do Azure](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática.  Este quickstart mostra como usar o [CLI Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) com o [Azure Web App Plugin para Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) implementar um ficheiro java web archive (WAR).

> [!NOTE]
> Neste artigo, estamos a trabalhar apenas com aplicações Java em pacotes de ficheiros WAR. O plug-in também suporta aplicações Web JAR, visite [Implementar um ficheiro JAR do Java SE no Serviço de Aplicações no Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) para experimentar.

> [!NOTE]
> O mesmo também pode ser feito usando IDEs populares como IntelliJ e Eclipse. Consulte os nossos documentos semelhantes no [Azure Toolkit para IntelliJ Quickstart](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) ou [Azure Toolkit para Eclipse Quickstart](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app).
>
![App de amostra em execução no Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

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

O processo de implementação para o Azure App Service pode recolher automaticamente as suas credenciais Azure do Azure CLI. O plugin Maven iniciará sessão com Oauth ou início de sessão do dispositivo se o Azure CLI não for instalado localmente. Verifique os detalhes da [autenticação com plugins Maven,](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication) se necessário.

Pode executar o comando maven abaixo para configurar a implantação
```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```

::: zone pivot="platform-windows" 
Ser-lhe-á pedido que selecione 
* **OS(Padrão: `linux` )**
* **Versão Java (Padrão: `1.8` )**
* **Recipiente web (Predefinição: `tomcat 8.5` )** 
 
Tenha cuidado ao inserir **`2`** para escolher o sistema operativo do **windows** no primeiro passo. As outras configurações podem ser deixadas por defeito premindo **ENTER**. Prima finalmente **`Y`** a solicitação **de Confirmação (Y/N)** para completar a configuração.

Um processo de amostra parece:

```console
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
Enter index to use: 2
Define value for javaVersion(Default: 1.8): 
1. 1.7
2. 1.7.0_191_ZULU
3. 1.7.0_51
4. 1.7.0_71
5. 1.7.0_80
6. 1.8 [*]
7. 1.8.0_102
8. 1.8.0_111
9. 1.8.0_144
10. 1.8.0_172
11. 1.8.0_172_ZULU
12. 1.8.0_181
13. 1.8.0_181_ZULU
14. 1.8.0_202
15. 1.8.0_202_ZULU
16. 1.8.0_25
17. 1.8.0_60
18. 1.8.0_73
19. 1.8.0_92
20. 11
21. 11.0.2_ZULU
Enter index to use:
Define value for webContainer(Default: tomcat 8.5): 
1. jetty 9.1
2. jetty 9.1.0.20131115
3. jetty 9.3
4. jetty 9.3.13.20161014
5. tomcat 7.0
6. tomcat 7.0.50
7. tomcat 7.0.62
8. tomcat 8.0
9. tomcat 8.0.23
10. tomcat 8.5 [*]
11. tomcat 8.5.20
12. tomcat 8.5.31
13. tomcat 8.5.34
14. tomcat 8.5.37
15. tomcat 8.5.6
16. tomcat 9.0
17. tomcat 9.0.0
18. tomcat 9.0.12
19. tomcat 9.0.14
20. tomcat 9.0.8
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1590394316693
ResourceGroup : helloworld-1590394316693-rg
Region : westeurope
PricingTier : PremiumV2_P1v2
OS : Windows
Java : 1.8
WebContainer : tomcat 8.5
Deploy to slot : false
Confirm (Y/N)? :
[INFO] Saving configuration to pom.
```
::: zone-end
::: zone pivot="platform-linux"  

Ser-lhe-á pedido que selecione 
* **OS(Padrão: `linux` )**
* **Versão Java (Padrão: `Java 8` )**
* **Recipiente web (Predefinição: `Tomcat 8.5` )** 

Todas as configurações podem ser deixadas predefinidos premindo **ENTER**. Prima finalmente **`Y`** a solicitação **de Confirmação (Y/N)** para completar a configuração.
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
::: zone-end

Pode modificar as configurações do Serviço de Aplicações diretamente no `pom.xml` seu, se necessário, algumas comuns estão listadas abaixo:

 Propriedade | Necessário | Descrição | Versão
---|---|---|---
`<schemaVersion>` | false | Especifique a versão do esquema de configuração. Os valores suportados são: `v1` `v2` . . | 1.5.2
`<resourceGroup>` | true | Grupo de Recursos Azure para a sua Aplicação Web. | 0.1.0+
`<appName>` | true | O nome da sua Aplicação Web. | 0.1.0+
`<region>` | true | Especifica a região onde a sua Web App será hospedada; o valor predefinido é **westeurope**. Todas as regiões válidas na secção [Regiões Apoiadas.](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) | 0.1.0+
`<pricingTier>` | false | O nível de preços da sua Web App. O valor predefinido é **P1V2**.| 0.1.0+
`<runtime>` | true | A configuração do ambiente de tempo de execução, pode ver o detalhe [aqui.](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) | 0.1.0+
`<deployment>` | true | A configuração de implementação, pode ver os detalhes [aqui.](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) | 0.1.0+

Tenha cuidado com os valores de `<appName>` e `<resourceGroup>` ( e `helloworld-1590394316693` `helloworld-1590394316693-rg` consequentemente na demonstração), eles serão usados mais tarde.

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>Implementar a aplicação

O processo de implantação para o Azure App Service utiliza credenciais de conta do Azure CLI. [Inscreva-se com o CLI Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) antes de continuar.

```azurecli
az login
```
Em seguida, pode implementar a sua aplicação Java para Azure utilizando o seguinte comando:

```bash
mvn package azure-webapp:deploy
```

Uma vez concluída a implementação, a sua aplicação estará pronta `http://<appName>.azurewebsites.net/` em ( `http://helloworld-1590394316693.azurewebsites.net` na demonstração). Abra o url com o seu navegador web local, você deve ver

![App de amostra em execução no Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Parabéns!** Implementou a sua primeira aplicação Java para o Serviço de Aplicações.

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
> [Azure para Java Developers Resources](/java/azure/)

> [!div class="nextstepaction"]
> [Configure a app Java](configure-language-java.md)

> [!div class="nextstepaction"]
> [CI/CD com o Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Mapear domínio personalizado](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre os plugins maven para Azure](https://github.com/microsoft/azure-maven-plugins)
