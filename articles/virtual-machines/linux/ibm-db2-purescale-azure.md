---
title: IBM DB2 pureScale no Azure
description: Neste artigo, vamos mostrar uma arquitetura para a execução de um ambiente de pureScale IBM DB2 no Azure.
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
ms.devlang: na
ms.topic: article
ms.date: 11/09/2018
ms.author: njray
ms.openlocfilehash: 1622de0cccdbc8fee0681e209e756b30da292d3c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58003501"
---
# <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale no Azure

O ambiente de pureScale IBM DB2 fornece um cluster de base de dados para o Azure com elevada disponibilidade e escalabilidade em sistemas operativos Linux. Este artigo mostra uma arquitetura para a execução de DB2 pureScale no Azure.

## <a name="overview"></a>Descrição geral

As empresas há muito tempo tem usado a plataformas de sistema (RDBMS) de gestão de base de dados relacional para suas necessidades (OLTP) de processamento de transações online. Hoje em dia, muitos estão a migrar seus ambientes de banco de dados baseado no mainframe para o Azure como uma forma de expandir a capacidade, reduzir os custos e manter uma estrutura de custo operacional constante.

Migração, muitas vezes, é o primeiro passo para modernizar uma plataforma mais antiga. Por exemplo, um cliente empresarial recentemente rehosted seu ambiente do IBM DB2 em execução no z/OS para o IBM DB2 pureScale no Azure. Embora não é idêntico para o ambiente original, IBM DB2 pureScale no Linux fornece semelhante para a elevada disponibilidade e recursos de escalabilidade como IBM DB2 para z/sistema operacional em execução numa configuração Sysplex paralela no mainframe.

