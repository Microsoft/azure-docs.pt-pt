---
title: Conceito - Integre uma implementação de Solução VMware Azure num hub e tenha falado arquitetura
description: Conheça as recomendações para a integração de uma implementação de Azure VMware Solution num centro existente ou novo e falou arquitetura sobre a Azure.
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 66c6cc4841b4b36775fda89b29dc588100c3ad87
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92058476"
---
# <a name="integrate-azure-vmware-solution-in-a-hub-and-spoke-architecture"></a>Integre a Solução Azure VMware num hub e falou arquitetura

Neste artigo, fornecemos recomendações para a integração de uma implementação de Azure VMware Solution num hub existente ou num novo [Hub e Spoke arquitetura](/azure/architecture/reference-architectures/hybrid-networking/shared-services) em Azure. 


O cenário Hub e Spoke assumem um ambiente híbrido em nuvem com cargas de trabalho em:

* Azure nativo usando serviços IaaS ou PaaS
* Solução VMware no Azure 
* vSphere no local

## <a name="architecture"></a>Arquitetura

O *Hub* é uma Rede Virtual Azure que funciona como um ponto central de conectividade para as suas instalações e nuvem privada Azure VMware Solution. Os *Spokes* são redes virtuais espreitadas com o Hub para permitir a comunicação de rede transpare virtual.

O tráfego entre o datacenter no local, a nuvem privada Azure VMware Solution e o Hub passa pelas ligações Azure ExpressRoute. As redes virtuais faladas geralmente contêm cargas de trabalho baseadas em IaaS, mas podem ter serviços PaaS como [o App Service Environment](../app-service/environment/intro.md), que tem integração direta com a Rede Virtual, ou outros serviços PaaS com [Azure Private Link](../private-link/index.yml) ativados.

>[!IMPORTANT]
>Pode utilizar um Gateway ExpressRoute existente para ligar à Azure VMware Solution desde que não exceda o limite de quatro circuitos ExpressRoute por rede virtual.  No entanto, para aceder à Azure VMware Solution a partir das instalações através do ExpressRoute, deve ter ExpressRoute Global Reach uma vez que o gateway ExpressRoute não fornece encaminhamento transitório entre os seus circuitos conectados.

O diagrama mostra um exemplo de uma implantação de Hub e Spoke em Azure ligada às instalações e Solução Azure VMware através do ExpressRoute Global Reach.

:::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png" alt-text="Azure VMware Solution Hub e implementação de integração de spoke" border="false" lightbox="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png":::

A arquitetura tem os seguintes componentes principais:

-   **Local:** Centro de dados do cliente no local ligado ao Azure através de uma ligação ExpressRoute.

-   **Nuvem privada Azure VMware Solution:** Azure VMware Solution SDDC formado por um ou mais aglomerados vSphere, cada um com um máximo de 16 nós.

-   **Gateway ExpressRoute:** Permite a comunicação entre a nuvem privada Azure VMware Solution, serviços partilhados na rede virtual Hub e cargas de trabalho em execução em redes virtuais Spoke.

-   **ExpressRoute Global Reach:** Permite a conectividade entre as instalações e a nuvem privada Azure VMware Solution.


  > [!NOTE]
  > **Considerações de VPN S2S:** Para as implementações de produção da Azure VMware Solution, a Azure S2S VPN não é suportada devido aos requisitos de rede para VMware HCX. No entanto, pode ser usado para uma implantação de PoC.


-   **Rede virtual do hub:** Funciona como o ponto central de conectividade para a sua rede no local e nuvem privada Azure VMware Solution.

-   **Rede virtual falada**

    -   **IaaS Falou:** Um iaaS spoke acolhe cargas de trabalho baseadas em Azure IaaS, incluindo conjuntos de disponibilidade de VM e conjuntos de escala de máquina virtual, e os correspondentes componentes de rede.

    -   **PaaS Falou:** A PaaS Spoke acolhe serviços Azure PaaS utilizando endereços privados graças ao [Private Endpoint](../private-link/private-endpoint-overview.md) e [private Link](../private-link/private-link-overview.md).

