---
title: 'Resolução de problemas uma ligação VPN local-to-site Azure que não pode ligar o títuloSs: Azure VPN Gateway'
description: Saiba como resolver problemas numa ligação VPN local que de repente deixa de funcionar e não pode ser reconectada.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/22/2021
ms.author: genli
ms.openlocfilehash: cebb05b35379573fc9797e89dee3c0c2bf3de6e2
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867292"
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Resolução de problemas: Uma ligação VPN local-to-site Azure não pode ligar-se e parar de funcionar

Depois de configurar uma ligação VPN site-to-site entre uma rede no local e uma rede virtual Azure, a ligação VPN para subitamente de funcionar e não pode ser reconectada. Este artigo fornece medidas de resolução de problemas para ajudá-lo a resolver este problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

Para resolver o problema, tente primeiro [redefinir o gateway Azure VPN](./reset-gateway.md) e redefinir o túnel a partir do dispositivo VPN no local. Se o problema persistir, siga estes passos para identificar a causa do problema.

### <a name="prerequisite-step"></a>Passo pré-requisito

Verifique o tipo de gateway Azure VPN.

1. Aceda ao [Portal do Azure](https://portal.azure.com).

2. Consulte a página **geral** do gateway VPN para obter informações sobre o tipo.
    
    ![Visão geral do portal](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Passo 1. Verifique se o dispositivo VPN no local é validado

1. Verifique se está a utilizar um [dispositivo VPN validado e uma versão do sistema operativo.](vpn-gateway-about-vpn-devices.md#devicetable) Se o dispositivo não for um dispositivo VPN validado, poderá ter de contactar o fabricante do dispositivo para ver se existe um problema de compatibilidade.

2. Certifique-se de que o dispositivo VPN está corretamente configurado. Para obter mais informações, consulte [as amostras de configuração do dispositivo de Edição](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-key"></a>Passo 2. Verifique a chave partilhada

Compare a chave partilhada para o dispositivo VPN no local com a VPN da Rede Virtual Azure para garantir que as teclas coincidem. 

Para visualizar a chave partilhada para a ligação Azure VPN, utilize um dos seguintes métodos:

**Portal do Azure**

1. Aceda à ligação local-local da gateway VPN que criou.

2. Na secção **Definições,** clique na **tecla Compartilhada**.
    
    ![Chave partilhada](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para o modelo de implementação do Gestor de Recursos Azure:

```azurepowershell
Get-AzVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>
```

Para o modelo de implementação clássico:

```azurepowershell
Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName
```

### <a name="step-3-verify-the-vpn-peer-ips"></a>Passo 3. Verifique os IPs de pares VPN

-   A definição IP no objeto **Local Network Gateway** em Azure deve coincidir com o dispositivo IP no local.
-   A definição IP de gateway Azure que está definida no dispositivo no local deve coincidir com o IP de gateway Azure.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>Passo 4: Verifique UDR e NSGs na sub-rede gateway

Verifique e remova o encaminhamento definido pelo utilizador (UDR) ou Grupos de Segurança de Rede (NSGs) na sub-rede gateway e, em seguida, teste o resultado. Se o problema for resolvido, valide as definições que a UDR ou a NSG aplicaram.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>Passo 5. Verifique o endereço de interface externa do dispositivo VPN no local

Se o endereço IP virado para a Internet do dispositivo VPN estiver incluído na definição de **rede local** em Azure, poderá experimentar desconexões esporádicas.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>Passo 6. Verifique se as sub-redes correspondem exatamente (gateways baseados em políticas Azure)

-   Verifique se o espaço de endereço de rede virtual corresponde exatamente entre a rede virtual Azure e as definições no local.
-   Verifique se as sub-redes correspondem exatamente entre o **Gateway de Rede Local** e as definições no local para a rede no local.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>Passo 7. Verifique a sonda de saúde Azure Gateway

1. Abrir sonda de saúde navegando para o seguinte URL:

    `https://<YourVirtualNetworkGatewayIP>:8081/healthprobe`

2. Clique no aviso de certificado.
3. Se receber uma resposta, o gateway VPN é considerado saudável. Se não receber uma resposta, o gateway pode não ser saudável ou um NSG na sub-rede de gateway está a causar o problema. O seguinte texto é uma resposta de amostra:

    ```xml
    <?xml version="1.0"?>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Primary Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6</string>
    ```

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>Passo 8. Verifique se o dispositivo VPN no local tem a funcionalidade de sigilo frontal perfeita ativada

A funcionalidade de sigilo para a frente perfeita pode causar problemas de desconexão. Se o dispositivo VPN tiver um sigilo avançado perfeito ativado, desative a funcionalidade. Em seguida, atualize a política IPsec de gateway VPN.

## <a name="next-steps"></a>Passos seguintes

-   [Configure uma ligação site-a-local a uma rede virtual](./tutorial-site-to-site-portal.md)
-   [Configure uma política IPsec/IKE para ligações VPN site-to-site](vpn-gateway-ipsecikepolicy-rm-powershell.md)