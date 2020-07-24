---
title: 'Quickstart: Lançar uma aplicação Java Spring utilizando o Azure CLI'
description: Neste arranque rápido, você implementa uma aplicação de amostra para Azure Spring Cloud no Azure CLI.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 02/15/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 64da0e62850433233c00f430b104121adc1979c1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87021516"
---
# <a name="quickstart-launch-a-java-spring-application-using-the-azure-cli"></a>Quickstart: Lançar uma aplicação Java Spring utilizando o Azure CLI

O Azure Spring Cloud permite-lhe executar facilmente uma aplicação de microserviços baseada em Boot de mola no Azure.

Este quickstart mostra-lhe como implementar uma aplicação java Spring Cloud existente para Azure. Quando terminar, pode continuar a gerir a aplicação através do CLI Azure ou mudar para o portal Azure.

Após este arranque rápido, aprenderá a:

> [!div class="checklist"]
> * Prestação de uma instância de serviço
> * Desa parte para um servidor de configuração para uma instância
> * Construir uma aplicação de microserviços localmente
> * Implementar cada microserviço
> * Atribua o ponto final público para a sua aplicação

## <a name="prerequisites"></a>Pré-requisitos

>[!Note]
> Azure Spring Cloud é atualmente oferecido como uma pré-visualização pública. As ofertas de pré-visualização públicas permitem que os clientes experimentem novas funcionalidades antes do seu lançamento oficial.  As funcionalidades e serviços de pré-visualização do público não se destinam ao uso da produção.  Para mais informações sobre o suporte durante as pré-visualizações, por favor reveja as nossas [PERGUNTAS Frequentes](https://azure.microsoft.com/support/faq/) ou preencha um [pedido de Apoio](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) para saber mais.

>[!TIP]
> O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo.  Tem ferramentas Azure comuns pré-instaladas, incluindo as versões mais recentes de Git, JDK, Maven e o Azure CLI. Se tiver iniciado sessão na sua subscrição Azure, lance a sua [Azure Cloud Shell](https://shell.azure.com) a partir de shell.azure.com.  Você pode saber mais sobre Azure Cloud Shell [lendo a nossa documentação](../cloud-shell/overview.md)

Para concluir este guia de início rápido:

1. [Instalar o Git](https://git-scm.com/)
2. [Instalar JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Instalar Maven 3.0 ou superior](https://maven.apache.org/download.cgi)
4. [Instale a versão Azure CLI 2.0.67 ou superior](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Inscreva-se para uma subscrição do Azure](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Instale a extensão Azure CLI

Instale a extensão Azure Spring Cloud para o CLI Azure utilizando o seguinte comando

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-on-the-azure-cli"></a>Prestação de uma instância de serviço no CLI Azure

1. Faça login no Azure CLI e escolha a sua subscrição ativa. Não se esqueça de escolher a subscrição ativa que está em whitelist para Azure Spring Cloud

    ```azurecli
        az login
        az account list -o table
        az account set --subscription <Name or ID of subscription from the last step>
    ```

2. Prepare um nome para o seu serviço Azure Spring Cloud.  O nome deve ter entre 4 e 32 caracteres de comprimento e pode conter apenas letras minúsculas, números e hífenes.  O primeiro carácter do nome de serviço deve ser uma letra e o último caractere deve ser uma letra ou um número.

3. Crie um grupo de recursos para conter o seu serviço Azure Spring Cloud.

    ```azurecli
        az group create --location eastus --name <resource group name>
    ```

    Saiba mais sobre os [Grupos de Recursos do Azure](../azure-resource-manager/management/overview.md).

4. Abra uma janela Azure CLI e execute os seguintes comandos para providenciar uma instância de Azure Spring Cloud.

    ```azurecli
        az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    A instância de serviço levará cerca de cinco minutos para ser implantada.

5. Desaprote o nome do grupo de recursos predefinido e o nome do cluster utilizando os seguintes comandos:

    ```azurecli
        az configure --defaults group=<resource group name>
        az configure --defaults spring-cloud=<service instance name>
    ```

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=provision)

## <a name="setup-your-configuration-server"></a>Configurar o seu servidor de configuração

Atualize o seu config-servidor com a localização do repositório de git para o nosso projeto:

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=config-server)

## <a name="build-the-microservices-applications-locally"></a>Construir as aplicações de microserviços localmente

1. Crie uma nova pasta e clone o repositório de aplicações de amostra para a sua conta Azure Cloud.  

    ```console
        mkdir source-code
        git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Mude o diretório e construa o projeto.

    ```console
        cd piggymetrics
        mvn clean package -D skipTests
    ```

Compilar o projeto leva cerca de 5 minutos.  Uma vez concluído, deverá ter ficheiros JAR individuais para cada serviço nas respetivas pastas.

## <a name="create-the-microservices"></a>Criar os microserviços

Crie microserviços Spring Cloud utilizando os ficheiros JAR construídos no passo anterior. Irá criar três microserviços: **gateway,** **auth-service**e **serviço de conta.**

```azurecli
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

## <a name="deploy-applications-and-set-environment-variables"></a>Implementar aplicações e definir variáveis ambientais

Precisamos de enviar as nossas aplicações para o Azure. Utilize os seguintes comandos para implementar as três aplicações:

```azurecli
az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
```

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=deploy)

## <a name="assign-public-endpoint-to-gateway"></a>Atribuir ponto final público ao gateway

Precisamos de uma forma de aceder à aplicação através de um navegador web. A nossa aplicação gateway precisa de um ponto final virado para o público.

1. Atribuir o ponto final utilizando o seguinte comando:

```azurecli
az spring-cloud app update -n gateway --is-public true
```

2. Consultar a aplicação **gateway** para o seu IP público para que possa verificar se a aplicação está em execução:

```azurecli
az spring-cloud app show --name gateway --query properties.url
```

3. Navegue para o URL fornecido pelo comando anterior para executar a aplicação PiggyMetrics.
    ![Screenshot de PiggyMetrics em execução](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

Também pode navegar no portal Azure para encontrar o URL. 
1. Navegue para o serviço
2. Selecione **Apps**
3. Selecione **gateway**

    ![Screenshot de PiggyMetrics em execução](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. Encontre o URL na página **de visualização** do gateway ![ Screenshot da PiggyMetrics em execução](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Passos Seguintes

Neste arranque rápido, lançou uma aplicação Spring Cloud do Azure CLI.  Para saber mais sobre a Azure Spring Cloud, continue o tutorial na preparação da sua app para implementação.

> [!div class="nextstepaction"]
> [Prepare a sua aplicação Azure Spring Cloud para implantação](spring-cloud-tutorial-prepare-app-deployment.md)

Mais amostras estão disponíveis no GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
