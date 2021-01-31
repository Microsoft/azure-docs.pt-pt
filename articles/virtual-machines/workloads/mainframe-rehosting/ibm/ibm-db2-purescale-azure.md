---
title: IBM DB2 pureScale em Azure
description: Neste artigo, mostramos uma arquitetura para executar um ambiente IBM DB2 pureScale em Azure.
author: njray
manager: edprice
editor: edprice
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: 711c1ba49ad0f347d30f2c8c40352ed95c1fd057
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221736"
---
# <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale em Azure

O ambiente IBM DB2 pureScale fornece um cluster de base de dados para a Azure com elevada disponibilidade e escalabilidade nos sistemas operativos Linux. Este artigo mostra uma arquitetura para executar DB2 pureScale on Azure.

## <a name="overview"></a>Descrição Geral

Há muito que as empresas utilizam plataformas tradicionais de gestão de bases de dados relacionais (RDBMS) para atender às suas necessidades de processamento de transações online (OLTP). Hoje em dia, muitos estão a migrar os seus ambientes de base de dados baseados em computadores principais para Azure como forma de expandir a capacidade, reduzir custos e manter uma estrutura de custos operacional estável. A migração é muitas vezes o primeiro passo na modernização de uma plataforma legado. 

Recentemente, um cliente da empresa reocupou o seu ambiente IBM DB2 em execução em z/OS para a IBM DB2 pureScale on Azure. A solução de cluster de base de dados Db2 pureScale proporciona elevada disponibilidade e escalabilidade nos sistemas operativos Linux. O cliente executou o Db2 com sucesso como um caso autónomo de escala numa única máquina virtual (VM) num sistema de grande escala em Azure antes de instalar a Db2 pureScale. 

Embora não seja idêntico ao ambiente original, o IBM DB2 pureScale no Linux oferece características de alta disponibilidade e escalabilidade semelhantes às da IBM DB2 para z/SO em execução numa configuração Sysplex Paralela no computador principal. Neste cenário, o cluster é conectado via iSCSI a um cluster de armazenamento partilhado. Utilizamos o sistema de ficheiros GlusterFS, um sistema de ficheiros distribuídos de código aberto gratuito, escalável e aberto, especificamente otimizado para armazenamento em nuvem. No entanto, a IBM já não apoia esta solução. Para manter o seu apoio da IBM, precisa de utilizar um sistema de ficheiros compatíveis com iSCSI suportado. Microsoft oferece espaços de armazenamento direto (S2D) como opção

