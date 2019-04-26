---
title: Grupos de volumes do StorSimple Snapshot Manager | Documentos da Microsoft
description: Descreve como utilizar o snap-in do MMC do Snapshot Manager do StorSimple para criar e gerir grupos de volumes.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: 7a232414-6a28-4b81-bd7b-cf61e28b33d7
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: e84bc790ac577796e91be010deecc8c5cea1b010
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60303151"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Utilizar o Snapshot Manager do StorSimple para criar e gerir grupos de volumes
## <a name="overview"></a>Descrição geral
Pode utilizar o **grupos de volumes** nó ou o **âmbito** painel para atribuir volumes a grupos de volumes, ver informações sobre um grupo de volume, agendar cópias de segurança e editar grupos de volumes.

Os grupos de volume são conjuntos de volumes relacionados utilizados para garantir que as cópias de segurança são consistentes com aplicações. Para obter mais informações, consulte [Volumes e grupos de volumes](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) e [integração com o serviço de cópia de sombra de volumes do Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

> [!IMPORTANT]
> * Todos os volumes num grupo de volume devem vir de um fornecedor de serviços de nuvem única.
> * Ao configurar grupos de volumes, não misture volumes partilhados de cluster (CSVs) e não CSVs no mesmo grupo de volume. Snapshot Manager do StorSimple não suporta uma combinação de CSVs e não CSVs no mesmo instantâneo.

![Nó de grupos de volume](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Figura 1: Nó de grupos de volumes do StorSimple Snapshot Manager** 

Este tutorial explica como pode utilizar o Snapshot Manager do StorSimple para:

* Ver informações sobre os grupos de volume
* Criar um grupo de volume
* Fazer cópias de segurança de um grupo de volume
* Editar um grupo de volume
* Eliminar um grupo de volume

Todas essas ações também estão disponíveis os **ações** painel.

## <a name="view-volume-groups"></a>Ver grupos de volume
Se clicar o **grupos de volumes** nó, o **resultados** painel mostra as seguintes informações sobre cada grupo de volumes, consoante as seleções de coluna que fizer. (As colunas a **resultados** painel são configuráveis. Com o botão direito a **Volumes** nó, selecione **vista**e, em seguida, selecione **Add/Remove Columns**.)

| Coluna de resultados | Descrição |
|:--- |:--- |
| Name |O **nome** coluna contém o nome do grupo de volume. |
| Aplicação |O **aplicativos** coluna mostra o número de escritores VSS atualmente instalados e em execução no anfitrião do Windows. |
| Selecionado |O **selecionados** coluna mostra o número de volumes que estão contidos no grupo de volume. Um zero (0) indica que nenhum aplicativo é associado a volumes no grupo de volume. |
| Importado |O **importada** coluna mostra o número de volumes importados. Quando definido como **True**, esta coluna indica que um grupo de volume foram importado a partir do portal do Azure e não foi criado no Snapshot Manager do StorSimple. |

> [!NOTE]
> Grupos de volumes do StorSimple Snapshot Manager também são apresentados no **políticas de cópia de segurança** separador no portal do Azure.
> 
> 

## <a name="create-a-volume-group"></a>Criar um grupo de volume
Utilize o procedimento seguinte para criar um grupo de volume.

#### <a name="to-create-a-volume-group"></a>Para criar um grupo de volume
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. Na **âmbito** painel, clique com botão direito **grupos de volumes**e, em seguida, clique em **criar grupo de volumes**.
   
    ![Criar grupo de volumes](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    O **criar um grupo de volume** é apresentada a caixa de diálogo.
   
    ![Criar uma caixa de diálogo do grupo de volume](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. Introduza as seguintes informações:
   
   1. Na **nome** , escreva um nome exclusivo para o novo grupo de volume.
   2. Na **aplicativos** caixa, selecionar aplicativos associados com os volumes que irá adicionar ao grupo de volume.
      
       O **aplicativos** listas de caixa, apenas os aplicativos que utilizam volumes do StorSimple e têm escritores VSS habilitados para os mesmos. Um escritor VSS está ativado apenas se todos os volumes que reconheça o escritor de volumes do StorSimple. Se a caixa de aplicativos estiver vazia, não existem aplicações que utilizam volumes do StorSimple do Azure e tem suporte escritores VSS são instaladas. (Atualmente, Azure StorSimple suporta Microsoft Exchange e o SQL Server.) Para obter mais informações sobre os escritores VSS, consulte [integração com o serviço de cópia de sombra de volumes do Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).
      
       Se selecionar uma aplicação, todos os volumes associados, pelo que são automaticamente selecionados. Por outro lado, se selecionar volumes associados uma aplicação específica, o aplicativo está automaticamente selecionado na **aplicativos** caixa. 
   3. Na **Volumes** caixa, selecione volumes do StorSimple para adicionar ao grupo de volume. 
      
      * É possível incluir volumes com partições únicas ou múltiplas. (Múltiplos volumes de partição podem ser discos dinâmicos ou discos básicos com várias partições.) Um volume que contém várias partições é tratado como uma única unidade. Conseqüentemente, se adicionar apenas uma das partições para um grupo de volumes, todas as outras partições são adicionadas automaticamente a esse grupo de volume ao mesmo tempo. Depois de adicionar um volume de partição vários a um grupo de volume, o volume de partição vários continua deve ser tratada como uma única unidade.
      * Pode criar grupos de volume vazio ao não atribuir todos os volumes a eles. 
      * Não misture volumes partilhados de cluster (CSVs) e não CSVs no mesmo grupo de volume. Snapshot Manager do StorSimple não suporta uma combinação de CSV volumes e volumes sem CSV no mesmo instantâneo.
4. Clique em **OK** para guardar o grupo de volume.

## <a name="back-up-a-volume-group"></a>Fazer cópias de segurança de um grupo de volume
Utilize o procedimento seguinte para fazer backup de um grupo de volume.

#### <a name="to-back-up-a-volume-group"></a>Para fazer backup de um grupo de volume
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. Na **âmbito** painel, expanda o **grupos de volumes** nó, um nome de grupo do volume com o botão direito e, em seguida, clique em **efetuar cópia de segurança**.
   
    ![Backup imediatamente o grupo de volumes](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. Na **efetuar cópia de segurança** caixa de diálogo, selecione **instantâneo Local** ou **instantâneo de Cloud**e, em seguida, clique em **criar**.
   
    ![Tirar a caixa de diálogo de cópia de segurança](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. Para confirmar que a cópia de segurança está em execução, expanda o **trabalhos** nó e clique em **em execução**. A cópia de segurança deverá ser listada.
5. Para ver o instantâneo foi concluído, expanda o **catálogo de cópia de segurança** nó, expanda o nome do grupo de volume e, em seguida, clique em **instantâneo Local** ou **instantâneo de Cloud**. A cópia de segurança será apresentada se foi concluída com êxito.

## <a name="edit-a-volume-group"></a>Editar um grupo de volume
Utilize o procedimento seguinte para editar um grupo de volume.

#### <a name="to-edit-a-volume-group"></a>Para editar um grupo de volume
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. Na **âmbito** painel, expanda o **grupos de volumes** nó, um nome de grupo do volume com o botão direito e, em seguida, clique em **editar**.
3. O * * criar um grupo de volume * * é apresentada a caixa de diálogo. Pode alterar o **Name**, **aplicativos**, e **Volumes** entradas.
4. Clique em **OK** para guardar as alterações.

## <a name="delete-a-volume-group"></a>Eliminar um grupo de volume
Utilize o procedimento seguinte para eliminar um grupo de volume. 

> [!WARNING]
> Isto também elimina todas as cópias de segurança associadas ao grupo de volume.
> 
> 

#### <a name="to-delete-a-volume-group"></a>Para eliminar um grupo de volume
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. Na **âmbito** painel, expanda o **grupos de volumes** nó, um nome de grupo do volume com o botão direito e, em seguida, clique em **eliminar**.
3. O **eliminar grupo de volumes** é apresentada a caixa de diálogo. Tipo **confirmar** na caixa de texto e, em seguida, clique **OK**.
   
    O grupo eliminado o volume foi eliminado da lista na **resultados** painel e todas as cópias de segurança que estão associadas esse grupo de volumes são eliminadas do catálogo de cópia de segurança.

## <a name="next-steps"></a>Passos Seguintes
* Saiba como [utilizar o Snapshot Manager do StorSimple para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Saiba como [utilizar o Snapshot Manager do StorSimple para criar e gerir políticas de cópia de segurança](storsimple-snapshot-manager-manage-backup-policies.md).

