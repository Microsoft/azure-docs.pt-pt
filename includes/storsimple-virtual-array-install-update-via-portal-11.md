---
title: incluir ficheiro
description: incluir ficheiro
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 07/18/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 65d5a88f7b5d059deb633f062639e455c64ef2f7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66167074"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Para instalar atualizações através do portal do Azure

1. Aceda ao seu Gestor de Dispositivos do StorSimple e selecione **Dispositivos**. Na lista de dispositivos ligados ao seu serviço, selecione e clique no dispositivo que pretende atualizar.

2. Sob **configurações**, clique em **atualizações do dispositivo**.  

3. Verá uma mensagem se as atualizações de software estiverem disponíveis. Para procurar atualizações, também pode clicar em **Analisar**. Tome nota da versão do software que está a executar. 

    ![atualizar o dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate3m1.png)

    Será notificado quando a análise é iniciada e concluída com êxito.
 
4. Assim que as atualizações são analisadas, clique em **Transferir atualizações**. Sob **novas atualizações**, reveja as notas de versão. Tenha também em atenção que depois das atualizações são transferidas, terá de confirmar a instalação. Clique em **OK**.

    ![atualizar o dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate6m.png)

    É notificado quando o carregamento iniciar e for concluído com êxito.

5. Sob **atualizações do dispositivo**, clique em **instalar**.

     ![atualizar o dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate11m1.png)

6. Sob **novas atualizações**, é avisado que a atualização é indesejada. Como a matriz virtual é um dispositivo de nó único, o dispositivo é reiniciado depois de ser atualizado. Isto afeta qualquer E/S em curso. Clique em **OK** para instalar as atualizações.

    ![atualizar o dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate12m.png)

    Será notificado quando a tarefa de instalação é iniciada.

7.  Após a instalação for concluída com êxito, clique em **ver tarefa** ligação. Esta ação leva-o para o **instalar atualizações** painel. Pode ver informações detalhadas sobre a tarefa aqui. 

    ![atualizar o dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate16m1.png)

8. Se usar uma matriz virtual a executar a versão de software Update 1 (10.0.10296.0), agora estão a executar o Update 1.1 e são feitas. Pode ignorar os passos restantes. 

    Se usar uma matriz virtual a executar a versão de software atualização 0.6 (10.0.10293.0), agora são atualizados para a atualização 1.0. Veja outra mensagem indicando que atualizações estão disponíveis. Repita os passos 4 a 7 para instalar a atualização 1.1.

    

