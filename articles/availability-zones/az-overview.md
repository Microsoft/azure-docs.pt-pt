---
title: Regiões e Zonas de Disponibilidade em Azure
description: Conheça as regiões e zonas de disponibilidade em Azure para atender aos seus requisitos técnicos e regulamentares.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 08/27/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: 485042c795fd94ef7daa8e00b9869190e9df61c8
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97678554"
---
# <a name="regions-and-availability-zones-in-azure"></a>Regiões e Zonas de Disponibilidade em Azure

Os serviços microsoft Azure estão disponíveis globalmente para conduzir as suas operações na nuvem a um nível ideal. Pode escolher a melhor região para as suas necessidades com base em considerações técnicas e regulamentares: capacidades de serviço, residência de dados, requisitos de conformidade e latência.

## <a name="terminology"></a>Terminologia

Para melhor entender as regiões e as Zonas de Disponibilidade em Azure, ajuda a compreender termos ou conceitos chave.

| Termo ou conceito | Descrição |
| --- | --- |
| region | Um conjunto de datacenters implantados dentro de um perímetro definido pela latência e ligados através de uma rede regional dedicada de baixa latência. |
| geografia | Uma área do mundo contendo pelo menos uma região de Azure. As geografias definem um mercado discreto que preserva os limites de residência de dados e conformidade. As localizações geográficas permitem que os clientes com uma residência de dados e necessidades de conformidade específicas mantenham os respetivos dados e aplicações próximos. As geografias são tolerantes a falhas para resistir ao fracasso total da região através da sua ligação à nossa dedicada infraestrutura de rede de alta capacidade. |
| Zona de Disponibilidade | Localizações físicas únicas dentro de uma região. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. |
| região recomendada | Uma região que fornece a mais ampla gama de capacidades de serviço e é projetada para apoiar Zonas de Disponibilidade agora, ou no futuro. Estes são designados no portal Azure como **Recomendado**. |
| região alternativa (outra) | Uma região que estende a pegada de Azure dentro de um limite de residência de dados onde uma região recomendada também existe. Regiões alternativas ajudam a otimizar a latência e fornecem uma segunda região para as necessidades de recuperação de desastres. Não se destinam a apoiar zonas de disponibilidade (embora o Azure realize uma avaliação regular destas regiões para determinar se devem tornar-se regiões recomendadas). Estes são designados no portal Azure como **Outros**. |
| serviço fundacional | Um serviço core Azure que está disponível em todas as regiões quando a região está geralmente disponível. |
| serviço mainstream | Um serviço Azure disponível em todas as regiões recomendadas no prazo de 12 meses a partir da disponibilidade geral da região/serviço ou da disponibilidade orientada para a procura em regiões alternativas. |
| serviço especializado | Um serviço Azure que é orientado pela procura em regiões apoiadas por hardware personalizado/especializado. |
| serviço regional | Um serviço Azure que é implantado regionalmente e permite ao cliente especificar a região em que o serviço será implantado. Para obter uma lista completa, consulte [produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=all) |
| serviço não regional | Um serviço Azure para o qual não existe dependência de uma região específica de Azure. Os serviços não regionais são mobilizados para duas ou mais regiões e, em caso de falência regional, o serviço de outra região continua a servir os clientes. Para obter uma lista completa, consulte [produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=all) |

## <a name="regions"></a>Regiões

Uma região é um conjunto de datacenters implantados dentro de um perímetro definido pela latência e conectados através de uma rede regional dedicada de baixa latência. O Azure dá-lhe a flexibilidade para implementar aplicações onde precisa, incluindo em várias regiões para oferecer resiliência transversal. Para mais informações, consulte [a visão geral do pilar de resiliência.](/azure/architecture/framework/resiliency/overview)

## <a name="availability-zones"></a>Zonas de Disponibilidade

Uma Zona de Disponibilidade é uma oferta de alta disponibilidade que protege as suas aplicações e dados contra falhas no datacenter. As Zonas de Disponibilidade são localizações físicas exclusivas numa região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões ativadas. A separação física das Zonas de Disponibilidade dentro de uma região protege as aplicações e os dados contra falhas do datacenter. Os serviços redundantes de zona replicam as suas aplicações e dados através das Zonas de Disponibilidade para proteger de pontos de falha únicos. Com Zonas de Disponibilidade, o Azure oferece à indústria o melhor SLA de 99,99% de VM uptime. O [SLA do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) completo explica a disponibilidade garantida do Azure em termos globais.

Uma Zona de Disponibilidade numa região de Azure é uma combinação de um domínio de falha e um domínio de atualização. Por exemplo, se criar três ou mais VMs em três zonas numa região de Azure, os seus VMs são efetivamente distribuídos por três domínios de avaria e três domínios de atualização. A plataforma Azure reconhece esta distribuição através de domínios de atualização para garantir que os VMs em diferentes zonas não estão programados para serem atualizados ao mesmo tempo.

