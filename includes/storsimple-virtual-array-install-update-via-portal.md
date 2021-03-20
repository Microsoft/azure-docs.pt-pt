---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: df572b88cf8a67163b28ec87154dcea01646b9a2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95561271"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Para instalar atualizações através do portal do Azure

1. Aceda ao seu Gestor de Dispositivos do StorSimple e selecione **Dispositivos**. Na lista de dispositivos ligados ao seu serviço, selecione e clique no dispositivo que pretende atualizar. 

    ![Em Recente, o Gestor de Dispositivos MySSDevManager é destacado e selecionado, os Dispositivos são destacados e selecionados, e o dispositivo MYSSIS1103 é destacado e selecionado.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate1m.png) 

2. No painel **Definições**, clique em **Atualizações de dispositivos**. 

    ![Informações para o MYSSIS1103 aparecem. Nas Definições, as atualizações do dispositivo são realçadas.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate2m.png)  

3. Verá uma mensagem se as atualizações de software estiverem disponíveis. Para procurar atualizações, também pode clicar em **Analisar**.

    ![No painel de atualizações do Dispositivo é realçado o botão Descamada. Há uma mensagem de quatro linhas: Última digitalização / Não digitalizada / Versão de software / 10.0.10280.0.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate3m.png)

    Será notificado quando a análise iniciar e for concluída com êxito.

    ![A notificação diz "A verificação de atualizações para o dispositivo MySSIS11103.2:12 PM / Concluído com sucesso a operação".](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate5m.png)

4. Assim que as atualizações são analisadas, clique em **Transferir atualizações**. 

    ![O painel de atualizações do Dispositivo diz que "Estão disponíveis novas atualizações", e "Última digitalização / 11/3/2016 14:12 / Versão de software / 10.0.10280.0". A versão está em destaque.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate6m.png)

5. No painel **Novas atualizações**, consulte as informações que são transferidas após as atualizações, terá de confirmar a instalação. Clique em **OK**.

    ![O painel de novas atualizações diz que "Depois de as atualizações serem descarregadas, terá de confirmar a instalação". O botão OK é realçado.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate7m.png)

6. É notificado quando o carregamento iniciar e for concluído com êxito.

     ![A notificação diz "Descarregar atualizações para o dispositivo MySSIS... 14h13..](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate8m.png)

5. No painel **Atualizações do dispositivo**, clique em **Instalar**.

     ![O painel de atualizações do Dispositivo mostra a mensagem "Confirmar atualizações de instalação". O botão de instalação é realçado.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate11m.png)   

6. No painel **Novas atualizações**, é avisado que a atualização é indesejada. Como a matriz virtual é um dispositivo de nó único, o dispositivo é reiniciado depois de ser atualizado. Isto afeta qualquer E/S em curso. Clique em **OK** para instalar as atualizações. 

    ![A mensagem no painel de novas atualizações é "O seu dispositivo terá um tempo de inatividade quando estas atualizações estiverem instaladas". O botão OK é realçado.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate12m.png) 

7. Será notificado quando a tarefa de instalação é iniciada. 

    ![A notificação diz "Instalar atualizações para o dispositivo MYSSIS1103". 14h15.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate13m.png)

8.  Assim que a instalação for concluída com êxito, clique na ligação **Ver Tarefa** no painel **Atualizações do dispositivo**, para monitorizar a instalação. 

    ![No painel de atualizações do Dispositivo, o link rotulou "Instalar atualizações. View Job" destaca-se.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate15m.png)

    Esta ação irá encaminhá-lo para o painel **Atualizações de Instalação**. Pode ver informações detalhadas sobre a tarefa aqui.

    ![O painel de atualizações de instalação tem informações de instalação, incluindo dispositivo, estado, duração, hora de início e tempo de paragem.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate16m.png)

9. Após as atualizações serem instaladas com êxito, verá uma mensagem com estas indicações no painel Atualizações do dispositivo. A versão do software também muda para **10.0.10288.0**. 

    ![O painel de atualizações do Dispositivo mostra "O seu dispositivo está atualizado" e fornece a versão do software (10.0.10288.0).](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate17m.png)
