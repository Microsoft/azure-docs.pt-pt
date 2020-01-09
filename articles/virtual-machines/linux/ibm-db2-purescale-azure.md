---
title: IBM DB2 pureScale no Azure
description: Neste artigo, mostramos uma arquitetura para executar um ambiente IBM DB2 pureScale no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: 4012048100bbed2229c45434ee4a27dfe9b952e7
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530085"
---
# <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale no Azure

O ambiente IBM DB2 pureScale fornece um cluster de banco de dados para o Azure com alta disponibilidade e escalabilidade em sistemas operacionais Linux. Este artigo mostra uma arquitetura para executar o DB2 pureScale no Azure.

## <a name="overview"></a>Visão geral

As empresas têm muito usado as plataformas de RDBMS (sistema de gerenciamento de banco de dados relacional) tradicionais para atender às suas necessidades de OLTP (processamento de transações online). Hoje em dia, muitos estão migrando seus ambientes de banco de dados baseados em mainframe para o Azure como uma maneira de expandir a capacidade, reduzir custos e manter uma estrutura de custo operacional estável. A migração é geralmente a primeira etapa na modernização de uma plataforma herdada. 

Recentemente, um cliente corporativo renovou seu ambiente IBM DB2 em execução em z/OS para IBM DB2 pureScale no Azure. A solução de cluster de banco de dados DB2 pureScale fornece alta disponibilidade e escalabilidade em sistemas operacionais Linux. O cliente executou o DB2 com êxito como uma instância autônoma de expansão em uma única VM (máquina virtual) em um sistema de grande escala no Azure antes da instalação do DB2 pureScale. 

Embora não seja idêntico ao ambiente original, o IBM DB2 pureScale no Linux oferece recursos semelhantes de alta disponibilidade e escalabilidade, como IBM DB2 para z/OS em execução em uma configuração de sysplex paralelo no mainframe. Nesse cenário, o cluster é conectado via iSCSI a um cluster de armazenamento compartilhado. Usamos o sistema de arquivos GlusterFS, um sistema de arquivos distribuído de código aberto gratuito e escalonável, especificamente otimizado para armazenamento em nuvem. No entanto, a IBM não dá mais suporte a essa solução. Para manter o suporte da IBM, você precisa usar um sistema de arquivos compatível com iSCSI com suporte. A Microsoft oferece Espaços de Armazenamento Diretos (S2D) como uma opção

