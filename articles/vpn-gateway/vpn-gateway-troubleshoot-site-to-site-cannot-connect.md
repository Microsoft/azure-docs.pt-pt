---
title: 'Solucionar problemas de uma conexão VPN site a site do Azure que não pode conectar titleSuffix: gateway de VPN do Azure'
description: Saiba como solucionar problemas de uma conexão VPN site a site que repentinamente pára de funcionar e não pode ser reconectada.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: 4e827c5f6eedc819bc3635cb09a28f65df51312c
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75862582"
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Solução de problemas: uma conexão VPN site a site do Azure não pode se conectar e parar de funcionar

Depois de configurar uma conexão VPN site a site entre uma rede local e uma rede virtual do Azure, a conexão VPN repentinamente pára de funcionar e não pode ser reconectada. Este artigo fornece etapas de solução de problemas para ajudá-lo a resolver esse problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

Para resolver o problema, primeiro tente [redefinir o gateway de VPN do Azure](vpn-gateway-resetgw-classic.md) e redefina o túnel do dispositivo VPN local. Se o problema persistir, siga estas etapas para identificar a causa do problema.

### <a name="prerequisite-step"></a>Etapa de pré-requisito

Verifique o tipo de gateway de VPN do Azure.

1. Aceda ao [Portal do Azure](https://portal.azure.com).

2. Verifique a página **visão geral** do gateway de VPN para as informações de tipo.
    
    ![Visão geral do gateway](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Passo 1. Verificar se o dispositivo VPN local é validado

1. Verifique se você está usando um [dispositivo VPN validado e uma versão do sistema operacional](vpn-gateway-about-vpn-devices.md#devicetable). Se o dispositivo não for um dispositivo VPN validado, talvez seja necessário entrar em contato com o fabricante do dispositivo para ver se há um problema de compatibilidade.

2. Verifique se o dispositivo VPN está configurado corretamente. Para obter mais informações, consulte [Editar exemplos de configuração de dispositivo](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-key"></a>Passo 2. Verificar a chave compartilhada

Compare a chave compartilhada para o dispositivo VPN local com a VPN de rede virtual do Azure para certificar-se de que as chaves correspondem. 

Para exibir a chave compartilhada para a conexão VPN do Azure, use um dos seguintes métodos:

**Portal do Azure**

1. Vá para a conexão site a site do gateway de VPN que você criou.

2. Na seção **configurações** , clique em **chave compartilhada**.
    
    ![Chave partilhada](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**O Azure PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para o modelo de implantação de Azure Resource Manager:

    Get-AzVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>

Para o modelo de implantação clássico:

    Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName

### <a name="step-3-verify-the-vpn-peer-ips"></a>Passo 3. Verificar os IPs de mesmo nível de VPN

-   A definição de IP no objeto de **Gateway de rede local** no Azure deve corresponder ao IP do dispositivo local.
-   A definição de IP do gateway do Azure que está definida no dispositivo local deve corresponder ao IP do gateway do Azure.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>Passo 4. Verifique UDR e NSGs na sub-rede do gateway

Verifique e remova o roteamento definido pelo usuário (UDR) ou grupos de segurança de rede (NSGs) na sub-rede de gateway e, em seguida, teste o resultado. Se o problema for resolvido, valide as configurações que o UDR ou o NSG aplicou.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>Passo 5. Verificar o endereço da interface externa do dispositivo VPN local

- Se o endereço IP voltado para a Internet do dispositivo VPN estiver incluído na definição de **rede local** no Azure, você poderá experimentar desconexões esporádicas.
- A interface externa do dispositivo deve estar diretamente na Internet. Não deve haver nenhuma conversão de endereços de rede ou firewall entre a Internet e o dispositivo.
- Para configurar o clustering de firewall para ter um IP virtual, você deve interromper o cluster e expor o dispositivo VPN diretamente a uma interface pública com a qual o gateway pode interagir.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>Passo 6. Verificar se as sub-redes correspondem exatamente (gateways baseados em políticas do Azure)

-   Verifique se os espaços de endereço de rede virtual correspondem exatamente entre a rede virtual do Azure e as definições locais.
-   Verifique se as sub-redes correspondem exatamente entre o **Gateway de rede local** e as definições locais para a rede local.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>Passo 7. Verificar a investigação de integridade do gateway do Azure

1. Abra a investigação de integridade navegando até a seguinte URL:

    `https://<YourVirtualNetworkGatewayIP>:8081/healthprobe`

2. Clique no aviso do certificado.
3. Se você receber uma resposta, o gateway de VPN será considerado íntegro. Se você não receber uma resposta, o gateway pode não estar íntegro ou um NSG na sub-rede do gateway está causando o problema. O texto a seguir é uma resposta de exemplo:

    ```xml
    <?xml version="1.0"?>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Primary Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6</string>
    ```

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>Passo 8. Verifique se o dispositivo VPN local tem o recurso de sigilo de encaminhamento perfeito habilitado

O recurso de sigilo de encaminhamento perfeito pode causar problemas de desconexão. Se o dispositivo VPN tiver o sigilo de encaminhamento perfeito habilitado, desabilite o recurso. Em seguida, atualize a política IPsec do gateway de VPN.

## <a name="next-steps"></a>Passos seguintes

-   [Configurar uma conexão site a site com uma rede virtual](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [Configurar uma política de IPsec/IKE para conexões VPN site a site](vpn-gateway-ipsecikepolicy-rm-powershell.md)
