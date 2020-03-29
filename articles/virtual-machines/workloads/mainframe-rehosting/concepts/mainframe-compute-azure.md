---
title: Mova a computação do mainframe para máquinas virtuais azure
description: Os recursos computacionais azure comparam favoravelmente à capacidade do mainframe para que possa migrar e modernizar as aplicações IBM z14.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 97f354d0a313d58c671366dd0e5f485504823e13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76288936"
---
# <a name="move-mainframe-compute-to-azure"></a>Mova a computação do mainframe para Azure

Os mainframes têm uma reputação de alta fiabilidade e disponibilidade e continuam a ser a espinha dorsal de muitas empresas. Pensa-se que têm uma escalabilidade quase ilimitada e poder de computação também. No entanto, algumas empresas ultrapassaram a capacidade dos maiores mainframes disponíveis. Se isto soa a si, o Azure oferece agilidade, alcance e poupança de infraestruturas.

Para executar cargas de trabalho no Microsoft Azure, precisa de saber como as capacidades computacionais do seu computador principal se comparam ao Azure. Baseado num mainframe IBM z14 (o modelo mais atual a partir desta escrita), este artigo diz-lhe como obter resultados comparáveis no Azure.

Para começar, considere os ambientes lado a lado. O valor seguinte compara um ambiente mainframe para executar aplicações a um ambiente de hospedagem Azure.

![Serviços azure e ambientes de emulação oferecem apoio comparável e dinamizamigração](media/mainframe-compute-azure.png)

A potência dos principais quadros é frequentemente utilizada para sistemas de processamento de transações online (OLTP) que lidam com milhões de atualizações para milhares de utilizadores. Estas aplicações usam frequentemente software para processamento de transações, tratamento de ecrãs e entrada de formulários. Podem utilizar um Sistema de Controlo de Informações do Cliente (CICS), Sistema de Gestão de Informações (IMS) ou Pacote de Interface de Transações (TIP).

Como mostra o número, um emulador TPM em Azure pode lidar com cargas de trabalho cics e IMS. Um emulador do sistema de lote em Azure desempenha o papel de Linguagem de Controlo de Emprego (JCL). Os dados do mainframe são migrados para bases de dados Do Azure, como a Base de Dados Azure SQL. Os serviços Azure ou outro software hospedado em Máquinas Virtuais Azure podem ser utilizados para a gestão do sistema.

## <a name="mainframe-compute-at-a-glance"></a>Computação mainframe num ápice

No quadro principal do z14, os processadores são organizados em até quatro *gavetas.* Uma *gaveta* é simplesmente um conjunto de processadores e chipsets. Cada gaveta pode ter seis chips de processador central ativo (CP) e cada CP tem 10 chips de controlador de sistema (SC). Na terminologia Intel x86, existem seis tomadas por gaveta, 10 núcleos por tomada e quatro gavetas. Esta arquitetura fornece o equivalente áspero de 24 tomadas e 240 núcleos, no máximo, para um z14.

O fast z14 CP tem uma velocidade de 5,2 GHz. Tipicamente, um z14 é entregue com todos os CPs na caixa. São ativados quando necessário. Um cliente é geralmente cobrado por pelo menos quatro horas de tempo de computação por mês, apesar do uso real.

Um processador mainframe pode ser configurado como um dos seguintes tipos:

- Processador de Propósito Geral (GP)
- Processador de Informação Integrado Sistema z (zIIP)
- Facilidade Integrada para o processador Linux (IFL)
- Processador de assistência ao sistema (SAP)
- Processador Integrado de Acoplamento (ICF)

## <a name="scaling-mainframe-compute-up-and-out"></a>Dimensionamento da computação principal para cima e para fora

Os mainframes da IBM oferecem a capacidade de escalar até 240 núcleos (o tamanho z14 atual para um único sistema). Além disso, os principais quadros da IBM podem escalar através de uma funcionalidade chamada Acoplamento Facility (CF). O CF permite que vários sistemas mainframe acedam simultaneamente aos mesmos dados. Utilizando o CF, o mainframe Parallel Sysplex agrupa processadores mainframe em clusters. Quando este guia foi escrito, a funcionalidade Parallel Sysplex suportava 32 agrupamentos de 64 processadores cada. Até 2.048 processadores podem ser agrupados desta forma para aumentar a capacidade computacional.

