---
title: Diretrizes de desempenho para o SQL Server em Azure Microsoft Docs
description: Fornece diretrizes para otimizar o desempenho do SQL Server nos VMs do Microsoft Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/18/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 880f1c601cf4132fdec9e5d25b1bf1f2ff175ab7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650542"
---
# <a name="performance-guidelines-for-sql-server-in-azure-virtual-machines"></a>Diretrizes de desempenho para o SQL Server nas Máquinas Virtuais do Azure

## <a name="overview"></a>Descrição geral

Este artigo fornece orientação para otimizar o desempenho do SQL Server na Máquina Virtual Microsoft Azure. Ao executar o SQL Server em Máquinas Virtuais do Azure, recomendamos que continue a utilizar as mesmas opções de otimização do desempenho da base de dados que se aplicam ao SQL Server no ambiente de servidor no local. No entanto, o desempenho das bases de dados relacional numa cloud pública depende de muitos fatores, como o tamanho da máquina virtual e a configuração dos discos de dados.

[As imagens do SQL Server disponibilizadas no portal Azure](quickstart-sql-vm-create-portal.md) seguem as melhores práticas de configuração de armazenamento geral (para mais informações sobre como o armazenamento é configurado, consulte a [configuração de armazenamento para VMs do Servidor SQL).](virtual-machines-windows-sql-server-storage-configuration.md) Após o fornecimento, considere aplicar outras otimizações discutidas neste artigo. Baseie as suas escolhas na sua carga de trabalho e verifique através de testes.

> [!TIP]
> Há tipicamente uma compensação entre otimizar os custos e otimizar para o desempenho. Este artigo está focado em obter o *melhor* desempenho para O Servidor SQL em VMs Azure. Se a sua carga de trabalho for menos exigente, poderá não necessitar de todas as otimizações listadas abaixo. Considere as suas necessidades de desempenho, custos e padrões de carga de trabalho à medida que avalia estas recomendações.

## <a name="quick-check-list"></a>Lista de verificação rápida

Segue-se uma lista de verificação rápida para um ótimo desempenho do SQL Server em Máquinas Virtuais Azure:

