---
title: Verificar a utilização de recursos do Azure em relação a limites | Documentos da Microsoft
description: Saiba como verificar a sua utilização de recursos do Azure em relação a limites de subscrição do Azure.
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
ms.author: jdial
ms.openlocfilehash: 56434e12e1d5eec34e8848d71a018147f1e161f6
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57777265"
---
# <a name="check-resource-usage-against-limits"></a>Verificar a utilização de recursos em relação a limites

Neste artigo, irá aprender a ver o número de cada tipo de recurso de rede que tenha implementado na sua subscrição e o que sua [limites de subscrição](../azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) são. A capacidade de ver a utilização de recursos em relação a limites é útil para controlar a utilização atual e planear a utilização futura. Pode utilizar o [Portal do Azure](#azure-portal), [PowerShell](#powershell), ou o [da CLI do Azure](#azure-cli) para controlar a utilização.

## <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no Azure [portal](https://portal.azure.com).
2. No canto superior, à esquerda do portal do Azure, selecione **todos os serviços**.
3. Introduza *subscrições* no **filtro** caixa. Quando **subscrições** aparecer nos resultados da pesquisa, selecione.
4. Selecione o nome da subscrição que pretende ver informações de utilização.
5. Sob **configurações**, selecione **utilização + quotas**.
6. Pode selecionar as seguintes opções:
    - **Tipos de recurso**: Pode selecionar todos os tipos de recursos ou selecione os tipos específicos de recursos que pretende visualizar.
    - **Fornecedores**: Pode selecionar todos os fornecedores de recursos ou selecione **computação**, **rede**, ou **armazenamento**.
    - **Localizações**: Pode selecionar todas as localizações do Azure, ou selecione as localizações específicas.
    - Pode selecionar para mostrar todos os recursos ou apenas os recursos em que pelo menos uma é implementada.

    O exemplo na imagem seguinte mostra todos os recursos de rede pelo menos um recurso implementado nos EUA Leste:

        ![View usage data](./media/check-usage-against-limits/view-usage.png)

    Pode ordenar as colunas ao selecionar o cabeçalho da coluna. Os limites mostrados são os limites para a sua subscrição. Se precisar de aumentar um limite predefinido, selecione **pedido aumentar**, em seguida, preencha e submeta o pedido de suporte. Todos os recursos têm um limite máximo listado no Azure [limites](../azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits). Se o limite atual já o número máximo, o limite não pode ser aumentado.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode executar os comandos que se seguem na [Azure Cloud Shell](https://shell.azure.com/powershell), ou ao executar o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Se executar o PowerShell a partir do seu computador, terá do módulo Azure PowerShell, versão 1.0.0 ou posterior. Executar `Get-Module -ListAvailable Az` no seu computador, para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver executando o PowerShell localmente, terá também de executar `Login-AzAccount` para iniciar sessão no Azure.

Ver a utilização de limites com [Get-AzNetworkUsage](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkusage). O exemplo seguinte obtém a utilização de recursos em que pelo menos um recurso é implementado na localização E.U.A. Leste:

```azurepowershell-interactive
Get-AzNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

Recebe um resultado formatado com o mesmo que o resultado de exemplo seguinte:

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

Se utilizar comandos de interface de linha de comandos (CLI) do Azure para concluir tarefas neste artigo, a executar os comandos do [Azure Cloud Shell](https://shell.azure.com/bash), ou ao executar a CLI do seu computador. Este artigo requer a versão 2.0.32 CLI do Azure ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver a executar a CLI do Azure localmente, terá também de executar `az login` para iniciar sessão no Azure.

Ver a utilização de limites com [lista de rede de az-usages](/cli/azure/network?view=azure-cli-latest#az-network-list-usages). O exemplo seguinte obtém a utilização de recursos na localização E.U.A. Leste:

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

Recebe um resultado formatado com o mesmo que o resultado de exemplo seguinte:

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
