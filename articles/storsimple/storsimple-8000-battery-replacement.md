---
title: Substituir a bateria no dispositivo da série StorSimple 8000 do Microsoft Azure | Documentos da Microsoft
description: Descreve como remover, substituir e manter o módulo de bateria de segurança no dispositivo StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 4ebf3f28d40e0461d140a3fe74fb940720f26db6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60418972"
---
# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>Substitua o módulo de bateria de segurança no dispositivo StorSimple

## <a name="overview"></a>Descrição geral
O bastidor principal energia e refrigeração módulo (PCM) no seu dispositivo do Microsoft Azure StorSimple tem um pacote de bateria adicionais. Este pacote fornece power para que o dispositivo StorSimple pode guardar os dados se houver perda de energia CA ao bastidor principal. Este pacote de bateria é referido como o *módulo de bateria de segurança*. O módulo de bateria de segurança existe apenas para o bastidor principal no seu dispositivo StorSimple (o bastidor EBOD não contém um módulo de bateria de segurança).

Este tutorial explica como:

* Remover o módulo de bateria de segurança
* Instalar um novo módulo de bateria de segurança
* Manter o módulo de bateria de segurança

> [!IMPORTANT]
> Antes de remover e substituir um módulo de bateria de segurança, reveja as informações de segurança na [introdução à substituição de componente de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-backup-battery-module"></a>Remover o módulo de bateria de segurança
O módulo de bateria de segurança para o dispositivo StorSimple é uma unidade substituível em campo. Para que esta é instalada no PCM, o módulo de bateria deve ser armazenado em seu pacote original. Execute os seguintes passos para remover a bateria da cópia de segurança.

#### <a name="to-remove-the-backup-battery-module"></a>Para remover o módulo de bateria de segurança
1. No portal do Azure, aceda ao painel de serviço do StorSimple Device Manager. Aceda a **dispositivos** e, em seguida, selecione o seu dispositivo da lista de dispositivos. Navegue para **Monitor** > **estado de funcionamento do Hardware**. Sob **Shared Components**, ver o estado da bateria.
2. Identifique o PCM em que a bateria falhou. Figura 1 mostra parte de trás do dispositivo StorSimple.
   
    ![Backplane dos módulos de bastidor principal do dispositivo](./media/storsimple-battery-replacement/IC740994.png)
   
    **Figura 1** verso mostrando módulos PCM e controlador de dispositivo primário
   
   | Label | Descrição |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Controlador 0 |
   | 4 |Controlador 1 |
   
    Como é mostrado por número 3 na figura 2, os indicadores de monitorização por Instrutor no PCM 0, que corresponde ao **falhas de bateria** deve ser ativada.
   
    ![Backplane de LEDs indicador de monitorização do PCM do dispositivo](./media/storsimple-battery-replacement/IC740992.png)
   
    **Figura 2** PCM de voltar a mostrar o LEDs dos indicadores de monitorização
   
   | Label | Descrição |
   |:--- |:--- |
   | 1 |Falha de energia de AC |
   | 2 |Falha na ventoinha |
   | 3 |Falhas de bateria |
   | 4 |PCM OK |
   | 5 |Falha de energia do controlador de domínio |
   | 6 |Bom estado de funcionamento de bateria |
3. Para remover o PCM com uma bateria com falha, siga os passos em [remover um PCM](storsimple-8000-power-cooling-module-replacement.md#remove-a-pcm).
4. Com o PCM removido, leve e girar o identificador de módulo de bateria para cima, conforme indicado na figura a seguir e tirá-lo até remover a bateria.
   
    ![Remover da bateria do PCM](./media/storsimple-battery-replacement/IC741019.png)
   
    **Figura 3** removendo a bateria do PCM
5. Coloca o módulo na unidade substituível em campo empacotamento.
6. Devolva a unidade com defeito à Microsoft para a manutenção e a manipulação correta.

## <a name="install-a-new-backup-battery-module"></a>Instalar um novo módulo de bateria de segurança
Execute os seguintes passos para instalar o módulo de bateria de substituição no PCM no bastidor principal do dispositivo StorSimple.

#### <a name="to-install-the-battery-module"></a>Para instalar o módulo de bateria
1. Coloca o módulo de bateria de segurança a orientação adequada no PCM.
2. Prima a tecla o identificador de módulo de bateria todo o caminho para o conector de usuários.
3. Substituir o PCM no bastidor principal ao seguir as diretrizes [substituir um energia e refrigeração módulo no seu dispositivo StorSimple](storsimple-8000-power-cooling-module-replacement.md).
4. Depois de concluída a substituição, vá para o seu dispositivo e, em seguida, aceda a **Monitor** > **estado de funcionamento do Hardware** no portal do Azure. Verifique se o estado da bateria para se certificar de que a instalação foi concluída com êxito. Um status verde indica que a bateria está em bom estada.

## <a name="maintain-the-backup-battery-module"></a>Manter o módulo de bateria de segurança
No seu dispositivo StorSimple, o módulo de bateria de segurança fornece potência para o controlador durante um evento de perda de energia. Ele permite que o dispositivo StorSimple salvar dados importantes antes de desligar de forma controlada. Com dois baterias totalmente cobradas nos PCMs, o sistema consegue processar dois eventos de perda consecutivos.

No portal do Azure, o **estado de funcionamento do Hardware** sob a **Monitor** painel indica se a bateria está a funcionar incorretamente ou se aproxima a fim de vida. O status da bateria é indicado pelo **bateria no PCM 0** ou **bateria no PCM 1** sob **Shared Components**. Este painel mostrará uma **DEGRADED** estado para atingir o final da vida, e **falha** para atingido o fim de vida.

> [!NOTE]
> Pode reportar a bateria **falhada** quando ele simplesmente precisa ser cobrado.


Se o **DEGRADED** estado é apresentado, recomendamos o seguinte método de ação:

* O sistema pode ter ocorrido uma queda de energia recentes ou as baterias passando por manutenção periódica. Observe o sistema durante 12 horas antes de continuar.
  
  * Se o estado for ainda **DEGRADED** após 12 horas de ligação contínua para AC ligam-se com os controladores e PCMs em execução, em seguida, a bateria tem de ser substituído. Tente [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para um módulo de bateria de segurança de substituição.
  * Se ficar o estado OK após 12 horas, a bateria está operacional, além de apenas um custo de manutenção.
* Se não houve uma perda associada de energia CA e o PCM é ativado e ligado à energia CA, a bateria tem de ser substituído. [Contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para encomendar um módulo de bateria de segurança de substituição.

> [!IMPORTANT]
> Descarte a bateria com falha, de acordo com as regulamentações nacionais e regionais.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [substituição de componente de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).

