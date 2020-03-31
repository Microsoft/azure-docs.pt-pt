---
title: Criar um cluster Azure Data Explorer & DB usando powershell
description: Saiba como criar um cluster e base de dados do Azure Data Explorer utilizando o PowerShell
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 690c3e281e65f54f240c70f7a6e5038f54102c99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560597"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>Criar um cluster e base de dados Azure Data Explorer utilizando o PowerShell

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Pitão](create-cluster-database-python.md)
> * [Modelo de BRAÇO](create-cluster-database-resource-manager.md)  

O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerido que permite realizar análises em tempo real em volumes grandes de transmissão de dados a partir de aplicações, sites, dispositivos IoT e muito mais. Para utilizar o Azure Data Explorer, primeiro crie um cluster e crie uma ou mais bases de dados nesse cluster. Em seguida, ingira (carregue) os dados para uma base de dados, de modo a poder executar consultas neles. Neste artigo, cria-se um cluster e uma base de dados utilizando o Powershell. Pode executar cmdlets e scripts PowerShell no Windows, Linux ou em [Azure Cloud Shell](../cloud-shell/overview.md) com [Az.Kusto](/powershell/module/az.kusto/?view=azps-1.4.0#kusto) para criar e configurar clusters e bases de dados do Azure Data Explorer.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o Azure CLI localmente, este artigo requer a versão Azure CLI 2.0.4 ou posterior. Execute `az --version` para verificar a sua versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-parameters"></a>Configurar parâmetros

Os seguintes passos não são necessários se estiver a comandar comandos em Azure Cloud Shell. Se estiver a executar o CLI localmente, siga os passos 1 & 2 para iniciar sessão no Azure e definir a sua subscrição atual:

1. Execute o seguinte comando para iniciar sessão no Azure:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Detete a subscrição onde pretende que o seu cluster seja criado:

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
1. Ao executar o Azure CLI localmente ou na Casca de Nuvem Azure, é necessário instalar o módulo Az.Kusto no seu dispositivo:

    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Criar o cluster Azure Data Explorer

1. Crie o seu cluster utilizando o seguinte comando:

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**Definição** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | Nome | *mykustocluster* | O nome desejado do seu aglomerado.|
   | Sku | *D13_v2* | O SKU que será usado para o seu cluster. |
   | ResourceGroupName | *testrg* | O nome do grupo de recursos onde o cluster será criado. |

    Existem parâmetros opcionais adicionais que pode utilizar, como a capacidade do cluster.

1. Execute o seguinte comando para verificar se o seu cluster foi criado com sucesso:

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster -ResourceGroupName testrg
    ```

Se o `provisioningState` resultado `Succeeded` contiver o valor, então o cluster foi criado com sucesso.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Criar a base de dados no cluster Azure Data Explorer

1. Crie a sua base de dados utilizando o seguinte comando:

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**Definição** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | ClusterName | *mykustocluster* | O nome do seu cluster onde será criada a base de dados.|
   | Nome | *mykustodatabase* | O nome da sua base de dados.|
   | ResourceGroupName | *testrg* | O nome do grupo de recursos onde o cluster será criado. |
   | SoftDeletePeriod | *3650:00:00:00* | O tempo que os dados serão mantidos disponíveis para consulta. |
   | HotCachePeriod | *3650:00:00:00* | A quantidade de tempo que os dados serão mantidos em cache. |

1. Execute o seguinte comando para ver a base de dados que criou:

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster -ResourceGroupName testrg -Name mykustodatabase
    ```

Agora tem um cluster e uma base de dados.

## <a name="clean-up-resources"></a>Limpar recursos

* Se planeia seguir os nossos outros artigos, guarde os recursos que criou.
* Para limpar os recursos, elimine o cluster. Ao eliminar um cluster, também elimina todas as bases de dados do mesmo. Utilize o seguinte comando para eliminar o seu cluster:

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>Passos seguintes

* [Comandos adicionais de Az.Kusto](/powershell/module/az.kusto/?view=azps-1.7.0#kusto)
* [Dados de ingestão utilizando o Azure Data Explorer .NET Standard SDK (Pré-visualização)](net-standard-ingest-data.md)