| Área | Otimizações |
| --- | --- |
| [Tamanho VM](#vm-size-guidance) | - Utilize tamanhos VM com 4 ou mais vCPU como [E4S_v3](../../ev3-esv3-series.md) ou superior, ou [DS12_v2](../../dv2-dsv2-series-memory.md) ou superior.<br/><br/> - [Es, Eas, Ds e Das Series](../../sizes-general.md) oferecem a memória ideal para a relação vCPU necessária para o desempenho da carga de trabalho OLTP. <br/><br/> - [A Série M](../../m-series.md) oferece a maior relação de memória para vCPU necessária para o desempenho crítico da missão e é ideal para cargas de trabalho de armazém de dados. <br/><br/> - Recolher os requisitos de [iopS](../premium-storage-performance.md#iops)da carga de trabalho-alvo, os requisitos de [entrada](../premium-storage-performance.md#throughput) e [latência](../premium-storage-performance.md#latency) nos momentos de pico, seguindo a lista de verificação dos requisitos de desempenho da [aplicação](../premium-storage-performance.md#application-performance-requirements-checklist) e, em seguida, selecione o [Tamanho VM](../sizes-general.md) que pode escalar para os requisitos de desempenho da sua carga de trabalho.|
| [Armazenamento](#storage-guidance) | - Para testes detalhados do desempenho do SQL Server em VMs Azure com tPC-E e TPC_C benchmarks, consulte o desempenho do [Blog Otimize OLTP](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). <br/><br/> - Utilize [SSDs premium](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) para obter as melhores vantagens de preço/desempenho. Configure [a cache ReadOnly](../premium-storage-performance.md#disk-caching) para ficheiros de dados e nenhuma cache para o ficheiro de registo. <br/><br/> - Utilize [discos Ultra](../disks-types.md#ultra-disk) se forem necessárias menos de 1 ms de latências de armazenamento pela carga de trabalho. <br/><br/> - Recolher os requisitos de latência de armazenamento para dados do Servidor SQL, registo e ficheiros Temp [DB, monitorizando a aplicação](../premium-storage-performance.md#application-performance-requirements-checklist) antes de escolher o tipo de disco. Se <forem necessárias de 1ms de armazenamento, utilize Discos Ultra, caso contrário, utilize SSD premium. Se forem necessárias baixas tardios apenas para o ficheiro de registo e não para ficheiros de dados, [forneça o Disco Ultra](../disks-enable-ultra-ssd.md) a iopS e níveis de entrada necessários apenas para o ficheiro de registo. <br/><br/> -  [As partilhas de ficheiros premium](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) são recomendadas como armazenamento partilhado para um cluster de falha do SQL Server. As ações de ficheiropremium não suportam o cache e oferecem um desempenho limitado em comparação com os discos SSD premium. Escolha discos premium geridos por SSD em vez de ações de ficheiropremium para instâncias SQL autónomas; mas alavancar as ações de ficheiropremium para o cluster failover por exemplo armazenamento partilhado para facilitar a manutenção e escalabilidade flexível. <br/><br/> - O armazenamento normal só é recomendado para fins de desenvolvimento e teste ou para ficheiros de backup e não deve ser utilizado para cargas de trabalho de produção. <br/><br/> - Mantenha a conta de [armazenamento](../../../storage/common/storage-create-storage-account.md) e o VM do Servidor SQL na mesma região.<br/><br/> - Desativar o [armazenamento geo-redundante](../../../storage/common/storage-redundancy.md) do Azure (geo-replicação) na conta de armazenamento.  |
| [Discos](#disks-guidance) | - Utilize um mínimo de 2 [discos SSD premium](../disks-types.md#premium-ssd) (1 para ficheiro de registo e 1 para ficheiros de dados). <br/><br/> - Para cargas de trabalho que exijam <de 1 ms IO, ative o acelerador de escrita para série S e considere a utilização de discos Ultra SSD para séries Es e DS. <br/><br/> - Ativar [a leitura apenas](../premium-storage-performance.md#disk-caching) no disco(s) que acolhe os ficheiros de dados.<br/><br/> - Adicione 20% de capacidade adicional de IOPS/entrada premium do que a sua carga de trabalho requer ao [configurar o armazenamento para dados, registo e ficheiros TempDB](virtual-machines-windows-sql-server-storage-configuration.md) de 20% <br/><br/> - Evite utilizar sistemaoperativo ou discos temporários para armazenamento ou registo de bases de dados.<br/><br/> - Não permita que o cache no disco(s) que apresente o ficheiro de registo.  **Importante:** Pare o serviço SQL Server ao alterar as definições de cache para um disco Azure VM.<br/><br/> - Stripe vários discos de dados Azure para obter um maior consumo de armazenamento.<br/><br/> - Formato com tamanhos de atribuição documentados. <br/><br/> - Coloque o TempDB na `D:\` unidade SSD local para cargas de trabalho críticas do Servidor SQL da missão (após escolher o tamanho vM correto). Se criar o VM a partir de modelos de quickstart do portal Azure ou azure e [colocar temp DB no Disco Local,](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583) então não precisa de mais nenhuma ação; para todos os outros casos siga os passos no blog para utilização de [SSDs para armazenar TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) para evitar falhas após o reinício. Se a capacidade da unidade local não for suficiente para o seu tamanho Temp DB, coloque temp DB numa piscina de armazenamento [despojada](../premium-storage-performance.md) em discos SSD premium com [cache apenas de leitura](../premium-storage-performance.md#disk-caching). |
| [I/O](#io-guidance) |- Ativar a compressão da página da base de dados.<br/><br/> - Ativar a inicialização instantânea de ficheiros para ficheiros de dados.<br/><br/> - Limite o crescimento automático da base de dados.<br/><br/> - Desativar o auto-psiquiatra da base de dados.<br/><br/> - Mover todas as bases de dados para discos de dados, incluindo bases de dados do sistema.<br/><br/> - Mover o registo de erros do Servidor SQL e rastrear diretórios de ficheiros para discos de dados.<br/><br/> - Configurar as localizações de ficheiros de cópia de segurança e de base de dados predefinidas.<br/><br/> - [Ativar páginas bloqueadas na memória.](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows?view=sql-server-2017)<br/><br/> - Aplique correções de desempenho do Servidor SQL. |
| [Específico de recurso](#feature-specific-guidance) | - Recua diretamente para o armazenamento de bolhas.<br/><br/>- Utilize [cópias de segurança instantâneas](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) para bases de dados superiores a 12 TB. <br/><br/>- Utilize vários ficheiros TEMP DB, 1 ficheiro por núcleo, até 8 ficheiros.<br/><br/>- Detete a memória do servidor max a 90% ou até 50 GB para o Sistema Operativo. <br/><br/>- Ativar o NUM macio. |

Para obter mais informações sobre *como* e *porquê* fazer estas otimizações, por favor reveja os detalhes e orientações fornecidos nas seguintes secções.

## <a name="vm-size-guidance"></a>Orientação do tamanho vm

Comece por recolher os requisitos de cpu, memória e armazenamento da carga de trabalho em momentos de pico. \LogicalDisk\Disk Reads/Sec e \LogicalDisk\Disk Writes/Sec os contadores de desempenho podem ser usados para recolher e escrever requisitos de IOPS e \LogicalDisk\Disk Bytes/Sec counter pode ser usado para recolher requisitos de entrada de armazenamento para [ficheiros](../premium-storage-performance.md#disk-caching) Data, Log e Temp DB. Depois de definidos os requisitos de IOPS e de entrada no pico, avalie as ofertas de tamanhos VM dessa capacidade. Por exemplo, se a sua carga de trabalho requer 20 K de leitura IOPS e 10K escrever IOPS no pico, pode escolher E16s_v3 (com até 32 K cached e 25600 IOPS não cached) ou M16_s (com até 20 K cached e 10K iOPS não cached) com 2 discos P30. Certifique-se de compreender tanto os requisitos de entrada como de IOPS da carga de trabalho, uma vez que os VMs têm diferentes limites de escala para IOPS e para a sua entrada.<br/><br/>[DSv_3](../../dv3-dsv3-series.md) e [séries Es_v3](../../ev3-esv3-series.md) são hospedados em hardware de propósito geral com processadores Intel Haswell ou Broadwell. [A série M](../../m-series.md) oferece a maior contagem e memória de VCPU para as maiores cargas de trabalho do SQL Server e hospedada em hardware otimizado de memória com a família do processador Skylake. Estes suportes de série VM armazenam o armazenamento premium, que é recomendado para o melhor desempenho com cache de leitura de nível anfitrião. Tanto Es_v3 como as séries M também estão disponíveis em [tamanhos de núcleo limitados,](../../windows/constrained-vcpu.md)que poupam dinheiro para cargas de trabalho com menor esputação e elevada capacidade de armazenamento. 

## <a name="storage-guidance"></a>Orientações de armazenamento

Para testes detalhados do desempenho do SQL Server em VMs Azure com referências TPC-E e TPC_C, consulte o desempenho do [Otimize OLTP](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)do blog . 

A cache de blob azure com SSDs premium é recomendada para todas as cargas de trabalho de produção. 

> [!WARNING]
> HDDs e SSDs padrão têm llátas e largura de banda variadas e são recomendados apenas para trabalhos de dev/teste. As cargas de trabalho de produção devem utilizar SSDs premium.

Além disso, recomendamos que crie a sua conta de armazenamento Azure no mesmo centro de dados que as suas máquinas virtuais SQL Server para reduzir os atrasos de transferência. Ao criar uma conta de armazenamento, desative a geo-replicação como uma ordem de escrita consistente em vários discos não é garantida. Em vez disso, considere configurar uma tecnologia de recuperação de desastres do SQL Server entre dois centros de dados Azure. Para mais informações, consulte [Alta Disponibilidade e Recuperação de Desastres para o Servidor SQL em Máquinas Virtuais Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Orientação de discos

Existem três tipos principais de discos num VM Azure:

* **Disco OS**: Quando criar uma Máquina Virtual Azure, a plataforma anexará pelo menos um disco (rotulado como unidade **C)** ao VM para o disco do seu sistema operativo. Este disco é um VHD armazenado como uma página blob no armazenamento.
* **Disco temporário**: As máquinas virtuais Azure contêm outro disco chamado disco temporário (rotulado como **D:** unidade). Este é um disco no nó que pode ser usado para o espaço de arranhão.
* Discos de **dados:** Também pode anexar discos adicionais à sua máquina virtual como discos de dados, e estes serão armazenados no armazenamento como bolhas de página.

As seguintes secções descrevem recomendações para a utilização destes diferentes discos.

### <a name="operating-system-disk"></a>Disco do sistema operativo

Um disco de sistema operativo é um VHD que pode iniciar e montar como uma versão em execução de um sistema operativo e está rotulado como unidade **C.**

A política padrão de cache no disco do sistema operativo é **Read/Write**. Para aplicações sensíveis ao desempenho, recomendamos que utilize discos de dados em vez do disco do sistema operativo. Consulte a secção em Discos de Dados abaixo.

### <a name="temporary-disk"></a>Disco temporário

A unidade de armazenamento temporário, rotulada como unidade **D,** não é perceitada ao armazenamento de bolhas Azure. Não guarde os ficheiros da base de dados do utilizador ou os ficheiros de registo de transações do utilizador no **D:** unidade.

Coloque tempDB na unidade `D:\` SSD local para cargas de trabalho críticas do Servidor SQL da missão (depois de escolher o tamanho vM correto). Se criar o VM a partir de modelos de quickstart do portal Azure ou azure e [colocar temp DB no Disco Local,](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)então não precisa de mais nenhuma ação; para todos os outros casos siga os passos no blog para utilização de [SSDs para armazenar TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) para evitar falhas após o reinício. Se a capacidade da unidade local não for suficiente para o seu tamanho Temp DB, coloque temp DB numa piscina de armazenamento [despojada](../premium-storage-performance.md) em discos SSD premium com [cache apenas de leitura](../premium-storage-performance.md#disk-caching).

Para VMs que suportam SSDs premium, também pode armazenar TempDB num disco que suporta SSDs premium com suporte de suporte de cache de leitura habilitado.


### <a name="data-disks"></a>Discos de dados

* **Utilize discos SSD premium para dados e ficheiros**de registo : Se não estiver a utilizar a tirade disquete, utilize dois discos SSD premium onde um disco contenha o ficheiro de registo e o outro contenha os dados. Cada SSD premium fornece uma série de IOPS e largura de banda (MB/s) dependendo do seu tamanho, conforme descrito no artigo, [Selecione um tipo](../disks-types.md)de disco . Se estiver a utilizar uma técnica de striping de discos, como espaços de armazenamento, obtém um desempenho ideal tendo duas piscinas, uma para os ficheiros de registo e outra para os ficheiros de dados. No entanto, se planeia utilizar casos de cluster sQL Server (FCI), deve configurar uma piscina ou utilizar ações de [ficheiro premium.](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md)

   > [!TIP]
   > - Para obter resultados de testes em várias configurações de disco e carga de trabalho, consulte o seguinte post de blog: Diretrizes de [Configuração de Armazenamento para Servidor SQL no Azure VM](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/).
   > - Para o desempenho crítico da missão para os Servidores SQL que precisam de ~ 50.000 IOPS, considere substituir 10 -P30 discos por um Ultra SSD. Para mais informações, consulte o seguinte post de blog: [Mission critical performance with Ultra SSD](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > Ao fornecer um VM de servidor SQL no portal, tem a opção de editar a sua configuração de armazenamento. Dependendo da sua configuração, o Azure configura um ou mais discos. Vários discos são combinados numa única piscina de armazenamento com striping. Tanto os dados como os ficheiros de registo residem juntos nesta configuração. Para mais informações, consulte a [configuração de Armazenamento para VMs do Servidor SQL](virtual-machines-windows-sql-server-storage-configuration.md).

* **Striping do disco**: Para obter mais informações, pode adicionar discos de dados adicionais e utilizar a Striping do Disco. Para determinar o número de discos de dados, é necessário analisar o número de IOPS e largura de banda necessária para os seus ficheiros de registo, bem como para os seus dados e ficheiros TempDB. Note que diferentes tamanhos de VM têm limites diferentes no número de IOPs e largura de banda suportadas, consulte as tabelas em IOPS por [tamanho VM](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Utilize as seguintes diretrizes:

  * Para windows 8/Windows Server 2012 ou posteriormente, utilize espaços de [armazenamento](https://technet.microsoft.com/library/hh831739.aspx) com as seguintes diretrizes:

      1. Defina o interleave (tamanho das riscas) para 64 KB (65.536 bytes) para cargas de trabalho OLTP e 256 KB (262.144 bytes) para o armazenamento de dados para evitar o impacto do desempenho devido ao desalinhamento da divisória. Isto deve ser definido com powerShell.
      2. Definir contagem de colunas = número de discos físicos. Utilize o PowerShell ao configurar mais de 8 discos (não o Server Manager UI). 

    Por exemplo, o seguinte PowerShell cria um novo conjunto de armazenamento com o tamanho da interlicença para 64 KB e o número de colunas para 2:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Para o Windows 2008 R2 ou mais cedo, pode utilizar discos dinâmicos (volumes com riscas de OS) e o tamanho das riscas é sempre de 64 KB. Esta opção é depreciada a partir do Windows 8/Windows Server 2012. Para obter informações, consulte a declaração de suporte do [Serviço de Discos Virtuais está em transição para](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx)a API de Gestão de Armazenamento do Windows .

  * Se estiver a utilizar espaços de [armazenamento direto (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) com [instâncias de cluster de failover do servidor SQL,](virtual-machines-windows-portal-sql-create-failover-cluster.md)tem de configurar uma única piscina. Embora possam ser criados volumes diferentes nessa única piscina, todos partilharão as mesmas características, como a mesma política de cache.

  * Determine o número de discos associados ao seu pool de armazenamento com base nas suas expectativas de carga. Tenha em mente que diferentes tamanhos de VM permitem diferentes números de discos de dados anexados. Para mais informações, consulte [Tamanhos para Máquinas Virtuais](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Se não estiver a utilizar SSDs premium (cenários de dev/teste), a recomendação é adicionar o número máximo de discos de dados suportados pelo tamanho do [VM](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e utilizar a Striping de Disco.

* **Política de cache**: Note as seguintes recomendações para a política de cache dependendo da sua configuração de armazenamento.

  * Se estiver a utilizar discos separados para dados e ficheiros de registo, ative a leitura de cache nos discos de dados que hospedam os seus ficheiros de dados e ficheiros de dados TempDB. Isto pode resultar num benefício significativo de desempenho. Não ative o cache no disco que segura o ficheiro de registo, uma vez que causa uma diminuição menor do desempenho.

  * Se estiver a usar tiras de disco numa única piscina de armazenamento, a maioria das cargas de trabalho beneficiará da leitura do cache. Se tiver piscinas de armazenamento separadas para os ficheiros de registo e dados, ative a leitura do cache apenas no depósito dos ficheiros de dados. Em certas cargas de trabalho de escrita pesadas, um melhor desempenho pode ser alcançado sem cache. Isto só pode ser determinado através de testes.

  * As recomendações anteriores aplicam-se aos SSDs premium. Se não estiver a utilizar SSDs premium, não ative qualquer cache em discos de dados.

  * Para obter instruções sobre a configuração do cache do disco, consulte os seguintes artigos. Para o modelo de implementação clássico (ASM) consulte: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) e [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). Para o modelo de implementação do Gestor de Recursos Azure, consulte: [Set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) e [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Pare o serviço SQL Server ao alterar a definição de cache dos discos Azure VM para evitar a possibilidade de qualquer corrupção na base de dados.

* Tamanho da unidade de **atribuição NTFS**: Ao formar o disco de dados, recomenda-se que utilize um tamanho de unidade de alocação de 64 KB para dados e ficheiros de registo, bem como tempDB. Se tempDB for colocado no disco temporário (D:\ unidade) o desempenho obtido ao alavancar esta unidade supera a necessidade de um tamanho de unidade de alocação de 64K. 

* **Boas práticas**de gestão do disco : Ao remover um disco de dados ou alterar o seu tipo de cache, pare o serviço SQL Server durante a alteração. Quando as definições de cache são alteradas no disco OS, o Azure para o VM, altera o tipo de cache e reinicia o VM. Quando as definições de cache de um disco de dados são alteradas, o VM não é parado, mas o disco de dados é separado do VM durante a alteração e, em seguida, religado.

  > [!WARNING]
  > A não paragem do serviço SQL Server durante estas operações pode causar corrupção na base de dados.


## <a name="io-guidance"></a>Orientação de I/O

* Os melhores resultados com SSDs premium são alcançados quando paralelamente a sua aplicação e pedidos. Os SSDs Premium são projetados para cenários em que a profundidade da fila IO é superior a 1, pelo que verá pouco ou nenhum ganho de desempenho para pedidos de série de fios únicos (mesmo que sejam intensivos de armazenamento). Por exemplo, isto pode afetar os resultados dos testes de uma só linha de ferramentas de análise de desempenho, como o SQLIO.

* Considere usar [a compressão](https://msdn.microsoft.com/library/cc280449.aspx) da página de base de dados, pois pode ajudar a melhorar o desempenho das cargas de trabalho intensivas em I/O. No entanto, a compressão de dados pode aumentar o consumo de CPU no servidor de base de dados.

* Considere permitir a inicialização instantânea do ficheiro para reduzir o tempo necessário para a atribuição inicial de ficheiros. Para tirar partido da inicialização instantânea do ficheiro, concede a conta de serviço SQL Server (MSSQLSERVER) com SE_MANAGE_VOLUME_NAME e adiciona-a à política de segurança de Tarefas de Manutenção de **Volume de Desempenho.** Se estiver a utilizar uma imagem da plataforma SQL Server para o Azure, a conta de serviço predefinida (NT Service\MSSQLSERVER) não é adicionada à política de segurança de Tarefas de Manutenção de **Volume de Desempenho.** Por outras palavras, a inicialização instantânea do ficheiro não está ativada numa imagem da plataforma SQL Server Azure. Depois de adicionar a conta de serviço SQL Server à política de segurança de Tarefas de Manutenção de **Volume de Desempenho,** reinicie o serviço SQL Server. Pode haver considerações de segurança para usar esta funcionalidade. Para mais informações, consulte a [inicialização do ficheiro de base](https://msdn.microsoft.com/library/ms175935.aspx)de dados .

* **autogrow** é considerado apenas uma contingência para um crescimento inesperado. Não gere os seus dados e faça log crescimento no dia-a-dia com o auto-crescimento. Se for utilizado um crescimento automático, faça o pré-cultivo do ficheiro utilizando o interruptor Tamanho.

* Certifique-se de que a **entração automática** está desativada para evitar sobrecargas desnecessárias que podem afetar negativamente o desempenho.

* Mova todas as bases de dados para discos de dados, incluindo bases de dados do sistema. Para mais informações, consulte [Move System Databases](https://msdn.microsoft.com/library/ms345408.aspx).

* Mova o registo de erros do Servidor SQL e rastreie os diretórios de ficheiros para os discos de dados. Isto pode ser feito no SQL Server Configuration Manager clicando na instância do SQL Server e selecionando propriedades. As definições de registo de erros e de ficheiros de rastreio podem ser alteradas no separador Parâmetros de **Arranque.** O Diretório de Despejo é especificado no separador **Advanced.** A imagem que se segue mostra onde procurar o parâmetro de arranque de registo de erros.

    ![SQL ErrorLog Screenshot](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* Configurar localizações de ficheiros de cópia de segurança e de dados de base de dados. Utilize as recomendações neste artigo e faça as alterações na janela de propriedades do Servidor. Para obter instruções, consulte [ver ou alterar as localizações predefinidas para ficheiros de dados e registos (Estúdio de Gestão de Servidores SQL)](https://msdn.microsoft.com/library/dd206993.aspx). A imagem que se segue demonstra onde fazer estas alterações.

    ![Ficheiros de registo de dados e cópia de segurança sQL](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Ativar páginas bloqueadas para reduzir a IO e quaisquer atividades de paging. Para mais informações, consulte Ativar as páginas de bloqueio na opção memória [(Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

* Se estiver a executar o SQL Server 2012, instale o Service Pack 1 Update Cumulativo 10. Esta atualização contém a correção para um mau desempenho em I/O quando executa a select em declaração de tabela temporária no SQL Server 2012. Para obter informações, consulte este artigo base de [conhecimento.](https://support.microsoft.com/kb/2958012)

* Considere comprimir quaisquer ficheiros de dados ao transferir para dentro e para fora do Azure.

## <a name="feature-specific-guidance"></a>Orientação específica de recurso

Algumas implementações podem obter benefícios adicionais de desempenho usando técnicas de configuração mais avançadas. A lista que se segue destaca algumas funcionalidades do SQL Server que podem ajudá-lo a obter um melhor desempenho:

### <a name="back-up-to-azure-storage"></a>Back up to Azure Storage
Ao executar backups para o Servidor SQL em execução em máquinas virtuais Azure, pode utilizar a [Cópia de Segurança do Servidor SQL para URL](https://msdn.microsoft.com/library/dn435916.aspx). Esta funcionalidade está disponível a partir do SQL Server 2012 SP1 CU2 e recomendada para o backup dos discos de dados anexos. Quando fizer backup/restaurar de/para o armazenamento azure, siga as recomendações fornecidas na [Reserva de Apoio ao Servidor SQL para URL Best Practices e Resolução de Problemas e Restauro de Backups Armazenados no Armazenamento Azure](https://msdn.microsoft.com/library/jj919149.aspx). Também pode automatizar estas cópias de segurança utilizando [backup automatizado para o Servidor SQL em Máquinas Virtuais Azure](virtual-machines-windows-sql-automated-backup.md).

Antes do SQL Server 2012, pode utilizar a [Cópia de Segurança do Servidor SQL para a Ferramenta Azure](https://www.microsoft.com/download/details.aspx?id=40740). Esta ferramenta pode ajudar a aumentar a entrada de cópia de reserva usando vários alvos de listras de reserva.

### <a name="sql-server-data-files-in-azure"></a>Ficheiros de dados do Servidor SQL em Azure

Esta nova funcionalidade, [SQL Server Data Files in Azure,](https://msdn.microsoft.com/library/dn385720.aspx)está disponível a partir do SQL Server 2014. Executar o SQL Server com ficheiros de dados no Azure demonstra características de desempenho comparáveis como a utilização de discos de dados Azure.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Instância de cluster failover e espaços de armazenamento

Se estiver a utilizar espaços de armazenamento, ao adicionar nós ao cluster na página **de confirmação,** limpe a caixa de verificação rotulada **Adicione todo o armazenamento elegível ao cluster**. 

![Desverificar armazenamento elegível](media/virtual-machines-windows-sql-performance/uncheck-eligible-cluster-storage.png)

Se estiver a utilizar espaços de armazenamento e não desverificar **Adicione todo o armazenamento elegível ao cluster,** o Windows desprende os discos virtuais durante o processo de agrupamento. Como resultado, não aparecem no Disk Manager ou explorer até que os espaços de armazenamento sejam removidos do cluster e recolocados usando o PowerShell. Espaços de Armazenamento agrupa vários discos em piscinas de armazenamento. Para mais informações, consulte [Espaços de Armazenamento](/windows-server/storage/storage-spaces/overview).

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre armazenamento e desempenho, consulte [as Diretrizes de Configuração de Armazenamento para o Servidor SQL no Azure VM](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)

Para obter as melhores práticas de segurança, consulte [considerações de segurança para o Servidor SQL em Máquinas Virtuais Azure](virtual-machines-windows-sql-security.md).

Reveja outros artigos da Máquina Virtual do Servidor SQL no [SQL Server na visão geral das máquinas virtuais azure](virtual-machines-windows-sql-server-iaas-overview.md). Se tiver dúvidas sobre máquinas virtuais do SQL Server, veja as [Perguntas Mais Frequentes](virtual-machines-windows-sql-server-iaas-faq.md).
