---
title: Conceito - Integre uma implementação de Azure VMware Solution (AVS) num hub e tenha uma arquitetura de fala
description: Conheça as recomendações para a integração de uma implementação de Azure VMware Solution (AVS) num centro existente ou novo e falou arquitetura sobre Azure.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 0d95ed81c5188eab0dc508f5320549c4a402e151
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87062926"
---
# <a name="integrate-azure-vmware-solution-avs-in-a-hub-and-spoke-architecture"></a>Integre a Solução Azure VMware (AVS) num hub e falou arquitetura

Neste artigo, fornecemos recomendações para a integração de uma implementação de Azure VMware Solution (AVS) num hub existente ou numa nova [arquitetura hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/shared-services) on Azure. 

O cenário Hub e Spoke assumem um ambiente híbrido em nuvem com cargas de trabalho em:

* Azure nativo usando serviços IaaS ou PaaS
* AVS 
* vSphere no local

## <a name="architecture"></a>Arquitetura

O *Hub* é uma Rede Virtual Azure que funciona como um ponto central de conectividade com as suas instalações e nuvem privada AVS. Os *Spokes* são redes virtuais espreitadas com o Hub para permitir a comunicação de rede transpare virtual.

O tráfego entre o datacenter no local, a nuvem privada AVS e o Hub passa pelas ligações ExpressRoute. As redes virtuais faladas geralmente contêm cargas de trabalho baseadas em IaaS, mas podem ter serviços PaaS como [o App Service Environment](../app-service/environment/intro.md), que tem integração direta com a Rede Virtual, ou outros serviços PaaS com [Azure Private Link](../private-link/index.yml) ativados. 

O diagrama mostra um exemplo de uma implantação de Hub e Spoke em Azure ligada a instalações e AVS através do ExpressRoute.

:::image type="content" source="./media/hub-spoke/avs-hub-and-spoke-deployment.png" alt-text="Implementação de integração do AVS Hub e Spoke":::




A arquitetura tem os seguintes componentes principais:

-   **Local:** Centro de dados do cliente no local ligado ao Azure através de uma ligação via expressa.

-   **Nuvem privada AVS:** AVS SDDC formado por um ou mais aglomerados vSphere, cada um com um máximo de 16 nós.

-   **Gateway ExpressRoute:** Permite a comunicação entre a AVS Private Cloud, a rede no local, serviços partilhados na rede virtual Hub e cargas de trabalho em execução em redes virtuais Spoke.

    > [!NOTE]
    > **Considerações de VPN S2S:** Para as implantações de produção avs, o Azure S2S não é suportado devido aos requisitos de rede para o HCX. No entanto, para uma implantação de PoC ou não-produção que não exija HCX, pode ser utilizado.

-   **Rede virtual do hub:** Funciona como o ponto central de conectividade para a sua rede no local e nuvem privada AVS.

-   **Rede virtual falada**

    -   **IaaS Falou:** Um iaaS spoke irá acolher cargas de trabalho baseadas em Azure IaaS, incluindo conjuntos de disponibilidade de VM e conjuntos de escala de máquinas virtuais, e os correspondentes componentes de rede.

    -   **PaaS Falou:** A PaaS Spoke acolhe serviços Azure PaaS utilizando endereços privados graças ao [Private Endpoint](../private-link/private-endpoint-overview.md) e [private Link](../private-link/private-link-overview.md).

-   **Firewall Azure:** Atua como peça central para segmentar o tráfego entre os Porta-vozes, on-prem e AVS.

-   **Gateway de aplicação:** Expõe e protege aplicações web que funcionam quer em Azure IaaS/PaaS, quer em máquinas virtuais AVS (VMs). Integra-se com outros serviços como a API Management.

## <a name="network-and-security-considerations"></a>Considerações de rede e segurança

As ligações ExpressRoute permitem que o tráfego flua entre as instalações, o AVS e o tecido da rede Azure. O AVS utiliza [o ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) para implementar esta conectividade.

A conectividade no local também pode utilizar o ExpressRoute Global Reach, mas não é obrigatório.

* **No local para o fluxo de tráfego avs**

  :::image type="content" source="media/hub-spoke/on-prem-to-avs-traffic-flow.png" alt-text="No local para o fluxo de tráfego avs":::


* **FLUXO DE TRÁFEGO AVS para Hub VNET**

  :::image type="content" source="media/hub-spoke/avs-to-hub-vnet-traffic-flow.png" alt-text="FLUXO de tráfego de rede virtual AVS para Hub":::


Pode encontrar mais detalhes sobre conceitos de networking e interconectividade AVS na documentação do [produto AVS.](./concepts-networking.md)

### <a name="traffic-segmentation"></a>Segmentação do tráfego

[Azure Firewall](../firewall/index.yml) é a peça central da topologia hub e spoke, implantada na rede virtual Hub. Utilize o Azure Firewall ou outro aparelho virtual suportado pela Azure para estabelecer regras de tráfego e segmentar a comunicação entre os diferentes raios de estado, no local e cargas de trabalho AVS.

Crie tabelas de rota para direcionar o tráfego para a Firewall Azure.  Para as redes virtuais Spoke, crie uma rota que define a rota padrão para a interface interna do Azure Firewall, desta forma quando uma carga de trabalho na Rede Virtual precisa de chegar ao espaço de endereço AVS a firewall pode avaliá-la e aplicar a regra de tráfego correspondente para permitir ou negar.  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="Criar tabelas de rotas para direcionar o tráfego para a Firewall Azure":::


