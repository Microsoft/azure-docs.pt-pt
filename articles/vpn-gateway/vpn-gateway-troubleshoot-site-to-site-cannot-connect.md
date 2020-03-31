---
title: 'Troubleshoot uma ligação VPN site-to-site Azure que não pode ligar títuloSuffix: Azure VPN Gateway'
description: Aprenda a resolver problemas com uma ligação VPN site-to-site que de repente para de funcionar e não pode ser reconectada.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: 4e827c5f6eedc819bc3635cb09a28f65df51312c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75862582"
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Resolução de problemas: Uma ligação VPN site-site Azure não pode ligar e deixa de funcionar

Depois de configurar uma ligação VPN site-to-site entre uma rede no local e uma rede virtual Azure, a ligação VPN para subitamente de funcionar e não pode ser reconectada. Este artigo fornece medidas de resolução de problemas para ajudá-lo a resolver este problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

Para resolver o problema, tente primeiro [redefinir o gateway Azure VPN](vpn-gateway-resetgw-classic.md) e repor o túnel a partir do dispositivo VPN no local. Se o problema persistir, siga estes passos para identificar a causa do problema.

### <a name="prerequisite-step"></a>Passo pré-requisito

Verifique o tipo de porta de entrada Azure VPN.

1. Vá ao [portal Azure.](https://portal.azure.com)

2. Consulte a página **de visão geral** do gateway VPN para obter as informações do tipo.
    
    ![Visão geral do portal](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Passo 1. Verifique se o dispositivo VPN no local é validado

1. Verifique se está a utilizar um [dispositivo VPN validado e versão do sistema operativo](vpn-gateway-about-vpn-devices.md#devicetable). Se o dispositivo não for um dispositivo VPN validado, poderá ter de contactar o fabricante do dispositivo para ver se existe um problema de compatibilidade.

2. Certifique-se de que o dispositivo VPN está corretamente configurado. Para mais informações, consulte [as amostras](vpn-gateway-about-vpn-devices.md#editing)de configuração do dispositivo Editar .

### <a name="step-2-verify-the-shared-key"></a>Passo 2. Verifique a chave partilhada

Compare a chave partilhada para o dispositivo VPN no local com a VPN da Rede Virtual Azure para se certificar de que as teclas correspondem. 

Para visualizar a chave partilhada para a ligação VPN Azure, utilize um dos seguintes métodos:

**Portal Azure**

1. Vá à ligação site-site vpn que criou.

2. Na secção **Definições,** clique na **tecla Partilhada**.
    
    ![Chave partilhada](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para o modelo de implementação do Gestor de Recursos Azure:

    Get-AzVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>

Para o modelo clássico de implantação:

    Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName

### <a name="step-3-verify-the-vpn-peer-ips"></a>Passo 3. Verifique os IPs de pares VPN

-   A definição IP no objeto Gateway da **rede local** em Azure deve coincidir com o IP do dispositivo no local.
-   A definição IP de gateway Azure que está definida no dispositivo no local deve corresponder ao IP de gateway Azure.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>Passo 4. Verifique udr e NSGs na subnet gateway

Verifique e remova o encaminhamento definido pelo utilizador (UDR) ou os Grupos de Segurança da Rede (NSGs) na subnet gateway e, em seguida, teste o resultado. Se o problema for resolvido, valide as definições aplicadas pelo UDR ou nsg.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>Passo 5. Verifique o endereço de interface externa do dispositivo VPN no local

- Se o endereço IP virado para a Internet do dispositivo VPN estiver incluído na definição de **rede Local** em Azure, poderá experimentar desconexões esporádicas.
- A interface externa do dispositivo deve estar diretamente na Internet. Não deve haver tradução de endereço de rede ou firewall entre a Internet e o dispositivo.
- Para configurar o agrupamento de firewall para ter um IP virtual, deve quebrar o cluster e expor o aparelho VPN diretamente a uma interface pública com a que o gateway pode interagir.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>Passo 6. Verifique se as subredes correspondem exatamente (gateways baseados em políticas de Azure)

-   Verifique se os espaços de endereço s da rede virtual correspondem exatamente entre a rede virtual Azure e as definições no local.
-   Verifique se as subredes correspondem exatamente entre o Gateway da **Rede Local** e as definições no local para a rede no local.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>Passo 7. Verifique a sonda de saúde Azure Gateway

1. Abrir sonda de saúde navegando para o seguinte URL:

    `https://<YourVirtualNetworkGatewayIP>:8081/healthprobe`

2. Clique no aviso de certificado.
3. Se receber uma resposta, o gateway VPN é considerado saudável. Se não receber uma resposta, o portal pode não ser saudável ou um NSG na subnet de gateway está a causar o problema. O seguinte texto é uma resposta da amostra:

    ```xml
    <?xml version="1.0"?>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Primary Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6</string>
    ```

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>Passo 8. Verifique se o dispositivo VPN no local tem a funcionalidade de sigilo avançado perfeita ativada

A funcionalidade de sigilo para a frente perfeita pode causar problemas de desconexão. Se o dispositivo VPN tiver um perfeito sigilo para a frente, desative a funcionalidade. Em seguida, atualize a política vpn gateway IPsec.

## <a name="next-steps"></a>Passos seguintes

-   [Configure uma ligação site-a-site a uma rede virtual](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [Configure uma política IPsec/IKE para ligações VPN site-to-site](vpn-gateway-ipsecikepolicy-rm-powershell.md)
