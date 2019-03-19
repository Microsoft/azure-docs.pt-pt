---
title: Excluir discos da replicação ao configurar a recuperação após desastre com o serviço Azure Site Recovery | Documentos da Microsoft
description: Descreve como excluir discos da VM da replicação durante a recuperação após desastre para o Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 01/19/2019
ms.author: mayg
ms.openlocfilehash: f86ded99ef5280a4e6929c39a9fd323d1b61f6f0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57992332"
---
# <a name="exclude-disks-from-replication"></a>Excluir discos da replicação
Este artigo descreve como excluir discos da replicação. Esta exclusão pode otimizar a largura de banda de replicação consumida ou otimizar os recursos do lado do destino que esses discos utilizam.

## <a name="supported-scenarios"></a>Cenários suportados

**Funcionalidade** | **VMware para o Azure** | **Hyper-V para o Azure** | **Azure para o Azure**| **Hyper-V para Hyper-V** 
--|--|--|--|--
Excluir o disco | Sim | Sim | Não | Não

## <a name="why-exclude-disks-from-replication"></a>Porquê excluir discos da replicação?
Muitas vezes, é necessário excluir discos da replicação porque:

- As alterações a dados no disco excluído não são importantes ou não têm de ser replicadas.

- Pretende poupar recursos de armazenamento e de rede ao não replicar estas alterações.

## <a name="what-are-the-typical-scenarios"></a>Quais são os cenários típicos?
Pode identificar exemplos específicos de alterações a dados que são excelentes candidatos para exclusão. Os exemplos podem incluir escritas num ficheiro de paginação (pagefile.sys) e escritas no ficheiro tempdb do Microsoft SQL Server. Dependendo da carga de trabalho e do subsistema de armazenamento, o ficheiro de paginação pode registar um volume muito elevado de alterações. No entanto, replicar estes dados a partir do site primário para o Azure consumiria recursos de forma intensiva. Assim, pode utilizar os seguintes passos para otimizar a replicação de uma máquina virtual com um disco virtual único que tenha o sistema operativo e o ficheiro de paginação:

1. Divida o disco virtual único em dois discos virtuais. Um disco virtual tem o sistema operativo e o outro tem o ficheiro de paginação.
2. Exclua o disco do ficheiro de paginação da replicação.

Da mesma forma, pode utilizar os seguintes passos para otimizar um disco que tenha o ficheiro tempdb do Microsoft SQL Server e o ficheiro da base de dados do sistema:

1. Mantenha a base de dados do sistema e o tempdb em dois discos diferentes.
2. Exclua o disco tempdb da replicação.

## <a name="how-to-exclude-disks"></a>Como Excluir discos
Siga o fluxo de trabalho [Enable replication (Ativar a replicação)](site-recovery-hyper-v-site-to-azure.md) para proteger uma máquina virtual a partir do portal do Azure Site Recovery. No quarto passo do fluxo de trabalho, utilize a coluna **DISK TO REPLICATE (DISCO A REPLICAR)** para excluir discos da replicação. Por predefinição, todos os discos estão selecionados para replicação. Na caixa de verificação, anule a seleção dos discos que pretende excluir da replicação e conclua os passos para ativá-la.

![Excluir discos da replicação e ativar a replicação para reativação pós-falha do Hyper-V para o Azure](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)

>[!NOTE]
>
> * Só pode excluir discos básicos de replicação. Não é possível excluir discos do sistema operativo. Recomendamos que não exclua discos dinâmicos. O Azure Site Recovery não consegue identificar o disco rígido virtual (VHD) básico ou dinâmico na máquina virtual convidada.  Se todos os discos de volume dinâmico dependentes não forem excluídos, o disco dinâmico protegido torna-se um disco com falha numa máquina virtual de ativação pós-falha e os dados desse disco não estarão acessíveis.
> * Após ativar a replicação, não pode adicionar ou remover discos para replicação. Se pretende adicionar ou excluir um disco, tem de desativar a proteção da máquina virtual e reativá-la novamente.
> * Se excluir um disco necessário para o funcionamento de uma aplicação, após a ativação pós-falha do Azure, tem de criar o disco manualmente no Azure, para que a aplicação replicada possa ser executada. Em alternativa, pode integrar a automatização do Azure num plano de recuperação para criar o disco durante a ativação pós-falha da máquina.
> * Os discos que criar manualmente no Azure não realizarão a reativação pós-falha. Por exemplo, se realizar a ativação pós-falha de três discos e criar dois discos diretamente em Máquinas Virtuais do Azure, apenas três discos em que foi realizada a ativação pós falha realizarão a reativação pós-falha do Azure para o Hyper-V. Não pode incluir discos que foram criados manualmente na reativação pós-falha ou em replicação inversa do Hyper-V para o Azure.

## <a name="end-to-end-scenarios-of-exclude-disks"></a>Cenários ponto a ponto da exclusão de discos
Para compreender a funcionalidade de exclusão de discos, consideremos dois cenários:

