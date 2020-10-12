---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d6bad1ec584b28fee77bc654eba8a2d0c7b5df30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184322"
---
#### <a name="to-add-a-storage-account-credential-in-the-same-azure-subscription-as-the-storsimple-device-manager-service"></a>Para adicionar uma credencial de conta de armazenamento na mesma subscrição do Azure que o serviço Gestor de Dispositivos do StorSimple

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Na secção **Configuração**, clique em **Credenciais da conta de armazenamento**.

    ![Credenciais da conta de armazenamento](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct1.png)

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

![Lista das credenciais da conta de armazenamento](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct6.png)

