---
title: 'Tamanho VM: Melhores práticas de desempenho & diretrizes'
description: Fornece diretrizes de tamanho VM e boas práticas para otimizar o desempenho do seu SqL Server na Azure Virtual Machine (VM).
services: virtual-machines-windows
documentationcenter: na
author: dplessMSFT
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: jroth
ms.openlocfilehash: 9427ae1b9bd68f63df40d24122cc13b5460fbc27
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572501"
---
# <a name="vm-size-performance-best-practices-for-sql-server-on-azure-vms"></a>Tamanho VM: Melhores práticas de desempenho para SQL Server em VMs Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo fornece orientação de tamanho VM uma série de boas práticas e diretrizes para otimizar o desempenho do seu SQL Server em Azure Virtual Machines (VMs).

Existe tipicamente uma compensação entre otimizar os custos e otimizar o desempenho. Esta série de boas práticas de desempenho está focada em obter o *melhor* desempenho para SQL Server em Azure Virtual Machines. Se a sua carga de trabalho for menos exigente, poderá não necessitar de todas as otimizações recomendadas. Considere as suas necessidades de desempenho, custos e padrões de carga de trabalho à medida que avalia estas recomendações.


## <a name="checklist"></a>Lista de Verificação

Reveja a seguinte lista de verificação para obter uma breve visão geral das melhores práticas do tamanho de VM que o resto do artigo cobre mais detalhadamente: 

- Utilize tamanhos VM com 4 ou mais vCPU como o [Standard_M8-4ms](/../../virtual-machines/m-series), o [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series), ou o [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) ou superior. 
- Utilize tamanhos de máquinas virtuais [otimizados](../../../virtual-machines/sizes-memory.md) de memória para o melhor desempenho das cargas de trabalho do SQL Server. 
- A série [DSv2 11-15,](../../../virtual-machines/dv2-dsv2-series-memory.md) [Edsv4,](../../../virtual-machines/edv4-edsv4-series.md) a série [M-](../../../virtual-machines/m-series.md)e a [série Mv2](../../../virtual-machines/mv2-series.md) oferecem a relação memória-vCore ideal exigida para as cargas de trabalho OLTP. Ambos os VMs da série M oferecem a maior relação memória-vCore necessária para cargas de trabalho críticas da missão e também são ideais para cargas de trabalho de armazém de dados. 
- Considere uma relação memória-vCore mais elevada para cargas de trabalho críticas e de armazém de dados da missão. 
- Aproveite as imagens do mercado da Máquina Virtual Azure, uma vez que as definições e opções de armazenamento do SQL Server são configuradas para um desempenho ideal do SQL Server. 
- Recolha as características de desempenho da carga de trabalho do alvo e use-as para determinar o tamanho de VM adequado para o seu negócio.

Para comparar a lista de verificação de tamanhos VM com as outras, consulte a lista completa de [boas práticas](performance-guidelines-best-practices-checklist.md)de desempenho . 

## <a name="overview"></a>Descrição Geral

Quando estiver a criar um SQL Server em Azure VM, considere cuidadosamente o tipo de carga de trabalho necessária. Se estiver a migrar um ambiente existente, [colete uma linha de base](performance-guidelines-best-practices-collect-baseline.md) de desempenho para determinar os requisitos do seu SQL Server nos requisitos Azure VM. Se este for um novo VM, então crie o seu novo SQL Server VM com base nos requisitos do seu fornecedor. 

Se estiver a criar um novo SQL Server VM com uma nova aplicação construída para a nuvem, pode facilmente dimensionar o seu SQL Server VM à medida que os seus dados e requisitos de utilização evoluem.
Inicie os ambientes de desenvolvimento com as séries D, Série B ou Av2 de nível inferior e cresça o seu ambiente ao longo do tempo. 

Utilize as imagens de mercado sql Server VM com a configuração de armazenamento no portal. Isto facilitará a criação adequada das piscinas de armazenamento necessárias para obter o tamanho, IOPS e produção necessária para as suas cargas de trabalho. É importante escolher VMs sql server que suportam armazenamento premium e armazenamento premium. Consulte o artigo [de armazenamento](performance-guidelines-best-practices-storage.md) para saber mais. 

