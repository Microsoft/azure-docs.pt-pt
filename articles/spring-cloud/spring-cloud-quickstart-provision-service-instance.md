---
title: Quickstart - Serviço De Provision Azure Spring Cloud
description: Descreve a criação de exemplos de serviço Azure Spring Cloud para implementação de aplicações.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: c91237e3a14c60e477f58be0bf62f634b462960b
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951919"
---
# <a name="quickstart-provision-azure-spring-cloud-service"></a>Quickstart: Serviço De Provision Azure Spring Cloud

Pode instantaneamente a Nuvem de primavera do Azure utilizando o portal Azure ou o Azure CLI.  Ambos os métodos são explicados nos seguintes procedimentos.
## <a name="prerequisites"></a>Pré-requisitos

* [Instalar JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Inscreva-se para uma subscrição do Azure](https://azure.microsoft.com/free/)
* (Opcional) [Instale a versão Azure CLI 2.0.67 ou superior](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e instale a extensão Azure Spring Cloud com comando: `az extension add --name spring-cloud`
* (Opcional) [Instale o Azure Toolkit para IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) e [inscreva-se](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="provision-an-instance-of-azure-spring-cloud"></a>Provisionar um exemplo de Azure Spring Cloud

#### <a name="portal"></a>[Portal](#tab/Azure-portal)

O procedimento a seguir cria uma instância de Azure Spring Cloud usando o portal Azure.

1. Num novo separador, abra o [portal Azure](https://ms.portal.azure.com/). 

2. A partir da caixa de pesquisa superior, procure **a Azure Spring Cloud.**

3. Selecione **Azure Spring Cloud** a partir dos resultados.

    ![Início do ícone ASC](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Na página Azure Spring Cloud, clique **+ Adicionar**.

    ![Adicionar ícone ASC](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Preencha o formulário na página Azure Spring Cloud **Create.**  Considere as seguintes orientações:
    - **Subscrição**: Selecione a subscrição que pretende ser faturada para este recurso.
    - **Grupo de recursos**: Criar novos grupos de recursos para novos recursos é uma boa prática. Note que este será usado em etapas posteriores como **\<resource group name\>** .
    - **Dados de serviço/Nome**: Especifique o **\<service instance name\>** .  O nome deve ter entre 4 e 32 caracteres de comprimento e pode conter apenas letras minúsculas, números e hífenes.  O primeiro carácter do nome de serviço deve ser uma letra e o último caractere deve ser uma letra ou um número.
    - **Localização**: Selecione a localização para a sua instância de serviço.

    ![Início do portal ASC](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Clique em **Rever e criar**.

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=public-endpoint)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

O procedimento a seguir utiliza a extensão do Azure CLI para prever uma instância de Azure Spring Cloud.

1. Faça login no Azure CLI e escolha a sua subscrição ativa. Não se esqueça de escolher a subscrição ativa que está em whitelist para Azure Spring Cloud

    ```azurecli
    az login
    az account list -o table
    az account set --subscription <Name or ID of subscription, skip if you only have 1 subscription>
    ```

1. Prepare um nome para o seu serviço Azure Spring Cloud.  O nome deve ter entre 4 e 32 caracteres de comprimento e pode conter apenas letras minúsculas, números e hífenes.  O primeiro carácter do nome de serviço deve ser uma letra e o último caractere deve ser uma letra ou um número.

1. Crie um grupo de recursos para conter o seu serviço Azure Spring Cloud.

    ```azurecli
    az group create --location eastus --name <resource group name>
    ```

    Saiba mais sobre os [Grupos de Recursos do Azure](../azure-resource-manager/management/overview.md).

1. Abra uma janela Azure CLI e execute os seguintes comandos para providenciar uma instância de Azure Spring Cloud.

    ```azurecli
    az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    A instância de serviço levará cerca de cinco minutos para ser implantada.
---

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Configurar servidor de configuração](spring-cloud-quickstart-setup-config-server.md)


