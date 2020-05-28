---
title: Regiões e Zonas de Disponibilidade em Azure
description: Conheça as regiões e zonas de disponibilidade em Azure para satisfazer os seus requisitos técnicos e regulamentares.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 04/28/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: 124aac96550b5d462c0794053452ed28dba27452
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84013219"
---
# <a name="regions-and-availability-zones-in-azure"></a>Regiões e Zonas de Disponibilidade em Azure

Os serviços do Microsoft Azure estão disponíveis globalmente para impulsionar as suas operações na nuvem a um nível ótimo. Pode escolher a melhor região para as suas necessidades com base em considerações técnicas e regulamentares: capacidades de serviço, residência de dados, requisitos de conformidade e latência.

## <a name="terminology"></a>Terminologia

Para melhor compreender as regiões e as Zonas de Disponibilidade em Azure, ajuda a compreender termos ou conceitos-chave.

| Termo ou conceito | Descrição |
| --- | --- |
| . | Um conjunto de datacenters implantados dentro de um perímetro definido pela latência e ligados através de uma rede regional dedicada de baixa latência. |
| geografia | Uma área do mundo contendo pelo menos uma região de Azure. As geografias definem um mercado discreto que preserva os limites da residência de dados e da conformidade. As localizações geográficas permitem que os clientes com uma residência de dados e necessidades de conformidade específicas mantenham os respetivos dados e aplicações próximos. As geografias são tolerantes a falhas para suportar a completa falha da região através da sua ligação à nossa dedicada infraestrutura de rede de alta capacidade. |
| Zona de Disponibilidade | Locais físicos únicos dentro de uma região. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. |
| região recomendada | Uma região que fornece a maior variedade de capacidades de serviço e foi projetada para apoiar as Zonas de Disponibilidade agora, ou no futuro. Estes são designados no portal Azure como **Recomendado**. |
| região alternativa (outra) | Uma região que alarga a pegada de Azure dentro de um limite de residência de dados onde também existe uma região recomendada. As regiões alternativas ajudam a otimizar a latência e a proporcionar uma segunda região para as necessidades de recuperação de catástrofes. Não foram concebidos para apoiar as Zonas de Disponibilidade (embora o Azure realize uma avaliação regular destas regiões para determinar se devem tornar-se regiões recomendadas). Estes são designados no portal Azure como **Outros**. |
| serviço fundacional | Um serviço core Azure que está disponível em todas as regiões quando a região está geralmente disponível. |
| serviço mainstream | Um serviço Azure que esteja disponível em todas as regiões recomendadas no prazo de 12 meses a partir da região/disponibilidade geral da região ou disponibilidade orientada para a procura em regiões alternativas. |
| serviço especializado | Um serviço Azure que é uma disponibilidade orientada para a procura em regiões apoiadapor hardware personalizado/especializado. |
| serviço regional | Um serviço Azure que é implantado regionalmente e permite ao cliente especificar a região em que o serviço será implantado. Para obter uma lista completa, consulte [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=all). |
| serviço não regional | Um serviço Azure para o qual não há dependência de uma região específica de Azure. Os serviços não regionais são implantados em duas ou mais regiões e, se houver uma falha regional, o caso do serviço noutra região continua a servir os clientes. Para obter uma lista completa, consulte [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=all). |

## <a name="regions"></a>Regiões

Uma região é um conjunto de datacenters implantados dentro de um perímetro definido pela latência e conectados através de uma rede regional dedicada de baixa latência. O Azure dá-lhe a flexibilidade para implementar aplicações onde for necessário, incluindo em várias regiões para oferecer resiliência transversal. Para mais informações, consulte a [visão geral do pilar de resiliência.](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)

## <a name="availability-zones"></a>Zonas de Disponibilidade

Uma Zona de Disponibilidade é uma oferta de alta disponibilidade que protege as suas aplicações e dados de falhas no datacenter. As Zonas de Disponibilidade são localizações físicas exclusivas numa região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões ativadas. A separação física das Zonas de Disponibilidade dentro de uma região protege aplicações e dados de falhas no datacenter. Os serviços redundantes em zonas replicam as suas aplicações e dados através das Zonas de Disponibilidade para proteger contra pontos únicos de falha. Com Zonas de Disponibilidade, o Azure oferece à indústria o melhor tempo de uptime De SLA de 99,99% vM. O [SLA do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) completo explica a disponibilidade garantida do Azure em termos globais.

