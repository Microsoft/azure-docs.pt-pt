---
title: 'Início rápido: Criar um cluster do Explorador de dados do Azure e a base de dados com o PowerShell'
description: Saiba como criar um cluster do Explorador de dados do Azure e a base de dados com o PowerShell
services: data-explorer
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/17/2019
ms.openlocfilehash: 650bdc5cdf99645bc2be6c8e85737dacd10a6b27
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287524"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>Criar um cluster do Explorador de dados do Azure e a base de dados com o PowerShell

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  


Este início rápido descreve como criar um cluster do Explorador de dados do Azure e a base de dados com o PowerShell.

Pode executar cmdlets do PowerShell e scripts no Windows, Linux, ou num [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) para criar e configurar [Explorador de dados do Azure](https://docs.microsoft.com/azure/kusto/ ).

O [ **Az.Kusto**](https://docs.microsoft.com/powershell/module/az.kusto/?view=azps-1.4.0#kusto ). Com o Azure PowerShell e **Az.Kusto**, pode realizar as seguintes tarefas:

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, precisa de uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="configure-parameters"></a>Configurar parâmetros

Os passos seguintes não são necessários se estiver a executar comandos no Azure Cloud Shell. Se estiver a executar a CLI localmente, siga estes passos para iniciar sessão no Azure e para definir sua assinatura atual:

1. Execute o seguinte comando para iniciar sessão no Azure:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Defina a subscrição onde pretende que o cluster ser criado.

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
3. Instale o módulo de Az.Kusto no seu dispositivo:
    
    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto  
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Criar o cluster do Explorador de dados do Azure

1. Crie o cluster utilizando o seguinte comando:

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**Definição** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | Name | *mykustocluster* | O nome do cluster pretendido.|
   | Sku | *D13_v2* | O SKU que será utilizado para o seu cluster. |
   | ResourceGroupName | *testrg* | O nome do grupo de recursos onde será criado o cluster. |

    Existem parâmetros opcionais adicionais que pode utilizar, como a capacidade do cluster.

2. Execute o seguinte comando para verificar se o cluster foi criado com êxito:

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster --ResourceGroupName testrg
    ```

Se o resultado contém `provisioningState` com o `Succeeded` valor, em seguida, o cluster foi criado com êxito.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Criar a base de dados do cluster do Explorador de dados do Azure

1. Crie a base de dados, utilize o seguinte comando:

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**Definição** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | ClusterName | *mykustocluster* | O nome do cluster onde a base de dados será criado.|
   | Name | *mykustodatabase* | O nome da base de dados.|
   | ResourceGroupName | *testrg* | O nome do grupo de recursos onde será criado o cluster. |
   | SoftDeletePeriod | *3650:00:00:00* | A quantidade de tempo que os dados serão mantidos disponíveis para consulta. |
   | HotCachePeriod | *3650:00:00:00* | A quantidade de tempo que os dados serão mantidos na cache. |

2. Execute o seguinte comando para ver a base de dados que criou:

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster --ResourceGroupName testrg -Name mykustodatabase
    ```

Agora tem um cluster e uma base de dados.

## <a name="clean-up-resources"></a>Limpar recursos

* Se tenciona seguir os nossos inícios rápidos e tutoriais, mantenha os recursos que criou.
* Para limpar os recursos, elimine o cluster. Quando eliminar um cluster, também elimina todos os bancos de dados no mesmo. Utilize o seguinte comando para eliminar o cluster:

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>Passos Seguintes

Pode encontrar mais comandos Az.Kusto [ **aqui**](https://docs.microsoft.com/powershell/module/az.kusto/?view=azps-1.4.0#kusto )

> [!div class="nextstepaction"]
> [Quickstart: Ingestão de dados com o SDK do Azure Data Explorer .NET Standard (pré-visualização)](net-standard-ingest-data.md)
