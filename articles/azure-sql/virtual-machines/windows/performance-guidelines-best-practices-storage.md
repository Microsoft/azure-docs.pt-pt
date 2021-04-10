---
title: 'Armazenamento: Melhores práticas de desempenho & diretrizes'
description: Fornece boas práticas e diretrizes de armazenamento para otimizar o desempenho do seu SQL Server na Azure Virtual Machine (VM).
services: virtual-machines-windows
documentationcenter: na
author: dplessMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: jroth
ms.openlocfilehash: 001a9a15c259d0b0d73eec9c9a39ad7c27f26721
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572520"
---
# <a name="storage-performance-best-practices-for-sql-server-on-azure-vms"></a>Armazenamento: Melhores práticas de desempenho para SQL Server em VMs Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo fornece as melhores práticas e diretrizes de armazenamento para otimizar o desempenho do seu SQL Server em Azure Virtual Machines (VMs).

Existe tipicamente uma compensação entre otimizar os custos e otimizar o desempenho. Esta série de boas práticas de desempenho está focada em obter o *melhor* desempenho para SQL Server em Azure Virtual Machines. Se a sua carga de trabalho for menos exigente, poderá não necessitar de todas as otimizações recomendadas. Considere as suas necessidades de desempenho, custos e padrões de carga de trabalho à medida que avalia estas recomendações.

Para saber mais, consulte os outros artigos desta série: [Performance Checklist,](performance-guidelines-best-practices-checklist.md) [VM size](performance-guidelines-best-practices-vm-size.md)e [Collect baseline](performance-guidelines-best-practices-collect-baseline.md). 

## <a name="checklist"></a>Lista de Verificação

Reveja a seguinte lista de verificação para obter uma breve visão geral das melhores práticas de armazenamento que o resto do artigo cobre mais detalhadamente: 

