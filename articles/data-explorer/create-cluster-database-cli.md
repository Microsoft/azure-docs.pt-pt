---
title: Crie um cluster e DB do Azure Data Explorer com o Azure CLI
description: Saiba como criar um cluster e base de dados azure Data Explorer utilizando o Azure CLI
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 6b8c2924e50da095c3bc5c7db2d2bf48ef5a27c2
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561940"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-azure-cli"></a>Criar um cluster e base de dados Azure Data Explorer utilizando o Azure CLI

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [python](create-cluster-database-python.md)
> * [Modelo ARM](create-cluster-database-resource-manager.md)

O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerido que permite realizar análises em tempo real em volumes grandes de transmissão de dados a partir de aplicações, sites, dispositivos IoT e muito mais. Para utilizar o Azure Data Explorer, cria-se primeiro um cluster e cria-se uma ou mais bases de dados nesse cluster. Em seguida, ingere (carregar) dados numa base de dados para que possa fazer perguntas contra ele. Neste artigo, cria um cluster e uma base de dados utilizando o Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este artigo, precisa de uma subscrição Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o Azure CLI localmente, este artigo requer a versão Azure CLI 2.0.4 ou posterior. Execute `az --version` para verificar a sua versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-the-cli-parameters"></a>Configure os parâmetros CLI

Os seguintes passos não são necessários se estiver a comandar comandos em Azure Cloud Shell. Se estiver a executar o CLI localmente, siga estes passos para iniciar sessão no Azure e definir a sua subscrição atual:

1. Execute o seguinte comando para iniciar sessão no Azure:

    ```azurecli-interactive
    az login
    ```

1. Detete a subscrição onde pretende que o seu cluster seja criado. Substitua `MyAzureSub` pelo nome da subscrição Azure que pretende utilizar:

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Criar o cluster Azure Data Explorer

1. Crie o seu cluster utilizando o seguinte comando:

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**Definição** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | nome | *azureclitest* | O nome desejado do seu aglomerado.|
   | sku | *D13_v2* | O SKU que será usado para o seu cluster. |
   | resource-group | *testrg* | O nome do grupo de recursos onde o cluster será criado. |

    Existem parâmetros opcionais adicionais que pode utilizar, como a capacidade do cluster.

1. Execute o seguinte comando para verificar se o seu cluster foi criado com sucesso:

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

Se o resultado contiver `provisioningState` com o valor `Succeeded`, então o cluster foi criado com sucesso.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Criar a base de dados no cluster Azure Data Explorer

1. Crie a sua base de dados utilizando o seguinte comando:

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period P365D --hot-cache-period P31D
    ```

   |**Definição** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | nome do cluster | *azureclitest* | O nome do seu cluster onde será criada a base de dados.|
   | nome | *clidatabase* | O nome da sua base de dados.|
   | resource-group | *testrg* | O nome do grupo de recursos onde o cluster será criado. |
   | soft-delete-período | *P365D* | Significa o tempo que os dados serão mantidos disponíveis para consulta. Consulte a política de [retenção](/azure/kusto/concepts/retentionpolicy) para mais informações. |
   | hot-cache-período | *P31D* | Significa a quantidade de tempo que os dados serão mantidos em cache. Consulte a [política de cache](/azure/kusto/concepts/cachepolicy) para mais informações. |

1. Execute o seguinte comando para ver a base de dados que criou:

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

Agora tem um cluster e uma base de dados.

## <a name="clean-up-resources"></a>Limpar recursos

* Se planeia seguir os nossos outros artigos, guarde os recursos que criou.
* Para limpar os recursos, elimine o cluster. Ao eliminar um cluster, também elimina todas as bases de dados do mesmo. Utilize o seguinte comando para eliminar o seu cluster:

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>Passos seguintes

* [Ingerir dados utilizando a biblioteca Python do Explorador de Dados Azure](python-ingest-data.md)
