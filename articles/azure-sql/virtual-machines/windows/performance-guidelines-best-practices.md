---
title: Diretrizes de desempenho para SQL Server em Azure Microsoft Docs
description: Fornece diretrizes para otimizar o desempenho do SQL Server em Microsoft Azure Virtual Machines.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/09/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 2cff67dde7cfe9e015cd25b26811410ce6e686e9
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462536"
---
# <a name="performance-guidelines-for-sql-server-on-azure-virtual-machines"></a>Diretrizes de desempenho do SQL Server nas Máquinas Virtuais do Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo fornece orientação para otimizar o desempenho do SQL Server nas Máquinas Virtuais Microsoft Azure.

## <a name="overview"></a>Descrição geral

Enquanto executa o SQL Server em Azure Virtual Machines, recomendamos que continue a utilizar as mesmas opções de afinação de desempenho da base de dados que são aplicáveis ao SQL Server em ambientes de servidores no local. No entanto, o desempenho das bases de dados relacional numa cloud pública depende de muitos fatores, como o tamanho da máquina virtual e a configuração dos discos de dados.

[As imagens do SQL Server aerudas no portal Azure](sql-vm-create-portal-quickstart.md) seguem as [melhores práticas](storage-configuration.md)de configuração de armazenamento geral . Após o provisionamento, considere aplicar outras otimizações discutidas neste artigo. Baseie as suas escolhas na sua carga de trabalho e verifique através de testes.

> [!TIP]
> Existe tipicamente uma compensação entre otimizar os custos e otimizar o desempenho. Este artigo está focado em obter o *melhor* desempenho para SQL Server em Azure Virtual Machines. Se a sua carga de trabalho for menos exigente, poderá não necessitar de todas as otimizações listadas abaixo. Considere as suas necessidades de desempenho, custos e padrões de carga de trabalho à medida que avalia estas recomendações.

## <a name="quick-checklist"></a>Lista de verificação rápida

Segue-se uma lista de verificação rápida para um desempenho ótimo do SQL Server em Azure Virtual Machines:

