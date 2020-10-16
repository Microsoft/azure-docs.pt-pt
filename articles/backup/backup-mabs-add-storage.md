---
title: Use armazenamento de backup moderno com servidor de backup Azure
description: Conheça as novas funcionalidades no Azure Backup Server. Este artigo descreve como atualizar a instalação do Servidor de Cópia de Segurança.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: b077296e58e1193e454a686a392d802e905500a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91292902"
---
# <a name="add-storage-to-azure-backup-server"></a>Adicionar armazenamento ao Azure Backup Server

O Azure Backup Server V2 e mais tarde suporta o Modern Backup Storage que oferece uma poupança de armazenamento de 50%, cópias de segurança que são três vezes mais rápidas e mais eficientes. Também oferece armazenamento consciente da carga de trabalho.

> [!NOTE]
> Para utilizar o armazenamento de backup moderno, tem de executar backup Server V2 ou V3 no Windows Server 2016 ou V3 no Windows Server 2019.
> Se executar o Backup Server V2 numa versão anterior do Windows Server, o Azure Backup Server não pode tirar partido do armazenamento de backup moderno. Em vez disso, protege cargas de trabalho como com o Backup Server V1. Para obter mais informações, consulte a matriz de [proteção da](backup-mabs-protection-matrix.md)versão do Servidor de Cópia de Segurança .
>
> Para obter desempenhos de backup melhorados, recomendamos implementar MABS v3 com armazenamento hierárquico no Windows Server 2019. Consulte o artigo DPM "[Configurar MBS com Armazenamento Tiered](/system-center/dpm/add-storage#set-up-mbs-with-tiered-storage)" para obter etapas para configurar o armazenamento hierárquico.

## <a name="volumes-in-backup-server"></a>Volumes no Servidor de Backup

Backup Server V2 ou mais tarde aceita volumes de armazenamento. Quando adiciona um volume, o Backup Server forma o volume para o Sistema de Ficheiros Resiliente (ReFS), que o armazenamento de backup moderno requer. Para adicionar um volume, e expandi-lo mais tarde, se precisar, sugerimos que use este fluxo de trabalho:

1. Configurar o Servidor de Backup num VM.
2. Criar um volume num disco virtual numa piscina de armazenamento:
    1. Adicione um disco a uma piscina de armazenamento e crie um disco virtual com layout simples.
    2. Adicione quaisquer discos adicionais e estenda o disco virtual.
    3. Crie volumes no disco virtual.
3. Adicione os volumes ao Servidor de Backup.
4. Configure o armazenamento consciente da carga de trabalho.

## <a name="create-a-volume-for-modern-backup-storage"></a>Criar um volume para armazenamento de backup moderno

A utilização do Backup Server V2 ou mais tarde com volumes como armazenamento de disco pode ajudá-lo a manter o controlo sobre o armazenamento. Um volume pode ser um único disco. No entanto, se pretender prolongar o armazenamento no futuro, crie um volume a partir de um disco criado utilizando espaços de armazenamento. Isto pode ajudar se quiser expandir o volume para armazenamento de cópias de segurança. Esta secção oferece as melhores práticas para criar um volume com esta configuração.

