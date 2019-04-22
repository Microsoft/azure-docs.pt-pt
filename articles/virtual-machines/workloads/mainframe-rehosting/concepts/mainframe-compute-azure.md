---
title: Mover a computação de mainframe para máquinas de virtuais do Azure
description: Comparação de recursos de maneira favorável à capacidade de mainframe de computação no Azure para que possa migrar e modernizar aplicativos de z14 da IBM.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 8aea4a74ba84855f011dada70ea75ec0d5fb64fe
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58896520"
---
# <a name="move-mainframe-compute-to-azure"></a>Mover a computação de mainframe para o Azure

Mainframes tem uma reputação por elevada fiabilidade e disponibilidade e continuam a ser o backbone de confiança de muitas empresas. Eles muitas vezes estiverem definidos ter escalabilidade quase ilimitada e também a capacidade de computação. No entanto, algumas empresas têm outgrown a capacidade dos maior mainframes disponíveis. Se isso parece como, o Azure oferece economias de agilidade, alcance e infraestrutura.

Para executar cargas de trabalho de mainframe no Microsoft Azure, precisa saber como seu mainframe computação compare capacidades para o Azure. Com base num mainframe de z14 IBM (o modelo mais atual até ao presente momento), este artigo mostra-lhe como obter resultados comparáveis no Azure.

Para começar a utilizar, considere os ambientes de lado a lado. A figura a seguir compara um ambiente de mainframe para a execução de aplicações para um ambiente de alojamento do Azure.

![Serviços do Azure e uma oferta de ambientes de emulação comparável de suporte e simplifica a migração](media/mainframe-compute-azure.png)

O poder dos mainframes, muitas vezes, é utilizado para transações online (OLTP) sistemas que lidar com milhões de atualizações para milhares de usuários de processamento. Esses aplicativos, muitas vezes, utilizam o software para processamento de transações, manipulação de tela e entrada de formulário. Eles podem usar um sistema de controle de informações do cliente (CICS), o sistema de gerenciamento de informações (IMS) ou o pacote de Interface de transação (TIP).

Conforme mostrado na figura, um emulador do TPM no Azure pode processar cargas de trabalho CICS e IMS. Um emulador de sistema do batch no Azure executa a função de linguagem de controle de tarefa (JCL). Dados de mainframe são migrados para bases de dados do Azure, como a base de dados do Azure SQL. Serviços do Azure ou outro software alojadas em máquinas de virtuais do Azure pode ser utilizado para gestão do sistema.

## <a name="mainframe-compute-at-a-glance"></a>Computação de mainframe rapidamente

No z14 mainframe, os processadores são organizados em até quatro *gavetas de*. R *gaveta* é simplesmente um cluster de processadores e chipsets. Cada gaveta pode ter seis chips de processador central Active Directory (CP) e cada CP tem 10 chips de controlador (SC) do sistema. Na terminologia do Intel x86, existem seis soquetes por gaveta, 10 núcleos por socket e gavetas de quatro. Esta arquitetura fornece o equivalente aproximado de 24 sockets e 240 núcleos, máximo, para um z14.

O fast z14 CP tem uma velocidade de relógio 5.2 GHz. Normalmente, um z14 é entregue com todos os CPs na caixa. Eles estiverem ativados, conforme necessário. Um cliente normalmente é cobrado por, pelo menos, quatro horas de tempo de computação por mês, apesar da utilização real.

Um processador de mainframe pode ser configurado como um dos seguintes tipos:

- Processador de finalidade (GP) geral
- Processador de informações integrada (zIIP) do sistema z
- Instalação integrada para o processador de Linux (IFL)
- Processador de assistência de sistema (SAP)
- Processador de acoplamento instalações ICF () integrado

## <a name="scaling-mainframe-compute-up-and-out"></a>Computação de mainframe dimensionamento verticalmente e horizontalmente

IBM mainframes oferecem a capacidade de dimensionar até 240 núcleos (o tamanho z14 atual para um único sistema). Além disso, IBM mainframes, podem aumentar horizontalmente por meio de um recurso chamado o acoplamento instalações (CF). O CF permite que vários sistemas de mainframe acessar os mesmos dados em simultâneo. Usando o CF, os processadores de mainframe de grupos de tecnologia de Sysplex paralela do mainframe em clusters. Quando este guia foi escrito, a funcionalidade de Sysplex paralela suportada 32 agrupamentos de 64 processadores. Até 2.048 processadores pode ser agrupado dessa maneira para ampliar a capacidade de computação.

