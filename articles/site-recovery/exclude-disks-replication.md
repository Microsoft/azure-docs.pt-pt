---
title: Excluir discos da replicação com o Azure Site Recovery
description: Como excluir discos da replicação para o Azure com o Azure Site Recovery.
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 57bf06f0fde85714530c06cbd008db08de7460d2
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/26/2019
ms.locfileid: "75498270"
---
# <a name="exclude-disks-from-disaster-recovery"></a>Excluir discos da recuperação de desastre

Este artigo descreve como excluir discos da replicação durante a recuperação de desastres do local para o Azure com o [Azure site Recovery](site-recovery-overview.md). Você pode excluir discos da replicação por vários motivos:

- Para que os dados não importantes que estão sendo copiados no disco excluído não sejam replicados.
- Para otimizar a largura de banda de replicação consumida ou recursos do lado de destino.
- Para salvar os recursos de armazenamento e rede, não replicando os dados que você não precisa.
- As VMs do Azure atingiram Site Recovery limites de replicação.


## <a name="supported-scenarios"></a>Cenários suportados

Você pode excluir discos da replicação conforme resumido na tabela.

**Azure para o Azure** | **VMware para o Azure** | **Hyper-V para o Azure** 
--- | --- | ---
Sim (usando o PowerShell) | Sim | Sim 

## <a name="exclude-limitations"></a>Limitações de exclusão

**Limitação** | **VMs do Azure** | **VMs VMware** | **VMs de Hyper-V**
--- | --- | ---
**Tipos de disco** | Você pode excluir discos básicos da replicação.<br/><br/> Não é possível excluir discos do sistema operacional ou discos dinâmicos. Os discos temporários são excluídos por padrão. | Você pode excluir discos básicos da replicação.<br/><br/> Não é possível excluir discos do sistema operacional ou discos dinâmicos. | Você pode excluir discos básicos da replicação.<br/><br/> Não é possível excluir discos do sistema operativo. Recomendamos que não exclua discos dinâmicos. Site Recovery não pode identificar qual VHS é básico ou dinâmico na VM convidada. Se todos os discos de volume dinâmico dependentes não forem excluídos, o disco dinâmico protegido se tornará um disco com falha em uma VM de failover e os dados nesse disco não estarão acessíveis.
**Replicando disco** | Você não pode excluir um disco que está replicando.<br/><br/> Desabilite e reabilite a replicação para a VM. |  Você não pode excluir um disco que está replicando. |  Você não pode excluir um disco que está replicando.
**Serviço de mobilidade (VMware)** | Não relevante | Você pode excluir discos somente em VMs que têm o serviço de mobilidade instalado.<br/><br/> Isso significa que você precisa instalar manualmente o serviço de mobilidade nas VMs para as quais deseja excluir discos. Você não pode usar o mecanismo de instalação por push porque ele instala o serviço de mobilidade somente após a replicação ser habilitada. | Não relevante.
**Adicionar/remover** | Você pode adicionar e remover discos em VMs do Azure com discos gerenciados. | Você não pode adicionar ou remover discos depois que a replicação está habilitada. Desabilite e reabilite a replicação para adicionar um disco. | Você não pode adicionar ou remover discos depois que a replicação está habilitada. Desabilite e reabilite a replicação.
**Ativação pós-falha** | Se um aplicativo precisar de um disco que você excluiu, após o failover, você precisará criar o disco manualmente para que o aplicativo replicado possa ser executado.<br/><br/> Como alternativa, você pode criar o disco durante o failover de VM, integrando a automação do Azure em um plano de recuperação. | Se você excluir um disco de que um aplicativo precisa, crie-o manualmente no Azure após o failover. | Se você excluir um disco de que um aplicativo precisa, crie-o manualmente no Azure após o failover.
**Failback local-discos criados manualmente** | Não relevante | **VMs do Windows**: discos criados manualmente no Azure não fazem failback. Por exemplo, se você executar failover de três discos e criar dois discos diretamente em uma VM do Azure, somente os três discos que sofreram failover serão então submetidos a failback.<br/><br/> **VMs do Linux**: os discos criados manualmente no Azure sofrerão failback. Por exemplo, se você executar failover de três discos e criar dois discos em uma VM do Azure, todos os cinco serão submetidos a failback. Não pode excluir da reativação pós-falha os discos que foram criados manualmente. | Discos criados manualmente no Azure não fazem failback. Por exemplo, se você executar failover de três discos e criar dois discos diretamente em uma VM do Azure, somente três discos que sofreram failover serão submetidos a failback.
**Discos excluídos por failback local** | Não relevante | Se você fizer failback para o computador original, a configuração de disco da VM de failback não incluirá os discos excluídos. Discos que foram excluídos da replicação do VMware para o Azure não estão disponíveis na VM de failback. | Quando o failback é para o local do Hyper-V original, a configuração de disco da VM de failback permanece a mesma do disco da VM de origem original. Os discos que foram excluídos do site do Hyper-V para replicação do Azure estão disponíveis na VM de failback.



