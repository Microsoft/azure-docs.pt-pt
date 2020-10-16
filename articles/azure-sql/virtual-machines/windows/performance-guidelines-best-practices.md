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
ms.date: 10/18/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: d720fdd05a4356b8fec2c007a4e5a3d1ea1cf00b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91293310"
---
# <a name="performance-guidelines-for-sql-server-on-azure-virtual-machines"></a>Diretrizes de desempenho do SQL Server nas Máquinas Virtuais do Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo fornece orientação para otimizar o desempenho do SQL Server nas Máquinas Virtuais Microsoft Azure.

## <a name="overview"></a>Descrição geral

 Enquanto executa o SQL Server em Azure Virtual Machines, recomendamos que continue a utilizar as mesmas opções de afinação de desempenho da base de dados que são aplicáveis ao SQL Server em ambientes de servidores no local. No entanto, o desempenho das bases de dados relacional numa cloud pública depende de muitos fatores, como o tamanho da máquina virtual e a configuração dos discos de dados.

[As imagens do SQL Server a forradas no portal Azure](sql-vm-create-portal-quickstart.md) seguem as melhores práticas de configuração de armazenamento geral (para obter mais informações sobre como o armazenamento é configurado, consulte [a configuração de armazenamento para máquinas virtuais SQL Server (VMs)](storage-configuration.md)). Após o provisionamento, considere aplicar outras otimizações discutidas neste artigo. Baseie as suas escolhas na sua carga de trabalho e verifique através de testes.

> [!TIP]
> Existe tipicamente uma compensação entre otimizar os custos e otimizar o desempenho. Este artigo está focado em obter o *melhor* desempenho para SQL Server em Azure Virtual Machines. Se a sua carga de trabalho for menos exigente, poderá não necessitar de todas as otimizações listadas abaixo. Considere as suas necessidades de desempenho, custos e padrões de carga de trabalho à medida que avalia estas recomendações.

## <a name="quick-checklist"></a>Lista de verificação rápida

Segue-se uma lista de verificação rápida para um desempenho ótimo do SQL Server em Azure Virtual Machines:

