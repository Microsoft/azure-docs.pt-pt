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
ms.openlocfilehash: 662619e101b45d1dd8b34ea97e31f214b254124a
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521873"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Resolver problemas de peering de rede virtual

Este guia de resolução de problemas fornece passos para ajudá-lo a resolver a maioria dos problemas de observação de [rede virtuais.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)

![Diagrama de peering de rede virtual](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>Configure o peering da rede virtual entre duas redes virtuais

As redes virtuais estão na mesma subscrição ou em assinaturas diferentes?

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>As redes virtuais estão na mesma subscrição

Para configurar o olhar de rede virtual para as redes virtuais que se encontram na mesma subscrição, utilize os métodos nos seguintes artigos:

* Se as redes virtuais estiverem na *mesma região,* consulte [Criar um olhar.](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering)
* Se as redes virtuais estiverem nas *diferentes regiões,* consulte o peering da [rede virtual.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 

> [!Note]
> A conectividade não funciona sobre a rede virtual global que procura os seguintes recursos: 
>
> * Máquinas virtuais (VMs) por trás do Equilíbrio de Carga Interna Básica (ILB) SKU
> * Cache redis (usa Basic ILB SKU)
> * Gateway de aplicação (utiliza Basic ILB SKU)
> * Conjuntos de escala de máquina virtual (utiliza SKU Básico ILB)
> * Clusters de tecido de serviço Azure (utiliza Basic ILB SKU)
> * SQL Server Always On (usa Basic ILB SKU)
> * Ambiente de serviço de aplicações Azure para PowerApps (usa Basic ILB SKU)
> * Gestão aPI Azure (utiliza Basic ILB SKU)
> * Serviços de Domínio de Diretório Ativo Azure (Azure AD DS) (utiliza Basic ILB SKU)

Para obter mais informações, consulte os [requisitos e constrangimentos](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) do peering global.

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>As redes virtuais estão em diferentes subscrições ou inquilinos do Diretório Ativo

Para configurar o peering de rede virtual para redes virtuais em diferentes subscrições ou inquilinos de Diretório Ativo, consulte [Create peering em diferentes subscrições para o Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

> [!Note]
> Para configurar o peering da rede, deve ter permissões do Colaborador da **Rede** em ambas as subscrições. Para mais informações, consulte [permissões de peering](virtual-network-manage-peering.md#permissions).

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Configure a rede virtual olhando com topologia de centro-falado que usa recursos no local

![Diagrama de rede virtual olhando com no local falado](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>Para uma ligação site-to-site ou uma ligação ExpressRoute

Siga os passos em: Configure o trânsito de [gateway VPN para o peering virtual da rede](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).

### <a name="for-point-to-site-connections"></a>Para ligações ponto-a-local

1. Siga os passos em: Configure o trânsito de [gateway VPN para o peering virtual da rede](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).
2. Depois de o peering de rede virtual ser estabelecido ou alterado, descarregue e reinstale o pacote ponto-a-site para que os clientes ponto-a-site obtem as rotas atualizadas para a rede virtual falada.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>Configure o peering da rede virtual com a rede virtual de topologia de centro-falado

![Diagrama de rede virtual olhando com uma rede virtual falada](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>As redes virtuais estão na mesma região


1. Na rede virtual hub, configure um aparelho virtual de rede (NVA).
1. Nas redes virtuais faladas, têm rotas definidas pelo utilizador com o próximo tipo de lúpulo "aparelho virtual de rede" aplicado.

Para mais informações, consulte [a correnção de serviço](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining).

> [!Note]
> Se necessitar de ajuda para configurar um NVA, [contacte o fornecedor NVA](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

Para obter ajuda na resolução de problemas da configuração e encaminhamento do dispositivo NVA, consulte problemas de [aparelhos virtuais da Rede em Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

### <a name="the-virtual-networks-are-in-different-regions"></a>As redes virtuais estão em diferentes regiões

O trânsito sobre o epeering global da rede virtual é agora apoiado. A conectividade não funciona sobre a rede virtual global que procura os seguintes recursos:

* VMs por trás do Basic ILB SKU
* Cache redis (usa Basic ILB SKU)
* Gateway de aplicação (utiliza Basic ILB SKU)
* Conjuntos de escala (utiliza SKU Básico ILB)
* Clusters de tecido de serviço (utiliza Basic ILB SKU)
* SQL Server Always On (usa Basic ILB SKU)
* Ambiente de Serviço de Aplicativos (usa Basic ILB SKU)
* Gestão API (utiliza Basic ILB SKU)
* Azure AD DS (usa Basic ILB SKU)

Para saber mais sobre os requisitos e restrições globais de peering, consulte o [peering da rede virtual.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>Problemas de resolução de um problema de conectividade entre duas redes virtuais peered

Inscreva-se no [portal Azure](https://portal.azure.com/) com uma conta que tenha as [funções e permissões necessárias.](virtual-network-manage-peering.md#permissions) Selecione a rede virtual, selecione **Peering**e, em seguida, verifique o campo **'Status'.** Qual é o estado?

### <a name="the-peering-status-is-connected"></a>O estado de observação é "Conectado"

Para resolver esta questão:

1. Verifique os fluxos de tráfego da rede:

   Utilize a [ligação Troubleshoot](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview) e o [fluxo IP verificar](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) da fonte VM para o destino VM para determinar se existe um NSG ou UDR que está a causar interferência nos fluxos de tráfego.

   Se estiver a usar uma firewall ou NVA: 
   1. Documente os parâmetros UDR para que possa restaurá-los depois de este passo estar completo.
   2. Retire o UDR da subnet VM de origem ou NIC que aponta para o NVA como o próximo salto. Verifique a conectividade da fonte VM diretamente para o destino que está a contornar a NVA. Se este passo não funcionar, consulte o resoluto de [problemas da NVA.](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)

2. Pegue um rastreio de rede: 
   1. Inicie um rastreio de rede no destino VM. Para windows, pode utilizar **Netsh**. Para o Linux, utilize **o TCPDump**.
   2. Executar **TcpPing** ou **PsPing** da fonte para o IP de destino.

      Este é um exemplo de um comando **TcpPing:**`tcping64.exe -t <destination VM address> 3389`

   3. Depois de o **TcpPing** estar completo, pare o rastreio da rede no destino.
   4. Se os pacotes chegarem da fonte, não há problema de networking. Examine tanto a firewall VM como a aplicação que escuta nessa porta para localizar o problema de configuração.

   > [!Note]
   > Não é possível ligar-se aos seguintes tipos de recursos sobre o peering global da rede virtual (redes virtuais em diferentes regiões):
   >
   > * VMs por trás do Basic ILB SKU
   > * Cache redis (usa Basic ILB SKU)
   > * Gateway de aplicação (utiliza Basic ILB SKU)
   > * Conjuntos de escala (utiliza SKU Básico ILB)
   > * Clusters de tecido de serviço (utiliza Basic ILB SKU)
   > * SQL Server Always On (usa Basic ILB SKU)
   > * Ambiente de Serviço de Aplicativos (usa Basic ILB SKU)
   > * Gestão API (utiliza Basic ILB SKU)
   > * Azure AD DS (usa Basic ILB SKU)

Para obter mais informações, consulte os [requisitos e constrangimentos](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) do peering global.

### <a name="the-peering-status-is-disconnected"></a>O estado de observação é "Desligado"

Para resolver este problema, elimine o epeering de ambas as redes virtuais e, em seguida, recriá-los.

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>Problemas de resolução de um problema de conectividade entre uma rede virtual de hub-spoke e um recurso no local

A sua rede utiliza um gateway NVA ou VPN de terceiros?

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>A minha rede usa um gateway NVA ou VPN de terceiros

Para resolver problemas de conectividade que afetam um portal de porta de entrada de NVA ou VPN de terceiros, consulte os seguintes artigos:

* [Atirador de problemas nVA](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [Cadeia de serviços](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>A minha rede não utiliza um gateway NVA ou VPN de terceiros

A rede virtual hub e a rede virtual falada têm um gateway VPN?

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>Tanto a rede virtual hub como a rede virtual falada têm um gateway VPN

Usar um portal remoto não é suportado.

Se a rede virtual falada já tiver um gateway VPN, a opção **use remote gateway** não é suportada na rede virtual falada. Isto deve-se a uma limitação virtual de observação de redes.

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>Tanto a rede virtual hub como a rede virtual falada não têm um gateway VPN

Para as ligações site-to-site ou Azure ExpressRoute, verifique as seguintes causas primárias de problemas de conectividade para a rede virtual remota a partir do local:

* Na rede virtual que tem um portal, verifique se a caixa de verificação de **tráfego reencaminhada permitir** é selecionada.
* Na rede virtual que não tem um portal, verifique se a caixa de verificação de **gateway remoto Use** é selecionada.
* Mande o seu administrador de rede verificar os seus dispositivos no local para verificar se todos têm o espaço de endereço de rede virtual remoto adicionado.

Para ligações ponto-a-local:

* Na rede virtual que tem um portal, verifique se a caixa de verificação de **tráfego reencaminhada permitir** é selecionada.
* Na rede virtual que não tem um portal, verifique se a caixa de verificação de **gateway remoto Use** é selecionada.
* Faça o download e reinstalação do pacote de clientes ponto-a-site. As rotas de rede virtuais que são recentemente espreitadas não adicionam automaticamente rotas aos clientes ponto-a-site.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Problemas de resolução de um problema de conectividade de rede de hub-spoke entre redes virtuais faladas na mesma região

Uma rede de hub deve incluir um NVA. Configure os UDRs em raios que tenham um conjunto NVA como o próximo salto, e permitam permitir o **tráfego encaminhada** na rede virtual do hub.

Para mais informações, consulte a corrente de [serviço](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining), e discuta estes requisitos com o [fornecedor nVA](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) à sua escolha.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Problemas de resolução de um problema de conectividade de rede de centro-falado entre redes virtuais faladas em diferentes regiões

O trânsito sobre o epeering global da rede virtual é agora apoiado. A conectividade não funciona sobre a rede virtual global que procura os seguintes recursos:

* VMs por trás do Basic ILB SKU
* Cache redis (usa Basic ILB SKU)
* Gateway de aplicação (utiliza Basic ILB SKU)
* Conjuntos de escala (utiliza SKU Básico ILB)
* Clusters de tecido de serviço (utiliza Basic ILB SKU)
* SQL Server Always On (usa Basic ILB SKU)
* Ambiente de Serviço de Aplicativos (usa Basic ILB SKU)
* Gestão API (utiliza Basic ILB SKU)
* Azure AD DS (usa Basic ILB SKU)

Para mais informações, consulte os [requisitos e constrangimentos](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) do peering global e [diferentes topologias VPN](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/).

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Problemas de resolução de um problema de conectividade de rede de hub-spoke entre uma aplicação web e a rede virtual falada

Para resolver esta questão:

1. Inicie sessão no Portal do Azure. 
1. Na aplicação web, selecione **networking**, e, em seguida, selecione **VNet Integration**.
1. Verifique se consegue ver a rede virtual remota. Introduza manualmente o espaço de endereços de rede virtual remota **(Sync Network** e **Add Routes).**

Para obter mais informações, veja os artigos seguintes:

* [Integre a sua app com uma rede virtual Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [Acerca do encaminhamento VPN de Ponto a Site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>Resolução de problemas uma mensagem de erro de configuração de rede virtual 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>O `<TENANT ID>` inquilino atual não está autorizado a aceder à subscrição ligada

Para resolver esta questão, consulte [Create peering - Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

### <a name="not-connected"></a>Não ligado

Para resolver este problema, elimine o epeering de ambas as redes virtuais e, em seguida, recriá-los.

### <a name="failed-to-peer-a-databricks-virtual-network"></a>Falhou em espiar uma rede virtual databricks

Para resolver este problema, configure a rede virtual de observação sob **os Tijolos de Dados Do Azure**e, em seguida, especifique a rede virtual alvo utilizando o ID de **recurso**. Para obter mais informações, consulte a [rede virtual Peer a Databricks para uma rede virtual remota](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2).

### <a name="the-remote-virtual-network-lacks-a-gateway"></a>A rede virtual remota carece de um portal

Este problema ocorre quando você observa redes virtuais de diferentes inquilinos e mais tarde quer configurar `Use Remote Gateways`. Uma limitação do portal Azure é que não pode validar a presença de uma rede virtual de gateway na rede virtual de outro inquilino.

Há duas formas de resolver a questão:

 * Elimine os pares e `Use Remote Gateways` ative a opção quando criar um novo epeering.
 * Utilize powerShell ou CLI, em vez do `Use Remote Gateways`portal Azure, para ativar .

## <a name="next-steps"></a>Passos seguintes

* [Troubleshooting connectivity problems between Azure VMs](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms) (Resolver problemas de conectividade entre VMs do Azure)