## <a name="typical-scenarios"></a>Cenários típicos

Exemplos de rotatividade de dados que são ótimos candidatos para exclusão incluem gravações em um arquivo de paginação (pagefile. sys) e grava no arquivo TempDB de Microsoft SQL Server. Dependendo da carga de trabalho e do subsistema de armazenamento, os arquivos de paginação e tempdb podem registrar uma quantidade significativa de rotatividade. A replicação desse tipo de dados para o Azure consome muitos recursos.

- Para otimizar a replicação de uma VM com um único disco virtual que inclui o sistema operacional e o arquivo de paginação, você pode:
    1. Divida o disco virtual único em dois discos virtuais. Um disco virtual tem o sistema operativo e o outro tem o ficheiro de paginação.
    2. Exclua o disco do ficheiro de paginação da replicação.

- Para otimizar a replicação de um disco que inclui o Microsoft SQL Server arquivo tempdb e o arquivo de banco de dados do sistema, você pode:
    1. Mantenha a base de dados do sistema e o tempdb em dois discos diferentes.
    2. Exclua o disco tempdb da replicação.

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>Exemplo 1: excluir o disco tempdb do SQL Server

Vejamos como lidar com a exclusão de disco, failover e failover para uma fonte SQL Server VM Windows-* * SalesDB * * *, para a qual desejamos excluir tempdb. 

### <a name="exclude-disks-from-replication"></a>Excluir discos da replicação

Temos esses discos na VM do Windows de origem SalesDB.

**Nome do disco** | **Disco do SO convidado** | **Letra da unidade** | **Tipo de dados do disco**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Disco do sistema operacional.
DB-Disk1| Disk1 | D:\ | Banco de dados do sistema SQL e Database1 de usuário.
DB-Disk2 (disco excluído da proteção) | Disk2 | E:\ | Arquivos temporários.
DB-Disk3 (disco excluído da proteção) | Disk3 | F:\ | Banco de dados tempdb do SQL.<br/><br/> Caminho da pasta-F:\MSSQL\Data\. Anote o caminho da pasta antes do failover.
DB-Disk4 | Disk4 |G:\ | User Database2

1. Habilitamos a replicação para a VM SalesDB.
2. Excluímos disco 2 e Disk3 da replicação porque a rotatividade de dados nesses discos é temporária. 


### <a name="handle-disks-during-failover"></a>Tratar discos durante o failover

Como os discos não são replicados, quando você faz failover para o Azure, esses discos não estão presentes na VM do Azure criada após o failover. A VM do Azure tem os discos resumidos nesta tabela.

**Disco do SO convidado** | **Letra da unidade** | **Tipo de dados do disco**
--- | --- | ---
Disk0 | C:\ | Disco do sistema operacional.
Disk1 | E:\ | Armazenamento temporário<br/><br/>O Azure adiciona este disco. Como disco 2 e Disk3 foram excluídos da replicação, E: é a primeira letra da unidade da lista disponível. O Azure atribui E: ao volume de armazenamento temporário. Outras letras de unidade para discos replicados permanecem as mesmas.
Disk2 | D:\ | Base de dados do sistema SQL e User Database1
Disk3 | G:\ | User Database2

Em nosso exemplo, como Disk3, o disco tempdb do SQL, foi excluído da replicação e não está disponível na VM do Azure, o serviço SQL está em um estado parado e precisa do caminho F:\MSSQL\Data. Você pode criar esse caminho de duas maneiras: 

- Adicione um novo disco após o failover e atribua o caminho da pasta tempdb.
- Utilizar um disco de armazenamento temporário existente para o caminho da pasta tempdb.

