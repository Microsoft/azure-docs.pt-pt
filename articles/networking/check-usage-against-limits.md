---
title: Verificar o uso de recursos do Azure em relação aos limites | Microsoft Docs
description: Saiba como verificar o uso de recursos do Azure em relação aos limites de assinatura do Azure.
services: networking
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/05/2018
ms.author: jeconnoc
ms.openlocfilehash: 5c807397af3ee06b490017b7e14f7b64123b5075
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645230"
---
# <a name="check-resource-usage-against-limits"></a>Verificar o uso de recursos em relação aos limites

Neste artigo, você aprenderá a ver o número de cada tipo de recurso de rede que você implantou em sua assinatura e quais são seus [limites de assinatura](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) . A capacidade de exibir o uso de recursos em relação aos limites é útil para acompanhar o uso atual e planejar o uso futuro. Você pode usar o [portal do Azure](#azure-portal), o [PowerShell](#powershell)ou o [CLI do Azure](#azure-cli) para acompanhar o uso.

## <a name="azure-portal"></a>Portal do Azure

1. Faça logon no [portal](https://portal.azure.com)do Azure.
2. Na parte superior, canto esquerdo da portal do Azure, selecione **todos os serviços**.
3. Insira *assinaturas* na caixa de **filtro** . Quando a opção **Subscrições** aparecer nos resultados da pesquisa, selecione-a.
4. Selecione o nome da assinatura para a qual você deseja exibir informações de uso.
5. Em **configurações**, selecione **uso + cota**.
6. Pode selecionar as seguintes opções:
   - **Tipos de recursos**: você pode selecionar todos os tipos de recursos ou selecionar os tipos específicos de recursos que deseja exibir.
   - **Provedores**: você pode selecionar todos os provedores de recursos ou selecionar **computação**, **rede**ou **armazenamento**.
   - **Locais**: você pode selecionar todos os locais do Azure ou selecionar locais específicos.
   - Você pode selecionar para mostrar todos os recursos ou apenas os recursos em que pelo menos um é implantado.

     O exemplo na figura a seguir mostra todos os recursos de rede com pelo menos um recurso implantado no leste dos EUA:

       ![Exibir dados de uso](./media/check-usage-against-limits/view-usage.png)

     Você pode classificar as colunas selecionando o título da coluna. Os limites mostrados são os limites para sua assinatura. Se você precisar aumentar um limite padrão, selecione a **solicitação aumentar**e, em seguida, conclua e envie a solicitação de suporte. Todos os recursos têm um limite máximo listado nos [limites](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits)do Azure. Se o limite atual já estiver no número máximo, o limite não poderá ser aumentado.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Você pode executar os comandos a seguir no [Azure cloud Shell](https://shell.azure.com/powershell)ou executando o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Se você executar o PowerShell do seu computador, precisará do módulo Azure PowerShell, versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` em seu computador para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar `Login-AzAccount` para fazer logon no Azure.

Exiba seu uso em relação aos limites com [Get-AzNetworkUsage](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkusage). O exemplo a seguir obtém o uso de recursos em que pelo menos um recurso é implantado no local leste dos EUA:

```azurepowershell-interactive
Get-AzNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

Você recebe a saída formatada da mesma forma que a seguinte saída de exemplo:

```powershell
ResourceType            CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
```

## <a name="azure-cli"></a>CLI do Azure

Se você estiver usando comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, execute os comandos no [Azure cloud Shell](https://shell.azure.com/bash)ou executando a CLI do seu computador. Este artigo requer o CLI do Azure versão 2.0.32 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver executando o CLI do Azure localmente, você também precisará executar `az login` para fazer logon no Azure.

Exiba seu uso em relação aos limites com [AZ Network List-usos](/cli/azure/network?view=azure-cli-latest#az-network-list-usages). O exemplo a seguir obtém o uso de recursos no local leste dos EUA:

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

Você recebe a saída formatada da mesma forma que a seguinte saída de exemplo:

```azurecli
Name                    CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
Load Balancers                     0   100
Application Gateways               0    50
```
