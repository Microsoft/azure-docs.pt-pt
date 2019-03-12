---
title: Utilizar o armazenamento de cópia de segurança moderno com o Azure Backup Server
description: Saiba mais sobre os novos recursos do servidor de cópia de segurança do Azure. Este artigo descreve como atualizar a instalação do servidor de cópia de segurança.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adigan
ms.openlocfilehash: ee62138a56851747e75d75b7a806b2b73ba4fbbb
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57544652"
---
# <a name="add-storage-to-azure-backup-server"></a>Adicionar armazenamento ao Azure Backup Server

O Azure Backup Server V2 e posterior suporta o armazenamento de cópia de segurança moderno que oferece poupança de armazenamento de 50%, cópias de segurança que é o armazenamento de três vezes mais rápido e mais eficiente. Ele também oferece armazenamento com deteção de carga de trabalho.

> [!NOTE]
> Para utilizar o armazenamento de cópia de segurança moderno, tem de executar cópia de segurança do servidor V2 ou V3 no Windows Server 2016 ou V3 no Windows Server 2019.
> Se executar V2 do servidor de cópia de segurança numa versão anterior do Windows Server, o servidor de cópia de segurança do Azure não podem tirar partido do armazenamento de cópia de segurança Moderno. Em vez disso, ele protege cargas de trabalho de forma que funciona com V1 do servidor de cópia de segurança. Para obter mais informações, consulte a versão do servidor de cópia de segurança [matriz de proteção](backup-mabs-protection-matrix.md).

## <a name="volumes-in-backup-server"></a>Volumes no servidor de cópia de segurança

Cópia de segurança de servidor V2 ou mais tarde aceita volumes de armazenamento. Quando adiciona um volume, o servidor de cópia de segurança formata o volume para o ficheiro de sistema Resiliente (ReFS), que necessita de armazenamento de cópia de segurança Moderno. Para adicionar um volume e expandi-lo mais tarde se for necessário, sugerimos que utilize este fluxo de trabalho:

1.  Configure o servidor de cópia de segurança numa VM.
2.  Crie um volume num disco virtual num agrupamento de armazenamento:
    1.  Adicionar um disco ao agrupamento de armazenamento e criar um disco virtual com o esquema simples.
    2.  Adicione quaisquer discos adicionais e expanda o disco virtual.
    3.  Crie volumes no disco virtual.
3.  Adicione os volumes de servidor de cópia de segurança.
4.  Configure o armazenamento com deteção de carga de trabalho.

## <a name="create-a-volume-for-modern-backup-storage"></a>Criar um volume para o armazenamento de cópia de segurança Moderno

Com a V2 de servidor de cópia de segurança ou posterior com volumes como armazenamento em disco pode ajudá-lo a manter o controlo sobre o armazenamento. Um volume pode ser um único disco. No entanto, se pretender expandir o armazenamento no futuro, crie um volume fora de um disco criado com espaços de armazenamento. Isso pode ajudar se quiser expandir o volume de armazenamento de cópia de segurança. Esta seção oferece práticas recomendadas para criar um volume com esta configuração.