O mínimo recomendado para um ambiente OLTP de produção é 4 vCore, 32 GB de memória e uma relação memória-vCore de 8. Para novos ambientes, comece com máquinas 4 vCore e escala para 8, 16, 32 vCores ou mais quando os seus dados e requisitos de cálculo mudarem. Para o rendimento do OLTP, destino SQL Server VMs que têm 5000 IOPS para cada vCore. 

O armazém de dados do SQL Server e os ambientes críticos da missão terão muitas vezes de escalar para além da relação 8 memória-vCore. Para ambientes médios, pode querer escolher uma relação memória-vCore de 16 e uma relação memória-vCore de 32 para ambientes maiores de armazém de dados. 

Os ambientes de armazém de dados do SQL Server beneficiam frequentemente do processamento paralelo de máquinas maiores. Por esta razão, a série M e a série Mv2 são opções fortes para ambientes maiores de armazéns de dados.

Utilize o vCPU e a configuração de memória da sua máquina de origem como base de base para migrar uma base de dados do SQL Server de corrente no local para o SQL Server em VMs Azure. Leve a sua licença principal para a Azure para aproveitar o [Benefício Híbrido Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) e economizar nos custos de licenciamento do SQL Server.

## <a name="memory-optimized"></a>Otimizada para memória

Os [tamanhos de máquina virtual otimizados pela memória](../../../virtual-machines/sizes-memory.md) são um alvo primário para VMs do SQL Server e a escolha recomendada pela Microsoft. As máquinas virtuais otimizadas pela memória oferecem rácios de memória-CPU mais fortes e opções de cache média-grande. 

### <a name="m-mv2-and-mdsv2-series"></a>SérieS M, Mv2 e Mdsv2

A [série M](../../../virtual-machines/m-series.md) oferece contagens vCore e memória para algumas das maiores cargas de trabalho do SQL Server.  

A [série Mv2](../../../virtual-machines/mv2-series.md) tem as mais altas contagens e memória vCore e é recomendada para cargas de trabalho críticas e de armazéns de dados. As instâncias da série Mv2 são tamanhos VM otimizados de memória que fornecem um desempenho computacional sem paralelo para suportar grandes bases de dados na memória e cargas de trabalho com uma relação memória-CPU alta que é perfeita para servidores de base de dados relacionais, caches grandes e análises na memória.

O [Standard_M64ms](../../../virtual-machines/m-series.md) tem uma relação memória-vCore de 28, por exemplo.

