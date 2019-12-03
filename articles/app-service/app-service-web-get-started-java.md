---
title: 'Início rápido: criar um aplicativo Java no Windows'
description: Implante seu primeiro Olá, Mundo Java para Azure App serviço no Windows em minutos. O plug-in do Maven para o serviço de aplicativo torna conveniente implantar aplicativos Java.
keywords: Azure, serviço de aplicativo, aplicativo Web, Windows, Java, Maven, início rápido
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: c511ed3f432c8e54a820c01839269712271774bd
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672165"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-windows"></a>Início rápido: criar um aplicativo Java no serviço de Azure App no Windows

> [!NOTE]
> Este artigo implementa uma aplicação no Serviço de Aplicações no Windows. Para implantar o serviço de aplicativo no _Linux_, consulte [criar aplicativo Web Java no Linux](./containers/quickstart-java.md).
>

O [Serviço de Aplicações do Azure](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática.  Este guia de início rápido mostra como usar o [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) com o [plug-in do Maven para Azure app Service](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) para implantar um arquivo War (Java Web Archive).

> [!NOTE]
> A mesma coisa também pode ser feita usando IDEs populares, como o IntelliJ e o eclipse. Confira nossos documentos semelhantes em [Azure Toolkit for IntelliJ início rápido](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) ou [Azure Toolkit for Eclipse início rápido](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app).
>
![Aplicativo de exemplo em execução no serviço Azure App](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Criar uma aplicação em Java

Execute o seguinte comando do Maven no prompt de Cloud Shell para criar um novo aplicativo chamado `helloworld`:

```bash
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp
```

## <a name="configure-the-maven-plugin"></a>Configurar o plug-in do Maven

Para implementar a partir do Maven, utilize o editor de código no Cloud Shell para abrir o ficheiro do projeto `pom.xml` no diretório `helloworld`. 

```bash
code pom.xml
```

Em seguida, adicione a seguinte definição de plug-in dentro do elemento `<build>` do ficheiro `pom.xml`.

```xml
<plugins>
    <!--*************************************************-->
    <!-- Deploy to Tomcat in App Service Windows         -->
    <!--*************************************************-->
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.8.0</version>
        <configuration>
            <!-- Specify v2 schema -->
            <schemaVersion>v2</schemaVersion>
            <!-- App information -->
            <subscriptionId>SUBSCRIPTION_ID</subscriptionId>
            <resourceGroup>RESOURCEGROUP_NAME</resourceGroup>
            <appName>WEBAPP_NAME</appName>
            <region>REGION</region>
            <!-- Java Runtime Stack for App Service on Windows-->
            <runtime>
                <os>windows</os>
                <javaVersion>1.8</javaVersion>
                <webContainer>tomcat 9.0</webContainer>
            </runtime>
            <deployment>
                <resources>
                    <resource>
                        <directory>${project.basedir}/target</directory>
                        <includes>
                            <include>*.war</include>
                        </includes>
                    </resource>
                </resources>
            </deployment>
        </configuration>
    </plugin>
</plugins>
```

> [!NOTE]
> Neste artigo, estamos a trabalhar apenas com aplicações Java em pacotes de ficheiros WAR. O plug-in também suporta aplicações Web JAR, visite [Implementar um ficheiro JAR do Java SE no Serviço de Aplicações no Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) para experimentar.


Atualize os seguintes marcadores de posição na configuração do plug-in:

| Marcador de posição | Descrição |
| ----------- | ----------- |
| `SUBSCRIPTION_ID` | A ID exclusiva da assinatura na qual você deseja implantar seu aplicativo. A ID da assinatura padrão pode ser encontrada no Cloud Shell ou CLI usando o comando `az account show`. Para todas as assinaturas disponíveis, use o comando `az account list`.|
| `RESOURCEGROUP_NAME` | Nome do novo grupo de recursos no qual criar seu aplicativo. Ao colocar todos os recursos de uma aplicação num grupo, pode geri-los em conjunto. Por exemplo, eliminar o grupo de recursos eliminará todos os recursos associados à aplicação. Atualize esse valor com um novo nome de grupo de recursos exclusivo, por exemplo, *MyResource*Group. Irá utilizar este nome de grupo de recursos para limpar todos os recursos do Azure numa secção posterior. |
| `WEBAPP_NAME` | O nome do aplicativo será parte do nome do host para o aplicativo quando implantado no Azure (WEBAPP_NAME. azurewebsites. net). Atualize esse valor com um nome exclusivo para o novo aplicativo do serviço de aplicativo, que hospedará seu aplicativo Java, por exemplo, *contoso*. |
| `REGION` | Uma região do Azure em que o aplicativo está hospedado, por exemplo, *westus2*. Pode obter uma lista de regiões do Cloud Shell ou da CLI com o comando `az account list-locations`. |

## <a name="deploy-the-app"></a>Implementar a aplicação

Implemente a sua aplicação Java no Azure com o seguinte comando:

```bash
mvn package azure-webapp:deploy
```

Uma vez concluída a implementação, navegue para a aplicação implementada com o seguinte URL no seu browser, por exemplo `http://<webapp>.azurewebsites.net/`.

![Aplicativo de exemplo em execução no serviço Azure App](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

**Parabéns!** Você implantou seu primeiro aplicativo Java no serviço de aplicativo no Windows.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Recursos do Azure para desenvolvedores de Java](/java/azure/)

> [!div class="nextstepaction"]
> [Mapear domínio personalizado](app-service-web-tutorial-custom-domain.md)
