---
title: Mover o computador principal para as máquinas virtuais Azure
description: Os recursos de computação Azure comparam-se favoravelmente à capacidade do mainframe para que possa migrar e modernizar aplicações IBM z14.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.openlocfilehash: 32f259f20e0e24b4d5346c598e23fdc2df48dee6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556439"
---
# <a name="move-mainframe-compute-to-azure"></a>Mover o computador principal para Azure

Os mainframes têm uma reputação de elevada fiabilidade e disponibilidade e continuam a ser a espinha dorsal de confiança de muitas empresas. Pensa-se que também têm uma escalabilidade quase ilimitada e poder de computação. No entanto, algumas empresas ultrapassaram a capacidade dos maiores quadros principais disponíveis. Se isto soa a si, o Azure oferece agilidade, alcance e poupança de infraestruturas.

Para executar cargas de trabalho no Microsoft Azure, precisa de saber como as capacidades de computação do seu computador principal se comparam ao Azure. Com base num mainframe IBM z14 (o modelo mais atual a partir desta escrita), este artigo diz-lhe como obter resultados comparáveis no Azure.

Para começar, considere os ambientes lado a lado. O seguinte valor compara um ambiente de quadro principal para executar aplicações a um ambiente de hospedagem Azure.

![Serviços Azure e ambientes de emulação oferecem apoio comparável e simplifica migração](media/mainframe-compute-azure.png)

O poder dos mainframes é frequentemente utilizado para sistemas de processamento de transações online (OLTP) que lidam com milhões de atualizações para milhares de utilizadores. Estas aplicações usam frequentemente software para processamento de transações, manuseamento de ecrãs e entrada de formulários. Podem utilizar um Sistema de Controlo de Informação do Cliente (CICS), Um Sistema de Gestão de Informação (IMS) ou um Pacote de Interface de Transação (TIP).

Como mostra o número, um emulador TPM em Azure pode lidar com cargas de trabalho CICS e IMS. Um emulador de sistema de lote em Azure desempenha o papel de Linguagem de Controlo de Emprego (JCL). Os dados do computador principal são migrados para bases de dados Azure, tais como Azure SQL Database. Os serviços Azure ou outro software alojado em Azure Virtual Machines podem ser utilizados para a gestão do sistema.

## <a name="mainframe-compute-at-a-glance"></a>Computação do computador principal num ápice

No computador central z14, os processadores estão dispostos em até quatro *gavetas*. Uma *gaveta* é simplesmente um conjunto de processadores e chipsets. Cada gaveta pode ter seis chips de processador central ativo (CP) e cada CP tem 10 chips de controlador de sistema (SC). Na terminologia Intel x86, existem seis tomadas por gaveta, 10 núcleos por tomada e quatro gavetas. Esta arquitetura fornece o equivalente áspero de 24 tomadas e 240 núcleos, no máximo, para um z14.

O rápido Z14 CP tem uma velocidade de 5,2 GHz. Normalmente, um z14 é entregue com todos os CPs na caixa. São ativados conforme necessário. Um cliente é geralmente cobrado por pelo menos quatro horas de tempo de cálculo por mês, apesar do uso real.

Um processador de computador principal pode ser configurado como um dos seguintes tipos:

- Processador de Final geral (GP)
- Processador integrado de informação do Sistema Z (zIIP)
- Facilidade Integrada para processador Linux (IFL)
- Processador de assistência ao sistema (SAP)
- Processador integrado de instalações de acoplamento (ICF)

## <a name="scaling-mainframe-compute-up-and-out"></a>Cálculo do computador principal de escala para cima e para fora

Os mainframes da IBM oferecem a capacidade de escalar até 240 núcleos (o tamanho atual do z14 para um único sistema). Além disso, os mainframes da IBM podem escalar através de uma funcionalidade chamada Facilidade de Engate (CF). O CF permite que vários sistemas mainframe acedam simultaneamente aos mesmos dados. Utilizando o CF, o computador principal A tecnologia Paralelo Sysplex agrupa processadores mainframe em clusters. Quando este guia foi escrito, a funcionalidade Sysplex Paralela suportava 32 agrupamentos de 64 processadores cada. Até 2.048 processadores podem ser agrupados desta forma para aumentar a capacidade de computação.

