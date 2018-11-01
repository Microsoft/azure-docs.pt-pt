---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8de46fee4f93fffa669dc96ec5bb84e36842a055
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165991"
---
#### <a name="to-delete-a-cloud-appliance"></a>Para eliminar uma aplicação da cloud

1. Inicie sessão no Portal do Azure.
2. Só é possível eliminar um dispositivo desativado que não contenha dados. Elimine primeiro os dados no dispositivo ou efetue a [ativação pós-falha dos dados](../articles/storsimple/storsimple-8000-device-failover-cloud-appliance.md) nos contentores de volumes para outro dispositivo. Depois de os dados serem eliminados, está pronto para desativar o dispositivo.
3. Na página do serviço do Gestor de Dispositivos do StorSimple, clique em **Dispositivos** e, em seguida, selecione o dispositivo. Clique com botão direito do rato e selecione **Desativar**.
4. Depois de o dispositivo ser desativado, clique com o botão direto do rato no dispositivo e selecione **Eliminar**.

    ![Selecionar o dispositivo desativado e clicar em eliminar](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance1.png)

5. Escreva o nome do dispositivo para confirmar a eliminação. Depois de o dispositivo ser eliminado, a lista de dispositivos é atualizada.

    ![Confirmar eliminação](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance2.png)

6. Será notificado depois de o dispositivo ser eliminado.

    ![Notificação de eliminação do dispositivo concluída com êxito](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance4.png)

7. A lista de dispositivos é atualizada para indicar o dispositivo eliminado.

    ![Lista atualizada de dispositivos](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance5.png)
