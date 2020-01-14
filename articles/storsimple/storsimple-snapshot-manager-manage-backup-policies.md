---
title: Políticas de backup do StorSimple Snapshot Manager | Microsoft Docs
description: Descreve como usar o snap-in do StorSimple Snapshot Manager MMC para criar e gerenciar as políticas de backup que controlam backups agendados.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 7b2df6c8384e78094d80a17d8982b0db9edf73e1
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933366"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>Usar o Snapshot Manager do StorSimple para criar e gerenciar políticas de backup
## <a name="overview"></a>Visão geral
Uma política de backup cria uma agenda para fazer backup de dados de volume localmente ou na nuvem. Ao criar uma política de backup, você também pode especificar uma política de retenção. (Você pode reter um máximo de 64 instantâneos). Para obter mais informações sobre políticas de backup, consulte [tipos de backup](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies) no [StorSimple 8000 Series: uma solução de nuvem híbrida](storsimple-overview.md).

Este tutorial explica como:

* Criar uma política de backup
* Editar uma política de backup
* Excluir uma política de backup

## <a name="create-a-backup-policy"></a>Criar uma política de backup
Use o procedimento a seguir para criar uma nova política de backup.

#### <a name="to-create-a-backup-policy"></a>Para criar uma política de backup
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No painel **escopo** , clique com o botão direito do mouse em **políticas de backup**e clique em **criar política de backup**.

    ![Criar uma política de backup](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    A caixa de diálogo **criar uma política** é exibida.

    ![Criar uma política-guia geral](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. Na guia **geral** , preencha as seguintes informações:

   1. Na caixa de texto **nome** , digite um nome para a política.
   2. Na caixa de texto **grupo de volumes** , digite o nome do grupo de volumes associado à política.
   3. Selecione **instantâneo local** ou **instantâneo de nuvem**.
   4. Selecione o número de instantâneos a serem retidos. Se você selecionar **tudo**, 64 instantâneos serão retidos (o máximo).
4. Clique no separador **Agenda** .

    ![Criar uma política – guia Agenda](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. Na guia **agenda** , preencha as seguintes informações:

   1. Clique na caixa de seleção **habilitar** para agendar o próximo backup.
   2. Em **configurações**, selecione **uma vez**, **diária**, **semanal**ou **mensal**.
   3. Na caixa de texto **Iniciar** , clique no ícone de calendário e selecione uma data de início.
   4. Em **Configurações avançadas**, você pode definir agendas de repetição opcionais e uma data de término.
   5. Clique em **OK**.

Depois de criar uma política de backup, as seguintes informações são exibidas no painel de **resultados** :

* **Nome** – o nome da política de backup.
* **Tipo** – instantâneo local ou instantâneo de nuvem.
* **Grupo de volumes** – o grupo de volumes associado à política.
* **Retenção** – o número de instantâneos retidos; o máximo é 64.
* **Criado** – a data em que essa política foi criada.
* **Habilitado** – se a política está atualmente em vigor: **true** indica que ela está em vigor; **False** indica que não está em vigor.

## <a name="edit-a-backup-policy"></a>Editar uma política de backup
Use o procedimento a seguir para editar uma política de backup existente.

#### <a name="to-edit-a-backup-policy"></a>Para editar uma política de backup
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No painel **escopo** , clique no nó **políticas de backup** . Todas as políticas de backup aparecem no painel de **resultados** .
3. Clique com o botão direito do mouse na política que você deseja editar e clique em **Editar**.

    ![Editar uma política de backup](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. Quando a janela **criar uma política** for exibida, insira suas alterações e clique em **OK**.

## <a name="delete-a-backup-policy"></a>Excluir uma política de backup
Use o procedimento a seguir para excluir uma política de backup.

#### <a name="to-delete-a-backup-policy"></a>Para excluir uma política de backup
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No painel **escopo** , clique no nó **políticas de backup** . Todas as políticas de backup aparecem no painel de **resultados** .
3. Clique com o botão direito do mouse na política de backup que você deseja excluir e clique em **excluir**.
4. Quando a mensagem de confirmação for exibida, clique em **Sim**.

    ![Excluir confirmação de política de backup](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba como [usar o snapshot Manager StorSimple para administrar sua solução storsimple](storsimple-snapshot-manager-admin.md).
* Saiba como [usar o snapshot Manager do StorSimple para exibir e gerenciar trabalhos de backup](storsimple-snapshot-manager-manage-backup-jobs.md).
