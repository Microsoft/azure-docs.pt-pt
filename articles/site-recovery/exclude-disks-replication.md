---
title: Excluir discos da replicação com recuperação do site Azure
description: Como excluir discos da replicação para Azure com a Recuperação do Site Azure.
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 15989fbfd65f758eb777c5170c217aba8707e0be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333669"
---
# <a name="exclude-disks-from-disaster-recovery"></a>Excluir discos da recuperação de desastres

Este artigo descreve como excluir os discos da replicação durante a recuperação de desastres das instalações para Azure com [a Recuperação do Local de Azure](site-recovery-overview.md). Pode excluir discos da replicação por uma série de razões:

- Para que os dados sem importância do disco excluído não sejam replicados.
- Para otimizar a largura de banda de replicação consumida, ou os recursos do lado do alvo.
- Para guardar recursos de armazenamento e rede, não replicando dados de que não precisa.
- Os VMs do Azure atingiram os limites de replicação da recuperação do local.


## <a name="supported-scenarios"></a>Cenários suportados

Pode excluir discos da replicação como resumido na tabela.

**Azure para o Azure** | **VMware para o Azure** | **Hyper-V para o Azure** | **Servidor Físico para Azure**
--- | --- | --- | ---
Sim | Sim | Sim | Sim

## <a name="exclude-limitations"></a>Excluir limitações

**Limitação** | **VMs do Azure** | **VMs VMware** | **VMs Hyper-V**
--- | --- | ---
**Tipos de discos** | Pode excluir discos básicos da replicação.<br/><br/> Não é possível excluir discos do sistema operativo ou discos dinâmicos. Os discos temporários são excluídos por predefinição. | Pode excluir discos básicos da replicação.<br/><br/> Não é possível excluir discos do sistema operativo ou discos dinâmicos. | Pode excluir discos básicos da replicação.<br/><br/> Não é possível excluir discos do sistema operativo. Recomendamos que não exclua discos dinâmicos. A Recuperação do Site não consegue identificar qual o VHS que é básico ou dinâmico no VM convidado. Se todos os discos de volume dinâmico dependentes não forem excluídos, o disco dinâmico protegido torna-se um disco falhado num VM de falha, e os dados desse disco não estão acessíveis.
**Disco replicador** | Não se pode excluir um disco que está a replicar-se.<br/><br/> Desativação e replicação reenerável para o VM. |  Não se pode excluir um disco que está a replicar-se. |  Não se pode excluir um disco que está a replicar-se.
**Serviço de mobilidade (VMware)** | Não relevante | Só pode excluir discos em VMs que tenham o serviço de Mobilidade instalado.<br/><br/> Isto significa que tem de instalar manualmente o serviço de Mobilidade nos VMs para os quais pretende excluir discos. Não é possível utilizar o mecanismo de instalação push porque instala o serviço Mobility apenas depois de ativar a replicação. | Não é relevante.
**Adicionar/Remover** | Pode adicionar discos geridos em VMs Azure ativados por replicação com discos geridos. Não é possível remover discos em VMs Azure ativados por replicação. | Não é possível adicionar ou remover discos após a replicação estar ativada. Desative e, em seguida, reprovação reenerável para adicionar um disco. | Não é possível adicionar ou remover discos após a replicação estar ativada. Desativar e, em seguida, replicação reenável.
**Ativação pós-falha** | Se uma aplicação precisar de um disco que excluiu, depois de falhar, precisa de criar o disco manualmente para que a aplicação replicada possa ser executada.<br/><br/> Em alternativa, pode criar o disco durante o failover VM, integrando a automação Azure num plano de recuperação. | Se excluir um disco de que uma aplicação necessita, crie-o manualmente no Azure após o failover. | Se excluir um disco de que uma aplicação necessita, crie-o manualmente no Azure após o failover.
**Discos de recuo no local criados manualmente** | Não relevante | **VMs do Windows**: Os discos criados manualmente em Azure não são falhados. Por exemplo, se falhar em três discos e criar dois discos diretamente num VM Azure, apenas os três discos que foram falhados são então falhados.<br/><br/> **Linux VMs**: Os discos criados manualmente em Azure falharam. Por exemplo, se falhar em três discos e criar dois discos num Azure VM, os cinco serão ressarados. Não pode excluir da reativação pós-falha os discos que foram criados manualmente. | Os discos criados manualmente em Azure não falham. Por exemplo, se falhar em três discos e criar dois discos diretamente num Azure VM, apenas três discos que foram falhados serão ressarídos.
**Discos excluídos no local** | Não relevante | Se falhar de volta à máquina original, a configuração do disco VM desreservada não inclui os discos excluídos. Os discos que foram excluídos da replicação VMware para Azure não estão disponíveis no VM de recuo. | Quando o recuo é para a localização original do Hiper-V, a configuração do disco VM de recuo permanece a mesma do disco VM de origem original. Os discos que foram excluídos do site Hyper-V para a replicação do Azure estão disponíveis no VM de recuo.