Um CF permite que os clusters de computação partilhem dados com acesso direto. É usado para bloquear informações, informações de cache e a lista de recursos de dados partilhados. Um Sysplex paralelo usando um ou mais CFs pode ser considerado como um cluster de cálculo de escala "compartilhado". Para obter mais informações sobre estas [funcionalidades, consulte Parallel Sysplex no site](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) da IBM Z.

As aplicações podem usar estas funcionalidades para fornecer desempenho de escala e alta disponibilidade. Para obter informações sobre como o CICS pode usar o Parallel Sysplex com CF, descarregue o [IBM CICS e o Acoplamento Facilidade: Para além do](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) redbook básico.

## <a name="azure-compute-at-a-glance"></a>Computação azure num ápice

Algumas pessoas pensam erradamente que os servidores baseados na Intel não são tão poderosos como os computadores principais. No entanto, os novos sistemas core-densos e baseados na Intel têm tanta capacidade computacional como os principais quadros. Esta secção descreve as opções de infraestruturas azure -as-a-service (IaaS) para computação e armazenamento. O Azure também fornece opções de plataforma como um serviço (PaaS), mas este artigo foca-se nas escolhas IaaS que proporcionam uma capacidade de mainframe comparável.

As Máquinas Virtuais Azure fornecem potência computacional numa gama de tamanhos e tipos. Em Azure, um CPU virtual (vCPU) equivale aproximadamente a um núcleo num computador principal.

Atualmente, a gama de tamanhos da Máquina Virtual Azure fornece de 1 a 128 vCPUs. Os tipos de máquinas virtuais (VM) são otimizados para cargas de trabalho particulares. Por exemplo, a seguinte lista mostra os tipos vM (atual a partir desta escrita) e as suas utilizações recomendadas:

| Tamanho     | Tipo e descrição                                                                 |
|----------|--------------------------------------------------------------------------------------|
| Série D | Finalidade geral com 64 vCPU e até 3,5 GHz velocidade do relógio                           |
| Série E | Memória otimizada com até 64 vCPUs                                                 |
| Série F | Compute otimizado com até 64 vCPUs e velocidade de relógio de 3..7 GHz                       |
| Série H | Otimizado para aplicações de computação de alto desempenho (HPC)                          |
| Série L | Armazenamento otimizado para aplicações de alto-produção apoiadas por bases de dados como noSQL |
| Série M | Maior computação e memória otimizados VMs com até 128 vCPUs                        |

