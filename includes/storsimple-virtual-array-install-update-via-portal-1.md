---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 3129bbe171329ecf37f8712394cedf9b70188220
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005826"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Para instalar atualizações através do portal do Azure

1. Aceda ao seu Gestor de Dispositivos do StorSimple e selecione **Dispositivos**. Na lista de dispositivos ligados ao seu serviço, selecione e clique no dispositivo que pretende atualizar.

    ![Em Recente, o Gestor de Dispositivos MySSDevManager é destacado e selecionado, os Dispositivos são destacados e selecionados, e o dispositivo MYSSIS1103 é destacado e selecionado.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. No painel **Definições**, clique em **Atualizações de dispositivos**.

    ![São apresentadas informações para o MYSSIS1103. Nas Definições, as atualizações do dispositivo são realçadas.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. Verá uma mensagem se as atualizações de software estiverem disponíveis. Para procurar atualizações, também pode clicar em **Analisar**. Tome nota da versão de software que está a executar. 

    ![O painel de atualizações do Dispositivo diz que "Estão disponíveis novas atualizações", e "Última digitalização / 11/01/2017 10:56 AM / Versão de software / 10.0.10289.0". A versão está em destaque.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate3m1.png)

    É notificado quando a varredura começa e termina com sucesso.

    ![A notificação diz "Digitalizar atualizações para o dispositivo MySVAFS110.... 10:57 AM / A operação está em curso."](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate5m.png)

4. Assim que as atualizações são analisadas, clique em **Transferir atualizações**.

    ![No painel de atualizações do Dispositivo encontra-se a mensagem "Estão disponíveis novas atualizações". O botão de atualizações de Descarregamento está em destaque.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate6m.png)

5. Na lâmina **de novas atualizações,** reveja as notas de lançamento. Note também que depois de as atualizações serem descarregadas, é necessário confirmar a instalação. Clique em **OK**.

    ![O painel de novas atualizações diz que "Depois de as atualizações serem descarregadas, terá de confirmar a instalação". O botão OK é realçado.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate7m.png)

6. É notificado quando o carregamento iniciar e for concluído com êxito.

     ![A notificação diz "Descarregar atualizações para o dispositivo MySVAFS... 11h07".](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate8m.png)

5. No painel **Atualizações do dispositivo**, clique em **Instalar**.

     ![As atualizações do dispositivo dizem "Confirme a instalação de atualizações". O botão de instalação é realçado.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate11m1.png)

6. No painel **Novas atualizações**, é avisado que a atualização é indesejada. Como a matriz virtual é um dispositivo de nó único, o dispositivo é reiniciado depois de ser atualizado. Isto afeta qualquer E/S em curso. Clique em **OK** para instalar as atualizações.

    ![A mensagem no painel de novas atualizações é "O seu dispositivo terá um tempo de inatividade quando estas atualizações estiverem instaladas". O botão OK é realçado.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate12m.png)

7. Será notificado quando a tarefa de instalação é iniciada.

    ![A notificação diz "Instalar atualizações para o dispositivo MySVAFS110.... 11h09".](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate13m.png)

8.  Assim que a instalação for concluída com êxito, clique na ligação **Ver Tarefa** no painel **Atualizações do dispositivo**, para monitorizar a instalação. 

    ![No painel de atualizações do Dispositivo, existe um link marcado como "Instalação de atualizações. Ver Jó". O botão de instalação é realçado.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate15m1.png)

    Esta ação leva-o à lâmina **'Atualizações de Instalação'.** Pode ver informações detalhadas sobre a tarefa aqui.

    ![O painel de atualizações de instalação tem informações de instalação, incluindo dispositivo, estado, duração, hora de início e tempo de paragem.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate16m1.png)

9. Se começou com uma versão virtual de software de execução Update 0.6 (10.0.10293.0), está agora a executar a Atualização 1 e está feito. Pode saltar os passos restantes. Se começou com uma matriz virtual a executar uma versão de software antes do Update 0.6 (10.0.10293.0), está agora atualizado para a Atualização 0.6. Vê outra mensagem indicando que as atualizações estão disponíveis. Repita os passos 4-8 para instalar a Atualização 1.

    ![O painel de atualizações do Dispositivo diz que "Estão disponíveis novas atualizações", e "Última digitalização / 11/1/2017 10:56 AM / Versão de software / 10.0.10293.0".](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate17.png)

