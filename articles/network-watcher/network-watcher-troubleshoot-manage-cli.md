---
title: Solucionar problemas de gateway e conexões de VNET do Azure-CLI do Azure
titleSuffix: Azure Network Watcher
description: Esta página explica como usar a solução de problemas do observador de rede do Azure CLI do Azure
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 2838bc61-b182-4da8-8533-27db8fdbd177
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: kumud
ms.openlocfilehash: ca61486ce58ccd3385518c2d22e0690c1fb34d16
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277818"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-azure-cli"></a>Solucionar problemas de gateway de rede virtual e conexões usando o observador de rede do Azure CLI do Azure

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI do Azure](network-watcher-troubleshoot-manage-cli.md)
> - [API REST](network-watcher-troubleshoot-manage-rest.md)

O observador de rede fornece muitos recursos que se relacionam com a compreensão de seus recursos de rede no Azure. Um desses recursos é a solução de problemas de recursos. A solução de problemas de recursos pode ser chamada por meio do portal, do PowerShell, da CLI ou da API REST. Quando chamado, o observador de rede inspeciona a integridade de um gateway de rede virtual ou uma conexão e retorna suas descobertas.

Para executar as etapas neste artigo, você precisa [instalar a interface de linha de comando do Azure para Mac, Linux e Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que você já seguiu as etapas em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede.

Para obter uma lista de tipos de gateway com suporte, visite [tipos de gateway com suporte](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Descrição geral

A solução de problemas de recursos fornece a capacidade de solucionar problemas que surgem com gateways de rede virtual e conexões. Quando uma solicitação é feita para a solução de problemas de recursos, os logs são consultados e inspecionados. Quando a inspeção for concluída, os resultados serão retornados. As solicitações de solução de problemas de recursos são solicitações de execução longa, o que pode levar vários minutos para retornar um resultado. Os logs da solução de problemas são armazenados em um contêiner em uma conta de armazenamento que é especificada.

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Recuperar uma conexão de gateway de rede virtual

Neste exemplo, a solução de problemas de recursos está sendo executada em uma conexão. Você também pode passá-lo para um gateway de rede virtual. O cmdlet a seguir lista as conexões VPN em um grupo de recursos.

```azurecli
az network vpn-connection list --resource-group resourceGroupName
```

Depois de ter o nome da conexão, você pode executar este comando para obter sua ID de recurso:

```azurecli
az network vpn-connection show --resource-group resourceGroupName --ids vpnConnectionIds
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

A solução de problemas de recursos retorna dados sobre a integridade do recurso; ela também salva os logs em uma conta de armazenamento a ser revisada. Nesta etapa, criaremos uma conta de armazenamento, se existir uma conta de armazenamento existente, você poderá usá-la.

1. Criar a conta de armazenamento

    ```azurecli
    az storage account create --name storageAccountName --location westcentralus --resource-group resourceGroupName --sku Standard_LRS
    ```

1. Obter as chaves da conta de armazenamento

    ```azurecli
    az storage account keys list --resource-group resourcegroupName --account-name storageAccountName
    ```

1. Criar o contentor

    ```azurecli
    az storage container create --account-name storageAccountName --account-key {storageAccountKey} --name logs
    ```

## <a name="run-network-watcher-resource-troubleshooting"></a>Executar solução de problemas de recursos do observador de rede

Você soluciona problemas de recursos com o cmdlet `az network watcher troubleshooting`. Passamos o cmdlet do grupo de recursos, o nome do observador de rede, a ID da conexão, a ID da conta de armazenamento e o caminho para o blob para armazenar os resultados da solução de problemas.

```azurecli
az network watcher troubleshooting start --resource-group resourceGroupName --resource resourceName --resource-type {vnetGateway/vpnConnection} --storage-account storageAccountName  --storage-path https://{storageAccountName}.blob.core.windows.net/{containerName}
```

Depois de executar o cmdlet, o observador de rede revisa o recurso para verificar a integridade. Ele retorna os resultados para o Shell e armazena os logs dos resultados na conta de armazenamento especificada.

## <a name="understanding-the-results"></a>Compreendendo os resultados

O texto da ação fornece orientação geral sobre como resolver o problema. Se uma ação puder ser executada para o problema, será fornecido um link com diretrizes adicionais. No caso em que não há nenhuma orientação adicional, a resposta fornece a URL para abrir um caso de suporte.  Para obter mais informações sobre as propriedades da resposta e o que está incluído, visite [visão geral de solução de problemas do observador de rede](network-watcher-troubleshoot-overview.md)

Para obter instruções sobre como baixar arquivos de contas de armazenamento do Azure, consulte Introdução ao [armazenamento de BLOBs do Azure usando o .net](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Outra ferramenta que pode ser usada é Gerenciador de Armazenamento. Mais informações sobre Gerenciador de Armazenamento podem ser encontradas aqui no seguinte link: [Gerenciador de armazenamento](https://storageexplorer.com/)

## <a name="next-steps"></a>Passos seguintes

Se as configurações tiverem sido alteradas para interromper a conectividade VPN, consulte [gerenciar grupos de segurança de rede](../virtual-network/manage-network-security-group.md) para rastrear o grupo de segurança de rede e as regras de segurança que podem estar em questão.
