---
title: Criar aplicação web Java no Linux - serviço de aplicações do Azure
description: Neste início rápido, implemente o seu primeiro Hello World Java no Serviço de Aplicações do Azure no Linux, em apenas alguns minutos.
services: app-service\web
documentationcenter: ''
author: msangapu
manager: cfowler
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 12/10/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: 541d431c9387d512374047c4cc004e1a237e1ef1
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57406529"
---
# <a name="quickstart-create-a-java-app-in-app-service-on-linux"></a>Início rápido: Criar uma aplicação Java no serviço de aplicações no Linux

O [Serviço de Aplicações no Linux](app-service-linux-intro.md) oferece um serviço de alojamento na Web altamente dimensionável e com aplicação de patches automática através do sistema operativo Linux. Este início rápido mostra como utilizar o [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) com o [Plug-in do Maven para aplicações Web do Azure (pré-visualização)](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) para implementar um ficheiro de arquivo (WAR) de web de Java.

![Aplicação de exemplo em execução no Azure](media/quickstart-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Criar uma aplicação em Java

Execute o seguinte comando Maven na linha de comandos Cloud Shell para criar uma nova aplicação com o nome `helloworld`:

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
    <!-- Deploy to Tomcat in App Service Linux           -->
    <!--*************************************************-->
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.5.3</version>
        <configuration>
            <!-- App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
   
            <!-- Java Runtime Stack for App on Linux-->
            <linuxRuntime>tomcat 8.5-jre8</linuxRuntime> 
        </configuration>
    </plugin>
</plugins>
```    


> [!NOTE] 
> Neste artigo, estamos a trabalhar apenas com aplicações Java em pacotes de ficheiros WAR. O plug-in também suporta aplicações Web JAR, visite [Implementar um ficheiro JAR do Java SE no Serviço de Aplicações no Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) para experimentar.


Atualize os seguintes marcadores de posição na configuração do plug-in:

| Marcador de posição | Descrição |
| ----------- | ----------- |
| `RESOURCEGROUP_NAME` | Nome do novo grupo de recursos no qual pretende criar a sua aplicação. Ao colocar todos os recursos de uma aplicação num grupo, pode geri-los em conjunto. Por exemplo, eliminar o grupo de recursos eliminará todos os recursos associados à aplicação. Atualize este valor com um nome exclusivo do novo grupo de recursos, como, por exemplo, *TestResources*. Irá utilizar este nome de grupo de recursos para limpar todos os recursos do Azure numa secção posterior. |
| `WEBAPP_NAME` | O nome da aplicação vai fazer parte do nome de anfitrião para a aplicação quando implementada no Azure (WEBAPP_NAME.azurewebsites.net). Atualizar este valor com um nome exclusivo para a nova aplicação de serviço de aplicações, que irá alojar a aplicação de Java, por exemplo *contoso*. |
| `REGION` | Uma região do Azure onde a aplicação está alojada, por exemplo `westus2`. Pode obter uma lista de regiões do Cloud Shell ou da CLI com o comando `az account list-locations`. |

## <a name="deploy-the-app"></a>Implementar a aplicação

Implemente a sua aplicação Java no Azure com o seguinte comando:

```bash
mvn package azure-webapp:deploy
```

Uma vez concluída a implementação, navegue para a aplicação implementada com o seguinte URL no seu browser, por exemplo `http://<webapp>.azurewebsites.net/helloworld`. 

![Aplicação de exemplo em execução no Azure](media/quickstart-java/java-hello-world-in-browser-curl.png)

**Parabéns!** Implementou a sua primeira aplicação Java no Serviço de Aplicações no Linux.


[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]


## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, utilizou Maven para criar uma aplicação de Java, configurada o [Plug-in do Maven para aplicações Web do Azure](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin), em seguida, implementou uma aplicação de Java de arquivo morto compactado de web para o serviço de aplicações no Linux. Consulte os seguintes tutoriais e artigos de instruções para obter mais informações, alojamento de aplicações Java no serviço de aplicações no Linux.

- [Tutorial: Implementar um Java aplicação empresarial com PostgreSQL](tutorial-java-enterprise-postgresql-app.md)
- [Configurar uma origem de dados do Tomcat](app-service-linux-java.md#tomcat)
- [CI/CD com Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)
- [Configurar as ferramentas de monitorização de desempenho de aplicações](how-to-java-apm-monitoring.md)
