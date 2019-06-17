---
title: Políticas de cópia de segurança do Snapshot Manager do StorSimple | Documentos da Microsoft
description: Descreve como utilizar o snap-in do MMC do Snapshot Manager do StorSimple para criar e gerir as políticas de cópia de segurança que controlam as cópias de segurança agendadas.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 7dac26b058b959011e38b4373369b8a1115d2705
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64687284"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>Utilizar o Snapshot Manager do StorSimple para criar e gerir políticas de cópia de segurança
## <a name="overview"></a>Descrição geral
Uma política de cópia de segurança cria uma agenda de cópias de segurança de dados de volumes localmente ou na cloud. Quando cria uma política de cópia de segurança, também pode especificar uma política de retenção. (Pode manter um máximo de 64 instantâneos). Para obter mais informações sobre as políticas de cópia de segurança, consulte [tipos de cópia de segurança](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies) na [série StorSimple 8000: uma solução de cloud híbrida](storsimple-overview.md).

Este tutorial explica como:

* Criar uma política de cópia de segurança
* Editar uma política de cópia de segurança
* Eliminar uma política de cópia de segurança

## <a name="create-a-backup-policy"></a>Criar uma política de cópia de segurança
Utilize o procedimento seguinte para criar uma nova política de cópia de segurança.

#### <a name="to-create-a-backup-policy"></a>Para criar uma política de cópia de segurança
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. Na **âmbito** painel, clique com botão direito **políticas de cópia de segurança**e clique em **criar política de cópia de segurança**.

    ![Criar uma política de cópia de segurança](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    O **criar uma política** é apresentada a caixa de diálogo.

    ![Criar uma política - separador Geral](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. Sobre o **gerais** separador, conclua as seguintes informações:

   1. Na **nome** caixa de texto, escreva um nome para a política.
   2. Na **grupo de volumes** caixa de texto, escreva o nome do grupo de volumes associados à política.
   3. Selecione **instantâneo Local** ou **instantâneo de Cloud**.
   4. Selecione o número de instantâneos a manter. Se selecionou **todos os**, 64 instantâneos irão ser retidos (o máximo).
4. Clique nas **agenda** separador.

    ![Criar uma política - separador Agenda](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. Sobre o **agenda** separador, conclua as seguintes informações:

   1. Clique nas **ativar** caixa de verificação para agendar a cópia de segurança seguinte.
   2. Sob **configurações**, selecione **uma vez**, **diária**, **semanal**, ou **mensal**.
   3. Na **iniciar** caixa de texto, clique no ícone de calendário e selecione uma data de início.
   4. Sob **definições avançadas**, pode definir agendas de repetição opcionais e uma data de fim.
   5. Clique em **OK**.

Depois de criar uma política de cópia de segurança, as seguintes informações aparecem no **resultados** painel:

* **Nome** – o nome da política de cópia de segurança.
* **Tipo de** – instantâneo local ou um instantâneo de cloud.
* **Grupo de volumes** – o grupo de volumes associado à política.
* **Retenção** – o número de instantâneos retidos; o máximo são 64.
* **Criado** – a data em que esta política foi criada.
* **Ativado** – se a política está atualmente em vigor: **Verdadeiro** indica que está em vigor; **False** indica que não está em vigor.

## <a name="edit-a-backup-policy"></a>Editar uma política de cópia de segurança
Utilize o procedimento seguinte para editar uma política de cópia de segurança existente.

#### <a name="to-edit-a-backup-policy"></a>Para editar uma política de cópia de segurança
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. Na **âmbito** painel, clique nas **políticas de cópia de segurança** nó. Todas as políticas de cópia de segurança são apresentados no **resultados** painel.
3. Com o botão direito a política que pretende editar e, em seguida, clique em **editar**.

    ![Editar uma política de cópia de segurança](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. Quando o **criar uma política** janela for exibida, introduza as suas alterações e, em seguida, clique em **OK**.

## <a name="delete-a-backup-policy"></a>Eliminar uma política de cópia de segurança
Utilize o procedimento seguinte para eliminar uma política de cópia de segurança.

#### <a name="to-delete-a-backup-policy"></a>Para eliminar uma política de cópia de segurança
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. Na **âmbito** painel, clique nas **políticas de cópia de segurança** nó. Todas as políticas de cópia de segurança são apresentados no **resultados** painel.
3. A política de cópia de segurança que pretende eliminar e, em seguida, clique com o botão direito **eliminar**.
4. Quando aparecer a mensagem de confirmação, clique em **Sim**.

    ![Eliminar confirmação da política de cópia de segurança](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Passos Seguintes
* Saiba como [utilizar o Snapshot Manager do StorSimple para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Saiba como [utilizar o Snapshot Manager do StorSimple para ver e gerir tarefas de cópia de segurança](storsimple-snapshot-manager-manage-backup-jobs.md).