| Área | Otimizações |
| --- | --- |
| [Tamanho VM](#vm-size-guidance) | - Utilize tamanhos VM com 4 ou mais vCPU como [E4S_v3](../../../virtual-machines/ev3-esv3-series.md) ou superior, ou [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md) ou superior.<br/><br/> - [Es, Eas, Ds e Das Series](../../../virtual-machines/sizes-general.md) oferece a memória ideal para vCPU rácio necessário para o desempenho da carga de trabalho OLTP. <br/><br/> - [A Série M](../../../virtual-machines/m-series.md) oferece a maior memória para vCPU rácio necessário para o desempenho crítico da missão e é ideal para cargas de trabalho de armazém de dados. <br/><br/> - Recolher os requisitos [de IOPS,](../../../virtual-machines/windows/premium-storage-performance.md#iops) [produção](../../../virtual-machines/windows/premium-storage-performance.md#throughput)  e [latência](../../../virtual-machines/windows/premium-storage-performance.md#latency) do destino nos momentos de pico, seguindo a [lista de verificação dos requisitos de desempenho](../../../virtual-machines/windows/premium-storage-performance.md#application-performance-requirements-checklist) da aplicação e selecione o Tamanho [VM](../../../virtual-machines/sizes-general.md) que pode escalar para os requisitos de desempenho da sua carga de trabalho.|
| [Armazenamento](#storage-guidance) | - Para testar detalhadamente o desempenho do SQL Server em Máquinas Virtuais Azure com referências TPC-E e TPC_C, consulte o desempenho do blog [Otimize OLTP](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). <br/><br/> - Utilize [SSDs premium](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) para obter as melhores vantagens de preço/desempenho. Configure [a cache ReadOnly](../../../virtual-machines/windows/premium-storage-performance.md#disk-caching) para ficheiros de dados e nenhuma cache para o ficheiro de registo. <br/><br/> - Utilize Discos Ultra se forem [necessárias](../../../virtual-machines/disks-types.md#ultra-disk) menos de 1 ms de atrasos de armazenamento pela carga de trabalho. Consulte [migrar para ultra disco](storage-migrate-to-ultradisk.md) para saber mais. <br/><br/> - Recolher os requisitos de latência de armazenamento para os dados do SQL Server, registo e DB temp [monitorizando a aplicação](../../../virtual-machines/windows/premium-storage-performance.md#application-performance-requirements-checklist) antes de escolher o tipo de disco. Se forem necessárias <dedesemquências de armazenamento de 1 m, utilize discos ultra,. Se as baixas latências forem apenas necessárias para o ficheiro de registo e não para ficheiros de dados, então [forneça o Disco Ultra](../../../virtual-machines/disks-enable-ultra-ssd.md) no IOPS necessário e os níveis de produção apenas para o Ficheiro de registo. <br/><br/> -  [As ações de ficheiros premium](failover-cluster-instance-premium-file-share-manually-configure.md) são recomendadas como armazenamento partilhado para uma instância de cluster de failover do SQL Server. As ações de ficheiros premium não suportam o cache, e oferecem um desempenho limitado em comparação com os discos SSD premium. Escolha discos geridos premium por SSD em vez de ações de ficheiros premium para instâncias SQL autónomas; mas alavancar as ações de ficheiros premium para o armazenamento partilhado de exemplo de failover para facilitar a manutenção e a escalabilidade flexível. <br/><br/> - O armazenamento normalizado só é recomendado para fins de desenvolvimento e teste ou para ficheiros de cópia de segurança e não deve ser utilizado para cargas de trabalho de produção. <br/><br/> - Mantenha a [conta de armazenamento](../../../storage/common/storage-create-storage-account.md) e o SQL Server VM na mesma região.<br/><br/> - Desativar [o armazenamento geo-redundante](../../../storage/common/storage-redundancy.md) da Azure (geo-replicação) na conta de armazenamento.  |
| [Discos](#disks-guidance) | - Utilize um mínimo de 2 [discos SSD premium](../../../virtual-machines/disks-types.md#premium-ssd) (1 para ficheiros de registo e 1 para ficheiros de dados). <br/><br/> - Para cargas de trabalho que exijam <1 ms de latências IO, ative o acelerador de escrita para a série M e considere a utilização de discos Ultra SSD para séries Es e DS. <br/><br/> - Ativar [a leitura apenas](../../../virtual-machines/windows/premium-storage-performance.md#disk-caching) no(s) disco(s) que hospeda os ficheiros de dados.<br/><br/> - Adicione uma capacidade adicional de 20% de IOPS/produção premium do que a sua carga de trabalho requer ao [configurar o armazenamento para os ficheiros do SQL Server, log e TempDB](storage-configuration.md) <br/><br/> - Evite utilizar sistema operativo ou discos temporários para armazenamento de bases de dados ou registo de registo.<br/><br/> - Não ative o cache no(s) disco(s) hospedando o ficheiro de registo.  **Importante**: Pare o serviço SQL Server ao alterar as definições de cache para um disco Azure Virtual Machines.<br/><br/> - Stripe vários discos de dados Azure para obter um aumento da produção de armazenamento.<br/><br/> - Formato com tamanhos de atribuição documentados. <br/><br/> - Coloque o TempDB na unidade SSD local `D:\` para as cargas de trabalho críticas do SQL Server (depois de escolher o tamanho VM correto). Se criar o VM a partir do portal Azure ou dos modelos de arranque rápido do Azure e [colocar o Temp DB no Disco Local,](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583) então não precisa de mais ação; para todos os outros casos siga os passos no blog para  [usar SSDs para armazenar TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) para evitar falhas após o reinício. Se a capacidade da unidade local não for suficiente para o seu tamanho DeP DB, em seguida, coloque o Temp DB numa piscina de armazenamento [listrada](../../../virtual-machines/windows/premium-storage-performance.md) em discos SSD premium com [cache apenas de leitura](../../../virtual-machines/windows/premium-storage-performance.md#disk-caching). |
| [I/O](#io-guidance) |- Ativar a compressão da página da base de dados.<br/><br/> - Ativar a inicialização instantânea do ficheiro para ficheiros de dados.<br/><br/> - Limitar o crescimento automático da base de dados.<br/><br/> - Desative o auto-shrink da base de dados.<br/><br/> - Mover todas as bases de dados para discos de dados, incluindo bases de dados do sistema.<br/><br/> - Mover o registo de erros do SQL Server e rastrear os diretórios de ficheiros para discos de dados.<br/><br/> - Configurar a cópia de segurança predefinida e as localizações dos ficheiros de base de dados.<br/><br/> - [Ativar páginas bloqueadas na memória](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows?view=sql-server-2017).<br/><br/> - Aplicar correções de desempenho do SQL Server. |
| [Específico de recursos](#feature-specific-guidance) | - Volte diretamente para o armazém da Azure Blob.<br/><br/>- Utilize [cópias de segurança instantâneas](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) de ficheiros para bases de dados superiores a 12 TB. <br/><br/>- Utilize vários ficheiros DB temporários, 1 ficheiro por núcleo, até 8 ficheiros.<br/><br/>- Coloque a memória máxima do servidor em 90% ou até 50 GB para o Sistema Operativo. <br/><br/>- Ativar um numa suave. |

Para obter mais informações sobre *como* e *porquê* fazer estas otimizações, por favor reveja os detalhes e orientações fornecidos nas seguintes secções.

## <a name="vm-size-guidance"></a>Orientação do tamanho VM

Comece por recolher os requisitos de cpu, memória e produção de armazenamento da carga de trabalho em momentos de pico. \LogicalDisk\Disk Reads/Sec and \LogicalDisk\Disk Writes/Sec's performance counters can be used to collect read and write IOPS requirements and \LogicalDisk\Disk Bytes/Sec counter can be used to collect [storage throughputs](../../../virtual-machines/premium-storage-performance.md#disk-caching) for Data, Log and Temp DB counter. Após iops e os requisitos de produção no pico são definidos, então avaliar tamanhos VM oferece essa capacidade. Por exemplo, se a sua carga de trabalho necessitar de 20 K de leitura IOPS e 10K write IOPS no pico, pode escolher E16s_v3 (com até 32 K em cache e 25600 IOPS não colados) ou M16_s (com até 20 K em cache e 10K de IOPS sem pressão) com discos de 2 P30. Certifique-se de compreender tanto os requisitos de produção como o IOPS da carga de trabalho, uma vez que os VMs têm limites de escala diferentes para IOPS e produção.<br/><br/>[DSv_3](../../../virtual-machines/dv3-dsv3-series.md) e [Es_v3 séries](../../../virtual-machines/ev3-esv3-series.md) são hospedados em hardware de propósito geral com processadores Intel Haswell ou Broadwell. [A série M](../../../virtual-machines/m-series.md) oferece a maior contagem e memória vCPU para as maiores cargas de trabalho do SQL Server e hospedada em hardware otimizado de memória com a família do processador Skylake. Estes armazenamentos premium de suporte da série VM, que é recomendado para o melhor desempenho com cache de leitura de nível de anfitrião. Tanto as séries Es_v3 como M também estão disponíveis em [tamanhos de núcleo limitados,](../../../virtual-machines/constrained-vcpu.md)o que poupa dinheiro para cargas de trabalho com menor computação e elevada capacidade de armazenamento. 

## <a name="storage-guidance"></a>Orientações de armazenamento

Para testar detalhadamente o desempenho do SQL Server em Azure Virtual Machines com referências TPC-E e TPC_C, consulte o desempenho do blog [Otimize OLTP](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). 

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

Coloque o TempDB na unidade SSD local `D:\` para as cargas críticas do SQL Server (depois de escolher o tamanho VM correto). Se criar o VM a partir do portal Azure ou dos modelos de quickstart Azure e [colocar o Temp DB no Disco Local,](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)então não precisa de mais ação; para todos os outros casos siga os passos no blog para  [usar SSDs para armazenar TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) para evitar falhas após o reinício. Se a capacidade da unidade local não for suficiente para o seu tamanho DeP DB, em seguida, coloque o Temp DB numa piscina de armazenamento [listrada](../../../virtual-machines/premium-storage-performance.md) em discos SSD premium com [cache apenas de leitura](../../../virtual-machines/premium-storage-performance.md#disk-caching).

Para VMs que suportam SSDs premium, também pode armazenar TempDB num disco que suporta SSDs premium com cache de leitura ativado.


### <a name="data-disks"></a>Discos de dados

* **Utilize discos SSD premium para ficheiros de dados e registos**: Se não estiver a utilizar a desmontagem de discos, utilize dois discos SSD premium onde um disco contenha o ficheiro de registo e o outro contenha os dados. Cada SSD premium fornece uma série de IOPS e largura de banda (MB/s) dependendo do seu tamanho, conforme descrito no artigo, [Selecione um tipo](../../../virtual-machines/disks-types.md)de disco . Se estiver a utilizar uma técnica de striping de discos, como espaços de armazenamento, obtém o melhor desempenho com duas piscinas, uma para o ficheiro de registo e outra para os ficheiros de dados. No entanto, se planeia utilizar as instâncias de cluster failover do SQL Server (FCI), tem de configurar uma piscina ou utilizar [ações de ficheiros premium.](failover-cluster-instance-premium-file-share-manually-configure.md)

   > [!TIP]
   > - Para obter resultados de testes em várias configurações de disco e carga de trabalho, consulte o seguinte post de blog: [Diretrizes de configuração de armazenamento para servidor SQL em Máquinas Virtuais Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/).
   > - Para um desempenho crítico da missão para os SqL Servers que necessitem de ~ 50.000 IOPS, considere substituir discos de 10 -P30 por um Ultra SSD. Para mais informações, consulte o seguinte post de blog: [Desempenho crítico da missão com Ultra SSD](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > Ao providenciar um SQL Server VM no portal, tem a opção de editar a sua configuração de armazenamento. Dependendo da sua configuração, o Azure configura um ou mais discos. Vários discos são combinados numa única piscina de armazenamento com striping. Tanto os ficheiros de dados como os ficheiros de registo residem juntos nesta configuração. Para obter mais informações, consulte [a configuração de Armazenamento para VMs do servidor SQL](storage-configuration.md).

* **Tiragem do**disco : Para obter mais produção, pode adicionar discos de dados adicionais e utilizar a desmontagem do disco. Para determinar o número de discos de dados, é necessário analisar o número de IOPS e largura de banda necessárias para o(s) ficheiros de registo e para os seus dados e ficheiros TempDB. Note que diferentes tamanhos de VM têm limites diferentes no número de IOPs e largura de banda suportada, ver as tabelas no tamanho IOPS por [VM](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Utilize as seguintes diretrizes:

  * Para windows 8/Windows Server 2012 ou posterior, utilize [os Espaços de Armazenamento](https://technet.microsoft.com/library/hh831739.aspx) com as seguintes orientações:

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

  * Para o Windows 2008 R2 ou mais cedo, pode utilizar discos dinâmicos (volumes listrados de OS) e o tamanho das listras é sempre de 64 KB. Esta opção é depreciada a partir do Windows 8/Windows Server 2012. Para obter informações, consulte a declaração de suporte no [Serviço de Disco Virtual que está em transição para a API de Gestão de Armazenamento do Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Se estiver a utilizar [espaços de armazenamento diretos (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) com [instâncias de cluster de failover do servidor SQL,](failover-cluster-instance-storage-spaces-direct-manually-configure.md)tem de configurar uma única piscina. Embora possam ser criados volumes diferentes nesse único pool, todos eles partilharão as mesmas características, como a mesma política de caching.

  * Determine o número de discos associados à sua piscina de armazenamento com base nas suas expectativas de carga. Tenha em mente que diferentes tamanhos de VM permitem diferentes números de discos de dados anexados. Para obter mais informações, consulte [tamanhos para máquinas virtuais.](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

  * Se não estiver a utilizar SSDs premium (cenários dev/teste), a recomendação é adicionar o número máximo de discos de dados suportados pelo [seu tamanho VM](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e utilizar a tira do disco.

* **Política de Caching**: Note as seguintes recomendações para a política de caching dependendo da sua configuração de armazenamento.

  * Se estiver a utilizar discos separados para ficheiros de dados e registos, ative a leitura dos discos de dados que hospedam os seus ficheiros de dados e ficheiros de dados TempDB. Isto pode resultar num benefício significativo para o desempenho. Não ative o cache no disco que mantém o ficheiro de registo, pois isto causa uma diminuição menor do desempenho.

  * Se estiver a utilizar o striping de disco numa única piscina de armazenamento, a maioria das cargas de trabalho beneficiarão da leitura do cache. Se tiver conjuntos de armazenamento separados para os ficheiros de registo e dados, ative a leitura apenas no conjunto de armazenamento para os ficheiros de dados. Em certas cargas de trabalho de escrita pesada, um melhor desempenho pode ser alcançado sem caching. Isto só pode ser determinado através de testes.

  * As recomendações anteriores aplicam-se aos SSDs premium. Se não estiver a utilizar SSDs premium, não ative qualquer cache em quaisquer discos de dados.

  * Para obter instruções sobre a configuração do cache do disco, consulte os seguintes artigos. Para o modelo de implementação clássico (ASM) consulte: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) e [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). Para o modelo de implementação do Gestor de Recursos Azure, consulte: [Set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) e [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Pare o serviço SQL Server ao alterar a definição de cache dos discos Azure Virtual Machines para evitar a possibilidade de qualquer corrupção na base de dados.

* **Tamanho da unidade de atribuição NTFS**: Ao formatar o disco de dados, recomenda-se que utilize um tamanho de unidade de atribuição de 64 KB para ficheiros de dados e registos, bem como o TempDB. Se o TempDB for colocado no disco temporário (D:\ unidade) o desempenho obtido aproveitando esta unidade supera a necessidade de uma unidade de atribuição de 64K. 

* **Boas práticas de gestão do disco**: Ao remover um disco de dados ou alterar o seu tipo de cache, pare o serviço SQL Server durante a alteração. Quando as definições de cache são alteradas no disco OS, O Azure para o VM, altera o tipo de cache e reinicia o VM. Quando as definições de cache de um disco de dados são alteradas, o VM não é parado, mas o disco de dados é desligado do VM durante a alteração e, em seguida, recolocado.

  > [!WARNING]
  > A não paragem do serviço SQL Server durante estas operações pode causar corrupção na base de dados.


## <a name="io-guidance"></a>Orientação de I/O

* Os melhores resultados com SSDs premium são alcançados quando você paraleliza a sua aplicação e pedidos. Os SSDs premium são projetados para cenários em que a profundidade da fila IO é maior do que 1, por isso verá pouco ou nenhum ganho de desempenho para pedidos de série de rosca única (mesmo que sejam intensivos de armazenamento). Por exemplo, isto pode ter impacto nos resultados dos testes de análise de desempenho, tais como SQLIO.

* Considere usar a [compressão da página da base de dados,](https://msdn.microsoft.com/library/cc280449.aspx) pois pode ajudar a melhorar o desempenho das cargas de trabalho intensivas de I/O. No entanto, a compressão de dados pode aumentar o consumo de CPU no servidor de base de dados.

* Considere permitir a inicialização instantânea do ficheiro para reduzir o tempo necessário para a atribuição inicial de ficheiros. Para tirar partido da inicialização instantânea do ficheiro, concede à conta de serviço SQL Server (MSSQLSERVER) SE_MANAGE_VOLUME_NAME e adicioná-la à política de segurança **de Tarefas de Manutenção** de Volume de Desempenho. Se estiver a utilizar uma imagem da plataforma SQL Server para o Azure, a conta de serviço predefinida (NT Service\MSSQLSERVER) não é adicionada à política de segurança de Tarefas de Manutenção de **Volume de Execução.** Por outras palavras, a inicialização instantânea do ficheiro não é ativada numa imagem da plataforma SQL Server Azure. Depois de adicionar a conta de serviço SQL Server à política de segurança **de Tarefas de Manutenção** de Volume de Desempenho, reinicie o serviço SQL Server. Pode haver considerações de segurança para a utilização desta funcionalidade. Para obter mais informações, consulte [a inicialização do ficheiro database](https://msdn.microsoft.com/library/ms175935.aspx).

* Esteja ciente de que **o autogrow** é considerado apenas uma contingência para um crescimento inesperado. Não gerencie os seus dados e faça login o crescimento no dia-a-dia com o autogrow. Se for utilizado um auto-sobrancelha, pré-cresça o ficheiro utilizando o interruptor Tamanho.

* Certifique-se de que **o autoshrink** está desativado para evitar sobrecargas desnecessárias que possam afetar negativamente o desempenho.

* Mover todas as bases de dados para discos de dados, incluindo bases de dados do sistema. Para obter mais informações, consulte [as bases de dados do Sistema de Movimento](https://msdn.microsoft.com/library/ms345408.aspx).

* Mover o registo de erros do SQL Server e rastrear os diretórios de ficheiros para discos de dados. Isto pode ser feito no SQL Server Configuration Manager clicando à direita na sua instância do SQL Server e selecionando propriedades. As definições de registo de erros e de ficheiros de rastrear podem ser alteradas no **separador Parâmetros de Arranque.** O Diretório de Despejo está especificado no separador **Avançado.** A imagem que se segue mostra onde procurar o parâmetro de arranque de registo de erro.

    ![SQL ErrorLog Screenshot](./media/performance-guidelines-best-practices/sql_server_error_log_location.png)

* Configurar a cópia de segurança predefinida e as localizações dos ficheiros de base de dados. Utilize as recomendações deste artigo e faça as alterações na janela de propriedades do Servidor. Para obter instruções, consulte [Ver ou Alterar as Localizações Predefinidos para Ficheiros de Dados e Registo (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). A imagem que se segue demonstra onde fazer estas alterações.

    ![Ficheiros de registo de dados e backup de dados SQL](./media/performance-guidelines-best-practices/sql_server_default_data_log_backup_locations.png)
* Ativar páginas bloqueadas para reduzir io e quaisquer atividades de paging. Para obter mais informações, consulte [Ativar as páginas de bloqueio na opção Memória (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

* Se estiver a executar o SQL Server 2012, instale o Service Pack 1 Cumulativo Update 10. Esta atualização contém a correção para um mau desempenho em I/O quando executa selecione para declaração de tabela temporária no SQL Server 2012. Para obter informações, consulte este [artigo base de conhecimento.](https://support.microsoft.com/kb/2958012)

* Considere comprimir quaisquer ficheiros de dados ao transferir para dentro/para fora do Azure.

## <a name="feature-specific-guidance"></a>Orientação específica para recursos

Algumas implementações podem obter benefícios adicionais de desempenho usando técnicas de configuração mais avançadas. A lista a seguir destaca algumas funcionalidades do SQL Server que podem ajudá-lo a obter um melhor desempenho:

### <a name="back-up-to-azure-storage"></a>Voltar para o Azure Storage
Ao realizar backups para o SQL Server em execução em Máquinas Virtuais Azure, pode utilizar [backup do servidor SQL para URL](https://msdn.microsoft.com/library/dn435916.aspx). Esta funcionalidade está disponível a partir do SQL Server 2012 SP1 CU2 e recomendada para fazer o backup dos discos de dados anexados. Quando fizer backup/restaurar para/a partir do Azure Storage, siga as recomendações fornecidas no [SQL Server Backup para URL Best Practices e Troubleshooting and Restore from Backups Armazenados no Armazenamento Azure](https://msdn.microsoft.com/library/jj919149.aspx). Também pode automatizar estas cópias de segurança utilizando [cópias de segurança automatizadas para O Servidor SQL em Máquinas Virtuais Azure](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md).

Antes do SQL Server 2012, pode utilizar [backup do servidor SQL para a ferramenta Azure](https://www.microsoft.com/download/details.aspx?id=40740). Esta ferramenta pode ajudar a aumentar a produção de backup usando vários alvos de listras de backup.

### <a name="sql-server-data-files-in-azure"></a>Ficheiros de dados do servidor SQL em Azure

Esta nova funcionalidade, [SQL Server Data Files in Azure,](https://msdn.microsoft.com/library/dn385720.aspx)está disponível a partir do SQL Server 2014. Executar o SQL Server com ficheiros de dados em Azure demonstra características de desempenho comparáveis como a utilização de discos de dados Azure.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Instância de cluster de failover e espaços de armazenamento

Se estiver a utilizar espaços de armazenamento, ao adicionar nós ao cluster na página **de Confirmação,** limpe a caixa de verificação com a etiqueta **Adicionar todo o armazenamento elegível ao cluster**. 

![Armazenamento elegível descheck](./media/performance-guidelines-best-practices/uncheck-eligible-cluster-storage.png)

Se estiver a utilizar espaços de armazenamento e não desmarcar **Adicione todo o armazenamento elegível ao cluster, o**Windows destaca os discos virtuais durante o processo de agrupamento. Como resultado, não aparecem no Disk Manager ou No Explorer até que os espaços de armazenamento sejam removidos do cluster e religados utilizando o PowerShell. Os Espaços de Armazenamento agrulem vários discos para piscinas de armazenamento. Para mais informações, consulte [os Espaços de Armazenamento.](/windows-server/storage/storage-spaces/overview)

## <a name="multiple-instances"></a>Múltiplas instâncias 

Considere as seguintes boas práticas ao implementar várias instâncias do SQL Server numa única máquina virtual: 

- Desaperte a memória máxima do servidor para cada instância do SQL Server, certificando-se de que sobrou memória para o sistema operativo. Certifique-se de atualizar as restrições de memória para as instâncias do SQL Server se alterar a quantidade de memória que é atribuída à máquina virtual. 
- Tenha LUNs separados para dados, registos e TempDB, uma vez que todos eles têm diferentes padrões de carga de trabalho e você não quer que eles impactem uns aos outros. 
- Teste minuciosamente o seu ambiente sob cargas de trabalho pesadas semelhantes à produção para garantir que consegue lidar com a capacidade máxima de carga de trabalho dentro da sua aplicação SLAs. 

Os sinais de sistemas sobrecarregados podem incluir, mas não se limitam a, exaustão do fio do trabalhador, tempos de resposta lentos e/ou memória do sistema do sistema de despachante paralisado. 




## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre armazenamento e desempenho, consulte [as diretrizes de configuração de armazenamento para SQL Server em Azure Virtual Machines](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)

Para obter as melhores práticas de segurança, consulte [considerações de segurança para o SQL Server em Azure Virtual Machines](security-considerations-best-practices.md).

Reveja outros artigos da Máquina Virtual do SqL Server [no SQL Server na Visão Geral das Máquinas Virtuais Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). Se tiver dúvidas sobre máquinas virtuais do SQL Server, veja as [Perguntas Mais Frequentes](frequently-asked-questions-faq.md).
