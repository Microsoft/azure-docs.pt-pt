---
title: Substitua a bateria no dispositivo da série Microsoft Azure StorSimple 8000
description: Descreve como remover, substituir e manter o módulo de bateria de reserva no seu dispositivo StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 17a6a07d117557cdee23634bd211a14cf90d49e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023776"
---
# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>Substituir um módulo de bateria de reserva no seu dispositivo StorSimple

## <a name="overview"></a>Descrição Geral
O módulo de alimentação e arrefecimento do recinto primário (PCM) no seu dispositivo Microsoft Azure StorSimple tem uma bateria adicional. Esta embalagem fornece energia para que o dispositivo StorSimple possa guardar dados se houver perda de energia CA para o recinto primário. Esta bateria é referida como o *módulo de bateria de reserva*. O módulo de bateria de reserva existe apenas para o invólucro primário do seu dispositivo StorSimple (o compartimento EBOD não contém um módulo de bateria de reserva).

Este tutorial explica como:

* Remova o módulo de bateria de reserva
* Instale um novo módulo de bateria de reserva
* Mantenha o módulo de bateria de reserva

> [!IMPORTANT]
> Antes de remover e substituir um módulo de bateria de reserva, reveja as informações de segurança na [substituição do componente de hardware StorSimple](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-backup-battery-module"></a>Remova o módulo de bateria de reserva
O módulo de bateria de reserva para o seu dispositivo StorSimple é uma unidade substituível por campo. Antes de ser instalado no PCM, o módulo da bateria deve ser guardado na sua embalagem original. Execute os seguintes passos para remover a bateria de reserva.

#### <a name="to-remove-the-backup-battery-module"></a>Para remover o módulo de bateria de reserva
1. No portal Azure, aceda à sua lâmina de serviço StorSimple Device Manager. Vá aos **Dispositivos** e, em seguida, selecione o seu dispositivo a partir da lista de dispositivos. Navegue para **monitorizar** a  >  **saúde do hardware.** Em **Componentes Partilhados,** veja o estado da bateria.
2. Identifique o PCM no qual a bateria falhou. A figura 1 mostra a parte de trás do dispositivo StorSimple.
   
    ![Backplane dos módulos do recinto primário do dispositivo](./media/storsimple-battery-replacement/IC740994.png)
   
    **Figura 1** Parte de trás do dispositivo primário que mostra os módulos pcm e controlador
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Controlador 0 |
   | 4 |Controlador 1 |
   
    Como mostrado pelo número 3 na Figura 2, o led indicador de monitorização no PCM 0 que corresponde à **falha da bateria** deve ser acendido.
   
    ![Backplane Of Device PCM Monitoring Indicator LEDs](./media/storsimple-battery-replacement/IC740992.png)
   
    **Figura 2** Parte de trás do PCM mostrando os LED indicadores de monitorização
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |Falha de energia ca |
   | 2 |Falha do ventilador |
   | 3 |Falha na bateria |
   | 4 |PCM OK |
   | 5 |Falha de energia em DC |
   | 6 |Bateria saudável |
3. Para remover o PCM com uma bateria falhada, siga os passos no [Remove a PCM](storsimple-8000-power-cooling-module-replacement.md#remove-a-pcm).
4. Com o PCM removido, levante e rode o cabo do módulo da bateria para cima, conforme indicado na figura seguinte, e puxe-o para cima para remover a bateria.
   
    ![Remoção da bateria do PCM](./media/storsimple-battery-replacement/IC741019.png)
   
    **Figura 3** Remoção da bateria do PCM
5. Coloque o módulo na embalagem da unidade substituível pelo campo.
6. Devolva a unidade defeituosa à Microsoft para uma manutenção e manuseamento adequados.

## <a name="install-a-new-backup-battery-module"></a>Instale um novo módulo de bateria de reserva
Execute os seguintes passos para instalar o módulo de bateria de substituição no PCM no compartimento primário do seu dispositivo StorSimple.

#### <a name="to-install-the-battery-module"></a>Para instalar o módulo de bateria
1. Coloque o módulo de bateria de reserva na orientação adequada no PCM.
2. Pressione a pega do módulo da bateria até ao assento do conector.
3. Substitua o PCM no compartimento primário seguindo as diretrizes em [Substituir um Módulo de Alimentação e Arrefecimento no seu dispositivo StorSimple](storsimple-8000-power-cooling-module-replacement.md).
4. Depois de concluída a substituição, vá ao seu dispositivo e, em seguida, vá para a  >  **saúde** do Hardware Monitor no portal Azure. Verifique o estado da bateria para se certificar de que a instalação foi bem sucedida. Um estado verde indica que a bateria está saudável.

## <a name="maintain-the-backup-battery-module"></a>Mantenha o módulo de bateria de reserva
No seu dispositivo StorSimple, o módulo de bateria de reserva fornece energia ao controlador durante um evento de perda de energia. Permite que o dispositivo StorSimple guarde dados críticos antes de desligar de forma controlada. Com duas baterias totalmente carregadas nos PCMs, o sistema pode lidar com dois eventos de perda consecutiva.

No portal Azure, a saúde do **Hardware** sob a lâmina **monitor** indica se a bateria está avariada ou se o fim de vida está se aproximando. O estado da bateria é indicado pela **bateria em PCM 0** ou **bateria em PCM 1** em **Componentes Partilhados**. Esta lâmina mostrará um estado **degradado** para a aproximação em fim de vida, e **FALHA** para o fim de vida alcançado.

> [!NOTE]
> A bateria pode reportar **FALHA** quando simplesmente precisa de ser carregada.


Se o estado **degradado** aparecer, recomendamos o seguinte curso de ação:

* O sistema pode ter sofrido uma recente perda de energia ou as baterias podem estar a ser submetidas a uma manutenção periódica. Observe o sistema durante 12 horas antes de prosseguir.
  
  * Se o estado ainda estiver **degradado** após 12 horas de ligação contínua à energia CA com os controladores e PCMs em funcionamento, então a bateria precisa de ser substituída. Contacte [o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para obter um módulo de bateria de reserva de substituição.
  * Se o estado ficar bem após 12 horas, a bateria está operacional, e só precisava de uma carga de manutenção.
* Se não tiver havido uma perda associada de energia CA e o PCM estiver ligado e ligado à energia CA, a bateria tem de ser substituída. [Contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para encomendar um módulo de bateria de reserva de substituição.

> [!IMPORTANT]
> Elimine a bateria falhada de acordo com as normas nacionais e regionais.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a substituição do componente de hardware StorSimple](storsimple-8000-hardware-component-replacement.md).

