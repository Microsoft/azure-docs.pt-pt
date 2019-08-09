---
title: Diretrizes de desempenho para SQL Server no Azure | Microsoft Docs
description: Fornece diretrizes para otimizar o desempenho de SQL Server em VMs Microsoft Azure.
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
ms.date: 09/26/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: d81c1941f114efbfd4ede559152317e907edeaea
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882392"
---
# <a name="performance-guidelines-for-sql-server-in-azure-virtual-machines"></a>Diretrizes de desempenho para SQL Server em máquinas virtuais do Azure

## <a name="overview"></a>Descrição geral

Este artigo fornece diretrizes para otimizar o desempenho de SQL Server em Microsoft Azure máquina virtual. Ao executar SQL Server em máquinas virtuais do Azure, recomendamos que você continue usando as mesmas opções de ajuste de desempenho do banco de dados que são aplicáveis a SQL Server no ambiente de servidor local. No entanto, o desempenho das bases de dados relacional numa cloud pública depende de muitos fatores, como o tamanho da máquina virtual e a configuração dos discos de dados.

[SQL Server imagens provisionadas no portal do Azure](quickstart-sql-vm-create-portal.md) seguir as práticas recomendadas de configuração de armazenamento geral (para obter mais informações sobre como o armazenamento é configurado, consulte [configuração de armazenamento para VMs SQL Server](virtual-machines-windows-sql-server-storage-configuration.md)). Após o provisionamento, considere aplicar outras otimizações discutidas neste artigo. Baseie suas escolhas em sua carga de trabalho e verifique por meio de testes.

> [!TIP]
> Normalmente, há uma compensação entre otimizar os custos e otimizar o desempenho. Este artigo se concentra em obter o *melhor* desempenho para SQL Server em VMs do Azure. Se sua carga de trabalho for menos exigente, talvez você não precise de todas as otimizações listadas abaixo. Considere suas necessidades de desempenho, custos e padrões de carga de trabalho ao avaliar essas recomendações.

## <a name="quick-check-list"></a>Lista de verificação rápida

A seguir está uma lista de verificação rápida para o desempenho ideal de SQL Server em máquinas virtuais do Azure:

