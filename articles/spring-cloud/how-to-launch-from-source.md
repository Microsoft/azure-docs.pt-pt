---
title: Como - Lançar a sua aplicação Cloud primavera a partir do código fonte
description: Neste arranque rápido, aprenda a lançar a sua aplicação Azure Spring Cloud diretamente a partir do seu código fonte
author: MikeDodaro
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/03/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: a6710a15bd0637eead0051ebb70a7cdd8bb8aa58
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879303"
---
# <a name="how-to-launch-your-spring-cloud-application-from-source-code"></a>Como lançar a sua aplicação Cloud Spring a partir do código fonte

**Este artigo aplica-se a:** ✔️ Java

A Azure Spring Cloud permite aplicações de microserviços baseadas em Nuvem de primavera no Azure.

Pode lançar aplicações diretamente a partir do código fonte java ou a partir de um JAR pré-construído. Este artigo explica os procedimentos de implantação.

Este quickstart explica como:

> [!div class="checklist"]
> * Prestação de uma instância de serviço
> * Desa parte para um servidor de configuração para uma instância
> * Construir uma aplicação de microserviços localmente
> * Implementar cada microserviço
> * Atribua o ponto final público para a sua aplicação

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de que a sua subscrição Azure tem as dependências necessárias:

1. [Instalar o Git](https://git-scm.com/)
2. [Instalar JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [Instalar Maven 3.0 ou superior](https://maven.apache.org/download.cgi)
4. [Instalar a CLI do Azure](/cli/azure/install-azure-cli)
5. [Inscreva-se para uma subscrição do Azure](https://azure.microsoft.com/free/)

> [!TIP]
> O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo.  Tem ferramentas Azure comuns pré-instaladas, incluindo as versões mais recentes de Git, JDK, Maven e o Azure CLI. Se tiver iniciado sessão na sua subscrição Azure, lance a sua [Azure Cloud Shell](https://shell.azure.com) a partir de shell.azure.com.  Você pode saber mais sobre Azure Cloud Shell [lendo a nossa documentação](../cloud-shell/overview.md)

## <a name="install-the-azure-cli-extension"></a>Instale a extensão Azure CLI

Instale a extensão Azure Spring Cloud para o CLI Azure com o seguinte comando

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Prestação de uma instância de serviço utilizando o CLI Azure

Faça login no Azure CLI e escolha a sua subscrição ativa. 

```azurecli
az login
az account list -o table
az account set --subscription
```

Crie um grupo de recursos para conter o seu serviço Azure Spring Cloud. Pode saber mais sobre [os Grupos de Recursos Azure.](../azure-resource-manager/management/overview.md)

```azurecli
az group create --location eastus --name <resource group name>
```

Executar os seguintes comandos para providenciar uma instância de Azure Spring Cloud. Prepare um nome para o seu serviço Azure Spring Cloud. O nome deve ter entre 4 e 32 caracteres e pode conter apenas letras minúsculas, números e hífenes. O primeiro carácter do nome de serviço deve ser uma letra e o último caractere deve ser uma letra ou um número.

```azurecli
az spring-cloud create -n <resource name> -g <resource group name>
```

A instância de serviço levará cerca de cinco minutos para ser implantada.

Desaprove o nome do grupo de recursos predefinidos e o nome da instância Azure Spring Cloud utilizando os seguintes comandos:

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

## <a name="create-the-azure-spring-cloud-application"></a>Crie a aplicação Azure Spring Cloud

O seguinte comando cria uma aplicação Azure Spring Cloud na sua subscrição.  Isto cria um serviço vazio ao qual podemos fazer o upload da nossa aplicação.

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>Implemente a sua aplicação Cloud primavera

Pode implementar a sua aplicação a partir de um JAR pré-construído ou de um repositório Gradle ou Maven.  Encontre instruções para cada caso abaixo.

### <a name="deploy-a-built-jar"></a>Implementar um JAR construído

Para implantar a partir de um JAR construído sobre a sua máquina local, [certifique-se](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven)de que a sua construção produz um JAR gordo .

Para implantar o FAT-JAR para uma implementação ativa

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR e.g. "target\hellospring-0.0.1-SNAPSHOT.jar">
```

Para implantar o FAT-JAR para uma implantação específica

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-fat-JAR e.g. "target\hellospring-0.0.1-SNAPSHOT.jar">
```

### <a name="deploy-from-source-code"></a>Implantação a partir do código fonte

Azure Spring Cloud usa [kpack](https://github.com/pivotal/kpack) para construir o seu projeto.  Pode utilizar o Azure CLI para carregar o seu código fonte, construir o seu projeto utilizando o kpack e implantá-lo na aplicação-alvo.

> [!WARNING]
> O projeto deve produzir apenas um ficheiro JAR com `main-class` uma entrada no in `MANIFEST.MF` `target` (para implantações de Maven) ou `build/libs` (para implantações gradle).  Vários ficheiros JAR com `main-class` entradas farão com que a implementação falhe.

Para projetos mono módulo Maven / Gradle:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

Para projetos Maven / Gradle com vários módulos, repita para cada módulo:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name> --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>Mostrar registos de implantação

Reveja os registos de construção kpack utilizando o seguinte comando:

```azurecli
az spring-cloud app show-deploy-log -n <app-name> [-d <deployment-name>]
```

> [!NOTE]
> Os registos kpack só mostrarão a mais recente implantação se essa implementação for construída a partir da fonte utilizando o kpack.

## <a name="assign-a-public-endpoint-to-gateway"></a>Atribuir um ponto final público ao gateway

1. Abra a página **do Painel de Aplicações.**
2. Selecione a `gateway` aplicação para mostrar a página **'Detalhes da Aplicação'.**
3. Selecione **Atribuir ponto final** para atribuir um ponto final público ao gateway. A implementação pode demorar alguns minutos. 
4. Introduza o IP público atribuído no seu navegador para ver a sua aplicação de execução.

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a:

> [!div class="checklist"]
> * Prestação de uma instância de serviço
> * Desa parte para um servidor de configuração para uma instância
> * Construir uma aplicação de microserviços localmente
> * Implementar cada microserviço
> * Editar variáveis ambientais para aplicações
> * Atribua IP público para o seu gateway de aplicação

> [!div class="nextstepaction"]
> [Troncos de nuvem de primavera, métricas, rastreio](spring-cloud-quickstart-logs-metrics-tracing.md)

Mais amostras estão disponíveis no GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).