-   **Firewall Azure:** Atua como peça central para segmentar o tráfego entre a Porta-voz e a Solução Azure VMware.

-   **Gateway de aplicação:** Expõe e protege aplicações web que funcionam quer em Azure IaaS/PaaS, quer em Azure VMware Solution (VMs). Integra-se com outros serviços como a API Management.

## <a name="network-and-security-considerations"></a>Considerações de rede e segurança

As ligações ExpressRoute permitem que o tráfego flua entre as instalações, a Azure VMware Solution e o tecido de rede Azure. A Azure VMware Solution utiliza [o ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) para implementar esta conectividade.

Como um gateway ExpressRoute não fornece um encaminhamento transitório entre os seus circuitos conectados, a conectividade no local também deve usar o ExpressRoute Global Reach para comunicar entre o ambiente vSphere no local e a Solução VMware Azure. 

* **No local para o fluxo de tráfego da Solução VMware Azure**

  :::image type="content" source="./media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png" alt-text="Azure VMware Solution Hub e implementação de integração de spoke" border="false" lightbox="./media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png":::


* **Solução Azure VMware para o fluxo de tráfego hub VNET**

  :::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png" alt-text="Azure VMware Solution Hub e implementação de integração de spoke" border="false" lightbox="./media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png":::


Pode encontrar mais detalhes sobre a rede de soluções Azure VMware e conceitos de conectividade na documentação do [produto Azure VMware Solution](./concepts-networking.md).

### <a name="traffic-segmentation"></a>Segmentação do tráfego

[Azure Firewall](../firewall/index.yml) é a peça central da topologia hub e spoke, implantada na rede virtual Hub. Utilize o Azure Firewall ou outro aparelho virtual suportado pela Azure para estabelecer regras de tráfego e segmentar a comunicação entre os diferentes raios e as cargas de trabalho da Azure VMware Solution.

Crie tabelas de rota para direcionar o tráfego para a Firewall Azure.  Para as redes virtuais Spoke, crie uma rota que define a rota padrão para a interface interna do Azure Firewall, desta forma quando uma carga de trabalho na Rede Virtual precisa de chegar ao espaço de endereço de resolução Azure VMware Solução a firewall pode avaliá-la e aplicar a regra de tráfego correspondente para permitir ou negar.  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="Azure VMware Solution Hub e implementação de integração de spoke" lightbox="media/hub-spoke/create-route-table-to-direct-traffic.png":::


> [!IMPORTANT]
> Não é suportada uma rota com prefixo de endereço 0.0.0.0/0 na definição **GatewaySubnet.**

Definir rotas para redes específicas na tabela de rotas correspondente. Por exemplo, as rotas para chegar à gestão da Solução VMware Azure e cargas de trabalho IP prefixas das cargas de trabalho faladas e vice-versa.

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="Azure VMware Solution Hub e implementação de integração de spoke" lightbox="media/hub-spoke/specify-gateway-subnet-for-route-table.png":::

Um segundo nível de segmentação de tráfego utilizando os grupos de segurança da rede dentro dos Porta-vozes e do Hub para criar uma política de tráfego mais granular.

> [!NOTE]
> **Tráfego de instalações para Azure VMware Solution:** O tráfego entre cargas de trabalho no local, quer com base em vSphere, quer outros, é ativado pela Global Reach, mas o tráfego não passa pelo Azure Firewall no centro. Neste cenário, deve implementar mecanismos de segmentação de tráfego no local ou na Azure VMware Solution.

### <a name="application-gateway"></a>Gateway de Aplicação

A azure Application Gateway V1 e V2 foram testados com aplicações web que funcionam em VMs de Solução VMware Azure como pool de backend. O Application Gateway é atualmente o único método suportado para expor aplicações web em execução em VMs de Solução VMware Azure para a internet. Também pode expor as aplicações a utilizadores internos de forma segura.

Reveja o artigo específico da Azure VMware Solution no [Application Gateway](./protect-azure-vmware-solution-with-application-gateway.md) para obter os detalhes e requisitos.

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="Azure VMware Solution Hub e implementação de integração de spoke" border="false":::


### <a name="jump-box-and-azure-bastion"></a>Caixa de salto e Bastião Azure

