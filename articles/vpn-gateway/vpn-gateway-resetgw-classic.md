---
title: Redefinir um gateway de VPN do Azure para restabelecer o túnel IPsec
description: Este artigo orienta você pela redefinição do gateway de VPN do Azure para restabelecer túneis IPsec. O artigo se aplica aos gateways de VPN nos modelos de implantação clássico e do Gerenciador de recursos.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/05/2019
ms.author: cherylmc
ms.openlocfilehash: 6fd4bdf15ea5693a50403f3c31f72b920871f04f
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779812"
---
# <a name="reset-a-vpn-gateway"></a>Repor um Gateway de VPN

Repor o gateway de VPN do Azure é útil se perder a conectividade VPN em vários locais num ou mais túneis de rede de VPNs. Nesta situação, os dispositivos VPN no local estão todos a funcionar corretamente, mas não podem estabelecer túneis IPsec com os gateways de VPN do Azure. Este artigo ajuda você a redefinir o gateway de VPN.

### <a name="what-happens-during-a-reset"></a>O que acontece durante uma redefinição?

Um gateway de VPN é composto de duas instâncias de VM em execução em uma configuração de espera ativa. Quando você redefine o gateway, ele reinicia o gateway e, em seguida, reaplica as configurações entre locais a ele. O gateway mantém o endereço IP público que já tem, ou seja, não terá de atualizar a configuração do router da VPN com um novo endereço IP público para o VPN Gateway do Azure.

Quando você emite o comando para redefinir o gateway, a instância ativa atual do gateway de VPN do Azure é reinicializada imediatamente. Haverá uma breve lacuna durante o failover da instância ativa (sendo reinicializada) para a instância em espera. O intervalo deve ser inferior a um minuto.

Se a ligação não for restaurada após o primeiro reinício, execute o mesmo comando novamente para reiniciar a segunda instância da VM (o novo gateway ativo). Se forem pedidos dois reinícios seguidos, haverá um período ligeiramente superior em que ambas as instâncias da VM (ativa e no modo de espera) estão a ser reiniciadas. Isso causará uma lacuna mais longa na conectividade de VPN, até 30 a 45 minutos para que as VMs concluam as reinicializações.

Após duas reinicializações, se você ainda estiver tendo problemas de conectividade entre locais, abra uma solicitação de suporte do portal do Azure.

## <a name="before"></a>Antes de começar

Antes de repor o gateway, verifique os itens principais listados abaixo para cada túnel de rede de VPNs (S2S) do IPsec. Qualquer erro de correspondência nos itens desligará os túneis VPN S2S. Verificar e corrigir as configurações para seus gateways de VPN locais e do Azure o poupa de reinicializações desnecessárias e de interrupções para as outras conexões de trabalho nos gateways.

Verifique os seguintes itens antes de redefinir o gateway:

* Os endereços IP de Internet (VIPs) do VPN Gateway do Azure e do gateway de VPN no local estão corretamente configurados nas políticas de VPN do Azure e no local.
* A chave pré-partilhada tem de ser igual no VPN Gateway do Azure e no gateway no local.
* Se aplicar uma configuração IPsec/IKE específica, tal como encriptação, algoritmos hash e PFS (Perfect Forward Secrecy), confirme se o VPN Gateway do Azure e o gateway no local têm as mesmas configurações.

## <a name="portal"></a>Portal do Azure

Você pode redefinir um gateway de VPN do Resource Manager usando o portal do Azure. Se você quiser redefinir um gateway clássico, consulte as etapas do [PowerShell](#resetclassic) .

### <a name="resource-manager-deployment-model"></a>Modelo de implementação Resource Manager

1. Abra o [portal do Azure](https://portal.azure.com) e navegue até o gateway de rede virtual do Gerenciador de recursos que você deseja redefinir.
2. Na folha do gateway de rede virtual, clique em ' Redefinir '.

   ![Folha redefinir gateway de VPN](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)
3. Na folha redefinir, clique no botão **Redefinir** .

## <a name="ps"></a>PowerShell

### <a name="resource-manager-deployment-model"></a>Modelo de implementação Resource Manager

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O cmdlet para redefinir um gateway é **Reset-AzVirtualNetworkGateway**. Antes de executar uma redefinição, verifique se você tem a versão mais recente dos [cmdlets AZ do PowerShell](https://docs.microsoft.com/powershell/module/az.network). O exemplo a seguir redefine um gateway de rede virtual chamado VNet1GW no grupo de recursos TestRG1:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Resultado:

Ao receber um resultado de retorno, você pode assumir que a redefinição do gateway foi bem-sucedida. No entanto, não há nada no resultado de retorno que indica explicitamente que a redefinição foi bem-sucedida. Se você quiser olhar com mais detalhes o histórico para ver exatamente quando ocorreu a redefinição do gateway, poderá exibir essas informações no [portal do Azure](https://portal.azure.com). No portal, navegue até **"GATEWAYNAME"-> Resource Health**.

### <a name="resetclassic"></a>Modelo de implantação clássico

O cmdlet para redefinir um gateway é **Reset-AzureVNetGateway**. Os cmdlets Azure PowerShell para o gerenciamento de serviços devem ser instalados localmente na sua área de trabalho. Você não pode usar Azure Cloud Shell. Antes de executar uma redefinição, verifique se você tem a versão mais recente dos [cmdlets do PowerShell do SM (gerenciamento de serviços)](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets). Ao usar esse comando, verifique se você está usando o nome completo da rede virtual. Os VNets clássicos que foram criados usando o portal têm um nome longo que é necessário para o PowerShell. Você pode exibir o nome longo usando ' Get-AzureVNetConfig-ExportToFile C:\Myfoldername\NetworkConfig.xml '.

O exemplo a seguir redefine o gateway para uma rede virtual chamada "Group TestRG1 TestVNet1" (que mostra simplesmente "TestVNet1" no Portal):

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

## <a name="cli"></a>CLI do Azure

Para redefinir o gateway, use o comando [AZ Network vnet-gateway Reset](https://docs.microsoft.com/cli/azure/network/vnet-gateway) . O exemplo a seguir redefine um gateway de rede virtual chamado VNet5GW no grupo de recursos TestRG5:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Resultado:

Ao receber um resultado de retorno, você pode assumir que a redefinição do gateway foi bem-sucedida. No entanto, não há nada no resultado de retorno que indica explicitamente que a redefinição foi bem-sucedida. Se você quiser olhar com mais detalhes o histórico para ver exatamente quando ocorreu a redefinição do gateway, poderá exibir essas informações no [portal do Azure](https://portal.azure.com). No portal, navegue até **"GATEWAYNAME"-> Resource Health**.
