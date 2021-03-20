---
title: Visão geral da pré-visualização da infraestrutura baremetal em Azure
description: Visão geral da Infraestruturas BareMetal em Azure.
ms.custom: references_regions
ms.topic: conceptual
ms.date: 1/4/2021
ms.openlocfilehash: eb4dc129719dc410f7101598e3d72e68f17809c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97860976"
---
#  <a name="what-is-baremetal-infrastructure-preview-on-azure"></a>O que é a pré-visualização da Infraestruturas BareMetal no Azure?

A Azure BareMetal Infrastructure fornece uma solução segura para cargas de trabalho personalizadas da empresa migratória. As instâncias BareMetal são hardware de anfitrião/servidor não partilhado que lhe é atribuído. Desbloqueia a sua solução on-prem com cargas de trabalho especializadas que requerem contratos de hardware, licenciamento e suporte certificados. O Azure trata da monitorização e manutenção da infraestrutura para si, enquanto o sistema operativo (OS) no hóspede e a monitorização da aplicação estão dentro da sua propriedade.

A Infraestruturas BareMetal oferece um caminho para modernizar a sua paisagem de infraestruturas, mantendo os investimentos e arquitetura existentes. Com a Infraestruturas BareMetal, pode trazer cargas de trabalho especializadas para a Azure, permitindo-lhe acesso e integração com serviços Azure com baixa latência.

## <a name="sku-availability-in-azure-regions"></a>Disponibilidade de SKU nas regiões de Azure
A BareMetal Infrastructure para cargas de trabalho especializadas e gerais está disponível, a começar por quatro regiões com base nos selos da Revisão 4.2 (Rev 4.2):
- Europa Ocidental
- Europa do Norte
- E.U.A. Leste 2
- E.U.A. Centro-Sul

>[!NOTE]
>**Rev 4.2** é a mais recente infraestrutura baremetal rebranded usando a arquitetura rev 4 existente.  O Rev 4 proporciona uma maior proximidade com os anfitriões da máquina virtual Azure (VM). Tem melhorias significativas na latência da rede entre as unidades de instância Azure VMs e BareMetal implantadas em selos ou linhas Rev 4.  Pode aceder e gerir as suas instâncias BareMetal através do portal Azure. 

## <a name="support"></a>Suporte
A Infraestrutura BareMetal é compatível com a ISO 27001, ISO 27017, SOC 1 e SOC 2.  Também utiliza um modelo de "bring-your-your-own-license" (BYOL): OS, carga de trabalho especializada e aplicações de terceiros.  

Assim que receber acesso à raiz e controlo total, assume a responsabilidade por:
- Conceber e implementar soluções de backup e recuperação, elevada disponibilidade e recuperação de desastres
- Licenciamento, segurança e suporte para sistemas operativos e software de terceiros

A Microsoft é responsável por:
- Fornecendo o hardware para cargas de trabalho especializadas 
- Provisionamento do SISTEMA

:::image type="content" source="media/baremetal-support-model.png" alt-text="Modelo de suporte à infraestrutura BareMetal" border="false":::

## <a name="compute"></a>Computação
A Infraestruturas BareMetal oferece vários SKUs para cargas de trabalho especializadas. Os SKUs disponíveis variam desde o sistema de duas tomadas mais pequenos até ao sistema de 24 tomadas. Utilize os SKUs específicos para a sua carga de trabalho especializada.

O carimbo de instância BareMetal combina os seguintes componentes:

- **Computação:** Servidores baseados numa geração diferente de processadores Intel Xeon que fornecem a capacidade de computação necessária e são certificados para a carga de trabalho especializada.

- **Rede:** Um tecido de rede de alta velocidade unificado interliga componentes de computação, armazenamento e LAN.

- **Armazenamento:** Uma infraestrutura acessada através de um tecido de rede unificado.

Dentro da infraestrutura multi-arrendatário do carimbo BareMetal, os clientes são implantados em inquilinos isolados. Ao colocar um inquilino, nomeie uma assinatura Azure dentro da sua inscrição no Azure. Esta subscrição do Azure é a que os casos bareMetal são faturados.

>[!NOTE]
>Um cliente implantado no caso BareMetal é isolado num inquilino. Um inquilino está isolado na camada de networking, armazenamento e computação de outros inquilinos. As unidades de armazenamento e computação atribuídas aos diferentes inquilinos não podem ver-se ou comunicar entre si sobre os casos BareMetal.

