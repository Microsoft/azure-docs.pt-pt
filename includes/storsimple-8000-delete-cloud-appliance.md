---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: ac708eb2ac79a74b8f4e09a7306a42665b3aca94
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184316"
---
#### <a name="to-delete-a-cloud-appliance"></a>Para eliminar uma aplicação da cloud

1. Inicie sessão no Portal do Azure.
2. Só é possível eliminar um dispositivo desativado que não contenha dados. Elimine primeiro os dados no dispositivo ou efetue a [ativação pós-falha dos dados](../articles/storsimple/storsimple-8000-device-failover-cloud-appliance.md) nos contentores de volumes para outro dispositivo. Depois de os dados serem eliminados, está pronto para desativar o dispositivo.
3. Na sua página de serviço StorSimple Device Manager, clique em **Dispositivos** e, em seguida, selecione o dispositivo. Clique com botão direito do rato e selecione **Desativar**.
4. Depois de o dispositivo ser desativado, clique com o botão direto do rato no dispositivo e selecione **Eliminar**.

    ![Selecionar o dispositivo desativado e clicar em eliminar](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance1.png)

5. Escreva o nome do dispositivo para confirmar a eliminação. Depois de o dispositivo ser eliminado, a lista de dispositivos é atualizada.

    ![Confirmar eliminação](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance2.png)

6. Será notificado depois de o dispositivo ser eliminado.

    ![Notificação de eliminação do dispositivo concluída com êxito](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance4.png)

7. A lista de dispositivos é atualizada para indicar o dispositivo eliminado.

    ![Lista atualizada de dispositivos](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance5.png)
