---
title: Repor um gateway de VPN do Azure para restabelecer túneis IPsec | Documentos da Microsoft
description: Este artigo explica como repor o Gateway de VPN do Azure para restabelecer túneis IPsec. O artigo aplica-se aos gateways de VPN no clássica e os modelos de implementação do Resource Manager.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/14/2019
ms.author: cherylmc
ms.openlocfilehash: d09f03aed8a739eaaaf8c60c379a746551d3b6f8
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57009890"
---
# <a name="reset-a-vpn-gateway"></a>Repor um Gateway de VPN

Repor o gateway de VPN do Azure é útil se perder a conectividade VPN em vários locais num ou mais túneis de rede de VPNs. Nesta situação, os dispositivos VPN no local estão todos a funcionar corretamente, mas não podem estabelecer túneis IPsec com os gateways de VPN do Azure. Este artigo ajuda-o a repor o gateway VPN.

### <a name="what-happens-during-a-reset"></a>O que acontece durante uma reposição?

Um gateway de VPN é composto por duas instâncias de VM em execução numa configuração ativo-em espera. Ao repor o gateway, reinicia o gateway e, em seguida, volta a aplicar as configurações em vários locais ao mesmo. O gateway mantém o endereço IP público que já tem, ou seja, não terá de atualizar a configuração do router da VPN com um novo endereço IP público para o VPN Gateway do Azure.

Quando emitir o comando para repor o gateway, a instância ativa atual do gateway de VPN do Azure é reiniciada imediatamente. Haverá um intervalo breve durante a ativação pós-falha da instância ativa (a ser reiniciadas), para a instância em espera. O intervalo deve ser inferior a um minuto.

Se a ligação não for restaurada após o primeiro reinício, execute o mesmo comando novamente para reiniciar a segunda instância da VM (o novo gateway ativo). Se forem pedidos dois reinícios seguidos, haverá um período ligeiramente superior em que ambas as instâncias da VM (ativa e no modo de espera) estão a ser reiniciadas. Isto resultará num intervalo maior na conetividade da VPN, de até 2 a 4 minutos para as VMs concluírem os reinícios.

Depois de dois reinícios, se ainda ocorrerem problemas de conectividade em vários locais, abra um pedido de suporte do portal do Azure.

## <a name="before"></a>Antes de começar

Antes de repor o gateway, verifique os itens principais listados abaixo para cada túnel de rede de VPNs (S2S) do IPsec. Qualquer erro de correspondência nos itens desligará os túneis VPN S2S. A verificar e corrigir as configurações para no local e gateways de VPN do Azure poupa de reinícios desnecessários e interrupções para as outras ligações de trabalho nos gateways.

Antes de repor o gateway, verifique se os seguintes itens:

* Os endereços IP de Internet (VIPs) do VPN Gateway do Azure e do gateway de VPN no local estão corretamente configurados nas políticas de VPN do Azure e no local.
* A chave pré-partilhada tem de ser igual no VPN Gateway do Azure e no gateway no local.
* Se aplicar uma configuração IPsec/IKE específica, tal como encriptação, algoritmos hash e PFS (Perfect Forward Secrecy), confirme se o VPN Gateway do Azure e o gateway no local têm as mesmas configurações.

## <a name="portal"></a>Portal do Azure

Pode repor um gateway de VPN do Resource Manager com o portal do Azure. Se pretender repor um gateway clássico, veja a [PowerShell](#resetclassic) passos.

### <a name="resource-manager-deployment-model"></a>Modelo de implementação Resource Manager

1. Abra o [portal do Azure](https://portal.azure.com) e navegue para o gateway de rede virtual do Gestor de recursos que pretende repor.
2. No painel para o gateway de rede virtual, clique em "Repor".

  ![Repor o painel do Gateway de VPN](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)
3. No painel da reposição, clique nas **repor** botão.

## <a name="ps"></a>PowerShell

### <a name="resource-manager-deployment-model"></a>Modelo de implementação Resource Manager

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O cmdlet para repor um gateway é **reposição-AzVirtualNetworkGateway**. Antes de executar uma reposição, certifique-se de que tem a versão mais recente do [cmdlets do PowerShell Az](https://docs.microsoft.com/powershell/module/az.network). O exemplo seguinte repõe um gateway de rede virtual com o nome VNet1GW no grupo de recursos de TestRG1:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Resultado:

Quando receber um resultado de retorno, pode assumir a reposição do gateway foi concluída com êxito. No entanto, não há nada no resultado de retorno que indica explicitamente que a reposição foi concluída com êxito. Se quiser examinar de perto o histórico para ver exatamente quando a reposição do gateway ocorreu, pode ver as informações que o [portal do Azure](https://portal.azure.com). No portal, navegue para **"GatewayName" -> Estado de funcionamento do recurso**.

### <a name="resetclassic"></a>Modelo de implementação clássica

O cmdlet para repor um gateway é **Reset-AzureVNetGateway**. Antes de executar uma reposição, certifique-se de que tem a versão mais recente do [cmdlets do PowerShell de gestão de serviço (SM)](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets). O exemplo seguinte repõe o gateway para uma rede virtual denominada "ContosoVNet":

```powershell
Reset-AzureVNetGateway –VnetName “ContosoVNet”
```

Resultado:

```powershell
Error          :
HttpStatusCode : OK
Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
Status         : Successful
RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
StatusCode     : OK
```

## <a name="cli"></a>CLI do Azure

Para repor o gateway, utilize o [vnet-gateway de rede de az repor](https://docs.microsoft.com/cli/azure/network/vnet-gateway) comando. O exemplo seguinte repõe um gateway de rede virtual com o nome VNet5GW no grupo de recursos de TestRG5:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Resultado:

Quando receber um resultado de retorno, pode assumir a reposição do gateway foi concluída com êxito. No entanto, não há nada no resultado de retorno que indica explicitamente que a reposição foi concluída com êxito. Se quiser examinar de perto o histórico para ver exatamente quando a reposição do gateway ocorreu, pode ver as informações que o [portal do Azure](https://portal.azure.com). No portal, navegue para **"GatewayName" -> Estado de funcionamento do recurso**.