| Área | Otimizações |
| --- | --- |
| [Tamanho da VM](#vm-size-guidance) | - [DS3_v2](../sizes-general.md) ou superior para SQL Enterprise Edition.<br/><br/> - [DS2_v2](../sizes-general.md) ou superior para SQL Standard e Web Editions. |
| [Armazenamento](#storage-guidance) | -Usar [SSDs Premium](../disks-types.md). O armazenamento Standard é recomendado apenas para desenvolvimento/teste.<br/><br/> -Mantenha a [conta de armazenamento](../../../storage/common/storage-create-storage-account.md) e SQL Server VM na mesma região.<br/><br/> * Desabilitar o [armazenamento com redundância geográfica](../../../storage/common/storage-redundancy.md) do Azure (replicação geográfica) na conta de armazenamento. |
| [Discos](#disks-guidance) | -Use um mínimo de 2 [discos p30](../disks-types.md#premium-ssd) (1 para arquivos de log e 1 para arquivos de dados, incluindo tempdb). Para cargas de trabalho que exigem ~ 50.000 IOPS, considere o uso de um SSD Ultra. <br/><br/> -Evite usar o sistema operacional ou discos temporários para armazenamento ou registro de banco de dados.<br/><br/> – Habilite o cache de leitura nos discos que hospedam os arquivos de dados e os arquivos de dados TempDB.<br/><br/> -Não habilite o cache em disco (s) que hospeda o arquivo de log.  **Importante**: Pare o serviço de SQL Server ao alterar as configurações de cache para um disco de VM do Azure.<br/><br/> – Distribua vários discos de dados do Azure para obter uma taxa de transferência de e/s aumentada.<br/><br/> -Formato com tamanhos de alocação documentados. <br/><br/> -Coloque o tempdb na unidade SSD `D:\` local para cargas de trabalho de SQL Server de missão crítica (depois de escolher o tamanho correto da VM). Mais informações no blog [usando SSDs para armazenar tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/).  |
| [I/O](#io-guidance) |-Habilitar compactação de página de banco de dados.<br/><br/> -Habilitar a inicialização instantânea de arquivo para arquivos de dados.<br/><br/> -Limite o aumento automático do banco de dados.<br/><br/> -Desabilitar a redução automática do banco de dados.<br/><br/> – Mover todos os bancos de dados para discos de data, incluindo bancos de dado do sistema.<br/><br/> -Mover SQL Server log de erros e rastrear diretórios de arquivos para discos de dados.<br/><br/> -Configurar os locais de arquivo de banco de dados e backup padrão.<br/><br/> – Habilitar páginas bloqueadas.<br/><br/> -Aplicar correções de desempenho de SQL Server. |
| [Específico do recurso](#feature-specific-guidance) | -Fazer backup diretamente no armazenamento de BLOBs. |

Para obter mais informações sobre *como* e *por que* fazer essas otimizações, consulte os detalhes e as diretrizes fornecidas nas seções a seguir.

## <a name="vm-size-guidance"></a>Diretrizes de tamanho de VM

Para aplicativos sensíveis ao desempenho, é recomendável que você use os seguintes [tamanhos de máquinas virtuais](../sizes.md):

* **Edição do SQL Server Enterprise**: DS3_v2 ou superior
* **SQL Server Standard e edições da Web**: DS2_v2 ou superior

[Série DSv2](../sizes-general.md#dsv2-series) As VMs dão suporte ao armazenamento Premium, o que é recomendado para o melhor desempenho. Os tamanhos recomendados aqui são linhas de base, mas o tamanho real da máquina que você seleciona depende de suas demandas de carga de trabalho. As VMs da série DSv2 são VMs de finalidade geral que são boas para uma variedade de cargas de trabalho, enquanto outros tamanhos de computadores são otimizados para tipos de carga de trabalho específicos. Por exemplo, a [série M](../sizes-memory.md#m-series) oferece a maior contagem de vCPU e memória para as maiores cargas de trabalho de SQL Server. A [série GS](../sizes-previous-gen.md#gs-series) e a [série DSv2 11-15](../sizes-memory.md#dsv2-series-11-15) são otimizadas para grandes requisitos de memória. Ambas as séries também estão disponíveis em [tamanhos de núcleo restritos](../../windows/constrained-vcpu.md), o que poupa dinheiro para cargas de trabalho com demandas de computação mais baixas. As máquinas da [série ls](../sizes-storage.md) são otimizadas para alta taxa de transferência e e/s de disco. É importante considerar sua carga de trabalho SQL Server específica e aplicá-la à sua seleção de uma série de VMs e tamanho.

## <a name="storage-guidance"></a>Orientações de armazenamento

As VMs da série DS (juntamente com as séries DSv2 e GS) dão suporte ao [SSDs Premium](../disks-types.md). O SSDs Premium é recomendado para todas as cargas de trabalho de produção.

> [!WARNING]
> Os HDDs e SSDs padrão têm latências e larguras de banda variáveis e são recomendados apenas para cargas de trabalho de desenvolvimento/teste. As cargas de trabalho de produção devem usar o SSDs Premium.

Além disso, recomendamos que você crie sua conta de armazenamento do Azure na mesma data center que suas máquinas virtuais SQL Server para reduzir os atrasos de transferência. Ao criar uma conta de armazenamento, desabilite a replicação geográfica, pois a ordem de gravação consistente em vários discos não é garantida. Em vez disso, considere configurar uma tecnologia de recuperação de desastres SQL Server entre dois data centers do Azure. Para obter mais informações, consulte [alta disponibilidade e recuperação de desastre para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Diretrizes de discos

Há três tipos de disco principais em uma VM do Azure:

* **Disco do so**: Quando você cria uma máquina virtual do Azure, a plataforma anexará pelo menos um disco (rotulado como a unidade **C** ) à VM para o disco do sistema operacional. Esse disco é um VHD armazenado como um blob de páginas no armazenamento.
* **Disco temporário**: As máquinas virtuais do Azure contêm outro disco chamado disco temporário (rotulado como a unidade **D**:). Este é um disco no nó que pode ser usado para o espaço transitório.
* **Discos de dados**: Você também pode anexar discos adicionais à sua máquina virtual como discos de dados, e eles serão armazenados no armazenamento como BLOBs de páginas.

As seções a seguir descrevem as recomendações para o uso desses discos diferentes.

### <a name="operating-system-disk"></a>Disco do sistema operativo

Um disco do sistema operacional é um VHD que pode ser inicializado e montado como uma versão em execução de um sistema operacional e rotulado como unidade **C** .

A política de cache padrão no disco do sistema operacional é de **leitura/gravação**. Para aplicativos sensíveis ao desempenho, recomendamos que você use discos de dados em vez do disco do sistema operacional. Consulte a seção em discos de dados abaixo.

### <a name="temporary-disk"></a>Disco temporário

A unidade de armazenamento temporário, rotulada como a unidade **D**:, não é persistida no armazenamento de BLOBs do Azure. Não armazene os arquivos de banco de dados do usuário ou os arquivos de log de transações do usuário na unidade **D**:.

Para VMs D-Series, Dv2 e série G, a unidade temporária nessas VMs é baseada em SSD. Se sua carga de trabalho faz uso intensivo de TempDB (como objetos temporários ou junções complexas), o armazenamento de TempDB na unidade **D** pode resultar em maior taxa de transferência de tempdb e menor latência de tempdb. Para obter um cenário de exemplo, consulte a discussão de TempDB na seguinte postagem de blog: [Diretrizes de configuração de armazenamento para SQL Server na VM do Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm).

Para VMs que dão suporte a SSDs Premium (série DS, DSv2 e GS), é recomendável armazenar TempDB em um disco que dá suporte ao SSDs Premium com cache de leitura habilitado.

Há uma exceção a essa recomendação: _se o uso de tempdb for de gravação intensiva, você poderá obter um melhor desempenho armazenando tempdb na unidade **D** local, que também é baseada em SSD nesses tamanhos de computador._ Examine o blog [usando SSDs com tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) para obter mais informações. 

### <a name="data-disks"></a>Discos de dados

* **Usar discos de dados para arquivos de dados e de log**: Se você não estiver usando a distribuição de disco, use dois discos p30 SSD Premium em que um disco contém os arquivos de log e o outro contém os dados e TempDB (exceto para cargas de trabalho de missão crítica e de gravação pesada, conforme mencionado acima). Cada SSD Premium fornece um número de IOPs e largura de banda (MB/s) dependendo de seu tamanho, conforme descrito no artigo, [Selecione um tipo de disco](../disks-types.md). Se você estiver usando uma técnica de distribuição de disco, como espaços de armazenamento, obterá um desempenho ideal tendo dois pools, um para os arquivos de log e o outro para os arquivos de dados. No entanto, se você planeja usar SQL Server FCI (instâncias de cluster de failover), deverá configurar um pool.

   > [!TIP]
   > - Para obter resultados de teste sobre várias configurações de disco e carga de trabalho, consulte a seguinte postagem no blog: [Diretrizes de configuração de armazenamento para SQL Server na VM do Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/).
   > - Para desempenho crítico para servidores SQL que precisam de ~ 50.000 IOPS, considere substituir discos de 10 p30 por um SSD Ultra. Para obter mais informações, consulte a seguinte postagem no blog: [Desempenho de missão crítica com SSD ultra](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > Ao provisionar uma VM SQL Server no portal, você tem a opção de editar sua configuração de armazenamento. Dependendo de sua configuração, o Azure configura um ou mais discos. Vários discos são combinados em um único pool de armazenamento com distribuição. Os arquivos de dados e de log residem juntos nessa configuração. Para obter mais informações, consulte [configuração de armazenamento para VMs SQL Server](virtual-machines-windows-sql-server-storage-configuration.md).

* **Distribuição de disco**: Para obter mais taxa de transferência, você pode adicionar mais discos de dados e usar a distribuição de disco. Para determinar o número de discos de dados, você precisa analisar o número de IOPS e largura de banda necessários para seus arquivos de log e para seus dados e arquivos TempDB. Observe que diferentes tamanhos de VM têm limites diferentes no número de IOPs e largura de banda com suporte, consulte as tabelas em IOPS por [tamanho de VM](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Use as seguintes diretrizes:

  * Para o Windows 8/Windows Server 2012 ou posterior, use [espaços de armazenamento](https://technet.microsoft.com/library/hh831739.aspx) com as seguintes diretrizes:

      1. Defina a intercalação (tamanho da faixa) como 64 KB (65536 bytes) para cargas de trabalho OLTP e 256 KB (262144 bytes) para cargas de trabalho de data warehouse para evitar o impacto no desempenho devido ao desalinhamento da partição. Isso deve ser definido com o PowerShell.
      2. Definir contagem de colunas = número de discos físicos. Use o PowerShell ao configurar mais de 8 discos (não Gerenciador do Servidor interface do usuário). 

    Por exemplo, o PowerShell a seguir cria um novo pool de armazenamento com o tamanho de intercalação para 64 KB e o número de colunas para 2:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Para o Windows 2008 R2 ou anterior, você pode usar discos dinâmicos (volumes distribuídos do sistema operacional) e o tamanho da distribuição é sempre 64 KB. Observe que essa opção foi preterida a partir do Windows 8/Windows Server 2012. Para obter informações, consulte a instrução de suporte no [serviço de disco virtual está em transição para a API de gerenciamento de armazenamento do Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Se você estiver usando [espaços de armazenamento diretos (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) com [SQL Server instâncias de cluster de failover](virtual-machines-windows-portal-sql-create-failover-cluster.md), deverá configurar um único pool. Observe que, embora volumes diferentes possam ser criados nesse único pool, todos eles compartilharão as mesmas características, como a mesma política de cache.

  * Determine o número de discos associados ao seu pool de armazenamento com base nas expectativas de carga. Tenha em mente que diferentes tamanhos de VM permitem diferentes números de discos de dados anexados. Para obter mais informações, consulte [tamanhos das máquinas virtuais](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Se você não estiver usando o SSDs Premium (cenários de desenvolvimento/teste), a recomendação é adicionar o número máximo de discos de dados com suporte pelo [tamanho da VM](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e usar a distribuição de disco.

* **Política de cache**: Observe as seguintes recomendações para a política de cache, dependendo da sua configuração de armazenamento.

  * Se você estiver usando discos separados para arquivos de dados e de log, habilite o cache de leitura nos discos de dados que hospedam seus arquivos de dados e arquivos de dados TempDB. Isso pode resultar em um benefício de desempenho significativo. Não habilite o cache no disco que contém o arquivo de log, pois isso causa uma pequena redução no desempenho.

  * Se você estiver usando a distribuição de disco em um único pool de armazenamento, a maioria das cargas de trabalho se beneficiará do cache de leitura. Se você tiver pools de armazenamento separados para os arquivos de log e de dados, habilite o cache de leitura somente no pool de armazenamento para os arquivos de dados. Em determinadas cargas de trabalho de gravação pesadas, um melhor desempenho pode ser obtido sem cache. Isso só pode ser determinado por meio de teste.

  * As recomendações anteriores se aplicam ao SSDs Premium. Se você não estiver usando o SSDs Premium, não habilite nenhum cache em nenhum disco de dados.

  * Para obter instruções sobre como configurar o cache de disco, consulte os artigos a seguir. Para o modelo de implantação clássico (ASM), consulte: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) e [set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). Para o modelo de implantação de Azure Resource Manager, consulte: [Set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) e [set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Pare o serviço de SQL Server ao alterar a configuração de cache de discos de VM do Azure para evitar a possibilidade de qualquer corrupção de banco de dados.

* **Tamanho da unidade de alocação NTFS**: Ao Formatar o disco de dados, é recomendável que você use um tamanho de unidade de alocação de 64 KB para arquivos de dados e de log, bem como TempDB.

* **Práticas recomendadas de gerenciamento de disco**: Ao remover um disco de dados ou alterar seu tipo de cache, interrompa o serviço de SQL Server durante a alteração. Quando as configurações de cache são alteradas no disco do sistema operacional, o Azure interrompe a VM, altera o tipo de cache e reinicia a VM. Quando as configurações de cache de um disco de dados são alteradas, a VM não é interrompida, mas o disco de dados é desanexado da VM durante a alteração e, em seguida, reanexado.

  > [!WARNING]
  > A falha na interrupção do serviço de SQL Server durante essas operações pode causar corrupção de banco de dados.


## <a name="io-guidance"></a>Diretrizes de e/s

* Os melhores resultados com o SSDs Premium são obtidos quando você paraleliza seu aplicativo e as solicitações. O SSDs Premium foi projetado para cenários em que a profundidade da fila de e/s é maior que 1, portanto, você verá pouco ou nenhum ganho de desempenho para solicitações de série de thread único (mesmo que elas tenham uso intensivo de armazenamento). Por exemplo, isso pode afetar os resultados de teste de thread único de ferramentas de análise de desempenho, como SQLIO.

* Considere usar a compactação de [página de banco de dados](https://msdn.microsoft.com/library/cc280449.aspx) , pois ela pode ajudar a melhorar o desempenho de cargas de trabalho intensivas de e/s No entanto, a compactação de dados pode aumentar o consumo de CPU no servidor do banco de dados.

* Considere habilitar a inicialização instantânea de arquivo para reduzir o tempo necessário para a alocação de arquivo inicial. Para aproveitar a inicialização instantânea de arquivo, conceda a conta de serviço SQL Server (MSSQLSERVER) com SE_MANAGE_VOLUME_NAME e adicione-a à política de segurança **executar tarefas de manutenção de volume** . Se você estiver usando uma imagem de plataforma SQL Server para o Azure, a conta de serviço padrão (NT Service\MSSQLSERVER) não será adicionada à política de segurança **executar tarefas de manutenção de volume** . Em outras palavras, a inicialização instantânea de arquivo não está habilitada em uma imagem da plataforma SQL Server Azure. Depois de adicionar a conta de serviço do SQL Server à política de segurança **executar tarefas de manutenção de volume** , reinicie o serviço SQL Server. Pode haver considerações de segurança para o uso desse recurso. Para obter mais informações, consulte [inicialização de arquivo de banco de dados](https://msdn.microsoft.com/library/ms175935.aspx).

* o crescimento **automático** é considerado meramente uma contingência para o crescimento inesperado. Não gerencie seus dados e o crescimento do log diariamente com o crescimento automático. Se o crescimento automático for usado, expanda o arquivo usando a opção de tamanho.

* Verifique se a **redução** automática está desabilitada para evitar sobrecarga desnecessária que pode afetar negativamente o desempenho.

* Mova todos os bancos de dados para discos de data, incluindo bancos de dado do sistema. Para obter mais informações, consulte [mover bancos de dados do sistema](https://msdn.microsoft.com/library/ms345408.aspx).

* Mover SQL Server log de erros e rastrear diretórios de arquivos para discos de dados. Isso pode ser feito em SQL Server Configuration Manager clicando com o botão direito do mouse na instância do SQL Server e selecionando Propriedades. As configurações log de erros e arquivo de rastreamento podem ser alteradas na guia **parâmetros de inicialização** . O diretório de despejo é especificado na guia **avançado** . A captura de tela a seguir mostra onde procurar o parâmetro de inicialização do log de erros.

    ![Captura de tela de erros SQL](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* Configurar backup padrão e locais de arquivo de banco de dados. Use as recomendações neste artigo e faça as alterações na janela Propriedades do servidor. Para obter instruções, consulte [Exibir ou alterar os locais padrão para arquivos de dados e de log (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). A captura de tela a seguir demonstra onde fazer essas alterações.

    ![Log de dados SQL e arquivos de backup](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Habilite páginas bloqueadas para reduzir a e/s e qualquer atividade de paginação. Para obter mais informações, consulte [habilitar a opção bloquear páginas na memória (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

* Se você estiver executando o SQL Server 2012, instale a atualização cumulativa 10 do Service Pack 1. Essa atualização contém a correção para baixo desempenho na e/s quando você executa a instrução SELECT INTO na tabela temporária no SQL Server 2012. Para obter informações, consulte este [artigo da base de dados de conhecimento](https://support.microsoft.com/kb/2958012).

* Considere compactar todos os arquivos de dados ao transferir para dentro/fora do Azure.

## <a name="feature-specific-guidance"></a>Diretrizes específicas do recurso

Algumas implantações podem alcançar benefícios de desempenho adicionais usando técnicas de configuração mais avançadas. A lista a seguir destaca alguns SQL Server recursos que podem ajudá-lo a obter um melhor desempenho:

### <a name="backup-to-azure-storage"></a>Backup no armazenamento do Azure
Ao executar backups para SQL Server em execução em máquinas virtuais do Azure, você pode usar [SQL Server Backup para URL](https://msdn.microsoft.com/library/dn435916.aspx). Esse recurso está disponível a partir do SQL Server 2012 SP1 CU2 e recomendado para fazer backup nos discos de dados anexados. Quando você faz backup/restauração de/para o armazenamento do Azure, siga as recomendações fornecidas em [SQL Server Backup em URL práticas recomendadas e solução de problemas e restauração de backups armazenados no armazenamento do Azure](https://msdn.microsoft.com/library/jj919149.aspx). Você também pode automatizar esses backups usando o [backup automatizado para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-automated-backup.md).

Antes do SQL Server 2012, você pode usar [SQL Server Backup para a ferramenta do Azure](https://www.microsoft.com/download/details.aspx?id=40740). Essa ferramenta pode ajudar a aumentar a taxa de transferência de backup usando vários destinos de distribuição de backup.

### <a name="sql-server-data-files-in-azure"></a>SQL Server arquivos de dados no Azure

Esse novo recurso, [SQL Server arquivos de dados no Azure](https://msdn.microsoft.com/library/dn385720.aspx), está disponível a partir do SQL Server 2014. A execução de SQL Server com arquivos de dados no Azure demonstra características de desempenho comparáveis com o uso de discos de dados do Azure.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Instância de cluster de failover e espaços de armazenamento

Se você estiver usando espaços de armazenamento, ao adicionar nós ao cluster na página de **confirmação** , desmarque a caixa de seleção **Adicionar todo o armazenamento elegível ao cluster**. 

![Desmarque armazenamento qualificado](media/virtual-machines-windows-sql-performance/uncheck-eligible-cluster-storage.png)

Se você estiver usando espaços de armazenamento e não marcar a seleção **Adicionar todo o armazenamento elegível ao cluster**, o Windows desanexará os discos virtuais durante o processo de clustering. Como resultado, eles não aparecerão no Gerenciador de discos ou no Explorer até que os espaços de armazenamento sejam removidos do cluster e reanexados usando o PowerShell. Os espaços de armazenamento agrupam vários discos em pools de armazenamento. Para obter mais informações, consulte [espaços de armazenamento](/windows-server/storage/storage-spaces/overview).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre armazenamento e desempenho, consulte [diretrizes de configuração de armazenamento para SQL Server na VM do Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)

Para obter as práticas recomendadas de segurança, consulte [considerações de segurança para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-security.md).

Examine outros artigos de máquina virtual SQL Server em [SQL Server em visão geral de máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md). Se tiver dúvidas sobre máquinas virtuais do SQL Server, veja as [Perguntas Mais Frequentes](virtual-machines-windows-sql-server-iaas-faq.md).
