---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 48a3326dbe0e9eed4a5490e720248555586d189c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184311"
---
### <a name="to-take-a-backup"></a>Para efetuar uma cópia de segurança

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. A partir da lista tabular de dispositivos, selecione e clique no seu dispositivo e, em seguida, clique em **Todas as definições**. No painel **Definições**, aceda a **Definições > Gerir > Política de cópias de segurança**.

    ![Adicionar-política-cópia-de-segurança](./media/storsimple-8000-take-backup/step8takebu1.png)

2. No painel **Política de cópias de segurança**, clique em **+ Adicionar política**.

    ![Adicionar-política-cópia-de-segurança](./media/storsimple-8000-take-backup/step8takebu2.png)

3. No painel **Criar política de cópias de segurança**, escreva um nome que contenha entre 3 e 150 carateres para a sua política de cópia de segurança.

4. Selecione os volumes cuja cópia de segurança efetuar. Se selecionar mais do que um volume, esses volumes são agrupados para criar uma cópia de segurança consistente de falhas.

    ![Adicionar-política-cópia-de-segurança](./media/storsimple-8000-take-backup/step8takebu4.png)

5. No painel **Adicionar primeira agenda**:

    1. Selecione o tipo de cópia de segurança. Para obter restauros mais rápidos, selecione **Local** snapshot. Para obter resiliência de dados, selecione **Cloud** snapshot.
    2. Especifique a frequência da cópia de segurança em minutos, horas, dias ou semanas.
    3. Selecione um tempo de retenção. As opções de período de retenção dependem da frequência da cópia de segurança. Por exemplo, para uma política diária, o período de retenção pode ser especificado em semanas, ao passo que o período de retenção para uma política mensal é em meses.
    4. Selecione a hora e data de início para a política de cópia de segurança.
    5. Clique em **OK** para criar a política de cópias de segurança.

        ![Adicionar-política-cópia-de-segurança](./media/storsimple-8000-take-backup/step8takebu5.png) 

6. Clique em **Criar** para iniciar a criação da política de cópias de segurança. Será notificado quando a política de cópias de segurança for criada com êxito. A lista de políticas de cópias de segurança também é atualizada.
      
      ![Adicionar-política-cópia-de-segurança](./media/storsimple-8000-take-backup/step8takebu9.png)
      
      Tem agora uma política de cópias de segurança que cria as cópias de segurança agendadas dos dados do seu volume.




