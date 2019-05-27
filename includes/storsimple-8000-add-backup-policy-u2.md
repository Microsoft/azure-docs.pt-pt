---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 02274bacb66a33ef54e07bc8113d7db46d4d5296
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66172172"
---
#### <a name="to-add-a-storsimple-backup-policy"></a>Para adicionar uma política de cópias de segurança StorSimple

1. Aceda ao seu dispositivo StorSimple e clique em **Política de cópias de segurança**.

2. No painel **Política de cópias de segurança**, clique em **+ Adicionar política**, na barra de comandos.
   
    ![Adicionar uma política de cópias de segurança](./media/storsimple-8000-add-backup-policy-u2/addbupol1.png)

3. No painel **Criar política de cópias de segurança**, realize os passos seguintes:
   
   1. **Selecionar dispositivo** é preenchido automaticamente com base no dispositivo que selecionou.
   
   2. Especifique um **Nome para a política** de cópias de segurança que contenha entre três e 150 carateres. Não pode mudar o nome depois de a política estar criada.
       
   3. Para atribuir volumes a esta política de cópias de segurança, selecione **Adicionar volumes** e, na lista em tabela de volumes, clique na caixa ou caixas de verificação para atribuir um ou mais volumes.

       ![Adicionar uma política de cópias de segurança](./media/storsimple-8000-add-backup-policy-u2/addbupol2.png)

   4. Para definir uma agenda para esta política de cópias de segurança, clique em **Primeiro agendamento** e, em seguida, modifique os parâmetros seguintes:

       ![Adicionar uma política de cópias de segurança](./media/storsimple-8000-add-backup-policy-u2/addbupol3.png)

       1. Em **Tipo de instantâneo**, selecione **Cloud** ou **Local**.

       2. Indique a frequência das cópias de segurança (especifique um número e, em seguida, selecione **Dias** ou **Semanas** na lista pendente).

       3. Introduza um agendamento de retenção.

       4. Introduza a hora e data de início da política de cópias de segurança.

       5. Clique em **OK** para definir a agenda.

   5. Clique em **Criar** para criar uma política de cópias de segurança.

       ![Adicionar uma política de cópias de segurança](./media/storsimple-8000-add-backup-policy-u2/addbupol4.png)
   
   6. Será notificado quando a política de cópias de segurança for criada. A política recentemente adicionada é apresentada na vista de tabela no painel **Política de Cópias de Segurança**.

       ![Adicionar uma política de cópias de segurança](./media/storsimple-8000-add-backup-policy-u2/addbupol7.png)

