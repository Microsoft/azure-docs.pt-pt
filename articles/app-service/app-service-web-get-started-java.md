---
title: 'Quickstart: Criar uma aplicação Java no Windows'
description: Implemente o seu primeiro Serviço de Aplicações Java Hello World para Azure no Windows em minutos. O Azure Web App Plugin para Maven torna conveniente implementar aplicações Java.
keywords: azure, serviço de aplicações, web app, janelas, java, maven, quickstart
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: ca3c7d6bc6621c4b82a44431ae313384c1653f79
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324238"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-windows"></a>Início Rápido: Criar uma aplicação Java no Serviço de Aplicações do Azure no Windows

> [!NOTE]
> Este artigo implementa uma aplicação no Serviço de Aplicações no Windows. Para implementar no Serviço de Aplicações em _Linux,_ consulte [a aplicação Web Create Java no Linux](./containers/quickstart-java.md).
>

O [Serviço de Aplicações do Azure](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática.  Este quickstart mostra como usar o [CLI Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) com o [Azure Web App Plugin para Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) implementar um ficheiro java web archive (WAR).

> [!NOTE]
> O mesmo também pode ser feito usando IDEs populares como IntelliJ e Eclipse. Consulte os nossos documentos semelhantes no [Azure Toolkit para IntelliJ Quickstart](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) ou [Azure Toolkit para Eclipse Quickstart](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app).
>
![App de amostra em execução no Azure App Service](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

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

O processo de implementação para o Azure App Service pode recolher automaticamente as suas credenciais Azure do CLI Azure. Se não tiver o Azure CLI instalado, o plugin Maven iniciará sessão com Oauth ou início de sessão do dispositivo. Verifique os detalhes da [autenticação com plugins Maven,](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication) se necessário.

Pode executar o seguinte comando maven no Comando De comando Para configurar a implementação, escolher **'2'** para o sistema operativo do **windows** no primeiro passo e, em seguida, aceitar as configurações predefinidas pressionando **ENTER** até obter o pedido **de Confirmação (Y/N)** e, em seguida, prima **'y'** e a configuração é feita. 

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```

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
> [Dei conta de um problema.](https://www.research.net/r/javae2e?tutorial=app-service-web-get-started-java&step=config)

## <a name="deploy-the-app"></a>Implementar a aplicação

O processo de implantação para o Azure App Service utiliza credenciais de conta do Azure CLI. [Inscreva-se com o CLI Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) antes de continuar.

```azurecli
az login
```
Em seguida, pode implementar a sua aplicação Java para Azure utilizando o seguinte comando:

```bash
mvn package azure-webapp:deploy
```

Uma vez concluída a implementação, navegue para a aplicação implementada com o seguinte URL no seu browser, por exemplo `http://<webapp>.azurewebsites.net/`.

![App de amostra em execução no Azure App Service](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

**Parabéns!** Implementou a sua primeira aplicação Java para o Serviço de Aplicações no Windows.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

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
> [Mapear domínio personalizado](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre os plugins maven para Azure](https://github.com/microsoft/azure-maven-plugins)
