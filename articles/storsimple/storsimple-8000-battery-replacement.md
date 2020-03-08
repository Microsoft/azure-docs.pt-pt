---
title: Substitua a bateria no dispositivo da série Microsoft Azure StorSimple 8000
description: Descreve como remover, substituir e manter o módulo de bateria de reserva no seu dispositivo StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: f21bbf4777aa74e84ffb8c1af903f90608d5551f
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78365876"
---
# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>Substitua o módulo da bateria de reserva no seu dispositivo StorSimple

## <a name="overview"></a>Descrição geral
O módulo de alimentação e arrefecimento do recinto primário (PCM) no seu dispositivo Microsoft Azure StorSimple tem uma bateria adicional. Este pacote fornece energia para que o dispositivo StorSimple possa guardar dados se houver perda de energia CA para o recinto primário. Esta bateria é referida como o módulo da *bateria de reserva*. O módulo de bateria de reserva existe apenas para o recinto primário no seu dispositivo StorSimple (o recinto EBOD não contém um módulo de bateria de reserva).

Este tutorial explica como:

* Remova o módulo da bateria de reserva
* Instale um novo módulo de bateria de backup
* Mantenha o módulo da bateria de reserva

> [!IMPORTANT]
> Antes de remover e substituir um módulo de bateria de reserva, reveja as informações de segurança na substituição do componente de [hardware StorSimple](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-backup-battery-module"></a>Remova o módulo da bateria de reserva
O módulo de bateria de reserva para o seu dispositivo StorSimple é uma unidade substituível por campo. Antes de ser instalado no PCM, o módulo da bateria deve ser armazenado na sua embalagem original. Execute os seguintes passos para remover a bateria de reserva.

#### <a name="to-remove-the-backup-battery-module"></a>Para remover o módulo da bateria de reserva
1. No portal Azure, vá à sua lâmina de serviço StorSimple Device Manager. Vá ao **Dispositivo e,** em seguida, selecione o seu dispositivo a partir da lista de dispositivos. Navegue para **monitorizar** a saúde do **hardware** > . Em **componentes partilhados,** veja o estado da bateria.
2. Identifique o PCM em que a bateria falhou. A figura 1 mostra a parte de trás do dispositivo StorSimple.
   
    ![Backplane dos módulos de recinto primário do dispositivo](./media/storsimple-battery-replacement/IC740994.png)
   
    **Figura 1** Parte traseira do dispositivo primário que mostra módulos PCM e controlador
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Controlador 0 |
   | 4 |Controlador 1 |
   
    Tal como mostrado pelo número 3 da Figura 2, deve acender-se o LED do indicador de monitorização no PCM 0 que corresponde à falha da **bateria.**
   
    ![LEDIndicador de monitorização pcm do dispositivo](./media/storsimple-battery-replacement/IC740992.png)
   
    **Figura 2** Traseira do PCM mostrando os LED indicador estonteantes de monitorização
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |Falha de energia do AC |
   | 2 |Falha do ventilador |
   | 3 |Falha na bateria |
   | 4 |PCM OK |
   | 5 |Falha de energia em DC |
   | 6 |Bateria saudável |
3. Para remover o PCM com uma bateria falhada, siga os passos em [Remover um PCM](storsimple-8000-power-cooling-module-replacement.md#remove-a-pcm).
4. Com o PCM removido, levante e rode o punho do módulo da bateria para cima, conforme indicado na figura seguinte, e puxe-o para cima para remover a bateria.
   
    ![Remoção da bateria do PCM](./media/storsimple-battery-replacement/IC741019.png)
   
    **Figura 3** Remoção da bateria do PCM
5. Coloque o módulo na embalagem de unidade substituível por campo.
6. Devolva a unidade defeituosa à Microsoft para uma manutenção e manuseamento adequados.

## <a name="install-a-new-backup-battery-module"></a>Instale um novo módulo de bateria de backup
Execute os seguintes passos para instalar o módulo de bateria de substituição no PCM no recinto primário do seu dispositivo StorSimple.

#### <a name="to-install-the-battery-module"></a>Para instalar o módulo da bateria
1. Coloque o módulo da bateria de reserva na orientação correta no PCM.
2. Pressione a pega do módulo da bateria até ao lugar do conector.
3. Substitua o PCM no recinto primário seguindo as diretrizes em Substituir um Módulo de [Alimentação e Arrefecimento no seu dispositivo StorSimple](storsimple-8000-power-cooling-module-replacement.md).
4. Depois de concluída a substituição, vá ao seu dispositivo e depois vá ao **Monitor** > saúde de **Hardware** no portal Azure. Verifique o estado da bateria para se certificar de que a instalação foi bem sucedida. Um estado verde indica que a bateria é saudável.

## <a name="maintain-the-backup-battery-module"></a>Mantenha o módulo da bateria de reserva
No seu dispositivo StorSimple, o módulo de bateria de reserva fornece energia ao controlador durante um evento de perda de energia. Permite que o dispositivo StorSimple guarde dados críticos antes de desligar de forma controlada. Com duas baterias totalmente carregadas nos PCMs, o sistema pode lidar com dois eventos de perda consecutivos.

No portal Azure, a saúde do **hardware** sob a lâmina **monitora** indica se a bateria está avariada ou se o fim de vida está se aproximando. O estado da bateria é indicado pela **Bateria no PCM 0** ou bateria no **PCM 1** sob componentes **partilhados**. Esta lâmina mostrará um estado **degradado** para a aproximação do fim de vida, e **falhou** para o fim de vida alcançado.

> [!NOTE]
> A bateria pode reportar **FAILED** quando simplesmente precisa de ser carregada.


Se o estado **degradado** aparecer, recomendamos o seguinte curso de ação:

* O sistema pode ter sofrido uma recente perda de energia ou as baterias podem estar a sofrer uma manutenção periódica. Observe o sistema durante 12 horas antes de prosseguir.
  
  * Se o estado ainda estiver **degradado** após 12 horas de ligação contínua à potência CA com os controladores e PCMs em funcionamento, então a bateria tem de ser substituída. Contacte [o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para obter um módulo de bateria de substituição.
  * Se o estado ficar OK após 12 horas, a bateria está operacional, e só precisou de uma carga de manutenção.
* Se não tiver havido uma perda associada de energia ca e o PCM estiver ligado e ligado à potência CA, a bateria tem de ser substituída. [Contacte](storsimple-8000-contact-microsoft-support.md) o Microsoft Support para encomendar um módulo de bateria de substituição.

> [!IMPORTANT]
> Elimine a bateria falhada de acordo com os regulamentos nacionais e regionais.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a substituição do componente de [hardware StorSimple](storsimple-8000-hardware-component-replacement.md).