- Disco tempdb do SQL Server
- Disco do ficheiro de paginação (pagefile.sys)

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>Exemplo 1: Excluir o disco tempdb do SQL Server
Consideremos uma máquina virtual do SQL Server que tem um tempdb que pode ser excluído.

O nome do disco virtual é SalesDB.

Os discos na máquina virtual de origem são os seguintes:


**Nome do disco** | **Sistema operativo convidado disco#** | **Letra da unidade** | **Tipo de dados no disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disco do sistema operativo
DB-Disk1| Disk1 | D:\ | Base de dados do sistema SQL e User Database1
DB-Disk2 (disco excluído da proteção) | Disk2 | E:\ | Ficheiros temporários
DB-Disk3 (disco excluído da proteção) | Disk3 | F:\ | Base de dados tempdb do SQL (caminho da pasta (F:\MSSQL\Data\) <br /> <br />Anote o caminho da pasta antes da ativação pós-falha.
DB-Disk4 | Disk4 |G:\ |User Database2

Uma vez que as alterações a dados em dois discos da máquina virtual são temporárias, enquanto proteger a máquina virtual de SalesDB, exclua Disk2 e Disk3 da replicação. O Azure Site Recovery não irá replicar esses discos. Na ativação pós-falha, os discos não estarão presentes na máquina virtual de ativação pós-falha no Azure.

Os discos na máquina virtual do Azure após a ativação pós-falha são os seguintes:

**Sistema operativo convidado disco#** | **Letra da unidade** | **Tipo de dados no disco**
--- | --- | ---
DISK0 | C:\ | Disco do sistema operativo
Disk1 | E:\ | Armazenamento temporário<br /> <br />O Azure adiciona este disco e atribui a primeira letra de unidade disponível.
Disk2 | D:\ | Base de dados do sistema SQL e User Database1
Disk3 | G:\ | User Database2

Uma vez que Disk2 e Disk3 foram excluídos da máquina virtual SalesDB, E: é a primeira letra de unidade disponível na lista. O Azure atribui E: ao volume de armazenamento temporário. Em todos os outros discos replicados, as unidades de letra permanecem as mesmas.

Disk3, que foi o disco tempdb do SQL (caminho da pasta tempdb F:\MSSQL\Data\), foi excluído da replicação. O disco não está disponível na máquina virtual de ativação pós-falha. Como resultado, o serviço SQL está num estado parado e precisa do caminho F:\MSSQL\Data.

Existem duas formas de criar este caminho:

- Adicionar um disco novo e atribuir o caminho da pasta tempdb.
- Utilizar um disco de armazenamento temporário existente para o caminho da pasta tempdb.

### <a name="add-a-new-disk"></a>Adicionar um disco novo:

1. Aponte os caminhos de tempdb.mdf e tempdb.ldf do SQL antes da ativação pós-falha.
2. No portal do Azure, adicione um disco novo à máquina virtual de ativação pós-falha com um tamanho igual ou superior ao do disco tempdb do SQL de origem (Disk3).
3. Inicie sessão na máquina virtual do Azure. Na consola da gestão de discos (diskmgmt.msc), inicialize e formate o disco adicionado recentemente.
4. Atribua a mesma letra de unidade que foi utilizada pelo disco tempdb do SQL (F:).
5. Crie uma pasta tempdb no volume F: (F:\MSSQL\Data).
6. Inicie o serviço SQL a partir da consola do serviço.

### <a name="use-an-existing-temporary-storage-disk-for-the-sql-tempdb-folder-path"></a>Utilize um disco de armazenamento temporário existente para o caminho da pasta tempdb do SQL:

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

Veja a diretriz seguinte do Azure relativa ao disco de armazenamento temporário:

* [Utilizar SSDs em VMs do Azure para armazenar o ficheiro TempDB do SQL Server e Extensões do Conjunto de Memórias Intermédias](https://blogs.technet.microsoft.com/dataplatforminsider/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)
* [Melhores práticas de desempenho do SQL Server nas Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance)

## <a name="failback-from-azure-to-an-on-premises-host"></a>Reativação pós-falha (do Azure para um anfitrião no local)
Agora, vamos compreender os discos que vão ser replicados quando realizar a ativação pós-falha do Azure para o anfitrião Hyper-V no local. Os discos que criar manualmente no Azure não serão replicados. Por exemplo, se realizar a ativação pós-falha de três discos e criar dois diretamente em Máquinas Virtuais do Azure, apenas três discos em que foi realizada a ativação pós-falha realizarão a reativação pós-falha. Não pode incluir discos que foram criados manualmente na reativação pós-falha ou na nova proteção no local para o Azure. O disco de armazenamento temporário também não é replicado para anfitriões no local.

### <a name="failback-to-original-location-recovery"></a>Reativação pós-falha para a recuperação de localização original

No exemplo anterior, a configuração de disco da máquina virtual do Azure é a seguinte:

**Sistema operativo convidado disco#** | **Letra da unidade** | **Tipo de dados no disco**
--- | --- | ---
DISK0 | C:\ | Disco do sistema operativo
Disk1 | E:\ | Armazenamento temporário<br /> <br />O Azure adiciona este disco e atribui a primeira letra de unidade disponível.
Disk2 | D:\ | Base de dados do sistema SQL e User Database1
Disk3 | G:\ | User Database2

Quando a reativação pós-falha é realizada para a localização original, a configuração do disco da máquina virtual de reativação pós-falha permanece igual à configuração do disco da máquina virtual original para o Hyper-V. Os discos que foram excluídos do site Hyper-V para o Azure estarão disponíveis na máquina virtual de reativação pós-falha.

Após a ativação pós-falha planeada do Azure para o Hyper-V no local, os discos na máquina virtual do Hyper-V (localização original) são os seguintes:

**Nome do Disco** | **Sistema operativo convidado disco#** | **Letra da unidade** | **Tipo de dados no disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 |   C:\ | Disco do sistema operativo
DB-Disk1 | Disk1 | D:\ | Base de dados do sistema SQL e User Database1
BD-Disk2 (disco excluído) | Disk2 | E:\ | Ficheiros temporários
DB-Disk3 (disco excluído) | Disk3 | F:\ | Base de dados tempdb do SQL (caminho da pasta (F:\MSSQL\Data\)
DB-Disk4 | Disk4 | G:\ | User Database2

## <a name="example-2-exclude-the-paging-file-pagefilesys-disk"></a>Exemplo 2: Excluir o disco do ficheiro de paginação (pagefile.sys)

Consideremos uma máquina virtual que tem um disco de ficheiro de paginação que pode ser excluído.
Existem dois casos.

### <a name="case-1-the-paging-file-is-configured-on-the-d-drive"></a>Caso 1: O ficheiro de paginação está configurado na unidade d:
Segue-se a configuração do disco:

**Nome do disco** | **Sistema operativo convidado disco#** | **Letra da unidade** | **Tipo de dados no disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disco do sistema operativo
DB-Disk1 (disco excluído da proteção) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

Seguem-se as definições de ficheiro de paginação na máquina virtual de origem:

![Definições de ficheiro de paginação na máquina virtual de origem](./media/hyper-v-exclude-disk/pagefile-on-d-drive-sourceVM.png)

Após a ativação pós-falha da máquina virtual do Hyper-V para o Azure, os discos na máquina virtual do Azure são os seguintes:

**Nome do disco** | **Sistema operativo convidado disco#** | **Letra da unidade** | **Tipo de dados no disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disco do sistema operativo
DB-Disk1 | Disk1 | D:\ | Armazenamento temporário<br /> <br />pagefile.sys
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

Uma vez que Disk1 (d:) foi excluído, D: é a primeira letra de unidade disponível na lista. O Azure atribui D: ao volume de armazenamento temporário. Uma vez que D: está disponível na máquina virtual do Azure, a definição de ficheiro de paginação da máquina virtual permanece a mesma.

Seguem-se as definições de ficheiro de paginação na máquina virtual do Azure:

![Definições de ficheiro de paginação na máquina virtual do Azure](./media/hyper-v-exclude-disk/pagefile-on-Azure-vm-after-failover.png)

### <a name="case-2-the-paging-file-is-configured-on-another-drive-other-than-d-drive"></a>Caso 2: O ficheiro de paginação está configurado noutra unidade (que não seja a unidade d)

Segue-se a configuração de disco da máquina virtual de origem:

**Nome do disco** | **Sistema operativo convidado disco#** | **Letra da unidade** | **Tipo de dados no disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disco do sistema operativo
DB-Disk1 (disco excluído da proteção) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

Seguem-se as definições de ficheiro de paginação na máquina virtual no local:

![Definições de ficheiro de paginação na máquina virtual no local](./media/hyper-v-exclude-disk/pagefile-on-g-drive-sourceVM.png)

Após a ativação pós-falha da máquina virtual do Hyper-V para o Azure, os discos na máquina virtual do Azure são os seguintes:

**Nome do disco** | **Sistema operativo convidado disco#** | **Letra da unidade** | **Tipo de dados no disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0  |C:\ |Disco do sistema operativo
DB-Disk1 | Disk1 | D:\ | Armazenamento temporário<br /> <br />pagefile.sys
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

Uma vez que D: é a primeira letra de unidade disponível na lista, o Azure atribui D: ao volume de armazenamento temporário. Em todos os outros discos replicados, a unidade de letra permanece a mesma. Uma vez que o disco G: não está disponível, o sistema irá utilizar a unidade C: para o ficheiro de paginação.

Seguem-se as definições de ficheiro de paginação na máquina virtual do Azure:

![Definições de ficheiro de paginação na máquina virtual do Azure](./media/hyper-v-exclude-disk/pagefile-on-Azure-vm-after-failover-2.png)

## <a name="next-steps"></a>Passos Seguintes
Depois da implementação estar instalada e em execução, [saiba mais](site-recovery-failover.md) sobre os diferentes tipos de ativação pós-falha.