Este artigo descreve a arquitetura utilizada para esta migração do Azure. O utilizador utilizou o Red Hat Linux 7.4 para testar a configuração. Esta versão está disponível no Azure Marketplace. Antes de escolher uma distribuição do Linux, certifique-se verificar as versões atualmente suportadas. Para obter detalhes, consulte a documentação para [IBM DB2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) e [GlusterFS](https://docs.gluster.org/en/latest/).

Este artigo é um ponto de partida para o seu plano de implementação de DB2. Seus requisitos empresariais serão diferentes, mas o padrão básico mesmo se aplica. Também pode utilizar este padrão de arquitetura para aplicações de processamento analítico online (OLAP) no Azure.

Este artigo não abranger diferenças e as tarefas de migração possível para mover o IBM DB2 para base de dados de z/OS para pureScale IBM DB2 em execução no Linux. E ele não fornece estimativas de dimensionamento e análises de carga de trabalho para mudar de DB2 z/OS para DB2 pureScale. 

Para ajudar a decidir qual a melhor arquitetura de pureScale DB2 para o seu ambiente, recomendamos que, totalmente, estimar o dimensionamento e fazer uma hipótese. No sistema de origem, certifique-se a ter em consideração DB2 z/OS paralelo Sysplex com arquitetura de compartilhamento de dados, a configuração de instalação de acoplamento e estatísticas de utilização do recurso (DDF) de dados distribuídos.

> [!NOTE]
> Este artigo descreve uma abordagem para a migração de DB2, mas existem outras. Por exemplo, DB2 pureScale também pode executar em ambientes virtualizados no local. IBM suporta DB2 no Microsoft Hyper-V em diversas configurações. Para obter mais informações, consulte [arquitetura de Virtualização do DB2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) no Centro de dados de conhecimento da IBM.

## <a name="architecture"></a>Arquitetura

Para suportar a elevada disponibilidade e escalabilidade no Azure, pode utilizar uma arquitetura de dados de escalamento horizontal, partilhado para DB2 pureScale. A migração de cliente utilizado a arquitetura de exemplo seguinte.

![DB2 pureScale em máquinas virtuais do Azure que mostra o armazenamento e rede](media/db2-purescale-on-azure/pureScaleArchitecture.png "DB2 pureScale em máquinas virtuais do Azure que mostra o armazenamento e rede")


O diagrama mostra as camadas lógicas necessárias para um cluster de pureScale DB2. Estes incluem máquinas virtuais para um cliente, para a gestão, para colocar em cache, para o motor de base de dados e para armazenamento partilhado. 

Além dos nós de motor de base de dados, o diagrama inclui dois nós utilizado para cluster (CFs) de instalações de colocação em cache. Pelo menos dois nós é utilizado para o próprio mecanismo de banco de dados. Um servidor de DB2 que pertence a um cluster de pureScale denomina-se um membro. 

O cluster está ligado através de iSCSI para um cluster com três nós GlusterFS armazenamento partilhado para fornecer armazenamento escalável e de elevada disponibilidade. DB2 pureScale é instalado em máquinas virtuais do Azure em execução no Linux.

Essa abordagem é um modelo que pode modificar para o tamanho e a escala da sua organização. Baseia-se no seguinte:

-   Dois ou mais membros de base de dados são combinados com, pelo menos, dois nós de CF. Os nós de gerir um conjunto de memória intermédia global (GBP) para os serviços de manager (GLM) de bloqueio global para controlar a contenção de bloqueio e de acesso partilhada de membros ativos e de memória compartilhada. Um nó de CF atua como principal e o outro como o nó de ativação pós-falha CF secundário. Para evitar um ponto único de falha no ambiente, um cluster de pureScale DB2 requer, pelo menos, quatro nós.

-   Armazenamento partilhado de alto desempenho (mostrado na P30 tamanho no diagrama). Cada um de nós Gluster FS utiliza este armazenamento.

-   Rede de alto desempenho para os membros de dados e armazenamento partilhado.

### <a name="compute-considerations"></a>Considerações de computação

Esta arquitetura é executado as camadas do aplicativo, armazenamento e dados em máquinas virtuais do Azure. O [scripts de configuração de implementação](https://aka.ms/db2onazure) crie o seguinte:

-   Um cluster de pureScale DB2. O tipo de recursos de computação que necessita no Azure depende do seu programa de configuração. Em geral, pode usar duas abordagens:

    -   Utilizar um com vários nós de alto desempenho computação (HPC)-rede de estilo em que instâncias pequenas a médias aceder ao armazenamento partilhado. Para este tipo HPC de configuração, do Azure com otimização de memória série E ou de armazenamento com otimização de série L [máquinas virtuais](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) fornecem o poder de computação necessários.

    -   Utilize menos instâncias de máquinas virtuais de grande para os motores de dados. Para as instâncias grandes, o maior com otimização de memória [série M](https://azure.microsoft.com/pricing/details/virtual-machines/series/) máquinas virtuais são ideais para cargas de trabalho dentro da memória pesadas. Poderá ter uma instância dedicada, dependendo do tamanho da partição lógica (LPAR) que é utilizada para executar o DB2.

-   O CF DB2 utiliza máquinas virtuais com otimização de memória, como a série E ou série L.

-   Armazenamento de GlusterFS utiliza padrão\_DS4\_v2 máquinas de virtuais em execução no Linux.

-   Uma jumpbox GlusterFS é uma norma\_DS2\_v2 máquina virtual Linux.

-   O cliente é uma norma\_DS3\_máquina de virtual de v2 com o Windows (utilizado para fins de teste).

-   Um servidor-testemunha é uma norma\_DS3\_v2 máquina virtual Linux (utilizado para DB2 pureScale).

> [!NOTE]
> Um cluster de pureScale DB2 requer, pelo menos, duas instâncias de DB2. Também requer uma instância de cache e uma instância do Gestor de bloqueio.

### <a name="storage-considerations"></a>Considerações sobre armazenamento

Como Oracle RAC, DB2 pureScale é um bloco de alto desempenho e/s, base de dados de escalamento horizontal. Recomendamos que utilize o maior [do Azure premium SSD](disks-types.md) opção adequado às suas necessidades. Opções de armazenamento mais pequenas de mensagens em fila podem ser adequadas para ambientes de desenvolvimento e teste, enquanto os ambientes de produção, muitas vezes, precisam de mais capacidade de armazenamento. A arquitetura de exemplo utiliza [P30](https://azure.microsoft.com/pricing/details/managed-disks/) devido a respetiva relação entre pedidos de IOPS tamanho e preço. Independentemente do tamanho, utilize o armazenamento Premium para um melhor desempenho.

DB2 pureScale utiliza um partilhado-tudo o que arquitetura, onde todos os dados estão acessíveis a partir de todos os nós de cluster. O armazenamento Premium tem de ser partilhado entre instâncias, se a pedido ou de instâncias dedicadas.

Um cluster de pureScale DB2 grande pode exigir 200 terabytes (TB) ou mais dos premium armazenamento compartilhado, com IOPS de 100 000. DB2 pureScale suporta uma interface de bloco iSCSI que pode utilizar no Azure. A interface de iSCSI requer um cluster de armazenamento partilhado que pode implementar com GlusterFS, S2D ou outra ferramenta. Este tipo de solução cria um dispositivo de rede (vSAN) de área de armazenamento virtual no Azure. DB2 pureScale utiliza vSAN para instalar o sistema de ficheiros em cluster que é utilizado para partilhar dados entre máquinas virtuais.

A arquitetura de exemplo utiliza GlusterFS, um sistema de ficheiros de distribuída gratuito, dimensionável e de código-fonte aberto otimizada para armazenamento na cloud.

### <a name="networking-considerations"></a>Considerações de redes

IBM recomenda a rede InfiniBand para todos os membros de um cluster de pureScale DB2. DB2 pureScale também utiliza o acesso remoto direto à memória (RDMA), que estiverem disponíveis e para o CFs.

Durante a configuração, vai criar do Azure [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para conter todas as máquinas virtuais. Em geral, é agrupar os recursos com base em seu tempo de vida e que irão gerenciá-las. As máquinas virtuais nesta arquitetura exigir [accelerated networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). É uma funcionalidade do Azure que fornece a latência de rede de ultra baixas, consistente, por meio da Virtualização de e/s de raiz única (SR-IOV) para uma máquina virtual.

Todas as máquinas virtuais do Azure é implementada numa rede virtual com sub-redes: main, Gluster FS front-end (gfsfe), Gluster FS back-end (bfsbe), DB2 pureScale (db2be) e DB2 pureScale front end (db2fe). O script de instalação também cria o principal [NICs](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics) nas máquinas virtuais na sub-rede principal.

Uso [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) para restringir o tráfego de rede na rede virtual e para isolar as sub-redes.

No Azure, DB2 pureScale tem de utilizar o TCP/IP como a ligação de rede para armazenamento.

## <a name="next-steps"></a>Passos Seguintes

-   [Implementar esta arquitetura no Azure](deploy-ibm-db2-purescale-azure.md)
