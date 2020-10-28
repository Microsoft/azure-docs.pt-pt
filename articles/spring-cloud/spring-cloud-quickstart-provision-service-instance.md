---
title: Quickstart - Serviço De Provision Azure Spring Cloud
description: Descreve a criação de exemplos de serviço Azure Spring Cloud para implementação de aplicações.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 22abdb64a2349045923e148db57f683b4be87636
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92742696"
---
# <a name="quickstart-provision-azure-spring-cloud-service"></a>Quickstart: Serviço De Provision Azure Spring Cloud

::: zone pivot="programming-language-csharp"
Neste arranque rápido, você usa o CLI Azure para providenciar uma instância do serviço Azure Spring Cloud.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). O serviço Azure Spring Cloud suporta versões .NET Core 3.1 e versões posteriores.
* [A versão Azure CLI 2.0.67 ou superior](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).
* [Git.](https://git-scm.com/)

## <a name="install-azure-cli-extension"></a>Instalar a extensão da CLI do Azure

Verifique se a sua versão Azure CLI é 2.0.67 ou posterior:

```azurecli
az --version
```

Instale a extensão Azure Spring Cloud para o Azure CLI utilizando o seguinte comando:

```azurecli
az extension add --name spring-cloud
```

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

1. Faça login no Azure CLI.

    ```azurecli
    az login
    ```

1. Se tiver mais do que uma subscrição, escolha a que pretende utilizar para este arranque rápido.

   ```azurecli
   az account list -o table
   ```

   ```azurecli
   az account set --subscription <Name or ID of a subscription from the last step>
   ```

## <a name="provision-an-instance-of-azure-spring-cloud"></a>Provisionar um exemplo de Azure Spring Cloud

1. Crie um [grupo de recursos](../azure-resource-manager/management/overview.md) para conter o seu serviço Azure Spring Cloud. O nome do grupo de recursos pode incluir caracteres alfanuméricos, sublinhados, parênteses, hífen, período (exceto no final) e caracteres Unicode.

   ```azurecli
   az group create --location eastus --name <resource group name>
   ```

1. Provisionar um caso de serviço Azure Spring Cloud. O nome da instância de serviço deve ser único, entre 4 e 32 caracteres de comprimento, e pode conter apenas letras minúsculas, números e hífens. O primeiro carácter do nome de serviço deve ser uma letra e o último caractere deve ser uma letra ou um número.

    ```azurecli
    az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    Este comando pode levar vários minutos para ser concluído.

1. Desaprova o nome do grupo de recursos predefinidos e o nome da instância de serviço para que não tenha de especificar repetidamente estes valores nos comandos subsequentes.

   ```azurecli
   az configure --defaults group=<resource group name>
   ```

   ```azurecli
   az configure --defaults spring-cloud=<service instance name>
   ```
::: zone-end

::: zone pivot="programming-language-java"
Pode instantaneamente a Nuvem de primavera do Azure utilizando o portal Azure ou o Azure CLI.  Ambos os métodos são explicados nos seguintes procedimentos.
## <a name="prerequisites"></a>Pré-requisitos

* [Instalar JDK 8](/java/azure/jdk/?preserve-view=true&view=azure-java-stable)
* [Inscreva-se para uma subscrição do Azure](https://azure.microsoft.com/free/)
* (Opcional) [Instale a versão Azure CLI 2.0.67 ou superior](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) e instale a extensão Azure Spring Cloud com comando: `az extension add --name spring-cloud`
* (Opcional) [Instale o Azure Toolkit para IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) e [inscreva-se](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="provision-an-instance-of-azure-spring-cloud"></a>Provisionar um exemplo de Azure Spring Cloud

#### <a name="portal"></a>[Portal](#tab/Azure-portal)

O procedimento a seguir cria uma instância de Azure Spring Cloud usando o portal Azure.

1. Num novo separador, abra o [portal Azure](https://ms.portal.azure.com/). 

2. A partir da caixa de pesquisa superior, procure **a Azure Spring Cloud.**

3. Selecione **Azure Spring Cloud** a partir dos resultados.

    ![Início do ícone ASC](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Na página Azure Spring Cloud, clique **+ Adicionar** .

    ![Adicionar ícone ASC](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Preencha o formulário na página Azure Spring Cloud **Create.**  Considere as seguintes orientações:
    - **Subscrição** : Selecione a subscrição que pretende ser faturada para este recurso.
    - **Grupo de recursos** : Criar novos grupos de recursos para novos recursos é uma boa prática. Note que este será usado em etapas posteriores como **\<resource group name\>** .
    - **Dados de serviço/Nome** : Especifique o **\<service instance name\>** .  O nome deve ter entre 4 e 32 caracteres de comprimento e pode conter apenas letras minúsculas, números e hífenes.  O primeiro carácter do nome de serviço deve ser uma letra e o último caractere deve ser uma letra ou um número.
    - **Localização** : Selecione a localização para a sua instância de serviço.

    ![Início do portal ASC](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Clique em **Rever e criar** .

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=public-endpoint)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

O procedimento a seguir utiliza a extensão do Azure CLI para prever uma instância de Azure Spring Cloud.

1. Faça login no Azure CLI e escolha a sua subscrição ativa.

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
::: zone-end

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou recursos Azure que continuarão a acumular encargos se permanecerem na sua subscrição. Se não pretende continuar para o próximo quickstart, consulte [clean up resources](spring-cloud-quickstart-logs-metrics-tracing.md#clean-up-resources). Caso contrário, avance para o próximo arranque rápido:

> [!div class="nextstepaction"]
> [Configurar o servidor de configuração](spring-cloud-quickstart-setup-config-server.md)