## <a name="typical-scenarios"></a>Cenários típicos

Exemplos de dados que são grandes candidatos à exclusão incluem escritas para um ficheiro de paging (pagefile.sys), e escreve para o ficheiro temporário do Microsoft SQL Server. Dependendo da carga de trabalho e do subsistema de armazenamento, os ficheiros de paging e temporário podem registar uma quantidade significativa de churn. Replicar este tipo de dados para o Azure é intensivo em recursos.

- Para otimizar a replicação de um VM com um único disco virtual que inclua tanto o sistema operativo como o ficheiro de paging, pode ser:
    1. Divida o disco virtual único em dois discos virtuais. Um disco virtual tem o sistema operativo e o outro tem o ficheiro de paginação.
    2. Exclua o disco do ficheiro de paginação da replicação.

- Para otimizar a replicação de um disco que inclua tanto o ficheiro temporário do Microsoft SQL Server como o ficheiro de base de dados do sistema, pode ser:
    1. Mantenha a base de dados do sistema e o tempdb em dois discos diferentes.
    2. Exclua o disco tempdb da replicação.

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>Exemplo 1: excluir o disco tempdb do SQL Server

Vamos ver como lidar com a exclusão do disco, failover e failover para uma fonte SQL Server Windows VM - **SalesDB****, para o qual queremos excluir a temperatura. 

### <a name="exclude-disks-from-replication"></a>Excluir discos da replicação

Temos estes discos na fonte Do Windows VM SalesDB.

**Nome do disco** | **Disco de SO convidado** | **Letra da unidade** | **Tipo de dados de disco**
--- | --- | --- | ---
DB-Disk0-OS | Disco0 | C:\ | Disco do sistema operativo.
DB-Disk1| Disk1 | D:\ | Base de dados do sistema SQL e Base de Dados de Utilizador1.
DB-Disk2 (disco excluído da proteção) | Disk2 | E:\ | Ficheiros temporários.
DB-Disk3 (disco excluído da proteção) | Disk3 | F:\ | Base de dados tempdb SQL.<br/><br/> Caminho da pasta - F:\MSSQL\Data\. Tome nota do caminho da pasta antes de falhar.
DB-Disk4 | Disk4 |G:\ | User Database2

1. Permitimos a replicação para o VM SalesDB.
2. Excluímos o Disk2 e o Disk3 da replicação porque os dados dos discos são temporários. 


### <a name="handle-disks-during-failover"></a>Manusear discos durante a falha

Uma vez que os discos não são replicados, quando falhas no Azure estes discos não estão presentes no Azure VM criado após o failover. O Azure VM tem os discos resumidos nesta tabela.

**Disco de SO convidado** | **Letra da unidade** | **Tipo de dados de disco**
--- | --- | ---
Disco0 | C:\ | Disco do sistema operativo.
Disk1 | E:\ | Armazenamento temporário<br/><br/>Azure adiciona este disco. Porque o Disk2 e o Disk3 foram excluídos da replicação, E: é a primeira letra de unidade da lista disponível. O Azure atribui E: ao volume de armazenamento temporário. Outras letras de unidade para discos replicados permanecem as mesmas.
Disk2 | D:\ | Base de dados do sistema SQL e User Database1
Disk3 | G:\ | User Database2

No nosso exemplo, uma vez que o Disk3, o disco temporário SQL, foi excluído da replicação e não está disponível no Azure VM, o serviço SQL está em estado parado, e precisa da trajetória F:\MSSQL\Data. Pode criar este caminho de várias maneiras: 

- Adicione um novo disco após o failover e atribua o caminho da pasta temporária.
- Utilizar um disco de armazenamento temporário existente para o caminho da pasta tempdb.

#### <a name="add-a-new-disk-after-failover"></a>Adicione um novo disco após falha

