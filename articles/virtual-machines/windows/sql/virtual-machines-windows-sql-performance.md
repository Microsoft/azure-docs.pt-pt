---
title: Melhores práticas de desempenho para o SQL Server no Azure | Documentos da Microsoft
description: Disponibiliza as melhores práticas para otimizar o desempenho do SQL Server em VMs do Azure de Microsoft.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/19/2018
ms.author: jroth
ms.openlocfilehash: a7a24bde6cc34befee7de3bcbf13b96c8b641af2
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888913"
---
# <a name="performance-best-practices-for-sql-server-in-azure-virtual-machines"></a>Melhores práticas de desempenho do SQL Server nas Máquinas Virtuais do Azure

## <a name="overview"></a>Descrição geral

Este artigo fornece orientações para otimizar o desempenho do SQL Server na máquina Virtual do Microsoft Azure. Ao executar o SQL Server em máquinas de virtuais do Azure, recomendamos que continua a utilizar o mesmo desempenho de base de dados, ajuste as opções que se aplicam ao SQL Server no ambiente de servidor no local. No entanto, o desempenho da base de dados relacional na cloud pública depende de vários fatores, como o tamanho de uma máquina virtual e a configuração dos discos de dados.

[Imagens do SQL Server aprovisionadas no portal do Azure](quickstart-sql-vm-create-portal.md) siga práticas recomendadas de configuração de armazenamento. Para obter mais informações sobre como o armazenamento é configurado, consulte [configuração do armazenamento para VMs do SQL Server](virtual-machines-windows-sql-server-storage-configuration.md). Após o aprovisionamento, considere a aplicar outras otimizações discutidas neste artigo. As opções de base na carga de trabalho e certifique-se por meio do teste.

> [!TIP]
> Este artigo se concentra em fazer os *melhor* desempenho para o SQL Server em VMs do Azure. Se a carga de trabalho menos exigentes, não poderá necessitar de otimização de todos os listados abaixo. Considere seus padrões de carga de trabalho e necessidades de desempenho durante a avaliação estas recomendações.

## <a name="quick-check-list"></a>Lista de verificação rápida

Segue-se uma lista de verificação rápida para um desempenho ideal do SQL Server em máquinas de virtuais do Azure:

