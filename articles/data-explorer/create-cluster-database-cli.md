---
title: Criar um cluster de Data Explorer do Azure e um banco de dados usando CLI do Azure
description: Saiba como criar um cluster de Data Explorer do Azure e um banco de dados usando o CLI do Azure
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: bd53a8e29254af617b6cfa68935a191a50fc526c
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326774"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-azure-cli"></a>Criar um cluster de Data Explorer do Azure e um banco de dados usando CLI do Azure

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Modelo ARM](create-cluster-database-resource-manager.md)

O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerido que permite realizar análises em tempo real em volumes grandes de transmissão de dados a partir de aplicações, sites, dispositivos IoT e muito mais. Para usar o Azure Data Explorer, primeiro crie um cluster e crie um ou mais bancos de dados nesse cluster. Em seguida, você pode ingerir (carregar) dados em um banco de dado para poder executar consultas nele. Neste artigo, você cria um cluster e um banco de dados usando CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa de uma assinatura do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o CLI do Azure localmente, este artigo exigirá o CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para verificar a sua versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-the-cli-parameters"></a>Configurar os parâmetros da CLI

As etapas a seguir não serão necessárias se você estiver executando comandos no Azure Cloud Shell. Se você estiver executando a CLI localmente, siga estas etapas para entrar no Azure e para definir sua assinatura atual:

1. Execute o seguinte comando para iniciar sessão no Azure:

    ```azurecli-interactive
    az login
    ```

1. Defina a assinatura em que você deseja que o cluster seja criado. Substitua `MyAzureSub` pelo nome da assinatura do Azure que você deseja usar:

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Criar o cluster de Data Explorer do Azure

1. Crie seu cluster usando o seguinte comando:

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**Definição** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | name | *azureclitest* | O nome desejado do cluster.|
   | sku | *D13_v2* | A SKU que será usada para o cluster. |
   | resource-group | *testrg* | O nome do grupo de recursos em que o cluster será criado. |

    Há parâmetros opcionais adicionais que você pode usar, como a capacidade do cluster.

1. Execute o seguinte comando para verificar se o cluster foi criado com êxito:

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

Se o resultado contiver `provisioningState` com o `Succeeded` valor, o cluster foi criado com êxito.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Criar o banco de dados no cluster de Data Explorer do Azure

1. Crie seu banco de dados usando o seguinte comando:

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period P365D --hot-cache-period P31D
    ```

   |**Definição** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | nome do cluster | *azureclitest* | O nome do cluster em que o banco de dados será criado.|
   | name | *clidatabase* | O nome do seu banco de dados.|
   | resource-group | *testrg* | O nome do grupo de recursos em que o cluster será criado. |
   | exclusão reversível-período | *P365D* | Significa a quantidade de tempo que os dados serão mantidos disponíveis para consulta. Consulte [política de retenção](/azure/kusto/concepts/retentionpolicy) para obter mais informações. |
   | tempo de cache ativo | *P31D* | Significa a quantidade de tempo em que os dados serão mantidos no cache. Consulte [política de cache](/azure/kusto/concepts/cachepolicy) para obter mais informações. |

1. Execute o seguinte comando para ver o banco de dados que você criou:

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

Agora você tem um cluster e um banco de dados.

## <a name="clean-up-resources"></a>Limpar recursos

* Se você planeja seguir nossos outros artigos, mantenha os recursos que você criou.
* Para limpar os recursos, exclua o cluster. Quando você exclui um cluster, ele também exclui todos os bancos de dados nele. Use o seguinte comando para excluir o cluster:

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>Passos seguintes

* [Ingerir dados usando a biblioteca do Azure Data Explorer Python](python-ingest-data.md)
