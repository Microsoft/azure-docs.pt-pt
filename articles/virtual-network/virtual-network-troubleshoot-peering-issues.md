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
ms.openlocfilehash: 0898a65323957cbab4c2ab5278e9970cf0c16a90
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219233"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Resolver problemas de peering de rede virtual

Este guia de solução de problemas fornece etapas para ajudá-lo a resolver a maioria dos problemas de [emparelhamento de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) .

![Diagrama de emparelhamento de rede virtual](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>Configurar o emparelhamento de rede virtual entre duas redes virtuais

As redes virtuais estão na mesma assinatura ou em assinaturas diferentes?

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>As redes virtuais estão na mesma assinatura

Para configurar o emparelhamento de rede virtual para as redes virtuais que estão na mesma assinatura, use os métodos nos seguintes artigos:

* Se as redes virtuais estiverem na *mesma região*, consulte [criar um emparelhamento](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering).
* Se as redes virtuais estiverem em *regiões diferentes*, consulte [emparelhamento de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 

> [!Note]
> A conectividade não funciona em emparelhamento de rede virtual global para os seguintes recursos: 
>
> * VMs (máquinas virtuais) por trás do SKU do ILB (balanceador de carga interno) básico
> * Cache Redis (usa o SKU ILB básico)
> * Gateway de aplicativo (usa o SKU ILB básico)
> * Conjuntos de dimensionamento de máquinas virtuais (usa o SKU ILB básico)
> * Clusters Service Fabric do Azure (usa o SKU básico do ILB)
> * SQL Server Always On (usa o SKU ILB básico)
> * Ambiente do Serviço de Aplicativo do Azure para PowerApps (usa o SKU básico do ILB)
> * Gerenciamento de API do Azure (usa o SKU básico do ILB)
> * Azure Active Directory Domain Services (AD DS do Azure) (usa o SKU básico do ILB)

Para obter mais informações, consulte os [requisitos e as restrições](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) de emparelhamento global.

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>As redes virtuais estão em assinaturas ou locatários de Active Directory diferentes

Para configurar o emparelhamento de rede virtual para redes virtuais em assinaturas ou Active Directory locatários diferentes, consulte [criar emparelhamento em assinaturas diferentes para CLI do Azure](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

> [!Note]
> Para configurar o emparelhamento de rede, você deve ter permissões de **colaborador de rede** em ambas as assinaturas. Para obter mais informações, consulte [permissões de emparelhamento](virtual-network-manage-peering.md#permissions).

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Configurar o emparelhamento de rede virtual com a topologia hub-spoke que usa recursos locais

![Diagrama de emparelhamento de rede virtual com spoke local](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>Para uma conexão site a site ou uma conexão do ExpressRoute

Siga as etapas em: [Configure o tráfego de gateway de VPN para o emparelhamento de rede virtual](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).

### <a name="for-point-to-site-connections"></a>Para conexões ponto a site

1. Siga as etapas em: [Configure o tráfego de gateway de VPN para o emparelhamento de rede virtual](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).
2. Após o emparelhamento de rede virtual ser estabelecido ou alterado, baixe e reinstale o pacote ponto a site para que os clientes ponto a site obtenham as rotas atualizadas para a rede virtual spoke.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>Configurar o emparelhamento de rede virtual com a rede virtual da topologia hub-spoke

![Diagrama de emparelhamento de rede virtual com um spoke de rede virtual](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>As redes virtuais estão na mesma região


1. Na rede virtual do Hub, configure uma solução de virtualização de rede (NVA).
1. Nas redes virtuais do spoke, tenha rotas definidas pelo usuário com o tipo do próximo salto "solução de virtualização de rede" aplicada.

Para obter mais informações, consulte [encadeamento de serviço](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining).

> [!Note]
> Se você precisar de ajuda para configurar um NVA, [entre em contato com o fornecedor do NVA](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

Para obter ajuda com a solução de problemas de configuração e roteamento do dispositivo NVA, consulte [problemas de solução de virtualização de rede no Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

### <a name="the-virtual-networks-are-in-different-regions"></a>As redes virtuais estão em regiões diferentes

Agora há suporte para tráfego em emparelhamento de rede virtual global. A conectividade não funciona em emparelhamento de rede virtual global para os seguintes recursos:

* VMs por trás do SKU ILB básico
* Cache Redis (usa o SKU ILB básico)
* Gateway de aplicativo (usa o SKU ILB básico)
* Conjuntos de dimensionamento (usa o SKU básico do ILB)
* Clusters de Service Fabric (usa o SKU ILB básico)
* SQL Server Always On (usa o SKU ILB básico)
* Ambiente do Serviço de Aplicativo (usa o SKU básico do ILB)
* Gerenciamento de API (usa o SKU básico do ILB)
* AD DS do Azure (usa o SKU básico do ILB)

Para saber mais sobre requisitos de emparelhamento global e restrições, confira [emparelhamento de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints).

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>Solucionar um problema de conectividade entre duas redes virtuais emparelhadas

Entre no [portal do Azure](https://portal.azure.com/) com uma conta que tenha as [funções e permissões](virtual-network-manage-peering.md#permissions)necessárias. Selecione a rede virtual, selecione **emparelhamento**e, em seguida, verifique o campo **status** . Qual é o status?

### <a name="the-peering-status-is-connected"></a>O status de emparelhamento é "conectado"

Para solucionar esse problema:

1. Verifique os fluxos de tráfego de rede:

   Use a [solução de problemas de conexão](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview) e de fluxo de [IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) da VM de origem para a VM de destino para determinar se há um NSG ou UDR que está causando interferência nos fluxos de tráfego.

   Se você estiver usando um firewall ou NVA: 
   1. Documente os parâmetros UDR para que você possa restaurá-los após a conclusão desta etapa.
   2. Remova o UDR da sub-rede VM de origem ou NIC que aponta para o NVA como o próximo salto. Verifique a conectividade da VM de origem diretamente para o destino que está ignorando o NVA. Se essa etapa não funcionar, consulte a [solução de problemas do NVA](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

2. Faça um rastreamento de rede: 
   1. Inicie um rastreamento de rede na VM de destino. Para o Windows, você pode usar o **netsh**. Para o Linux, use **tcpdump**.
   2. Execute **TcpPing** ou **PsPing** da origem para o IP de destino.

      Este é um exemplo de um comando **TcpPing** :`tcping64.exe -t <destination VM address> 3389`

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
   > * SQL Server Always On (usa o SKU ILB básico)
   > * Ambiente do Serviço de Aplicativo (usa o SKU básico do ILB)
   > * Gerenciamento de API (usa o SKU básico do ILB)
   > * AD DS do Azure (usa o SKU básico do ILB)

Para obter mais informações, consulte os [requisitos e as restrições](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) de emparelhamento global.

### <a name="the-peering-status-is-disconnected"></a>O status de emparelhamento é "desconectado"

Para resolver esse problema, exclua o emparelhamento de ambas as redes virtuais e, em seguida, crie-as novamente.

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>Solucionar um problema de conectividade entre uma rede virtual hub-spoke e um recurso local

Sua rede usa um gateway de NVA ou VPN de terceiros?

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>Minha rede usa um gateway de NVA ou VPN de terceiros

Para solucionar problemas de conectividade que afetam um gateway de VPN ou NVA de terceiros, consulte os seguintes artigos:

* [Solução de problemas do NVA](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [Encadeamento de serviço](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>Minha rede não usa um gateway de NVA ou VPN de terceiros

A rede virtual do Hub e a rede virtual do spoke têm um gateway de VPN?

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>A rede virtual do Hub e a rede virtual spoke têm um gateway de VPN

Não há suporte para o uso de um gateway remoto.

Se a rede virtual do spoke já tiver um gateway de VPN, a opção **usar gateway remoto** não terá suporte na rede virtual do spoke. Isso ocorre devido a uma limitação de emparelhamento de rede virtual.

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>A rede virtual do Hub e a rede virtual spoke não têm um gateway de VPN

Para conexões site a site ou do Azure ExpressRoute, verifique as seguintes causas primárias de problemas de conectividade para a rede virtual remota do local:

* Na rede virtual que tem um gateway, verifique se a caixa de seleção **Permitir tráfego encaminhado** está marcada.
* Na rede virtual que não tem um gateway, verifique se a caixa de seleção **usar gateway remoto** está marcada.
* Faça com que o administrador de rede verifique seus dispositivos locais para verificar se todos eles têm o espaço de endereço de rede virtual remoto adicionado.

Para conexões ponto a site:

* Na rede virtual que tem um gateway, verifique se a caixa de seleção **Permitir tráfego encaminhado** está marcada.
* Na rede virtual que não tem um gateway, verifique se a caixa de seleção **usar gateway remoto** está marcada.
* Baixe e reinstale o pacote do cliente ponto a site. As rotas de rede virtual que são emparelhadas recentemente não adicionam automaticamente rotas a clientes de ponto a site.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Solucionar um problema de conectividade de rede hub-spoke entre redes virtuais spoke na mesma região

Uma rede de Hub deve incluir um NVA. Configure o UDRs em spokes que têm um NVA definido como o próximo salto e habilite **permitir o tráfego encaminhado** na rede virtual do Hub.

Para obter mais informações, consulte [encadeamento de serviços](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)e discuta esses requisitos com o [fornecedor de NVA](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) de sua escolha.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Solucionar problemas de conectividade de rede hub-spoke entre redes virtuais spoke em regiões diferentes

Agora há suporte para tráfego em emparelhamento de rede virtual global. A conectividade não funciona em emparelhamento de rede virtual global para os seguintes recursos:

* VMs por trás do SKU ILB básico
* Cache Redis (usa o SKU ILB básico)
* Gateway de aplicativo (usa o SKU ILB básico)
* Conjuntos de dimensionamento (usa o SKU básico do ILB)
* Clusters de Service Fabric (usa o SKU ILB básico)
* SQL Server Always On (usa o SKU ILB básico)
* Ambiente do Serviço de Aplicativo (usa o SKU básico do ILB)
* Gerenciamento de API (usa o SKU básico do ILB)
* AD DS do Azure (usa o SKU básico do ILB)

Para obter mais informações, consulte os [requisitos e as restrições](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) de emparelhamento global e [topologias de VPN diferentes](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/).

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Solucionar um problema de conectividade de rede hub-spoke entre um aplicativo Web e a rede virtual spoke

Para solucionar esse problema:

1. Inicie sessão no Portal do Azure. 
1. No aplicativo Web, selecione **rede**e, em seguida, selecione **integração VNet**.
1. Verifique se você pode ver a rede virtual remota. Insira manualmente o espaço de endereço de rede virtual remota (**rede de sincronização** e **adicionar rotas**).

Para obter mais informações, veja os artigos seguintes:

* [Integrar seu aplicativo a uma rede virtual do Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [Sobre roteamento de VPN ponto a site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>Solucionar problemas de uma mensagem de erro de configuração de emparelhamento de rede virtual 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>O locatário `<TENANT ID>` atual não está autorizado a acessar a assinatura vinculada

Para resolver esse problema, consulte [criar CLI do Azure de emparelhamento](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

### <a name="not-connected"></a>Não ligado

Para resolver esse problema, exclua o emparelhamento de ambas as redes virtuais e, em seguida, recrie-as.

### <a name="failed-to-peer-a-databricks-virtual-network"></a>Falha ao emparelhar uma rede virtual do databricks

Para resolver esse problema, configure o emparelhamento de rede virtual em **Azure Databricks**e, em seguida, especifique a rede virtual de destino usando a **ID do recurso**. Para obter mais informações, consulte [emparelhar uma rede virtual do databricks para uma rede virtual remota](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2).

## <a name="next-steps"></a>Passos seguintes

* [Troubleshooting connectivity problems between Azure VMs](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms) (Resolver problemas de conectividade entre VMs do Azure)
