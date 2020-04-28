---
title: Grupos de volume do StorSimple Snapshot Manager / Microsoft Docs
description: Descreve como usar o snap-in Do StorSimple Snapshot Manager MMC para criar e gerir grupos de volume.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: 7a232414-6a28-4b81-bd7b-cf61e28b33d7
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 5198729cf96fb48c5dcd05096c04ea4d77c26de5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75931499"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Use o StorSimple Snapshot Manager para criar e gerir grupos de volume
## <a name="overview"></a>Descrição geral
Pode utilizar o nó dos **Grupos de Volume** no painel **scope** para atribuir volumes a grupos de volume, visualizar informações sobre um grupo de volume, agendar backups e editar grupos de volume.

Os grupos de volume são conjuntos de volumes relacionados usados para garantir que as cópias de segurança são consistentes com aplicações. Para mais informações, consulte [Volumes e grupos de volume](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) e [integração com o Serviço](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service)de Cópia de Sombra do Volume do Windows .

> [!IMPORTANT]
> * Todos os volumes de um grupo de volume devem provir de um único fornecedor de serviços na nuvem.
> * Quando configurar grupos de volume, não misture volumes partilhados por cluster (CSVs) e não-CSVs no mesmo grupo de volume. O StorSimple Snapshot Manager não suporta uma mistura de CSVs e não-CSVs no mesmo instantâneo.

