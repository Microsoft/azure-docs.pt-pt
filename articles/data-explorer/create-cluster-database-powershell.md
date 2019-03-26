---
title: 'Início rápido: Criar um cluster do Explorador de dados do Azure e a base de dados com o PowerShell'
description: Saiba como criar um cluster do Explorador de dados do Azure e a base de dados com o PowerShell
services: data-explorer
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/25/2019
ms.openlocfilehash: ec012f85c4b4e93e9be475781e9da79f686cbf9e
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417722"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>Criar um cluster do Explorador de dados do Azure e a base de dados com o PowerShell

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerido que permite realizar análises em tempo real em volumes grandes de transmissão de dados a partir de aplicações, sites, dispositivos IoT e muito mais. Para utilizar o Explorador de dados do Azure, primeiro cria um cluster e criar um ou mais bases de dados desse cluster. Em seguida, ingerir (carregar) dados numa base de dados para que pode executar consultas em relação a ele. Neste início rápido, vai criar um cluster e uma base de dados com o Powershell. Pode executar cmdlets do PowerShell e scripts no Windows, Linux, ou num [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) juntamente com [Az.Kusto](https://docs.microsoft.com/powershell/module/az.kusto/?view=azps-1.4.0#kusto) para criar e configurar clusters do Explorador de dados do Azure e bases de dados.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI do Azure localmente, este início rápido requer a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para verificar a sua versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-parameters"></a>Configurar parâmetros

Os passos seguintes não são necessários se estiver a executar comandos no Azure Cloud Shell. Se estiver a executar a CLI localmente, siga os passos 1 e 2 para iniciar sessão no Azure e para definir sua assinatura atual:

1. Execute o seguinte comando para iniciar sessão no Azure:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Defina a subscrição onde pretende que o cluster ser criado:

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
3. Ao executar a CLI do Azure localmente ou no Azure Cloud Shell, tem de instalar o módulo de Az.Kusto no seu dispositivo:
    
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

* [Comandos de Az.Kusto adicionais](https://docs.microsoft.com/powershell/module/az.kusto/?view=azps-1.4.0#kusto)
* [Quickstart: Ingestão de dados com o SDK do Azure Data Explorer .NET Standard (pré-visualização)](net-standard-ingest-data.md)