1. Aponte os caminhos de tempdb.mdf e tempdb.ldf do SQL antes da ativação pós-falha.
2. A partir do portal Azure, adicione um novo disco ao failover Azure VM. O disco deve ter o mesmo tamanho (ou maior) que o disco temporário SQL de origem (Disk3).
3. Inscreva-se no Azure VM.
4. Na consola da gestão de discos (diskmgmt.msc), inicialize e formate o disco adicionado recentemente.
5. Atribua a mesma letra de unidade que foi usada pelo disco temporário SQL (F:)
6. Crie uma pasta tempdb no volume F: (F:\MSSQL\Data).
7. Inicie o serviço SQL a partir da consola do serviço.

#### <a name="use-an-existing-temporary-storage-disk"></a>Utilize um disco de armazenamento temporário existente 

1. Abra uma linha de comandos.
2. Execute o SQL Server no modo de recuperação a partir da linha de comandos.

    ```console
    Net start MSSQLSERVER /f / T3608
    ```

3. Execute o sqlcmd seguinte para alterar o caminho de tempdb para o caminho novo.

    ```sql
    sqlcmd -A -S SalesDB        **Use your SQL DBname**
    USE master;     
    GO      
    ALTER DATABASE tempdb       
    MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
    GO      
    ALTER DATABASE tempdb       
    MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');       
    GO
    ```

4. Pare o serviço Microsoft SQL Server.

    ```console
    Net stop MSSQLSERVER
    ```

5. Inicie o serviço Microsoft SQL Server.

    ```console
    Net start MSSQLSERVER
    ```

### <a name="vmware-vms-disks-during-failback-to-original-location"></a>VMware VMs: Discos durante o failback para a localização original

Agora vamos ver como lidar com discos em VMware VMs quando falhar de volta à sua localização original no local.

- **Discos criados em Azure**: Uma vez que o nosso exemplo utiliza um Windows VM, os discos que cria manualmente no Azure não são replicados de volta ao seu site quando falha ou reprote um VM.
- **Disco de armazenamento temporário em Azure**: O disco de armazenamento temporário não é replicado de volta aos anfitriões no local.
- **Discos excluídos**: Os discos que foram excluídos da replicação VMware para Azure não estão disponíveis no VM no local após o failback.

Antes de voltar a falhar os VMs VMware VMs para a localização original, as definições do disco Azure VM são as seguintes.

**Disco de SO convidado** | **Letra da unidade** | **Tipo de dados de disco**
--- | --- | ---
Disco0 | C:\ | Disco do sistema operativo.
Disk1 | E:\ | Armazenamento temporário.
Disk2 | D:\ | Base de dados do sistema SQL e Base de Dados de Utilizador1.
Disk3 | G:\ | Base de Dados de Utilizadores2.

Após o failback, o VMware VM na localização original tem os discos resumidos na tabela.

**Disco de SO convidado** | **Letra da unidade** | **Tipo de dados de disco**
--- | --- | ---
Disco0 | C:\ | Disco do sistema operativo.
Disk1 | D:\ | Base de dados do sistema SQL e Base de Dados de Utilizador1.
Disk2 | G:\ | Base de Dados de Utilizadores2.


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>Hiper-V VMs: Discos durante o failback à localização original

Agora vamos ver como lidar com discos em Hiper-VMs quando falhares no local original.

- **Discos criados em Azure**: Os discos que cria manualmente em Azure não são replicados de volta ao seu site quando falha ou reprote um VM.
- **Disco de armazenamento temporário em Azure**: O disco de armazenamento temporário não é replicado de volta aos anfitriões no local.
- **Discos excluídos**: Após o recuo, a configuração do disco VM é a mesma que a configuração original do disco VM. Os discos que foram excluídos da replicação de Hyper-V para Azure estão disponíveis no VM de recuo.

Antes de voltar a falhar os Hiper-VMs para a localização original, as definições do disco Azure VM são as seguintes.

**Disco de SO convidado** | **Letra da unidade** | **Tipo de dados de disco**
--- | --- | ---
Disco0 | C:\ | Disco do sistema operativo.
Disk1 | E:\ | Armazenamento temporário.
Disk2 | D:\ | Base de dados do sistema SQL e Base de Dados de Utilizador1.
Disk3 | G:\ | Base de Dados de Utilizadores2.

Após o failover planeado (failback) de Azure para o Hyper-V no local, o Hiper-VM no local original tem os discos resumidos na tabela.

**Nome do Disco** | **N.º do disco do SO convidado** | **Letra da unidade** | **Tipo de dados de disco**
 --- | --- | --- | ---
