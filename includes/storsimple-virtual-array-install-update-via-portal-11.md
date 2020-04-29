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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67184783"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Para instalar atualizações através do portal do Azure

1. Aceda ao seu Gestor de Dispositivos do StorSimple e selecione **Dispositivos**. Na lista de dispositivos ligados ao seu serviço, selecione e clique no dispositivo que pretende atualizar.

2. Em **Definições,** clique em **atualizações do Dispositivo**.  

3. Verá uma mensagem se as atualizações de software estiverem disponíveis. Para procurar atualizações, também pode clicar em **Analisar**. Tome nota da versão de software que está a executar. 

    ![atualizar o dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate3m1.png)

    É notificado quando a varredura começa e completa com sucesso.
 
4. Assim que as atualizações são analisadas, clique em **Transferir atualizações**. Em **novas atualizações,** reveja as notas de lançamento. Note também que depois de serem descarregadas as atualizações, terá de confirmar a instalação. Clique em **OK**.

    ![atualizar o dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate6m.png)

    É notificado quando o carregamento iniciar e for concluído com êxito.

5. Nas **atualizações do Dispositivo,** clique em **Instalar**.

     ![atualizar o dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate11m1.png)

6. Em **novas atualizações,** é avisado que a atualização é disruptiva. Como a matriz virtual é um dispositivo de nó único, o dispositivo é reiniciado depois de ser atualizado. Isto afeta qualquer E/S em curso. Clique em **OK** para instalar as atualizações.

    ![atualizar o dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate12m.png)

    Será notificado quando a tarefa de instalação é iniciada.

7.  Depois de o trabalho de instalação terminar com sucesso, clique no link **'Ver Trabalho'.** Esta ação leva-o à lâmina **'Atualizações de instalação'.** Pode ver informações detalhadas sobre a tarefa aqui. 

    ![atualizar o dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate16m1.png)

8. Se começou com uma versão de software de execução virtual Update 1 (10.0.10296.0), está agora a executar o Update 1.1 e está feito. Pode saltar os passos restantes. 

    Se começou com uma versão de software de execução virtual Update 0.6 (10.0.10293.0), está agora atualizado para o Update 1.0. Vê outra mensagem indicando que as atualizações estão disponíveis. Repita os passos 4-7 para instalar a Atualização 1.1.

    