1. No Gestor do Servidor, selecione **Conjuntos**de armazenamento de  >  **volumes de serviços**de arquivo  >  **e**armazenamento . Em **DISCOS FÍSICOS,** selecione **New Storage Pool**.

    ![Criar uma nova piscina de armazenamento](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. Na caixa de entrega **tasks,** selecione **Novo Disco Virtual**.

    ![Adicione um disco virtual](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Selecione a piscina de armazenamento e, em seguida, **selecione Adicionar Disco Físico**.

    ![Adicione um disco físico](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. Selecione o disco físico e, em seguida, **selecione Extend Virtual Disk**.

    ![Estender o disco virtual](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. Selecione o disco virtual e, em seguida, selecione **Novo Volume**.

    ![Criar um novo volume](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. No **slogan do servidor e do disco selecione** o servidor e o novo disco. Em seguida, selecione **Seguinte**.

    ![Selecione o servidor e o disco](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Adicione volumes ao armazenamento de discos do Servidor de Backup

> [!NOTE]
>
> - Adicione apenas um disco à piscina para manter a contagem da coluna a 1. Em seguida, pode adicionar discos conforme necessário depois.
> - Se adicionar vários discos à piscina de armazenamento em movimento, o número de discos é armazenado como o número de colunas. Quando mais discos são adicionados, eles só podem ser um múltiplo do número de colunas.

Para adicionar um volume ao Backup Server, no painel **de Gestão,** rescanear o armazenamento e, em seguida, selecione **Adicionar**. Aparece uma lista de todos os volumes disponíveis para o armazenamento do servidor de backup. Depois de adicionados volumes disponíveis à lista de volumes selecionados, pode dar-lhes um nome amigável para ajudá-lo a geri-los. Para formatar estes volumes para ReFS para que o Backup Server possa utilizar os benefícios do Armazenamento de Backup Moderno, selecione **OK**.

![Adicionar Volumes disponíveis](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Configurar armazenamento consciente da carga de trabalho

Com o armazenamento consciente da carga de trabalho, pode selecionar os volumes que preferencialmente armazenam certos tipos de cargas de trabalho. Por exemplo, pode definir volumes dispendiosos que suportam um elevado número de operações de entrada/saída por segundo (IOPS) para armazenar apenas as cargas de trabalho que requerem cópias de segurança frequentes e de grande volume. Um exemplo é o SQL Server com registos de transações. Outras cargas de trabalho que são suportadas com menos frequência, como os VMs, podem ser apoiadas até volumes de baixo custo.

### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Pode configurar o armazenamento consciente da carga de trabalho utilizando o powerShell cmdlet Update-DPMDiskStorage, que atualiza as propriedades de um volume na piscina de armazenamento num Servidor de Backup Azure.

Sintaxe:

`Parameter Set: Volume`

```powershell
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```

A imagem que se segue mostra o Update-DPMDiskStorage cmdlet na janela PowerShell.

![O comando Update-DPMDiskStorage na janela PowerShell](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

As alterações que eis ao utilizar o PowerShell refletem-se na Consola de Administrador do Servidor de Cópia de Segurança.

![Discos e volumes na Consola de Administrador](./media/backup-mabs-add-storage/mabs-add-storage-9.png)

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrar o armazenamento do legado para o armazenamento de backup moderno

Depois de atualizar ou instalar o Backup Server V2 e atualizar o sistema operativo para o Windows Server 2016, atualize os seus grupos de proteção para utilizar o Armazenamento de Backup Moderno. Por defeito, os grupos de proteção não são alterados. Continuam a funcionar como inicialmente foram criados.

A atualização dos grupos de proteção para utilizar o Armazenamento de Cópia de Segurança Moderno é opcional. Para atualizar o grupo de proteção, pare a proteção de todas as fontes de dados utilizando a opção de retenção de dados. Em seguida, adicione as fontes de dados a um novo grupo de proteção.

1. Na Consola de Administrador, selecione a **função Proteção.** Na lista **de Membros do Grupo de Proteção,** clique com o botão direito no membro e, em seguida, selecione **Stop protection of member**.

   ![Parar a proteção do membro](/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. Na caixa de diálogo **Remove from Group,** reveja o espaço do disco usado e o espaço livre disponível para a piscina de armazenamento. A predefinição é deixar os pontos de recuperação no disco e permitir que os mesmos expirem, conforme a política de retenção associada. Selecione **OK**.

   Se pretender devolver imediatamente o espaço do disco usado ao conjunto de armazenamento gratuito, selecione a **réplica Delete na** caixa de verificação do disco para eliminar os dados de cópia de segurança (e pontos de recuperação) associados a esse membro.

   ![Remover da caixa de diálogo do grupo](/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Crie um grupo de proteção que utilize o armazenamento de backup moderno. Inclua as fontes de dados desprotegidas.

## <a name="add-disks-to-increase-legacy-storage"></a>Adicione discos para aumentar o armazenamento do legado

Se pretender utilizar o armazenamento antigo com o Backup Server, poderá ter de adicionar discos para aumentar o armazenamento do legado.

Adicionar armazenamento no disco:

1. Na Consola de Administrador, selecione **Management**  >  **Disk Storage**  >  **Add**.

    ![Adicionar diálogo de armazenamento de disco](/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

2. No diálogo **de armazenamento de disco** de adicionar, selecione Adicionar **discos**.

3. Na lista de discos disponíveis, selecione os discos que pretende adicionar, selecione **Add**, e, em seguida, selecione **OK**.

## <a name="next-steps"></a>Passos seguintes

Depois de instalar o Backup Server, aprenda a preparar o seu servidor ou comece a proteger uma carga de trabalho.

- [Preparar cargas de trabalho do servidor de backup](backup-azure-microsoft-azure-backup.md)
- [Utilize o Servidor de Backup para fazer backup de um servidor VMware](backup-azure-backup-server-vmware.md)
- [Use o Servidor de Backup para fazer backup no SQL Server](backup-azure-sql-mabs.md)