Um CF permite que os clusters de cálculo partilhar dados com acesso direto. É utilizado para o bloqueio de informações, informações de cache e a lista de recursos de dados partilhados. Um Sysplex paralela usando um ou mais CFs pode ser considerado um "partilhado tudo" cluster de cálculo de escalamento horizontal. Para obter mais informações sobre estas funcionalidades, consulte [Sysplex paralela no IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) no site da IBM.

Aplicativos podem usar esses recursos para fornecer desempenho de escalamento horizontal e elevada disponibilidade. Para obter mais informações sobre como CICS pode utilizar o Sysplex paralela com CF, transfira o [IBM CICS e o recurso de acoplamento: Além do básico](http://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) redbook.

## <a name="azure-compute-at-a-glance"></a>Computação do Azure rapidamente

Algumas pessoas acham por engano que servidores baseados em Intel não são tão poderosos quanto mainframes. No entanto, os novos sistemas de núcleo com muitas, baseado em Intel têm como muito a capacidade como mainframes de computação. Esta secção descreve as opções de (IaaS) de infraestrutura-como-serviço do Azure para computação e armazenamento. Azure fornece também a plataforma-como-serviço (PaaS) opções, mas este artigo incide sobre as opções de IaaS que fornecem a capacidade de mainframe comparável.

Máquinas virtuais do Azure fornecem o poder de computação numa variedade de tipos e tamanhos. No Azure, uma CPU virtual (Vcpus) equivale aproximadamente a um núcleo no seu próprio mainframe.

Atualmente, os tamanhos de intervalo de Máquina Virtual do Azure fornece a partir de 1 a 128 vCPUs. Tipos de máquina virtual (VM) são otimizados para cargas de trabalho específicas. Por exemplo, a lista seguinte mostra os tipos VM (atuais até ao presente momento) e respetivas utilizações recomendadas:

| Tamanho     | Tipo e uma descrição                                                                 |
|----------|--------------------------------------------------------------------------------------|
| Série D | Fins gerais com 64 Vcpus e até velocidade de relógio de 3.5 GHz                           |
| Série E | Memória otimizada com até 64 vCPUs                                                 |
| Série F | Computação otimizada com até 64 vCPUs e velocidade de clock 3..7 GHz                       |
| Série H | Otimizado para aplicações de computação de alto desempenho (HPC)                          |
| Série L | Armazenamento otimizado para aplicações de alto débito, apoiadas por bases de dados como NoSQL |
| Série M | Maior computação e memória otimizada VMs com até 128 vCPUs                        |

Para obter detalhes sobre as VMs disponíveis, consulte [séries de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/series/).

Um mainframe z14 pode ter até 240 núcleos. No entanto, z14 mainframes quase nunca usar todos os núcleos para um único aplicativo ou a carga de trabalho. Em vez disso, um mainframe segrega as cargas de trabalho em partições lógicas (LPARs) e as classificações de ter os LPARs — MIPS (milhões de instruções por segundo) ou MSU (milhões de serviço de unidade). Ao determinar o tamanho da VM comparável necessário para executar uma carga de trabalho de mainframe no Azure, classificação de fator na MIPS (ou MSU).

Seguem-se as estimativas de gerais:

-   150 MIPS por vCPU

-   1000 MIPS por processador

Para determinar o tamanho VM correto para uma determinada carga de trabalho num LPAR, primeiro Otimize a VM para a carga de trabalho. Em seguida, determine o número de vCPUs necessário. Uma estimativa conservador é 150 MIPS por vCPU. Com base nesta estimativa, por exemplo, uma VM de série F com 16 vCPUs poderia facilmente suportar uma carga de trabalho IBM Db2 proveniente de um LPAR com MIPS 2.400.

## <a name="azure-compute-scale-up"></a>Aumentar verticalmente de computação do Azure

As VMs de série M podem Dimensionar até 128 vCPUs (no momento que este artigo foi escrito). Utilizar a estimativa conservador de 150 MIPS por vCPU,-se a VM de série M equivale a cerca de 19,000 MIPS. A regra geral para estimar MIPS para um mainframe é MIPS 1000 por processador. Um mainframe z14 pode ter até 24 processadores e fornecem cerca de 24.000 MIPS para um sistema de mainframe único.

O mainframe z14 único maior tem aproximadamente 5.000 MIPS mais do que a VM maior disponível no Azure. Ainda é importante comparar a forma como as cargas de trabalho são implementadas. Se um sistema de mainframe tiver um aplicativo e uma base de dados relacional, normalmente estiver implementados no mesmo mainframe físico — cada um em seu próprio LPAR. A mesma solução no Azure é frequentemente implementada com uma VM para o aplicativo e uma VM separada, convenientemente tamanho da base de dados.

Por exemplo, se o aplicativo oferece suporte a um sistema de vCPU M64 e um vCPU M96 é utilizado para a base de dados, aproximadamente 150 vCPUs são necessários — ou cerca de 24.000 MIPS, como mostra a figura seguinte.

![Comparar implementações de carga de trabalho de 24.000 MIPS](media/mainframe-compute-mips.png)

A abordagem é migrar LPARs às VMs individuais. Em seguida, Azure facilmente se aumentar verticalmente para o tamanho necessário para a maioria dos aplicativos que estão implementadas num sistema de mainframe único.

## <a name="azure-compute-scale-out"></a>Escalamento de computação do Azure

Uma das vantagens de uma solução com base no Azure é a capacidade de aumentar horizontalmente. Capacidade disponível para uma aplicação de computação de torna dimensionamento quase ilimitada. O Azure suporta vários métodos para aumentar horizontalmente o poder de computação:

- **O balanceamento de carga num cluster.** Neste cenário, uma aplicação pode utilizar um [Balanceador de carga](/azure/load-balancer/load-balancer-overview) ou do resource manager para distribuir a carga de trabalho entre várias VMs num cluster. Se mais de computação seja necessária capacidade, VMs adicionais são adicionadas ao cluster.

- **Conjuntos de dimensionamento de máquina virtual.** Neste cenário de expansão, uma aplicação poder Dimensionar adicional [recursos de computação](/azure/virtual-machine-scale-sets/overview) com base na utilização VM. Quando atingir a pedido, o número de VMs num conjunto de dimensionamento também pode ser reduzidos, garantindo uma utilização eficiente da capacidade de computação.

- **PaaS dimensionamento.** Recursos de computação de dimensionamento de ofertas de PaaS do Azure. Por exemplo, [do Azure Service Fabric](/azure/service-fabric/service-fabric-overview) aloca recursos de computação para satisfazer o aumento do volume de pedidos.

- **Clusters do Kubernetes.** As aplicações no Azure podem utilizar [clusters do Kubernetes](/azure/aks/concepts-clusters-workloads) para serviços de computação para os recursos especificados. O Azure Kubernetes Service (AKS) é um serviço gerido que orquestra nós do Kubernetes, conjuntos e clusters no Azure.

Para escolher o método correto para dimensionar recursos de computação, é importante compreender como o Azure e mainframes diferem. A chave é como — ou se — os dados são partilhados por recursos de computação. No Azure, os dados (por predefinição) não é geralmente partilhados por várias VMs. Se a partilha de dados é exigido pelo cluster de cálculo de várias VMs no Escalamento horizontal, os dados partilhados tem de residir num recurso que oferece suporte a essa funcionalidade. No Azure, partilha de dados envolve armazenamento como a secção seguinte explica.

## <a name="azure-compute-optimization"></a>Otimização de computação do Azure

Pode otimizar a cada escalão de processamento numa arquitetura do Azure. Utilize o tipo mais adequado das VMs e recursos para cada ambiente. A figura seguinte mostra um padrão de potencial para a implementação de VMs no Azure para oferecer suporte a uma aplicação de CICS que utiliza o Db2. No site primário, produção, as VMs de pré-produção e de testes são implementadas com elevada disponibilidade. O site secundário destina-se a cópia de segurança e recuperação após desastre.

Cada camada também pode fornecer desastre adequado dos serviços de recuperação. Por exemplo, VMs de base de dados e de produção podem requerer uma recuperação de acesso frequente ou pouco frequente, enquanto o desenvolvimento e testes VMs suportam uma recuperação fria.

![Implementação de elevada disponibilidade que suporta a recuperação após desastre](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>Passos Seguintes

- [Migração de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Mainframe realojamento em máquinas de virtuais do Azure](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Mover o armazenamento de mainframe para o Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>Recursos da IBM

- [Sysplex paralela no IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS e o recurso de acoplamento: Além do básico](http://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Criação de usuários necessários para uma instalação da funcionalidade de pureScale de Db2](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - criar instância de comando](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale solução de banco de dados agrupadas](http://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Cloud do Microsoft Azure Government para aplicações de mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [A Microsoft e FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Mais recursos de migração

- [Alliance de modernização de plataforma: IBM Db2 no Azure](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)
- [Datacenter Virtual do Azure Lift- and -Shift guia](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
