---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 07/18/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 65d5a88f7b5d059deb633f062639e455c64ef2f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184783"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Para instalar atualizações através do portal do Azure

1. Aceda ao seu Gestor de Dispositivos do StorSimple e selecione **Dispositivos**. Na lista de dispositivos ligados ao seu serviço, selecione e clique no dispositivo que pretende atualizar.

2. Em **Definições**, clique em **atualizações do dispositivo**.  

3. Verá uma mensagem se as atualizações de software estiverem disponíveis. Para procurar atualizações, também pode clicar em **Analisar**. Tome nota da versão de software que está a executar. 

    ![atualizar o dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate3m1.png)

    É notificado quando a varredura começa e termina com sucesso.
 
4. Assim que as atualizações são analisadas, clique em **Transferir atualizações**. Em **Novas atualizações,** reveja as notas de lançamento. Note também que depois de as atualizações serem descarregadas, é necessário confirmar a instalação. Clique em **OK**.

    ![atualizar o dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate6m.png)

    É notificado quando o carregamento iniciar e for concluído com êxito.

5. Nas **atualizações do Dispositivo,** clique em **Instalar**.

     ![atualizar o dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate11m1.png)

6. Sob **novas atualizações,** é avisado que a atualização é disruptiva. Como a matriz virtual é um dispositivo de nó único, o dispositivo é reiniciado depois de ser atualizado. Isto afeta qualquer E/S em curso. Clique em **OK** para instalar as atualizações.

    ![atualizar o dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate12m.png)

    Será notificado quando a tarefa de instalação é iniciada.

7.  Depois de o trabalho de instalação terminar com sucesso, clique em **Ver Ligação De Trabalho.** Esta ação leva-o à lâmina **'Atualizações de Instalação'.** Pode ver informações detalhadas sobre a tarefa aqui. 

    ![atualizar o dispositivo](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate16m1.png)

8. Se começou com uma versão virtual de software de execução Update 1 (10.0.10296.0), está agora a executar Update 1.1 e está feito. Pode saltar os passos restantes. 

    Se começou com uma versão virtual de software de execução Update 0.6 (10.0.10293.0), está agora atualizada para a Atualização 1.0. Vê outra mensagem indicando que as atualizações estão disponíveis. Repita os passos 4-7 para instalar a Atualização 1.1.

    

