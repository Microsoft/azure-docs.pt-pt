---
title: 'Lista de verificação: Boas práticas de desempenho & diretrizes'
description: Fornece uma lista de verificação rápida para rever as suas melhores práticas e diretrizes para otimizar o desempenho do seu SQL Server na Azure Virtual Machine (VM).
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: mathoma
ms.custom: contperf-fy21q3
ms.reviewer: jroth
ms.openlocfilehash: 0b88884576a47db871c78b874104d4973ee9ba9a
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572455"
---
# <a name="checklist-performance-best-practices-for-sql-server-on-azure-vms"></a>Lista de verificação: Melhores práticas de desempenho para SQL Server em VMs Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo fornece uma lista de verificação rápida como uma série de boas práticas e diretrizes para otimizar o desempenho do seu SQL Server em Azure Virtual Machines (VMs). 

Para mais detalhes, consulte os outros artigos desta série: [Tamanho VM,](performance-guidelines-best-practices-vm-size.md) [Armazenamento,](performance-guidelines-best-practices-storage.md) [Linha de Base .](performance-guidelines-best-practices-collect-baseline.md) 


## <a name="overview"></a>Descrição Geral

Enquanto executa o SQL Server em Azure Virtual Machines, continue a utilizar as mesmas opções de afinação de desempenho da base de dados que são aplicáveis ao SQL Server em ambientes de servidores no local. No entanto, o desempenho de uma base de dados relacional numa nuvem pública depende de muitos fatores, como o tamanho de uma máquina virtual, e a configuração dos discos de dados.

Existe tipicamente uma compensação entre otimizar os custos e otimizar o desempenho. Esta série de boas práticas de desempenho está focada em obter o *melhor* desempenho para SQL Server em Azure Virtual Machines. Se a sua carga de trabalho for menos exigente, poderá não necessitar de todas as otimizações recomendadas. Considere as suas necessidades de desempenho, custos e padrões de carga de trabalho à medida que avalia estas recomendações.

## <a name="vm-size"></a>Tamanho da VM

Segue-se uma lista rápida de boas práticas de tamanho VM para executar o seu SQL Server em Azure VM: 

- Utilize tamanhos VM com 4 ou mais vCPU como o [Standard_M8-4ms](/../../virtual-machines/m-series), o [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series), ou o [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) ou superior. 
- Utilize tamanhos de máquinas virtuais [otimizados](../../../virtual-machines/sizes-memory.md) de memória para o melhor desempenho das cargas de trabalho do SQL Server. 
- A série [DSv2 11-15,](../../../virtual-machines/dv2-dsv2-series-memory.md) [Edsv4,](../../../virtual-machines/edv4-edsv4-series.md) a série [M-](../../../virtual-machines/m-series.md)e a [série Mv2](../../../virtual-machines/mv2-series.md) oferecem a relação memória-vCore ideal exigida para as cargas de trabalho OLTP. Ambos os VMs da série M oferecem a maior relação memória-vCore necessária para cargas de trabalho críticas da missão e também são ideais para cargas de trabalho de armazém de dados. 
- Considere uma relação memória-vCore mais elevada para cargas de trabalho críticas e de armazém de dados da missão. 
- Utilize as imagens do mercado da máquina virtual Azure, uma vez que as definições e opções de armazenamento do SQL Server são configuradas para um desempenho ideal do SQL Server. 
- Recolha as características de desempenho da carga de trabalho do alvo e use-as para determinar o tamanho de VM adequado para o seu negócio.

Para saber mais, consulte as [melhores práticas de tamanho VM.](performance-guidelines-best-practices-vm-size.md) 

## <a name="storage"></a>Armazenamento

Segue-se uma lista rápida de boas práticas de configuração de armazenamento para executar o seu SQL Server em Azure VM: 

