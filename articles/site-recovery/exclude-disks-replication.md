---
title: Excluir discos de replicação com recuperação do site Azure
description: Como excluir discos da replicação para Azure com recuperação do site Azure.
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 57bf06f0fde85714530c06cbd008db08de7460d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79281850"
---
# <a name="exclude-disks-from-disaster-recovery"></a>Excluir discos da recuperação de desastres

Este artigo descreve como excluir discos de replicação durante a recuperação de desastres das instalações para Azure com a Recuperação do [Local Azure.](site-recovery-overview.md) Pode excluir discos de replicação por uma série de razões:

- Para que os dados não importantes no disco excluído não sejam replicados.
- Para otimizar a largura de banda de replicação consumida, ou recursos do lado do alvo.
- Para poupar recursos de armazenamento e rede, não replicando dados de que não precisa.
- Os VMs azure atingiram os limites de replicação da recuperação do local.


## <a name="supported-scenarios"></a>Cenários suportados

Pode excluir discos de replicação como resumido na tabela.

**Azure para o Azure** | **VMware para o Azure** | **Hyper-V para o Azure** 
--- | --- | ---
Sim (usando powerShell) | Sim | Sim 

## <a name="exclude-limitations"></a>Excluir limitações

**Limitação** | **VMs do Azure** | **VMs VMware** | **VMs Hyper-V**
--- | --- | ---
**Tipos de disco** | Pode excluir discos básicos da replicação.<br/><br/> Não é possível excluir discos do sistema operativo ou discos dinâmicos. Os discos temporários são excluídos por defeito. | Pode excluir discos básicos da replicação.<br/><br/> Não é possível excluir discos do sistema operativo ou discos dinâmicos. | Pode excluir discos básicos da replicação.<br/><br/> Não é possível excluir discos do sistema operativo. Recomendamos que não exclua discos dinâmicos. A Recuperação do Site não consegue identificar qual vHS é básico ou dinâmico no VM convidado. Se todos os discos de volume dinâmico dependente não forem excluídos, o disco dinâmico protegido torna-se um disco falhado num VM failover, e os dados nesse disco não estão acessíveis.
**Risque de risadis** | Não podeexcluir um disco que está a replicar-se.<br/><br/> Desative e reative a replicação para o VM. |  Não podeexcluir um disco que está a replicar-se. |  Não podeexcluir um disco que está a replicar-se.
**Serviço de mobilidade (VMware)** | Não relevante | Só é possível excluir discos em VMs que tenham o serviço mobility instalado.<br/><br/> Isto significa que tem de instalar manualmente o serviço mobility nos VMs para os quais pretende excluir discos. Não é possível utilizar o mecanismo de instalação do impulso porque instala o serviço mobility apenas após a replicação estar ativada. | Não é relevante.
**Adicionar/Remover** | Pode adicionar e remover discos em VMs Azure com discos geridos. | Não é possível adicionar ou remover discos depois de ativada a replicação. Desative e, em seguida, reative a replicação para adicionar um disco. | Não é possível adicionar ou remover discos depois de ativada a replicação. Desativar e, em seguida, reativar a replicação.
**Ativação pós-falha** | Se uma aplicação precisar de um disco que tenha excluído, após a falha, terá de criar o disco manualmente para que a aplicação replicada possa ser executada.<br/><br/> Em alternativa, pode criar o disco durante a falha do VM, integrando a automação do Azure num plano de recuperação. | Se excluir um disco de que uma aplicação necessita, crie-o manualmente em Azure após a falha. | Se excluir um disco de que uma aplicação necessita, crie-o manualmente em Azure após a falha.
**No local, os discos de backback criados manualmente** | Não relevante | **Windows VMs**: Os discos criados manualmente em Azure não são reprovados. Por exemplo, se falhar em três discos e criar dois discos diretamente num VM Azure, apenas os três discos que foram falhados são então falhados.<br/><br/> **VMs Linux**: Os discos criados manualmente em Azure são falhados. Por exemplo, se falhar em três discos e criar dois discos num VM Azure, todos os cinco serão refalhados. Não pode excluir da reativação pós-falha os discos que foram criados manualmente. | Os discos criados manualmente em Azure não são falhados. Por exemplo, se falhar em três discos e criar dois discos diretamente num VM Azure, apenas três discos que foram falhados serão refalhados.
**Discos excluídos do reinamento no local** | Não relevante | Se não voltar à máquina original, a configuração do disco VM de reprovação não inclui os discos excluídos. Os discos que foram excluídos da VMware para a replicação do Azure não estão disponíveis no VM de backback. | Quando o failback é para a localização original do Hiper-V, a configuração do disco VM de recuo permanece a mesma do disco VM de origem original. Os discos que foram excluídos do site Hyper-V para a replicação do Azure estão disponíveis no VM de backback.



