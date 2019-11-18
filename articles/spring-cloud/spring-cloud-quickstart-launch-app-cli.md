---
title: 'Início rápido: iniciar um aplicativo Spring Java usando o CLI do Azure'
description: Neste guia de início rápido, você implanta um aplicativo de exemplo para o Azure Spring Cloud no CLI do Azure.
author: jpconnock
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 9751435636519100ea13fd850508c99faeb87a92
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133313"
---
# <a name="quickstart-launch-a-java-spring-application-using-the-azure-cli"></a>Início rápido: iniciar um aplicativo Spring Java usando o CLI do Azure

O Azure Spring Cloud permite que você execute facilmente um aplicativo de microserviço baseado em Spring boot no Azure.

Este guia de início rápido mostra como implantar um aplicativo Java Spring Cloud existente no Azure. Quando tiver terminado, você poderá continuar a gerenciar o aplicativo por meio do CLI do Azure ou alternar para o uso do portal do Azure.

Seguindo este guia de início rápido, você aprenderá a:

> [!div class="checklist"]
> * Provisionar uma instância de serviço
> * Definir um servidor de configuração para uma instância
> * Criar um aplicativo de microserviço localmente
> * Implantar cada microserviço
> * Atribuir um ponto de extremidade público para seu aplicativo

## <a name="prerequisites"></a>Pré-requisitos

>[!Note]
> Atualmente, o Azure Spring Cloud é oferecido como uma visualização pública. As ofertas de visualização pública permitem que os clientes experimentem os novos recursos antes do lançamento oficial.  Serviços e recursos de visualização pública não são destinados ao uso em produção.  Para obter mais informações sobre o suporte durante as visualizações, leia nossas [perguntas frequentes](https://azure.microsoft.com/support/faq/) ou registre um [solicitação de suporte](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) para saber mais.

>[!TIP]
> O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo.  Ele tem ferramentas comuns do Azure pré-instalados, incluindo as versões mais recentes do git, JDK, Maven e o CLI do Azure. Se você estiver conectado à sua assinatura do Azure, inicie o [Azure cloud Shell](https://shell.azure.com) do Shell.Azure.com.  Você pode saber mais sobre Azure Cloud Shell [lendo nossa documentação](../cloud-shell/overview.md)

Para concluir este guia de início rápido:

1. [Instale o Git](https://git-scm.com/)
2. [Instalar o JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Instalar o Maven 3,0 ou superior](https://maven.apache.org/download.cgi)
4. [Instalar o CLI do Azure versão 2.0.67 ou superior](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Inscrever-se para uma assinatura do Azure](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Instalar a extensão de CLI do Azure

Instale a extensão do Azure Spring Cloud para o CLI do Azure usando o comando a seguir

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-on-the-azure-cli"></a>Provisionar uma instância de serviço no CLI do Azure

1. Faça logon no CLI do Azure e escolha sua assinatura ativa. Certifique-se de escolher a assinatura ativa que está na lista de permissões para o Azure Spring Cloud

    ```azurecli
        az login
        az account list -o table
        az account set --subscription
    ```

2. Prepare um nome para seu serviço de nuvem do Azure Spring.  O nome deve ter entre 4 e 32 caracteres de comprimento e pode conter apenas letras minúsculas, números e hifens.  O primeiro caractere do nome do serviço deve ser uma letra e o último caractere deve ser uma letra ou um número.

3. Crie um grupo de recursos para conter seu serviço de nuvem Spring do Azure.

    ```azurecli
        az group create --location eastus --name <resource group name>
    ```
    Saiba mais sobre os [Grupos de Recursos do Azure](../azure-resource-manager/resource-group-overview.md).

4. Abra uma janela de CLI do Azure e execute os comandos a seguir para provisionar uma instância do Azure Spring Cloud.

    ```azurecli
        az spring-cloud create -n <service name> -g <resource group name>
    ```

    A instância do serviço levará cerca de cinco minutos para ser implantada.

5. Defina o nome do grupo de recursos padrão e o nome do cluster usando os seguintes comandos:

    ```azurecli
        az configure --defaults group=<service group name>
        az configure --defaults spring-cloud=<service instance name>
    ```

## <a name="setup-your-configuration-server"></a>Configurar seu servidor de configuração

Atualize seu config-Server com o local do repositório Git para nosso projeto:

```git
az spring-cloud config-server git set -n <your-service-name> --uri https://github.com/Azure-Samples/piggymetrics --label config
```

## <a name="build-the-microservices-applications-locally"></a>Crie os aplicativos de microserviço localmente

1. Crie uma nova pasta e clone o repositório de aplicativo de exemplo para sua conta de nuvem do Azure.  

    ```azurecli
        mkdir source-code
        git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Altere o diretório e compile o projeto.

    ```azurecli
        cd piggymetrics
        mvn clean package -D skipTests
    ```

A compilação do projeto leva cerca de 5 minutos.  Depois de concluído, você deve ter arquivos JAR individuais para cada serviço em suas respectivas pastas.

## <a name="create-the-microservices"></a>Criar os microserviços

Crie microserviços de nuvem Spring usando os arquivos JAR criados na etapa anterior. Você criará três microserviços: **Gateway**, **auth-Service**e **Account-Service**.

```azurecli
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

## <a name="deploy-applications-and-set-environment-variables"></a>Implantar aplicativos e definir variáveis de ambiente

Precisamos realmente implantar nossos aplicativos no Azure. Use os seguintes comandos para implantar todos os três aplicativos:

```azurecli
az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
```

## <a name="assign-public-endpoint-to-gateway"></a>Atribuir ponto de extremidade público ao gateway

Precisamos de uma maneira de acessar o aplicativo por meio de um navegador da Web. Nosso aplicativo de gateway precisa de um ponto de extremidade voltado para o público, que pode ser atribuído usando o seguinte comando:

```azurecli
az spring-cloud app update -n gateway --is-public true
```

Por fim, consulte o aplicativo de **Gateway** para seu IP público para que você possa verificar se o aplicativo está em execução:

```azurecli
az spring-cloud app show --name gateway | grep url
```

Navegue até a URL fornecida pelo comando anterior para executar o aplicativo PiggyMetrics.
    ![captura de tela do PiggyMetrics em execução](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

Você também pode navegar na portal do Azure para localizar a URL. 
1. Navegue até o serviço
1. Selecionar **aplicativos**
1. Selecionar **Gateway**

    ![Captura de tela de PiggyMetrics em execução](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
1. Localize a URL na página **visão geral do gateway** ![captura de tela de PiggyMetrics em execução](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você implantou um aplicativo Spring Cloud da CLI do Azure.  Para saber mais sobre o Azure Spring Cloud, continue no tutorial sobre como preparar seu aplicativo para implantação.

> [!div class="nextstepaction"]
> [Preparar seu aplicativo do Azure Spring Cloud para implantação](spring-cloud-tutorial-prepare-app-deployment.md)