#### <a name="add-a-new-disk-after-failover"></a>Adicionar um novo disco após o failover

1. Aponte os caminhos de tempdb.mdf e tempdb.ldf do SQL antes da ativação pós-falha.
2. No portal do Azure, adicione um novo disco à VM do Azure de failover. O disco deve ter o mesmo tamanho (ou maior) que o disco tempdb do SQL de origem (Disk3).
3. Entre na VM do Azure.
4. Na consola da gestão de discos (diskmgmt.msc), inicialize e formate o disco adicionado recentemente.
5. Atribua a mesma letra de unidade que foi usada pelo disco tempdb do SQL (F:)
6. Crie uma pasta tempdb no volume F: (F:\MSSQL\Data).
7. Inicie o serviço SQL a partir da consola do serviço.

#### <a name="use-an-existing-temporary-storage-disk"></a>Usar um disco de armazenamento temporário existente 

1. Abra uma linha de comandos.
2. Execute o SQL Server no modo de recuperação a partir da linha de comandos.

        Net start MSSQLSERVER /f / T3608

3. Execute o sqlcmd seguinte para alterar o caminho de tempdb para o caminho novo.

        sqlcmd -A -S SalesDB        **Use your SQL DBname**
        USE master;     
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');       
        GO


4. Pare o serviço Microsoft SQL Server.

        Net stop MSSQLSERVER
5. Inicie o serviço Microsoft SQL Server.

        Net start MSSQLSERVER



### <a name="vmware-vms-disks-during-failback-to-original-location"></a>VMs VMware: discos durante o failback para o local original

Agora, vejamos como lidar com discos em VMs VMware ao fazer failback para o local original.

- **Discos criados no Azure**: como nosso exemplo usa uma VM do Windows, os discos que você criar manualmente no Azure não serão replicados de volta para o site quando você fizer failback ou proteger novamente uma VM.
- **Disco de armazenamento temporário no Azure**: o disco de armazenamento temporário não é replicado de volta para hosts locais.
- **Discos excluídos**: discos que foram excluídos da replicação do VMware para o Azure não estão disponíveis na VM local após o failback.

Antes de realizar o failback das VMs do VMware para o local original, as configurações de disco da VM do Azure são as seguintes.

**Disco do SO convidado** | **Letra da unidade** | **Tipo de dados do disco**
--- | --- | ---
Disk0 | C:\ | Disco do sistema operacional.
Disk1 | E:\ | Armazenamento temporário.
Disk2 | D:\ | Banco de dados do sistema SQL e Database1 de usuário.
Disk3 | G:\ | Database2 do usuário.

Após o failback, a VM do VMware no local original tem os discos resumidos na tabela.

**Disco do SO convidado** | **Letra da unidade** | **Tipo de dados do disco**
--- | --- | ---
Disk0 | C:\ | Disco do sistema operacional.
Disk1 | D:\ | Banco de dados do sistema SQL e Database1 de usuário.
Disk2 | G:\ | Database2 do usuário.


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>VMs do Hyper-V: discos durante o failback para o local original

Agora, vejamos como tratar discos em VMs do Hyper-V quando você realiza o failback para o local original.

- **Discos criados no Azure**: os discos que você cria manualmente no Azure não são replicados de volta para seu site quando você realiza o failback ou protege novamente uma VM.
- **Disco de armazenamento temporário no Azure**: o disco de armazenamento temporário não é replicado de volta para hosts locais.
- **Discos excluídos**: após o failback, a configuração de disco da VM é igual à configuração original do disco da VM. Os discos que foram excluídos da replicação do Hyper-V para o Azure estão disponíveis na VM de failback.

Antes de executar o failback das VMs do Hyper-V para o local original, as configurações de disco da VM do Azure são as seguintes.

**Disco do SO convidado** | **Letra da unidade** | **Tipo de dados do disco**
--- | --- | ---
Disk0 | C:\ | Disco do sistema operacional.
Disk1 | E:\ | Armazenamento temporário.
Disk2 | D:\ | Banco de dados do sistema SQL e Database1 de usuário.
Disk3 | G:\ | Database2 do usuário.

Após o failover planejado (failback) do Azure para o Hyper-V local, a VM do Hyper-V no local original tem os discos resumidos na tabela.

