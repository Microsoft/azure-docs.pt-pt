---
title: Repor uma porta de entrada Azure VPN para restabelecer o túnel IPsec
description: Este artigo acompanha-o através da reposição do seu Azure VPN Gateway para restabelecer os túneis IPsec. O artigo aplica-se aos gateways VPN tanto nos modelos clássicos como nos modelos de implementação do Gestor de Recursos.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/09/2020
ms.author: cherylmc
ms.openlocfilehash: e3a5807a0ccfa39cc80acacedaa5fb4d3afaaed3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244423"
---
# <a name="reset-a-vpn-gateway"></a>Repor um Gateway de VPN

Repor o gateway de VPN do Azure é útil se perder a conectividade VPN em vários locais num ou mais túneis de rede de VPNs. Nesta situação, os dispositivos VPN no local estão todos a funcionar corretamente, mas não podem estabelecer túneis IPsec com os gateways de VPN do Azure. Este artigo ajuda-o a repor o seu gateway VPN.

### <a name="what-happens-during-a-reset"></a>O que acontece durante um reset?

Um gateway VPN é composto por dois casos vm que funcionam numa configuração de standby ativo. Quando reinicia o portal, reinicia o portal e, em seguida, reaplica as configurações de premissas cruzadas. O gateway mantém o endereço IP público que já tem, ou seja, não terá de atualizar a configuração do router da VPN com um novo endereço IP público para o VPN Gateway do Azure.

Quando emite o comando para reiniciar o gateway, a atual instância ativa do gateway Azure VPN é reiniciada imediatamente. Haverá uma breve lacuna durante a falha da instância ativa (sendo reiniciada), para a instância de espera. O intervalo deve ser inferior a um minuto.

Se a ligação não for restaurada após o primeiro reinício, execute o mesmo comando novamente para reiniciar a segunda instância da VM (o novo gateway ativo). Se forem pedidos dois reinícios seguidos, haverá um período ligeiramente superior em que ambas as instâncias da VM (ativa e no modo de espera) estão a ser reiniciadas. Isto causará uma maior lacuna na conectividade VPN, até 30 a 45 minutos para os VMs completarem as reinicializações.

Após duas reinicializações, se ainda estiver com problemas de conectividade transversal, por favor abra um pedido de apoio do portal Azure.

## <a name="before-you-begin"></a><a name="before"></a>Antes de começar

Antes de repor o gateway, verifique os itens principais listados abaixo para cada túnel de rede de VPNs (S2S) do IPsec. Qualquer erro de correspondência nos itens desligará os túneis VPN S2S. Verificar e corrigir as configurações para as suas instalações e os gateways Azure VPN salva-o de reboots e interrupções desnecessárias para as outras ligações de trabalho nos gateways.

Verifique os seguintes itens antes de reiniciar o seu portal:

* Os endereços IP de Internet (VIPs) do VPN Gateway do Azure e do gateway de VPN no local estão corretamente configurados nas políticas de VPN do Azure e no local.
* A chave pré-partilhada tem de ser igual no VPN Gateway do Azure e no gateway no local.
* Se aplicar uma configuração IPsec/IKE específica, tal como encriptação, algoritmos hash e PFS (Perfect Forward Secrecy), confirme se o VPN Gateway do Azure e o gateway no local têm as mesmas configurações.

## <a name="azure-portal"></a><a name="portal"></a>Portal Azure

Pode redefinir um gateway VPN do Gestor de Recursos utilizando o portal Azure. Se quiser repor um portal clássico, consulte os passos [powerShell.](#resetclassic)

### <a name="resource-manager-deployment-model"></a>Modelo de implementação Resource Manager

1. Abra o [portal Azure](https://portal.azure.com) e navegue até ao portal de rede virtual do Gestor de Recursos que pretende repor.
2. Na lâmina para o gateway de rede virtual, clique em 'Reset'.

   ![Redefinir a lâmina de gateway VPN](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)
3. Na lâmina de reset, clique no botão **Reset.**

## <a name="powershell"></a><a name="ps"></a>PowerShell

### <a name="resource-manager-deployment-model"></a>Modelo de implementação Resource Manager

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O cmdlet para redefinir um gateway é **Reset-AzVirtualNetworkGateway**. Antes de efetuar um reset, certifique-se de que tem a versão mais recente dos [cmdlets PowerShell Az](https://docs.microsoft.com/powershell/module/az.network). O exemplo seguinte repõe um portal de rede virtual chamado VNet1GW no grupo de recursos TestRG1:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Resultado:

Quando recebe um resultado de devolução, pode assumir que o reset do gateway foi bem sucedido. No entanto, não há nada no resultado da devolução que indique explicitamente que o reset foi bem sucedido. Se quiser olhar atentamente para a história para ver exatamente quando ocorreu o reset do portal, pode ver essa informação no [portal Azure](https://portal.azure.com). No portal, navegue para **'GatewayName' -> Saúde dos Recursos**.

### <a name="classic-deployment-model"></a><a name="resetclassic"></a>Modelo de implantação clássico

O cmdlet para redefinir um gateway é **Reset-AzureVNetGateway**. Os cmdlets Azure PowerShell para Gestão de Serviços devem ser instalados localmente no seu ambiente de trabalho. Não podes usar a Azure Cloud Shell. Antes de efetuar um reset, certifique-se de que tem a versão mais recente dos [cmdlets PowerShell (Gestão de Serviços) PowerShell](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets). Ao utilizar este comando, certifique-se de que está a utilizar o nome completo da rede virtual. Os VNets clássicos que foram criados usando o portal têm um nome longo que é necessário para powerShell. Pode ver o nome longo utilizando 'Get-AzureVNetConfig -ExportToFile C:\Myfoldername\NetworkConfig.xml'.

O exemplo seguinte repõe a porta de entrada para uma rede virtual chamada "Group TestRG1 TestVNet1" (que mostra como simplesmente "TestVNet1" no portal):

```powershell
Reset-AzureVNetGateway –VnetName 'Group TestRG1 TestVNet1'
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

## <a name="azure-cli"></a><a name="cli"></a>Azure CLI

Para redefinir o gateway, utilize o comando de reset de [rede az vnet gateway.](https://docs.microsoft.com/cli/azure/network/vnet-gateway) O exemplo seguinte repõe um portal de rede virtual chamado VNet5GW no grupo de recursos TestRG5:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Resultado:

Quando recebe um resultado de devolução, pode assumir que o reset do gateway foi bem sucedido. No entanto, não há nada no resultado da devolução que indique explicitamente que o reset foi bem sucedido. Se quiser olhar atentamente para a história para ver exatamente quando ocorreu o reset do portal, pode ver essa informação no [portal Azure](https://portal.azure.com). No portal, navegue para **'GatewayName' -> Saúde dos Recursos**.
