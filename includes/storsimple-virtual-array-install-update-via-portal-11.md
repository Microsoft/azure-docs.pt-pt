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
ms.openlocfilehash: 8ef8b9d3350d0599ab00dfdbb018cf8dd900e316
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95558034"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Para instalar atualizações através do portal do Azure

1. Aceda ao seu Gestor de Dispositivos do StorSimple e selecione **Dispositivos**. Na lista de dispositivos ligados ao seu serviço, selecione e clique no dispositivo que pretende atualizar.

2. Em **Definições**, clique em **atualizações do dispositivo**.  

3. Verá uma mensagem se as atualizações de software estiverem disponíveis. Para procurar atualizações, também pode clicar em **Analisar**. Tome nota da versão de software que está a executar. 

    ![O painel de atualizações do Dispositivo diz que "Novas atualizações estão disponíveis" (em destaque) e "Última digitalização / 6/22/2018 14:46 PM / Versão de software / 10.0.10296.0". A versão está em destaque.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate3m1.png)

    É notificado quando a varredura começa e termina com sucesso.
 
4. Assim que as atualizações são analisadas, clique em **Transferir atualizações**. Em **Novas atualizações,** reveja as notas de lançamento. Note também que depois de as atualizações serem descarregadas, é necessário confirmar a instalação. Clique em **OK**.

    ![Nas atualizações do Dispositivo, o botão de atualizações de Descarregamento é realçado. Em Novas atualizações, existe um link para lançar notas e uma mensagem para confirmar a instalação após o download das atualizações. Há um botão OK.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate6m.png)

    É notificado quando o carregamento iniciar e for concluído com êxito.

5. Nas **atualizações do Dispositivo,** clique em **Instalar**.

     ![As atualizações do dispositivo dizem "Confirme a instalação de atualizações". Destacam-se o botão Instalar e a versão Software.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate11m1.png)

6. Sob **novas atualizações,** é avisado que a atualização é disruptiva. Como a matriz virtual é um dispositivo de nó único, o dispositivo é reiniciado depois de ser atualizado. Isto afeta qualquer E/S em curso. Clique em **OK** para instalar as atualizações.

    ![As novas atualizações dizem que "o seu dispositivo terá um tempo de inatividade quando estas atualizações forem instaladas". Há um link para lançar notas, e um botão OK.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate12m.png)

    Será notificado quando a tarefa de instalação é iniciada.

7.  Depois de o trabalho de instalação terminar com sucesso, clique em **Ver Ligação De Trabalho.** Esta ação leva-o à lâmina **'Atualizações de Instalação'.** Pode ver informações detalhadas sobre a tarefa aqui. 

    ![As atualizações do dispositivo têm um link marcado como "Instalar atualizações. Ver trabalho".](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate16m1.png)

8. Se começou com uma versão virtual de software de execução Update 1 (10.0.10296.0), está agora a executar Update 1.1 e está feito. Pode saltar os passos restantes. 

    Se começou com uma versão virtual de software de execução Update 0.6 (10.0.10293.0), está agora atualizada para a Atualização 1.0. Vê outra mensagem indicando que as atualizações estão disponíveis. Repita os passos 4-7 para instalar a Atualização 1.1.

    

