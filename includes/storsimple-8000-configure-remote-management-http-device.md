---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 473bc0a58fe49c7f454c81402b57ddce7fc745b2
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159698"
---
#### <a name="to-configure-remote-management-on-cloud-appliance"></a>Para configurar a gestão remota na aplicação da cloud

1. No serviço Gestor de Dispositivos do StorSimple, clique em **Dispositivos**. Selecione e clique na sua aplicação da cloud a partir da lista de dispositivos ligados ao serviço.
    ![Aplicação da cloud selecionada pelo StorSimple](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage1.png)

2. Aceda a **Definições > Segurança** para abrir o painel **Definições de segurança**.

     ![Definições de segurança do StorSimple](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage2.png)

3. Aceda à secção **Gestão Remota**. Clique na caixa **Gestão remota**.
     ![Gestão remota do StorSimple](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage3.png)

4. No painel **Gestão remota**:

    1. Certifique-se de que **Ativar administração remota** está ativada.
    2. A predefinição é ligar através de HTTPS. Pode optar por ligar através de HTTP. A ligação através de HTTP é aceitável apenas em redes fidedignas. Certifique-se de que o HTTP está ativado.
    3. Na barra de comando da parte superior do painel, clique em **... Mais** e, em seguida, clique em **Transferir certificado** para transferir um certificado de gestão remota. Pode especificar uma localização para guardar este ficheiro. Este certificado deve ser instalado no computador cliente ou anfitrião que irá utilizar para estabelecer ligação com a aplicação da cloud.

        ![Painel de gestão remota](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage4.png)
5. Clique em **Guardar** e quando lhe for pedido, confirme as alterações.