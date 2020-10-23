---
title: Repor uma porta de entrada Azure VPN para restabelecer o túnel IPsec
description: Reinicie o seu Gateway Azure VPN para restabelecer os túneis IPsec para gateways VPN tanto nos modelos clássicos como nos modelos de implementação do Gestor de Recursos.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/21/2020
ms.author: cherylmc
ms.openlocfilehash: e39884f6d62fc43943f892aed0dac650a01d6c40
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92463013"
---
# <a name="reset-a-vpn-gateway"></a>Repor um Gateway de VPN

Repor o gateway de VPN do Azure é útil se perder a conectividade VPN em vários locais num ou mais túneis de rede de VPNs. Nesta situação, os dispositivos VPN no local estão todos a funcionar corretamente, mas não podem estabelecer túneis IPsec com os gateways de VPN do Azure. Este artigo ajuda-o a redefinir o seu gateway VPN.

### <a name="what-happens-during-a-reset"></a>O que acontece durante um reset?

Um gateway VPN é composto por duas instâncias VM em execução numa configuração de espera ativa. Quando reinicia o portal, reinicia o gateway e, em seguida, reaplica as configurações das premissas cruzadas. O gateway mantém o endereço IP público que já tem, ou seja, não terá de atualizar a configuração do router da VPN com um novo endereço IP público para o VPN Gateway do Azure.

Quando emite o comando para reiniciar o gateway, a instância ativa atual do gateway Azure VPN é reiniciada imediatamente. Haverá uma breve lacuna durante o failover desde o caso ativo (sendo reiniciado), para a instância de espera. O intervalo deve ser inferior a um minuto.

Se a ligação não for restaurada após o primeiro reinício, execute o mesmo comando novamente para reiniciar a segunda instância da VM (o novo gateway ativo). Se forem pedidos dois reinícios seguidos, haverá um período ligeiramente superior em que ambas as instâncias da VM (ativa e no modo de espera) estão a ser reiniciadas. Isto irá causar uma maior lacuna na conectividade VPN, até 30 a 45 minutos para os VMs completarem os reboots.

Depois de duas reinicializações, se ainda estiver a ter problemas de conectividade nas instalações, abra um pedido de apoio do portal Azure.

## <a name="before-you-begin"></a><a name="before"></a>Antes de começar

Antes de repor o gateway, verifique os itens principais listados abaixo para cada túnel de rede de VPNs (S2S) do IPsec. Qualquer erro de correspondência nos itens desligará os túneis VPN S2S. Verificar e corrigir as configurações para as suas entradas no local e gateways Azure VPN salva-o de reboots e interrupções desnecessárias para as outras ligações de trabalho nos gateways.

Verifique os seguintes itens antes de redefinir o seu gateway:

* Os endereços IP de Internet (VIPs) do VPN Gateway do Azure e do gateway de VPN no local estão corretamente configurados nas políticas de VPN do Azure e no local.
* A chave pré-partilhada tem de ser igual no VPN Gateway do Azure e no gateway no local.
* Se aplicar uma configuração IPsec/IKE específica, tal como encriptação, algoritmos hash e PFS (Perfect Forward Secrecy), confirme se o VPN Gateway do Azure e o gateway no local têm as mesmas configurações.

## <a name="azure-portal"></a><a name="portal"></a>Portal do Azure

Pode redefinir uma porta de entrada VPN do Gestor de Recursos utilizando o portal Azure. Se quiser redefinir uma porta de entrada clássica, consulte os passos PowerShell para o [modelo de implantação Clássico.](#resetclassic)

### <a name="resource-manager-deployment-model"></a>Modelo de implementação Resource Manager

[!INCLUDE [portal steps](../../includes/vpn-gateway-reset-gw-portal-include.md)]

## <a name="powershell"></a><a name="ps"></a>PowerShell

### <a name="resource-manager-deployment-model"></a>Modelo de implementação Resource Manager

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O cmdlet para repor um gateway é **Reset-AzVirtualNetworkGateway**. Antes de fazer um reset, certifique-se de que tem a versão mais recente dos [cmdlets PowerShell Az](https://docs.microsoft.com/powershell/module/az.network). O exemplo a seguir repõe uma porta de rede virtual chamada VNet1GW no grupo de recursos TestRG1:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Resultado:

Quando receber um resultado de retorno, pode presumir que o reset do gateway foi bem sucedido. No entanto, não há nada no resultado da devolução que indique explicitamente que o reset foi bem sucedido. Se quiser olhar atentamente para a história para ver exatamente quando ocorreu o reset do gateway, pode ver essa informação no [portal Azure](https://portal.azure.com). No portal, navegue para **'GatewayName' -> Resource Health**.

### <a name="classic-deployment-model"></a><a name="resetclassic"></a>Modelo de implementação clássica

O cmdlet para repor um gateway é **Reset-AzureVNetGateway**. Os cmdlets Azure PowerShell para gestão de serviços devem ser instalados localmente no seu ambiente de trabalho. Não podes usar a Azure Cloud Shell. Antes de efetuar um reset, certifique-se de que tem a versão mais recente dos [cmdlets PowerShell de Gestão de Serviços (SM).](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps#azure-service-management-cmdlets) Ao utilizar este comando, certifique-se de que utiliza o nome completo da rede virtual. Os VNets clássicos que foram criados usando o portal têm um nome longo que é necessário para o PowerShell. Pode ver o nome longo utilizando 'Get-AzureVNetConfig -ExportToFile C:\Myfoldername\NetworkConfig.xml'.

O exemplo a seguir repõe o portal para uma rede virtual chamada "Group TestRG1 TestVNet1" (que mostra simplesmente "TestVNet1" no portal):

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

## <a name="azure-cli"></a><a name="cli"></a>CLI do Azure

Para reiniciar o gateway, utilize o comando [de reset de gateway de vnet de rede az.](https://docs.microsoft.com/cli/azure/network/vnet-gateway) O exemplo a seguir repõe um gateway de rede virtual chamado VNet5GW no grupo de recursos TestRG5:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Resultado:

Quando receber um resultado de retorno, pode presumir que o reset do gateway foi bem sucedido. No entanto, não há nada no resultado da devolução que indique explicitamente que o reset foi bem sucedido. Se quiser olhar atentamente para a história para ver exatamente quando ocorreu o reset do gateway, pode ver essa informação no [portal Azure](https://portal.azure.com). No portal, navegue para **'GatewayName' -> Resource Health**.