## <a name="typical-scenarios"></a>Cenários típicos

Exemplos de dados que são grandes candidatos à exclusão incluem escritos para um ficheiro de paging (pagefile.sys), e escreve para o ficheiro tempdb do Microsoft SQL Server. Dependendo da carga de trabalho e do subsistema de armazenamento, os ficheiros de paging e tempdb podem registar uma quantidade significativa de churn. Replicar este tipo de dados ao Azure é intensivo em recursos.

- Para otimizar a replicação de um VM com um único disco virtual que inclui tanto o sistema operativo como o ficheiro de paging, poderia:
    1. Divida o disco virtual único em dois discos virtuais. Um disco virtual tem o sistema operativo e o outro tem o ficheiro de paginação.
    2. Exclua o disco do ficheiro de paginação da replicação.

- Para otimizar a replicação de um disco que inclui tanto o ficheiro tempdb do Microsoft SQL Server como o ficheiro de base de dados do sistema, poderia:
    1. Mantenha a base de dados do sistema e o tempdb em dois discos diferentes.
    2. Exclua o disco tempdb da replicação.

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>Exemplo 1: excluir o disco tempdb do SQL Server

Vamos ver como lidar com a exclusão do disco, falha e falha para uma fonte SQL Server Windows VM - **SalesDB***, para o qual queremos excluir tempdb. 

### <a name="exclude-disks-from-replication"></a>Excluir discos da replicação

Temos estes discos na fonte Windows VM SalesDB.

**Nome do disco** | **Disco de osso convidado** | **Letra da unidade** | **Tipo de dados do disco**
--- | --- | --- | ---
DB-Disk0-OS | Disco0 | C:\ | Disco do sistema operativo.
DB-Disk1| Disk1 | D:\ | Base de dados do sistema SQL e Base de Dados de Utilizadores1.
DB-Disk2 (disco excluído da proteção) | Disk2 | E:\ | Ficheiros temporários.
DB-Disk3 (disco excluído da proteção) | Disk3 | F:\ | Base de dados sql tempdb.<br/><br/> Caminho da pasta - F:\MSSQL\Data\. Tome nota do caminho da pasta antes de falhar.
DB-Disk4 | Disk4 |G:\ | User Database2

1. Permitimos a replicação para o SalesDB VM.
2. Excluímos o Disk2 e o Disk3 da replicação porque os dados sobre esses discos são temporários. 


### <a name="handle-disks-during-failover"></a>Manuseie discos durante a falha

Uma vez que os discos não são replicados, quando falhas no Azure estes discos não estão presentes no VM Azure criado após a falha. O Azure VM tem os discos resumidos nesta tabela.

**Disco de osso convidado** | **Letra da unidade** | **Tipo de dados do disco**
--- | --- | ---
Disco0 | C:\ | Disco do sistema operativo.
Disk1 | E:\ | Armazenamento temporário<br/><br/>Azure adiciona este disco. Como o Disk2 e o Disk3 foram excluídos da replicação, E: é a primeira letra da lista disponível. O Azure atribui E: ao volume de armazenamento temporário. Outras letras de unidade para discos replicados permanecem as mesmas.
Disk2 | D:\ | Base de dados do sistema SQL e User Database1
Disk3 | G:\ | User Database2

No nosso exemplo, uma vez que o Disk3, o disco tempdb SQL, foi excluído da replicação e não está disponível no Azure VM, o serviço SQL está em estado de paragem, e precisa do caminho F:\MSSQL\Data. Você pode criar este caminho de várias maneiras: 

- Adicione um novo disco após a falha e atribua o caminho da pasta tempdb.
- Utilizar um disco de armazenamento temporário existente para o caminho da pasta tempdb.

