---
title: Melhores práticas de desempenho para o SQL Server em máquinas de virtuais do Azure Stack
description: Disponibiliza as melhores práticas para otimizar o desempenho do SQL Server no Microsoft Azure Stack máquinas virtuais.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 7981df6aa1e08688bdbe3b18629450b996f7609e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58123407"
---
# <a name="optimize-sql-server-performance"></a>Otimizar o desempenho do SQL Server

Este artigo fornece orientações para otimizar o desempenho do SQL Server em máquinas de virtuais do Microsoft Azure Stack. Quando executar o SQL Server em máquinas de virtuais do Azure Stack, utilize as mesmas da base de dados ajuste de desempenho opções aplicáveis ao SQL Server num ambiente de servidor no local. O desempenho da base de dados relacional numa cloud do Azure Stack depende de vários fatores. Fatores incluem o tamanho da família de uma máquina virtual e a configuração dos discos de dados.

Durante a criação de imagens do SQL Server, [considere o aprovisionamento de máquinas virtuais no portal do Azure Stack](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). Transfira a extensão de IaaS do SQL da gestão de Marketplace no Portal de administração do Azure Stack e transferir a sua escolha de discos rígidos virtuais de SQL máquinas virtuais (VHDs). Estes incluem-se ao SQL2014SP2, SQL2016SP1 e SQL2017.

> [!NOTE]  
> Embora o artigo descreve como aprovisionar uma máquina de virtual do SQL Server com o portal do Azure global, a documentação de orientação também se aplica ao Azure Stack com as seguintes diferenças: SSD não está disponível para o disco do sistema operativo, os discos geridos não estão disponíveis e existem pequenas diferenças na configuração de armazenamento.

Introdução a *melhor* desempenho para o SQL Server em máquinas de virtuais do Azure Stack é o foco deste artigo. Se a carga de trabalho menos exigentes, não poderá necessitar de cada otimização recomendada. Considere seus padrões de carga de trabalho e necessidades de desempenho durante a avaliação estas recomendações.

> [!NOTE]  
> Para orientação de desempenho do SQL Server em máquinas virtuais do Azure, consulte [este artigo](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).

## <a name="before-you-begin"></a>Antes de começar
A lista de verificação seguinte é para um desempenho ideal do SQL Server em máquinas de virtuais do Azure Stack:


|Área|Otimizações|
|-----|-----|
|Tamanho da máquina virtual |[DS3](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) ou superior para o SQL Server Enterprise edition.<br><br>[DS2](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) ou superior para o SQL Server Standard edition e o Web edition.|
|Armazenamento |Utilize uma família de máquina virtual que suporta [o armazenamento Premium](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-acs-differences).|
|Discos |Utilize um mínimo de dois discos de dados (uma para ficheiros de registo) e outra para o ficheiro de dados e TempDB e escolha o tamanho do disco com base nas suas necessidades de capacidade. Predefinir as localizações de ficheiros de dados para estes discos durante a instalação do SQL Server.<br><br>Evite utilizar o sistema operativo ou discos temporários para o armazenamento de base de dados ou de registo.<br>Vários discos de dados do Azure para obter uma maior débito de e/s através dos espaços de armazenamento do stripe.<br><br>Formatar com tamanhos de alocação documentado.|
|I/O|Ative a inicialização instantânea de arquivo para ficheiros de dados.<br><br>Limitar o crescimento automático nas bases de dados com incrementos fixos razoavelmente pequenos (64MB - 256 MB).<br><br>Desative início do encolhimento automático no banco de dados.<br><br>Configure localizações de ficheiros de cópia de segurança e a base de dados padrão em discos de dados, não o disco do sistema operativo.<br><br>Ative páginas bloqueadas.<br><br>Aplicam-se de que as do SQL Server service packs e atualizações cumulativas.|
|Específicos de funcionalidades|Criar cópias de segurança diretamente ao armazenamento de BLOBs (se suportado pela versão do SQL Server em utilização).|
|||

