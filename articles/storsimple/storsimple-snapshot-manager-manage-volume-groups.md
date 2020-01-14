---
title: StorSimple Snapshot Manager grupos de volumes | Microsoft Docs
description: Descreve como usar o snap-in do StorSimple Snapshot Manager MMC para criar e gerenciar grupos de volumes.
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
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75931499"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Usar o Snapshot Manager do StorSimple para criar e gerenciar grupos de volumes
## <a name="overview"></a>Visão geral
Você pode usar o nó **grupos de volumes** no painel **escopo** para atribuir volumes a grupos de volumes, exibir informações sobre um grupo de volumes, agendar backups e editar grupos de volumes.

Grupos de volumes são pools de volumes relacionados usados para garantir que os backups sejam consistentes com o aplicativo. Para obter mais informações, consulte [volumes e grupos de volume](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) e [integração com o Windows serviço de cópias de sombra de volume](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

> [!IMPORTANT]
> * Todos os volumes em um grupo de volumes devem vir de um único provedor de serviços de nuvem.
> * Ao configurar grupos de volumes, não misture volumes compartilhados de cluster (CSVs) e não CSVs no mesmo grupo de volumes. O StorSimple Snapshot Manager não dá suporte a uma combinação de CSVs e não CSVs no mesmo instantâneo.

![Nó grupos de volumes](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Figura 1: nó de grupos de volume do StorSimple Snapshot Manager** 

Este tutorial explica como você pode usar o Snapshot Manager do StorSimple para:

* Exibir informações sobre seus grupos de volumes
* Criar um grupo de volumes
* Fazer backup de um grupo de volumes
* Editar um grupo de volumes
* Excluir um grupo de volumes

Todas essas ações também estão disponíveis no painel **ações** .

## <a name="view-volume-groups"></a>Exibir grupos de volumes
Se você clicar no nó **grupos de volumes** , o painel **resultados** mostrará as seguintes informações sobre cada grupo de volumes, dependendo das seleções de coluna feitas. (As colunas no painel de **resultados** são configuráveis. Clique com o botão direito do mouse no nó **volumes** , selecione **Exibir**e, em seguida, selecione **Adicionar/remover colunas**.)

| Coluna de resultados | Descrição |
|:--- |:--- |
| Nome |A coluna **nome** contém o nome do grupo de volumes. |
| Candidatura |A coluna **aplicativos** mostra o número de gravadores VSS atualmente instalados e em execução no host do Windows. |
| Selecionado |A coluna **selecionada** mostra o número de volumes contidos no grupo de volumes. Um zero (0) indica que nenhum aplicativo está associado aos volumes no grupo de volumes. |
| Importado |A coluna **importada** mostra o número de volumes importados. Quando definido como **true**, essa coluna indica que um grupo de volumes foi importado do portal do Azure e não foi criado no StorSimple snapshot Manager. |

> [!NOTE]
> Os grupos de volumes do StorSimple Snapshot Manager também são exibidos na guia **políticas de backup** na portal do Azure.
> 
> 

## <a name="create-a-volume-group"></a>Criar um grupo de volumes
Use o procedimento a seguir para criar um grupo de volumes.

#### <a name="to-create-a-volume-group"></a>Para criar um grupo de volumes
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No painel **escopo** , clique com o botão direito do mouse em **grupos de volumes**e clique em **Criar grupo de volumes**.
   
    ![Criar grupo de volumes](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    A caixa de diálogo **criar um grupo de volumes** é exibida.
   
    ![Caixa de diálogo criar um grupo de volumes](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. Introduza as seguintes informações:
   
   1. Na caixa **nome** , digite um nome exclusivo para o novo grupo de volumes.
   2. Na caixa **aplicativos** , selecione os aplicativos associados aos volumes que serão adicionados ao grupo de volumes.
      
       A caixa **aplicativos** lista somente os aplicativos que usam volumes do StorSimple e têm gravadores VSS habilitados para eles. Um gravador VSS será habilitado somente se todos os volumes dos quais o gravador estiver ciente forem volumes do StorSimple. Se a caixa aplicativos estiver vazia, nenhum aplicativo que use os volumes do Azure StorSimple e os gravadores VSS com suporte serão instalados. (Atualmente, o Azure StorSimple dá suporte ao Microsoft Exchange e SQL Server.) Para obter mais informações sobre gravadores VSS, consulte [integração com o Windows serviço de cópias de sombra de volume](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).
      
       Se você selecionar um aplicativo, todos os volumes associados a ele serão selecionados automaticamente. Por outro lado, se você selecionar volumes associados a um aplicativo específico, o aplicativo será selecionado automaticamente na caixa **aplicativos** . 
   3. Na caixa **volumes** , selecione volumes do StorSimple para adicionar ao grupo de volumes. 
      
      * Você pode incluir volumes com uma ou várias partições. (Vários volumes de partição podem ser discos dinâmicos ou discos básicos com várias partições.) Um volume que contém várias partições é tratado como uma única unidade. Consequentemente, se você adicionar apenas uma das partições a um grupo de volumes, todas as outras partições serão adicionadas automaticamente a esse grupo de volumes ao mesmo tempo. Depois de adicionar um volume de várias partições a um grupo de volumes, o volume de várias partições continua a ser tratado como uma única unidade.
      * Você pode criar grupos de volumes vazios não atribuindo volumes a eles. 
      * Não misture volumes compartilhados de cluster (CSVs) e não CSVs no mesmo grupo de volumes. O StorSimple Snapshot Manager não dá suporte a uma combinação de volumes CSV e volumes não CSV no mesmo instantâneo.
4. Clique em **OK** para salvar o grupo de volumes.

## <a name="back-up-a-volume-group"></a>Fazer backup de um grupo de volumes
Use o procedimento a seguir para fazer backup de um grupo de volumes.

#### <a name="to-back-up-a-volume-group"></a>Para fazer backup de um grupo de volumes
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No painel **escopo** , expanda o nó **grupos de volumes** , clique com o botão direito do mouse em um nome de grupo de volumes e clique em **fazer backup**.
   
    ![Fazer backup do grupo de volumes imediatamente](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. Na caixa de diálogo **fazer backup** , selecione **instantâneo local** ou **instantâneo de nuvem**e, em seguida, clique em **criar**.
   
    ![Caixa de diálogo fazer backup](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. Para confirmar que o backup está em execução, expanda o nó **trabalhos** e clique em **executando**. O backup deve ser listado.
5. Para exibir o instantâneo concluído, expanda o nó **Catálogo de backup** , expanda o nome do grupo de volumes e clique em instantâneo **local** ou **instantâneo de nuvem**. O backup será listado se for concluído com êxito.

## <a name="edit-a-volume-group"></a>Editar um grupo de volumes
Use o procedimento a seguir para editar um grupo de volumes.

#### <a name="to-edit-a-volume-group"></a>Para editar um grupo de volumes
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No painel **escopo** , expanda o nó **grupos de volumes** , clique com o botão direito do mouse em um nome de grupo de volumes e clique em **Editar**.
3. A caixa de diálogo * * criar um grupo de volumes * * é exibida. Você pode alterar o **nome**, os **aplicativos**e as entradas de **volumes** .
4. Clique em **OK** para guardar as alterações.

## <a name="delete-a-volume-group"></a>Excluir um grupo de volumes
Use o procedimento a seguir para excluir um grupo de volumes. 

> [!WARNING]
> Isso também exclui todos os backups associados ao grupo de volumes.
> 
> 

#### <a name="to-delete-a-volume-group"></a>Para excluir um grupo de volumes
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No painel **escopo** , expanda o nó **grupos de volumes** , clique com o botão direito do mouse em um nome de grupo de volumes e clique em **excluir**.
3. A caixa de diálogo **excluir grupo de volumes** é exibida. Digite **Confirm** na caixa de texto e clique em **OK**.
   
    O grupo de volumes excluído desaparece da lista no painel de **resultados** e todos os backups associados a esse grupo de volumes são excluídos do catálogo de backup.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [usar o snapshot Manager StorSimple para administrar sua solução storsimple](storsimple-snapshot-manager-admin.md).
* Saiba como [usar o snapshot Manager do StorSimple para criar e gerenciar políticas de backup](storsimple-snapshot-manager-manage-backup-policies.md).