| Área | Otimizações |
| --- | --- |
| [Tamanho VM](#vm-size-guidance) | - Utilize tamanhos VM com 4 ou mais vCPU como o [Standard_M8-4ms,](/../../virtual-machines/m-series)o [E4ds_v4,](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)ou o [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) ou superior. <br/><br/> - Utilize os tamanhos de máquinas virtuais [otimizados](../../../virtual-machines/sizes-memory.md) para o melhor desempenho das cargas de trabalho do SQL Server. <br/><br/> - A série [DSv2 11-15,](../../../virtual-machines/dv2-dsv2-series-memory.md) [Edsv4,](../../../virtual-machines/edv4-edsv4-series.md) a série [M-](../../../virtual-machines/m-series.md)e a [série Mv2](../../../virtual-machines/mv2-series.md) oferecem a relação memória-vCore ideal exigida para as cargas de trabalho OLTP. Ambos os VMs da série M oferecem a maior relação memória-vCore necessária para cargas de trabalho críticas da missão e também é ideal para cargas de trabalho de armazém de dados. <br/><br/> - Pode ser necessário um rácio memória-vCore mais elevado para a carga de trabalho crítica da missão e do armazém de dados. <br/><br/> - Aproveite as imagens do mercado da Máquina Virtual Azure, uma vez que as definições e opções de armazenamento do SQL Server são configuradas para um desempenho ideal do SQL Server. <br/><br/> - Recolher as características de desempenho da carga de trabalho do alvo e usá-las para determinar o tamanho de VM adequado para o seu negócio.|
| [Armazenamento](#storage-guidance) | - Para testar detalhadamente o desempenho do SQL Server em Máquinas Virtuais Azure com referências TPC-E e TPC_C, consulte o desempenho do blog [Otimize OLTP](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). <br/><br/> - Utilize [SSDs premium](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) para obter as melhores vantagens de preço/desempenho. Configurar [Leia apenas cache](../../../virtual-machines/premium-storage-performance.md#disk-caching) para ficheiros de dados e não cache para o ficheiro de registo. <br/><br/> - Utilize Discos Ultra se forem [necessárias](../../../virtual-machines/disks-types.md#ultra-disk) menos de 1-ms de atrasos de armazenamento pela carga de trabalho. Consulte [migrar para ultra disco](storage-migrate-to-ultradisk.md) para saber mais. <br/><br/> - Recolher os requisitos de latência de armazenamento para os dados do SQL Server, registo e DB temp [monitorizando a aplicação](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist) antes de escolher o tipo de disco. Se forem necessárias < dedesemes de armazenamento de 1-ms, utilize discos ultra,de outra forma use SSD premium. Se as baixas latências forem apenas necessárias para o ficheiro de registo e não para ficheiros de dados, então [forneça o Disco Ultra](../../../virtual-machines/disks-enable-ultra-ssd.md) no IOPS necessário e os níveis de produção apenas para o Ficheiro de registo. <br/><br/>  - O armazenamento normalizado só é recomendado para fins de desenvolvimento e teste ou para ficheiros de cópia de segurança e não deve ser utilizado para cargas de trabalho de produção. <br/><br/> - Mantenha a [conta de armazenamento](../../../storage/common/storage-account-create.md) e o SQL Server VM na mesma região.<br/><br/> - Desativar [o armazenamento geo-redundante](../../../storage/common/storage-redundancy.md) da Azure (geo-replicação) na conta de armazenamento.  |
| [Discos](#disks-guidance) | - Utilize um mínimo de 2 [discos SSD premium](../../../virtual-machines/disks-types.md#premium-ssd) (1 para ficheiros de registo e 1 para ficheiros de dados). <br/><br/> - Para cargas de trabalho que exijam < latências IO de 1-ms, ative o acelerador de escrita para séries M e considere a utilização de discos Ultra SSD para séries Es e DS. <br/><br/> - Ativar [a leitura apenas](../../../virtual-machines/premium-storage-performance.md#disk-caching) no(s) disco(s) que hospeda os ficheiros de dados.<br/><br/> - Adicione uma capacidade adicional de 20% de IOPS/produção premium do que a sua carga de trabalho requer ao [configurar o armazenamento para os ficheiros do SQL Server, log e TempDB](storage-configuration.md) <br/><br/> - Evite utilizar sistema operativo ou discos temporários para armazenamento de bases de dados ou registo de registo.<br/><br/> - Não ative o cache no(s) disco(s) hospedando o ficheiro de registo.  **Importante**: Pare o serviço SQL Server ao alterar as definições de cache para um disco Azure Virtual Machines.<br/><br/> - Stripe vários discos de dados Azure para obter um aumento da produção de armazenamento.<br/><br/> - Formato com tamanhos de atribuição documentados. <br/><br/> - Coloque o TempDB na unidade SSD local `D:\` para as cargas de trabalho críticas do SQL Server (depois de escolher o tamanho VM correto). Se criar o VM a partir do portal Azure ou dos modelos de quickstart Azure e [colocar o Temp DB no Disco Local,](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)então não precisa de mais ação; para todos os outros casos siga os passos no blog para  [usar SSDs para armazenar TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-TempDB-and-buffer-pool-extensions/) para evitar falhas após o reinício. Se a capacidade da unidade local não for suficiente para o seu tamanho DeP DB, em seguida, coloque o Temp DB numa piscina de armazenamento [listrada](../../../virtual-machines/premium-storage-performance.md) em discos SSD premium com [cache apenas de leitura](../../../virtual-machines/premium-storage-performance.md#disk-caching). |
| [I/O](#io-guidance) |- Ativar a compressão da página da base de dados.<br/><br/> - Ativar a inicialização instantânea do ficheiro para ficheiros de dados.<br/><br/> - Limitar o crescimento automático da base de dados.<br/><br/> - Desative o auto-shrink da base de dados.<br/><br/> - Mover todas as bases de dados para discos de dados, incluindo bases de dados do sistema.<br/><br/> - Mover o registo de erros do SQL Server e rastrear os diretórios de ficheiros para discos de dados.<br/><br/> - Configurar a cópia de segurança predefinida e as localizações dos ficheiros de base de dados.<br/><br/> - [Ativar páginas bloqueadas na memória](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).<br/><br/> - Avaliar e aplicar as [últimas atualizações cumulativas](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server) para a versão instalada do SQL Server. |
| [Específico de recursos](#feature-specific-guidance) | - Volte diretamente para o armazém da Azure Blob.<br/><br/>- Utilize [cópias de segurança instantâneas](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) de ficheiros para bases de dados superiores a 12 TB. <br/><br/>- Utilize vários ficheiros DB temporários, 1 ficheiro por núcleo, até 8 ficheiros.<br/><br/>- Coloque a memória máxima do servidor em 90% ou até 50 GB para o Sistema Operativo. <br/><br/>- Ativar um numa suave. |


<br/>
Para obter mais informações sobre *como* e *porquê* fazer estas otimizações, por favor reveja os detalhes e orientações fornecidos nas seguintes secções.
<br/><br/>

## <a name="getting-started"></a>Introdução

Se estiver a criar um novo SQL Server no Azure VM e não estiver a migrar um sistema de origem atual, crie o seu novo SQL Server VM com base nos requisitos do seu fornecedor.  Os requisitos do fornecedor para um SQL Server VM são os mesmos que o que irias implementar no local. 

Se estiver a criar um novo SQL Server VM com uma nova aplicação construída para a nuvem, pode facilmente dimensionar o seu SQL Server VM à medida que os seus dados e requisitos de utilização evoluem.
Inicie os ambientes de desenvolvimento com as séries D, Série B ou Av2 de nível inferior e cresça o seu ambiente ao longo do tempo. 

O mínimo recomendado para um ambiente OLTP de produção é 4 vCore, 32 GB de memória e uma relação memória-vCore de 8. Para novos ambientes, comece com máquinas 4 vCore e escala para 8, 16, 32 vCores ou mais quando os seus dados e requisitos de cálculo mudarem. Para o rendimento do OLTP, destino SQL Server VMs que têm 5000 IOPS para cada vCore. 

Utilize as imagens de mercado sql Server VM com a configuração de armazenamento no portal. Isto facilitará a criação adequada das piscinas de armazenamento necessárias para obter o tamanho, IOPS e produção necessária para as suas cargas de trabalho. É importante escolher VMs sql server que suportam armazenamento premium e armazenamento premium. Consulte a secção [de armazenamento](#storage-guidance) para saber mais. 

O armazém de dados do SQL Server e os ambientes críticos da missão terão muitas vezes de escalar para além da relação 8 memória-vCore. Para ambientes médios, pode querer escolher uma relação core-to-memory de 16 e uma relação core-to-memory para ambientes maiores de armazém de dados. 

Os ambientes de armazém de dados do SQL Server beneficiam frequentemente do processamento paralelo de máquinas maiores. Por esta razão, a série M e a série Mv2 são opções fortes para ambientes maiores de armazéns de dados.

## <a name="vm-size-guidance"></a>Orientação do tamanho VM

Utilize o vCPU e a configuração de memória da sua máquina de origem como base de base para migrar uma base de dados do SQL Server de corrente no local para o SQL Server em VMs Azure. Leve a sua licença principal para a Azure para aproveitar o [Benefício Híbrido Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) e economizar nos custos de licenciamento do SQL Server.

**A Microsoft recomenda uma relação memória-vCore de 8 como ponto de partida para a produção de cargas de trabalho do SQL Server.** Os rácios mais pequenos são aceitáveis para cargas de trabalho não produtivos. 

Escolha uma [memória otimizada,](../../../virtual-machines/sizes-memory.md) [finalidade geral,](../../../virtual-machines/sizes-general.md) [armazenamento otimizado](../../../virtual-machines/sizes-storage.md)ou tamanho de máquina virtual [vCore constrangido](../../../virtual-machines/constrained-vcpu.md) que seja o melhor para o desempenho do SQL Server com base na sua carga de trabalho (OLTP ou armazém de dados). 

### <a name="memory-optimized"></a>Com otimização de memória

Os [tamanhos de máquina virtual otimizados pela memória](../../../virtual-machines/sizes-memory.md) são um alvo primário para VMs do SQL Server e a escolha recomendada pela Microsoft. As máquinas virtuais otimizadas pela memória oferecem rácios de memória-CPU mais fortes e opções de cache média-grande. 

#### <a name="m-and-mv2-series"></a>SérieS M e Mv2

A [série M](../../../virtual-machines/m-series.md) oferece contagens vCore e memória para algumas das maiores cargas de trabalho do SQL Server.  

A [série Mv2](../../../virtual-machines/mv2-series.md) tem as mais altas contagens e memória vCore e é recomendada para cargas de trabalho críticas e de armazéns de dados. As instâncias da série Mv2 são tamanhos VM otimizados de memória que fornecem um desempenho computacional sem paralelo para suportar grandes bases de dados na memória e cargas de trabalho com uma relação memória-CPU alta que é perfeita para servidores de base de dados relacionais, caches grandes e análises na memória.

O [Standard_M64ms](../../../virtual-machines/m-series.md) tem uma relação memória-vCore de 28, por exemplo.

Algumas das características da série M e Mv2 atraentes para o desempenho do SQL Server incluem [armazenamento premium](../../../virtual-machines/premium-storage-performance.md) e suporte de [armazenamento premium,](../../../virtual-machines/premium-storage-performance.md#disk-caching) suporte [de ultra-disco](../../../virtual-machines/disks-enable-ultra-ssd.md) e [aceleração de escrita](../../../virtual-machines/how-to-enable-write-accelerator.md).

#### <a name="edsv4-series"></a>Série Edsv4

A [série Edsv4](../../../virtual-machines/edv4-edsv4-series.md) foi concebida para aplicações intensivas de memória. Estes VMs têm uma grande capacidade de armazenamento local SSD, forte disco local IOPS, até 504 GiB de RAM, e melhor compute em comparação com os tamanhos Ev3/Esv3 anteriores com VMs Gen2. Existe uma relação memória-vCore quase consistente de 8 através destas máquinas virtuais, o que é ideal para cargas de trabalho padrão do SQL Server. 

Esta série VM é ideal para aplicações e aplicações empresariais intensivas de memória que beneficiam de baixa latência, armazenamento local de alta velocidade.

As máquinas virtuais da série Edsv4 suportam [o armazenamento premium](../../../virtual-machines/premium-storage-performance.md)e o armazenamento [premium.](../../../virtual-machines/premium-storage-performance.md#disk-caching)

#### <a name="dsv2-series-11-15"></a>Série DSv2 11-15

A [série DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) tem as mesmas configurações de memória e disco que a série D anterior. Esta série tem uma relação memória-CPU consistente de 7 em todas as máquinas virtuais. 

O [DSv2 série 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) suporta [armazenamento premium](../../../virtual-machines/premium-storage-performance.md) e armazenamento [premium,](../../../virtual-machines/premium-storage-performance.md#disk-caching)o que é fortemente recomendado para um desempenho ideal.

### <a name="general-purpose"></a>Fins Gerais

Os [tamanhos de máquina virtual](../../../virtual-machines/sizes-general.md) de finalidade geral são projetados para fornecer rácios de memória-vCore equilibrados para cargas de trabalho de nível de entrada mais pequenos, tais como desenvolvimento e teste, servidores web e servidores de base de dados mais pequenos. 

Devido às relações memória-vCore mais pequenas com as máquinas virtuais de finalidade geral, é importante monitorizar cuidadosamente os contadores de desempenho baseados na memória para garantir que o SQL Server é capaz de obter a memória de cache tampão de que necessita. Consulte a [linha de base de desempenho da memória](#memory) para obter mais informações. 

Uma vez que a recomendação inicial para cargas de trabalho de produção é uma relação memória-vCore de 8, a configuração mínima recomendada para um VM de desempenho geral SQL Server é de 4 vCPU e 32 GB de memória. 

#### <a name="ddsv4-series"></a>Série Ddsv4

A [série Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) oferece uma combinação justa de vCPU, memória e disco temporário, mas com menor suporte memória-a-vCore. 

Os Ddsv4 VMs incluem latência mais baixa e armazenamento local de maior velocidade.

Estas máquinas são ideais para sql lado a lado e implementações de aplicativos que requerem acesso rápido ao armazenamento temporário e bases de dados relacionais departamacionais. Existe uma relação memória-vCore padrão de 4 em todas as máquinas virtuais desta série. 

Por esta razão, recomenda-se alavancar o D8ds_v4 como a máquina virtual de arranque desta série, que tem 8 vCores e 32 GBs de memória. A maior máquina é a D64ds_v4, que tem 64 vCores e 256 GBs de memória.

As máquinas virtuais [da série Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) suportam [armazenamento premium](../../../virtual-machines/premium-storage-performance.md) e [armazenamento premium.](../../../virtual-machines/premium-storage-performance.md#disk-caching)

> [!NOTE]
> A [série Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) não tem a relação memória-vCore de 8 que é recomendada para as cargas de trabalho do SQL Server. Como tal, considerar usar estas máquinas virtuais apenas para cargas de trabalho de aplicação e desenvolvimento mais pequenas.

#### <a name="b-series"></a>Série B

Os tamanhos de máquina virtual [da série B são ideais](../../../virtual-machines/sizes-b-series-burstable.md) para cargas de trabalho que não necessitam de um desempenho consistente, como a prova de conceito e servidores de aplicação e desenvolvimento muito pequenos. 

A maioria dos tamanhos de máquina virtual [da série B rebentam](../../../virtual-machines/sizes-b-series-burstable.md) têm uma relação memória-vCore de 4. A maior destas máquinas é a [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) com 20 vCores e 80 GB de memória.

Esta série é única, uma vez que as aplicações têm a capacidade de **rebentar** durante o horário comercial com créditos rebentados que variam com base no tamanho da máquina. 

Quando os créditos estão esgotados, o VM volta ao desempenho da máquina de base.

O benefício da série B é a poupança de cálculo que você poderia obter em comparação com os outros tamanhos VM em outras séries, especialmente se você precisar do poder de processamento com moderação ao longo do dia.

Esta série suporta [o armazenamento premium,](../../../virtual-machines/premium-storage-performance.md)mas **não suporta** o [armazenamento premium.](../../../virtual-machines/premium-storage-performance.md#disk-caching)

> [!NOTE] 
> A [série B rebentada](../../../virtual-machines/sizes-b-series-burstable.md) não tem a relação memória-vCore de 8 que é recomendada para as cargas de trabalho do SQL Server. Como tal, considere usar estas máquinas virtuais para aplicações mais pequenas, servidores web e cargas de trabalho de desenvolvimento apenas.

#### <a name="av2-series"></a>Série Av2

Os VMs [da série Av2](../../../virtual-machines/av2-series.md) são mais adequados para cargas de trabalho de nível de entrada como desenvolvimento e teste, servidores web de baixo tráfego, bases de dados de aplicações pequenas e médias e prova de conceitos.

Apenas os [Standard_A2m_v2](../../../virtual-machines/av2-series.md) (2 vCores e 16GBs de memória), [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 vCores e 32GBs de memória) e os [Standard_A8m_v2](../../../virtual-machines/av2-series.md) (8 vCores e 64GBs de memória) têm uma boa relação memória-vCore de 8 para estas três máquinas virtuais de topo. 

Estas máquinas virtuais são ambas boas opções para o desenvolvimento mais pequeno e testar máquinas SQL Server. 

O [Standard_A8m_v2](../../../virtual-machines/av2-series.md) 8 vCore também pode ser uma boa opção para pequenas aplicações e servidores web.

> [!NOTE] 
> A série Av2 não suporta armazenamento premium e, como tal, não é recomendado para a produção de cargas de trabalho SQL Server mesmo com as máquinas virtuais que têm uma relação memória-vCore de 8.

### <a name="storage-optimized"></a>Com otimização de armazenamento

Os [tamanhos de VM otimizados](../../../virtual-machines/sizes-storage.md) de armazenamento são para casos específicos de utilização. Estas máquinas virtuais são especificamente concebidas com produção de disco otimizada e IO. Esta série de máquinas virtuais destina-se a cenários de big data, armazenamento de dados e grandes bases de dados transacionais. 

#### <a name="lsv2-series"></a>Série Lsv2

A [série Lsv2](../../../virtual-machines/lsv2-series.md) apresenta alta produção, baixa latência e armazenamento local de NVMe. Os VMs da série Lsv2 são otimizados para usar o disco local no nó ligado diretamente ao VM em vez de usar discos de dados duráveis. 

Estas máquinas virtuais são opções fortes para big data, data warehouse, reporting e cargas de trabalho ETL. A elevada produção e iOPs do armazenamento local de NVMe é um bom caso de uso para processar ficheiros que serão carregados na sua base de dados e outros cenários onde os dados de origem podem ser recriados a partir do sistema de origem ou outros repositórios, como o armazenamento de Azure Blob ou O Lago de Dados Azure. [Série Lsv2](../../../virtual-machines/lsv2-series.md) Os VMs também podem rebentar o seu desempenho em disco até 30 minutos de cada vez.

Estas máquinas virtuais de 8 a 80 vCPU com 8 GiB de memória por vCPU e para cada 8 vCPUs há 1,92 TB de SSD NVMe. Isto significa que para o maior VM desta série, o [L80s_v2,](../../../virtual-machines/lsv2-series.md)há 80 vCPU e 640 BiB de memória com 10x1.92TB de armazenamento NVMe.  Existe uma relação memória-vCore consistente de 8 em todas estas máquinas virtuais.

O armazenamento NVMe é efémero, o que significa que os dados serão perdidos nestes discos se reiniciar a sua máquina virtual.

A série Lsv2 e Ls [suportam o armazenamento premium,](../../../virtual-machines/premium-storage-performance.md)mas não o armazenamento premium. A criação de uma cache local para aumentar os IOPs não é apoiada. 

> [!WARNING]
> Armazenar os seus ficheiros de dados no armazenamento efémero NVMe pode resultar em perda de dados quando o VM é transacionado. 

### <a name="constrained-vcores"></a>VCores constrangidos

As cargas de trabalho do Servidor SQL de alto desempenho muitas vezes precisam de maiores quantidades de memória, IO e produção sem as contagens de vCore mais altas. 

A maioria das cargas de trabalho da OLTP são bases de dados de aplicações impulsionadas por um grande número de transações menores. Com as cargas de trabalho da OLTP, apenas uma pequena quantidade dos dados é lida ou modificada, mas os volumes de transações impulsionadas pelas contagens dos utilizadores são muito mais elevados. É importante ter a memória do SQL Server disponível para planos de cache, armazenar dados recentemente acedidos para desempenho, e garantir que as leituras físicas podem ser lidas na memória rapidamente. 

Estes ambientes OLTP precisam de maiores quantidades de memória, armazenamento rápido e a largura de banda de I/O necessária para funcionar da melhor forma. 

De forma a manter este nível de desempenho sem os custos de licenciamento do SQL Server mais elevados, o Azure oferece tamanhos VM com [contagens de VCPU restritas.](../../../virtual-machines/constrained-vcpu.md) 

Isto ajuda a controlar os custos de licenciamento reduzindo os vCores disponíveis, mantendo a mesma memória, armazenamento e largura de banda de I/O da máquina virtual principal.

A contagem de vCPU pode ser limitada a metade a um quarto do tamanho original de VM. Reduzindo os vCores disponíveis para a máquina virtual, irá alcançar rácios de memória-vCore mais elevados.

Estes novos tamanhos VM têm um sufixo que especifica o número de vCPUs ativos para facilitar a sua identificação. 

Por exemplo, o [M64-32ms](../../../virtual-machines/constrained-vcpu.md) requer o licenciamento de apenas 32 SQL Server vCores com a memória, IO, e produção dos [M64ms](../../../virtual-machines/m-series.md) e o [M64-16ms](../../../virtual-machines/constrained-vcpu.md) requer licenciamento apenas 16 vCores.  Embora enquanto o [M64-16ms](../../../virtual-machines/constrained-vcpu.md) tenha um quarto do custo de licenciamento do SQL Server dos M64ms, o custo de computação da máquina virtual será o mesmo.

> [!NOTE] 
> - As cargas de trabalho de armazém de dados médios a grandes ainda podem beneficiar de [VMs vCore limitados,](../../../virtual-machines/constrained-vcpu.md)mas as cargas de trabalho do armazém de dados são geralmente caracterizadas por menos utilizadores e processos que abordam quantidades maiores de dados através de planos de consulta que funcionam em paralelo. 
> - O custo do cálculo, que inclui o licenciamento do sistema operativo, permanecerá o mesmo que a máquina virtual principal. 

## <a name="storage-guidance"></a>Orientações de armazenamento

Para testar detalhadamente o desempenho do SQL Server em Azure Virtual Machines com referências TPC-E e TPC-C, consulte o desempenho do blog [Otimize OLTP](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). 

Recomenda-se cache de bolhas Azure com SSDs premium para todas as cargas de trabalho de produção. 

> [!WARNING]
> Os HDDs standard e os SSDs têm latências e largura de banda variadas e só são recomendados para cargas de trabalho dev/teste. As cargas de trabalho de produção devem utilizar SSDs premium.

Além disso, recomendamos que crie a sua conta de armazenamento Azure no mesmo centro de dados que as máquinas virtuais do seu SQL Server para reduzir os atrasos de transferência. Ao criar uma conta de armazenamento, desative a geo-replicação, uma vez que a ordem de escrita consistente em vários discos não é garantida. Em vez disso, considere configurar uma tecnologia de recuperação de desastres do SQL Server entre dois centros de dados Azure. Para obter mais informações, consulte [Elevada Disponibilidade e Recuperação Após Desastre do SQL Server em Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="disks-guidance"></a>Orientação de discos

Existem três tipos principais de discos em máquinas virtuais Azure:

* **Disco DE SO**: Quando criar uma máquina virtual Azure, a plataforma irá anexar pelo menos um disco (rotulado como unidade **C)** ao VM para o disco do seu sistema operativo. Este disco é um VHD armazenado como uma bolha de página no armazenamento.
* **Disco temporário**: As máquinas virtuais Azure contêm outro disco chamado disco temporário (rotulado como **D**: unidade). Este é um disco no nó que pode ser usado para o espaço de risco.
* **Discos de dados**: Também pode anexar discos adicionais à sua máquina virtual como discos de dados, e estes serão armazenados no armazenamento como bolhas de página.

As seguintes secções descrevem recomendações para a utilização destes discos diferentes.

### <a name="operating-system-disk"></a>Disco do sistema operativo

Um disco de sistema operativo é um VHD que pode iniciar e montar como uma versão em execução de um sistema operativo e é rotulado como a unidade **C.**

A política de cache predefinida no disco do sistema operativo é **Ler/Escrever**. Para aplicações sensíveis ao desempenho, recomendamos que utilize discos de dados em vez do disco do sistema operativo. Consulte a secção em Discos de Dados abaixo.

### <a name="temporary-disk"></a>Disco temporário

A unidade de armazenamento temporária, rotulada como unidade **D,** não é persistido ao armazenamento de Azure Blob. Não guarde os ficheiros de base de dados do utilizador ou ficheiros de registo de transações de utilizador no **D**: unidade.

Coloque o TempDB na unidade SSD local `D:\` para as cargas críticas do SQL Server (depois de escolher o tamanho VM correto). Se criar o VM a partir do portal Azure ou dos modelos de quickstart Azure e [colocar o Temp DB no Disco Local,](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)então não precisa de mais ação; para todos os outros casos siga os passos no blog para  [usar SSDs para armazenar TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-TempDB-and-buffer-pool-extensions/) para evitar falhas após o reinício. Se a capacidade da unidade local não for suficiente para o seu tamanho DeP DB, em seguida, coloque o Temp DB numa piscina de armazenamento [listrada](../../../virtual-machines/premium-storage-performance.md) em discos SSD premium com [cache apenas de leitura](../../../virtual-machines/premium-storage-performance.md#disk-caching).

Para VMs que suportam SSDs premium, também pode armazenar TempDB num disco que suporta SSDs premium com cache de leitura ativado.


### <a name="data-disks"></a>Discos de dados

* **Utilize discos SSD premium para ficheiros de dados e registos**: Se não estiver a utilizar a desmontagem de discos, utilize dois discos SSD premium onde um disco contenha o ficheiro de registo e o outro contenha os dados. Cada SSD premium fornece uma série de IOPS e largura de banda (MB/s) dependendo do seu tamanho, conforme descrito no artigo, [Selecione um tipo](../../../virtual-machines/disks-types.md)de disco . Se estiver a utilizar uma técnica de striping de discos, como espaços de armazenamento, obtém o melhor desempenho com duas piscinas, uma para o ficheiro de registo e outra para os ficheiros de dados. No entanto, se planeia utilizar as instâncias de cluster failover do SQL Server (FCI), tem de configurar uma piscina ou utilizar [ações de ficheiros premium.](failover-cluster-instance-premium-file-share-manually-configure.md)

   > [!TIP]
   > - Para obter resultados de testes em várias configurações de disco e carga de trabalho, consulte o seguinte post de blog: [Diretrizes de configuração de armazenamento para servidor SQL em Máquinas Virtuais Azure](/archive/blogs/sqlserverstorageengine/storage-configuration-guidelines-for-sql-server-on-azure-vm).
   > - Para um desempenho crítico da missão para os SqL Servers que necessitem de ~ 50.000 IOPS, considere substituir discos de 10 -P30 por um Ultra SSD. Para mais informações, consulte o seguinte post de blog: [Desempenho crítico da missão com Ultra SSD](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > Ao providenciar um SQL Server VM no portal, tem a opção de editar a sua configuração de armazenamento. Dependendo da sua configuração, o Azure configura um ou mais discos. Vários discos são combinados numa única piscina de armazenamento com striping. Tanto os ficheiros de dados como os ficheiros de registo residem juntos nesta configuração. Para obter mais informações, consulte [a configuração de Armazenamento para VMs do servidor SQL](storage-configuration.md).

* **Tiragem do** disco : Para obter mais produção, pode adicionar discos de dados adicionais e utilizar a desmontagem do disco. Para determinar o número de discos de dados, é necessário analisar o número de IOPS e largura de banda necessárias para o(s) ficheiros de registo e para os seus dados e ficheiros TempDB. Note que diferentes tamanhos de VM têm limites diferentes no número de IOPs e largura de banda suportada, ver as tabelas no tamanho IOPS por [VM](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Utilize as seguintes diretrizes:

  * Para windows 8/Windows Server 2012 ou posterior, utilize [os Espaços de Armazenamento](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)) com as seguintes orientações:

      1. Deslohe o interleave (tamanho das listras) a 64 KB (65.536 bytes) para cargas de trabalho OLTP e 256 KB (262.144 bytes) para cargas de trabalho de armazenamento de dados para evitar o impacto do desempenho devido a desalinhamento da partição. Isto tem de ser definido com o PowerShell.
      2. Definir contagem de colunas = número de discos físicos. Utilize o PowerShell ao configurar mais de 8 discos (não o Server Manager UI). 

    Por exemplo, o seguinte PowerShell cria uma nova piscina de armazenamento com o tamanho interleave a 64 KB e o número de colunas iguais à quantidade de disco físico na piscina de armazenamento:

    ```powershell
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
    
    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" `
        -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
        -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
        –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
        -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
        -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Para o Windows 2008 R2 ou mais cedo, pode utilizar discos dinâmicos (volumes listrados de OS) e o tamanho das listras é sempre de 64 KB. Esta opção é depreciada a partir do Windows 8/Windows Server 2012. Para obter informações, consulte a declaração de suporte no [Serviço de Disco Virtual que está em transição para a API de Gestão de Armazenamento do Windows](/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api).

  * Se estiver a utilizar [espaços de armazenamento diretos (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) com [instâncias de cluster de failover do servidor SQL,](failover-cluster-instance-storage-spaces-direct-manually-configure.md)tem de configurar uma única piscina. Embora possam ser criados volumes diferentes nesse único pool, todos eles partilharão as mesmas características, como a mesma política de caching.

  * Determine o número de discos associados à sua piscina de armazenamento com base nas suas expectativas de carga. Tenha em mente que diferentes tamanhos de VM permitem diferentes números de discos de dados anexados. Para obter mais informações, consulte [tamanhos para máquinas virtuais.](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

  * Se não estiver a utilizar SSDs premium (cenários dev/teste), a recomendação é adicionar o número máximo de discos de dados suportados pelo [seu tamanho VM](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e utilizar a tira do disco.

* **Política de Caching**: Note as seguintes recomendações para a política de caching dependendo da sua configuração de armazenamento.

  * Se estiver a utilizar discos separados para ficheiros de dados e registos, ative a leitura dos discos de dados que hospedam os seus ficheiros de dados e ficheiros de dados TempDB. Isto pode resultar num benefício significativo para o desempenho. Não ative o cache no disco que mantém o ficheiro de registo, pois isto causa uma diminuição menor do desempenho.

  * Se estiver a utilizar o striping de disco numa única piscina de armazenamento, a maioria das cargas de trabalho beneficiarão da leitura do cache. Se tiver conjuntos de armazenamento separados para os ficheiros de registo e dados, ative a leitura apenas no conjunto de armazenamento para os ficheiros de dados. Em certas cargas de trabalho de escrita pesada, um melhor desempenho pode ser alcançado sem caching. Isto só pode ser determinado através de testes.

  * As recomendações anteriores aplicam-se aos SSDs premium. Se não estiver a utilizar SSDs premium, não ative qualquer cache em quaisquer discos de dados.

  * Para obter instruções sobre a configuração do cache do disco, consulte os seguintes artigos. Para o modelo de implementação clássico (ASM) consulte: [Set-AzureOSDisk](/previous-versions/azure/jj152847(v=azure.100)) e [Set-AzureDataDisk](/previous-versions/azure/jj152851(v=azure.100)). Para o modelo de implementação do Gestor de Recursos Azure, consulte: [Set-AzOSDisk](/powershell/module/az.compute/set-azvmosdisk) e [Set-AzVMDataDisk](/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Pare o serviço SQL Server ao alterar a definição de cache dos discos Azure Virtual Machines para evitar a possibilidade de qualquer corrupção na base de dados.

* **Tamanho da unidade de atribuição NTFS**: Ao formatar o disco de dados, recomenda-se que utilize um tamanho de unidade de atribuição de 64 KB para ficheiros de dados e registos, bem como o TempDB. Se o TempDB for colocado no disco temporário (D:\ unidade) o desempenho obtido aproveitando esta unidade supera a necessidade de um tamanho de unidade de atribuição de 64 KB. 

* **Boas práticas de gestão do disco**: Ao remover um disco de dados ou alterar o seu tipo de cache, pare o serviço SQL Server durante a alteração. Quando as definições de cache são alteradas no disco OS, O Azure para o VM, altera o tipo de cache e reinicia o VM. Quando as definições de cache de um disco de dados são alteradas, o VM não é parado, mas o disco de dados é desligado do VM durante a alteração e, em seguida, recolocado.

  > [!WARNING]
  > A não paragem do serviço SQL Server durante estas operações pode causar corrupção na base de dados.


## <a name="io-guidance"></a>Orientação de I/O

* Os melhores resultados com SSDs premium são alcançados quando você paraleliza a sua aplicação e pedidos. Os SSDs premium são projetados para cenários em que a profundidade da fila IO é maior do que 1, por isso verá pouco ou nenhum ganho de desempenho para pedidos de série de rosca única (mesmo que sejam intensivos de armazenamento). Por exemplo, isto pode ter impacto nos resultados dos testes de análise de desempenho, tais como SQLIO.

* Considere usar a [compressão da página da base de dados,](/sql/relational-databases/data-compression/data-compression) pois pode ajudar a melhorar o desempenho das cargas de trabalho intensivas de I/O. No entanto, a compressão de dados pode aumentar o consumo de CPU no servidor de base de dados.

* Considere permitir a inicialização instantânea do ficheiro para reduzir o tempo necessário para a atribuição inicial de ficheiros. Para tirar partido da inicialização instantânea do ficheiro, concede à conta de serviço SQL Server (MSSQLSERVER) SE_MANAGE_VOLUME_NAME e adicioná-la à política de segurança **de Tarefas de Manutenção** de Volume de Desempenho. Se estiver a utilizar uma imagem da plataforma SQL Server para o Azure, a conta de serviço predefinida (NT Service\MSSQLSERVER) não é adicionada à política de segurança de Tarefas de Manutenção de **Volume de Execução.** Por outras palavras, a inicialização instantânea do ficheiro não é ativada numa imagem da plataforma SQL Server Azure. Depois de adicionar a conta de serviço SQL Server à política de segurança **de Tarefas de Manutenção** de Volume de Desempenho, reinicie o serviço SQL Server. Pode haver considerações de segurança para a utilização desta funcionalidade. Para obter mais informações, consulte [a inicialização do ficheiro database](/sql/relational-databases/databases/database-instant-file-initialization).

* Esteja ciente de que **o autogrow** é considerado apenas uma contingência para um crescimento inesperado. Não gerencie os seus dados e faça login o crescimento no dia-a-dia com o autogrow. Se for utilizado um auto-sobrancelha, pré-cresça o ficheiro utilizando o interruptor Tamanho.

* Certifique-se de que **o autoshrink** está desativado para evitar sobrecargas desnecessárias que possam afetar negativamente o desempenho.

* Mover todas as bases de dados para discos de dados, incluindo bases de dados do sistema. Para obter mais informações, consulte [as bases de dados do Sistema de Movimento](/sql/relational-databases/databases/move-system-databases).

* Mover o registo de erros do SQL Server e rastrear os diretórios de ficheiros para discos de dados. Isto pode ser feito no SQL Server Configuration Manager clicando à direita na sua instância do SQL Server e selecionando propriedades. As definições de registo de erros e de ficheiros de rastrear podem ser alteradas no **separador Parâmetros de Arranque.** O Diretório de Despejo está especificado no separador **Avançado.** A imagem que se segue mostra onde procurar o parâmetro de arranque de registo de erro.

    ![SQL ErrorLog Screenshot](./media/performance-guidelines-best-practices/sql_server_error_log_location.png)

* Configurar a cópia de segurança predefinida e as localizações dos ficheiros de base de dados. Utilize as recomendações deste artigo e faça as alterações na janela de propriedades do Servidor. Para obter instruções, consulte [Ver ou Alterar as Localizações Predefinidos para Ficheiros de Dados e Registo (SQL Server Management Studio)](/sql/database-engine/configure-windows/view-or-change-the-default-locations-for-data-and-log-files). A imagem que se segue demonstra onde fazer estas alterações.

    ![Ficheiros de registo de dados e backup de dados SQL](./media/performance-guidelines-best-practices/sql_server_default_data_log_backup_locations.png)
* Ativar páginas bloqueadas para reduzir io e quaisquer atividades de paging. Para obter mais informações, consulte [Ativar as páginas de bloqueio na opção Memória (Windows)](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).

* Se estiver a executar o SQL Server 2012, instale o Service Pack 1 Cumulativo Update 10. Esta atualização contém a correção para um mau desempenho em I/O quando executa selecione para declaração de tabela temporária no SQL Server 2012. Para obter informações, consulte este [artigo base de conhecimento.](https://support.microsoft.com/kb/2958012)

* Considere comprimir quaisquer ficheiros de dados ao transferir para dentro/para fora do Azure.

## <a name="feature-specific-guidance"></a>Orientação específica para recursos

Algumas implementações podem obter benefícios adicionais de desempenho usando técnicas de configuração mais avançadas. A lista a seguir destaca algumas funcionalidades do SQL Server que podem ajudá-lo a obter um melhor desempenho:

### <a name="back-up-to-azure-storage"></a>Voltar para o Azure Storage
Ao realizar backups para o SQL Server em execução em Máquinas Virtuais Azure, pode utilizar [backup do servidor SQL para URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url). Esta funcionalidade está disponível a partir do SQL Server 2012 SP1 CU2 e recomendada para fazer o backup dos discos de dados anexados. Quando fizer backup/restaurar para/a partir do Azure Storage, siga as recomendações fornecidas no [SQL Server Backup para URL Best Practices e Troubleshooting and Restore from Backups Armazenados no Armazenamento Azure](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting). Também pode automatizar estas cópias de segurança utilizando [cópias de segurança automatizadas para O Servidor SQL em Máquinas Virtuais Azure](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md).

Antes do SQL Server 2012, pode utilizar [backup do servidor SQL para a ferramenta Azure](https://www.microsoft.com/download/details.aspx?id=40740). Esta ferramenta pode ajudar a aumentar a produção de backup usando vários alvos de listras de backup.

### <a name="sql-server-data-files-in-azure"></a>Ficheiros de dados do servidor SQL em Azure

Esta nova funcionalidade, [SQL Server Data Files in Azure,](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure)está disponível a partir do SQL Server 2014. Executar o SQL Server com ficheiros de dados em Azure demonstra características de desempenho comparáveis como a utilização de discos de dados Azure.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Instância de cluster de failover e espaços de armazenamento

Se estiver a utilizar espaços de armazenamento, ao adicionar nós ao cluster na página **de Confirmação,** limpe a caixa de verificação com a etiqueta **Adicionar todo o armazenamento elegível ao cluster**. 

![Armazenamento elegível descheck](./media/performance-guidelines-best-practices/uncheck-eligible-cluster-storage.png)

Se estiver a utilizar espaços de armazenamento e não desmarcar **Adicione todo o armazenamento elegível ao cluster, o** Windows destaca os discos virtuais durante o processo de agrupamento. Como resultado, não aparecem no Disk Manager ou No Explorer até que os espaços de armazenamento sejam removidos do cluster e religados utilizando o PowerShell. Os Espaços de Armazenamento agrulem vários discos para piscinas de armazenamento. Para mais informações, consulte [os Espaços de Armazenamento.](/windows-server/storage/storage-spaces/overview)

## <a name="multiple-instances"></a>Múltiplas instâncias 

Considere as seguintes boas práticas ao implementar várias instâncias do SQL Server numa única máquina virtual: 

- Desaperte a memória máxima do servidor para cada instância do SQL Server, certificando-se de que sobrou memória para o sistema operativo. Certifique-se de atualizar as restrições de memória para as instâncias do SQL Server se alterar a quantidade de memória que é atribuída à máquina virtual. 
- Tenha LUNs separados para dados, registos e TempDB, uma vez que todos eles têm diferentes padrões de carga de trabalho e você não quer que eles impactem uns aos outros. 
- Teste minuciosamente o seu ambiente sob cargas de trabalho pesadas semelhantes à produção para garantir que consegue lidar com a capacidade máxima de carga de trabalho dentro da sua aplicação SLAs. 

Os sinais de sistemas sobrecarregados podem incluir, mas não se limitam a, exaustão do fio do trabalhador, tempos de resposta lentos e/ou memória do sistema do sistema de despachante paralisado. 



## <a name="collect-performance-baseline"></a>Recolher linha de base de desempenho

Para uma abordagem mais prescritiva, recolha contadores de desempenho usando PerfMon/LogMan e capture estatísticas de espera do SQL Server para entender melhor as pressões gerais e potenciais estrangulamentos do ambiente de origem. 

Comece por recolher o CPU, a memória, o [IOPS,](../../../virtual-machines/premium-storage-performance.md#iops) [o rendimento](../../../virtual-machines/premium-storage-performance.md#throughput)e a [latência](../../../virtual-machines/premium-storage-performance.md#latency) da carga de trabalho de origem em momentos de pico após a [lista de verificação](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist)de desempenho da aplicação . 

Recolha dados durante horas de ponta, tais como cargas de trabalho durante o seu típico dia útil, mas também outros processos de alta carga, como o processamento de fim de dia e cargas de trabalho ETL de fim de semana. Considere aumentar os seus recursos para cargas de trabalho atipicamente pesadas, como o processamento no final do trimestre, e, em seguida, escalar feito uma vez que a carga de trabalho esteja concluída. 

Utilize a análise de desempenho para selecionar o [Tamanho VM](../../../virtual-machines/sizes-memory.md) que pode escalar para os requisitos de desempenho da sua carga de trabalho.


### <a name="iops-and-throughput"></a>IOPS e Produção

O desempenho do SQL Server depende fortemente do subsistema de E/S. A menos que a sua base de dados se encaixe na memória física, o SQL Server traz constantemente páginas de base de dados dentro e fora da piscina tampão. Os ficheiros de dados do SQL Server devem ser tratados de forma diferente. O acesso aos ficheiros de registo é sequencial, exceto quando uma transação precisa de ser revertida onde os ficheiros de dados, incluindo o TempDB, são acedidos aleatoriamente. Se tiver um subsistema de E/S lento, os seus utilizadores poderão experimentar problemas de desempenho, tais como tempos de resposta lentos e tarefas que não são concluídas devido a intervalos de tempo. 

As máquinas virtuais do Azure Marketplace têm ficheiros de registo num disco físico que é separado dos ficheiros de dados por padrão. Os ficheiros de dados tempDB contam e o tamanho cumprem as melhores práticas e são direcionados para o efémero D:/ Conduzir.. 

Os seguintes contadores PerfMon podem ajudar a validar a produção de IO exigida pelo seu SqL Server: 
* **\LogicalDisk\Disk Reads/Sec** (ler e escrever IOPS)
* **\LogicalDisk\Disk Writes/Sec** (ler e escrever IOPS) 
* **\LogicalDisk\Disk Bytes/Sec** (requisitos de produção para os ficheiros dados, log e TempDB)

Utilizando requisitos de IOPS e de produção nos níveis máximos, avalie os tamanhos de VM que correspondam à capacidade das suas medições. 

Se a sua carga de trabalho necessitar de 20 K de IOPS e 10K de escrita IOPS, pode escolher E16s_v3 (com até 32 K em cache e 25600 IOPS não colados) ou M16_s (com até 20 K em cache e 10K IOPS sem cochados) com 2 discos P30 sem pressão com espaços de armazenamento. 

Certifique-se de compreender tanto os requisitos de produção como o IOPS da carga de trabalho, uma vez que os VMs têm limites de escala diferentes para IOPS e produção.

### <a name="memory"></a>Memória

Rastreia tanto a memória externa utilizada pelo SO como a memória utilizada internamente pelo SQL Server. Identificar a pressão para qualquer um dos componentes ajudará a dimensionar as máquinas virtuais e a identificar oportunidades de afinação. 

Os seguintes contadores PerfMon podem ajudar a validar a saúde da memória de uma máquina virtual SQL Server: 
* [\Memory\MBytes disponíveis](/azure/monitoring/infrastructure-health/vmhealth-windows/winserver-memory-availmbytes)
* [\SQLServer:Memory Manager\Target Server Memory (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer:Memory Manager\Total Memória do Servidor (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer:Buffer Manager\Preguiçosas escreve/seg](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer:Buffer Manager\Page esperança de vida](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)

### <a name="compute--processing"></a>Computação / Processamento

O cálculo em Azure é gerido de forma diferente das instalações. Os servidores no local são construídos para durar vários anos sem uma atualização devido à sobrecarga de gestão e ao custo de aquisição de novo hardware. A virtualização atenua alguns destes problemas, mas as aplicações são otimizadas para tirar o máximo partido do hardware subjacente, o que significa que qualquer alteração significativa no consumo de recursos requer reequilíbrio de todo o ambiente físico. 

Este não é um desafio em Azure onde uma nova máquina virtual numa série diferente de hardware, e mesmo numa região diferente, é fácil de alcançar. 

Em Azure, pretende aproveitar o máximo possível de recursos de máquinas virtuais, pelo que as máquinas virtuais Azure devem ser configuradas para manter o CPU médio o mais alto possível sem afetar a carga de trabalho. 

Os seguintes contadores PerfMon podem ajudar a validar a saúde computacional de uma máquina virtual SQL Server:
* **\Tempo do processador informação (_Total) \%**
* **\Tempo do processador (sqlservr) \%**

> [!NOTE] 
> Idealmente, tente usar 80% do seu cálculo, com picos acima de 90% mas não atingindo 100% durante qualquer período de tempo sustentado. Fundamentalmente, você só quer providenciar o cálculo que a aplicação precisa e, em seguida, planeja escalar para cima ou para baixo como o negócio exige. 

## <a name="next-steps"></a>Passos seguintes

Para obter as melhores práticas de segurança, consulte [considerações de segurança para o SQL Server em Azure Virtual Machines](security-considerations-best-practices.md).

Reveja outros artigos da Máquina Virtual do SqL Server [no SQL Server na Visão Geral das Máquinas Virtuais Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). Se tiver dúvidas sobre máquinas virtuais do SQL Server, veja as [Perguntas Mais Frequentes](frequently-asked-questions-faq.md).