Uma Zona de Disponibilidade numa região do Azure é uma combinação de um domínio de falha e um domínio de atualização. Por exemplo, se criar três ou mais VMs em três zonas de uma região do Azure, os seus VMs são efetivamente distribuídos por três domínios de falha e três domínios de atualização. A plataforma Azure reconhece esta distribuição através de domínios de atualização para garantir que os VMs em diferentes zonas não são atualizados ao mesmo tempo.

Construa alta disponibilidade na sua arquitetura de aplicação co-localizando a sua computação, armazenamento, networking e recursos de dados dentro de uma zona e replicando-se noutras zonas. Os serviços do Azure que suportam as Zonas de Disponibilidade estão divididos em duas categorias:

- **Serviços zonais** – onde um recurso é fixado a uma zona específica (por exemplo, máquinas virtuais, discos geridos, endereços IP standard), ou
- **Serviços de redução de zonas** – quando a plataforma Azure se replica automaticamente em zonas (por exemplo, armazenamento redundante em zona, Base de Dados SQL).

Para alcançar uma continuidade abrangente de negócios no Azure, construa a sua arquitetura de aplicação utilizando a combinação de Zonas de Disponibilidade com pares da região de Azure. Pode replicar sincronizadamente as suas aplicações e dados utilizando zonas de disponibilidade dentro de uma região de Azure para alta disponibilidade e replicação assincronicamente em todas as regiões de Azure para proteção de recuperação de desastres.
 