Acesso Azure VMware Solution ambiente com jump box, que é um Windows 10 ou Windows Server VM implantado na sub-rede de serviço partilhado dentro da rede virtual Hub.

>[!IMPORTANT]
>Azure Bastion é o serviço recomendado para ligar à caixa de salto para evitar expor a Solução Azure VMware à internet. Não é possível utilizar o Azure Bastion para ligar aos VMs da Solução VMware Azure, uma vez que não são objetos Azure IaaS.  

Como uma boa prática de segurança, implemente o serviço [Microsoft Azure Bastion](../bastion/index.yml) dentro da rede virtual Hub. O Azure Bastion fornece acesso sem costura de PDR e SSH aos VM implantados em Azure sem a necessidade de fornecer endereços IP públicos a esses recursos. Uma vez prestado o serviço Azure Bastion, pode aceder ao VM selecionado a partir do portal Azure. Depois de estabelecer a ligação, abre-se um novo separador, mostrando o ambiente de trabalho da caixa de salto, e a partir desse ambiente de trabalho, pode aceder ao plano de gestão de nuvem privada Azure VMware Solution.

> [!IMPORTANT]
> Não forneça o endereço IP público para a caixa de salto VM nem exponha a porta 3389/TCP à internet pública. 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Azure VMware Solution Hub e implementação de integração de spoke" border="false":::


## <a name="azure-dns-resolution-considerations"></a>Considerações de resolução do Azure DNS

Para a resolução Azure DNS existem duas opções disponíveis:

-   Utilize os controladores de domínio Azure Ative (Azure AD) implantados no Hub (descritos em [considerações de identidade)](#identity-considerations)como servidores de nome.

-   Implemente e configuure uma zona privada Azure DNS.

A melhor abordagem é combinar ambos para fornecer uma resolução de nome fiável para Azure VMware Solution, no local, e Azure.

Como recomendação geral de design, utilize a infraestrutura Azure DNS existente (neste caso, DNS integrados ao Diretório Ativo) implantada em pelo menos dois VMs Azure implantados na rede virtual Hub e configurados nas redes virtuais Spoke para utilizar os servidores DNS do Azure nas definições de DNS.

O Azure Private DNS ainda pode ser usado onde a zona de DNS privado Azure está ligada às redes virtuais, e os servidores DNS são usados como resolver híbridos com encaminhamento condicional para as instalações/Azure VMware Solution executando a infraestrutura de DNS DS alavancando o cliente Azure Private DNS.

Existem várias considerações a considerar para as zonas privadas do Azure DNS:

* O autoregistamento deve ser ativado para que o Azure DNS gere automaticamente o ciclo de vida dos registos DNS para os VMs implantados nas redes virtuais Spoke.
* O número máximo de zonas privadas de DNS a que uma rede virtual pode ser ligada com o autoregistration ativado é apenas um.
* O número máximo de zonas privadas de DNS a que uma rede virtual pode ser ligada é de 1000 sem que seja ativado o auto-registo.

As instalações e os servidores Azure VMware Solution podem ser configurados com reencaminhadores condicional para resolver VMs em Azure para a zona Azure Private DNS.

## <a name="identity-considerations"></a>Considerações de identidade

Para fins de identidade, a melhor abordagem é implantar pelo menos um controlador de domínio AD no Hub, utilizando a sub-rede de serviço partilhada, idealmente duas delas em forma distribuída por zona ou um conjunto de disponibilidade de VM. Consulte [o Centro de Arquitetura Azure](/azure/architecture/reference-architectures/identity/adds-extend-domain) para estender o seu domínio de AD no local até Azure.

Adicionalmente, implemente outro controlador de domínio no lado Azure VMware Solution para agir como identidade e fonte de DNS dentro do ambiente vSphere.

Como uma melhor prática recomendada, integrar [o domínio AD com o Azure Ative Directory](/azure/architecture/reference-architectures/identity/azure-ad).

<!-- LINKS - external -->
[Azure Architecture Center]: /azure/architecture/

[Hub & Spoke topology]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: ../networking/index.yml

<!-- LINKS - internal -->