## <a name="os"></a>SO
Durante o provisionamento da instância BareMetal, pode selecionar o SISTEMA que pretende instalar nas máquinas. 

>[!NOTE]
>Lembre-se, a Infraestrutura BareMetal é um modelo BYOL.

As versões Linux OS disponíveis são:
- Red Hat Enterprise Linux (RHEL) 7.6
- SUSE Linux Enterprise Server (SLES)
   - SLES 12 SP2
   - SLES 12 SP3
   - SLES 12 SP4
   - SLES 12 SP5
   - SLES 15 SP1

## <a name="storage"></a>Armazenamento
Os casos BareMetal baseados no tipo SKU específico vêm com armazenamento NFS predefinido para o tipo de carga de trabalho específica. Ao providenciar a BareMetal, pode providenciar mais armazenamento com base no seu crescimento estimado, apresentando um pedido de apoio. Todo o armazenamento vem com um disco all-flash na Revisão 4.2 com suporte para NFSv3 e NFSv4. A nova Revisão 4.5 NVMe SSD estará disponível. Para obter mais informações sobre o tamanho do armazenamento, consulte a secção de [carga de trabalho BareMetal.](../../../virtual-machines/workloads/sap/get-started.md)

>[!NOTE]
>O armazenamento utilizado para o BareMetal cumpre os requisitos [da Publicação 140-2 da Federal Information Processing Standard (FIPS) que](/microsoft-365/compliance/offering-fips-140-2) oferecem encriptação em repouso por padrão. Os dados são armazenados de forma segura nos discos.

## <a name="networking"></a>Rede
A arquitetura dos serviços de rede Azure é um componente fundamental para uma implementação bem sucedida de cargas de trabalho especializadas em instâncias BareMetal. É provável que nem todos os sistemas de TI já estejam localizados em Azure. O Azure oferece-lhe tecnologia de rede para fazer com que o Azure pareça um centro de dados virtual para as suas implementações de software no local. A funcionalidade da rede Azure necessária para as instâncias BareMetal é:

- As redes virtuais Azure estão ligadas ao circuito ExpressRoute que se conecta aos seus ativos de rede no local.
- Um circuito ExpressRoute que ligue as instalações ao Azure deve ter uma largura de banda mínima igual ou superior a 1 Gbps.
- Diretório Ative estendido e DNS em Azure ou completamente em execução em Azure.

A utilização do ExpressRoute permite-lhe estender a sua rede no local para a nuvem da Microsoft através de uma ligação privada com a ajuda de um fornecedor de conectividade. Você pode permitir que **o ExpressRoute Premium** aumente a conectividade através das fronteiras geopolíticas ou use **o ExpressRoute Local** para uma transferência de dados rentável entre a localização perto da região Azure que deseja.

As instâncias BareMetal são aprovisionadas dentro do intervalo de endereço IP do servidor Azure VNET.

:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" alt-text="Diagrama de Infraestrutura Azure BareMetal" lightbox="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" border="false":::

A arquitetura mostrada é dividida em três secções:
- **Esquerda:** mostra a infraestrutura no local do cliente que executa diferentes aplicações, conectando-se através do parceiro ou router de borda local como equinix. Para mais informações, consulte [os fornecedores e locais de conectividade: Azure ExpressRoute](../../../expressroute/expressroute-locations.md).
- **Centro:** mostra [o ExpressRoute](../../../expressroute/expressroute-introduction.md) a ser aprovisionado utilizando a sua subscrição Azure oferecendo conectividade à rede de arestas Azure.
- **Direito:** mostra Azure IaaS, e neste caso a utilização de VMs para hospedar as suas aplicações, que estão ajatadas dentro da sua rede virtual Azure.
- **Em baixo:** mostra usando o seu ExpressRoute Gateway ativado com [ExpressRoute FastPath](../../../expressroute/about-fastpath.md) para conectividade BareMetal oferecendo baixa latência.   
   >[!TIP]
   >Para suportar isto, o seu Portal ExpressRoute deve ser UltraPerformance.  Para mais informações, consulte [as portas de rede virtual ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md).

## <a name="next-steps"></a>Passos seguintes

O próximo passo é aprender a identificar e interagir com unidades de Exemplo BareMetal através do portal Azure.

> [!div class="nextstepaction"]
> [Gerir Instâncias Bare-metal através do portal do Azure](baremetal-infrastructure-portal.md)