DB-Disk0-OS | Disco0 |   C:\ | Disco do sistema operativo.
DB-Disk1 | Disk1 | D:\ | Base de dados do sistema SQL e Base de Dados de Utilizador1.
BD-Disk2 (disco excluído) | Disk2 | E:\ | Ficheiros temporários.
DB-Disk3 (disco excluído) | Disk3 | F:\ | Base de dados de temperatura SQL<br/><br/> Caminho da pasta (F:\MSSQL\Data \) .
DB-Disk4 | Disk4 | G:\ | User Database2


## <a name="example-2-exclude-the-paging-file-disk"></a>Exemplo 2: Excluir o disco de ficheiro de paging

Vamos ver como lidar com a exclusão do disco, failover e failover para uma fonte Windows VM, para a qual queremos excluir o pagefile.sys disco de ficheiros tanto na unidade D, como numa unidade alternativa.


### <a name="paging-file-on-the-d-drive"></a>Arquivo de paging na unidade D

Temos estes discos na fonte VM.

**Nome do disco** | **Disco de SO convidado** | **Letra da unidade** | **Tipo de dados de disco**
--- | --- | --- | ---
DB-Disk0-OS | Disco0 | C:\ | Disco do sistema operativo
DB-Disk1 (Excluir da replicação) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

As nossas definições de ficheiros de paging na origem VM são as seguintes:

![Screenshot do diálogo de memória virtual com a linha D: Drive [Volume de Pagefile] realçada mostrando um tamanho de ficheiro de paging (MB) de 3000-7000.](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. Permitimos a replicação para o VM.
2. Excluímos DB-Disk1 da replicação.

#### <a name="disks-after-failover"></a>Discos após falha

Depois de ter falhado o Azure VM tem os discos resumidos na tabela.

**Nome do disco** | **Sistema operativo convidado disco#** | **Letra da unidade** | **Tipo de dados no disco**
--- | --- | --- | ---
DB-Disk0-OS | Disco0 | C:\ | Disco do sistema operativo
DB-Disk1 | Disk1 | D:\ | Armazenamento/pagefile.sys temporário <br/><br/> Porque DB-Disk1 (D:) foi excluído, D: é a primeira letra de unidade da lista disponível.<br/><br/> O Azure atribui D: ao volume de armazenamento temporário.<br/><br/> Porque D: está disponível, a definição de ficheiro de paging VM permanece a mesma).
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

As nossas definições de ficheiros de paging no Azure VM são as seguintes:

![Definições de ficheiro de paginação na máquina virtual do Azure](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>Arquivo de paging em outra unidade (não D:)

Vejamos o exemplo em que o ficheiro de paging não está na unidade D.  

Temos estes discos na fonte VM.

**Nome do disco** | **Disco de SO convidado** | **Letra da unidade** | **Tipo de dados de disco**
--- | --- | --- | ---
DB-Disk0-OS | Disco0 | C:\ | Disco do sistema operativo
DB-Disk1 (Excluir da replicação) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

As nossas definições de ficheiros de paging nas VM no local são as seguintes:

![Definições de ficheiro de paginação na máquina virtual no local](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. Permitimos a replicação para o VM.
2. Excluímos DB-Disk1 da replicação.

#### <a name="disks-after-failover"></a>Discos após falha

Depois de ter falhado o Azure VM tem os discos resumidos na tabela.

**Nome do disco** | **N.º do disco do SO convidado** | **Letra da unidade** | **Tipo de dados de disco**
--- | --- | --- | ---
DB-Disk0-OS | Disco0  |C:\ | Disco do sistema operativo
DB-Disk1 | Disk1 | D:\ | Armazenamento temporário<br/><br/> Uma vez que D: é a primeira letra de unidade disponível na lista, o Azure atribui D: ao volume de armazenamento temporário.<br/><br/> Em todos os outros discos replicados, a unidade de letra permanece a mesma.<br/><br/> Como o disco G: o disco não está disponível, o sistema utilizará o C: unidade para o ficheiro de paging.
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

As nossas definições de ficheiros de paging no Azure VM são as seguintes:

![Screenshot do diálogo de memória virtual com a linha de unidade realçada mostrando uma definição de tamanho de ficheiro de paging de "Sistema gerido".](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre as diretrizes para o disco de armazenamento temporário:
    - [Saiba mais sobre](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) a utilização de SSDs em VMs Azure para armazenar extensões de tempDB e buffer pool SQL
    - [Reveja as ](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) melhores práticas de desempenho para SQL Server em VMs Azure.
- Depois da implementação estar instalada e em execução, [saiba mais](failover-failback-overview.md) sobre os diferentes tipos de ativação pós-falha.
