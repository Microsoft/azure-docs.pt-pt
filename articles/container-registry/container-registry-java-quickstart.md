---
title: Quickstart - Construa e empurre imagens de contentores de Java para o registo do contentor de Azure usando Maven e Jib
description: Construa uma aplicação Java contentorizada e empurre-a para o Registo do Contentor Azure utilizando o plugin Maven Jib.
author: KarlErickson
ms.custom: devx-track-java, devx-track-azurecli
ms.author: karler
ms.topic: quickstart
ms.date: 02/26/2020
ms.openlocfilehash: 9e400ee0bae2690a84f9cfd3f6a76359e08eabc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92018343"
---
# <a name="quickstart-build-and-push-java-container-images-to-azure-container-registry"></a>Quickstart: Construa e empurre imagens de contentores de Java para o registo do contentor de Azure

Este quickstart mostra-lhe como construir uma aplicação Java contentorizada e empurrá-la para o Registo de Contentores Azure usando o plugin Maven Jib. A utilização de Maven e Jib é um exemplo de utilização de ferramentas de desenvolvimento para interagir com um registo de contentores Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os seus [benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita do Azure](https://azure.microsoft.com/pricing/free-trial).
* [A Interface Azure Command-Line (CLI)](/cli/azure/overview).
* Um Java Development Kit (JDK) suportado. Para mais informações sobre os JDKs que estão disponíveis para serem utilizados quando se programa no Azure, veja <https://aka.ms/azure-jdks>.
* Ferramenta de construção [Maven](http://maven.apache.org) da Apache (versão 3 ou superior).
* Um cliente [Git](https://git-scm.com).
* Um cliente [Docker](https://www.docker.com).
* [O ajudante credencial ACR Docker.](https://github.com/Azure/acr-docker-credential-helper)

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Criar a aplicação Web "Spring Boot on Docker Getting Started" (Introdução ao Spring Boot no Docker)

Os seguintes passos guiam-no ao longo da criação de uma aplicação Web Spring Boot e do teste da mesma a nível local.

1. A partir do pedido de comando, utilize o seguinte comando para clonar a Bota de primavera no projeto de amostra [de Docker Getting Started.](https://github.com/spring-guides/gs-spring-boot-docker)

   ```bash
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Mude o diretório para o projeto concluído.

   ```bash
   cd gs-spring-boot-docker/complete
   ```

1. Utilize o Maven para criar e executar o exemplo de aplicação.

   ```bash
   mvn package spring-boot:run
   ```

1. Teste a aplicação Web ao navegar para `http://localhost:8080` ou com o seguinte comando `curl`:

   ```bash
   curl http://localhost:8080
   ```

Deve ver a seguinte mensagem exibida: **Hello Docker World**

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Criar um registo de contentor do Azure com a CLI do Azure

Em seguida, irá criar um grupo de recursos Azure e o seu ACR utilizando os seguintes passos:

1. Inicie sessão na sua conta Azure utilizando o seguinte comando:

   ```azurecli
   az login
   ```

1. Especificar a subscrição do Azure para utilizar:

   ```azurecli
   az account set -s <subscription ID>
   ```

1. Crie um grupo de recursos para os recursos do Azure utilizados neste tutorial. No comando seguinte, certifique-se de substituir os espaços reservados pelo seu próprio nome de recurso e por uma localização como `eastus` .

   ```azurecli
   az group create \
       --name=<your resource group name> \
       --location=<location>
   ```

1. Crie um registo privado de contentores Azure no grupo de recursos, utilizando o seguinte comando. Certifique-se de que substitui os espaços reservados por valores reais. O tutorial emite o exemplo de aplicação sob a forma de imagem do Docker para este registo em passos posteriores.

   ```azurecli
   az acr create \
       --resource-group <your resource group name> \
       --location <location> \
       --name <your registry name> \
       --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry-via-jib"></a>Emitir a sua aplicação para o registo de contentor via Jib

Finalmente, irá atualizar a configuração do seu projeto e utilizar o pedido de comando para construir e implementar a sua imagem.

> [!NOTE]
> Para iniciar sessão no registo de contentores Azure que acabou de criar, terá de ter o daemon do Docker a funcionar. Para instalar o Docker na sua máquina, [aqui está a documentação oficial do Docker.](https://docs.docker.com/install/)

1. Faça login no seu Registo de Contentores Azure a partir do CLI Azure utilizando o seguinte comando. Certifique-se de substituir o espaço reservado pelo seu próprio nome de registo.

   ```azurecli
   az configure --defaults acr=<your registry name>
   az acr login
   ```

   O `az configure` comando define o nome de registo predefinido para usar com `az acr` comandos.

1. Navegue para o diretório de projeto concluído da sua aplicação Spring Boot (por exemplo, "*C:\SpringBoot\gs-spring-boot-docker\complete*" ou "*/users/robert/SpringBoot/gs-spring-boot-docker/complete*") e abra o ficheiro *pom.xml* com um editor de texto.

1. Atualize a `<properties>` recolha no ficheiro *pom.xml* com o seguinte XML. Substitua o espaço reservado pelo seu nome de registo e adicione uma `<jib-maven-plugin.version>` propriedade com `2.2.0` valor, ou uma versão mais recente do [jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin).

   ```xml
   <properties>
      <docker.image.prefix><your registry name>.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
      <jib-maven-plugin.version>2.2.0</jib-maven-plugin.version>
   </properties>
   ```

1. Atualize a `<plugins>` recolha no ficheiro *pom.xml* de modo a que o `<plugin>` elemento contenha e a entrada para o `jib-maven-plugin` , como mostra o exemplo seguinte. Note que estamos a usar uma imagem base do Microsoft Container Registry (MCR): `mcr.microsoft.com/java/jdk:8-zulu-alpine` , que contém um JDK oficialmente suportado para Azure. Para outras imagens base mcR com JDKs oficialmente suportados, consulte [Java SE JDK,](https://hub.docker.com/_/microsoft-java-jdk) [Java SE JRE,](https://hub.docker.com/_/microsoft-java-jre) [Java SE Headless JRE,](https://hub.docker.com/_/microsoft-java-jre-headless)e [Java SE JDK e Maven.](https://hub.docker.com/_/microsoft-java-maven)

   ```xml
   <plugin>
     <artifactId>jib-maven-plugin</artifactId>
     <groupId>com.google.cloud.tools</groupId>
     <version>${jib-maven-plugin.version}</version>
     <configuration>
        <from>
            <image>mcr.microsoft.com/java/jdk:8-zulu-alpine</image>
        </from>
        <to>
            <image>${docker.image.prefix}/${project.artifactId}</image>
        </to>
     </configuration>
   </plugin>
   ```

1. Navegue para o diretório de projeto concluído da sua aplicação Spring Boot, execute o seguinte comando para criar a imagem e emita-a para o registo:

   ```bash
   az acr login && mvn compile jib:build
   ```

> [!NOTE]
>
> Por razões de segurança, a credencial criada por `az acr login` é válida apenas por 1 hora. Se receber um erro *não autorizado 401,* pode executar novamente o `az acr login -n <your registry name>` comando para reautenticar.

## <a name="verify-your-container-image"></a>Verifique a imagem do seu recipiente

Parabéns! Agora tem a sua App Java contentorizada construída em Azure suportado JDK empurrado para o seu ACR. Pode agora testar a imagem implantando-a no Serviço de Aplicações Azure ou puxando-a para local com comando (substituindo o espaço reservado):

```bash
docker pull <your registry name>.azurecr.io/gs-spring-boot-docker
```

## <a name="next-steps"></a>Passos seguintes

Para outras versões das imagens oficiais da base java suportadas pela Microsoft, consulte:

* [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk)
* [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre)
* [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless)
* [Java SE JDK e Maven](https://hub.docker.com/_/microsoft-java-maven)

Para saber mais sobre o Spring e o Azure, avance para o centro de documentação relativa ao Spring no Azure.

> [!div class="nextstepaction"]
> [Spring no Azure](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>Recursos Adicionais

Para obter mais informações, veja os seguintes recursos:

* [Azure para Desenvolvedores de Java](/azure/java)
* [Working with Azure DevOps and Java](/azure/devops/java) (Trabalhar com o Azure DevOps e Java)
* [Introdução ao Spring Boot no Docker](https://spring.io/guides/gs/spring-boot-docker)
* [Spring Initializr](https://start.spring.io)
* [Deploy a Spring Boot Application to the Azure App Service](/azure/developer/java/spring-framework/deploy-spring-boot-java-app-on-linux#configure-maven-to-build-image-to-your-azure-container-registry) (Implementar uma Aplicação Spring Boot no Serviço de Aplicações do Azure)
* [Utilizar uma imagem personalizada do Docker para as Aplicações Web do Azure no Linux](../app-service/tutorial-custom-container.md)
