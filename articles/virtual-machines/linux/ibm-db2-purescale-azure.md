---
title: IBM DB2 pureScale on Azure
description: Neste artigo, mostramos uma arquitetura para gerir um ambiente IBM DB2 pureScale em Azure.
author: njray
manager: edprice
editor: edprice
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: d8309a69c9c38610fa7bea3fee202a60d836980c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945061"
---
# <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale on Azure

O ambiente IBM DB2 pureScale fornece um cluster de base de dados para o Azure com elevada disponibilidade e escalabilidade nos sistemas operativos Linux. Este artigo mostra uma arquitetura para executar DB2 pureScale em Azure.

## <a name="overview"></a>Descrição geral

Há muito que as empresas têm usado plataformas tradicionais de gestão de bases de dados relacionais (RDBMS) para atender às suas necessidades de processamento de transações online (OLTP). Hoje em dia, muitos estão a migrar os seus ambientes de base de dados baseados no mainframe para o Azure como forma de expandir a capacidade, reduzir custos e manter uma estrutura de custos operacionais constante. A migração é muitas vezes o primeiro passo na modernização de uma plataforma antiga. 

Recentemente, um cliente empresarial reacolou o seu ambiente IBM DB2 em z/OS para ibm DB2 pureScale on Azure. A solução de cluster de base de dados Db2 pureScale proporciona alta disponibilidade e escalabilidade nos sistemas operativos Linux. O cliente executou o Db2 com sucesso como uma instância autónoma e de escala numa única máquina virtual (VM) num sistema de grande escala no Azure antes de instalar o Db2 pureScale. 

Embora não seja idêntico ao ambiente original, o IBM DB2 pureScale em Linux oferece características de alta disponibilidade e escalabilidade semelhantes como o IBM DB2 para z/OS que corre numa configuração Paralela sysplex no computador principal. Neste cenário, o cluster está ligado via iSCSI a um cluster de armazenamento partilhado. Usamos o sistema de ficheiros GlusterFS, um sistema de ficheiros distribuídos gratuitamente, escalável e aberto especificamente otimizado para armazenamento em nuvem. No entanto, a IBM já não apoia esta solução. Para manter o seu suporte da IBM, precisa de utilizar um sistema de ficheiros compatível com iSCSI. Microsoft oferece Espaços de Armazenamento Direto (S2D) como opção