Este artigo descreve a arquitetura usada para esta migração Azure. O cliente usou o Red Hat Linux 7.4 para testar a configuração. Esta versão está disponível no Azure Marketplace. Antes de escolher uma distribuição Linux, certifique-se de verificar as versões atualmente suportadas. Para mais detalhes, consulte a documentação para [IBM DB2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) e [GlusterFS](https://docs.gluster.org/en/latest/).

Este artigo é um ponto de partida para o seu plano de implementação DB2. Os requisitos do seu negócio serão diferentes, mas o mesmo padrão básico se aplica. Você também pode usar este padrão arquitetônico para aplicações de processamento analítico on-line (OLAP) em Azure.

Este artigo não cobre diferenças e possíveis tarefas de migração para mover uma base de dados IBM DB2 para z/OS para a IBM DB2 pureScale em funcionamento no Linux. E não fornece estimativas de dimensionamento e análises de carga de trabalho para passar de DB2 z/OS para DB2 pureScale. 

Para ajudá-lo a decidir sobre a melhor arquitetura DB2 pureScale para o seu ambiente, recomendamos que você especifique completamente o tamanho e faça uma hipótese. No sistema de origem, certifique-se de considerar DB2 z/OS Parallel Sysplex com arquitetura de partilha de dados, configuração de instalação de engate e estatísticas de utilização de instalações de dados distribuídas (DDF).

> [!NOTE]
> Este artigo descreve uma abordagem à migração de DB2, mas há outros. Por exemplo, dB2 pureScale também pode funcionar em ambientes virtualizados no local. A IBM suporta o DB2 no Microsoft Hyper-V em várias configurações. Para mais informações, consulte [a arquitetura de virtualização pureScale DB2](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) no Centro de Conhecimento IBM.

## <a name="architecture"></a>Arquitetura

Para suportar a alta disponibilidade e escalabilidade em Azure, você pode usar uma arquitetura de dados partilhada e de escala para DB2 pureScale. A migração do cliente usou o seguinte exemplo de arquitetura.

![DB2 pureScale em máquinas virtuais Azure mostrando armazenamento e networking](media/pureScaleArchitecture.png "DB2 pureScale em máquinas virtuais Azure mostrando armazenamento e networking")


O diagrama mostra as camadas lógicas necessárias para um cluster DB2 pureScale. Estes incluem máquinas virtuais para um cliente, para gestão, para caching, para o motor de base de dados, e para armazenamento compartilhado. 

Além dos nóns do motor da base de dados, o diagrama inclui dois nóns utilizados para instalações de caching de cluster (CF). Um mínimo de dois nós são usados para o próprio motor de base de dados. Um servidor DB2 que pertence a um cluster pureScale é chamado de membro. 

O cluster é conectado via iSCSI a um cluster de armazenamento compartilhado de três nós para fornecer armazenamento de escala e alta disponibilidade. DB2 pureScale está instalado em máquinas virtuais Azure que executam o Linux.

Esta abordagem é um modelo que pode modificar para o tamanho e escala da sua organização. Baseia-se no seguinte:

-   Dois ou mais membros da base de dados são combinados com pelo menos dois nós CF. Os nós gerem um pool de tampão global (GBP) para serviços de memória partilhada e gestor de bloqueio global (GLM) para controlar o acesso partilhado e travar a contenção de membros ativos. Um nó CF atua como o principal e o outro como o nó de CF secundário e desfalecido. Para evitar um único ponto de falha no ambiente, um cluster DB2 pureScale requer pelo menos quatro nós.

-   Armazenamento partilhado de alto desempenho (mostrado no tamanho P30 no diagrama). Cada nó usa este armazenamento.

-   Networking de alto desempenho para os membros de dados e armazenamento partilhado.

### <a name="compute-considerations"></a>Considerações de computação

Esta arquitetura executa os níveis de aplicação, armazenamento e dados em máquinas virtuais Azure. Os [scripts de configuração de implementação](https://aka.ms/db2onazure) criam o seguinte:

-   Um aglomerado DB2 pureScale. O tipo de recursos de computação que precisa no Azure depende da sua configuração. Em geral, pode utilizar duas abordagens:

    -   Utilize uma rede de computação multi-nólada e de alto desempenho (HPC) onde as pequenas e médias instâncias acedem ao armazenamento partilhado. Para este tipo de configuração HPC, as máquinas virtuais da série E otimizadas pela memória Azure ou [as máquinas virtuais](../../../sizes.md) da série L otimizadas para armazenamento fornecem a potência de computação necessária.

    -   Utilize menos grandes casos de máquinas virtuais para os motores de dados. Em grandes casos, as maiores máquinas virtuais da [série M](https://azure.microsoft.com/pricing/details/virtual-machines/series/) otimizadas pela memória são ideais para cargas de trabalho pesadas na memória. Você pode precisar de um caso dedicado, dependendo do tamanho da partição lógica (LPAR) que é usada para executar DB2.

-   O DB2 CF utiliza máquinas virtuais otimizadas pela memória, como série E ou sérieS L.

-   Um cluster de armazenamento partilhado que usa \_ máquinas virtuais Standard DS4 \_ v2 que executam o Linux.

-   O jumpbox de gestão é uma \_ máquina virtual Standard DS2 \_ v2 que funciona linux.  Uma alternativa é a Azure Bastion, um serviço que proporciona uma experiência secure RDP/SSH para todos os VMs da sua rede virtual.

-   O cliente é uma \_ máquina virtual Standard DS3 \_ v2 que executa o Windows (utilizado para testes).

-   *Opcional.* Um servidor de testemunhas. Isto só é necessário com certas versões anteriores da Db2 pureScale. Este exemplo utiliza uma \_ máquina virtual Standard DS3 \_ v2 que funciona linux (utilizada para dB2 pureScale).

> [!NOTE]
> Um cluster DB2 pureScale requer pelo menos duas instâncias DB2. Também requer uma instância de cache e uma instância de gestor de fechadura.

### <a name="storage-considerations"></a>Considerações sobre armazenamento

Tal como o Oracle RAC, dB2 pureScale é uma base de dados de alta performance do bloco de alto desempenho, de escala. Recomendamos a utilização da maior opção [SSD premium Azure](../../../disks-types.md) que se adequa às suas necessidades. Opções de armazenamento mais pequenas podem ser adequadas para ambientes de desenvolvimento e teste, enquanto os ambientes de produção muitas vezes precisam de mais capacidade de armazenamento. O exemplo da arquitetura usa [P30](https://azure.microsoft.com/pricing/details/managed-disks/) devido à sua relação de IOPS com o tamanho e preço. Independentemente do tamanho, utilize o Premium Storage para melhor desempenho.

DB2 pureScale usa uma arquitetura de tudo partilhado, onde todos os dados são acessíveis a partir de todos os nós de cluster. O armazenamento premium deve ser partilhado em vários casos, seja a pedido ou em casos dedicados.

Um grande cluster DB2 pureScale pode requerer 200 terabytes (TB) ou mais de armazenamento partilhado premium, com IOPS de 100.000. DB2 pureScale suporta uma interface de bloco iSCSI que pode utilizar no Azure. A interface iSCSI requer um cluster de armazenamento partilhado que pode implementar com S2D ou outra ferramenta. Este tipo de solução cria um dispositivo de rede de área de armazenamento virtual (vSAN) em Azure. DB2 pureScale usa o vSAN para instalar o sistema de ficheiros agrupado que é usado para partilhar dados entre máquinas virtuais.

### <a name="networking-considerations"></a>Considerações de redes

A IBM recomenda a rede InfiniBand para todos os membros num cluster DB2 pureScale. DB2 pureScale também utiliza acesso remoto de memória direta (RDMA), sempre que disponível, para os CFs.

Durante a configuração, cria um [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) Azure para conter todas as máquinas virtuais. Em geral, agrupar recursos baseados na sua vida e quem os vai gerir. As máquinas virtuais desta arquitetura requerem [uma rede acelerada.](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) É uma funcionalidade Azure que fornece latência de rede consistente e ultra-baixa através da virtualização de I/O de raiz única (SR-IOV) a uma máquina virtual.

Todas as máquinas virtuais Azure são implantadas numa rede virtual que tem sub-redes: principal, Gluster FS front end (gfsfe), Gluster FS back end (bfsbe), DB2 pureScale (db2be) e DB2 pureScale front end (db2fe). O script de instalação também cria os [NICs primários](https://docs.microsoft.com/azure/virtual-machines/windows/multiple-nics) nas máquinas virtuais na sub-rede principal.

Utilize [grupos de segurança](../../../../virtual-network/virtual-network-vnet-plan-design-arm.md) de rede para restringir o tráfego de rede dentro da rede virtual e isolar as sub-redes.

No Azure, a DB2 pureScale precisa de utilizar o TCP/IP como ligação de rede para armazenamento.

## <a name="next-steps"></a>Passos seguintes

-   [Implemente esta arquitetura em Azure](deploy-ibm-db2-purescale-azure.md)