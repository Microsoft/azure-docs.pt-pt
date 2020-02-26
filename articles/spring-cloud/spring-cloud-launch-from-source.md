---
title: Quickstart - Lance a sua aplicação Spring Cloud a partir do código fonte
description: Neste arranque rápido, aprenda a lançar a sua aplicação Azure Spring Cloud diretamente a partir do seu código fonte
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/30/2019
ms.author: brendm
ms.openlocfilehash: b506fdcdec1ae3e98c1a4afe9c5124e284ed4d99
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589016"
---
# <a name="quickstart-launch-your-spring-cloud-application-from-source-code"></a>Quickstart: Lance a sua aplicação Spring Cloud a partir do código fonte

A Nuvem de primavera Azure permite-lhe executar facilmente aplicações de microserviço baseadas em Nuvem de primavera no Azure.

A Nuvem de primavera Azure permite-lhe lançar a sua aplicação diretamente a partir do seu código fonte java ou de um JAR pré-construído. Este artigo acompanha-o através dos passos necessários.

Após este arranque rápido, aprenderá a:

> [!div class="checklist"]
> * Provisão de uma instância de serviço
> * Definir um servidor de configuração, por exemplo
> * Construir uma aplicação de microserviços localmente
> * Implementar cada microserviço
> * Atribuir ponto final público para a sua aplicação

## <a name="prerequisites"></a>Pré-requisitos

>[!Note]
> A Nuvem de primavera Azure é atualmente oferecida como uma pré-visualização pública. As ofertas de pré-visualização pública permitem aos clientes experimentar novas funcionalidades antes do seu lançamento oficial.  As funcionalidades e serviços de pré-visualização pública não se destinam à utilização da produção.  Para mais informações sobre suporte durante as pré-visualizações, por favor reveja as nossas [FAQ](https://azure.microsoft.com/support/faq/) ou preencha um pedido de [Suporte](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) para saber mais.

Antes de começar, certifique-se de que a sua subscrição Azure tem as dependências necessárias:

1. [Instalar o Git](https://git-scm.com/)
2. [Instalar JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [Instale Maven 3.0 ou acima](https://maven.apache.org/download.cgi)
4. [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Inscreva-se para uma subscrição do Azure](https://azure.microsoft.com/free/)

> [!TIP]
> O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo.  Tem ferramentas azure comuns pré-instaladas, incluindo as versões mais recentes de Git, JDK, Maven e o Azure CLI. Se estiver ligado à sua subscrição Azure, lance o seu [Azure Cloud Shell](https://shell.azure.com) a partir de shell.azure.com.  Você pode saber mais sobre Azure Cloud Shell lendo a [nossa documentação](../cloud-shell/overview.md)

## <a name="install-the-azure-cli-extension"></a>Instalar a extensão Azure CLI

Instale a extensão Azure Spring Cloud para o Azure CLI com o seguinte comando

```Azure CLI
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Prever uma instância de serviço utilizando o Azure CLI

Inicie sessão no Azure CLI e escolha a sua subscrição ativa. Certifique-se de escolher a subscrição ativa que está listada para Azure Spring Cloud

```Azure CLI
az login
az account list -o table
az account set --subscription
```

Crie um grupo de recursos para conter o seu serviço Azure Spring Cloud. Pode saber mais sobre os Grupos de [Recursos Azure.](../azure-resource-manager/management/overview.md)

```azurecli
az group create --location eastus --name <resource group name>
```

Executar os seguintes comandos para fornecer uma instância de Azure Spring Cloud. Prepare um nome para o seu serviço Azure Spring Cloud. O nome deve ter entre 4 e 32 caracteres de comprimento e só pode conter letras minúsculas, números e hífenes. O primeiro carácter do nome de serviço deve ser uma letra e o último personagem deve ser uma letra ou um número.

```azurecli
az spring-cloud create -n <resource name> -g <resource group name>
```

A instância de serviço levará cerca de cinco minutos para ser implantada.

Detete o nome do grupo de recursos predefinido e o nome do cluster utilizando os seguintes comandos:

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

> [!div class="nextstepaction"]
> [Deparei-me com um problema.](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=provision)

## <a name="create-the-spring-cloud-application"></a>Criar a aplicação Spring Cloud

O seguinte comando cria uma aplicação Spring Cloud na sua subscrição.  Isto cria um serviço vazio da Nuvem de primavera para o qual podemos carregar a nossa aplicação.

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>Implante a sua aplicação Spring Cloud

Pode implementar a sua aplicação a partir de um JAR pré-construído ou de um repositório Gradle ou Maven.  Encontre instruções para cada caso abaixo.

### <a name="deploy-a-built-jar"></a>Implementar um JAR construído

Para ser implantado a partir de um JAR construído na sua máquina local, [certifique-se](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven)de que a sua construção produz um pote de gordura.

Para implantar o pote de gordura para uma implantação ativa

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR>
```

Para implantar o pote de gordura para uma implantação específica

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-built-jar>
```

### <a name="deploy-from-source-code"></a>Implementação do código fonte

A Azure Spring Cloud usa [kpack](https://github.com/pivotal/kpack) para construir o seu projeto.  Pode utilizar o Azure CLI para carregar o seu código fonte, construir o seu projeto utilizando kpack e implantá-lo na aplicação-alvo.

> [!WARNING]
> O projeto deve produzir apenas um ficheiro JAR com uma entrada `main-class` no `MANIFEST.MF` em `target` (para implantações maven ou `build/libs` (para implantações gradle).  Vários ficheiros JAR com entradas `main-class` farão com que a implementação falhe.

Para projetos maven / gradle de módulo único:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

Para projetos Maven/Gradle com vários módulos, repita para cada módulo:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name> --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>Mostrar registos de implantação

Reveja os registos de construção de kpack utilizando o seguinte comando:

```azurecli
az spring-cloud app show-deploy-log -n <app-name> [-d <deployment-name>]
```

> [!NOTE]
> Os registos de kpack só mostrarão a última implantação se essa implantação foi construída a partir da fonte usando kpack.

> [!div class="nextstepaction"]
> [Deparei-me com um problema.](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=deploy)

## <a name="assign-a-public-endpoint-to-gateway"></a>Atribuir um ponto final público ao gateway

1. Abra a página do Painel de **Aplicações.**
2. Selecione a aplicação `gateway` para mostrar a página Detalhes da **Aplicação.**
3. **Selecione Domínio de Atribuição** para atribuir um ponto final público ao gateway. Isto pode fazer alguns minutos. 
4. Insira o IP público atribuído no seu navegador para ver a sua aplicação de execução.

> [!div class="nextstepaction"]
> [Deparei-me com um problema.](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a:

> [!div class="checklist"]
> * Provisão de uma instância de serviço
> * Definir um servidor de configuração, por exemplo
> * Construir uma aplicação de microserviços localmente
> * Implementar cada microserviço
> * Editar variáveis ambientais para aplicações
> * Atribuir IP público para o seu gateway de candidatura

> [!div class="nextstepaction"]
> [Prepare a sua aplicação Azure Spring Cloud para implementação](spring-cloud-tutorial-prepare-app-deployment.md)

Mais amostras estão disponíveis no GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
