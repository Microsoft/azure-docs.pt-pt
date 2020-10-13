---
title: Ligue vNets inter-inquilino a um hub:PowerShell
titleSuffix: Azure Virtual WAN
description: Este artigo ajuda-o a ligar VNets de inquilinos cruzados a um hub virtual usando o PowerShell.
services: virtual-wan
author: wtnlee
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/28/2020
ms.author: wellee
ms.openlocfilehash: 881f955014032d18fec447784a879fbf4f0e24fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91571486"
---
# <a name="connect-cross-tenant-vnets-to-a-virtual-wan-hub"></a>Ligue VNets de inquilino transversal a um hub Virtual Wan

Este artigo ajuda-o a usar o VIRTUAL WAN para ligar um VNet a um centro virtual num inquilino diferente. Esta arquitetura é útil se você tem cargas de trabalho de clientes que devem estar conectados para ser a mesma rede, mas estão em inquilinos diferentes. Por exemplo, como mostrado no diagrama seguinte, você pode ligar um VNet não-Contoso (o Inquilino Remoto) a um centro virtual Contoso (o Inquilino-Mãe).

:::image type="content" source="./media/cross-tenant-vnet/connectivity.png" alt-text="Configurar configuração de encaminhamento" :::

Neste artigo, vai aprender a:

* Adicione outro inquilino como Contribuinte na sua assinatura Azure.
* Ligue um inquilino cruzado VNet a um centro virtual.

Os passos para esta configuração são realizados usando uma combinação do portal Azure e PowerShell. No entanto, a funcionalidade em si está disponível apenas em PowerShell e CLI.

## <a name="before-you-begin"></a>Antes de Começar

### <a name="prerequisites"></a>Pré-requisitos

Para utilizar os passos deste artigo, tem de ter a seguinte configuração já configurada no seu ambiente:

* Um WAN virtual e um hub virtual na subscrição dos seus pais.
* Uma rede virtual configurada numa subscrição num inquilino diferente (remoto).
* Certifique-se de que o espaço de endereço VNet no inquilino remoto não se sobrepõe a qualquer outro espaço de endereço dentro de quaisquer outros VNets já ligados ao centro virtual dos pais.

### <a name="working-with-azure-powershell"></a>Trabalhar com a Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="assign-permissions"></a><a name="rights"></a>Atribuir permissões

Para que a subscrição dos pais com o centro virtual modifique e aceda às redes virtuais no inquilino remoto, é necessário atribuir permissões **de Contribuinte** à subscrição dos seus pais a partir da subscrição do inquilino remoto.

1. Adicione a atribuição de função **contribuinte** à conta-mãe (a que tem o hub WAN virtual). Pode utilizar o PowerShell ou o portal Azure para atribuir este papel. Consulte os **seguintes artigos de atribuição de funções ou de atribuição de funções** para etapas:

   * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
   * [Portal](../role-based-access-control/role-assignments-portal.md)

1. Em seguida, adicione a subscrição de inquilino remoto e a subscrição do inquilino-mãe para a sessão atual da PowerShell. Execute o seguinte comando. Se você está assinado no pai, você só precisa executar o comando para o inquilino remoto.

   ```azurepowershell-interactive
   Add-AzAccount “xxxxx-b34a-4df9-9451-4402dcaecc5b”
   ```

1. Verifique se a atribuição de funções é bem sucedida ao iniciar sessão no Azure PowerShell utilizando as credenciais dos pais e executar o seguinte comando:

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. Se as permissões se propagarem com sucesso ao progenitor e tiverem sido adicionadas à sessão, a subscrição detida pelo inquilino remoto aparecerá na saída do comando.

## <a name="connect-vnet-to-hub"></a><a name="connect"></a>Ligue o VNet ao hub

Nos passos seguintes, irá alternar entre o contexto das duas subscrições à medida que liga a rede virtual ao centro virtual. Substitua os valores de exemplo para refletir o seu próprio ambiente.

1. Certifique-se de que está no contexto da sua conta remota executando o seguinte comando:

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId “[remote ID]”
   ```

1. Crie uma variável local para armazenar os metadados da rede virtual que pretende ligar ao hub.

   ```azurepowershell-interactive
   $remote = Get-AzVirtualNetwork -Name "[v-net name]" -ResourceGroupName "[resource group name]"
   ```

1. Mude de volta para a conta dos pais.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId “[parent ID]”
   ```

1. Ligue o VNet ao centro.

   ```azurepowershell-interactive
   New-AzVirtualHubVnetConnection -ResourceGroupName "[Parent Resource Group Name]" -VirtualHubName "[virtual hub name]" -Name "[name of connection]" -RemoteVirtualNetwork $[local variable name]
   ```

1. Pode ver a nova ligação em PowerShell ou no portal Azure.

   * **PowerShell:** Os metadados da ligação recém-formada mostrarão na consola PowerShell se a ligação foi formada com sucesso.
   * **Portal Azure:** Navegue para o centro virtual, **conectividade -> conexões de rede virtual**. Pode ver o ponteiro para a ligação. Para ver o recurso real, você precisará das permissões adequadas.
   
## <a name="troubleshooting"></a><a name="troubleshoot"></a>Resolução de problemas

* Verifique se os metadados em $remote (da [secção](#connect)anterior) correspondem às informações do portal Azure.
* Pode verificar permissões utilizando as definições de IAM do grupo de recursos do inquilino remoto, ou utilizando comandos Azure PowerShell (Get-AzSubscription).
* Certifique-se de que as cotações estão incluídas em torno dos nomes de grupos de recursos ou de quaisquer outras variáveis específicas do ambiente (por exemplo. "VirtualHub1" ou "VirtualNetwork1").

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o VIRTUAL WAN, consulte:

* O VIRTUAL WAN [FAQ](virtual-wan-faq.md)
