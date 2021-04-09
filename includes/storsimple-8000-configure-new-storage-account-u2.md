---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9f1c30b303bd1fe02e0685c7d848be92073ca2f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96016879"
---
#### <a name="to-add-a-storage-account-credential-in-the-same-azure-subscription-as-the-storsimple-device-manager-service"></a>Para adicionar uma credencial de conta de armazenamento na mesma subscrição do Azure que o serviço Gestor de Dispositivos do StorSimple

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Na secção **Configuração**, clique em **Credenciais da conta de armazenamento**.

    ![Vá para credenciais de conta de armazenamento](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct1.png)

2. No painel **Credenciais da conta de armazenamento**, clique em **+ Adicionar**.

    ![Adicionar uma credencial da conta de armazenamento](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct2.png)

3. No painel **Adicionar uma credencial de conta de armazenamento**, efetue os seguintes passos:

    1. À medida que está a adicionar uma credencial de conta de armazenamento na mesma subscrição do Azure que o seu serviço, certifique-se de que está selecionada **Atual**.

    2. Na lista pendente **conta de armazenamento**, selecione uma conta de armazenamento existente.

    3. Com base na conta de armazenamento selecionada, a **localização** será apresentada (indisponível e não pode ser alterada aqui).

    4. Selecione **Ativar Modo SSL** para criar um canal seguro para a comunicação de rede entre o seu dispositivo e a nuvem. Desative **Ativar SSL** apenas se estiver a utilizar uma cloud privada.

        ![Painel Adicionar credenciais da conta de armazenamento](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct3.png)

    5. Clique em **Adicionar** para iniciar a criação da tarefa para a credencial da conta de armazenamento. Será notificado quando a credencial da conta de armazenamento for criada com êxito.

        ![Notificação de êxito de credenciais da conta de armazenamento](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct5.png)

A credencial da conta de armazenamento criada recentemente será apresentada na lista de **Credenciais da conta de armazenamento**.

![Listar as credenciais de conta de armazenamento](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct6.png)

