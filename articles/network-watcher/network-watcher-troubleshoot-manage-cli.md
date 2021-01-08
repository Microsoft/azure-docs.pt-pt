---
title: Resolução de problemas Azure VNET Gateway e Conexões - Azure CLI
titleSuffix: Azure Network Watcher
description: Esta página explica como usar o Azure Network Watcher troubleshoot Azure CLI
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: d04ba6cc5f8527f8c05fbeb323c65230447e1e2e
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019708"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-azure-cli"></a>Resolução de problemas Gateway de rede virtual e conexões usando Azure Network Watcher Azure CLI

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI do Azure](network-watcher-troubleshoot-manage-cli.md)
> - [API REST](network-watcher-troubleshoot-manage-rest.md)

O Network Watcher fornece muitas capacidades no que diz respeito à compreensão dos recursos da sua rede em Azure. Uma destas capacidades é a resolução de problemas de recursos. A resolução de problemas de recursos pode ser chamada através do portal, PowerShell, CLI ou REST API. Quando é chamado, o Network Watcher inspeciona a saúde de um Gateway de Rede Virtual ou de uma Ligação e devolve as suas conclusões.

Para executar os passos neste artigo, é necessário [instalar a interface de linha de comando Azure para Mac, Linux e Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que já seguiu os passos na [Criação de um Observador de Rede](network-watcher-create.md) para criar um Observador de Rede.

Para uma lista de tipos de gateway suportados visite, [tipos de Gateway suportados](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Descrição geral

A resolução de problemas de recursos fornece os problemas de resolução de problemas de capacidade que surgem com gateways e conexões de rede virtual. Quando é feito um pedido para a resolução de problemas de recursos, os registos estão a ser consultados e inspecionados. Quando a inspeção estiver concluída, os resultados são devolvidos. Os pedidos de resolução de problemas de recursos são pedidos de longa duração, o que pode demorar vários minutos a devolver um resultado. Os registos da resolução de problemas são armazenados num contentor numa conta de armazenamento especificada.

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Recuperar uma ligação virtual de gateway de rede

Neste exemplo, a resolução de problemas de recursos está a ser escorri á Ligação. Também pode passar por um Gateway de Rede Virtual. O cmdlet que se segue lista as ligações VPN num grupo de recursos.

```azurecli
az network vpn-connection list --resource-group resourceGroupName
```

Assim que tiver o nome da ligação, pode executar este comando para obter o seu ID de recursos:

```azurecli
az network vpn-connection show --resource-group resourceGroupName --ids vpnConnectionIds
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Resolução de problemas de recursos Devolve dados sobre a saúde do recurso, também guarda registos para uma conta de armazenamento a ser revista. Neste passo, criamos uma conta de armazenamento, se existir uma conta de armazenamento existente, pode usá-la.

1. Criar a conta de armazenamento

    ```azurecli
    az storage account create --name storageAccountName --location westcentralus --resource-group resourceGroupName --sku Standard_LRS
    ```

1. Obtenha as chaves da conta de armazenamento

    ```azurecli
    az storage account keys list --resource-group resourcegroupName --account-name storageAccountName
    ```

1. Criar o contentor

    ```azurecli
    az storage container create --account-name storageAccountName --account-key {storageAccountKey} --name logs
    ```

## <a name="run-network-watcher-resource-troubleshooting"></a>Executar resolução de problemas de recursos do Observador de Rede

Resolva os recursos com o `az network watcher troubleshooting` cmdlet. Passamos o cmdlet do grupo de recursos, o nome do Observador de Rede, o ID da ligação, o ID da conta de armazenamento, e o caminho para a bolha para armazenar os resultados da resolução de problemas.

```azurecli
az network watcher troubleshooting start --resource-group resourceGroupName --resource resourceName --resource-type {vnetGateway/vpnConnection} --storage-account storageAccountName  --storage-path https://{storageAccountName}.blob.core.windows.net/{containerName}
```

Uma vez executado o cmdlet, o Network Watcher revê o recurso para verificar a saúde. Devolve os resultados à concha e armazena registos dos resultados na conta de armazenamento especificada.

## <a name="understanding-the-results"></a>Compreender os resultados

O texto de ação fornece orientações gerais sobre como resolver a questão. Se for possível tomar uma ação para esta questão, é fornecida uma ligação com orientações adicionais. No caso de não existir orientação adicional, a resposta fornece a url para abrir um caso de apoio.  Para mais informações sobre as propriedades da resposta e o que está incluído, visite [a visão geral da resolução de problemas do Observador de Rede](network-watcher-troubleshoot-overview.md)

Para obter instruções sobre o descarregamento de ficheiros a partir de contas de armazenamento azul, consulte para [começar com o armazenamento Azure Blob usando .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md). Outra ferramenta que pode ser usada é o Storage Explorer. Mais informações sobre o Storage Explorer podem ser encontradas aqui no seguinte link: [Storage Explorer](https://storageexplorer.com/)

## <a name="next-steps"></a>Próximos passos

Se as definições tiverem sido alteradas que impeçam a conectividade VPN, consulte [Os Grupos de Segurança da Rede](../virtual-network/manage-network-security-group.md) para localizar o grupo de segurança da rede e as regras de segurança que podem estar em causa.