Para mais detalhes sobre vMs disponíveis, consulte [a série Virtual Machine](https://azure.microsoft.com/pricing/details/virtual-machines/series/).

Um quadro central z14 pode ter até 240 núcleos. No entanto, os mainframes z14 quase nunca usam todos os núcleos para uma única aplicação ou carga de trabalho. Em vez disso, um computador principal segrega cargas de trabalho em divisórias lógicas (LPARs), e os LPARs têm classificações — MIPS (Milhões de Instruções por Segundo) ou MSU (Unidade de Serviço milhão). Ao determinar o tamanho de VM comparável necessário para executar uma carga de trabalho de computador principal no Azure, fator na classificação MIPS (ou MSU).

Seguem-se as estimativas gerais:

-   150 MIPS por vCPU

-   1.000 MIPS por processador

Para determinar o tamanho vm correto para uma determinada carga de trabalho num LPAR, primeiro otimize o VM para a carga de trabalho. Em seguida, determine o número de vCPUs necessários. Uma estimativa conservadora é de 150 MIPS por vCPU. Com base nesta estimativa, por exemplo, um VM da série F com 16 vCPUs poderia facilmente suportar uma carga de trabalho IBM Db2 proveniente de um LPAR com 2.400 MIPS.

## <a name="azure-compute-scale-up"></a>Escala de computação azure

Os VMs da série M podem escalar até 128 vCPUs (no momento em que este artigo foi escrito). Utilizando a estimativa conservadora de 150 MIPS por vCPU, o VM da série M equivale a cerca de 19.000 MIPS. A regra geral para estimar o MIPS para um computador principal é de 1.000 MIPS por processador. Um mainframe z14 pode ter até 24 processadores e fornecer cerca de 24.000 MIPS para um único sistema mainframe.

O maior mainframe single z14 tem aproximadamente 5.000 MIPS mais do que o maior VM disponível em Azure. No entanto, é importante comparar como as cargas de trabalho são implantadas. Se um sistema de computador principal tem uma aplicação e uma base de dados relacional, são normalmente implantados no mesmo mainframe físico - cada um no seu próprio LPAR. A mesma solução em Azure é frequentemente implementada utilizando um VM para a aplicação e um VM separado e adequado para a base de dados.

Por exemplo, se um sistema M64 vCPU suporta a aplicação, e um VCPU M96 for utilizado para a base de dados, são necessários aproximadamente 150 vCPUs — ou cerca de 24.000 MIPS, como mostra o seguinte número.

![Comparando as implantações de carga de trabalho de 24.000 MIPS](media/mainframe-compute-mips.png)

A abordagem é migrar LPARs para VMs individuais. Em seguida, o Azure facilmente dimensiona até ao tamanho necessário para a maioria das aplicações que são implantadas num único sistema de computador principal.

## <a name="azure-compute-scale-out"></a>Escala de computação azure

Uma das vantagens de uma solução baseada no Azure é a capacidade de escalar. A escalação disponibiliza capacidade de computação quase ilimitada para uma aplicação. O Azure suporta múltiplos métodos para reduzir a potência da computação:

- **Carregar equilibrando através de um aglomerado.** Neste cenário, uma aplicação pode usar um [balanceor](/azure/load-balancer/load-balancer-overview) de carga ou gestor de recursos para espalhar a carga de trabalho entre vários VMs em um cluster. Se for necessária mais capacidade computacional, são adicionados VMs adicionais ao cluster.

- **Conjuntos de escala de máquina virtual.** Neste cenário de explosão, uma aplicação pode escalar para [recursos de computação](/azure/virtual-machine-scale-sets/overview) adicionais com base no uso de VM. Quando a procura diminui, o número de VMs num conjunto de escala também pode diminuir, garantindo uma utilização eficiente da potência computacional.

- **Escala de PaaS.** Azure PaaS oferece recursos de computação em escala. Por exemplo, a [Azure Service Fabric](/azure/service-fabric/service-fabric-overview) aloca recursos de cálculo para fazer face aos aumentos no volume de pedidos.

- **Aglomerados kubernetes.** As aplicações no Azure podem utilizar [clusters Kubernetes](/azure/aks/concepts-clusters-workloads) para serviços de computação para recursos especificados. Azure Kubernetes Service (AKS) é um serviço gerido que orquestra os nós, piscinas e clusters kubernetes em Azure.

Para escolher o método certo para escalonar os recursos da computação, é importante entender como o Azure e os mainframes diferem. A chave é como - ou se - os dados são partilhados por recursos computacionais. No Azure, os dados (por padrão) não são normalmente partilhados por vários VMs. Se a partilha de dados for exigida por vários VMs num cluster de cálculo sem escalas, os dados partilhados devem residir num recurso que suporte esta funcionalidade. No Azure, a partilha de dados envolve o armazenamento à medida que a secção seguinte discute.

## <a name="azure-compute-optimization"></a>Otimização do cálculo Azure

Pode otimizar cada nível de processamento numa arquitetura Azure. Utilize o tipo de VMs mais adequado e características para cada ambiente. A figura que se segue mostra um padrão potencial para a implementação de VMs em Azure para suportar uma aplicação CICS que utiliza db2. No local primário, a produção, pré-produção e teste de VMs são implantados com elevada disponibilidade. O local secundário é para apoio e recuperação de desastres.

Cada nível também pode fornecer serviços adequados de recuperação de desastres. Por exemplo, os VMs de produção e base de dados podem exigir uma recuperação quente ou quente, enquanto os VMs de desenvolvimento e teste suportam uma recuperação fria.

![Implantação altamente disponível que apoia a recuperação de desastres](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>Passos seguintes

- [Migração de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Realojamento do mainframe em Máquinas Virtuais Azure](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Mova o armazenamento do mainframe para Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>Recursos IBM

- [Sisplex paralelo na IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS e a Facilidade de Acoplamento: Para além do Básico](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Criação de utilizadores necessários para uma instalação de funcionalidade de pureScale Db2](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - Criar comando de instância](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Solução de base de dados agrupada de pureScale Db2](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [Estúdio de Dados IBM](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Nuvem do Governo do Microsoft Azure para aplicações mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft e FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Mais recursos migratórios

- [Guia de elevador e turno do Centro de Dados Virtuais Azure](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
