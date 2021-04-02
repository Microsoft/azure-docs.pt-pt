---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 48ce594fa5f4b736e69b5b4ef7a10011fe8031fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96028259"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Para instalar atualizações através do portal do Azure

1. Aceda ao seu Gestor de Dispositivos do StorSimple e selecione **Dispositivos**. Na lista de dispositivos ligados ao seu serviço, selecione e clique no dispositivo que pretende atualizar. 

    ![Em Recente, o Gestor de Dispositivos MySSDevManager é destacado e selecionado, os Dispositivos são destacados e selecionados, e o dispositivo MYSSIS1103 é destacado e selecionado.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. No painel **Definições**, clique em **Atualizações de dispositivos**. 

    ![São apresentadas informações para o MYSSIS1103. Nas Definições, as atualizações do dispositivo são realçadas.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. Verá uma mensagem se as atualizações de software estiverem disponíveis. Para procurar atualizações, também pode clicar em **Analisar**.

    ![O painel de atualizações do Dispositivo diz que "Estão disponíveis novas atualizações", e "Última digitalização / 18/01/2017 14:42 PM / Versão de software / 10.0.10288.0". A versão está em destaque.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate3m1.png)

    Será notificado quando a análise iniciar e for concluída com êxito.

    ![A notificação diz "Digitalizar atualizações para o dispositivo MySSIS1103. 14:12 / Concluir com sucesso a operação."](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate5m.png)

4. Assim que as atualizações são analisadas, clique em **Transferir atualizações**. 

    ![No painel de atualizações do Dispositivo encontra-se a mensagem "Estão disponíveis novas atualizações". O botão de atualizações de Descarregamento está em destaque.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate6m.png)

5. No painel **Novas atualizações**, consulte as informações que são transferidas após as atualizações, terá de confirmar a instalação. Clique em **OK**.

    ![O painel de novas atualizações diz que "Depois de as atualizações serem descarregadas, terá de confirmar a instalação". O botão OK é realçado.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate7m.png)

6. É notificado quando o carregamento iniciar e for concluído com êxito.

     ![A notificação diz "Descarregar atualizações para o dispositivo MySSIS1... 14h13..](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate8m.png)

5. No painel **Atualizações do dispositivo**, clique em **Instalar**.

     ![As atualizações do dispositivo dizem "Confirme a instalação de atualizações". Existe um botão de instalação.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate11m1.png)   

6. No painel **Novas atualizações**, é avisado que a atualização é indesejada. Como a matriz virtual é um dispositivo de nó único, o dispositivo é reiniciado depois de ser atualizado. Isto afeta qualquer E/S em curso. Clique em **OK** para instalar as atualizações. 

    ![A mensagem no painel de novas atualizações é "O seu dispositivo terá um tempo de inatividade quando estas atualizações estiverem instaladas". O botão OK é realçado.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate12m.png) 

7. Será notificado quando a tarefa de instalação é iniciada. 

    ![A notificação diz "Instalar atualizações para o dispositivo MySSIS1103. 14h15".](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate13m.png)

8.  Assim que a instalação for concluída com êxito, clique na ligação **Ver Tarefa** no painel **Atualizações do dispositivo**, para monitorizar a instalação. 

    ![No painel de atualizações do Dispositivo, existe um link marcado como "Instalação de atualizações. Ver Jó".](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate15m1.png)

    Esta ação irá encaminhá-lo para o painel **Atualizações de Instalação**. Pode ver informações detalhadas sobre a tarefa aqui.

    ![O painel de atualizações de instalação tem informações de instalação, incluindo dispositivo, estado, duração, hora de início e tempo de paragem.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate16m1.png)

9. Após as atualizações serem instaladas com êxito, verá uma mensagem com estas indicações no painel **Atualizações do dispositivo**. 