Um CF permite que os clusters de cálculo partilhem dados com acesso direto. É usado para bloquear informações, informações de cache e a lista de recursos de dados partilhados. Um Sysplex Paralelo usando um ou mais CFs pode ser considerado como um cluster de computação de escala "compartilhado". Para obter mais informações sobre estas [funcionalidades, consulte O Sysplex Paralelo na IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) no site da IBM.

As aplicações podem usar estas funcionalidades para fornecer desempenho de escala e alta disponibilidade. Para obter informações sobre como o CICS pode usar O Sysplex Paralelo com CF, descarregue o [IBM CICS e o Mecanismo de Engate: Além do redbook Basics.](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)

## <a name="azure-compute-at-a-glance"></a>Azure compute num relance

Algumas pessoas pensam erradamente que os servidores baseados na Intel não são tão poderosos como os mainframes. No entanto, os novos sistemas baseados em intel têm tanta capacidade de computação como os mainframes. Esta secção descreve as opções de infraestrutura Azure como serviço (IaaS) para computação e armazenamento. O Azure também fornece opções de plataforma como serviço (PaaS), mas este artigo foca-se nas escolhas do IaaS que proporcionam capacidade de mainframe comparável.

As Máquinas Virtuais Azure fornecem energia de computação numa gama de tamanhos e tipos. Em Azure, um CPU virtual (vCPU) equivale aproximadamente a um núcleo num computador central.

Atualmente, a gama de tamanhos de Máquina Virtual Azure fornece de 1 a 128 vCPUs. Os tipos de máquinas virtuais (VM) são otimizados para determinadas cargas de trabalho. Por exemplo, a seguinte lista mostra os tipos de VM (corrente a partir desta escrita) e as suas utilizações recomendadas:

| Tamanho     | Tipo e descrição                                                                 |
|----------|--------------------------------------------------------------------------------------|
| Série D | Finalidade geral com 64 vCPU e até 3,5 GHz de velocidade do relógio                           |
| Série E | Memória otimizada com até 64 vCPUs                                                 |
| Série F | Computo otimizado com até 64 vCPUs e velocidade do relógio de 3,7 GHz                       |
| Série H | Otimizado para aplicações de computação de alto desempenho (HPC)                          |
| Série L | Armazenamento otimizado para aplicações de alto rendimento apoiadas por bases de dados como o NoSQL |
| Série M | Maior computação e memória otimizada VMs com até 128 vCPUs                        |

