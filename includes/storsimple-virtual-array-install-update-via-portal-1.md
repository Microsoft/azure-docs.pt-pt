---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: e64db93347794ccfc85252205c6d92a68e904635
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376129"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Para instalar atualizações através do portal do Azure

1. Aceda ao seu Gestor de Dispositivos do StorSimple e selecione **Dispositivos**. Na lista de dispositivos ligados ao seu serviço, selecione e clique no dispositivo que pretende atualizar.

    ![atualizar o dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. No painel **Definições** , clique em **Atualizações de dispositivos**.

    ![dispositivo de atualização 2](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. Verá uma mensagem se as atualizações de software estiverem disponíveis. Para procurar atualizações, também pode clicar em **Analisar**. Tome nota da versão de software que está a executar. 

    ![dispositivo de atualização 3](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate3m1.png)

    É notificado quando a varredura começa e termina com sucesso.

    ![dispositivo de atualização 4](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate5m.png)

4. Assim que as atualizações são analisadas, clique em **Transferir atualizações**.

    ![dispositivo de atualização 5](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate6m.png)

5. Na lâmina **de novas atualizações,** reveja as notas de lançamento. Note também que depois de as atualizações serem descarregadas, é necessário confirmar a instalação. Clique em **OK**.

    ![dispositivo de atualização 6](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate7m.png)

6. É notificado quando o carregamento iniciar e for concluído com êxito.

     ![dispositivo de atualização 7](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate8m.png)

5. No painel **Atualizações do dispositivo** , clique em **Instalar**.

     ![dispositivo de atualização 8](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate11m1.png)

6. No painel **Novas atualizações** , é avisado que a atualização é indesejada. Como a matriz virtual é um dispositivo de nó único, o dispositivo é reiniciado depois de ser atualizado. Isto afeta qualquer E/S em curso. Clique em **OK** para instalar as atualizações.

    ![dispositivo de atualização 9](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate12m.png)

7. Será notificado quando a tarefa de instalação é iniciada.

    ![dispositivo de atualização 10](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate13m.png)

8.  Assim que a instalação for concluída com êxito, clique na ligação **Ver Tarefa** no painel **Atualizações do dispositivo** , para monitorizar a instalação. 

    ![dispositivo de atualização 11](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate15m1.png)

    Esta ação leva-o à lâmina **'Atualizações de Instalação'.** Pode ver informações detalhadas sobre a tarefa aqui.

    ![dispositivo de atualização 12](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate16m1.png)

9. Se começou com uma versão virtual de software de execução Update 0.6 (10.0.10293.0), está agora a executar a Atualização 1 e está feito. Pode saltar os passos restantes. Se começou com uma matriz virtual a executar uma versão de software antes do Update 0.6 (10.0.10293.0), está agora atualizado para a Atualização 0.6. Vê outra mensagem indicando que as atualizações estão disponíveis. Repita os passos 4-8 para instalar a Atualização 1.

    ![dispositivo de atualização 13](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate17.png)

