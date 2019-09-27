---
title: Criar um cluster de Data Explorer do Azure e um banco de dados usando o PowerShell
description: Saiba como criar um cluster de Data Explorer do Azure e um banco de dados usando o PowerShell
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 1975125dd3bcd327ae7520e4cc413718e48d6ba9
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326759"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>Criar um cluster de Data Explorer do Azure e um banco de dados usando o PowerShell

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Modelo ARM](create-cluster-database-resource-manager.md)  

O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerido que permite realizar análises em tempo real em volumes grandes de transmissão de dados a partir de aplicações, sites, dispositivos IoT e muito mais. Para usar o Azure Data Explorer, primeiro crie um cluster e crie um ou mais bancos de dados nesse cluster. Em seguida, você pode ingerir (carregar) dados em um banco de dado para poder executar consultas nele. Neste artigo, você cria um cluster e um banco de dados usando o PowerShell. Você pode executar os cmdlets e scripts do PowerShell no Windows, Linux ou no [Azure cloud Shell](../cloud-shell/overview.md) com [AZ. Kusto](/powershell/module/az.kusto/?view=azps-1.4.0#kusto) para criar e configurar clusters e bancos de dados do Azure data Explorer.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o CLI do Azure localmente, este artigo exigirá o CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para verificar a sua versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-parameters"></a>Configurar parâmetros

As etapas a seguir não serão necessárias se você estiver executando comandos no Azure Cloud Shell. Se você estiver executando a CLI localmente, siga as etapas 1 & 2 para entrar no Azure e para definir sua assinatura atual:

1. Execute o seguinte comando para iniciar sessão no Azure:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Defina a assinatura em que você deseja que o cluster seja criado:

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
1. Ao executar CLI do Azure localmente ou na Azure Cloud Shell, você precisa instalar o módulo AZ. Kusto em seu dispositivo:
    
    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto  
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Criar o cluster de Data Explorer do Azure

1. Crie seu cluster usando o seguinte comando:

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**Definição** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | Name | *mykustocluster* | O nome desejado do cluster.|
   | SKU | *D13_v2* | A SKU que será usada para o cluster. |
   | ResourceGroupName | *testrg* | O nome do grupo de recursos em que o cluster será criado. |

    Há parâmetros opcionais adicionais que você pode usar, como a capacidade do cluster.

1. Execute o seguinte comando para verificar se o cluster foi criado com êxito:

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster -ResourceGroupName testrg
    ```

Se o resultado contiver `provisioningState` com o `Succeeded` valor, o cluster foi criado com êxito.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Criar o banco de dados no cluster de Data Explorer do Azure

1. Crie seu banco de dados usando o seguinte comando:

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**Definição** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | clusterName | *mykustocluster* | O nome do cluster em que o banco de dados será criado.|
   | Name | *mykustodatabase* | O nome do seu banco de dados.|
   | ResourceGroupName | *testrg* | O nome do grupo de recursos em que o cluster será criado. |
   | SoftDeletePeriod | *3650:00:00:00* | A quantidade de tempo que os dados serão mantidos disponíveis para consulta. |
   | HotCachePeriod | *3650:00:00:00* | A quantidade de tempo que os dados serão mantidos no cache. |

1. Execute o seguinte comando para ver o banco de dados que você criou:

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster -ResourceGroupName testrg -Name mykustodatabase
    ```

Agora você tem um cluster e um banco de dados.

## <a name="clean-up-resources"></a>Limpar recursos

* Se você planeja seguir nossos outros artigos, mantenha os recursos que você criou.
* Para limpar os recursos, exclua o cluster. Quando você exclui um cluster, ele também exclui todos os bancos de dados nele. Use o seguinte comando para excluir o cluster:

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>Passos seguintes

* [Comandos AZ. Kusto adicionais](/powershell/module/az.kusto/?view=azps-1.7.0#kusto)
* [Ingerir dados usando o SDK do Azure Data Explorer .NET Standard (versão prévia)](net-standard-ingest-data.md)
