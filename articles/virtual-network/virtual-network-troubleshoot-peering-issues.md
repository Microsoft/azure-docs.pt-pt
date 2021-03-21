---
title: Resolver problemas de peering de rede virtual
description: Passos para ajudar a resolver a maioria dos problemas de observação de rede virtuais.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-network
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 608f3d72aecb0c6d53325c0d082b1779822e9c80
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200535"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Resolver problemas de peering de rede virtual

Este guia de resolução de problemas fornece medidas para ajudá-lo a resolver a maioria dos problemas [de observação de rede virtuais.](virtual-network-peering-overview.md)

![Diagrama de observação de rede virtual](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>Configurar a rede virtual que espreita entre duas redes virtuais

As redes virtuais estão na mesma subscrição ou em diferentes subscrições?

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>As redes virtuais estão na mesma subscrição

Para configurar a rede virtual que observa as redes virtuais que estão na mesma subscrição, utilize os métodos nos seguintes artigos:

* Se as redes virtuais estiverem na *mesma região,* consulte [Criar um espreitamento](./virtual-network-manage-peering.md#create-a-peering).
* Se as redes virtuais estiverem nas *diferentes regiões,* consulte [a rede Virtual a espreitar.](./virtual-network-peering-overview.md) 

> [!Note]
> A conectividade não funciona sobre a rede virtual global que procura os seguintes recursos: 
>
> * Máquinas virtuais (VMs) por trás do balanceador de carga interna básico (ILB) SKU
> * Cache Redis (usa Basic ILB SKU)
> * Gateway de aplicação v1 (utiliza Basic ILB SKU)
> * Conjuntos de escala de máquina virtual (utiliza Basic ILB SKU)
> * Aglomerados de tecido de serviço Azure (utiliza Basic ILB SKU)
> * SQL Server Always On (utiliza SKU Básico ILB)
> * Azure App Service Environment for PowerApps (utiliza O SKU Básico do ILB)
> * Gestão AZURE API (utiliza O SKU Básico do ILB)
> * Azure Ative Directory Domain Services (Azure AD DS) (utiliza SKU Básico ILB)

Para mais informações, consulte os [requisitos e constrangimentos](./virtual-network-peering-overview.md#requirements-and-constraints) do persimento global.

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>As redes virtuais estão em diferentes subscrições ou inquilinos do Ative Directory

Para configurar a rede virtual que procura redes virtuais em diferentes subscrições ou inquilinos do Ative Directory, consulte [Criar espreitar em diferentes subscrições para o Azure CLI](./create-peering-different-subscriptions.md#cli).

> [!Note]
> Para configurar o espreitamento da rede, tem de ter permissões **de Network Contributor** em ambas as subscrições. Para obter mais informações, consulte [permissões de peering](virtual-network-manage-peering.md#permissions).

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Configure a rede virtual que observa com topologia falada por hub que utiliza recursos no local

![Diagrama de rede virtual olhando com no local falou](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>Para uma ligação site-a-local ou uma ligação ExpressRoute

Siga os passos em: [Configurar o trânsito de gateway VPN para o olhar da rede virtual](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="for-point-to-site-connections"></a>Para ligações ponto a local

1. Siga os passos em: [Configurar o trânsito de gateway VPN para o olhar da rede virtual](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Após o estomamento da rede virtual ser estabelecido ou alterado, descarregue e reinstale o pacote ponto-a-local para que os clientes ponto-a-local obtenham as rotas atualizadas para a rede virtual falada.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>Configurar a rede virtual que espreita com a rede virtual de topologia de hub-spoke

![Diagrama de rede virtual olhando com uma rede virtual falada](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>As redes virtuais estão na mesma região


1. Na rede virtual do hub, configurar um aparelho virtual de rede (NVA).
1. Nas redes virtuais faladas, tenha rotas definidas pelo utilizador com o próximo tipo de lúpulo "aparelho virtual de rede" aplicado.

Para obter mais informações, consulte [o chaining de serviço.](./virtual-network-peering-overview.md#service-chaining)

> [!Note]
> Se necessitar de ajuda para configurar um NVA, [contacte o fornecedor NVA](https://mskb.pkisolutions.com/kb/2984655).

Para obter ajuda na resolução de problemas da configuração e encaminhamento do dispositivo NVA, consulte [os problemas do aparelho virtual da Rede em Azure](./virtual-network-troubleshoot-nva.md).

### <a name="the-virtual-networks-are-in-different-regions"></a>As redes virtuais estão em diferentes regiões

O trânsito sobre o olhar global da rede virtual é agora suportado. A conectividade não funciona sobre a rede virtual global que procura os seguintes recursos:

* VMs protegidas pelo SKU do ILB Básico
* Cache Redis (usa Basic ILB SKU)
* Gateway de aplicações (utiliza Basic ILB SKU)
* Conjuntos de escala (utiliza Basic ILB SKU)
* Clusters do Service Fabric (utilizam o SKU do ILB Básico)
* SQL Server Always On (utiliza SKU Básico ILB)
* Ambiente de Serviço de Aplicações (utiliza SKU Básico do ILB)
* Gestão de API (utiliza o SKU do ILB Básico)
* Azure AD DS (utiliza SKU Básico ILB)

Para saber mais sobre requisitos e restrições globais de espreitar, consulte [a rede Virtual a espreitar.](./virtual-network-peering-overview.md#requirements-and-constraints)

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>Resolver problemas num problema de conectividade entre duas redes virtuais espreitadas

Inscreva-se no [portal Azure](https://portal.azure.com/) com uma conta que tenha as [funções e permissões necessárias.](virtual-network-manage-peering.md#permissions) Selecione a rede virtual, **selecione Peering** e, em seguida, verifique o campo **'Estado'.** Qual é a situação?

### <a name="the-peering-status-is-connected"></a>O estado de observação é "Conectado"

Para resolver este problema:

1. Verifique os fluxos de tráfego da rede:

   Utilize [a resolução](../network-watcher/network-watcher-connectivity-overview.md) de problemas de ligação e o fluxo IP [verifiquem](../network-watcher/network-watcher-ip-flow-verify-overview.md) da fonte VM para o destino VM para determinar se existe um NSG ou UDR que está a causar interferência nos fluxos de tráfego.

   Se estiver a usar uma firewall ou NVA: 
   1. Documente os parâmetros UDR para que possa restaurá-los após este passo estar completo.
   2. Retire o UDR da sub-rede VM de origem ou NIC que aponta para o NVA como o próximo salto. Verifique a conectividade desde a origem VM diretamente para o destino que está contornando a NVA. Se este passo não funcionar, consulte o [resolução de problemas da NVA.](./virtual-network-troubleshoot-nva.md)

2. Faça um rastreio de rede: 
   1. Inicie um rastreio de rede no VM de destino. Para windows, pode utilizar **netsh**. Para Linux, utilize **o TCPDump**.
   2. Executar **TcpPing** ou **PsPing** da fonte para o destino IP.

      Este é um exemplo de um comando **TcpPing:**`tcping64.exe -t <destination VM address> 3389`

   3. Depois de o **TcpPing** estar completo, pare o rastreio da rede no destino.
   4. Se os pacotes chegarem da fonte, não há problema de rede. Examine tanto a firewall VM como a aplicação que ouve nessa porta para localizar o problema de configuração.

   > [!Note]
   > Não é possível ligar-se aos seguintes tipos de recursos sobre o espreguite global da rede virtual (redes virtuais em diferentes regiões):
   >
   > * VMs protegidas pelo SKU do ILB Básico
   > * Cache Redis (usa Basic ILB SKU)
   > * Gateway de aplicações (utiliza Basic ILB SKU)
   > * Conjuntos de escala (utiliza Basic ILB SKU)
   > * Clusters do Service Fabric (utilizam o SKU do ILB Básico)
   > * SQL Server Always On (utiliza SKU Básico ILB)
   > * Ambiente de Serviço de Aplicações (utiliza SKU Básico do ILB)
   > * Gestão de API (utiliza o SKU do ILB Básico)
   > * Azure AD DS (utiliza SKU Básico ILB)

Para mais informações, consulte os [requisitos e constrangimentos](./virtual-network-peering-overview.md#requirements-and-constraints) do persimento global.

### <a name="the-peering-status-is-disconnected"></a>O estado de observação é "Desligado"

Para resolver este problema, elimine o espreitamento de ambas as redes virtuais e, em seguida, reu crie-as.

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>Resolver problemas num problema de conectividade entre uma rede virtual falada por hub e um recurso no local

A sua rede utiliza uma porta de entrada NVA ou VPN de terceiros?

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>A minha rede usa uma porta de entrada NVA ou VPN de terceiros

Para resolver problemas de conectividade que afetam um gateway de NVA ou VPN de terceiros, consulte os seguintes artigos:

* [Resolução de problemas NVA](./virtual-network-troubleshoot-nva.md)
* [Encadeamento de serviços](./virtual-network-peering-overview.md#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>A minha rede não usa uma porta de entrada NVA ou VPN de terceiros

A rede virtual do hub e a rede virtual falada têm uma porta de entrada VPN?

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>Tanto a rede virtual do hub como a rede virtual falada têm uma porta de entrada VPN

Usar um portal remoto não é suportado.

Se a rede virtual falada já tiver um gateway VPN, a opção **Utilização de gateway remoto** não é suportada na rede virtual falada. Isto deve-se a uma limitação de espreitar rede virtual.

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>Tanto a rede virtual do hub como a rede virtual falada não têm uma porta de entrada VPN

Para as ligações site-to-site ou Azure ExpressRoute, verifique as seguintes causas primárias de problemas de conectividade para a rede virtual remota a partir do local:

* Na rede virtual que tem um gateway, verifique se a caixa de verificação **de tráfego reencaminhada permite** ser selecionada.
* Na rede virtual que não tem um gateway, verifique se a caixa **de verificação de gateway remoto** de Utilização está selecionada.
* Que o administrador da rede verifique os seus dispositivos no local para verificar se todos têm o espaço de endereço de rede virtual remoto adicionado.

Para ligações ponto a local:

* Na rede virtual que tem um gateway, verifique se a caixa de verificação **de tráfego reencaminhada permite** ser selecionada.
* Na rede virtual que não tem um gateway, verifique se a caixa **de verificação de gateway remoto** de Utilização está selecionada.
* Faça o download e reinstale o pacote de clientes ponto a local. As rotas de rede virtuais que são recentemente espreitadas não adicionam automaticamente rotas aos clientes ponto a local.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Resolução de problemas um problema de conectividade de rede falado entre redes virtuais faladas na mesma região

Uma rede de hub deve incluir um NVA. Configure os UDRs em raios que têm um conjunto de NVA como o próximo salto, e permitem permitir o **tráfego reencaminhado** na rede virtual do hub.

Para mais informações, consulte [acorrente de Serviço](./virtual-network-peering-overview.md#service-chaining)e discuta estes requisitos com o [fornecedor NVA](https://mskb.pkisolutions.com/kb/2984655) à sua escolha.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Resolução de problemas um problema de conectividade de rede falado entre redes virtuais faladas em diferentes regiões

O trânsito sobre o olhar global da rede virtual é agora suportado. A conectividade não funciona sobre a rede virtual global que procura os seguintes recursos:

* VMs protegidas pelo SKU do ILB Básico
* Cache Redis (usa Basic ILB SKU)
* Gateway de aplicações (utiliza Basic ILB SKU)
* Conjuntos de escala (utiliza Basic ILB SKU)
* Clusters do Service Fabric (utilizam o SKU do ILB Básico)
* SQL Server Always On (utiliza SKU Básico ILB)
* Ambiente de Serviço de Aplicações (utiliza SKU Básico do ILB)
* Gestão de API (utiliza o SKU do ILB Básico)
* Azure AD DS (utiliza SKU Básico ILB)

Para mais informações, consulte os [requisitos e constrangimentos](./virtual-network-peering-overview.md#requirements-and-constraints) do levantamento global e [das Diferentes Topologias VPN.](/archive/blogs/igorpag/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2)

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Resolução de problemas um problema de conectividade de rede falado por hub entre uma aplicação web e a rede virtual falada

Para resolver este problema:

1. Inicie sessão no portal do Azure. 
1. Na aplicação web, selecione **networking** e, em seguida, selecione **VNet Integration**.
1. Verifique se consegue ver a rede virtual remota. Introduza manualmente o espaço de endereço de rede virtual remota **(Sync Network** e **Add Routes).**

Para obter mais informações, veja os seguintes artigos:

* [Integrar a aplicação numa rede virtual do Azure](../app-service/web-sites-integrate-with-vnet.md)
* [Acerca do encaminhamento VPN de Ponto a Site](../vpn-gateway/vpn-gateway-about-point-to-site-routing.md)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>Resolução de problemas uma mensagem de erro de configuração de rede virtual 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>Inquilino atual `<TENANT ID>` não está autorizado a aceder a subscrição ligada

Para resolver este problema, consulte [Criar olhando - Azure CLI](./create-peering-different-subscriptions.md#cli).

### <a name="not-connected"></a>Não ligado

Para resolver este problema, elimine o espreitamento de ambas as redes virtuais e, em seguida, recrie-os.

### <a name="failed-to-peer-a-databricks-virtual-network"></a>Falhou em espreitar uma rede virtual Databricks

Para resolver este problema, configurar a rede virtual que espreita sob **a Azure Databricks**, e, em seguida, especificar a rede virtual alvo utilizando o **ID de recursos**. Para obter mais informações, consulte [a rede virtual Peer a Databricks para uma rede virtual remota.](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-peering#id2)

### <a name="the-remote-virtual-network-lacks-a-gateway"></a>A rede virtual remota carece de uma porta de entrada

Esta questão ocorre quando se observa redes virtuais de diferentes inquilinos e mais tarde quer `Use Remote Gateways` configurar. Uma limitação do portal Azure é que não pode validar a presença de uma porta de entrada de rede virtual na rede virtual de outro inquilino.

Há duas formas de resolver a questão:

 * Elimine os olhos e ative a `Use Remote Gateways` opção quando criar um novo espreitamento.
 * Utilize o PowerShell ou o CLI, em vez do portal Azure, para ativar `Use Remote Gateways` .

## <a name="next-steps"></a>Passos seguintes

* [Troubleshooting connectivity problems between Azure VMs](./virtual-network-troubleshoot-connectivity-problem-between-vms.md) (Resolver problemas de conectividade entre VMs do Azure)
