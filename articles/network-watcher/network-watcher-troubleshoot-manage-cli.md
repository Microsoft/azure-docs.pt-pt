---
title: Troubleshoot Azure VNET Gateway and Connections - Azure CLI
titleSuffix: Azure Network Watcher
description: Esta página explica como usar o problema do Observador de Rede Azure Azure CLI
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: dc0aa8e6099a7ec017aead2fe0f16e9712e17936
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840728"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-azure-cli"></a>Troubleshoot Virtual Network Gateway e Conexões usando Azure Network Watcher Azure CLI

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI do Azure](network-watcher-troubleshoot-manage-cli.md)
> - [API REST](network-watcher-troubleshoot-manage-rest.md)

O Network Watcher fornece muitas capacidades no que diz respeito à compreensão dos seus recursos de rede em Azure. Uma destas capacidades é a resolução de problemas de recursos. A resolução de problemas de recursos pode ser chamada através do portal PowerShell, CLI ou REST API. Quando chamado, o Observador de Rede inspeciona a saúde de um Gateway de Rede Virtual ou de uma Ligação e devolve as suas descobertas.

Para executar as etapas neste artigo, você precisa [instalar a interface de linha de comando do Azure para Mac, Linux e Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que você já seguiu as etapas em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede.

Para uma lista de visitas de tipos de gateway suportados, os tipos de [Gateway suportados](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Visão geral

A resolução de problemas de recursos fornece os problemas de resolução de problemas de capacidade que surgem com Gateways e Conexões de Rede Virtual. Quando é feito um pedido para a resolução de problemas de recursos, os registos estão a ser consultados e inspecionados. Quando a inspeção estiver concluída, os resultados são devolvidos. Os pedidos de resolução de problemas de recursos são pedidos de longa duração, que podem demorar vários minutos a devolver um resultado. Os registos de resolução de problemas são guardados num recipiente numa conta de armazenamento especificada.

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Recuperar uma ligação de gateway de rede virtual

Neste exemplo, a resolução de problemas de recursos está a ser remeter-se numa Ligação. Também pode passar-lhe um Portal de Rede Virtual. O cmdlet seguinte lista as ligações vpn num grupo de recursos.

```azurecli
az network vpn-connection list --resource-group resourceGroupName
```

Assim que tiver o nome da ligação, pode executar este comando para obter o seu recurso Id:

```azurecli
az network vpn-connection show --resource-group resourceGroupName --ids vpnConnectionIds
```

## <a name="create-a-storage-account"></a>Create a storage account

A resolução de problemas de recursos devolve dados sobre a saúde do recurso, também guarda registos para uma conta de armazenamento a ser revista. Neste passo, criamos uma conta de armazenamento, se existir uma conta de armazenamento existente, pode utilizá-la.

1. Criar a conta de armazenamento

    ```azurecli
    az storage account create --name storageAccountName --location westcentralus --resource-group resourceGroupName --sku Standard_LRS
    ```

1. Pegue as chaves da conta de armazenamento

    ```azurecli
    az storage account keys list --resource-group resourcegroupName --account-name storageAccountName
    ```

1. Criar o contentor

    ```azurecli
    az storage container create --account-name storageAccountName --account-key {storageAccountKey} --name logs
    ```

## <a name="run-network-watcher-resource-troubleshooting"></a>Executar Network Watcher resolução de problemas de recursos

Tu atiras recursos com o `az network watcher troubleshooting` cmdlet. Passamos pelo grupo de recursos, o nome do Observador da Rede, a identificação da ligação, a identificação da conta de armazenamento e o caminho para a bolha para armazenar o problema resulta.

```azurecli
az network watcher troubleshooting start --resource-group resourceGroupName --resource resourceName --resource-type {vnetGateway/vpnConnection} --storage-account storageAccountName  --storage-path https://{storageAccountName}.blob.core.windows.net/{containerName}
```

Uma vez executado o cmdlet, o Observador de Rede revê o recurso para verificar a saúde. Devolve os resultados à concha e armazena registos dos resultados na conta de armazenamento especificada.

## <a name="understanding-the-results"></a>Compreender os resultados

O texto de ação fornece orientações gerais sobre a forma de resolver a questão. Se for possível tomar uma medida para a questão, é fornecida uma ligação com orientações adicionais. No caso de não existir orientação adicional, a resposta fornece o url para abrir um caso de suporte.  Para mais informações sobre as propriedades da resposta e o que está incluído, visite a visão geral do [Network Watcher Troubleshoot](network-watcher-troubleshoot-overview.md)

Para obter instruções sobre o download de ficheiros de contas de armazenamento azure, consulte o Get started com o [armazenamento Azure Blob utilizando .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Outra ferramenta que pode ser usada é o Storage Explorer. Mais informações sobre o Storage Explorer podem ser encontradas aqui no seguinte link: [Storage Explorer](https://storageexplorer.com/)

## <a name="next-steps"></a>Passos seguintes

Se foram alteradas as definições que impedem a conectividade VPN, consulte [a Manage Network Security Groups](../virtual-network/manage-network-security-group.md) para localizar o grupo de segurança da rede e as regras de segurança que possam estar em causa.
