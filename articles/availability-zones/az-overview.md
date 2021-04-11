---
title: Regiões e Zonas de Disponibilidade em Azure
description: Conheça as regiões e zonas de disponibilidade em Azure para atender aos seus requisitos técnicos e regulamentares.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: 44ff938d0990a1a7794140a82c0e9222bb69a5d7
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384260"
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
| serviço mainstream | Um serviço Azure disponível em todas as regiões recomendadas no prazo de 90 dias a partir da disponibilidade geral da região ou disponibilidade orientada para a procura em regiões alternativas. |
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

- **Fundacional** – Disponível em todas as regiões recomendadas e alternativas quando a região está geralmente disponível, ou no prazo de 90 dias da disponibilização geral de um novo serviço fundacional.
- **Mainstream** – Disponível em todas as regiões recomendadas no prazo de 90 dias a partir da disponibilidade geral da região; orientados pela procura em regiões alternativas (muitas já estão implantadas num grande subconjunto de regiões alternativas).
- **Ofertas especializadas** – Ofertas de serviços direcionados, muitas vezes focadas na indústria ou apoiadas por hardware personalizado/especializado. Disponibilidade orientada para a procura em regiões (muitas já estão implantadas num grande subconjunto de regiões recomendadas).

Para ver quais os serviços que são implantados numa determinada região, bem como o futuro roteiro para pré-visualização ou disponibilidade geral de serviços numa região, consulte [os Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=all)

Se uma oferta de serviço não estiver disponível numa região específica, pode partilhar o seu interesse contactando o seu representante de vendas da Microsoft.

| Tipo de região | Não regional | Fundacional | Mainstream | Especializada | Zonas de Disponibilidade | Residência dos dados |
| --- | --- | --- | --- | --- | --- | --- |
| Recomendado | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Orientada para a procura | :heavy_check_mark: | :heavy_check_mark: |
| Suplente | :heavy_check_mark: | :heavy_check_mark: | Orientada para a procura | Orientada para a procura | N/D | :heavy_check_mark: |

### <a name="services-by-category"></a>Serviços por categoria

Como mencionado anteriormente, a Azure classifica os serviços em três categorias: fundacional, mainstream e especializada. As categorias de serviço são atribuídas à disponibilidade geral. Muitas vezes, os serviços iniciam o seu ciclo de vida como um serviço especializado e à medida que a procura e o aumento da utilização podem ser promovidos ao mainstream ou fundacional. A tabela a seguir enumera a categoria de serviços como fundamental, mainstream. Deve ter em conta o seguinte sobre a tabela:

- Alguns serviços não são regionais. Para obter informações e uma lista de serviços não regionais, consulte [produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/)
- A geração mais antiga de serviços ou máquinas virtuais não está listada. Para obter mais informações, consulte a documentação em [gerações anteriores de tamanhos de máquinas virtuais.](../virtual-machines/sizes-previous-gen.md)
- Os serviços não são atribuídos a uma categoria até disponibilidade geral (GA). Para obter informações e uma lista de serviços de pré-visualização, consulte [produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/) 

