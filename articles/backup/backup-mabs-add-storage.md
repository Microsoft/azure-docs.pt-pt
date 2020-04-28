---
title: Utilize armazenamento de backup moderno com servidor de backup Azure
description: Conheça as novidades no Azure Backup Server. Este artigo descreve como atualizar a instalação do Seu Servidor de Backup.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: c6346d7b0275a00271c1787b378a63b8365edf2d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74172385"
---
# <a name="add-storage-to-azure-backup-server"></a>Adicionar armazenamento ao Azure Backup Server

O Azure Backup Server V2 e mais tarde suporta o Armazenamento de Backup Moderno que oferece uma poupança de armazenamento de 50%, backups que são três vezes mais rápidos e armazenamento mais eficiente. Também oferece armazenamento consciente da carga de trabalho.

> [!NOTE]
> Para utilizar o Armazenamento de Backup Moderno, tem de executar o Servidor de Backup V2 ou V3 no Windows Server 2016 ou V3 no Windows Server 2019.
> Se executar o Backup Server V2 numa versão anterior do Windows Server, o Azure Backup Server não pode tirar partido do Armazenamento de Backup Moderno. Em vez disso, protege as cargas de trabalho como faz com o Servidor de Backup V1. Para mais informações, consulte a matriz de [proteção](backup-mabs-protection-matrix.md)da versão 'Backup Server'.
>
> Para obter desempenhos de backup melhorados, recomendamos implementar MABS v3 com armazenamento hierárquico no Windows Server 2019. Consulte o artigo do DPM "[Configure MBS com Armazenamento Hierárquico](https://docs.microsoft.com/system-center/dpm/add-storage?view=sc-dpm-2019#set-up-mbs-with-tiered-storage)" para os passos para configurar o armazenamento hierárquico.

## <a name="volumes-in-backup-server"></a>Volumes no Servidor de Backup

O Servidor de Backup V2 ou mais tarde aceita volumes de armazenamento. Quando adiciona um volume, o Servidor de Backup formata o volume para o Sistema de Ficheiros Resiliente (ReFS), que o Armazenamento de Backup moderno requer. Para adicionar um volume, e expandi-lo mais tarde, se precisar, sugerimos que use este fluxo de trabalho:

1. Configurar o Servidor de Backup num VM.
2. Crie um volume num disco virtual numa piscina de armazenamento:
    1. Adicione um disco a uma piscina de armazenamento e crie um disco virtual com layout simples.
    2. Adicione quaisquer discos adicionais e estenda o disco virtual.
    3. Crie volumes no disco virtual.
3. Adicione os volumes ao Servidor de Backup.
4. Configure o armazenamento consciente da carga de trabalho.

## <a name="create-a-volume-for-modern-backup-storage"></a>Criar um volume para armazenamento de backup moderno

Utilizar o Servidor de Backup V2 ou mais tarde com volumes, uma vez que o armazenamento em disco pode ajudá-lo a manter o controlo sobre o armazenamento. Um volume pode ser um único disco. No entanto, se quiser estender o armazenamento no futuro, crie um volume a partir de um disco criado utilizando espaços de armazenamento. Isto pode ajudar se quiser expandir o volume para armazenamento de cópia de segurança. Esta secção oferece as melhores práticas para criar um volume com esta configuração.