Construa alta disponibilidade na arquitetura da sua aplicação co-localizando os seus recursos de computação, armazenamento, rede e dados dentro de uma zona e replicando-se em outras zonas. Os serviços do Azure que suportam as Zonas de Disponibilidade estão divididos em duas categorias:

- **Serviços zonais** – quando um recurso é fixado a uma zona específica (por exemplo, máquinas virtuais, discos geridos, endereços IP padrão), ou
- **Serviços redundantes de zona** – quando a plataforma Azure se replica automaticamente em zonas (por exemplo, armazenamento redundante de zona, Base de Dados SQL).

Para alcançar uma ampla continuidade de negócios em Azure, construa a sua arquitetura de aplicações utilizando a combinação de Zonas de Disponibilidade com pares da região de Azure. Pode replicar sincronizadamente as suas aplicações e dados utilizando Zonas de Disponibilidade dentro de uma região de Azure para uma alta disponibilidade e replicação assíncrona em todas as regiões de Azure para proteção de recuperação de desastres.
 
![vista conceptual de uma zona descendo em uma região](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> Os identificadores da Zona de Disponibilidade (os números 1, 2 e 3 na imagem acima) são logicamente mapeados para as zonas físicas reais para cada subscrição de forma independente. Isto significa que a Zona de Disponibilidade 1 numa determinada subscrição pode referir-se a uma zona física diferente da Zona de Disponibilidade 1 numa subscrição diferente. Como consequência, recomenda-se não depender de IDs da Zona de Disponibilidade em diferentes subscrições para colocação de máquinas virtuais.

## <a name="region-and-service-categories"></a>Região e categorias de serviços

A abordagem do Azure sobre a disponibilidade de serviços Azure em todas as regiões é melhor descrita através da expressão de serviços disponibilizados em regiões recomendadas e regiões alternativas.

- **Região recomendada** - Uma região que fornece a mais ampla gama de capacidades de serviço e é projetada para apoiar Zonas de Disponibilidade agora, ou no futuro. Estes são designados no portal Azure como **Recomendado**.
- **Região alternativa (outra) -** Uma região que alarga a pegada de Azure dentro de um limite de residência de dados onde também existe uma região recomendada. Regiões alternativas ajudam a otimizar a latência e fornecem uma segunda região para as necessidades de recuperação de desastres. Não se destinam a apoiar zonas de disponibilidade (embora o Azure realize uma avaliação regular destas regiões para determinar se devem tornar-se regiões recomendadas). Estes são designados no portal Azure como **Outros**.

### <a name="comparing-region-types"></a>Comparar tipos de região

Os serviços Azure estão agrupados em três categorias: serviços fundamentais, mainstream e especializados. A política geral da Azure em matéria de implantação de serviços em qualquer região é essencialmente impulsionada pelo tipo de região, categorias de serviços e procura de clientes:

- **Fundacional** – Disponível em todas as regiões recomendadas e alternativas quando a região está geralmente disponível, ou no prazo de 12 meses após a disponibilização geral de um novo serviço fundacional.
- **Mainstream** – Disponível em todas as regiões recomendadas no prazo de 12 meses a partir da disponibilidade geral da região/serviço; orientados pela procura em regiões alternativas (muitas já estão implantadas num grande subconjunto de regiões alternativas).
- **Ofertas especializadas** – Ofertas de serviços direcionados, muitas vezes focadas na indústria ou apoiadas por hardware personalizado/especializado. Disponibilidade orientada para a procura em regiões (muitas já estão implantadas num grande subconjunto de regiões recomendadas).

Para ver quais os serviços que são implantados numa determinada região, bem como o futuro roteiro para pré-visualização ou disponibilidade geral de serviços numa região, consulte [os Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=all)

Se uma oferta de serviço não estiver disponível numa região específica, pode partilhar o seu interesse contactando o seu representante de vendas da Microsoft.

| Tipo de região | Não regional | Fundacional | Mainstream | Especializada | Zonas de Disponibilidade | Residência dos dados |
| --- | --- | --- | --- | --- | --- | --- |
| Recomendado | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Orientada para a procura | :heavy_check_mark: | :heavy_check_mark: |
| Suplente | :heavy_check_mark: | :heavy_check_mark: | Orientada para a procura | Orientada para a procura | N/D | :heavy_check_mark: |

### <a name="services-by-category"></a>Serviços por categoria

Como mencionado anteriormente, a Azure classifica os serviços em três categorias: fundacional, mainstream e especializada. As categorias de serviço são atribuídas à disponibilidade geral. Muitas vezes, os serviços iniciam o seu ciclo de vida como um serviço especializado e à medida que a procura e o aumento da utilização podem ser promovidos ao mainstream ou fundacional. A tabela a seguir lista a categoria de serviços como fundamental, mainstream ou especializada. Deve ter em conta o seguinte sobre a tabela:

- Alguns serviços não são regionais. Para obter informações e uma lista de serviços não regionais, consulte [produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/)
- Máquinas virtuais de geração mais velha não estão listadas. Para obter mais informações, consulte a documentação em [gerações anteriores de tamanhos de máquinas virtuais.](../virtual-machines/sizes-previous-gen.md)

> [!div class="mx-tableFixed"]
> | Fundacional | Mainstream | Especializada |
> | --- | --- | --- |
> | Armazenamento de conta | Gestão de API | API do Azure para FHIR |
> | Gateway de Aplicação | App Configuration | Azure Analysis Services |
> | Azure Backup | Serviço de Aplicações | Azure Blockchain Service |
> | Azure Cosmos DB | Automatização | Azure Blueprints |
> | Armazenamento do Azure Data Lake Ger2 | Azure Active Directory Domain Services | Azure Database for MariaDB |
> | Azure ExpressRoute | Azure Bastion | HSM Dedicado do Azure |
> | Base de Dados SQL do Azure | Cache do Azure para Redis | Azure Dev Spaces |
> | Serviços Cloud | Azure Cognitive Search | Azure Digital Twins |
> | Serviços na Nuvem: Av2-Series | Azure Data Explorer | Azure Lab Services |
> | Serviços na Nuvem: Dv2-Series | Azure Data Share | Azure NetApp Files |
> | Serviços na Nuvem: Dv3-Series | Base de Dados do Azure para MySQL | Azure Quantum |
> | Serviços na Nuvem: Ev3-Series | Base de Dados do Azure para PostgreSQL | Serviço de Nuvem de primavera Azure |
> | Serviços na Nuvem: IPs de nível de exemplo | Azure Database Migration Service | Azure Time Series Insights |
> | Serviços cloud: IP reservado | Azure Databricks | Azure VMware Solution by CloudSimple |
> | Armazenamento em Disco | Azure DDoS Protection | Serviços cloud: Série G |
> | Hubs de Eventos | Azure DevTest Labs | Serviços cloud: Série H |
> | Cofre de Chaves | Azure Firewall | Serviços Cognitivos : Visão Personalizada |
> | Balanceador de carga | Azure Firewall Manager | Serviços Cognitivos : Reconhecimento de Altifalantes |
> | Service Bus | Funções do Azure | Data Box Heavy |
> | Service Fabric | Azure HPC Cache | Catálogo de Dados |
> | Conjuntos de Dimensionamento de Máquinas Virtuais | Hub IoT do Azure | Data Factory : Data Factory V1 |
> | Máquinas Virtuais | Azure Kubernetes Service (AKS) | Data Lake Analytics |
> | Máquinas Virtuais: Av2-Series | Azure Machine Learning | Azure Machine Learning Studio (clássico)|
> | Máquinas Virtuais: Bs-Series | Azure Private Link | Microsoft Genomics |
> | Máquinas Virtuais: DSv2-Series | Azure Red Hat OpenShift | Remote Rendering |
> | Máquinas Virtuais: DSv3-Series | Serviço SignalR do Azure | Spatial Anchors |
> | Máquinas Virtuais: Dv2-Series | Azure Site Recovery | StorSimple |
> | Máquinas Virtuais: Dv3-Series | Azure Stack Hub | Video Indexer |
> | Máquinas Virtuais: ESv3-Series | Azure Stream Analytics | Máquinas Virtuais: DASv4-Series |
> | Máquinas Virtuais: Ev3-Series | Azure Synapse Analytics | Máquinas Virtuais: DAv4-Series |
> | Máquinas Virtuais: Série F | Batch | Máquinas Virtuais: Série DCsv2 |
> | Máquinas Virtuais: FS-Series | Cloud Services: Série M | Máquinas Virtuais: EASv4-Series |
> | Máquinas Virtuais: IPs de nível de exemplo | Serviços Cognitivos | Máquinas Virtuais: EAv4-Series |
> | Máquinas Virtuais: IP reservado | Serviços Cognitivos: Visão Computacional | Máquinas Virtuais: Série G |
> | Rede Virtual | Serviços Cognitivos: Moderador de Conteúdo | Máquinas Virtuais: GS-Series |
> | Gateway de VPN | Serviços Cognitivos: Rosto | Máquinas Virtuais: HBv1-Series |
> |  | Serviços Cognitivos: Reconhecimento de Formulários | Máquinas Virtuais: HBv2-Series |
> |  | Serviços Cognitivos: Compreensão da Linguagem | Máquinas Virtuais: HCv1-Series |
> |  | Serviços Cognitivos: Fabricante QnA | Máquinas Virtuais: Série H |
> |  | Serviços Cognitivos: Serviços de Fala | Máquinas Virtuais: LS-Series |
> |  | Container Instances | Máquinas Virtuais: LSv2-Series |
> |  | Container Registry | Máquinas Virtuais: Mv2-Series |
> |  | Data Factory | Máquinas Virtuais: NC-Series |
> |  | Event Grid | Máquinas Virtuais: NCv2-Series |
> |  | HDInsight | Máquinas Virtuais: NCv3-Series |
> |  | Logic Apps | Máquinas Virtuais: NDs-Series |
> |  | Serviços de Multimédia | Máquinas Virtuais: NDv2-Series |
> |  | Observador de Rede | Máquinas Virtuais: NV-Series |
> |  | Hubs de Notificação | Máquinas Virtuais: NVv3-Series |
> |  | Power BI Embedded | Máquinas Virtuais: NVv4-Series |
> |  | Armazenamento Premium Blob | Máquinas Virtuais: SAP HANA em Azure Large Instances |
> |  | Armazenamento de ficheiros premium | Visual Studio App Center |
> |  | Armazenamento: Armazenamento de arquivo |  |
> |  | Armazenamento ultra disco |  |
> |  | Máquinas Virtuais: Ddsv4-Series |  |
> |  | Máquinas Virtuais: Ddv4-Series |  |
> |  | Máquinas Virtuais: Dsv4-Series |  |
> |  | Máquinas Virtuais: Dv4-Series |  |
> |  | Máquinas Virtuais: Edsv4-Series |  |
> |  | Máquinas Virtuais: Edv4-Series |  |
> |  | Máquinas Virtuais: Esv4-Series |  |
> |  | Máquinas Virtuais: Ev4-Series |  |
> |  | Máquinas Virtuais: Fsv2-Series |  |
> |  | Máquinas Virtuais: Série M |  |
> |  | WAN Virtual |  |

###  <a name="services-resiliency"></a>Resiliência dos serviços

Todos os serviços de gestão da Azure são projetados para serem resistentes a falhas a nível regional. No espectro de falhas, uma ou mais falhas da Zona de Disponibilidade dentro de uma região têm um raio de falha menor em comparação com uma falha da região inteira. O Azure pode recuperar de uma falha ao nível da zona de serviços de gestão na região ou de outra região de Azure. O Azure realiza uma manutenção crítica uma zona de cada vez dentro de uma região, para evitar falhas que impactem os recursos dos clientes implantados através das Zonas de Disponibilidade dentro de uma região.

### <a name="pricing-for-vms-in-availability-zones"></a>Preços para VMs em Zonas de Disponibilidade

Não há custo adicional para máquinas virtuais implantadas numa Zona de Disponibilidade. 99,99% VM uptime SLA é oferecido quando dois ou mais VMs são implantados em duas ou mais Zonas de Disponibilidade dentro de uma região de Azure. Haverá taxas adicionais de transferência de dados da Zona VM-vM da Zona De Disponibilidade. Para mais informações, reveja a página [de preços da largura de banda.](https://azure.microsoft.com/pricing/details/bandwidth/)

### <a name="get-started-with-availability-zones"></a>Começar com Zonas de Disponibilidade

- [Criar uma máquina virtual](../virtual-machines/windows/create-portal-availability-zone.md)
- [Adicione um disco gerido usando PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Criar um conjunto de escala de máquina virtual redundante de zona](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Balançar VMs em zonas usando um Balanceador de Carga Padrão com uma linha frontal redundante de zona](../load-balancer/quickstart-load-balancer-standard-public-cli.md)
- [Balançar VMs de equilíbrio dentro de uma zona usando um Balanceador de Carga Padrão com um frontend zonal](../load-balancer/quickstart-load-balancer-standard-public-cli.md)
- [Armazenamento com redundância entre zonas](../storage/common/storage-redundancy.md)
- [SqL Database nível de propósito geral](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Recuperação após desastre geográfica dos Hubs de Eventos](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Recuperação após desastre geográfica do Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Criar um gateway de rede virtual com redundância entre zonas](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Adicionar zona de região redundante para Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Começar cache Azure para zonas de disponibilidade de redis](https://aka.ms/redis/az/getstarted)
- [Criar uma instância do Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md)
- [Criar um cluster de serviço Azure Kubernetes (AKS) que utiliza Zonas de Disponibilidade](../aks/availability-zones.md)
- [Permitir redundância de zona no Registo de Contentores de Azure](../container-registry/zone-redundancy.md)

## <a name="next-steps"></a>Passos seguintes

- [Regiões que suportam Zonas de Disponibilidade em Azure](az-region.md)
- [Modelos de início rápido](https://aka.ms/azqs)