![Nó de grupos de volume](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Figura 1: StorSimple Snapshot Manager Volume Groups** 

Este tutorial explica como pode usar o StorSimple Snapshot Manager para:

* Consulte informações sobre os seus grupos de volume
* Criar um grupo de volume
* Apoiar um grupo de volume
* Editar um grupo de volume
* Eliminar um grupo de volume

Todas estas ações também estão disponíveis no painel **de ações.**

## <a name="view-volume-groups"></a>Ver grupos de volume
Se clicar no nó dos **Grupos** de Volume, o painel **de resultados** mostra as seguintes informações sobre cada grupo de volume, dependendo das seleções de colunas que fizer. (As colunas do painel **de resultados** são configuráveis. Clique à direita no nó dos **Volumes,** selecione **Ver**, e, em seguida, selecione **Colunas adicionar/remover**.)

| Coluna de resultados | Descrição |
|:--- |:--- |
| Nome |A coluna **Nome** contém o nome do grupo de volume. |
| Aplicação |A coluna **Aplicações** mostra o número de escritores VSS atualmente instalados e em execução no anfitrião do Windows. |
| Selecionado |A coluna **Selecionada** mostra o número de volumes contidos no grupo de volume. Um zero (0) indica que nenhuma aplicação está associada aos volumes do grupo de volume. |
| Importado |A coluna **importada** mostra o número de volumes importados. Quando definida para **True,** esta coluna indica que um grupo de volume foi importado do portal Azure e não foi criado no StorSimple Snapshot Manager. |

> [!NOTE]
> Os grupos de volume storSimple Snapshot Manager também são apresentados no separador **Políticas de Backup** no portal Azure.
> 
> 

## <a name="create-a-volume-group"></a>Criar um grupo de volume
Utilize o seguinte procedimento para criar um grupo de volume.

#### <a name="to-create-a-volume-group"></a>Para criar um grupo de volume
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **scope,** clique à direita Grupos de **Volume,** e, em seguida, clique em **Criar Grupo de Volume**.
   
    ![Criar grupo de volume](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    Aparece a caixa de diálogo de grupo Criar um grupo de **volume.**
   
    ![Criar um diálogo de grupo de volume](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. Introduza as seguintes informações:
   
   1. Na caixa **nome,** escreva um nome único para o novo grupo de volume.
   2. Na caixa **de Aplicações,** selecione aplicações associadas aos volumes que irá adicionar ao grupo de volume.
      
       A caixa **de Aplicações** lista apenas as aplicações que utilizam volumes StorSimple e têm escritores VSS habilitados para eles. Um escritor VSS só é habilitado se todos os volumes que o escritor conhece forem volumes StorSimple. Se a caixa de Aplicações estiver vazia, então não são instaladas aplicações que utilizem volumes Azure StorSimple e tenham suportado os escritores VSS. (Atualmente, o Azure StorSimple suporta o Microsoft Exchange e o SQL Server.) Para obter mais informações sobre os escritores vsS, consulte [Integração com o Windows Volume Shadow Copy Service](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).
      
       Se selecionar uma aplicação, todos os volumes associados à mesmo são automaticamente selecionados. Inversamente, se selecionar volumes associados a uma aplicação específica, a aplicação é automaticamente selecionada na caixa de **Aplicações.** 
   3. Na caixa **Volumes,** selecione volumes StorSimple para adicionar ao grupo de volume. 
      
      * Pode incluir volumes com divisórias individuais ou múltiplas. (Vários volumes de partição podem ser discos dinâmicos ou discos básicos com múltiplas divisórias.) Um volume que contém múltiplas divisórias é tratado como uma única unidade. Consequentemente, se adicionar apenas uma das divisórias a um grupo de volume, todas as outras divisórias são adicionadas automaticamente a esse grupo de volume ao mesmo tempo. Depois de adicionar um volume de partição múltiplo a um grupo de volume, o volume de partição múltipla continua a ser tratado como uma única unidade.
      * Pode criar grupos de volume vazios, não atribuindo-lhes quaisquer volumes. 
      * Não misture volumes partilhados por cluster (CSVs) e não-CSVs no mesmo grupo de volume. O StorSimple Snapshot Manager não suporta uma mistura de volumes CSV e volumes não CSV no mesmo instantâneo.
4. Clique em **OK** para salvar o grupo de volume.

## <a name="back-up-a-volume-group"></a>Apoiar um grupo de volume
Utilize o seguinte procedimento para fazer um volume de volume.

#### <a name="to-back-up-a-volume-group"></a>Para apoiar um grupo de volume
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Scope,** expanda o nó dos **Grupos de Volume,** clique à direita num nome de grupo de volume e, em seguida, clique em **Take Backup**.
   
    ![Apoiar o grupo de volume imediatamente](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. Na caixa de diálogo **Take Backup,** selecione **Local Snapshot** ou **Cloud Snapshot**, e, em seguida, clique em **Criar**.
   
    ![Tome o diálogo de reserva](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. Para confirmar que a cópia de segurança está em execução, expanda o nó **Jobs** e, em seguida, clique em **Running**. O reforço deve estar listado.
5. Para ver o instantâneo completo, expanda o nó **do Catálogo de Cópias** de Segurança, expanda o nome do grupo de volume e, em seguida, clique em Local **Snapshot** ou **Cloud Snapshot**. O backup será listado se terminar com sucesso.

## <a name="edit-a-volume-group"></a>Editar um grupo de volume
Utilize o seguinte procedimento para editar um grupo de volume.

#### <a name="to-edit-a-volume-group"></a>Para editar um grupo de volume
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Scope,** expanda o nó dos **Grupos de Volume,** clique à direita num nome de grupo de volume e, em seguida, clique em **Editar**.
3. Aparece a caixa de diálogo **Criar um grupo de volumes.* Pode alterar as entradas **de Nome,** **Aplicações**e **Volumes.**
4. Clique em **OK** para guardar as alterações.

## <a name="delete-a-volume-group"></a>Eliminar um grupo de volume
Utilize o seguinte procedimento para eliminar um grupo de volume. 

> [!WARNING]
> Isto também elimina todas as cópias de segurança associadas ao grupo de volume.
> 
> 

#### <a name="to-delete-a-volume-group"></a>Para eliminar um grupo de volume
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Scope,** expanda o nó dos **Grupos de Volume,** clique à direita num nome de grupo de volume e, em seguida, clique em **Eliminar**.
3. Aparece a caixa de diálogo do **Grupo Delete Volume.** Digite **Confirmar** na caixa de texto e, em seguida, clique **OK**.
   
    O grupo de volume eliminado desaparece da lista no painel **de resultados** e todas as cópias de segurança associadas a esse grupo de volume são eliminadas do catálogo de cópias de segurança.

## <a name="next-steps"></a>Passos seguintes
* Aprenda a utilizar o [StorSimple Snapshot Manager para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Aprenda a usar o [StorSimple Snapshot Manager para criar e gerir políticas](storsimple-snapshot-manager-manage-backup-policies.md)de backup.