#### <a name="add-a-new-disk-after-failover"></a>Adicione um novo disco após a falha

1. Aponte os caminhos de tempdb.mdf e tempdb.ldf do SQL antes da ativação pós-falha.
2. A partir do portal Azure, adicione um novo disco ao Failover Azure VM. O disco deve ter o mesmo tamanho (ou maior) que o disco tempdb SQL de origem (Disk3).
3. Inscreva-se no Azure VM.
4. Na consola da gestão de discos (diskmgmt.msc), inicialize e formate o disco adicionado recentemente.
5. Atribuir a mesma letra de unidade que foi utilizada pelo disco tempdb SQL (F:)
6. Crie uma pasta tempdb no volume F: (F:\MSSQL\Data).
7. Inicie o serviço SQL a partir da consola do serviço.

#### <a name="use-an-existing-temporary-storage-disk"></a>Utilize um disco de armazenamento temporário existente 

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



### <a name="vmware-vms-disks-during-failback-to-original-location"></a>VMs VMs: Discos durante falha na localização original

Agora vamos ver como lidar com discos em VMware VMs quando você falhar de volta ao seu local original no local.

- **Discos criados em Azure**: Uma vez que o nosso exemplo utiliza um VM Windows, os discos que cria manualmente em Azure não são replicados de volta ao seu site quando falha ou reprotege um VM.
- **Disco de armazenamento temporário em Azure**: O disco de armazenamento temporário não é replicado de volta aos anfitriões no local.
- **Discos excluídos**: Os discos que foram excluídos da vMware para a replicação do Azure não estão disponíveis no VM no local após o recuo.

Antes de repor os VMware VMs para a localização original, as definições do disco Azure VM são as seguintes.

**Disco de osso convidado** | **Letra da unidade** | **Tipo de dados do disco**
--- | --- | ---
Disco0 | C:\ | Disco do sistema operativo.
Disk1 | E:\ | Armazenamento temporário.
Disk2 | D:\ | Base de dados do sistema SQL e Base de Dados de Utilizadores1.
Disk3 | G:\ | Base de Dados do Utilizador2.

Após o failback, o VMware VM na localização original tem os discos resumidos na tabela.

**Disco de osso convidado** | **Letra da unidade** | **Tipo de dados do disco**
--- | --- | ---
Disco0 | C:\ | Disco do sistema operativo.
Disk1 | D:\ | Base de dados do sistema SQL e Base de Dados de Utilizadores1.
Disk2 | G:\ | Base de Dados do Utilizador2.


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>Hiper-V VMs: Discos durante falha na localização original

Agora vamos ver como lidar com discos em VMs Hiper-V quando você falhar de volta ao seu local original no local.

- **Os discos criados em Azure**: Os discos que cria manualmente em Azure não são replicados de volta ao seu site quando falha ou reprotege um VM.
- **Disco de armazenamento temporário em Azure**: O disco de armazenamento temporário não é replicado de volta aos anfitriões no local.
- **Discos excluídos**: Após a falha, a configuração do disco VM é a mesma que a configuração original do disco VM. Os discos que foram excluídos da replicação do Hyper-V para o Azure estão disponíveis no VM de backback.

Antes de reaver os VMs Hiper-V para a localização original, as definições do disco VM Azure são as seguintes.

**Disco de osso convidado** | **Letra da unidade** | **Tipo de dados do disco**
--- | --- | ---
Disco0 | C:\ | Disco do sistema operativo.
Disk1 | E:\ | Armazenamento temporário.
Disk2 | D:\ | Base de dados do sistema SQL e Base de Dados de Utilizadores1.
Disk3 | G:\ | Base de Dados do Utilizador2.

Após o failover planeado (failback) de Azure para o hiper-V no local, o VM Hyper-V no local original tem os discos resumidos na tabela.

**Nome do Disco** | **N.º do disco do SO convidado** | **Letra da unidade** | **Tipo de dados do disco**
 --- | --- | --- | ---
DB-Disk0-OS | Disco0 |   C:\ | Disco do sistema operativo.
DB-Disk1 | Disk1 | D:\ | Base de dados do sistema SQL e Base de Dados de Utilizadores1.
BD-Disk2 (disco excluído) | Disk2 | E:\ | Ficheiros temporários.
DB-Disk3 (disco excluído) | Disk3 | F:\ | Base de dados tempdb SQL<br/><br/> Caminho da pasta (F:\MSSQL\Data\).
DB-Disk4 | Disk4 | G:\ | User Database2