| Área | Otimizações |
| --- | --- |
| [Tamanho da VM](#vm-size-guidance) |[DS3_v2](../sizes-general.md) ou superior para o SQL Enterprise edition.<br/><br/>[DS2_v2](../sizes-general.md) ou superior para as edições SQL Standard e Web. |
| [Armazenamento](#storage-guidance) |Uso [o armazenamento Premium](../premium-storage.md). Armazenamento Standard só é recomendado para programação/teste.<br/><br/>Manter o [conta de armazenamento](../../../storage/common/storage-create-storage-account.md) e VM do SQL Server na mesma região.<br/><br/>Desativar o Azure [armazenamento georredundante](../../../storage/common/storage-redundancy.md) (georreplicação) na conta de armazenamento. |
| [Discos](#disks-guidance) |Utilizar um mínimo de 2 [P30 discos](../premium-storage.md#scalability-and-performance-targets) (1 para ficheiros de registo e 1 para ficheiros de dados e TempDB; ou stripe dois ou mais discos e armazenamento de todos os ficheiros num único volume).<br/><br/>Evite utilizar o sistema operativo ou discos temporários para o armazenamento de base de dados ou de registo.<br/><br/>Ative a colocação em cache de leitura no disco que aloja os ficheiros de dados e ficheiros de dados TempDB.<br/><br/>Não ative a colocação em cache no disco (s) que aloja o ficheiro de registo.<br/><br/>Importante: Pare o serviço do SQL Server ao alterar as definições de cache para um disco de VM do Azure.<br/><br/>Vários discos de dados do Azure para obter maior débito de e/s do stripe.<br/><br/>Formatar com tamanhos de alocação documentado. |
| [E/S](#io-guidance) |Ative a compressão de página da base de dados.<br/><br/>Ative a inicialização instantânea de arquivo para ficheiros de dados.<br/><br/>Limitar o aumento automático no banco de dados.<br/><br/>Desative início do encolhimento automático no banco de dados.<br/><br/>Mova todas as bases de dados para discos de dados, incluindo bases de dados do sistema.<br/><br/>Mova SQL Server erro log e rastreamento de diretórios de arquivos para discos de dados.<br/><br/>Configure localizações de ficheiros de cópia de segurança e a base de dados predefinidas.<br/><br/>Ative páginas bloqueadas.<br/><br/>Aplica correções de desempenho do SQL Server. |
| [Específicos de funcionalidades](#feature-specific-guidance) |Criar cópias de segurança diretamente ao armazenamento de Blobs. |

Para obter mais informações sobre *como* e *motivo pelo qual* para tornar essas otimizações, reveja os detalhes e as diretrizes fornecidas nas secções seguintes.

## <a name="vm-size-guidance"></a>Documentação de orientação de tamanho VM

Para aplicações confidenciais de desempenho, é recomendado que utilize o seguinte procedimento [tamanhos de máquinas virtuais](../sizes.md):

* **SQL Server Enterprise Edition**: DS3_v2 ou superior
* **SQL Server Standard e Web edições**: DS2_v2 ou superior

[Série DSv2](../sizes-general.md#dsv2-series) VMs suportam o premium storage, o que é recomendado para o melhor desempenho. Os tamanhos recomendados aqui são as linhas de base, mas o tamanho real do computador que selecionar depende de suas necessidades de carga de trabalho. As VMs da série DSv2 são VMs que são bons para uma variedade de cargas de trabalho, ao passo que outros tamanhos de máquinas estão otimizados para tipos de carga de trabalho específica para fins gerais. Por exemplo, o [série M](../sizes-memory.md#m-series) oferece a contagem de vCPU mais alta e a memória para as maiores cargas de trabalho do SQL Server. O [série GS](../sizes-memory.md#gs-series) e [11 a 15 de série DSv2](../sizes-memory.md#dsv2-series-11-15) estão otimizados para os requisitos de memória de grandes dimensões. Ambos esse série também estão disponíveis no [restrita tamanhos de núcleo](../../windows/constrained-vcpu.md), que poupa dinheiro para cargas de trabalho com menor demandas de computação. O [série Ls](../sizes-storage.md) máquinas estão otimizadas para débito de disco elevados e e/s. É importante considerar sua carga de trabalho do SQL Server específica e aplicá-la a sua seleção de uma série VM e o tamanho.

## <a name="storage-guidance"></a>Orientações do armazenamento

Suporte de VMs de série DS (juntamente com a série DSv2 e série GS) [o armazenamento Premium](../premium-storage.md). Para todas as cargas de trabalho de produção, recomenda-se o armazenamento Premium.

> [!WARNING]
> Armazenamento standard tem largura de banda e latências variados e só é recomendado para cargas de trabalho de programador/teste. Cargas de trabalho de produção, devem utilizar o armazenamento Premium.

Além disso, recomendamos que crie a sua conta de armazenamento do Azure no mesmo centro de dados como suas máquinas virtuais do SQL Server para reduzir os atrasos de transferência. Ao criar uma conta de armazenamento, desative replicação geográfica como a ordem de escrita consistente em vários discos não é garantida. Em vez disso, considere configurar uma tecnologia de recuperação de desastres do SQL Server entre dois datacenters do Azure. Para obter mais informações, consulte [elevada disponibilidade e recuperação após desastre para SQL Server em máquinas de virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Orientações de discos

Existem três tipos de disco principal numa VM do Azure:

* **Disco do SO**: Quando cria uma Máquina Virtual do Azure, a plataforma se ligará a pelo menos um disco (identificado como o **C** unidade) para a VM para o seu disco de sistema operativo. Este disco é um VHD armazenado como um blob de página no armazenamento.
* **Disco temporário**: máquinas virtuais do Azure contêm outro disco chamado o disco temporário (identificado como o **1!d**: unidade). Este é um disco no nó que pode ser utilizado para espaço transitório.
* **Discos de dados**: também pode anexar discos adicionais à sua máquina virtual como discos de dados e estes serão armazenados no armazenamento de BLOBs de página.

As secções seguintes descrevem as recomendações para utilizar estes discos diferentes.

### <a name="operating-system-disk"></a>Disco do sistema operativo

Disco de sistema operativo é um VHD que pode efetuar o arranque e montar como uma versão em execução de um sistema operacional e tem o nome como **C** unidade.

Política de no disco do sistema operativo de colocação em cache predefinida é **leitura/escrita**. Para aplicações confidenciais de desempenho, recomendamos que utilize discos de dados em vez de disco do sistema operativo. Consulte a secção em discos de dados abaixo.

### <a name="temporary-disk"></a>Disco temporário

A unidade de armazenamento temporário, identificada como a **1!d**: unidade, não é persistente para o armazenamento de Blobs do Azure. Não armazene os ficheiros de base de dados de utilizador ou arquivos de log de transação de utilizador sobre o **1!d**: unidade.

Para a série D, série Dv2 e VMs de série G, a unidade temporária nestas VMs é baseado em SSD. Se a sua carga de trabalho faz uso intensivo de TempDB (por exemplo, objetos temporários ou associações complexas), armazenamento de TempDB no **1!d** unidade poderia resultar num maior débito de TempDB e menor latência de TempDB.

Para as VMs que suportam o armazenamento Premium (séries DS, série DSv2 e série GS), recomendamos o armazenamento de TempDB num disco que suporta o armazenamento Premium com cache de leitura ativado. Há uma exceção para esta recomendação; Se a utilização de TempDB está escrita intensiva, pode alcançar um desempenho mais elevado por meio do armazenamento TempDB no local **1!d** unidade, o que também é baseado em SSD nestes tamanhos de máquina.

### <a name="data-disks"></a>Discos de dados

* **Utilizar discos de dados para ficheiros de dados e de registo**: Se não estiver a utilizar a repartição de disco, utilize o armazenamento Premium dois [P30 discos](../premium-storage.md#scalability-and-performance-targets) em que um disco contém os ficheiros de registo e a outra contém os dados e ficheiros de TempDB. Cada disco de armazenamento Premium fornece um número de IOPs e largura de banda (MB/s) dependendo de seu tamanho, conforme descrito no artigo, [utilizando o armazenamento Premium para discos](../premium-storage.md). Se estiver a utilizar uma técnica de repartição de disco, tais como espaços de armazenamento, é recomendável colocar todos os dados e ficheiros na mesma unidade de registo.

   > [!NOTE]
   > Quando aprovisionar uma VM do SQL Server no portal, tem a opção de editar a configuração de armazenamento. Consoante a configuração, o Azure configura um ou mais discos. Vários discos são combinados num agrupamento de armazenamento única com repartição. Ambos os ficheiros de dados e de registo de residir em conjunto nesta configuração. Para obter mais informações, consulte [configuração do armazenamento para VMs do SQL Server](virtual-machines-windows-sql-server-storage-configuration.md).

* **Repartição de disco**: para obter mais débito, pode adicionar discos de dados adicionais e utilize a repartição de disco. Para determinar o número de discos de dados, terá de analisar o número de IOPS e largura de banda necessária para o ficheiro de registo (s) e para seus dados e ficheiros de TempDB. Tenha em atenção que os diferentes tamanhos de VM têm limites diferentes no número de IOPs e largura de banda suportados, consulte as tabelas sobre o IOPS por [tamanho da VM](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Utilize as seguintes diretrizes:

  * Para o Windows 8 e Windows Server 2012 ou posterior, utilize [espaços de armazenamento](https://technet.microsoft.com/library/hh831739.aspx) com as seguintes diretrizes:

      1. Defina o interleave (tamanho da faixa) de 64 KB (65536 bytes) para cargas de trabalho OLTP e 256 KB (262144 bytes) para cargas de trabalho de armazenamento de dados para evitar o impacto de desempenho devido ao alinhamento incorreto da partição. Tem de ser definida com o PowerShell.
      1. Definir o número de colunas = número de discos físicos. Utilize o PowerShell, ao configurar mais de 8 discos (não a IU de Gestor de servidor). 

    Por exemplo, o PowerShell seguinte cria um novo agrupamento de armazenamento com o tamanho de intercalação de 64 KB e o número de colunas para 2:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Para o Windows 2008 R2 ou anterior, pode usar discos dinâmicos (volumes repartidos de SO) e o tamanho do stripe é sempre de 64 KB. Tenha em atenção que esta opção foi preterida a partir do Windows 8 e Windows Server 2012. Para obter informações, consulte a declaração de suporte em [serviço de discos virtuais é fazer a transição para a API de gestão de armazenamento do Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Se estiver a utilizar [espaços de armazenamento direto (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) com um cenário como [instâncias de Cluster de ativação pós-falha do SQL Server](virtual-machines-windows-portal-sql-create-failover-cluster.md), tem de configurar um único conjunto. Observe que embora volumes diferentes podem ser criadas nesse único agrupamento, eles serão todas partilham as mesmas características, como a mesma política de colocação em cache.

  * Determine o número de discos associados com o agrupamento de armazenamento com base em suas expectativas de carga. Tenha em atenção que os diferentes tamanhos de VM permitem números diferentes de discos de dados anexados. Para obter mais informações, consulte [tamanhos de máquinas virtuais](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Se não estiver a utilizar o armazenamento Premium (cenários de desenvolvimento/teste), a recomendação é adicionar o número máximo de discos de dados suportados pela sua [tamanho da VM](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e utilize a repartição de disco.

* **Política de colocação em cache**: tenha em atenção as seguintes recomendações para colocar em cache da política consoante a configuração de armazenamento.

  * Se estiver a utilizar discos separados para os ficheiros de dados e de registo, ative a colocação em cache de leitura nos discos de dados que aloja os seus ficheiros de dados e ficheiros de dados TempDB. Isso pode resultar num benefício significativo no desempenho. Não ative a colocação em cache no disco que contém o ficheiro de registo, pois Isto causa uma pequena diminuição no desempenho.

  * Se estiver a utilizar a repartição de disco, a maioria das cargas de trabalho se beneficiarão da colocação em cache de leitura. Devido ao ganho de desempenho com repartição de disco, esta recomendação aplica-se mesmo quando o ficheiro de registo está na mesma unidade. Em determinadas cargas de trabalho pesadas de escrita, um melhor desempenho pode ser obtido sem cache. Só pode ser determinada por meio do teste.

  * As recomendações anteriores se aplica aos discos de armazenamento Premium. Se não estiver a utilizar o armazenamento Premium, não permitem o armazenamento em cache em qualquer discos de dados.

  * Para obter instruções sobre como configurar a colocação em cache do disco, consulte os artigos seguintes. Para o modelo de implementação clássico (ASM), consulte: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) e [conjunto AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). Para o modelo de implementação Azure Resource Manager, consulte: [Set-AzureRMOSDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk?view=azurermps-4.4.1) e [Set-AzureRMVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmdatadisk?view=azurermps-4.4.1).

     > [!WARNING]
     > Pare o serviço do SQL Server quando alterar a definição de cache dos discos de VM do Azure para evitar a possibilidade de qualquer dano de base de dados.

* **Tamanho da unidade de alocação NTFS**: ao formatar o disco de dados, é recomendado que utilize um tamanho de unidade de alocação de 64 KB de dados e arquivos de log, bem como TempDB.

* **Práticas recomendadas de gerenciamento de disco**: Quando remover um disco de dados ou alterar seu tipo de cache, pare o serviço do SQL Server durante a alteração. Quando as definições de colocação em cache são alteradas no disco do SO, o Azure para a VM, altera o tipo de cache e reinicia a VM. Quando as definições de cache de um disco de dados são alteradas, a VM não está parada, mas o disco de dados é desligado da VM durante a alteração e, em seguida, voltar a ligar.

  > [!WARNING]
  > Falha ao parar o serviço SQL Server durante estas operações pode causar Corrupção de base de dados.

## <a name="io-guidance"></a>Orientações de e/s

* Os melhores resultados com o armazenamento Premium são alcançados ao executar em paralelo a sua aplicação e os pedidos. O armazenamento Premium destina-se a cenários em que a profundidade de fila de e/s é superior a 1, verá pouca ou nenhuma ganhos de desempenho para um único thread pedidos seriais (mesmo que estejam intensivas em termos de armazenamento). Por exemplo, isto poderia afetar os resultados do teste de thread único de ferramentas de análise de desempenho, como SQLIO.

* Considere a utilização [compactação page de base de dados](https://msdn.microsoft.com/library/cc280449.aspx) como o pode ajudar a melhorar o desempenho de cargas de trabalho intensivas de e/s. No entanto, a compressão de dados pode aumentar o consumo da CPU no servidor de base de dados.

* Considere ativar a inicialização instantânea de arquivo reduzir o tempo necessário para alocação de ficheiro inicial. Para aproveitar a inicialização instantânea de arquivo, de conceder à conta de serviço do SQL Server (MSSQLSERVER) com SE_MANAGE_VOLUME_NAME e adicioná-lo para o **efetuar tarefas de manutenção de Volume** política de segurança. Se estiver a utilizar uma imagem de plataforma do SQL Server para o Azure, a conta de serviço predefinida (NT Service\MSSQLSERVER) não é adicionada para o **efetuar tarefas de manutenção de Volume** política de segurança. Em outras palavras, a inicialização instantânea de arquivo não está ativada numa imagem de plataforma do Azure do SQL Server. Depois de adicionar a conta de serviço do SQL Server para o **efetuar tarefas de manutenção de Volume** política de segurança, reinicie o serviço do SQL Server. Pode haver considerações de segurança para utilizar esta funcionalidade. Para obter mais informações, consulte [inicialização de arquivo de banco de dados](https://msdn.microsoft.com/library/ms175935.aspx).

* **o aumento automático** é considerado como estando meramente um plano de contingência crescimento inesperado. Não gerir o crescimento dos seus dados e de registo diariamente com aumento automático. Se o aumento automático for utilizado, aumente previamente o arquivo usando a opção de tamanho.

* Certifique-se **encolhimento automático** está desativada para evitar a sobrecarga desnecessária, que pode afetar negativamente o desempenho.

* Mova todas as bases de dados para discos de dados, incluindo bases de dados do sistema. Para obter mais informações, consulte [mover bases de dados de sistema](https://msdn.microsoft.com/library/ms345408.aspx).

* Mova SQL Server erro log e rastreamento de diretórios de arquivos para discos de dados. Isso pode ser feito no Gestor de configuração do SQL Server ao clicar com o botão direito a instância do SQL Server e selecionar propriedades. Definições de ficheiro de registo e rastreio de erro podem ser alteradas no **parâmetros de arranque** separador. O diretório de cópia de segurança é especificado na **avançadas** separador. Captura de ecrã seguinte mostra onde procurar para o parâmetro de arranque do registo de erro.

    ![Captura de ecrã de registo de erros SQL](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* Configure localizações de ficheiros de cópia de segurança e a base de dados predefinidas. Utilize as recomendações neste artigo e fazer as alterações na janela de propriedades do servidor. Para obter instruções, consulte [ver ou alterar as localizações predefinidas para os dados e ficheiros de registo (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Captura de ecrã seguinte demonstra onde pretende efetuar estas alterações.

    ![Ficheiros de registo de dados do SQL e cópia de segurança](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Ative páginas bloqueadas reduzir a e/s e qualquer atividade de paginação. Para obter mais informações, consulte [ativar a bloquear páginas na opção de memória (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

* Se estiver a executar o SQL Server 2012, instale o Service Pack 1 a atualização cumulativa 10. Esta atualização contém a correção para um mau desempenho e/s ao executar select na instrução de tabela temporária no SQL Server 2012. Para obter informações, consulte esta [artigo da base de dados de conhecimento](http://support.microsoft.com/kb/2958012).

* Considere a compactação de quaisquer arquivos de dados durante a transferência de entrada/saída do Azure.

## <a name="feature-specific-guidance"></a>Orientações específicas de funcionalidade

Algumas implementações podem alcançar os benefícios de desempenho adicionais usando técnicas mais avançadas de configuração. A lista seguinte destaca algumas funcionalidades do SQL Server que podem ajudar a melhorar o desempenho:

* **Cópia de segurança para o armazenamento do Azure**: quando efetuar cópias de segurança para o SQL Server em execução em máquinas virtuais do Azure, pode utilizar [cópia de segurança do SQL Server para URL](https://msdn.microsoft.com/library/dn435916.aspx). Esta funcionalidade está disponível a partir do SQL Server 2012 SP1 CU2 e recomendada para o backup em discos de dados anexados. Quando cópia de segurança/restauro de/para o armazenamento do Azure, siga as recomendações fornecidas ao [SQL Server cópia de segurança para URL melhores práticas e resolução de problemas e restaurar a partir de cópias de segurança armazenadas no armazenamento do Azure](https://msdn.microsoft.com/library/jj919149.aspx). Também é possível automatizar estas cópias de segurança usando [cópia de segurança automatizada para SQL Server em máquinas de virtuais do Azure](virtual-machines-windows-sql-automated-backup.md).

    Antes do SQL Server 2012, pode usar [cópia de segurança do SQL Server para a ferramenta do Azure](https://www.microsoft.com/download/details.aspx?id=40740). Essa ferramenta pode ajudar a aumentar o débito de cópia de segurança com vários destinos de cópia de segurança do stripe.

* **Ficheiros de dados do SQL Server no Azure**: esta nova funcionalidade [ficheiros de dados do SQL Server no Azure](https://msdn.microsoft.com/library/dn385720.aspx), está disponível a partir do SQL Server 2014. Executar o SQL Server com ficheiros de dados no Azure demonstra as características de desempenho comparável como utilizar discos de dados do Azure.

## <a name="next-steps"></a>Próximos Passos

Para melhores práticas de segurança, consulte [considerações de segurança para o SQL Server em máquinas de virtuais do Azure](virtual-machines-windows-sql-security.md).

Reveja os outros artigos de Máquina Virtual do SQL Server em [SQL Server em Azure Virtual Machines Overview](virtual-machines-windows-sql-server-iaas-overview.md). Se tiver dúvidas sobre máquinas virtuais do SQL Server, veja as [Perguntas Mais Frequentes](virtual-machines-windows-sql-server-iaas-faq.md).