- Monitorize a aplicação e determine os requisitos de largura de banda de armazenamento e latência para os ficheiros de [dados,](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) registos e temporários do SQL Server antes de escolher o tipo de disco. 
- Para otimizar o desempenho do armazenamento, planeie o maior IOPS disponível e utilize o cache de dados como uma característica de desempenho para leituras de dados, evitando [a tampa/estrangulamento de máquinas virtuais e discos.](../../../virtual-machines/premium-storage-performance.md#throttling)
- Coloque os ficheiros de dados, registos e temporários em unidades separadas.
    - Para a unidade de dados, utilize [apenas discos P30 e P40 premium](../../../virtual-machines/disks-types.md#premium-ssd) para garantir a disponibilidade de suporte à cache
    - Para o plano de log drive para capacidade e desempenho de teste versus custo enquanto avalia os [discos P30 - P80 premium](../../../virtual-machines/disks-types.md#premium-ssd).
      - Se for necessária latência de armazenamento submilissegundo, utilize [discos ultra Azure](../../../virtual-machines/disks-types.md#ultra-disk) para o registo de transações. 
      - Para as implementações de máquinas virtuais da série M, considere [escrever acelerador](../../../virtual-machines/how-to-enable-write-accelerator.md) sobre a utilização de discos ultra Azure.
    - Coloque [a temperatura](/sql/relational-databases/databases/tempdb-database) na unidade efémera local SSD para a maioria das `D:\` cargas de trabalho do SQL Server depois de escolher o tamanho VM ideal. 
      - Se a capacidade da unidade local não for suficiente para a temporária, considere dimensionar o VM. Consulte [as políticas de caching de ficheiros de dados](performance-guidelines-best-practices-storage.md#data-file-caching-policies) para obter mais informações.
- Stripe vários discos de dados Azure usando [espaços de armazenamento](/windows-server/storage/storage-spaces/overview) para aumentar a largura de banda de I/S até os limites de IOPS e limites de produção da máquina virtual alvo.
- Definir [o cache do anfitrião](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) para ler apenas para discos de ficheiros de dados.
- Definir [o cache do anfitrião para](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) nenhum para discos de ficheiro de registo.
    - Não ative a leitura/escrita em discos que contenham ficheiros SQL Server. 
    - Pare sempre o serviço SQL Server antes de alterar as definições de cache do seu disco.
- Para o desenvolvimento e teste, as cargas de trabalho consideram a utilização de um armazenamento padrão. Não é aconselhável utilizar o HDD/SDD standard para cargas de trabalho de produção.
- [O rebentamento do disco baseado em crédito](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20) só deve ser considerado para cargas de trabalho dev/teste mais pequenos e sistemas departameiros.
- Provisione a conta de armazenamento na mesma região que o SQL Server VM. 
- Desative o armazenamento geo-redundante da Azure (geo-replicação) e utilize lRS (armazenamento redundante local) na conta de armazenamento.
- Formate o seu disco de dados para utilizar o tamanho da unidade de atribuição de 64 KB para todos os ficheiros de dados colocados numa unidade diferente da unidade temporária `D:\` (que tem um padrão de 4 KB). Os VMs do Servidor SQL implantados através do Azure Marketplace vêm com discos de dados formatados com o tamanho da unidade de atribuição e intercalam para o conjunto de armazenamento definido para 64 KB. 

Para saber mais, consulte as [melhores práticas](performance-guidelines-best-practices-storage.md)de armazenamento. 


## <a name="azure--sql-feature-specific"></a>Azure & sql recurso específico

Segue-se uma lista rápida de boas práticas para configurações específicas do SQL Server e do Azure ao executar o seu SQL Server em Azure VM: 

- Registe-se com a [Extensão do Agente SQL IaaS](sql-agent-extension-manually-register-single-vm.md) para desbloquear uma série de [benefícios](sql-server-iaas-agent-extension-automate-management.md#feature-benefits)de recurso . 
- Ativar a compressão da página da base de dados.
- Ativar a inicialização instantânea do ficheiro para ficheiros de dados.
- Limite o crescimento automático da base de dados.
- Desative o auto-shrink da base de dados.
- Mover todas as bases de dados para discos de dados, incluindo bases de dados do sistema.
- Mover o registo de erros do SQL Server e rastrear os diretórios de ficheiros para discos de dados.
- Configurar a cópia de segurança predefinida e as localizações dos ficheiros de base de dados.
- [Ativar páginas bloqueadas na memória](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).
- Avalie e aplique as [últimas atualizações cumulativas](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server) para a versão instalada do SQL Server.
- Volte diretamente para o armazenamento da Azure Blob.
- Utilize vários ficheiros [temporários,](/sql/relational-databases/databases/tempdb-database#optimizing-tempdb-performance-in-sql-server) 1 ficheiro por núcleo, até 8 ficheiros.



## <a name="next-steps"></a>Passos seguintes

Para saber mais, veja os outros artigos desta série:
- [Tamanho VM](performance-guidelines-best-practices-vm-size.md)
- [Armazenamento](performance-guidelines-best-practices-storage.md)
- [Recolher linha de base](performance-guidelines-best-practices-collect-baseline.md)

Para obter as melhores práticas de segurança, consulte [considerações de segurança para o SQL Server em Azure Virtual Machines](security-considerations-best-practices.md).

Reveja outros artigos da Máquina Virtual do SqL Server [no SQL Server na Visão Geral das Máquinas Virtuais Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). Se tiver dúvidas sobre máquinas virtuais do SQL Server, veja as [Perguntas Mais Frequentes](frequently-asked-questions-faq.md).