**Nome do Disco** | **N.º do disco do SO convidado** | **Letra da unidade** | **Tipo de dados do disco**
 --- | --- | --- | ---
DB-Disk0-OS | Disk0 |   C:\ | Disco do sistema operacional.
DB-Disk1 | Disk1 | D:\ | Banco de dados do sistema SQL e Database1 de usuário.
BD-Disk2 (disco excluído) | Disk2 | E:\ | Arquivos temporários.
DB-Disk3 (disco excluído) | Disk3 | F:\ | Banco de dados tempdb do SQL<br/><br/> Caminho da pasta (F:\MSSQL\Data\).
DB-Disk4 | Disk4 | G:\ | User Database2


## <a name="example-2-exclude-the-paging-file-disk"></a>Exemplo 2: excluir o disco do arquivo de paginação

Vejamos como lidar com a exclusão de disco, failover e failover para uma VM Windows de origem, para a qual desejamos excluir o disco de arquivo pagefile. sys na unidade D e uma unidade alternativa.


### <a name="paging-file-on-the-d-drive"></a>Arquivo de paginação na unidade D

Temos esses discos na VM de origem.

**Nome do disco** | **Disco do SO convidado** | **Letra da unidade** | **Tipo de dados do disco**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Disco do sistema operativo
DB-DISK1 (excluir da replicação) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

As configurações do arquivo de paginação na VM de origem são as seguintes:

![Definições de ficheiro de paginação na máquina virtual de origem](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. Habilitamos a replicação para a VM.
2. Excluímos DB-DISK1 da replicação.

#### <a name="disks-after-failover"></a>Discos após o failover

Após o failover, a VM do Azure tem os discos resumidos na tabela.

**Nome do disco** | **Sistema operativo convidado disco#** | **Letra da unidade** | **Tipo de dados no disco**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Disco do sistema operativo
DB-Disk1 | Disk1 | D:\ | Armazenamento temporário/pagefile. sys <br/><br/> Porque DB-DISK1 (D:) foi excluído, D: é a primeira letra da unidade da lista disponível.<br/><br/> O Azure atribui D: ao volume de armazenamento temporário.<br/><br/> Como D: está disponível, a configuração do arquivo de paginação da VM permanece a mesma).
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

As configurações do arquivo de paginação na VM do Azure são as seguintes:

![Definições de ficheiro de paginação na máquina virtual do Azure](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>Arquivo de paginação em outra unidade (não D:)

Vejamos um exemplo no qual o arquivo de paginação não está na unidade D.  

Temos esses discos na VM de origem.

**Nome do disco** | **Disco do SO convidado** | **Letra da unidade** | **Tipo de dados do disco**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Disco do sistema operativo
DB-DISK1 (excluir da replicação) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

As configurações do arquivo de paginação na VM local são as seguintes:

![Definições de ficheiro de paginação na máquina virtual no local](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. Habilitamos a replicação para a VM.
2. Excluímos DB-DISK1 da replicação.

#### <a name="disks-after-failover"></a>Discos após o failover

Após o failover, a VM do Azure tem os discos resumidos na tabela.

**Nome do disco** | **N.º do disco do SO convidado** | **Letra da unidade** | **Tipo de dados do disco**
--- | --- | --- | ---
DB-Disk0-OS | Disk0  |C:\ | Disco do sistema operativo
DB-Disk1 | Disk1 | D:\ | Armazenamento temporário<br/><br/> Uma vez que D: é a primeira letra de unidade disponível na lista, o Azure atribui D: ao volume de armazenamento temporário.<br/><br/> Em todos os outros discos replicados, a unidade de letra permanece a mesma.<br/><br/> Como o disco G: não está disponível, o sistema usará a unidade C: para o arquivo de paginação.
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

As configurações do arquivo de paginação na VM do Azure são as seguintes:

![Definições de ficheiro de paginação na máquina virtual do Azure](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre as diretrizes para o disco de armazenamento temporário:
    - [Saiba mais sobre como](https://blogs.technet.microsoft.com/dataplatforminsider/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) usar o SSDS em VMs do Azure para armazenar SQL Server tempdb e extensões do pool de buffers
    - [Examine](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance) as práticas recomendadas de desempenho para SQL Server em VMs do Azure.
- Depois da implementação estar instalada e em execução, [saiba mais](failover-failback-overview.md) sobre os diferentes tipos de ativação pós-falha.

