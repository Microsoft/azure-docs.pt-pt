---
title: Resolver problemas de ligações - CLI do Azure e do Gateway de rede Virtual do Azure | Documentos da Microsoft
description: Esta página explica como utilizar o observador de rede do Azure CLI do Azure de resolução de problemas
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 2838bc61-b182-4da8-8533-27db8fdbd177
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: 8910b4963c7f40b5b6ecdde06c7c7a0c9b86e97a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57899116"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-azure-cli"></a>Resolver problemas de Gateway de rede Virtual e ligações através da CLI do Azure do observador de rede do Azure

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI do Azure](network-watcher-troubleshoot-manage-cli.md)
> - [API REST](network-watcher-troubleshoot-manage-rest.md)

Observador de rede oferece muitos recursos que diz respeito à Noções básicas sobre os recursos de rede no Azure. Um desses recursos é o recurso de resolução de problemas. Resolução de problemas de recursos pode ser chamada através do portal, o PowerShell, a CLI ou a REST API. Quando chamado, o observador de rede inspeciona o estado de funcionamento de um Gateway de rede Virtual ou uma ligação e devolve conclusões.

Para efetuar os passos neste artigo, precisa [instale a interface de linha de comandos do Azure para Mac, Linux e Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que já tiver seguido os passos em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede.

Para obter uma lista de visita de tipos de gateway suportados [tipos de Gateway suportados](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Descrição geral

Resolução de problemas de recursos fornece a capacidade de resolver os problemas que surgem com ligações e Gateways de rede Virtual. Quando é efetuado um pedido para a resolução de problemas de recursos, os registos estão a ser consultado e inspecioná-lo. Quando a inspeção estiver concluída, os resultados são devolvidos. Recursos de pedidos de resolução de problemas são de longa pedidos, que pode demorar vários minutos para retornar um resultado. Os registos de resolução de problemas são armazenados num contentor numa conta de armazenamento que está especificado.

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Obter uma ligação de Gateway de rede Virtual

Neste exemplo, a resolução de problemas de recursos está a ser foi executada numa conexão. Também pode passá-lo um Gateway de rede Virtual. O cmdlet seguinte lista as ligações vpn num grupo de recursos.

```azurecli
az network vpn-connection list --resource-group resourceGroupName
```

Assim que tiver o nome da ligação, pode executar este comando para obter o seu Id de recurso:

```azurecli
az network vpn-connection show --resource-group resourceGroupName --ids vpnConnectionIds
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Resolução de problemas de recursos devolve dados sobre o estado de funcionamento do recurso, também economiza registos para uma conta de armazenamento para ser revisto. Neste passo, vamos criar uma conta de armazenamento, se existir uma conta de armazenamento existente pode utilizá-lo.

1. Criar a conta de armazenamento

    ```azurecli
    az storage account create --name storageAccountName --location westcentralus --resource-group resourceGroupName --sku Standard_LRS
    ```

1. Obter as chaves de conta de armazenamento

    ```azurecli
    az storage account keys list --resource-group resourcegroupName --account-name storageAccountName
    ```

1. Criar o contentor

    ```azurecli
    az storage container create --account-name storageAccountName --account-key {storageAccountKey} --name logs
    ```

## <a name="run-network-watcher-resource-troubleshooting"></a>Executar a resolução de problemas de recursos do observador de rede

Resolver problemas relacionados com recursos com o `az network watcher troubleshooting` cmdlet. Passamos o cmdlet o grupo de recursos, o nome do observador de rede, o Id da ligação, o Id da conta de armazenamento, e o caminho para o blob para armazenar a resolução de problemas resulta em.

```azurecli
az network watcher troubleshooting start --resource-group resourceGroupName --resource resourceName --resource-type {vnetGateway/vpnConnection} --storage-account storageAccountName  --storage-path https://{storageAccountName}.blob.core.windows.net/{containerName}
```

Depois de executar o cmdlet, o observador de rede analisa o recurso para verificar o estado de funcionamento. Ele retorna os resultados para o shell e armazena os registos dos resultados na conta de armazenamento especificada.

## <a name="understanding-the-results"></a>Noções básicas sobre os resultados

O texto de ação fornece orientações gerais sobre como resolver o problema. Se uma ação possa ser executada para o problema, é fornecida uma ligação com orientações adicionais. No caso em que não existe nenhuma orientação adicional, a resposta fornece o url para abrir um incidente de suporte.  Para obter mais informações sobre as propriedades de resposta e o que está incluído, visite [descrição geral de resolução de problemas de observador de rede](network-watcher-troubleshoot-overview.md)

Para obter instruções sobre o download de arquivos de contas de armazenamento do azure, consulte [introdução ao armazenamento de Blobs do Azure com o .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Outra ferramenta que pode ser utilizada é o Explorador de armazenamento. Obter mais informações sobre o Explorador de armazenamento podem ser encontradas aqui na seguinte hiperligação: [Explorador de armazenamento](https://storageexplorer.com/)

## <a name="next-steps"></a>Passos Seguintes

Se foram alteradas as definições de conectividade VPN que stop, veja [gerir grupos de segurança de rede](../virtual-network/manage-network-security-group.md) rastrear as regras de segurança e de grupo de segurança de rede que podem ser em questão.
