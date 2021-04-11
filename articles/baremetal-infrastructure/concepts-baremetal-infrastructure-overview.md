---
title: Visão geral da Infraestrutura BareMetal em Azure
description: Fornece uma visão geral da Infraestrutura BareMetal em Azure.
ms.custom: references_regions
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/08/2021
ms.openlocfilehash: 7a4998a096a5c5d9e793c34d5046dce59262a2ae
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257573"
---
#  <a name="what-is-baremetal-infrastructure-on-azure"></a>O que é a Infraestrutura BareMetal em Azure?

O Microsoft Azure oferece uma infraestrutura em nuvem com uma vasta gama de serviços integrados na nuvem para atender às necessidades do seu negócio. Em alguns casos, porém, poderá ter de executar serviços em servidores metálicos nus sem uma camada de virtualização. Pode necessitar de acesso à raiz e controlo sobre o sistema operativo (OS). Para responder a essa necessidade, a Azure oferece infraestruturas BareMetal para várias aplicações de alto valor e críticas à missão.

A Infraestrutura BareMetal é composta por instâncias BareMetal dedicadas (instâncias computacionais), armazenamento de alto desempenho e aplicação apropriado (NFS, ISCSI e Fiber Channel), bem como um conjunto de LANs virtuais específicas da função (VLANs) num ambiente isolado. O armazenamento pode ser partilhado em instâncias BareMetal para permitir funcionalidades como clusters de escala ou para criar pares de alta disponibilidade com STONITH.
 
Este ambiente também tem VLANs especiais a que pode aceder se estiver a executar máquinas virtuais (VMs) em uma ou mais Redes Virtuais Azure (VNets) na sua subscrição Azure. Todo o ambiente é representado como um grupo de recursos na sua subscrição Azure.

A Infraestrutura BareMetal é oferecida em mais de 30 SKUs de 2 tomadas a 24 servidores de tomadas e memória que varia de 1,5 TB a 24 TBs. Um grande conjunto de SKUs também está disponível com memória Octane. O Azure oferece a maior gama de casos de metal nu numa nuvem de hiperescala.

## <a name="why-baremetal-infrastructure"></a>Porquê a Infraestruturas Desmômetros?  

Algumas cargas de trabalho centrais na empresa são compostas por tecnologias que simplesmente não são projetadas para funcionar em um cenário de nuvem virtualizada típica. Requerem arquitetura especial, hardware certificado ou tamanhos extraordinariamente grandes. Embora estas tecnologias tenham as funcionalidades mais sofisticadas de proteção de dados e continuidade de negócios, essas funcionalidades não são construídas para a nuvem virtualizada. São mais sensíveis a latências, vizinhos barulhentos, e requerem muito mais controlo sobre a gestão de mudanças e atividade de manutenção.

A Infraestrutura BareMetal é construída, certificada e testada para um conjunto selecionado de tais aplicações. A Azure foi a primeira a oferecer tais soluções, e desde então lidera com o maior portefólio e sistemas mais sofisticados.

A Infraestruturas BareMetal oferece estes benefícios: 