Para obter mais informações sobre *como* e *motivo pelo qual* para tornar essas otimizações, reveja os detalhes e as diretrizes fornecidas nas secções seguintes.

## <a name="virtual-machine-size-guidance"></a>Documentação de orientação de tamanho de máquina virtual

Para aplicações sensíveis a desempenho, o seguinte procedimento [tamanhos de máquinas virtuais](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) são recomendadas:

- **Edição do SQL Server Enterprise:** DS3 ou superior

- **SQL Server Standard edition e o Web edition:** DS2 ou superior

Com o Azure Stack não existe nenhuma diferença de desempenho entre as séries DS e DS_v2 de famílias de máquina virtual.

## <a name="storage-guidance"></a>Orientações do armazenamento

Máquinas de virtuais de série DS (juntamente com a série DSv2) no Azure Stack fornecer a máxima do sistema operativo disco e os dados de débito de disco (IOPS). Uma máquina virtual da série DS ou DSv2 fornece até 1000 IOPS para o disco do sistema operativo e até 2,300 IOPS por disco de dados, não importa o tipo ou tamanho do disco escolhido.

Débito de disco de dados é determinado exclusivamente com base na série de família de máquina virtual. Pode [veja este artigo](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) para identificar o débito de disco de dados por série de família de máquina virtual.

> [!NOTE]  
> Para cargas de trabalho de produção, selecione uma máquina de virtual de série DS ou série DSv2 para fornecer o IOPS máximo possível no sistema operacional de discos de dados e de disco.

Ao criar uma conta de armazenamento no Azure Stack, a opção de replicação geográfica não tem efeito uma vez que esta capacidade não está disponível no Azure Stack.

## <a name="disks-guidance"></a>Orientações de discos

Existem três tipos de disco principal numa máquina virtual do Azure Stack:

- **Disco do sistema operativo:** Quando cria uma máquina virtual do Azure Stack, a plataforma anexa pelo menos um disco (identificado como a **C** unidade) para a máquina virtual para o seu disco de sistema operativo. Este disco é um VHD armazenado como um blob de página no armazenamento.

- **Disco temporário:** Máquinas de virtuais do Azure Stack conter outro disco chamado o disco temporário (identificado como a **1!d** unidade). Este é um disco no nó que pode ser utilizado para espaço transitório.

- **Discos de dados:** Pode anexar discos adicionais à sua máquina virtual como discos de dados e estes discos são armazenados no armazenamento, como blobs de páginas.

As secções seguintes descrevem as recomendações para utilizar estes discos diferentes.

### <a name="operating-system-disk"></a>Disco do sistema operativo

Disco de sistema operativo é um VHD que pode efetuar o arranque e montar como uma versão em execução de um sistema operacional e tem o nome como **C** unidade.

### <a name="temporary-disk"></a>Disco temporário

A unidade de armazenamento temporário, identificada como a **1!d** unidade, não é persistente. Não armazene todos os dados que não está dispostos a perder, como seus arquivos de banco de dados de utilizador ou arquivos de log de transação de utilizador, diante da **1!d** unidade.

Recomendamos que o serviço de armazenamento de TempDB num disco de dados, pois cada disco de dados fornece um máximo de até 2,300 IOPS por disco de dados.

### <a name="data-disks"></a>Discos de dados

- **Utilize discos de dados para ficheiros de registo e dados.** Se não estiver a utilizar a repartição de disco, utilize dois discos de dados de uma máquina virtual que suporte o armazenamento Premium, em que um disco contém os ficheiros de registo e a outra contém os dados e ficheiros de TempDB. Cada disco de dados fornece um número de IOPS e largura de banda (MB/s) dependendo da família de máquina virtual, conforme descrito em [tamanhos de máquinas virtuais suportados no Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). Se estiver a utilizar uma técnica de repartição de disco, tais como espaços de armazenamento, coloque todos os ficheiros de dados e de registo na mesma unidade (incluindo TempDB). Esta configuração dá-lhe o número máximo de IOPS disponíveis para o SQL Server consumir, não importa o que necessita de ficheiros-los a qualquer momento.