Este artigo descreve a arquitetura usada para esta migração Azure. O cliente usou o Red Hat Linux 7.4 para testar a configuração. Esta versão está disponível no Azure Marketplace. Antes de escolher uma distribuição Linux, certifique-se de verificar as versões suportadas atualmente. Para mais detalhes, consulte a documentação para [IBM DB2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) e [GlusterFS](https://docs.gluster.org/en/latest/).

Este artigo é um ponto de partida para o seu plano de implementação DB2. Os seus requisitos de negócio serão diferentes, mas o mesmo padrão básico aplica-se. Também pode utilizar este padrão arquitetónico para aplicações de processamento analítico online (OLAP) no Azure.

Este artigo não cobre diferenças e possíveis tarefas de migração para mover uma base de dados IBM DB2 para z/OS para ibm DB2 pureScale em execução no Linux. E não fornece estimativas de dimensionamento e análises de carga de trabalho para passar de DB2 z/OS para DB2 pureScale. 

Para ajudá-lo a decidir sobre a melhor arquitetura DB2 pureScale para o seu ambiente, recomendamos que você calcule totalmente o tamanho e faça uma hipótese. No sistema de origem, certifique-se de considerar DB2 z/OS Parallel Sysplex com arquitetura de partilha de dados, configuração do Acoplamento facility e estatísticas de utilização de instalações de dados distribuídas (DDF).

> [!NOTE]
> Este artigo descreve uma abordagem à migração de DB2, mas há outras. Por exemplo, o DB2 pureScale também pode funcionar em ambientes virtualizados no local. A IBM suporta db2 no Microsoft Hyper-V em várias configurações. Para mais informações, consulte a arquitetura de [virtualização DB2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) no IBM Knowledge Center.

## <a name="architecture"></a>Arquitetura

Para suportar alta disponibilidade e escalabilidade no Azure, pode utilizar uma arquitetura de dados partilhada em escala para db2 pureScale. A migração do cliente usou a arquitetura exemplo seguinte.

![DB2 pureScale em máquinas virtuais Azure mostrando armazenamento e networking](media/db2-purescale-on-azure/pureScaleArchitecture.png "DB2 pureScale em máquinas virtuais Azure mostrando armazenamento e networking")


O diagrama mostra as camadas lógicas necessárias para um cluster DB2 pureScale. Estas incluem máquinas virtuais para um cliente, para gestão, para cache, para o motor de base de dados, e para armazenamento partilhado. 

Além dos nós do motor da base de dados, o diagrama inclui dois nós utilizados para instalações de cache de cluster (CFs). Um mínimo de dois nódosos são usados para o próprio motor de base de dados. Um servidor DB2 que pertence a um cluster pureScale é chamado de membro. 

O cluster está ligado via iSCSI a um cluster de armazenamento partilhado de três nós para fornecer armazenamento em escala e alta disponibilidade. DB2 pureScale está instalado em máquinas virtuais Azure que executam o Linux.

Esta abordagem é um modelo que pode modificar para o tamanho e escala da sua organização. Baseia-se no seguinte:

-   Dois ou mais membros da base de dados são combinados com pelo menos dois nós CF. Os nós gerem um pool tampão global (GBP) para serviços de memória partilhada e gestor de bloqueio global (GLM) para controlar o acesso partilhado e travar a contenção de membros ativos. Um nó CF atua como o principal e o outro como o nó cf secundário e falhado. Para evitar um único ponto de falha no ambiente, um cluster DB2 pureScale requer pelo menos quatro nós.

-   Armazenamento partilhado de alto desempenho (mostrado no tamanho P30 no diagrama). Cada nó usa este armazenamento.

-   Networking de alto desempenho para os membros de dados e armazenamento partilhado.

### <a name="compute-considerations"></a>Considerações computacionais

Esta arquitetura executa os níveis de aplicação, armazenamento e dados em máquinas virtuais Azure. Os scripts de configuração de [implementação](https://aka.ms/db2onazure) criam o seguinte:

-   Um cluster DB2 pureScale. O tipo de recursos computacionais de que precisa do Azure depende da sua configuração. Em geral, pode usar duas abordagens:

    -   Utilize uma rede de computação multi-nó, de alto desempenho (HPC), onde casos de pequena si acedem ao armazenamento partilhado. Para este tipo de configuração HPC, as máquinas virtuais otimizadas pela memória Azure ou as [máquinas virtuais](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) otimizadas pela série L fornecem a potência de computação necessária.

    -   Utilize menos grandes instâncias de máquinas virtuais para os motores de dados. Para grandes instâncias, as maiores máquinas virtuais da [série M](https://azure.microsoft.com/pricing/details/virtual-machines/series/) otimizadas para memória são ideais para cargas de trabalho pesadas na memória. Você pode precisar de um exemplo dedicado, dependendo do tamanho da partição lógica (LPAR) que é usado para executar DB2.

-   O DB2 CF utiliza máquinas virtuais otimizadas pela memória, tais como séries E ou série S.

-   Um cluster de armazenamento\_partilhado\_que utiliza máquinas virtuais Standard DS4 v2 que executam o Linux.

-   A caixa de salto\_de\_gestão é uma máquina virtual Standard DS2 v2 que executa o Linux.  Uma alternativa é a Azure Bastion, um serviço que fornece uma experiência segura de RDP/SSH para todos os VMs da sua rede virtual.

-   O cliente é\_uma\_máquina virtual Standard DS3 v2 que executa o Windows (utilizada para testar).

-   *Opcional.* Um servidor de testemunhas. Isto só é necessário com certas versões anteriores do Db2 pureScale. Este exemplo utiliza\_uma\_máquina virtual Standard DS3 v2 que executa o Linux (utilizado para db2 pureScale).

> [!NOTE]
> Um cluster DB2 pureScale requer pelo menos duas instâncias DB2. Também requer uma instância de cache e uma instância de lock manager.

### <a name="storage-considerations"></a>Considerações sobre armazenamento

Tal como o Oracle RAC, o DB2 pureScale é um bloco de alta performance I/O, base de dados scale-out. Recomendamos a utilização da maior opção [SSD premium Azure](disks-types.md) que se adequa às suas necessidades. As opções de armazenamento mais pequenas podem ser adequadas para ambientes de desenvolvimento e teste, enquanto os ambientes de produção muitas vezes precisam de mais capacidade de armazenamento. O exemplo da arquitetura usa [P30](https://azure.microsoft.com/pricing/details/managed-disks/) devido à sua relação entre IOPS e tamanho e preço. Independentemente do tamanho, utilize o Armazenamento Premium para melhor desempenho.

DB2 pureScale usa uma arquitetura de tudo partilhado, onde todos os dados são acessíveis a partir de todos os nós de cluster. O armazenamento premium deve ser partilhado em várias instâncias, seja a pedido ou em instâncias dedicadas.

Um grande cluster DB2 pureScale pode exigir 200 terabytes (TB) ou mais de armazenamento partilhado premium, com IOPS de 100.000. Db2 pureScale suporta uma interface de bloco iSCSI que pode utilizar no Azure. A interface iSCSI requer um cluster de armazenamento partilhado que pode implementar com S2D ou outra ferramenta. Este tipo de solução cria um dispositivo de rede de área de armazenamento virtual (vSAN) em Azure. Db2 pureScale usa o vSAN para instalar o sistema de ficheiros agrupado que é usado para partilhar dados entre máquinas virtuais.

### <a name="networking-considerations"></a>Considerações de redes

A IBM recomenda a rede InfiniBand para todos os membros num cluster DB2 pureScale. Db2 pureScale também utiliza acesso remoto à memória direta (RDMA), sempre que disponível, para os CFs.

Durante a configuração, cria-se um grupo de [recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) Azure para conter todas as máquinas virtuais. Em geral, agrupamos recursos com base na sua vida e quem os gerirá. As máquinas virtuais desta arquitetura requerem [uma ligação acelerada em rede.](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) É uma funcionalidade Azure que proporciona latência consistente e ultra-baixa da rede através da virtualização de I/O (SR-IOV) de raiz única para uma máquina virtual.

Todas as máquinas virtuais Azure são implantadas numa rede virtual que tem subredes: principal, extremidade frontal Gluster FS (gfsfe), extremidade traseira Gluster FS (bfsbe), DB2 pureScale (db2be) e extremidade frontal pureScale DB2 (db2fe). O script de instalação também cria os [NICs primários](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics) nas máquinas virtuais na subnet principal.

Utilize [grupos](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) de segurança de rede para restringir o tráfego de rede dentro da rede virtual e isolar as subredes.

No Azure, o DB2 pureScale precisa de utilizar o TCP/IP como ligação de rede para armazenamento.

## <a name="next-steps"></a>Passos seguintes

-   [Desloque esta arquitetura em Azure](deploy-ibm-db2-purescale-azure.md)
