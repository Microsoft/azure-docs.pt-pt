---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 23ce17844a0113f63931c6ece7d36bfefedc2de5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100552273"
---
#### <a name="to-add-a-storsimple-backup-policy"></a>Para adicionar uma política de cópias de segurança StorSimple

1. Aceda ao seu dispositivo StorSimple e clique em **Política de cópias de segurança**.

2. No painel **Política de cópias de segurança**, clique em **+ Adicionar política**, na barra de comandos.
   
    ![Adicionar uma política de cópias de segurança](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-01.png)

3. No painel **Criar política de cópias de segurança**, realize os passos seguintes:
   
   1. **Selecionar dispositivo** é preenchido automaticamente com base no dispositivo que selecionou.
   
   2. Especifique um **nome de segurança Nome da política** que tem de 3 a 150 caracteres. Não pode mudar o nome depois de a política estar criada.
       
   3. Para atribuir volumes a esta política de cópias de segurança, selecione **Adicionar volumes** e, na lista em tabela de volumes, clique na caixa ou caixas de verificação para atribuir um ou mais volumes.

       ![Adicionar uma política de cópias de segurança 2](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-02.png)<!--Replacement screen source: create-backup-policy-addvolumes.png-->

   4. Para definir uma agenda para esta política de cópias de segurança, clique em **Primeiro agendamento** e, em seguida, modifique os parâmetros seguintes:<!--Do the substeps remain the same? Can they follow without a screenshot?-->

       ![Adicione uma política de backup 3](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-03.png)<!--Replacement screen source: create-backup-policy-first-schedule.png-->

       1. Em **Tipo de instantâneo**, selecione **Cloud** ou **Local**.

       2. Indicar a frequência das cópias de segurança (especifique um número e, em seguida, escolha **Days** ou **Weeks** da lista de drop-down.

       3. Introduza um agendamento de retenção.

       4. Introduza a hora e data de início da política de cópias de segurança.

       5. Clique em **OK** para definir a agenda.

   5. Clique em **Criar** para criar uma política de cópias de segurança.
   
   6. Será notificado quando a política de cópias de segurança for criada. A política recentemente adicionada é apresentada na vista de tabela no painel **Política de Cópias de Segurança**.

       ![Adicione uma política de backup 5](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-07.png)