- Casos dedicados
- Hardware certificado para cargas de trabalho especializadas
    - SAP (Consulte a [nota SAP #1928533)](https://launchpad.support.sap.com/#/notes/1928533)
    - Oráculo (Consulte o [documento Oráculo ID #948372.1)](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=52088246571495&id=948372.1&_adf.ctrl-state=kwnkj1hzm_52)
- Metal nu (sem virtualização compute)
- Baixa latência entre Azure hospedado VMs de aplicação a instâncias BareMetal (0.35 ms)
- Todos os Flash SSD e NVMe
    - Até 1 PB/inquilino 
    - IOPS até 1,2 milhões/inquilino 
    - Largura de banda de rede de 40/100 GB
    - Acessível via NFS, ISCSI e FC
- Energia redundante, fontes de alimentação, NICs, TORs, portos, WANs, armazenamento e gestão
- Sobressalentes quentes para substituição de uma falha (sem necessidade de reconfiguração)
- Janelas de manutenção coordenadas do cliente
- Instantâneos conscientes da aplicação, arquivo, espelhamento e clonagem


## <a name="sku-availability-in-azure-regions"></a>Disponibilidade de SKU nas regiões de Azure

A BareMetal Infrastructure oferece vários SKUs certificados para cargas de trabalho especializadas. Utilize os SKUs específicos para atender às suas necessidades.

- Grandes casos – Desde sistemas de duas tomadas a quatro tomadas.  
- Casos muito grandes – Desde sistemas de quatro tomadas a vinte tomadas. 

A Infraestrutura BareMetal para cargas de trabalho especializadas está disponível nas seguintes regiões de Azure:
- Europa Ocidental
- Europa do Norte
- Alemanha Centro Ocidental *apoio zonas
- Apoio às zonas leste dos EUA 2 *
- Apoio às zonas leste dos EUA
- Apoio às zonas dos EUA
- Apoio às zonas west US 2 *
- E.U.A. Centro-Sul

>[!NOTE]
>O suporte de **zonas** refere-se a zonas de disponibilidade dentro de uma região onde os casos BareMetal podem ser implantados em zonas para alta resiliência e disponibilidade. Esta capacidade permite o suporte para o dimensionamento ativo multi-site.

## <a name="managing-baremetal-instances-in-azure"></a>Gestão de casos BareMetal em Azure 

Dependendo das suas necessidades, as topologias de aplicação da Infraestrutura BareMetal podem ser complexas. Pode implementar múltiplas instâncias, em um ou mais locais, com armazenamento partilhado ou dedicado, e ligações especializadas lan e WAN. Assim, para a Infraestrutura BareMetal, a Azure oferece uma captura consultiva dessa informação por um CSA/GBB no campo num portal de provisionamento. 

No momento em que a sua Infraestrutura BareMetal é aprovisionada, o SO, redes, volumes de armazenamento, colocações em zonas e regiões, e as ligações WAN entre locais já estão pré-configuradas. Está definido para registar as suas licenças de SO (BYOL), configurar o SO e instalar a camada de aplicação.

Poderá ver todos os recursos da Infraestruturas BareMetal, e o seu estado e atributos, no portal Azure. Você também pode operar as instâncias e pedidos de serviço aberto e bilhetes de apoio a partir daí. 

## <a name="operational-model"></a>Modelo operacional
A Infraestrutura BareMetal é compatível com a ISO 27001, ISO 27017, SOC 1 e SOC 2. Também utiliza um modelo de "bring-your-your-own-license" (BYOL): OS, carga de trabalho especializada e aplicações de terceiros.  

Assim que receber acesso à raiz e controlo total, assume a responsabilidade por:
- Conceber e implementar soluções de backup e recuperação, alta disponibilidade e recuperação de desastres.
- Licenciamento, segurança e suporte para o sistema operativo e software de terceiros.

A Microsoft é responsável por:
- Fornecendo o hardware para cargas de trabalho especializadas. 
- Provisionando o SO.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-support-model.png" alt-text="Diagrama do modelo de suporte da Infraestruturas BareMetal." border="false":::

## <a name="baremetal-instance-stamp"></a>Carimbo de exemplo BareMetal

O carimbo de instância BareMetal combina os seguintes componentes:

- **Computação:** Servidores baseados na geração de processadores Intel Xeon que fornecem a capacidade de computação necessária e são certificados para a carga de trabalho especializada.

- **Rede:** Um tecido de rede de alta velocidade unificado interliga componentes de computação, armazenamento e LAN.

- **Armazenamento:** Uma infraestrutura acessada através de um tecido de rede unificado.

Dentro da infraestrutura multi-arrendatário do carimbo BareMetal, os clientes são implantados em inquilinos isolados. Ao colocar um inquilino, nomeie uma assinatura Azure dentro da sua inscrição no Azure. Esta subscrição do Azure é a que está faturada para os seus casos BareMetal.

>[!NOTE]
>Um cliente que implementa um caso BareMetal está isolado num inquilino. Um inquilino está isolado na camada de networking, armazenamento e computação de outros inquilinos. As unidades de armazenamento e computação atribuídas a diferentes inquilinos não podem ver-se ou comunicar entre si nos seus casos BareMetal.

## <a name="operating-system"></a>Sistema operativo
Durante o provisionamento da instância BareMetal, pode selecionar o SISTEMA que pretende instalar nas máquinas. 

>[!NOTE]
>Lembre-se, a Infraestrutura BareMetal é um modelo BYOL.

As versões Linux OS disponíveis são:
- Red Hat Enterprise Linux (RHEL)
- SUSE Linux Enterprise Server (SLES)

## <a name="storage"></a>Armazenamento
A Infraestrutura BareMetal fornece armazenamento NFS altamente redundante e armazenamento de Fiber Channel. A infraestrutura oferece uma integração profunda para cargas de trabalho empresariais como SAP, SQL e muito mais. Também fornece capacidades consistentes de proteção de dados e gestão de dados. As ferramentas de gestão de self-service oferecem capacidades de replicação, clonagem e granular eficientes do espaço, juntamente com um único painel de monitorização de vidro. A infraestrutura permite capacidades zero de RPO e RTO para disponibilidade de dados e necessidades de continuidade do negócio. 

A infraestrutura de armazenamento oferece:
- Até 4 x 100 GB de ligações.
- Até 32 GB de ligações de canal de fibra.
- Todas as unidades flash SSD e NVMe.
- Latência ultra-baixa e alta produção.
- Escala até 4 PB de armazenamento cru. 
- Até 11 milhões de IOPS.

Estes protocolos de acesso a dados são suportados: 
- iSCSI 
- NFS (v3 ou v4) 
- Canal de Fibra 
- NVMe sobre o FC  

## <a name="networking"></a>Rede
A arquitetura dos serviços de rede Azure é um componente fundamental para uma implementação bem sucedida de cargas de trabalho especializadas em instâncias BareMetal. É provável que nem todos os sistemas de TI já estejam localizados em Azure. O Azure oferece-lhe tecnologia de rede para fazer com que o Azure pareça um centro de dados virtual para as suas implementações de software no local. A funcionalidade da rede Azure necessária para as instâncias BareMetal inclui:

- Redes virtuais Azure ligadas ao circuito Azure ExpressRoute que se conecta aos seus ativos de rede no local.
- O circuito ExpressRoute que liga as instalações ao Azure deve ter uma largura de banda mínima igual ou superior a 1 Gbps.
- Diretório Ativo Alargado e DNS em Azure, ou completamente em execução em Azure.

O ExpressRoute permite-lhe estender a sua rede no local para a nuvem da Microsoft através de uma ligação privada com a ajuda de um fornecedor de conectividade. Você pode usar **ExpressRoute Local** para transferência de dados rentável entre a sua localização no local e a região Azure que você deseja. Para alargar a conectividade através das fronteiras geopolíticas, pode ativar **o ExpressRoute Premium**. 

As instâncias BareMetal são aprovisionadas dentro do intervalo de endereço IP do servidor Azure VNet.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" alt-text="Diagrama arquitetónico do diagrama da Infraestrutura Azure BareMetal." lightbox="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" border="false":::

A arquitetura mostrada é dividida em três secções:
- **Esquerda:** Mostra a infraestrutura no local do cliente que executa diferentes aplicações, conectando-se através do parceiro ou router de borda local como equinix. Para mais informações, consulte [os fornecedores e locais de conectividade: Azure ExpressRoute](../expressroute/expressroute-locations.md).
- **Centro:** Mostra [o ExpressRoute](../expressroute/expressroute-introduction.md) a ser aprovisionado utilizando a sua subscrição Azure oferecendo conectividade à rede de arestas Azure.
- **À direita:** Mostra a Azure IaaS, e neste caso, a utilização de VMs para hospedar as suas aplicações, que estão ajatadas dentro da sua rede virtual Azure.
- **Em baixo:** Mostra usando o seu ExpressRoute Gateway ativado com [ExpressRoute FastPath](../expressroute/about-fastpath.md) para conectividade BareMetal oferecendo baixa latência.   
   >[!TIP]
   >Para suportar isto, o seu Portal ExpressRoute deve ser UltraPerformance. Para mais informações, consulte [as portas de rede virtual ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md).

## <a name="next-steps"></a>Passos seguintes

O próximo passo é aprender a identificar e interagir com os casos BareMetal através do portal Azure.

> [!div class="nextstepaction"]
> [Gerir casos BareMetal através do portal Azure](connect-baremetal-infrastructure.md)