Este artigo descreve a arquitetura usada para esta migração do Azure. O cliente usou o Red Hat Linux 7,4 para testar a configuração. Esta versão está disponível no Azure Marketplace. Antes de escolher uma distribuição do Linux, certifique-se de verificar as versões com suporte no momento. Para obter detalhes, consulte a documentação do [IBM DB2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) e [GlusterFS](https://docs.gluster.org/en/latest/).

Este artigo é um ponto de partida para seu plano de implementação do DB2. Seus requisitos de negócios serão diferentes, mas o mesmo padrão básico se aplicará. Você também pode usar esse padrão de arquitetura para aplicativos OLAP (processamento analítico online) no Azure.

Este artigo não aborda as diferenças e as possíveis tarefas de migração para mover um banco de dados IBM DB2 para z/OS para o IBM DB2 pureScale em execução no Linux. E não fornece estimativas de dimensionamento e análise de carga de trabalho para mudar do DB2 z/OS para o DB2 pureScale. 

Para ajudá-lo a decidir sobre a melhor arquitetura do DB2 pureScale para seu ambiente, recomendamos que você calcule totalmente o dimensionamento e tome uma hipótese. No sistema de origem, certifique-se de considerar o sysplex paralelo do DB2 z/OS com arquitetura de compartilhamento de dados, acoplamento de configuração de instalação e estatísticas de uso do DDF (Distributed Data Facility).

> [!NOTE]
> Este artigo descreve uma abordagem para a migração do DB2, mas há outras. Por exemplo, o DB2 pureScale também pode ser executado em ambientes locais virtualizados. A IBM dá suporte ao DB2 no Microsoft Hyper-V em várias configurações. Para obter mais informações, consulte [arquitetura de virtualização do DB2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) no centro de conhecimento IBM.

## <a name="architecture"></a>Arquitetura

Para dar suporte à alta disponibilidade e escalabilidade no Azure, você pode usar uma arquitetura de dados compartilhados e de expansão para DB2 pureScale. A migração do cliente usou a arquitetura de exemplo a seguir.

![DB2 pureScale em máquinas virtuais do Azure mostrando armazenamento e rede](media/db2-purescale-on-azure/pureScaleArchitecture.png "DB2 pureScale em máquinas virtuais do Azure mostrando armazenamento e rede")


O diagrama mostra as camadas lógicas necessárias para um cluster DB2 pureScale. Isso inclui máquinas virtuais para um cliente, para gerenciamento, para caching, para o mecanismo de banco de dados e para armazenamento compartilhado. 

Além dos nós do mecanismo de banco de dados, o diagrama inclui dois nós usados para os recursos de cache de cluster (CFs). Um mínimo de dois nós é usado para o próprio mecanismo de banco de dados. Um servidor DB2 que pertence a um cluster pureScale é chamado de membro. 

O cluster é conectado via iSCSI a um cluster de armazenamento compartilhado de três nós para fornecer armazenamento de expansão e alta disponibilidade. O DB2 pureScale é instalado em máquinas virtuais do Azure que executam o Linux.

Essa abordagem é um modelo que você pode modificar para o tamanho e a escala da sua organização. Ele é baseado no seguinte:

-   Dois ou mais membros do banco de dados são combinados com pelo menos dois nós do CF. Os nós gerenciam um pool de buffers global (GBP) para a memória compartilhada e os serviços do Gerenciador de bloqueio global (GLM) para controlar o acesso compartilhado e bloquear a contenção de membros ativos. Um nó do CF atua como o primário e o outro como o nó do CF de failover secundário. Para evitar um ponto único de falha no ambiente, um cluster DB2 pureScale requer pelo menos quatro nós.

-   Armazenamento compartilhado de alto desempenho (mostrado em tamanho de p30 no diagrama). Cada nó usa esse armazenamento.

-   Rede de alto desempenho para os membros de dados e o armazenamento compartilhado.

### <a name="compute-considerations"></a>Considerações sobre computação

Essa arquitetura executa as camadas de aplicativo, armazenamento e dados em máquinas virtuais do Azure. Os [scripts de instalação de implantação](https://aka.ms/db2onazure) criam o seguinte:

-   Um cluster DB2 pureScale. O tipo de recursos de computação que você precisa no Azure depende da sua configuração. Em geral, você pode usar duas abordagens:

    -   Use uma rede de estilo HPC (computação de alto desempenho) de vários nós, em que instâncias de pequeno a médio porte acessam o armazenamento compartilhado. Para esse tipo de HPC de configuração, as [máquinas virtuais](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) da série L com otimização de memória do Azure ou do armazenamento otimizado para a computação fornecem o poder de computador necessário.

    -   Use menos instâncias de máquina virtual grandes para os mecanismos de dados. Para instâncias grandes, as maiores máquinas virtuais da [série M com](https://azure.microsoft.com/pricing/details/virtual-machines/series/) otimização de memória são ideais para cargas de trabalho pesadas na memória. Talvez você precise de uma instância dedicada, dependendo do tamanho da partição lógica (LPAR) usada para executar o DB2.

-   O DB2 CF usa máquinas virtuais com otimização de memória, como E-Series ou L-Series.

-   Um cluster de armazenamento compartilhado que usa máquinas virtuais DS4\_v2\_padrão que executam o Linux.

-   O Jumpbox de gerenciamento é uma máquina virtual\_DS2\_v2 padrão que executa o Linux.  Uma alternativa é a bastiões do Azure, um serviço que fornece uma experiência segura de RDP/SSH para todas as VMs em sua rede virtual.

-   O cliente é uma máquina virtual Standard\_DS3\_v2 que executa o Windows (usado para teste).

-   *Opcional*. Um servidor testemunha. Isso é necessário apenas com determinadas versões anteriores do DB2 pureScale. Este exemplo usa uma máquina virtual Standard\_DS3\_v2 executando o Linux (usado para DB2 pureScale).

> [!NOTE]
> Um cluster DB2 pureScale requer pelo menos duas instâncias do DB2. Ele também requer uma instância de cache e uma instância do Gerenciador de bloqueio.

### <a name="storage-considerations"></a>Considerações sobre armazenamento

Assim como o Oracle RAC, o DB2 pureScale é um banco de dados de escalabilidade horizontal de e/s de bloco de alto desempenho. É recomendável usar a maior opção de [SSD Premium do Azure](disks-types.md) que atenda às suas necessidades. Opções de armazenamento menores podem ser adequadas para ambientes de desenvolvimento e teste, enquanto ambientes de produção geralmente precisam de mais capacidade de armazenamento. A arquitetura de exemplo usa [p30](https://azure.microsoft.com/pricing/details/managed-disks/) devido à sua taxa de IOPS para tamanho e preço. Independentemente do tamanho, use o armazenamento Premium para obter o melhor desempenho.

O DB2 pureScale usa uma arquitetura de tudo compartilhado, onde todos os dados podem ser acessados de todos os nós de cluster. O armazenamento Premium deve ser compartilhado entre várias instâncias, seja sob demanda ou em instâncias dedicadas.

Um cluster pureScale DB2 grande pode exigir 200 terabytes (TB) ou mais de armazenamento compartilhado Premium, com IOPS de 100.000. O DB2 pureScale dá suporte a uma interface de bloco iSCSI que você pode usar no Azure. A interface iSCSI requer um cluster de armazenamento compartilhado que você pode implementar com o S2D ou outra ferramenta. Esse tipo de solução cria um dispositivo de rede de área de armazenamento virtual (vSAN) no Azure. O DB2 pureScale usa o vSAN para instalar o sistema de arquivos clusterizado que é usado para compartilhar dados entre máquinas virtuais.

### <a name="networking-considerations"></a>Considerações de redes

A IBM recomenda a rede InfiniBand para todos os membros em um cluster DB2 pureScale. O DB2 pureScale também usa o RDMA (acesso remoto direto à memória), quando disponível, para o CFs.

Durante a instalação, você cria um [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) do Azure para conter todas as máquinas virtuais. Em geral, você agrupa recursos com base no seu tempo de vida e quem os gerenciará. As máquinas virtuais nessa arquitetura exigem [rede acelerada](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). É um recurso do Azure que fornece latência de rede extremamente baixa e consistente por meio de SR-IOV (virtualização de e/s de raiz única) para uma máquina virtual.

Cada máquina virtual do Azure é implantada em uma rede virtual que tem sub-redes: Main, Gluster FS front end (gfsfe), Gluster FS back-end (bfsbe), DB2 pureScale (db2be) e DB2 pureScale front end (db2fe). O script de instalação também cria as [NICs](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics) primárias nas máquinas virtuais na sub-rede principal.

Use [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) para restringir o tráfego de rede na rede virtual e para isolar as sub-redes.

No Azure, o DB2 pureScale precisa usar TCP/IP como a conexão de rede para armazenamento.

## <a name="next-steps"></a>Passos seguintes

-   [Implantar essa arquitetura no Azure](deploy-ibm-db2-purescale-azure.md)