![visão conceptual de uma zona descendo em uma região](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> Os identificadores da Zona de Disponibilidade (os números 1, 2 e 3 na imagem acima) estão logicamente mapeados para as zonas físicas reais para cada subscrição de forma independente. Isto significa que a Zona de Disponibilidade 1 numa determinada subscrição pode referir-se a uma zona física diferente da Zona de Disponibilidade 1 numa subscrição diferente. Como consequência, recomenda-se não confiar em IDs da Zona de Disponibilidade em diferentes subscrições para colocação de máquinas virtuais.

## <a name="region-and-service-categories"></a>Região e categorias de serviços

A abordagem do Azure sobre a disponibilidade de serviços Azure em todas as regiões é melhor descrita através da expressão de serviços disponibilizados em regiões recomendadas e regiões alternativas.

- **Região recomendada** - Uma região que fornece a maior variedade de capacidades de serviço e foi projetada para apoiar as Zonas de Disponibilidade agora, ou no futuro. Estes são designados no portal Azure como **Recomendado**.
- **Região alternativa (outra)** - Uma região que alarga a pegada de Azure dentro de um limite de residência de dados onde também existe uma região recomendada. As regiões alternativas ajudam a otimizar a latência e a proporcionar uma segunda região para as necessidades de recuperação de catástrofes. Não foram concebidos para apoiar as Zonas de Disponibilidade (embora o Azure realize uma avaliação regular destas regiões para determinar se devem tornar-se regiões recomendadas). Estes são designados no portal Azure como **Outros**.

### <a name="comparing-region-types"></a>Comparando os tipos de região

Os serviços azure estão agrupados em três categorias: serviços fundacionais, mainstream e especializados. A política geral da Azure em matéria de implantação de serviços em qualquer região é essencialmente impulsionada pelo tipo de região, categorias de serviços e procura de clientes:

- **Fundacional** – Disponível em todas as regiões recomendadas e alternativas quando a região estiver geralmente disponível, ou no prazo de 12 meses após a disponibilização geral de um novo serviço fundacional.
- **Mainstream** – Disponível em todas as regiões recomendadas no prazo de 12 meses a partir da disponibilidade geral da região/serviço; impulsionados pela procura em regiões alternativas (muitos já estão implantados num grande subconjunto de regiões alternativas).
- **Ofertas de serviços especializadas** – direcionadas, muitas vezes focadas na indústria ou apoiadas por hardware personalizado/especializado. Disponibilidade orientada para a procura em todas as regiões (muitas já estão implantadas num grande subconjunto de regiões recomendadas).

Para ver quais os serviços que são implantados numa determinada região, bem como o futuro roteiro para pré-visualização ou disponibilidade geral de serviços numa região, consulte [produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=all)

Se uma oferta de serviço não estiver disponível numa região específica, pode partilhar o seu interesse contactando o seu representante de vendas da Microsoft.

| Tipo de região | Não-regional | Fundacional | Mainstream | Especializada | Zonas de Disponibilidade | Residência dos dados |
| --- | --- | --- | --- | --- | --- | --- |
| Recomendado | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Orientado para a procura | :heavy_check_mark: | :heavy_check_mark: |
| Suplente | :heavy_check_mark: | :heavy_check_mark: | Orientado para a procura | Orientado para a procura | N/D | :heavy_check_mark: |

### <a name="services-by-category"></a>Serviços por categoria

Como mencionado anteriormente, o Azure classifica os serviços em três categorias: fundacional, mainstream e especializado. As categorias de serviço saem atribuídas à disponibilidade geral. Muitas vezes, os serviços iniciam o seu ciclo de vida como um serviço especializado e à medida que a procura e a utilização aumentam podem ser promovidos para mainstream ou fundacional. A tabela seguinte lista a categoria de serviços como fundacional, mainstream ou especializado. Deve observar o seguinte sobre a tabela:

- Alguns serviços não são regionais. Para obter informações e uma lista de serviços não regionais, consulte [produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/)
- As máquinas virtuais de geração mais velha não estão listadas. Para mais informações, consulte documentação em [gerações anteriores de tamanhos de máquinas virtuais](../virtual-machines/sizes-previous-gen.md).

> [!div class="mx-tableFixed"]
> | Fundacional | Mainstream | Especializada |
> | --- | --- | --- |
> | Armazenamento de conta | Gestão de API | API do Azure para FHIR |
> | Gateway de Aplicação | App Configuration | Azure Blockchain Service |
> | Azure Backup | Serviço de Aplicações | Azure Blueprints |
> | Azure Cosmos DB | Automatização | Azure Database for MariaDB |
> | Armazenamento do Azure Data Lake Ger2 | Azure Active Directory Domain Services | HSM Dedicado do Azure |
> | Azure ExpressRoute | Azure Analysis Services | Espaços de Programador do Azure |
> | Base de Dados SQL do Azure | Azure Bastion | Azure Digital Twins |
> | Serviços Cloud | Cache do Azure para Redis | Azure Lab Services |
> | Serviços cloud: Série Av2 | Azure Cognitive Search | Azure NetApp Files |
> | Serviços cloud: Dv2-Series | Azure Data Explorer | Azure Quantum |
> | Serviços cloud: Dv3-Series | Azure Data Share | Azure Time Series Insights |
> | Serviços cloud: Ev3-Series | Base de Dados do Azure para MySQL | Azure VMware Solution by CloudSimple |
> | Serviços cloud: IPs de nível de instância | Base de Dados do Azure para PostgreSQL | Serviços cloud: A8 - A11 (Compute Intensivo) |
> | Serviços cloud: IP reservado | Azure Database Migration Service | Serviços cloud: G-Series |
> | Armazenamento em Disco | Azure Databricks | Serviços cloud: H-Series |
> | Hubs de Eventos | Proteção contra DDoS do Azure | Serviços Cognitivos: Detetor de Anomalias |
> | Cofre de Chaves | Azure DevTest Labs | Serviços Cognitivos : Visão Personalizada |
> | Load balancer | Azure Firewall Manager | Serviços Cognitivos : Reconhecimento de Altifalantes |
> | Service Bus | Azure Firewall | Data Box Heavy |
> | Service Fabric | Funções do Azure | Catálogo de Dados |
> | Conjuntos de Dimensionamento de Máquinas Virtuais | Azure HPC Cache | Fábrica de Dados : Fábrica de Dados V1 |
> | Máquinas Virtuais | Azure IoT Hub | Data Lake Analytics |
> | Máquinas Virtuais: Série Av2 | Serviço de Kubernetes do Azure (AKS) | Machine Learning Studio |
> | Máquinas Virtuais: Série B | Azure Machine Learning | Microsoft Genomics |
> | Máquinas Virtuais: DSv2-Series | Azure Private Link | Remote Rendering |
> | Máquinas Virtuais: DSv3-Series | Azure Red Hat OpenShift | Spatial Anchors |
> | Máquinas Virtuais: Série Dv2 | Azure Site Recovery | StorSimple |
> | Máquinas Virtuais: Série Dv3 | Serviço de nuvem de primavera azure | Indexador de Vídeos |
> | Máquinas Virtuais: Série ESv3 | Hub de pilha azure | Máquinas Virtuais: A8 - A11 (Compute Intensivo) |
> | Máquinas Virtuais: Ev3-Series | Azure Stream Analytics | Máquinas Virtuais: SÉRIE DASv4 |
> | Máquinas Virtuais: Série F | Azure Synapse Analytics | Máquinas Virtuais: DAv4-Series |
> | Máquinas Virtuais: Série FS | Serviço Azure SignalR | Máquinas Virtuais: Série DCsv2 |
> | Máquinas Virtuais: IPs de nível de instância | Batch | Máquinas Virtuais: Série EASv4 |
> | Máquinas Virtuais: IP reservado | Serviços cloud: série M | Máquinas Virtuais: Série EAv4 |
> | Rede Virtual | Serviços Cognitivos | Máquinas Virtuais: G-Series |
> | Gateway de VPN | Serviços Cognitivos: Visão Computacional | Máquinas Virtuais: GS-Series |
> |  | Serviços Cognitivos: Moderador de Conteúdo | Máquinas Virtuais: Série HBv1 |
> |  | Serviços Cognitivos: Face | Máquinas Virtuais: HBv2-Series |
> |  | Serviços Cognitivos: Compreensão da Linguagem | Máquinas Virtuais: Série HCv1 |
> |  | Serviços Cognitivos: Serviços de Fala | Máquinas Virtuais: Série H |
> |  | Serviços Cognitivos: Fabricante qnA | Máquinas Virtuais: LS-Series |
> |  | Container Instances | Máquinas Virtuais: LSv2-Series |
> |  | Registo de Contentor | Máquinas Virtuais: Mv2 -série |
> |  | Data Factory | Máquinas Virtuais: NC-Series |
> |  | Event Grid | Máquinas Virtuais: Série NCv2 |
> |  | HDInsight | Máquinas Virtuais: Série NCv3 |
> |  | Aplicações Lógicas | Máquinas Virtuais: NDs-Series |
> |  | Serviços de Multimédia | Máquinas Virtuais: NDv2-Series |
> |  | Observador de Rede | Máquinas Virtuais: Série NV |
> |  | Hubs de Notificação | Máquinas Virtuais: NVv3-Series |
> |  | Power BI Embedded | Máquinas Virtuais: Série NVv4 |
> |  | Armazenamento Premium Blob | Máquinas Virtuais: SAP HANA em Grandes Instâncias Azure |
> |  | Armazenamento de Ficheiros Premium | Visual Studio App Center |
> |  | Armazenamento: Armazenamento de arquivo |  |
> |  | Armazenamento ultra disco |  |
> |  | Máquinas Virtuais: Série Fsv2 |  |
> |  | Máquinas Virtuais: Série M |  |
> |  | WAN Virtual |  |

###  <a name="services-resiliency"></a>Resiliência dos serviços

Todos os serviços de gestão azure são arquitetos para serem resilientes a falhas a nível regional. No espectro de falhas, uma ou mais falhas da Zona de Disponibilidade numa região têm um raio de avaria menor em comparação com uma falha total da região. O Azure pode recuperar de uma falha de serviços de gestão ao nível da zona dentro da região ou de outra região do Azure. O Azure realiza uma zona de manutenção crítica numa zona de cada vez dentro de uma região, para evitar falhas que afetem os recursos dos clientes implantados em zonas de disponibilidade dentro de uma região.

### <a name="pricing-for-vms-in-availability-zones"></a>Preços para VMs em Zonas de Disponibilidade

Não existe um custo adicional para máquinas virtuais implantadas numa Zona de Disponibilidade. 99,99% VM uptime SLA é oferecido quando dois ou mais VMs são implantados em duas ou mais Zonas de Disponibilidade dentro de uma região de Azure. Haverá taxas adicionais de transferência de dados VM-to-VM da Zona Inter-Disponibilidade. Para mais informações, reveja a página de [preços da Largura de Banda.](https://azure.microsoft.com/pricing/details/bandwidth/)

### <a name="get-started-with-availability-zones"></a>Começar com Zonas de Disponibilidade

- [Criar uma máquina virtual](../virtual-machines/windows/create-portal-availability-zone.md)
- [Adicione um disco gerido utilizando powerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Criar um conjunto de escala de máquina virtual redundante de zona](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Peso de vMs em zonas usando um Balancer de Carga Padrão com uma extremidade frontal redundante de zona](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Peso de saldo VMs dentro de uma zona usando um Balancer de carga padrão com uma extremidade frontal zonal](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Armazenamento com redundância entre zonas](../storage/common/storage-redundancy-zrs.md)
- [Base de Dados SQL](../azure-sql/database/high-availability-sla.md#zone-redundant-configuration)
- [Recuperação após desastre geográfica dos Hubs de Eventos](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Recuperação após desastre geográfica do Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Criar um gateway de rede virtual com redundância entre zonas](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Adicione zona redundante região para Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Começar azure cache para redis zonas de disponibilidade](https://aka.ms/redis/az/getstarted)
- [Criar uma instância do Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md)
- [Crie um cluster azure Kubernetes Service (AKS) que utilize zonas de disponibilidade](../aks/availability-zones.md)

## <a name="next-steps"></a>Próximos passos

- [Regiões que apoiam zonas de disponibilidade em Azure](az-region.md)
- [Modelos de início rápido](https://aka.ms/azqs)