> [!IMPORTANT]
> Não é suportada uma rota com prefixo de endereço 0.0.0.0/0 na definição **GatewaySubnet.**

Definir rotas para redes específicas na tabela de rotas correspondente. Por exemplo, as rotas para chegar à gestão avs e cargas de trabalho IP prefixa a partir de instalações e vice-versa, encaminhando todo o tráfego de instalações para a nuvem privada AVS através de Azure Firewall.

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="Definir rotas para redes específicas no quadro de rotas correspondente":::

Um segundo nível de segmentação de tráfego utilizando os grupos de segurança da rede dentro dos Porta-vozes e do Hub para criar uma política de tráfego mais granular. 


### <a name="application-gateway"></a>Gateway de Aplicação

A azure Application Gateway V1 e V2 foram testados com aplicações web que funcionam em VMs AVS como piscina de backend. O Application Gateway é atualmente o único método suportado para expor aplicações web em execução em VMs AVS para a internet. Também pode expor as aplicações a utilizadores internos de forma segura.

:::image type="content" source="media/hub-spoke/avs-second-level-traffic-segmentation.png" alt-text="Segundo nível de segmentação de tráfego utilizando os Grupos de Segurança da Rede":::


### <a name="jumpbox-and-azure-bastion"></a>Jumpbox e Azure Bastion

Aceda ao ambiente AVS com o Jumpbox, que é um VM windows 10 ou Windows Server implantado na sub-rede de serviço partilhada dentro da rede virtual Hub.

Como uma boa prática de segurança, implemente o serviço [Microsoft Azure Bastion](../bastion/index.yml) dentro da rede virtual Hub. O Azure Bastion fornece acesso sem costura de PDR e SSH aos VM implantados em Azure sem a necessidade de fornecer endereços IP públicos a esses recursos. Uma vez prestado o serviço Azure Bastion, pode aceder ao VM selecionado a partir do portal Azure. Depois de estabelecer a ligação, abre-se um novo separador, mostrando o ambiente de trabalho Jumpbox, e a partir desse ambiente de trabalho, pode aceder ao plano de gestão de nuvem privada AVS.

> [!IMPORTANT]
> Não forneça o endereço IP público para a VM da Jumpbox nem exponha a porta 3389/TCP à internet pública. 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Rede virtual Azure Bastion Hub":::


## <a name="azure-dns-resolution-considerations"></a>Considerações de resolução do Azure DNS

Para a resolução Azure DNS existem duas opções disponíveis:

-   Utilize os controladores de domínio Azure Ative (Azure AD) implantados no Hub (descritos em [considerações de identidade)](#identity-considerations)como servidores de nome.

-   Implemente e configuure uma zona privada Azure DNS.

A melhor abordagem é combinar ambos para fornecer uma resolução de nome fiável para AVS, no local e Azure.

Como recomendação geral de design, utilize a infraestrutura Azure DNS existente (neste caso, DNS integrados ao Diretório Ativo) implantada em pelo menos dois VMs Azure implantados na rede virtual Hub e configurados nas redes virtuais Spoke para utilizar os servidores DNS do Azure nas definições de DNS.

O Azure Private DNS ainda pode ser usado onde a zona de DNS privado Azure está ligada às redes virtuais, e os servidores DNS são usados como resolver híbridos com encaminhamento condicional para as instalações/AVS que executam nomes DNS alavancando a infraestrutura de DNS privada do cliente Azure Private.

Existem várias considerações a considerar para as zonas privadas do Azure DNS:

* O autoregistamento deve ser ativado para que o Azure DNS gere automaticamente o ciclo de vida dos registos DNS para os VMs implantados nas redes virtuais Spoke.
* O número máximo de zonas privadas de DNS a que uma rede virtual pode ser ligada com o autoregistration ativado é apenas um.
* O número máximo de zonas privadas de DNS a que uma rede virtual pode ser ligada é de 1000 sem que seja ativado o auto-registo.

As instalações e os servidores AVS podem ser configurados com reencaminhadores condicional para resolver VMs em Azure para a zona de DNS Privados Azure.

## <a name="identity-considerations"></a>Considerações de identidade

Para fins de identidade, a melhor abordagem é implantar pelo menos um controlador de domínio AD no Hub, utilizando a sub-rede de serviço partilhada, idealmente duas delas em forma distribuída por zona ou um conjunto de disponibilidade de VM. Consulte [o Centro de Arquitetura Azure](/azure/architecture/reference-architectures/identity/adds-extend-domain) para estender o seu domínio de AD no local até Azure.

Adicionalmente, implementar outro controlador de domínio no lado AVS para agir como identidade e fonte de DNS dentro do ambiente vSphere.

Para vCenter e SSO, estabeleça uma fonte de identidade no portal Azure, sobre ** \> Gerir \> fontes de identidade**.

Como uma melhor prática recomendada, integrar [o domínio AD com o Azure Ative Directory](/azure/architecture/reference-architectures/identity/azure-ad).

<!-- LINKS - external -->
[Azure Architecture Center]: /azure/architecture/

[Hub & Spoke topology]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: ../networking/index.yml

<!-- LINKS - internal -->
