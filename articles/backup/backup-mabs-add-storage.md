---
title: Usar Armazenamento de Backup Moderno com Servidor de Backup do Azure
description: Saiba mais sobre os novos recursos do Servidor de Backup do Azure. Este artigo descreve como atualizar a instalação do servidor de backup.
ms.reviewer: adigan
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: dacurwin
ms.openlocfilehash: 92717e704fb3f9e79b364fcf47bbcc096c5dd1d0
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74090747"
---
# <a name="add-storage-to-azure-backup-server"></a>Adicionar armazenamento ao Azure Backup Server

O Servidor de Backup do Azure V2 e posterior dá suporte a Armazenamento de Backup Moderno que oferece economia de armazenamento de 50%, backups que são três vezes mais rápidos e armazenamento mais eficiente. Ele também oferece armazenamento com reconhecimento de carga de trabalho.

> [!NOTE]
> Para usar Armazenamento de Backup Moderno, você deve executar o servidor de backup v2 ou V3 no Windows Server 2016 ou V3 no Windows Server 2019.
> Se você executar o servidor de backup V2 em uma versão anterior do Windows Server, Servidor de Backup do Azure não poderá aproveitar Armazenamento de Backup Moderno. Em vez disso, ele protege as cargas de trabalho como faz com o servidor de backup v1. Para obter mais informações, consulte a matriz de [proteção](backup-mabs-protection-matrix.md)de versão do servidor de backup.
>
> Para obter desempenho de backup aprimorado, recomendamos implantar o MABS v3 com armazenamento em camadas no Windows Server 2019. Veja o artigo do DPM "[Configurar MBS com armazenamento em camadas](https://docs.microsoft.com/system-center/dpm/add-storage?view=sc-dpm-2019#set-up-mbs-with-tiered-storage)" para obter as etapas para configurar o armazenamento em camadas.

## <a name="volumes-in-backup-server"></a>Volumes no servidor de backup

O servidor de backup v2 ou posterior aceita volumes de armazenamento. Quando você adiciona um volume, o servidor de backup formata o volume para o ReFS (sistema de arquivos resiliente), que Armazenamento de Backup Moderno requer. Para adicionar um volume e expandi-lo mais tarde, se necessário, sugerimos que você use este fluxo de trabalho:

1. Configure o servidor de backup em uma VM.
2. Criar um volume em um disco virtual em um pool de armazenamento:
    1. Adicione um disco a um pool de armazenamento e crie um disco virtual com layout simples.
    2. Adicione discos adicionais e estenda o disco virtual.
    3. Crie volumes no disco virtual.
3. Adicione os volumes ao servidor de backup.
4. Configure o armazenamento com reconhecimento de carga de trabalho.

## <a name="create-a-volume-for-modern-backup-storage"></a>Criar um volume para Armazenamento de Backup Moderno

O uso do servidor de backup v2 ou posterior com volumes como armazenamento em disco pode ajudá-lo a manter o controle do armazenamento. Um volume pode ser um único disco. No entanto, se você quiser estender o armazenamento no futuro, crie um volume a partir de um disco criado usando espaços de armazenamento. Isso pode ajudar se você quiser expandir o volume para armazenamento de backup. Esta seção oferece as práticas recomendadas para a criação de um volume com essa configuração.