[Mdsv2 Medium Memory series](../../..//virtual-machines/msv2-mdsv2-series.md) é uma nova série M que está atualmente em [pré-visualização](https://aka.ms/Mv2MedMemoryPreview) que oferece uma gama de máquinas virtuais de nível M com uma oferta de memória mais intermédia. Estas máquinas são adequadas para cargas de trabalho do SQL Server com um suporte mínimo de 10 memória-vCore até 30.

Algumas das características da série M e Mv2 atraentes para o desempenho do SQL Server incluem [armazenamento premium](../../../virtual-machines/premium-storage-performance.md) e suporte de [armazenamento premium,](../../../virtual-machines/premium-storage-performance.md#disk-caching) suporte [de ultra-disco](../../../virtual-machines/disks-enable-ultra-ssd.md) e [aceleração de escrita](../../../virtual-machines/how-to-enable-write-accelerator.md).

### <a name="edsv4-series"></a>Série Edsv4

A [série Edsv4](../../../virtual-machines/edv4-edsv4-series.md) foi concebida para aplicações intensivas de memória. Estes VMs têm uma grande capacidade de armazenamento local SSD, forte disco local IOPS, até 504 GiB de RAM. Há um giB de memória quase consistente por vCore em toda a maioria destas máquinas virtuais, o que é ideal para cargas de trabalho padrão do SQL Server. 

Há uma nova máquina virtual neste grupo com o [Standard_E80ids_v4](../../../virtual-machines/edv4-edsv4-series.md) que oferece 80 vCores, 504 GBs de memória, com uma relação memória-vCore de 6. Esta máquina virtual é notável porque está [isolada,](../../../virtual-machines/isolation.md) o que significa que é garantida a única máquina virtual a funcionar no hospedeiro, e portanto está isolada de outras cargas de trabalho do cliente. Isto tem uma relação memória-vCore inferior ao recomendado para o SQL Server, pelo que só deve ser utilizada se for necessário um isolamento.

As máquinas virtuais da série Edsv4 suportam [o armazenamento premium](../../../virtual-machines/premium-storage-performance.md)e o armazenamento [premium.](../../../virtual-machines/premium-storage-performance.md#disk-caching)

### <a name="dsv2-series-11-15"></a>Série DSv2 11-15

A [série DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) tem as mesmas configurações de memória e disco que a série D anterior. Esta série tem uma relação memória-CPU consistente de 7 em todas as máquinas virtuais. Esta é a mais pequena das séries otimizadas pela memória e é uma boa opção de baixo custo para as cargas de trabalho do SQL Server de nível de entrada.

O [DSv2 série 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) suporta [armazenamento premium](../../../virtual-machines/premium-storage-performance.md) e armazenamento [premium,](../../../virtual-machines/premium-storage-performance.md#disk-caching)o que é fortemente recomendado para um desempenho ideal.

## <a name="general-purpose"></a>Fins gerais

Os [tamanhos de máquina virtual](../../../virtual-machines/sizes-general.md) de finalidade geral são projetados para fornecer rácios de memória-vCore equilibrados para cargas de trabalho de nível de entrada mais pequenos, tais como desenvolvimento e teste, servidores web e servidores de base de dados mais pequenos. 

Devido às relações memória-vCore mais pequenas com as máquinas virtuais de finalidade geral, é importante monitorizar cuidadosamente os contadores de desempenho baseados na memória para garantir que o SQL Server é capaz de obter a memória de cache tampão de que necessita. Consulte a [linha de base de desempenho da memória](performance-guidelines-best-practices-collect-baseline.md#memory) para obter mais informações. 

Uma vez que a recomendação inicial para cargas de trabalho de produção é uma relação memória-vCore de 8, a configuração mínima recomendada para um VM de desempenho geral SQL Server é de 4 vCPU e 32 GB de memória. 

### <a name="ddsv4-series"></a>Série Ddsv4

A [série Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) oferece uma combinação justa de vCPU, memória e disco temporário, mas com menor suporte memória-a-vCore. 

Os Ddsv4 VMs incluem latência mais baixa e armazenamento local de maior velocidade.

Estas máquinas são ideais para sql lado a lado e implementações de aplicativos que requerem acesso rápido ao armazenamento temporário e bases de dados relacionais departamacionais. Existe uma relação memória-vCore padrão de 4 em todas as máquinas virtuais desta série. 

Por esta razão, recomenda-se alavancar o D8ds_v4 como a máquina virtual de arranque desta série, que tem 8 vCores e 32 GBs de memória. A maior máquina é a D64ds_v4, que tem 64 vCores e 256 GBs de memória.

As máquinas virtuais [da série Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) suportam [armazenamento premium](../../../virtual-machines/premium-storage-performance.md) e [armazenamento premium.](../../../virtual-machines/premium-storage-performance.md#disk-caching)

> [!NOTE]
> A [série Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) não tem a relação memória-vCore de 8 que é recomendada para as cargas de trabalho do SQL Server. Como tal, considerar usar estas máquinas virtuais apenas para cargas de trabalho de aplicação e desenvolvimento mais pequenas.

### <a name="b-series"></a>Série B

Os tamanhos de máquina virtual [da série B são ideais](../../../virtual-machines/sizes-b-series-burstable.md) para cargas de trabalho que não necessitam de um desempenho consistente, como a prova de conceito e servidores de aplicação e desenvolvimento muito pequenos. 

A maioria dos tamanhos de máquina virtual [da série B rebentam](../../../virtual-machines/sizes-b-series-burstable.md) têm uma relação memória-vCore de 4. A maior destas máquinas é a [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) com 20 vCores e 80 GB de memória.

Esta série é única, uma vez que as aplicações têm a capacidade de **rebentar** durante o horário comercial com créditos rebentados que variam com base no tamanho da máquina. 

Quando os créditos estão esgotados, o VM volta ao desempenho da máquina de base.

O benefício da série B é a poupança de cálculo que você poderia obter em comparação com os outros tamanhos VM em outras séries, especialmente se você precisar do poder de processamento com moderação ao longo do dia.

Esta série suporta [o armazenamento premium,](../../../virtual-machines/premium-storage-performance.md)mas **não suporta** o [armazenamento premium.](../../../virtual-machines/premium-storage-performance.md#disk-caching)

> [!NOTE] 
> A [série B rebentada](../../../virtual-machines/sizes-b-series-burstable.md) não tem a relação memória-vCore de 8 que é recomendada para as cargas de trabalho do SQL Server. Como tal, considere usar estas máquinas virtuais para aplicações mais pequenas, servidores web e cargas de trabalho de desenvolvimento apenas.

### <a name="av2-series"></a>Série Av2

Os VMs [da série Av2](../../../virtual-machines/av2-series.md) são mais adequados para cargas de trabalho de nível de entrada como desenvolvimento e teste, servidores web de baixo tráfego, bases de dados de aplicações pequenas e médias e prova de conceitos.

Apenas os [Standard_A2m_v2](../../../virtual-machines/av2-series.md) (2 vCores e 16GBs de memória), [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 vCores e 32GBs de memória) e os [Standard_A8m_v2](../../../virtual-machines/av2-series.md) (8 vCores e 64GBs de memória) têm uma boa relação memória-vCore de 8 para estas três máquinas virtuais de topo. 

Estas máquinas virtuais são ambas boas opções para o desenvolvimento mais pequeno e testar máquinas SQL Server. 

O [Standard_A8m_v2](../../../virtual-machines/av2-series.md) 8 vCore também pode ser uma boa opção para pequenas aplicações e servidores web.

> [!NOTE] 
> A série Av2 não suporta armazenamento premium e, como tal, não é recomendado para a produção de cargas de trabalho SQL Server mesmo com as máquinas virtuais que têm uma relação memória-vCore de 8.

## <a name="storage-optimized"></a>Otimizada para armazenamento

Os [tamanhos de VM otimizados](../../../virtual-machines/sizes-storage.md) de armazenamento são para casos específicos de utilização. Estas máquinas virtuais são especificamente concebidas com produção de disco otimizada e IO. 

### <a name="lsv2-series"></a>Série Lsv2

A [série Lsv2](../../../virtual-machines/lsv2-series.md) apresenta alta produção, baixa latência e armazenamento local de NVMe. Os VMs da série Lsv2 são otimizados para usar o disco local no nó ligado diretamente ao VM em vez de usar discos de dados duráveis. 

Estas máquinas virtuais são opções fortes para big data, data warehouse, reporting e cargas de trabalho ETL. A elevada produção e iops do armazenamento local de NVMe é um bom caso de uso para processar ficheiros que serão carregados na sua base de dados e outros cenários onde os dados podem ser recriados a partir do sistema de origem ou outros repositórios, como o armazenamento de Azure Blob ou o Lago de Dados Azure. [Série Lsv2](../../../virtual-machines/lsv2-series.md) Os VMs também podem rebentar o seu desempenho em disco até 30 minutos de cada vez.

Estas máquinas virtuais de 8 a 80 vCPU com 8 GiB de memória por vCPU e para cada 8 vCPUs há 1,92 TB de SSD NVMe. Isto significa que para o maior VM desta série, o [L80s_v2,](../../../virtual-machines/lsv2-series.md)há 80 vCPU e 640 BiB de memória com 10x1.92TB de armazenamento NVMe.  Existe uma relação memória-vCore consistente de 8 em todas estas máquinas virtuais.

O armazenamento NVMe é efémero, o que significa que os dados serão perdidos nestes discos se você negociar a sua máquina virtual, ou se for movido para um anfitrião diferente para a cura do serviço.

A série Lsv2 e Ls [suportam o armazenamento premium,](../../../virtual-machines/premium-storage-performance.md)mas não o armazenamento premium. A criação de uma cache local para aumentar os IOPs não é apoiada. 

> [!WARNING]
> Armazenar os seus ficheiros de dados no armazenamento efémero NVMe pode resultar em perda de dados quando o VM é transacionado. 

## <a name="constrained-vcores"></a>VCores constrangidos

As cargas de trabalho do Servidor SQL de alto desempenho muitas vezes precisam de maiores quantidades de memória, I/O, e produção sem as contagens de vCore mais altas. 

A maioria das cargas de trabalho da OLTP são bases de dados de aplicações impulsionadas por um grande número de transações menores. Com as cargas de trabalho da OLTP, apenas uma pequena quantidade dos dados é lida ou modificada, mas os volumes de transações impulsionadas pelas contagens dos utilizadores são muito mais elevados. É importante ter a memória do SQL Server disponível para planos de cache, armazenar dados recentemente acedidos para desempenho, e garantir que as leituras físicas podem ser lidas na memória rapidamente. 

Estes ambientes OLTP precisam de maiores quantidades de memória, armazenamento rápido e a largura de banda de I/O necessária para funcionar da melhor forma. 

De forma a manter este nível de desempenho sem os custos de licenciamento do SQL Server mais elevados, o Azure oferece tamanhos VM com [contagens de VCPU restritas.](../../../virtual-machines/constrained-vcpu.md) 

Isto ajuda a controlar os custos de licenciamento reduzindo os vCores disponíveis, mantendo a mesma memória, armazenamento e largura de banda de I/O da máquina virtual principal.

A contagem de vCPU pode ser limitada a metade a um quarto do tamanho original de VM. A redução dos vCores disponíveis para a máquina virtual irá alcançar rácios de memória-vCore mais elevados, mas o custo do cálculo permanecerá o mesmo.

Estes novos tamanhos VM têm um sufixo que especifica o número de vCPUs ativos para facilitar a sua identificação. 

Por exemplo, o [M64-32ms](../../../virtual-machines/constrained-vcpu.md) requer o licenciamento de apenas 32 SQL Server vCores com a memória, I/O, e a produção dos [M64ms](../../../virtual-machines/m-series.md) e do [M64-16ms](../../../virtual-machines/constrained-vcpu.md) requer licenciamento apenas 16 vCores.  Embora enquanto o [M64-16ms](../../../virtual-machines/constrained-vcpu.md) tenha um quarto do custo de licenciamento do SQL Server dos M64ms, o custo de computação da máquina virtual será o mesmo.

> [!NOTE] 
> - As cargas de trabalho de armazém de dados médios a grandes ainda podem beneficiar de [VMs vCore limitados,](../../../virtual-machines/constrained-vcpu.md)mas as cargas de trabalho do armazém de dados são geralmente caracterizadas por menos utilizadores e processos que abordam quantidades maiores de dados através de planos de consulta que funcionam em paralelo. 
> - O custo do cálculo, que inclui o licenciamento do sistema operativo, permanecerá o mesmo que a máquina virtual principal. 



## <a name="next-steps"></a>Passos seguintes

Para saber mais, veja os outros artigos desta série:
- [Lista de verificação rápida](performance-guidelines-best-practices-checklist.md)
- [Armazenamento](performance-guidelines-best-practices-storage.md)
- [Recolher linha de base](performance-guidelines-best-practices-collect-baseline.md)

Para obter as melhores práticas de segurança, consulte [considerações de segurança para o SQL Server em Azure Virtual Machines](security-considerations-best-practices.md).

Reveja outros artigos da Máquina Virtual do SqL Server [no SQL Server na Visão Geral das Máquinas Virtuais Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). Se tiver dúvidas sobre máquinas virtuais do SQL Server, veja as [Perguntas Mais Frequentes](frequently-asked-questions-faq.md).