> [!div class="mx-tableFixed"]
> | Fundacional                           | Mainstream                                        | 
> |----------------------------------------|---------------------------------------------------|
> | Contas de Armazenamento                       | Gestão de API                                    | 
> | Gateway de Aplicação                    | App Configuration                                 | 
> | Azure Backup                           | Serviço de Aplicações                                       | 
> | Azure Cosmos DB                        | Automatização                                        | 
> | Armazenamento do Azure Data Lake Ger2           | Azure Active Directory Domain Services            | 
> | Azure ExpressRoute                     | Azure Bastion                                     | 
> | IP Público do Azure                        | Cache do Azure para Redis                             | 
> | Base de Dados SQL do Azure                     | Azure Cognitive Search                            | 
> | Instância Gerida do SQL do Azure             | Serviços Cognitivos do Azure                          | 
> | Armazenamento de Discos                           | Serviços Cognitivos do Azure: Imagem Digitalizada         | 
> | Hubs de Eventos                             | Azure Cognitive Services: Moderador de Conteúdo       | 
> | Key Vault                              | Serviços Cognitivos Azure: Rosto                    | 
> | Balanceador de carga                          | Azure Cognitive Services: Leitor imersivo        | 
> | Service Bus                            | Serviços Cognitivos Azure: Compreensão da Linguagem  | 
> | Service Fabric                         | Serviços Cognitivos Azure: Serviços de Fala         | 
> | Armazenamento: Níveis de armazenamento de blob quente/fresco   | Serviços Cognitivos do Azure: Análise de Texto          | 
> | Armazenamento: Discos geridos                 | Serviços Cognitivos Azure: Tradutor              | 
> | Conjuntos de Dimensionamento de Máquinas Virtuais             | Azure Data Explorer                               | 
> | Máquinas Virtuais                       | Azure Data Share                                  | 
> | Máquinas Virtuais: Azure Anfitrião Dedicado | Base de Dados do Azure para MySQL                          | 
> | Máquinas Virtuais: Av2-Series           | Base de Dados do Azure para PostgreSQL                     | 
> | Máquinas Virtuais: Bs-Series            | Azure DDoS Protection                             | 
> | Máquinas Virtuais: DSv2-Series          | Azure Firewall                                    | 
> | Máquinas Virtuais: DSv3-Series          | Azure Firewall Manager                            | 
> | Máquinas Virtuais: Dv2-Series           | Funções do Azure                                   | 
> | Máquinas Virtuais: Dv3-Series           | Hub IoT do Azure                                     |     
> | Máquinas Virtuais: ESv3-Series          | Azure Kubernetes Service (AKS)                    | 
> | Máquinas Virtuais: Ev3-Series           | Azure Machine Learning                            | 
> | Rede Virtual                        | Monitor Azure: Insights de Aplicação               | 
> | Gateway de VPN                            | Monitor Azure: Log Analytics                      | 
> |                                        | Azure Private Link                                | 
> |                                        | Azure Red Hat OpenShift                           | 
> |                                        | Azure Site Recovery                               | 
> |                                        | Azure Stream Analytics                            | 
> |                                        | Azure Synapse Analytics                           | 
> |                                        | Batch                                             | 
> |                                        | Cloud Services: Série M                          | 
> |                                        | Container Instances                               | 
> |                                        | Container Registry                                | 
> |                                        | Data Factory                                      | 
> |                                        | Event Grid                                        | 
> |                                        | HDInsight                                         |  
> |                                        | Logic Apps                                        | 
> |                                        | Serviços de Multimédia                                    | 
> |                                        | Observador de Rede                                   | 
> |                                        | Hubs de Notificação                                 | 
> |                                        | Armazenamento Premium Blob                              | 
> |                                        | Armazenamento de ficheiros premium                             | 
> |                                        | Máquinas Virtuais: Ddsv4-Series                    | 
> |                                        | Máquinas Virtuais: Ddv4-Series                     | 
> |                                        | Máquinas Virtuais: Dsv4-Series                     | 
> |                                        | Máquinas Virtuais: Dv4-Series                      | 
> |                                        | Máquinas Virtuais: Edsv4-Series                    | 
> |                                        | Máquinas Virtuais: Edv4-Series                     | 
> |                                        | Máquinas Virtuais: Esv4-Series                     | 
> |                                        | Máquinas Virtuais: Ev4-Series                      | 
> |                                        | Máquinas Virtuais: Fsv2-Series                     | 
> |                                        | Máquinas Virtuais: Série M                        | 
> |                                        | WAN Virtual                                       | 



### <a name="specialized-services"></a>Serviços Especializados
Como mencionado anteriormente, a Azure classifica os serviços em três categorias: fundacional, mainstream e especializada. As categorias de serviço são atribuídas à disponibilidade geral. Muitas vezes, os serviços iniciam o seu ciclo de vida como um serviço especializado e à medida que a procura e o aumento da utilização podem ser promovidos ao mainstream ou fundacional. A tabela que se segue lista serviços especializados. 

> [!div class="mx-tableFixed"]
> | Especializada                                          |
> |------------------------------------------------------|
> | API do Azure para FHIR                                   |
> | Azure Analysis Services                              |
> | Serviços Cognitivos Azure: Detetor de Anomalias           |
> | Serviços Cognitivos Azure: Visão Personalizada              |
> | Serviços Cognitivos Azure: Reconhecimento de Formulários            |
> | Serviços Cognitivos Azure: Personalizante               |
> | Serviços Cognitivos Azure: Fabricante QnA                  |
> | Azure Database for MariaDB                           |
> | Azure Database Migration Service                     |
> | HSM Dedicado do Azure                                  |
> | Azure Digital Twins                                  |
> | Azure Health Bot                                     |
> | Azure HPC Cache                                      |
> | Azure Lab Services                                   |
> | Azure NetApp Files                                   |
> | Azure SignalR Service                                |
> | Serviço de Nuvem de primavera Azure                           |
> | Azure Time Series Insights                           |
> | Solução VMware no Azure                                |
> | Azure VMware Solution by CloudSimple                 |
> | Data Lake Analytics                                  |
> | Azure Machine Learning Studio (clássico)              |
> | Spatial Anchors                                      |
> | Armazenamento: Armazenamento de arquivo                             |
> | Armazenamento ultra disco                                   |
> | Video Indexer                                        |
> | Máquinas Virtuais: DASv4-Series                       |
> | Máquinas Virtuais: DAv4-Series                        |
> | Máquinas Virtuais: Série DCsv2                       |
> | Máquinas Virtuais: EASv4-Series                       |
> | Máquinas Virtuais: EAv4-Series                        |
> | Máquinas Virtuais: HBv1-Series                        |
> | Máquinas Virtuais: HBv2-Series                        |
> | Máquinas Virtuais: HCv1-Series                        |
> | Máquinas Virtuais: Série H                           |
> | Máquinas Virtuais: LSv2-Series                        |
> | Máquinas Virtuais: Mv2-Series                         |
> | Máquinas Virtuais: NCv3-Series                        |
> | Máquinas Virtuais: NDv2-Series                        |
> | Máquinas Virtuais: NVv3-Series                        |
> | Máquinas Virtuais: NVv4-Series                        | 
> | Máquinas Virtuais: SAP HANA em Azure Large Instances  |




## <a name="next-steps"></a>Passos seguintes

- [Regiões que suportam Zonas de Disponibilidade em Azure](az-region.md)
- [Modelos de início rápido](https://aka.ms/azqs)