> [!NOTE]  
> Quando aprovisiona uma máquina virtual do SQL Server no portal, tem a opção de editar a configuração de armazenamento. Consoante a configuração, o Azure Stack configura um ou mais discos. Vários discos são combinados num agrupamento de armazenamento única. Ambos os ficheiros de dados e de registo de residir em conjunto nesta configuração.

- **Repartição de disco:** Para obter mais débito, pode adicionar discos de dados adicionais e utilize a repartição de disco. Para determinar o número de discos de dados, que precisa, analise o número de IOPS e largura de banda necessária para os ficheiros de registo e para os seus dados e ficheiros de TempDB. Tenha em atenção que são de limites de IOPS por disco de dados com base na família da série de máquina virtual e não com base no tamanho de máquina virtual. Limites de largura de banda de rede, no entanto, se baseiam o tamanho de máquina virtual. Consulte as tabelas no [tamanhos de máquinas virtuais no Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) para obter mais detalhes. Utilize as seguintes diretrizes:

  - Para Windows Server 2012 ou posterior, utilize [espaços de armazenamento](https://technet.microsoft.com/library/hh831739.aspx) com as seguintes diretrizes:

    1. Defina o interleave (tamanho da faixa) a 64 KB (65,536 bytes) para cargas de trabalho (OLTP) e 256 KB (262.144 bytes) para cargas de trabalho de armazenamento de dados para evitar o impacto de desempenho devido ao alinhamento incorreto da partição de processamento de transações online. Tem de ser definida com o PowerShell.

    2. Definir o número de colunas = número de discos físicos. Utilize o PowerShell, ao configurar mais de oito discos (não a IU de Gestor de servidor).

       Por exemplo, o PowerShell seguinte cria um novo agrupamento de armazenamento com o tamanho de intercalação definido para 64 KB e o número de colunas para 2:

       ```PowerShell  
       $PoolCount = Get-PhysicalDisk -CanPool $True
       $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

       New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false
       ```

- Determine o número de discos associados com o agrupamento de armazenamento com base em suas expectativas de carga. Tenha em atenção que os tamanhos de máquinas virtuais permitem diferentes números de discos de dados anexados. Para obter mais informações, consulte [tamanhos de máquinas virtuais suportados no Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes).
- Para obter o máximo IOPS possíveis para discos de dados, a recomendação é adicionar o número máximo de discos de dados suportados pela sua [tamanho da máquina virtual](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) e utilize a repartição de disco.
- **Tamanho da unidade de alocação NTFS:** Ao formatar o disco de dados, recomenda-se que utilize um tamanho de unidade de alocação de 64 KB de dados e arquivos de log, bem como TempDB.
- **Práticas de gerenciamento de disco:** Quando remover um disco de dados, pare o serviço do SQL Server durante a alteração. Além disso, não altere as definições de cache nos discos, ele não fornece quaisquer melhorias de desempenho.

> [!WARNING]  
> Falha ao parar o serviço de SQL durante estas operações pode causar Corrupção de base de dados.

## <a name="io-guidance"></a>Orientações de e/s

- Considere ativar a inicialização instantânea de arquivo reduzir o tempo necessário para alocação de ficheiro inicial. Para aproveitar a inicialização instantânea de arquivo, pode conceder a conta de serviço do SQL Server (MSSQLSERVER) com **SE_MANAGE_VOLUME_NAME** e adicione-o para o **efetuar tarefas de manutenção de Volume** segurança política. Se estiver a utilizar uma imagem de plataforma do SQL Server para o Azure, a predefinição conta de serviço (**NT Service\MSSQLSERVER**) não for adicionado para o **efetuar tarefas de manutenção de Volume** política de segurança. Em outras palavras, a inicialização instantânea de arquivo não está ativada numa imagem de plataforma do Azure do SQL Server. Depois de adicionar a conta de serviço do SQL Server para o **efetuar tarefas de manutenção de Volume** política de segurança, reinicie o serviço do SQL Server. Pode haver considerações de segurança para utilizar esta funcionalidade. Para obter mais informações, consulte [inicialização de arquivo de banco de dados](https://msdn.microsoft.com/library/ms175935.aspx).
- **O aumento automático** é o plano de contingência para crescimento inesperado. Não gerir o crescimento dos seus dados e de registo diariamente com aumento automático. Se o aumento automático for utilizado, aumentar previamente o ficheiro com o **tamanho** mudar.
- Certifique-se **encolhimento automático** está desativada para evitar a sobrecarga desnecessária, que pode afetar negativamente o desempenho.
- Configure localizações de ficheiros de cópia de segurança e a base de dados predefinidas. Utilize as recomendações neste artigo e efetue as alterações na janela de propriedades do servidor. Para obter instruções, consulte [ver ou alterar as localizações predefinidas para os dados e ficheiros de registo (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Captura de ecrã seguinte demonstra onde pretende efetuar estas alterações:

    > ![Ver ou alterar as localizações predefinidas](./media/sql-server-vm-considerations/image1.png)

- Ative páginas bloqueadas reduzir a e/s e qualquer atividade de paginação. Para obter mais informações, consulte [ativar a bloquear páginas na opção de memória (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

- Considere a compactação de quaisquer arquivos de dados durante a transferência de entrada/saída do Azure Stack, incluindo cópias de segurança.

## <a name="feature-specific-guidance"></a>Orientações específicas de funcionalidade

Algumas implementações podem alcançar os benefícios de desempenho adicionais usando técnicas mais avançadas de configuração. A lista seguinte destaca algumas funcionalidades do SQL Server que podem ajudá-lo a melhorar o desempenho:

- **Criar uma cópia de segurança do Azure** **armazenamento.** Quando efetuar cópias de segurança para o SQL Server em execução em máquinas de virtuais do Azure Stack, pode utilizar a cópia de segurança do SQL Server para URL. Esta funcionalidade está disponível a partir do SQL Server 2012 SP1 CU2 e recomendada para o backup em discos de dados anexados.

    Quando a cópia de segurança ou restauro com o armazenamento do Azure, siga as recomendações fornecidas [cópia de segurança do SQL Server para o URL de melhores práticas e resolução de problemas](https://msdn.microsoft.com/library/jj919149.aspx) e [restauro de cópias de segurança armazenadas no Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure?view=sql-server-2017). Também é possível automatizar estas cópias de segurança usando [cópia de segurança automatizada para SQL Server em máquinas de virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup).

-   **Criar cópias de segurança para o armazenamento do Azure Stack.** Pode criar cópias de segurança para o armazenamento do Azure Stack, de modo semelhante tal como acontece com o backup do armazenamento do Azure. Quando cria uma cópia de segurança dentro do SQL Server Management Studio (SSMS), terá de introduzir manualmente as informações de configuração. Não é possível utilizar o SSMS para criar o contentor de armazenamento ou a assinatura de acesso partilhado. SSMS liga-se apenas a subscrições do Azure, não as subscrições do Azure Stack. Em vez disso, tem de criar a conta de armazenamento, o contentor e a assinatura de acesso partilhado no portal do Azure Stack ou com o PowerShell.

    Quando colocar as informações para a caixa de diálogo de cópia de segurança do SQL Server:

    ![Cópia de segurança do SQL Server](./media/sql-server-vm-considerations/image3.png)

    > [!NOTE]  
    > A assinatura de acesso partilhado é o token SAS do portal do Azure Stack, sem o líder "?" na cadeia de caracteres. Se utilizar a função de cópia a partir do portal, tem de eliminar o líder "?" para o token funcionar dentro do SQL Server.

    Assim que tiver o destino de cópia de segurança, configurar e configurada no SQL Server, pode, em seguida, criar cópias de segurança para o armazenamento de Blobs do Azure Stack.

## <a name="next-steps"></a>Passos Seguintes

[Utilizar os serviços ou criar aplicativos para o Azure Stack](azure-stack-considerations.md)