## <a name="example-2-exclude-the-paging-file-disk"></a>Exemplo 2: Excluir o disco de ficheiros de paging

Vamos ver como lidar com a exclusão do disco, a falha e a falha para um VM do Windows de origem, para o qual queremos excluir o disco de ficheiros pagefile.sys tanto na unidade D como numa unidade alternativa.


### <a name="paging-file-on-the-d-drive"></a>Arquivo de paging na unidade D

Temos estes discos na fonte VM.

**Nome do disco** | **Disco de osso convidado** | **Letra da unidade** | **Tipo de dados do disco**
--- | --- | --- | ---
DB-Disk0-OS | Disco0 | C:\ | Disco do sistema operativo
DB-Disk1 (Excluir da replicação) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

As nossas definições de ficheirode paging na Fonte VM são as seguintes:

![Definições de ficheiro de paginação na máquina virtual de origem](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. Permitimos a replicação para o VM.
2. Excluímos db-Disk1 da replicação.

#### <a name="disks-after-failover"></a>Discos após falha

Depois de falhar o VM Azure tem os discos resumidos na tabela.

**Nome do disco** | **Sistema operativo convidado disco#** | **Letra da unidade** | **Tipo de dados no disco**
--- | --- | --- | ---
DB-Disk0-OS | Disco0 | C:\ | Disco do sistema operativo
DB-Disk1 | Disk1 | D:\ | Armazenamento temporário/pagefile.sys <br/><br/> Porque DB-Disk1 (D:) foi excluída, D: é a primeira carta de condução da lista disponível.<br/><br/> O Azure atribui D: ao volume de armazenamento temporário.<br/><br/> Porque D: está disponível, a definição de ficheiro de paging VM permanece a mesma).
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

As nossas definições de ficheiros de paging no Azure VM são as seguintes:

![Definições de ficheiro de paginação na máquina virtual do Azure](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>Ficheiro de paging em outra unidade (não D:)

Vejamos o exemplo em que o ficheiro de paging não está na unidade D.  

Temos estes discos na fonte VM.

**Nome do disco** | **Disco de osso convidado** | **Letra da unidade** | **Tipo de dados do disco**
--- | --- | --- | ---
DB-Disk0-OS | Disco0 | C:\ | Disco do sistema operativo
DB-Disk1 (Excluir da replicação) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

As nossas definições de ficheiros de paging no VM no local são as seguintes:

![Definições de ficheiro de paginação na máquina virtual no local](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. Permitimos a replicação para o VM.
2. Excluímos db-Disk1 da replicação.

#### <a name="disks-after-failover"></a>Discos após falha

Depois de falhar o VM Azure tem os discos resumidos na tabela.

**Nome do disco** | **N.º do disco do SO convidado** | **Letra da unidade** | **Tipo de dados do disco**
--- | --- | --- | ---
DB-Disk0-OS | Disco0  |C:\ | Disco do sistema operativo
DB-Disk1 | Disk1 | D:\ | Armazenamento temporário<br/><br/> Uma vez que D: é a primeira letra de unidade disponível na lista, o Azure atribui D: ao volume de armazenamento temporário.<br/><br/> Em todos os outros discos replicados, a unidade de letra permanece a mesma.<br/><br/> Como o disco G: não está disponível, o sistema utilizará o C: dirija para o ficheiro de paging.
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

As nossas definições de ficheiros de paging no Azure VM são as seguintes:

![Definições de ficheiro de paginação na máquina virtual do Azure](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre as diretrizes para o disco de armazenamento temporário:
    - [Saiba mais sobre](https://blogs.technet.microsoft.com/dataplatforminsider/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) a utilização de SSDs em VMs Azure para armazenar extensões SQL Server TempDB e Buffer Pool
    - [Reveja](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance) as melhores práticas de desempenho para o SQL Server em VMs Azure.
- Depois da implementação estar instalada e em execução, [saiba mais](failover-failback-overview.md) sobre os diferentes tipos de ativação pós-falha.