1. No Gestor do Servidor, selecione **File and Storage Services** > **Volumes** > **Storage Pools**. Em **DISCOS FÍSICOS,** selecione **New Storage Pool**.

    ![Criar uma nova piscina de armazenamento](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. Na caixa de lançamento **tasks,** selecione **Novo Disco Virtual**.

    ![Adicione um disco virtual](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Selecione a piscina de armazenamento e, em seguida, **selecione Adicionar Disco Físico**.

    ![Adicione um disco físico](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. Selecione o disco físico e, em seguida, **selecione Extend Virtual Disk**.

    ![Estender o disco virtual](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. Selecione o disco virtual e, em seguida, selecione **Novo Volume**.

    ![Criar um novo volume](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. No diálogo **Selecione o servidor e o** diálogo do disco, selecione o servidor e o novo disco. Em seguida, selecione **Next**.

    ![Selecione o servidor e o disco](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Adicione volumes ao armazenamento de discos do Servidor de Backup

> [!NOTE]
>
> - Adicione apenas um disco à piscina para manter a contagem de colunas a 1. Em seguida, pode adicionar discos conforme necessário depois.
> - Se adicionar vários discos ao depósito de uma vez, o número de discos é armazenado como o número de colunas. Quando mais discos são adicionados, só podem ser um múltiplo do número de colunas.

Para adicionar um volume ao Backup Server, no painel **de gestão,** rescante o armazenamento e, em seguida, selecione **Adicionar**. Aparece uma lista de todos os volumes disponíveis para armazenamento de servidor de backup. Depois de serem adicionados volumes disponíveis à lista de volumes selecionados, pode dar-lhes um nome amigável para ajudá-los a geri-los. Para formatar estes volumes para ReFS para que o Servidor de Backup possa utilizar os benefícios do Armazenamento de Backup Moderno, selecione **OK**.

![Adicionar Volumes Disponíveis](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Configurar armazenamento consciente da carga de trabalho

Com armazenamento consciente da carga de trabalho, pode selecionar os volumes que preferencialmente armazenam certos tipos de carga seletiva. Por exemplo, pode definir volumes caros que suportam um elevado número de operações de entrada/saída por segundo (IOPS) para armazenar apenas as cargas de trabalho que requerem cópias de segurança frequentes e de alto volume. Um exemplo é o SQL Server com registos de transações. Outras cargas de trabalho que são apoiadas com menos frequência, como os VMs, podem ser apoiadas até volumes de baixo custo.

### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Pode configurar o armazenamento consciente da carga de trabalho utilizando o PowerShell cmdlet Update-DPMDiskStorage, que atualiza as propriedades de um volume no pool de armazenamento num Servidor de Backup Azure.

Sintaxe:

`Parameter Set: Volume`

```powershell
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```

A imagem seguinte mostra o cmdlet Update-DPMDiskStorage na janela PowerShell.

![O comando Update-DPMDiskStorage na janela PowerShell](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

As alterações que fizer ao utilizar o PowerShell refletem-se na Consola de Administrador do Servidor de Backup.

![Discos e volumes na Consola de Administrador](./media/backup-mabs-add-storage/mabs-add-storage-9.png)

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrar armazenamento legado para armazenamento de backup moderno

Depois de atualizar ou instalar o V2 do Servidor de Backup e atualizar o sistema operativo para o Windows Server 2016, atualize os seus grupos de proteção para utilizar o Armazenamento de Backup Moderno. Por padrão, os grupos de proteção não são alterados. Continuam a funcionar como foram inicialmente configurados.

A atualização dos grupos de proteção para utilizar o Armazenamento de Cópia de Segurança Moderno é opcional. Para atualizar o grupo de proteção, pare a proteção de todas as fontes de dados utilizando a opção de reter dados. Em seguida, adicione as fontes de dados a um novo grupo de proteção.

1. Na Consola do Administrador, selecione a função **Protection.** Na lista do Grupo de **Proteção,** clique no membro e, em seguida, selecione **a proteção stop do membro**.

   ![Parar a proteção do membro](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. Na caixa de diálogo **Remover do Grupo,** reveja o espaço utilizado do disco e o espaço livre disponível para a piscina de armazenamento. A predefinição é deixar os pontos de recuperação no disco e permitir que os mesmos expirem, conforme a política de retenção associada. Clique em **OK**.

   Se pretender devolver imediatamente o espaço de disco utilizado ao pool de armazenamento gratuito, selecione a **réplica Delete na** caixa de verificação do disco para eliminar os dados de backup (e pontos de recuperação) associados a esse membro.

   ![Remover da caixa de diálogo do Grupo](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Crie um grupo de proteção que utilize o Armazenamento de Backup Moderno. Inclua as fontes de dados desprotegidas.

## <a name="add-disks-to-increase-legacy-storage"></a>Adicione discos para aumentar o armazenamento do legado

Se quiser utilizar o armazenamento legado com o Backup Server, poderá ter de adicionar discos para aumentar o armazenamento do legado.

Adicionar armazenamento no disco:

1. Na Consola do Administrador, selecione **Management** > **Disk Storage** > **Add**.

    ![Adicionar diálogo de armazenamento de disco](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

2. No diálogo de armazenamento de **disco adicionar,** selecione **Adicionar discos**.

3. Na lista de discos disponíveis, selecione os discos que pretende adicionar, selecione **Adicionar**, e, em seguida, selecione **OK**.

## <a name="next-steps"></a>Passos seguintes

Depois de instalar o Backup Server, aprenda a preparar o seu servidor ou comece a proteger uma carga de trabalho.

- [Preparar cargas de trabalho do Servidor de Backup](backup-azure-microsoft-azure-backup.md)
- [Utilize o Servidor de Backup para fazer backup de um servidor VMware](backup-azure-backup-server-vmware.md)
- [Utilize o Servidor de Backup para fazer backup do Servidor SQL](backup-azure-sql-mabs.md)