- Monitorize a aplicação e determine os requisitos de largura de banda de armazenamento e latência para os ficheiros de [dados,](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) registos e temporários do SQL Server antes de escolher o tipo de disco. 
- Para otimizar o desempenho do armazenamento, planeie o maior IOPS disponível e utilize o cache de dados como uma característica de desempenho para leituras de dados, evitando a [cobertura de máquinas virtuais e discos.](../../../virtual-machines/premium-storage-performance.md#throttling)
- Coloque os ficheiros de dados, registos e temporários em unidades separadas.
    - Para a unidade de dados, utilize [apenas discos P30 e P40 premium](../../../virtual-machines/disks-types.md#premium-ssd) para garantir a disponibilidade de suporte à cache
    - Para o plano de log drive para capacidade e desempenho de teste versus custo enquanto avalia os [discos P30 - P80 premium](../../../virtual-machines/disks-types.md#premium-ssd)
      - Se for necessária latência de armazenamento submilissegundo, utilize [discos ultra Azure](../../../virtual-machines/disks-types.md#ultra-disk) para o registo de transações. 
      - Para as implementações de máquinas virtuais da série M, considere [escrever acelerador](../../../virtual-machines/how-to-enable-write-accelerator.md) sobre a utilização de discos ultra Azure.
    - Coloque [a temperatura](/sql/relational-databases/databases/tempdb-database) na unidade efémera local SSD para a maioria das `D:\` cargas de trabalho do SQL Server depois de escolher o tamanho VM ideal. 
      - Se a capacidade da unidade local não for suficiente para a temporária, considere dimensionar o VM. Consulte [as políticas de caching de ficheiros de dados](#data-file-caching-policies) para obter mais informações.
- Stripe vários discos de dados Azure usando [espaços de armazenamento](/windows-server/storage/storage-spaces/overview) para aumentar a largura de banda de I/S até os limites de IOPS e limites de produção da máquina virtual alvo.
- Definir [o cache do anfitrião](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) para ler apenas para discos de ficheiros de dados.
- Definir [o cache do anfitrião para](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) nenhum para discos de ficheiro de registo.
    - Não ative a leitura/escrita em discos que contenham ficheiros SQL Server. 
    - Pare sempre o serviço SQL Server antes de alterar as definições de cache do seu disco.
- Para o desenvolvimento e teste de cargas de trabalho, e arquivo de backup a longo prazo considere a utilização de armazenamento padrão. Não é aconselhável utilizar o HDD/SDD standard para cargas de trabalho de produção.
- [O rebentamento do disco baseado em crédito](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20) só deve ser considerado para cargas de trabalho dev/teste mais pequenos e sistemas departameiros.
- Provisione a conta de armazenamento na mesma região que o SQL Server VM. 
- Desative o armazenamento geo-redundante da Azure (geo-replicação) e utilize lRS (armazenamento redundante local) na conta de armazenamento.
- Formate o seu disco de dados para utilizar o tamanho da unidade de atribuição de 64 KB para todos os ficheiros de dados colocados numa unidade diferente da unidade temporária `D:\` (que tem um padrão de 4 KB). Os VMs do Servidor SQL implantados através do Azure Marketplace vêm com discos de dados formatados com o tamanho da unidade de atribuição e intercalam para o conjunto de armazenamento definido para 64 KB. 

Para comparar a lista de verificação de armazenamento com as outras, consulte a lista completa de [boas práticas](performance-guidelines-best-practices-checklist.md)de desempenho . 

## <a name="overview"></a>Descrição Geral

Para encontrar a configuração mais eficaz para as cargas de trabalho do SQL Server num VM Azure, comece por [medir o desempenho de armazenamento da sua aplicação de negócio](performance-guidelines-best-practices-collect-baseline.md#storage). Uma vez conhecidos os requisitos de armazenamento, selecione uma máquina virtual que suporte o IOPS necessário e produção com a relação memória-vCore apropriada. 

Escolha um tamanho VM com escalabilidade de armazenamento suficiente para a sua carga de trabalho e uma mistura de discos (geralmente numa piscina de armazenamento) que satisfaçam os requisitos de capacidade e desempenho do seu negócio. 

O tipo de disco depende tanto do tipo de ficheiro que está alojado no disco como dos seus requisitos de desempenho máximo.

> [!TIP]
> O fornecimento de um SQL Server VM através do portal Azure ajuda a guiá-lo através do processo de configuração de armazenamento e implementa a maioria das melhores práticas de armazenamento, tais como a criação de conjuntos de armazenamento separados para os seus ficheiros de dados e registos, direcionando a temperatura para a `D:\` unidade e permitindo a política de caching ideal. Para obter mais informações sobre o fornecimento e configuração do armazenamento, consulte a [configuração de armazenamento SQL VM](storage-configuration.md). 

## <a name="vm-disk-types"></a>Tipos de disco de VM

Tem uma escolha no nível de desempenho para os seus discos. Os tipos de discos geridos disponíveis como armazenamento subjacente (listados pelo aumento das capacidades de desempenho) são unidades de disco rígido padrão (HDD), SSDs padrão, unidades de estado sólido premium (SSD) e discos ultra. 

O desempenho do disco aumenta com a capacidade, agrupado por [etiquetas de disco premium](../../../virtual-machines/disks-types.md#premium-ssd) como o P1 com 4 GiB de espaço e 120 IOPS para o P80 com 32 TiB de armazenamento e 20.000 IOPS. O armazenamento premium suporta uma cache de armazenamento que ajuda a melhorar a leitura e a escrever o desempenho para algumas cargas de trabalho. Para obter mais informações, consulte [a visão geral dos discos geridos.](../../../virtual-machines/managed-disks-overview.md) 

Existem também três tipos principais de [discos](../../../virtual-machines/managed-disks-overview.md#disk-roles) a considerar para o seu SQL Server no Azure VM - um disco DE, um disco temporário e os seus discos de dados. Escolha cuidadosamente o que está armazenado na unidade do sistema operativo `(C:\)` e na unidade temporária efémera `(D:\)` . 

### <a name="operating-system-disk"></a>Disco do sistema operativo

Um disco de sistema operativo é um VHD que pode ser iniciado e montado como uma versão em execução de um sistema operativo e é rotulado como a `C:\` unidade. Quando criar uma máquina virtual Azure, a plataforma irá anexar pelo menos um disco ao VM para o disco do sistema operativo. A `C:\` unidade é a localização predefinida para instalações de aplicações e configuração de ficheiros. 

Para a produção de ambientes SQL Server, não utilize o disco do sistema operativo para ficheiros de dados, ficheiros de registo, registos de erros. 

### <a name="temporary-disk"></a>Disco temporário

Muitas máquinas virtuais Azure contêm outro tipo de disco chamado disco temporário (rotulado como `D:\` unidade). Dependendo da série de máquinas virtuais e do tamanho, a capacidade deste disco variará. O disco temporário é efémero, o que significa que o armazenamento do disco é recriado (como em, é translocado e atribuído novamente), quando a máquina virtual é reiniciada, ou transferida para um hospedeiro diferente (para [cura de serviço,](/troubleshoot/azure/virtual-machines/understand-vm-reboot)por exemplo). 

A unidade de armazenamento temporário não é persistido para o armazenamento remoto e, portanto, não deve armazenar ficheiros de base de dados de utilizadores, ficheiros de registo de transações ou qualquer coisa que deve ser preservada. 

Coloque a temperatura na unidade temporária local do SSD `D:\` para cargas de trabalho do SQL Server, a menos que o consumo de cache local seja uma preocupação. Se estiver a utilizar uma máquina virtual que [não tenha um disco temporário,](../../../virtual-machines/azure-vms-no-temp-disk.md) então é aconselhável colocar a temperatura no seu próprio disco isolado ou piscina de armazenamento com caching definido apenas para ler. Para saber mais, consulte [as políticas de caching de dados temporários](performance-guidelines-best-practices-storage.md#data-file-caching-policies).

### <a name="data-disks"></a>Discos de dados

Os discos de dados são discos de armazenamento remoto que são muitas [vezes criados em piscinas de armazenamento,](/windows-server/storage/storage-spaces/overview) de forma a exceder a capacidade e o desempenho que qualquer disco poderia oferecer à máquina virtual.

Anexe o número mínimo de discos que satisfaz o IOPS, a produção e os requisitos de capacidade da sua carga de trabalho. Não exceda o número máximo de discos de dados da menor máquina virtual a que pretende redimensionar.

Coloque os dados e faça login ficheiros em discos de dados previstos para melhor atender aos requisitos de desempenho. 

Formate o seu disco de dados para utilizar o tamanho da unidade de atribuição de 64 KB para todos os ficheiros de dados colocados numa unidade diferente da unidade temporária `D:\` (que tem um padrão de 4 KB). Os VMs do Servidor SQL implantados através do Azure Marketplace vêm com discos de dados formatados com o tamanho da unidade de atribuição e intercalam para o conjunto de armazenamento definido para 64 KB. 

## <a name="premium-disks"></a>Discos premium

Utilize discos SSD premium para ficheiros de dados e registos para a produção de cargas de trabalho do SQL Server. Premium SSD IOPS e largura de banda varia em com base no tamanho e tipo do [disco.](../../../virtual-machines/disks-types.md) 

Para cargas de trabalho de produção, utilize os discos P30 e/ou P40 para ficheiros de dados do SQL Server para garantir o suporte de cache e utilizar o P30 até P80 para ficheiros de registo de transações do SQL Server.  Para o melhor custo total de propriedade, comece com P30s (5000 IOPS/200 MBPS) para ficheiros de dados e registos e escolha apenas capacidades mais altas quando precisar de controlar a contagem de discos de máquina virtual.

Para cargas de trabalho OLTP, combine o IOPS alvo por disco (ou pool de armazenamento) com os seus requisitos de desempenho utilizando cargas de trabalho em horas de pico e nos `Disk Reads/sec`  +  `Disk Writes/sec` contadores de desempenho. Para o armazém de dados e reporte cargas de trabalho, combine a produção do alvo utilizando cargas de trabalho em momentos de pico e nos `Disk Read Bytes/sec`  +  `Disk Write Bytes/sec` . 

Utilize espaços de armazenamento para obter um desempenho ideal, configurar duas piscinas, uma para o ficheiro de registo e outra para os ficheiros de dados. Se não estiver a utilizar a tiragem de disco, utilize dois discos SSD premium mapeados para unidades separadas, onde uma unidade contém o ficheiro de registo e a outra contém os dados.

O [IOPS a provisionado e a produção](../../../virtual-machines/disks-types.md#premium-ssd) por disco que é usado como parte da sua piscina de armazenamento. As capacidades combinadas de IOPS e de produção dos discos são a capacidade máxima até aos limites de produção da máquina virtual.

A melhor prática é utilizar o menor número possível de discos, cumprindo os requisitos mínimos para iops (e produção) e capacidade. No entanto, o equilíbrio entre o preço e o desempenho tende a ser melhor com um grande número de pequenos discos em vez de um pequeno número de discos grandes.

### <a name="scaling-premium-disks"></a>Discos premium de escala

Quando um Disco Gerido Azure é implantado pela primeira vez, o nível de desempenho desse disco baseia-se no tamanho do disco a provisionado. Designe o nível de desempenho na implementação ou altere-o posteriormente, sem alterar o tamanho do disco. Se a procura aumentar, pode aumentar o nível de desempenho para atender às necessidades do seu negócio. 

A alteração do nível de desempenho permite que os administradores se preparem e satisfaçam uma maior procura sem depender da [explosão do disco](../../../virtual-machines/disk-bursting.md#credit-based-bursting). 

Utilize o desempenho mais elevado durante o tempo necessário, onde a faturação é projetada para atingir o nível de desempenho do armazenamento. Atualize o nível para corresponder aos requisitos de desempenho sem aumentar a capacidade. Volte ao nível original quando o desempenho extra já não for necessário.

Esta expansão de desempenho rentável e temporária é um forte argumento de utilização para eventos direcionados, tais como compras, testes de desempenho, eventos de formação e outras janelas breves onde é necessário um maior desempenho apenas a curto prazo. 

Para obter mais informações, consulte [os níveis de desempenho para discos geridos](../../../virtual-machines/disks-change-performance.md). 

## <a name="azure-ultra-disk"></a>Disco ultra azul

Se houver necessidade de tempos de resposta submilissegundos com latência reduzida, considere a utilização do [disco ultra Azure](../../../virtual-machines/disks-types.md#ultra-disk) para a unidade de registo do SQL Server, ou mesmo a unidade de dados para aplicações extremamente sensíveis à latência de E/O. 

O disco ultra pode ser configurado onde a capacidade e o IOPS podem escalar de forma independente. Com os administradores de discos ultra podem providenciar um disco com a capacidade, IOPS e requisitos de produção baseados nas necessidades de aplicação. 

O disco ultra não é suportado em todas as séries VM e tem outras limitações, tais como disponibilidade de região, redundância e suporte para Azure Backup. Para saber mais, consulte [o Uso de discos Ultra Azure](../../../virtual-machines/disks-enable-ultra-ssd.md) para obter uma lista completa de limitações. 

## <a name="standard-hdds-and-ssds"></a>HDDs e SSDs padrão

[Os HDDs](../../../virtual-machines/disks-types.md#standard-hdd) standard e os SSDs têm latências e largura de banda variadas e só são recomendados para cargas de trabalho dev/teste. As cargas de trabalho de produção devem utilizar SSDs premium. Se estiver a utilizar o SSD Standard (cenários dev/teste), a recomendação é adicionar o número máximo de discos de dados suportados pelo seu [tamanho VM](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json) e utilizar a tiragem de disco com espaços de armazenamento para obter o melhor desempenho.

## <a name="caching"></a>Colocação em cache

As máquinas virtuais que suportam o armazenamento premium podem tirar partido de uma funcionalidade adicional chamada Azure BlobCache ou caching hospedeiro para estender o IOPS e as capacidades de produção de uma máquina virtual. As máquinas virtuais ativadas tanto para armazenamento premium como para o armazenamento premium têm estes dois limites de largura de banda de armazenamento diferentes que podem ser usados em conjunto para melhorar o desempenho do armazenamento.

A produção de IOPS e MBps sem cache conta contra os limites de produção de disco não encaqueado de uma máquina virtual. Os limites máximos em cache fornecem um tampão adicional para leituras que ajudam a lidar com o crescimento e picos inesperados.

Ativar o tiaching premium sempre que a opção for suportada para melhorar significativamente o desempenho das leituras contra a unidade de dados sem custos adicionais. 

As leituras e as escritas para o Azure BlobCache (IOPS em cache e produção) não contam com os limites de IOPS e de produção não encabertos da máquina virtual.

> [!NOTE]
> O Caching do disco não é suportado para discos 4 TiB e maiores (P50 e maior). Se vários discos estiverem ligados ao seu VM, cada disco que seja menor que 4 TiB irá suportar o cache. Para obter mais informações, consulte [o cache do disco.](../../../virtual-machines/premium-storage-performance.md#disk-caching) 

### <a name="uncached-throughput"></a>Produção não escavada

O disco máximo iops e a produção são o limite máximo de armazenamento remoto que a máquina virtual pode manusear. Este limite é definido na máquina virtual e não é um limite para o armazenamento subjacente do disco. Este limite aplica-se apenas a I/O contra unidades de dados remotamente anexadas ao VM, não à I/O local contra a unidade temporária `D:\` (unidade) ou à unidade de SO.

A quantidade de IOPS não encadeado e produção disponível para um VM pode ser verificada na documentação da sua máquina virtual.

Por exemplo, a documentação da [série M](../../../virtual-machines/m-series.md) mostra que o rendimento máximo não abricado para o VM Standard_M8ms é de 5000 IOPS e 125 MBps de produção de disco não encaqueado. 

![Screenshot mostrando documentação de saída de disco sem arrochado da série M.](./media/performance-guidelines-best-practices/m-series-table.png)

Da mesma forma, pode ver que o Standard_M32ts suporta 20.000 IOPS de disco não encaqueado e 500 MBps sem saída. Este limite rege-se ao nível da máquina virtual, independentemente do armazenamento de disco premium subjacente.

Para obter mais informações, consulte [os limites não encatados e em cache.](../../../virtual-machines/linux/disk-performance-linux.md#virtual-machine-uncached-vs-cached-limits)


### <a name="cached-and-temp-storage-throughput"></a>Produção de armazenamento em cache e temporário

O limite máximo de produção em cache e de potência temporária é um limite separado do limite de produção não encatado na máquina virtual. O Azure BlobCache consiste numa combinação da memória de acesso aleatório do hospedeiro de máquina virtual e SSD anexada localmente. A unidade temporária `D:\` (unidade) dentro da máquina virtual também está hospedada neste SSD local.

O limite máximo de armazenamento em cache e temporário rege o I/O contra a unidade temporária local `D:\` (unidade) e o Azure BlobCache **apenas se** o cache do hospedeiro estiver ativado. 

Quando o caching é ativado no armazenamento premium, as máquinas virtuais podem escalar para além das limitações do armazenamento remoto VM IOPS e limites de produção.  

Apenas certas máquinas virtuais suportam tanto o armazenamento premium como o armazenamento premium (que precisa de ser verificado na documentação da máquina virtual). Por exemplo, a documentação da [série M](../../../virtual-machines/m-series.md) indica que tanto o armazenamento premium como o caching de armazenamento premium são suportados: 

![Screenshot mostrando suporte de armazenamento M-Series Premium.](./media/performance-guidelines-best-practices/m-series-table-premium-support.png)

Os limites da cache variarão em função do tamanho da máquina virtual. Por exemplo, o Standard_M8ms VM suporta 10000 discos em cache IOPS e 1000 MBps em cache com um tamanho total de cache de 793 GiB. Da mesma forma, o VM Standard_M32ts suporta 40000 discos em cache IOPS e 400 MBps em cache com um tamanho total de cache de 3174 GiB. 

![Screenshot mostrando documentação de saída de disco em cache da série M.](./media/performance-guidelines-best-practices/m-series-table-cached-temp.png)

Pode ativar manualmente o caching do anfitrião num VM existente. Pare todas as cargas de trabalho da aplicação e os serviços do SQL Server antes de serem feitas quaisquer alterações à política de caching da sua máquina virtual. A alteração de qualquer uma das definições de cache da máquina virtual resulta em que o disco-alvo seja desmontado e recolocado após a aplicação das definições.

### <a name="data-file-caching-policies"></a>Políticas de caching de ficheiros de dados

A sua política de caching de armazenamento varia consoante o tipo de ficheiros de dados do SQL Server que estão alojados na unidade. 

A tabela seguinte fornece um resumo das políticas de caching recomendadas com base no tipo de dados do SQL Server: 

|Disco do Servidor SQL |Recomendação |
|---------|---------|
| **Disco de dados** | Ativar `Read-only` o cache para os discos que hospedam ficheiros de dados do SQL Server. <br/> As leituras da cache serão mais rápidas do que as leituras não encaqueadas do disco de dados. <br/> IOPS sem produção e produção mais IOPS cached e produção produzirão o desempenho total possível disponível a partir da máquina virtual dentro dos limites dos VMs, mas o desempenho real variará em função da capacidade da carga de trabalho de usar a cache (relação de cache hit). <br/>|
|**Disco de registo de transações**|Desloque a política de cache `None` para os discos que hospedam o registo de transações.  Não existe qualquer benefício de desempenho em permitir o cache para o disco de registo de transações, e de facto ter um `Read-only` ou `Read/Write` cache ativado na unidade de registo pode degradar o desempenho das escritas contra a unidade e diminuir a quantidade de cache disponível para leituras na unidade de dados.  |
|**Disco operativo DE OS** | A política de caching padrão pode ser `Read-only` ou para a unidade de `Read/write` SO. <br/> Não é aconselhável alterar o nível de caching da unidade de so.  |
| **tempdb**| Se a temperatura não puder ser colocada na unidade efémera `D:\` por razões de capacidade, redimensione a máquina virtual para obter uma unidade efémera maior ou coloque a temperatura numa unidade de dados separada com `Read-only` caching configurada. <br/> A cache da máquina virtual e a unidade efémera utilizam o SSD local, por isso tenha isto em mente quando o tamanho como temporário I/O irá contar com os limites de IOPS em cache e de produção de máquinas virtuais quando hospedados na unidade efémera.| 
| | | 


Para saber mais, consulte [o cache do disco.](../../../virtual-machines/premium-storage-performance.md#disk-caching) 


## <a name="disk-striping"></a>Striping de disco

Analise a produção e largura de banda necessárias para os seus ficheiros de dados SQL para determinar o número de discos de dados, incluindo o ficheiro de registo e a temperatura. Os limites de produção e largura de banda variam de acordo com o tamanho de VM. Para saber mais, consulte o [tamanho do VM](../../../virtual-machines/sizes.md)

Adicione discos de dados adicionais e utilize a tiragem do disco para obter mais produção. Por exemplo, uma aplicação que precise de 12.000 IOPS e 180 MB/s pode usar três discos P30 listrados para entregar 15.000 IOPS e 600 MB/s. 

Para configurar a desmontagem do disco, consulte [a tiragem do disco](storage-configuration.md#disk-striping). 

## <a name="disk-capping"></a>Tampa de disco 

Existem limites de produção tanto no disco como no nível da máquina virtual. Os limites máximos de IOPS por VM e por disco diferem e são independentes uns dos outros.

As aplicações que consomem recursos para além destes limites serão estranguladas (também conhecidas como capped). Selecione uma máquina virtual e o tamanho do disco numa risca de disco que satisfaça os requisitos de aplicação e não enfrente limitações de limitação de tampa. Para endereçar a tampa, utilize o caching ou afinar a aplicação de modo a que seja necessária menos produção.

Por exemplo, uma aplicação que precisa de 12.000 IOPS e 180 MB/s: 
- Utilize o [Standard_M32ms](../../../virtual-machines/m-series.md) que tem uma produção máxima de disco não-apensado de 20.000 IOPS e 500 MBps.
- Stripe três discos P30 para entregar 15.000 IOPS e 600-MB/s.
- Utilize uma máquina virtual [Standard_M16ms](../../../virtual-machines/m-series.md) e utilize cache de hospedeiro para utilizar cache local sobre produção consumista. 

As máquinas virtuais configuradas para escalar em períodos de alta utilização devem providenciar armazenamento com IOPS e produção suficientes para suportar o tamanho máximo de VM, mantendo o número total de discos inferior ou igual ao número máximo suportado pelo menor VM SKU visado a ser utilizado.

Para obter mais informações sobre as limitações de cobertura do disco e utilizar o cache para evitar a tampa, consulte [a tampa do disco IO](../../../virtual-machines/disks-performance.md).

> [!NOTE] 
> Algumas tampas de disco podem ainda resultar num desempenho satisfatório para os utilizadores; sintonizar e manter cargas de trabalho em vez de redimensionar para um VM maior para equilibrar a gestão de custos e desempenho para o negócio. 


## <a name="write-acceleration"></a>Escrever Aceleração

Write Acceleration é uma funcionalidade de disco que só está disponível para as Máquinas Virtuais [série M](https://docs.microsoft.com/azure/virtual-machines/m-series) (VMs). O objetivo da Write Acceleration é melhorar a latência de I/O de escritas contra o Azure Premium Storage quando você precisa de um dígito único de latência I/O devido a cargas de trabalho críticas de OLTP ou ambientes de armazém de dados de alto volume. 

Utilize a Aceleração da Escrita para melhorar a latência da escrita para a unidade que hospeda os ficheiros de registo. Não utilize a Aceleração de Escrita para ficheiros de dados do SQL Server. 

Os discos de write Accelerator partilham o mesmo limite de IOPS que a máquina virtual. Os discos em anexo não podem exceder o limite de IOPS do acelerador de escrita para um VM.  

A tabela seguinte descreve o número de discos de dados e IOPS suportados por máquina virtual: 

| SKU da VM  | # Escreva discos aceleradores  | Escreva IOPS de disco de aceleração por VM  |
|---|---|---|
| M416ms_v2, M416s_v2  | 16  | 20 000  |
| M128ms, M128s  | 16  | 20 000  |
| M208ms_v2, M208s_v2  | 8  | 10000  |
| M64ms, M64ls, M64s  |  8 | 10000 |
| M32ms, M32ls, M32ts, M32s  | 4  | 5000  |
| M16ms, M16s  | 2 | 2500 |
| M8ms, M8s  | 1 | 1250 |

Existem várias restrições à utilização da Aceleração da Escrita. Para saber mais, consulte [restrições ao utilizar o Acelerador de Escrita.](../../../virtual-machines/how-to-enable-write-accelerator.md#restrictions-when-using-write-accelerator)


### <a name="comparing-to-azure-ultra-disk"></a>Comparando com o disco ultra Azure

A maior diferença entre a Write Acceleration e os discos Azure ultra é que a Write Acceleration é uma funcionalidade de máquina virtual disponível apenas para os discos M-Series e Azure ultra é uma opção de armazenamento. Write Acceleration é uma cache otimizada por escrita com as suas próprias limitações com base no tamanho da máquina virtual. Os discos Azure ultra são uma opção de armazenamento de disco de baixa latência para máquinas virtuais Azure. 

Se possível, utilize a Write Acceleration sobre discos ultra para o disco de registo de transações. Para máquinas virtuais que não suportam a Write Acceleration mas que requerem baixa latência no registo de transações, utilize discos ultra Azure. 

## <a name="monitor-storage-performance"></a>Monitorar o desempenho do armazenamento

Para avaliar as necessidades de armazenamento e determinar o bom desempenho do armazenamento, é preciso entender o que medir e o que esses indicadores significam. 

[IOPS (Entrada/Saída por segundo)](../../../virtual-machines/premium-storage-performance.md#iops) é o número de pedidos que a aplicação está a fazer para armazenamento por segundo. Meça o IOPS utilizando os contadores do Monitor de Desempenho `Disk Reads/sec` e `Disk Writes/sec` . As aplicações [OLTP (processamento de transações online)](/azure/architecture/data-guide/relational-data/online-transaction-processing) precisam de impulsionar iops mais elevados para obter o melhor desempenho. Aplicações como sistemas de processamento de pagamentos, compras online e sistemas de retalho de ponto de venda são todos exemplos de aplicações OLTP.

[O rendimento](../../../virtual-machines/premium-storage-performance.md#throughput) é o volume de dados que está a ser enviado para o armazenamento subjacente, muitas vezes medido por megabytes por segundo. Medir a produção com os balcões do Monitor de Desempenho `Disk Read Bytes/sec` e `Disk Write Bytes/sec` . [O armazenamento de dados](/azure/architecture/data-guide/relational-data/data-warehousing) é otimizado em torno de maximizar a produção sobre o IOPS. Aplicações como lojas de dados para análise, reporte, fluxos de trabalho ETL e outros alvos de inteligência empresarial são todos exemplos de aplicações de armazenamento de dados.

Os tamanhos das unidades de I/O influenciam as capacidades de IOPS e de produção, uma vez que os tamanhos de E/S mais pequenos produzem maiores IOPS e os tamanhos de E/O maiores produzem uma produção mais elevada. O SQL Server escolhe automaticamente o tamanho ideal de E/S. Para obter mais informações sobre, consulte [Otimize IOPS, produção e latência para as suas aplicações.](../../../virtual-machines/premium-storage-performance.md#optimize-iops-throughput-and-latency-at-a-glance) 

Existem métricas específicas do Azure Monitor que são inestimáveis para descobrir a cobertura ao nível da máquina virtual e do disco, bem como o consumo e a saúde da cache AzureBlob. Para identificar contadores chave para adicionar à sua solução de monitorização e painel de instrumentos do portal Azure, consulte [as métricas de utilização do Armazenamento](../../../virtual-machines/disks-metrics.md#storage-io-utilization-metrics). 

> [!NOTE]
> Atualmente, o Azure Monitor não oferece métricas de nível de disco para a unidade temporária efémera `(D:\)` . VM Cached IOPS Percentagem consumida e VM Cached Bandwidth Consumed Percentage reflete iOPS e produção tanto da unidade temporária efémera como do `(D:\)` hospedeiro caching juntos.


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as melhores práticas de desempenho, consulte os outros artigos desta série:
- [Lista de verificação rápida](performance-guidelines-best-practices-checklist.md)
- [Tamanho VM](performance-guidelines-best-practices-vm-size.md)
- [Recolher linha de base](performance-guidelines-best-practices-collect-baseline.md)

Para obter as melhores práticas de segurança, consulte [considerações de segurança para o SQL Server em Azure Virtual Machines](security-considerations-best-practices.md).

Para testar detalhadamente o desempenho do SQL Server em VMs Azure com referências TPC-E e TPC_C, consulte o desempenho do blog [Otimize OLTP](https://techcommunity.microsoft.com/t5/sql-server/optimize-oltp-performance-with-sql-server-on-azure-vm/ba-p/916794).

Reveja outros artigos da Máquina Virtual do SqL Server [no SQL Server na Visão Geral das Máquinas Virtuais Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). Se tiver dúvidas sobre máquinas virtuais do SQL Server, veja as [Perguntas Mais Frequentes](frequently-asked-questions-faq.md).
