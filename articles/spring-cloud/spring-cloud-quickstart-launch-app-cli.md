---
title: Iniciar um aplicativo Spring Java usando o CLI do Azure
description: Neste guia de início rápido, você implanta um aplicativo de exemplo para o Azure Spring Cloud no CLI do Azure.
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/04/2019
ms.author: v-vasuke
ms.openlocfilehash: 632059710bf54847eb7a0a1aa388fd1409a3075a
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038973"
---
# <a name="quickstart-launch-a-java-spring-application-using-the-azure-cli"></a>Início rápido: Iniciar um aplicativo Spring Java usando o CLI do Azure

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
> Antes de iniciar este guia de início rápido, verifique se sua assinatura do Azure tem acesso ao Azure Spring Cloud.  Como um serviço de visualização, pedimos que você entre em contato conosco para que possamos adicionar sua assinatura à nossa lista de permissões.  Se você quiser explorar os recursos do Azure Spring Cloud, entre em contato conosco por email: azure-spring-cloud@service.microsoft.com.

>[!TIP]
> O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo.  Ele tem ferramentas comuns do Azure pré-instalados, incluindo as versões mais recentes do git, JDK, Maven e o CLI do Azure. Se você estiver conectado à sua assinatura do Azure, inicie o [Azure cloud Shell](https://shell.azure.com) do Shell.Azure.com.  Você pode saber mais sobre Azure Cloud Shell [lendo nossa documentação](../cloud-shell/overview.md)

Para concluir este guia de início rápido:

1. [Instalar o Git](https://git-scm.com/)
2. [Instalar o JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Instalar o Maven 3,0 ou superior](https://maven.apache.org/download.cgi)
4. [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Inscrever-se para uma assinatura do Azure](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Instalar a extensão de CLI do Azure

Instale a extensão do Azure Spring Cloud para o CLI do Azure usando o comando a seguir

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
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
        cd PiggyMetrics
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

>[!NOTE]
> Os nomes de aplicativo devem corresponder aos nomes dos JARS exatamente para que o servidor de configuração fornecido funcione corretamente.

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

Navegue até a URL fornecida pelo comando anterior para ver o aplicativo PiggyMetrics em execução.

## <a name="next-steps"></a>Próximos Passos

Neste guia de início rápido, você implantou um aplicativo Spring Cloud da CLI do Azure.  Para saber mais sobre o Azure Spring Cloud, continue no tutorial sobre como preparar seu aplicativo para implantação.

> [!div class="nextstepaction"]
> [Preparar seu aplicativo do Azure Spring Cloud para implantação](spring-cloud-tutorial-prepare-app-deployment.md)
