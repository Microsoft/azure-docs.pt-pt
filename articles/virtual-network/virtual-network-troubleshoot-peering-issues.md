---
title: Resolver problemas de peering de rede virtual
description: Etapas para ajudar a resolver a maioria dos problemas de emparelhamento de rede virtual.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 68d4f55d4a382f59386e72779a5f60cfc2a65338
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091105"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Resolver problemas de peering de rede virtual

Este guia de solução de problemas fornece etapas para ajudá-lo a resolver a maioria dos problemas de [emparelhamento de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) .

![IMAGEM](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="scenario-1-configure-virtual-network-peering-between-two-virtual-networks"></a>Cenário 1: Configurar o emparelhamento de rede virtual entre duas redes virtuais

As redes virtuais estão na mesma assinatura ou em assinaturas diferentes?

### <a name="connection-type-1-the-virtual-networks-are-in-the-same-subscription"></a>Tipo de conexão 1: As redes virtuais estão na mesma assinatura

Para configurar o emparelhamento de rede virtual para as redes virtuais que estão na mesma assinatura, use os métodos fornecidos nos seguintes artigos, conforme apropriado:

* Se as redes virtuais estiverem na **mesma região**, siga as etapas para [criar um emparelhamento para redes virtuais na mesma assinatura](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering).
* Se as redes virtuais estiverem em **regiões diferentes**, siga as etapas para configurar o [emparelhamento de rede virtual global](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).  

> [!Note]
> A conectividade não funcionará sobre o emparelhamento VNet global para os seguintes recursos: 
>
> * VMs por trás do SKU ILB básico
> * Cache Redis (usa o SKU ILB básico)
> * Gateway de aplicativo (usa o SKU ILB básico)
> * Conjuntos de dimensionamento (usa o SKU básico do ILB)
> * Clusters de Service Fabric (usa o SKU ILB básico)
> * SQL Always-on (usa o SKU básico do ILB)
> * Ambientes do serviço de aplicativo (ASE) (usa o SKU básico do ILB)
> * Gerenciamento de API (usa o SKU básico do ILB)
> * Serviço de domínio Azure Active Directory (ADDS) (usa o SKU básico do ILB)

Para obter mais informações, consulte os [requisitos e as restrições](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) de emparelhamento global.

### <a name="connection-type-2-the-virtual-networks-are-in-different-subscriptions-or-ad-tenants"></a>Tipo de conexão 2: As redes virtuais estão em assinaturas ou locatários do AD diferentes

Para configurar o emparelhamento de rede virtual para redes virtuais em assinaturas ou Active Directory locatários diferentes, siga as etapas em [criar emparelhamento em assinaturas diferentes para CLI do Azure](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

> [!Note]
> Para configurar o emparelhamento de rede, você deve ter permissões de **colaborador de rede** em ambas as assinaturas. Para obter mais informações, consulte [permissões de emparelhamento](virtual-network-manage-peering.md#permissions).

## <a name="scenario-2-configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Cenário 2: Configurar o emparelhamento de rede virtual com a topologia hub-spoke que usa recursos locais

![IMAGEM](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="connection-type-1-for-site-to-site-connection-or-expressroute-connection"></a>Tipo de conexão 1: Para conexão site a site ou conexão do ExpressRoute

Siga as etapas em: [Configure o tráfego de gateway de VPN para o emparelhamento de rede virtual](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).

### <a name="connection-type-2-for-point-to-site-connections"></a>Tipo de conexão 2: Para conexões ponto a site

1. Siga as etapas em: [Configure o tráfego de gateway de VPN para o emparelhamento de rede virtual](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).
2. Depois que o emparelhamento de rede virtual é estabelecido ou alterado, o pacote de ponto a site deve ser baixado e instalado novamente para que os clientes de ponto a site obtenham as rotas atualizadas para a rede virtual spoke.

## <a name="scenario-3-configure-virtual-network-peering-with-hub-spoke-topology-for-azure-virtual-network"></a>Cenário 3: Configurar o emparelhamento de rede virtual com a topologia hub-spoke para a rede virtual do Azure

![IMAGEM](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="connection-type-1-the-virtual-networks-are-in-the-same-region"></a>Tipo de conexão 1: As redes virtuais estão na mesma região

Você deve configurar uma NVA (solução de virtualização de rede) na rede virtual do Hub e ter rotas definidas pelo usuário com o próximo salto "solução de virtualização de rede" aplicada nas redes virtuais do spoke. Para obter mais informações, consulte [encadeamento de serviço](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining).

> [!Note]
> Se você precisar de ajuda para configurar um NVA, [entre em contato com o fornecedor do NVA](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

Para obter ajuda com a solução de problemas de configuração e roteamento do dispositivo NVA, consulte [problemas de solução de virtualização de rede no Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

### <a name="connection-type-2-the-virtual-networks-are-in-different-regions"></a>Tipo de conexão 2: As redes virtuais estão em regiões diferentes

Agora há suporte para trânsito em emparelhamento VNet global. A conectividade não funciona em emparelhamento VNet global para os seguintes recursos:

* VMs por trás do SKU ILB básico
* Cache Redis (usa o SKU ILB básico)
* Gateway de aplicativo (usa o SKU ILB básico)
* Conjuntos de dimensionamento (usa o SKU básico do ILB)
* Clusters de Service Fabric (usa o SKU ILB básico)
* SQL Always-on (usa o SKU básico do ILB)
* Ambientes do serviço de aplicativo (ASE) (usa o SKU básico do ILB)
* Gerenciamento de API (usa o SKU básico do ILB)
* Serviço de domínio Azure Active Directory (ADDS) (usa o SKU básico do ILB)

Para saber mais sobre requisitos de emparelhamento global e restrições, confira [emparelhamento de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints).

## <a name="scenario-4-i-have-a-connectivity-issue-between-two-peered-virtual-networks"></a>Cenário 4: Tenho um problema de conectividade entre duas redes virtuais emparelhadas

Entre no [portal do Azure](https://portal.azure.com/) com uma conta que tenha as [funções e permissões](virtual-network-manage-peering.md#permissions)necessárias. Selecione a rede virtual, selecione **emparelhamento**e, em seguida, verifique o campo **status** . Qual é o status?

### <a name="connection-type-1-the-peering-status-shows-connected"></a>Tipo de conexão 1: O status de emparelhamento mostra ' conectado '

Para solucionar o problema, siga estas etapas:

1. Verifique os fluxos de tráfego de rede:

   Use a [solução de problemas de conexão](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview) e de fluxo de [IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) da VM de origem para a VM de destino para determinar se há um NSG ou UDR que está causando interferência nos fluxos de tráfego.

   Se você estiver usando um firewall ou um dispositivo NVA, siga estas etapas: 
   1. Documente os parâmetros UDR para que você possa restaurá-los após a conclusão desta etapa.
   2. Remova o UDR da sub-rede VM de origem ou NIC que aponta para o NVA como o próximo salto. Verifique a conectividade da VM de origem diretamente com o destino que está ignorando o NVA. Se essa etapa funcionar, consulte a [solução de problemas do NVA](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

2. Faça um rastreamento de rede: 
   1. Inicie um rastreamento de rede na VM de destino. Para o Windows, você pode usar o **netsh**. Para o Linux, use **tcpdump**.
   2. Execute **TcpPing** ou **PsPing** da origem para o IP de destino.

   * Este é um exemplo de um comando **TcpPing** :`tcping64.exe -t <destination VM address> 3389`

   3. Depois que o **TcpPing** for concluído, pare o rastreamento de rede no destino.
   4. Se os pacotes chegam da origem, não há nenhum problema de rede. Examine o firewall da VM e o aplicativo que está escutando nessa porta para localizar o problema de configuração.

   > [!Note]
   > Você não pode se conectar aos seguintes tipos de recursos por meio do emparelhamento de rede virtual global (redes virtuais em regiões diferentes):
   >
   > * VMs por trás do SKU ILB básico
   > * Cache Redis (usa o SKU ILB básico)
   > * Gateway de aplicativo (usa o SKU ILB básico)
   > * Conjuntos de dimensionamento (usa o SKU básico do ILB)
   > * Clusters de Service Fabric (usa o SKU ILB básico)
   > * SQL Always-on (usa o SKU básico do ILB)
   > * Ambientes do serviço de aplicativo (ASE) (usa o SKU básico do ILB)
   > * Gerenciamento de API (usa o SKU básico do ILB)
   > * Serviço de domínio Azure Active Directory (ADDS) (usa o SKU básico do ILB)

Para obter mais informações, consulte os [requisitos e as restrições](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) de emparelhamento global.

### <a name="connection-type-2-the-peering-status-shows-disconnected"></a>Tipo de conexão 2: O status de emparelhamento mostra ' desconectado '

Você deve excluir emparelhamentos de ambos os VNets e recriá-los.

## <a name="scenario-5-i-have-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-on-premises-resource"></a>Cenário 5: Tenho um problema de conectividade entre uma rede virtual hub-spoke e um recurso local

Você usa um gateway de NVA ou VPN de terceiros?

### <a name="connection-type-1-my-network-uses-a-third-party-nva-or-vpn-gateway"></a>Tipo de conexão 1: Minha rede usa um gateway de NVA ou VPN de terceiros

Para solucionar problemas de conectividade que afetam um gateway de VPN ou NVA de terceiros, consulte os seguintes artigos:

* [Solução de problemas do NVA](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [Encadeamento de serviço](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="connection-type-2-my-network-does-not-a-third-party-nva-or-vpn-gateway"></a>Tipo de conexão 2: Minha rede não é um gateway de NVA ou VPN de terceiros

As redes virtuais Hub e spoke têm um gateway de VPN?

#### <a name="both-the-hub-and-spoke-virtual-networks-have-a-vpn-gateway"></a>Ambas as redes virtuais Hub e spoke têm um gateway de VPN

Não há suporte para o uso de um gateway remoto.

Devido a uma limitação de emparelhamento VNet, o **uso do gateway remoto** não terá suporte na vnet do spoke se a vnet do spoke já tiver um gateway de VPN.

#### <a name="both-the-hub-and-spoke-virtual-networks-dont-have-a-vpn-gateway"></a>As redes virtuais Hub e spoke não têm um gateway de VPN

Para conexões site a site ou ExpressRoute, verifique essas causas primárias de problemas de conectividade para a rede virtual remota do local.

* Verifique se a caixa de seleção **Permitir tráfego encaminhado** está selecionada na rede virtual que tem um gateway.
* Verifique se a caixa de seleção **usar gateway remoto** está marcada na rede virtual que não tem um gateway.
* Faça com que o administrador de rede verifique seus dispositivos locais para verificar se todos eles têm o espaço de endereço de rede virtual remoto adicionado.

Para conexões ponto a site:

* Verifique se a caixa de seleção **Permitir tráfego encaminhado** está selecionada na rede virtual que tem um gateway.
* Verifique se a caixa de seleção **usar gateway remoto** está marcada na rede virtual que não tem um gateway.
* Baixe e instale o pacote do cliente ponto a site novamente. As rotas de rede virtual que são emparelhadas recentemente não adicionam automaticamente rotas a clientes de ponto a site.

## <a name="scenario-6-i-have-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Cenário 6: Tenho um problema de conectividade de rede hub-spoke entre redes virtuais spoke na mesma região

Você deve ter um NVA em uma rede de Hub, configurar o UDRs em spokes que têm um NVA definido como o próximo salto e habilitar **permitir o tráfego encaminhado** na rede virtual do Hub.

Para obter mais informações, consulte [encadeamento de serviços](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)e discuta esses requisitos com o [fornecedor de NVA](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) de sua escolha.

## <a name="scenario-7-i-have-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Cenário 7: Tenho um problema de conectividade de rede hub-spoke entre redes virtuais spoke em regiões diferentes

Agora há suporte para trânsito em emparelhamento VNet global. A conectividade não funcionará sobre o emparelhamento VNet global para os seguintes recursos:

* VMs por trás do SKU ILB básico
* Cache Redis (usa o SKU ILB básico)
* Gateway de aplicativo (usa o SKU ILB básico)
* Conjuntos de dimensionamento (usa o SKU básico do ILB)
* Clusters de Service Fabric (usa o SKU ILB básico)
* SQL Always-on (usa o SKU básico do ILB)
* Ambientes do serviço de aplicativo (ASE) (usa o SKU básico do ILB)
* Gerenciamento de API (usa o SKU básico do ILB)
* Serviço de domínio Azure Active Directory (ADDS) (usa o SKU básico do ILB)

Para obter mais informações, consulte [requisitos e restrições](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) de emparelhamento global e [topologias de VPN diferentes](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/).

## <a name="scenario-8-i-have-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Cenário 8: Tenho um problema de conectividade de rede hub-spoke entre um aplicativo Web e a rede virtual spoke

Para solucionar esse problema, siga estas etapas:

1. Entre no portal do Azure. Vá para o aplicativo Web, selecione **rede**e, em seguida, selecione **integração VNet**.
2. Verifique se você pode ver a rede virtual remota. Insira manualmente o espaço de endereço de rede virtual remota (**rede de sincronização** e **adicionar rotas**).

Para obter mais informações, veja os artigos seguintes:

* [Integrar seu aplicativo a uma rede virtual do Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [Sobre roteamento de VPN ponto a site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="scenario-9-i-receive-an-error-when-configuring-virtual-network-peering"></a>Cenário 9: Recebo um erro ao configurar o emparelhamento de rede virtual

### <a name="error-1-current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>Erro 1: O locatário `<TENANT ID>` atual não está autorizado a acessar a assinatura vinculada

Para resolver esse problema, siga as etapas em [criar emparelhamento-CLI do Azure](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

### <a name="error-2-not-connected"></a>Erro 2: Não Ligado

Você deve excluir emparelhamentos de ambos os VNets e recriá-los.

### <a name="error-3-failed-to-peer-a-databricks-virtual-network"></a>Erro 3: Falha ao emparelhar uma rede virtual do databricks

Para resolver esse problema, configure o emparelhamento de rede virtual na folha **Azure Databricks** e, em seguida, especifique a rede virtual de destino usando a **ID do recurso**. Para obter mais informações, consulte [emparelhar uma rede virtual do databricks para uma rede virtual remota](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2).

## <a name="next-steps"></a>Passos Seguintes

* [Troubleshooting connectivity problems between Azure VMs](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms) (Resolver problemas de conectividade entre VMs do Azure)