1. Em Gerenciador do Servidor, selecione **serviços de arquivo e armazenamento** > **volumes** > **pools de armazenamento**. Em **discos físicos**, selecione **novo pool de armazenamento**.

    ![Criar um novo pool de armazenamento](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. Na caixa suspensa **tarefas** , selecione **novo disco virtual**.

    ![Adicionar um disco virtual](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Selecione o pool de armazenamento e, em seguida, selecione **adicionar disco físico**.

    ![Adicionar um disco físico](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. Selecione o disco físico e, em seguida, selecione **estender disco virtual**.

    ![Estender o disco virtual](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. Selecione o disco virtual e, em seguida, selecione **novo volume**.

    ![Criar um novo volume](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. Na caixa de diálogo **selecionar servidor e disco** , selecione o servidor e o novo disco. Em seguida, selecione **Seguinte**.

    ![Selecionar o servidor e o disco](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Adicionar volumes ao armazenamento de disco do servidor de backup

> [!NOTE]
>
> - Adicione apenas um disco ao pool para manter a contagem de colunas como 1. Em seguida, você pode adicionar discos conforme necessário posteriormente.
> - Se você adicionar vários discos ao pool de armazenamento em um lugar, o número de discos será armazenado como o número de colunas. Quando mais discos são adicionados, eles só podem ser um múltiplo do número de colunas.

Para adicionar um volume ao servidor de backup, no painel **Gerenciamento** , examine novamente o armazenamento e, em seguida, selecione **Adicionar**. É exibida uma lista de todos os volumes disponíveis a serem adicionados para o armazenamento do servidor de backup. Depois que os volumes disponíveis são adicionados à lista de volumes selecionados, você pode dar a eles um nome amigável para ajudá-lo a gerenciá-los. Para formatar esses volumes para ReFS, para que o servidor de backup possa usar os benefícios de Armazenamento de Backup Moderno, selecione **OK**.

![Adicionar volumes disponíveis](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Configurar o armazenamento com reconhecimento de carga de trabalho

Com o armazenamento com reconhecimento de carga de trabalho, você pode selecionar os volumes que armazenam preferencialmente determinados tipos de cargas de trabalho. Por exemplo, você pode definir volumes caros que dão suporte a um grande número de operações de entrada/saída por segundo (IOPS) para armazenar somente as cargas de trabalho que exigem backups frequentes de alto volume. Um exemplo é SQL Server com logs de transação. Outras cargas de trabalho que são submetidas a backup com menos frequência, como VMs, podem ser submetidas a backup em volumes de baixo custo.

### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Você pode configurar o armazenamento com reconhecimento de carga de trabalho usando o cmdlet do PowerShell Update-DPMDiskStorage, que atualiza as propriedades de um volume no pool de armazenamento em um Servidor de Backup do Azure.

Sintaxe

`Parameter Set: Volume`

```powershell
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```

A captura de tela a seguir mostra o cmdlet Update-DPMDiskStorage na janela do PowerShell.

![O comando Update-DPMDiskStorage na janela do PowerShell](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

As alterações feitas usando o PowerShell são refletidas no servidor de backup Console do Administrador.

![Discos e volumes no Console do Administrador](./media/backup-mabs-add-storage/mabs-add-storage-9.png)

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrar armazenamento herdado para Armazenamento de Backup Moderno

Depois de atualizar para o ou instalar o servidor de backup V2 e atualizar o sistema operacional para o Windows Server 2016, atualize seus grupos de proteção para usar Armazenamento de Backup Moderno. Por padrão, os grupos de proteção não são alterados. Eles continuam a funcionar como foram inicialmente configurados.

A atualização de grupos de proteção para usar Armazenamento de Backup Moderno é opcional. Para atualizar o grupo de proteção, interrompa a proteção de todas as fontes de dados usando a opção manter dados. Em seguida, adicione as fontes de dados a um novo grupo de proteção.

1. No Console do Administrador, selecione o recurso **proteção** . Na lista de **membros do grupo de proteção** , clique com o botão direito do mouse no membro e selecione **parar proteção do membro**.

   ![Interromper a proteção do membro](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. Na caixa **de diálogo remover do grupo** , examine o espaço em disco usado e o espaço livre disponível para o pool de armazenamento. O padrão é deixar os pontos de recuperação no disco e permitir que eles expirem de acordo com a política de retenção associada. Clique em **OK**.

   Se você quiser retornar imediatamente o espaço em disco usado para o pool de armazenamento livre, marque a caixa de seleção **excluir réplica no disco** para excluir os dados de backup (e os pontos de recuperação) associados a esse membro.

   ![Caixa de diálogo remover do grupo](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Crie um grupo de proteção que usa Armazenamento de Backup Moderno. Inclua as fontes de dados desprotegidas.

## <a name="add-disks-to-increase-legacy-storage"></a>Adicionar discos para aumentar o armazenamento herdado

Se você quiser usar o armazenamento herdado com o servidor de backup, talvez seja necessário adicionar discos para aumentar o armazenamento herdado.

Para adicionar armazenamento em disco:

1. No Console do Administrador, selecione >  de gerenciamento **armazenamento em disco** > **Adicionar**.

    ![Caixa de diálogo Adicionar Armazenamento em Disco](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

2. Na caixa de diálogo **adicionar armazenamento em disco** , selecione **adicionar discos**.

3. Na lista de discos disponíveis, selecione os discos que você deseja adicionar, selecione **Adicionar**e, em seguida, selecione **OK**.

## <a name="next-steps"></a>Passos seguintes

Depois de instalar o servidor de backup, saiba como preparar o servidor ou começar a proteger uma carga de trabalho.

- [Preparar cargas de trabalho do servidor de backup](backup-azure-microsoft-azure-backup.md)
- [Usar o servidor de backup para fazer backup de um servidor VMware](backup-azure-backup-server-vmware.md)
- [Usar o servidor de backup para fazer backup de SQL Server](backup-azure-sql-mabs.md)
