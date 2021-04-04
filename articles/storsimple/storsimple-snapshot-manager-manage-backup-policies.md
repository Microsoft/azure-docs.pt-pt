---
title: Políticas de backup do StorSimple Snapshot Manager | Microsoft Docs
description: Descreve como utilizar o snap-in do StorSimple Snapshot Manager MMC para criar e gerir as políticas de backup que controlam as cópias de segurança programadas.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 745aeb9844f1abad075d34b34735563200ea618b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90054947"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>Use storSimple Snapshot Manager para criar e gerir políticas de backup
## <a name="overview"></a>Descrição Geral
Uma política de backup cria um calendário para fazer backup de dados de volume localmente ou na nuvem. Quando cria uma política de backup, também pode especificar uma política de retenção. (Pode reter um máximo de 64 instantâneos.) Para obter mais informações sobre as políticas de backup, consulte [os tipos de Backup](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies) na série [StorSimple 8000: uma solução de nuvem híbrida](storsimple-overview.md).

Este tutorial explica como:

* Criar uma política de cópias de segurança
* Editar uma política de backup
* Excluir uma política de backup

## <a name="create-a-backup-policy"></a>Criar uma política de cópias de segurança
Utilize o seguinte procedimento para criar uma nova política de backup.

#### <a name="to-create-a-backup-policy"></a>Para criar uma política de backup
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **de âmbito,** clique com o botão direito políticas de **backup** e clique em Criar Política **de Backup**.

    ![Criar uma política de cópias de segurança](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    Aparece a caixa de diálogo **Create a Policy.**

    ![Criar uma Política - Separador Geral](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. No separador **Geral,** complete as seguintes informações:

   1. Na caixa de texto **'Nome',** escreva um nome para a apólice.
   2. Na caixa de texto **do grupo Volume,** digite o nome do grupo de volume associado à política.
   3. Selecione **Snapshot Local** ou **Cloud Snapshot**.
   4. Selecione o número de instantâneos para reter. Se selecionar **Todos**, serão mantidos 64 instantâneos (o máximo).
4. Clique no separador **Agenda** .

    ![Criar uma Política - Separador agendar](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. No **separador Agendar,** preencha as seguintes informações:

   1. Clique na caixa de verificação **Enable** para agendar a próxima cópia de segurança.
   2. Em **Definições**, selecione **Uma vez,** **Diária,** **Semanal** **ou Mensal**.
   3. Na caixa de texto **Iniciar,** clique no ícone do calendário e selecione uma data de início.
   4. Em **Definições Avançadas,** pode definir horários de repetição opcionais e uma data de fim.
   5. Clique em **OK**.

Depois de criar uma política de backup, as seguintes informações aparecem no painel **de resultados:**

* **Nome** - o nome da política de backup.
* **Tipo** – instantâneo local ou instantâneo em nuvem.
* **Grupo volume** – o grupo de volume associado à política.
* **Retenção** – o número de instantâneos retidos; o máximo é de 64.
* **Criado** – a data em que esta política foi criada.
* **Ativado** – se a política está atualmente em vigor: **A verdade** indica que está em vigor; **Falso** indica que não está em vigor.

## <a name="edit-a-backup-policy"></a>Editar uma política de backup
Utilize o seguinte procedimento para editar uma política de backup existente.

#### <a name="to-edit-a-backup-policy"></a>Para editar uma política de backup
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Scope,** clique no nó **'Backup Policies'.** Todas as políticas de backup aparecem no painel **de resultados.**
3. Clique com o botão direito na política que pretende editar e, em seguida, clique em **Editar**.

    ![Editar uma política de backup](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. Quando aparecer a janela **Criar uma Política,** introduza as alterações e, em seguida, clique em **OK**.

## <a name="delete-a-backup-policy"></a>Excluir uma política de backup
Utilize o seguinte procedimento para eliminar uma política de backup.

#### <a name="to-delete-a-backup-policy"></a>Para eliminar uma política de backup
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Scope,** clique no nó **'Backup Policies'.** Todas as políticas de backup aparecem no painel **de resultados.**
3. Clique com o botão direito na política de backup que pretende eliminar e, em seguida, clique em **Eliminar**.
4. Quando a mensagem de confirmação aparecer, clique em **Sim**.

    ![Eliminar confirmação da política de backup](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba como [utilizar o StorSimple Snapshot Manager para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Saiba como [utilizar o StorSimple Snapshot Manager para visualizar e gerir trabalhos de backup.](storsimple-snapshot-manager-manage-backup-jobs.md)
