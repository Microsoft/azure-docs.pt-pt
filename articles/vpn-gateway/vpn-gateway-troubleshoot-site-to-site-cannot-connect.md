---
title: Resolver problemas de uma ligação de VPN de site a site do Azure que não é possível ligar | Documentos da Microsoft
description: Saiba como resolver problemas de uma ligação de VPN de site a site que, de repente, deixa de funcionar e não pode ser novamente.
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 01729971169011002fa4231f043f82f105f81cdc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60458183"
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Resolução de problemas: Uma ligação de VPN de site a site do Azure não é possível ligar e deixa de funcionar

Depois de configurar uma ligação de VPN de site a site entre uma rede no local e uma rede virtual do Azure, a ligação VPN, de repente, deixa de funcionar e não pode ser novamente. Este artigo fornece passos de resolução de problemas para o ajudar a resolver este problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

Para resolver o problema, primeiro tente [repor o gateway de VPN do Azure](vpn-gateway-resetgw-classic.md) e repor o túnel do dispositivo VPN no local. Se o problema persistir, siga estes passos para identificar a causa do problema.

### <a name="prerequisite-step"></a>Passo dos pré-requisitos

Verifique o tipo do gateway de VPN do Azure.

1. Aceda ao [Portal do Azure](https://portal.azure.com).

2. Verifique os **descrição geral** página do gateway VPN para obter as informações de tipo.
    
    ![Descrição geral do gateway](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Passo 1. Verifique se o dispositivo VPN no local é validado

1. Verifique se estão a utilizar um [validado o dispositivo VPN e versão do sistema operativo](vpn-gateway-about-vpn-devices.md#devicetable). Se o dispositivo não for um dispositivo VPN validado, poderá ter de contactar o fabricante do dispositivo para ver se existe um problema de compatibilidade.

2. Certifique-se de que o dispositivo VPN está configurado corretamente. Para obter mais informações, consulte [editar os exemplos de configuração de dispositivo](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-key"></a>Passo 2. Certifique-se a chave partilhada

Compare a chave partilhada para o dispositivo VPN no local para a VPN de rede Virtual do Azure para se certificar de que as chaves correspondem. 

Para ver a chave partilhada para a ligação de VPN do Azure, utilize um dos seguintes métodos:

**Portal do Azure**

1. Vá para a ligação de site a site de gateway VPN que criou.

2. Na **configurações** secção, clique em **chave partilhada**.
    
    ![Chave partilhada](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para o modelo de implementação Azure Resource Manager:

    Get-AzVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>

Para o modelo de implementação clássica:

    Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName

### <a name="step-3-verify-the-vpn-peer-ips"></a>Passo 3. Verifique se o elemento de rede VPN IPs

-   A definição de IP na **Gateway de rede Local** objeto no Azure deve corresponder o IP do dispositivo no local.
-   A definição de IP do gateway do Azure que é definida no dispositivo no local deve corresponder o IP de gateway do Azure.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>Passo 4. Verifique o UDR e NSGs na sub-rede de gateway

Verificar e remover o encaminhamento definido pelo utilizador (UDR) ou grupos de segurança de rede (NSGs) na sub-rede de gateway e, em seguida, o resultado do teste. Se o problema for resolvido, Valide as definições que o UDR ou o NSG aplicado.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>Passo 5. Verifique o endereço de interface externa do dispositivo VPN no local

- Se o endereço IP de acesso à Internet do dispositivo VPN está incluído nos **rede Local** definição no Azure, poderão ocorrer interrupções de ligação esporádicos.
- Interface externa do dispositivo tem de ser diretamente na Internet. Não deve haver tradução de endereços de rede ou firewall entre a Internet e o dispositivo.
- Para configurar a firewall do clustering de ter um IP virtual, tem de interromper o cluster e expor a aplicação de VPN diretamente a uma interface pública que o gateway pode interagir com o.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>Passo 6. Certifique-se de que as sub-redes de corresponder exatamente (gateways de baseado na política do Azure)

-   Certifique-se de que os espaços de endereços de rede virtual correspondem exatamente entre as definições de no local e a rede virtual do Azure.
-   Verifique se as sub-redes correspondem exatamente entre o **Gateway de rede Local** e definições para a rede no local no local.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>Passo 7. Certifique-se a sonda de estado de funcionamento do gateway do Azure

1. Sonda de estado de funcionamento abertos ao navegar para o seguinte URL:

    `https://<YourVirtualNetworkGatewayIP>:8081/healthprobe`

2. Clique no aviso de certificado.
3. Se receber uma resposta, o gateway de VPN é considerado em bom estado. Se não receber uma resposta, o gateway não pode ser bom estado de funcionamento ou um NSG da sub-rede de gateway está a causar o problema. O texto seguinte é uma resposta de exemplo:

    &lt;? versão xml = "1.0"? > <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">instância principal: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6 < / a cadeia de caracteres&gt;

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>Passo 8. Verifique se o dispositivo VPN no local tem a funcionalidade de secrecy encaminhamento perfeito ativada

A funcionalidade de secrecy encaminhamento perfeito pode causar problemas de Desativação. Se o dispositivo VPN tem perfeito secrecy encaminhamento ativado, desative a funcionalidade. Em seguida, atualize o política de IPsec do gateway de VPN.

## <a name="next-steps"></a>Passos Seguintes

-   [Configurar uma ligação site a site a uma rede virtual](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [Configurar uma política de IPsec/IKE para ligações de VPN de site a site](vpn-gateway-ipsecikepolicy-rm-powershell.md)