Para mais informações sobre os VMs disponíveis, consulte [a série Máquina Virtual](https://azure.microsoft.com/pricing/details/virtual-machines/series/).

Um computador central z14 pode ter até 240 núcleos. No entanto, os mainframes z14 quase nunca usam todos os núcleos para uma única aplicação ou carga de trabalho. Em vez disso, um computador central segrega cargas de trabalho em divisórias lógicas (LPARs), e os LPARs têm classificações - MIPS (Milhões de Instruções por Segundo) ou MSU (Unidade de Serviço de Milhões). Ao determinar o tamanho VM comparável necessário para executar uma carga de trabalho do computador principal em Azure, fator na classificação MIPS (ou MSU).

Seguem-se as estimativas gerais:

-   150 MIPS por vCPU

-   1.000 MIPS por processador

Para determinar o tamanho VM correto para uma determinada carga de trabalho num LPAR, primeiro otimize o VM para a carga de trabalho. Em seguida, determine o número de vCPUs necessário. Uma estimativa conservadora é de 150 MIPS por vCPU. Com base nesta estimativa, por exemplo, um VM da série F com 16 vCPUs poderia facilmente suportar uma carga de trabalho IBM Db2 proveniente de um LPAR com 2.400 MIPS.

## <a name="azure-compute-scale-up"></a>Escala de computação Azure

Os VMs da série M podem escalar até 128 vCPUs (no momento em que este artigo foi escrito). Utilizando a estimativa conservadora de 150 MIPS por vCPU, o VM da série M equivale a cerca de 19.000 MIPS. A regra geral para estimar o MIPS para um computador principal é de 1.000 MIPS por processador. Um computador central z14 pode ter até 24 processadores e fornecer cerca de 24.000 MIPS para um único sistema de computador principal.

O maior computador central do Z14 tem aproximadamente 5.000 MIPS a mais do que o maior VM disponível em Azure. No entanto, é importante comparar como as cargas de trabalho são implementadas. Se um sistema de computador central tiver uma aplicação e uma base de dados relacional, são normalmente implantados no mesmo computador principal físico - cada um no seu próprio LPAR. A mesma solução em Azure é frequentemente implementada usando um VM para a aplicação e um VM separado e adequadamente dimensionado para a base de dados.

Por exemplo, se um sistema M64 vCPU suporta a aplicação, e um M96 vCPU é usado para a base de dados, são necessários aproximadamente 150 vCPUs - ou cerca de 24.000 MIPS como mostra o seguinte número.

![Comparando as colocações de carga de trabalho de 24.000 MIPS](media/mainframe-compute-mips.png)

A abordagem é migrar LPARs para VMs individuais. Em seguida, o Azure facilmente escala até o tamanho necessário para a maioria das aplicações que são implantadas num único sistema de computador principal.

## <a name="azure-compute-scale-out"></a>Escala de cálculo Azure

Uma das vantagens de uma solução baseada no Azure é a capacidade de escalar. O dimensionamento disponibiliza capacidade de computação quase ilimitada a uma aplicação. O Azure suporta vários métodos para reduzir a potência de computação:

- **Carga equilibrando-se através de um aglomerado.** Neste cenário, uma aplicação pode usar um [equilibrador](../../../../load-balancer/load-balancer-overview.md) de carga ou gestor de recursos para espalhar a carga de trabalho entre vários VMs num cluster. Se for necessária mais capacidade de cálculo, adicionam-se VM adicionais ao cluster.

- **Conjuntos de escala de máquina virtual.** Neste cenário de explosão, uma aplicação pode escalar para recursos adicionais [de computação](../../../../virtual-machine-scale-sets/overview.md) com base no uso de VM. Quando a procura diminui, o número de VMs num conjunto de escala também pode diminuir, garantindo uma utilização eficiente da potência de computação.

- **Escala paaS.** Azure PaaS oferece recursos de computação em escala. Por exemplo, [a Azure Service Fabric](../../../../service-fabric/service-fabric-overview.md) aloca recursos de computação para responder a aumentos no volume de pedidos.

- **Aglomerados de Kubernetes.** As aplicações no Azure podem utilizar [clusters Kubernetes](../../../../aks/concepts-clusters-workloads.md) para serviços de computação para recursos especificados. Azure Kubernetes Service (AKS) é um serviço gerido que orquestra nós, piscinas e clusters de Kubernetes em Azure.

Para escolher o método certo para escalonar os recursos computacional, é importante entender como o Azure e os mainframes diferem. A chave é como - ou se - os dados são partilhados por recursos compute. Em Azure, os dados (por padrão) não são normalmente partilhados por vários VMs. Se a partilha de dados for exigida por vários VMs num cluster de computação em escala, os dados partilhados devem residir num recurso que suporte esta funcionalidade. No Azure, a partilha de dados envolve armazenamento à medida que a secção seguinte discute.

## <a name="azure-compute-optimization"></a>Otimização do cálculo Azure

Pode otimizar cada nível de processamento numa arquitetura Azure. Utilize o tipo de VMs e funcionalidades mais adequados para cada ambiente. O número a seguir mostra um padrão potencial para a implantação de VMs em Azure para suportar uma aplicação CICS que utiliza Db2. No local primário, a produção, pré-produção e teste de VMs são implantados com elevada disponibilidade. O local secundário é para apoio e recuperação de desastres.

Cada nível também pode fornecer serviços adequados de recuperação de desastres. Por exemplo, os VM de produção e de base de dados podem necessitar de uma recuperação quente ou quente, enquanto o desenvolvimento e o teste de VMs suportam uma recuperação fria.

![Implantação altamente disponível que suporta a recuperação de desastres](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>Passos seguintes

- [Migração de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Rehosagem de mainframe em Máquinas Virtuais Azure](../overview.md)
- [Mover o armazenamento do computador principal para Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>Recursos IBM

- [Sysplex Paralelo na IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS e o Mecanismo de Acoplamento: Além do Básico](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Criar utilizadores necessários para uma instalação Db2 pureScale Feature](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - Criar comando de instância](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Solução de base de dados clustered pureScale Db2](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [Estúdio de Dados IBM](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Nuvem do Governo do Microsoft Azure para aplicações mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft e FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Mais recursos migratórios

- [Guia de elevador e turno do centro de dados virtual Azure](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://glusterdocs.readthedocs.io/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