1. No Gestor de servidor, selecione **File and Storage Services** > **Volumes** > **agrupamentos de armazenamento**. Sob **discos físicos**, selecione **novo agrupamento de armazenamento**.

    ![Criar um novo agrupamento de armazenamento](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. Na **tarefas** caixa de lista pendente, selecione **novo disco Virtual**.

    ![Adicionar um disco virtual](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Selecione o agrupamento de armazenamento e, em seguida, selecione **adicionar disco físico**.

    ![Adicionar um disco físico](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. Selecione o disco físico e, em seguida, selecione **expandir disco Virtual**.

    ![Expanda o disco virtual](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. Selecione o disco virtual e, em seguida, selecione **Novo Volume**.

    ![Crie um novo volume](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. Na **selecione o servidor e disco** caixa de diálogo, selecione o servidor e o novo disco. Em seguida, selecione **Seguinte**.

    ![Selecione o servidor e o disco](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Adicionar volumes ao armazenamento de disco do servidor de cópia de segurança

Adicionar um volume para o servidor de cópia de segurança, o **gerenciamento** painel, reanalise o armazenamento e, em seguida, selecione **Add**. É apresentada uma lista de todos os volumes disponíveis para serem adicionadas para o armazenamento de servidor de cópia de segurança. Depois de volumes disponíveis são adicionados à lista de volumes selecionados, pode dar-lhes um nome amigável para o ajudar a gerenciá-los. Para formatar estes volumes para ReFS para que o servidor de cópia de segurança pode usar os benefícios do armazenamento de cópia de segurança moderno, selecione **OK**.

![Adicionar Volumes disponíveis](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Configurar o armazenamento com deteção de carga de trabalho

Com o armazenamento com deteção de carga de trabalho, pode selecionar volumes que armazenam preferencialmente determinados tipos de cargas de trabalho. Por exemplo, pode definir volumes dispendiosos que suportam um número elevado de operações de entrada/saída por segundo (IOPS) para armazenar apenas as cargas de trabalho que necessitam de cópias de segurança frequentes, grande volume. Um exemplo é o SQL Server com logs de transação. Outras cargas de trabalho que são uma cópia de segurança com menos frequência, como VMs, podem ser copiadas para volumes de baixo custo.

### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Pode configurar o armazenamento com deteção de carga de trabalho através do cmdlet Update-DPMDiskStorage, que atualiza as propriedades de um volume no agrupamento de armazenamento num servidor de cópia de segurança do Azure PowerShell. 

Sintaxe:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```
Captura de ecrã seguinte mostra o cmdlet Update-DPMDiskStorage na janela do PowerShell.

![O comando Update-DPMDiskStorage na janela do PowerShell](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

As alterações que fizer através do PowerShell são refletidas na consola do administrador do servidor de cópia de segurança.

![Discos e volumes na consola do administrador](./media/backup-mabs-add-storage/mabs-add-storage-9.png)


## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrar armazenamento legado para o armazenamento de cópia de segurança Moderno
Depois de atualizar ou instalar V2 do servidor de cópia de segurança e atualize o sistema operativo para o Windows Server 2016, atualize os grupos de proteção para utilizar o armazenamento de cópia de segurança Moderno. Por predefinição, os grupos de proteção não são alterados. Continuam a funcionar conforme foram configurados inicialmente.

A atualização dos grupos de proteção para utilizar o armazenamento de cópia de segurança moderno é opcional. Para atualizar o grupo de proteção, pare a proteção de todas as origens de dados utilizando a opção manter dados. Em seguida, adicione as origens de dados a um novo grupo de proteção.

1. Na consola do administrador, selecione o **proteção** funcionalidade. Na **membro do grupo de proteção** listar, o membro com o botão direito e, em seguida, selecione **parar proteção do membro**.

  ![Parar proteção do membro](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. Na **remover do grupo** diálogo caixa, reveja o espaço em disco utilizado e o espaço livre disponível no agrupamento de armazenamento. A predefinição é deixar os pontos de recuperação no disco e permitir que os mesmos expirem, conforme a política de retenção associada. Clique em **OK**.

  Se pretender devolver imediatamente o espaço em disco utilizado para o pool de armazenamento livre, selecione o **Eliminar réplica no disco** caixa de verificação para eliminar os dados de cópia de segurança (e pontos de recuperação) associados a esse membro.

  ![Remover da caixa de diálogo de grupo](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Crie um grupo de proteção que utiliza o armazenamento de cópia de segurança Moderno. Inclua as origens de dados não protegidas.

## <a name="add-disks-to-increase-legacy-storage"></a>Adicionar discos para aumentar o armazenamento legado

Se pretender utilizar o armazenamento legado com o servidor de cópia de segurança, poderá ter de adicionar discos para aumentar o armazenamento legado.

Para adicionar armazenamento em disco:

1. Na consola do administrador, selecione **gerenciamento** > **armazenamento de disco** > **adicionar**.

    ![Adicionar a caixa de diálogo de armazenamento em disco](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

4. Na **adicionar armazenamento no disco** caixa de diálogo, selecione **adicione discos**.

5. Na lista de discos disponíveis, selecione os discos que pretende adicionar, selecione **Add**e, em seguida, selecione **OK**.

## <a name="next-steps"></a>Passos Seguintes
Depois de instalar o servidor de cópia de segurança, saiba como preparar o servidor ou começar a proteger uma carga de trabalho.

- [Preparar as cargas de trabalho do servidor de cópia de segurança](backup-azure-microsoft-azure-backup.md)
- [Utilizar o servidor de cópia de segurança para fazer backup de um servidor VMware](backup-azure-backup-server-vmware.md)
- [Utilizar o servidor de cópia de segurança para criar cópias de segurança do SQL Server](backup-azure-sql-mabs.md)
