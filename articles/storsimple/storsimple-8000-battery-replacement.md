---
title: Substitua a bateria no dispositivo da série Microsoft Azure StorSimple 8000
description: Descreve como remover, substituir e manter o módulo de bateria de backup em seu dispositivo StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: f21bbf4777aa74e84ffb8c1af903f90608d5551f
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276893"
---
# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>Substitua o módulo de bateria de backup em seu dispositivo StorSimple

## <a name="overview"></a>Visão geral
O módulo de energia e resfriamento do compartimento primário (PCM) em seu dispositivo Microsoft Azure StorSimple tem um pacote de bateria adicional. Esse pacote fornece energia para que o dispositivo StorSimple possa salvar dados se houver perda de energia CA no compartimento principal. Esse pacote de bateria é chamado de *módulo de bateria de backup*. O módulo de bateria de backup existe somente para o compartimento primário em seu dispositivo StorSimple (o compartimento EBOD não contém um módulo de bateria de backup).

Este tutorial explica como:

* Remover o módulo de bateria de backup
* Instalar um novo módulo de bateria de backup
* Manter o módulo de bateria de backup

> [!IMPORTANT]
> Antes de remover e substituir um módulo de bateria de backup, examine as informações de segurança na [introdução à substituição de componentes de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-backup-battery-module"></a>Remover o módulo de bateria de backup
O módulo de bateria de backup para seu dispositivo StorSimple é uma unidade substituível em campo. Antes de ser instalado no PCM, o módulo de bateria deve ser armazenado em seu empacotamento original. Execute as etapas a seguir para remover a bateria de backup.

#### <a name="to-remove-the-backup-battery-module"></a>Para remover o módulo de bateria de backup
1. Na portal do Azure, vá para a folha serviço do StorSimple Device Manager. Vá para **dispositivos** e, em seguida, selecione o dispositivo na lista de dispositivos. Navegue até **Monitor** > **integridade do hardware**. Em **componentes compartilhados**, examine o status da bateria.
2. Identifique o PCM no qual a bateria falhou. A Figura 1 mostra a parte traseira do dispositivo StorSimple.
   
    ![Plano posterior dos módulos do compartimento primário do dispositivo](./media/storsimple-battery-replacement/IC740994.png)
   
    **Figura 1** Parte traseira do dispositivo primário mostrando os módulos PCM e controlador
   
   | Label | Descrição |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Controlador 0 |
   | 4 |Controlador 1 |
   
    Conforme mostrado pelo número 3 na Figura 2, o LED indicador de monitoramento no PCM 0 que corresponde à **falha da bateria** deve ser aceso.
   
    ![Plano posterior dos LEDs indicadores de monitoramento do PCM do dispositivo](./media/storsimple-battery-replacement/IC740992.png)
   
    **Figura 2** Verso do PCM mostrando os LEDs indicadores de monitoramento
   
   | Label | Descrição |
   |:--- |:--- |
   | 1 |Falha de energia AC |
   | 2 |Falha do ventilador |
   | 3 |Falha da bateria |
   | 4 |PCM OK |
   | 5 |Falha de energia de DC |
   | 6 |Integridade da bateria |
3. Para remover o PCM com uma bateria com falha, siga as etapas em [remover um PCM](storsimple-8000-power-cooling-module-replacement.md#remove-a-pcm).
4. Com o PCM removido, levante e gire a alça do módulo de bateria para cima, conforme indicado na figura a seguir, e puxe-a para remover a bateria.
   
    ![Removendo bateria do PCM](./media/storsimple-battery-replacement/IC741019.png)
   
    **Figura 3** Removendo a bateria do PCM
5. Coloque o módulo no empacotamento de unidade substituível por campo.
6. Retorne a unidade com defeito à Microsoft para a manutenção e o tratamento adequados.

## <a name="install-a-new-backup-battery-module"></a>Instalar um novo módulo de bateria de backup
Execute as etapas a seguir para instalar o módulo de bateria substituta no PCM no compartimento primário do seu dispositivo StorSimple.

#### <a name="to-install-the-battery-module"></a>Para instalar o módulo de bateria
1. Coloque o módulo de bateria de backup na orientação correta no PCM.
2. Pressione a alça do módulo de bateria para baixo até colocar o conector.
3. Substitua o PCM no compartimento primário seguindo as diretrizes em [substituir um módulo de energia e resfriamento em seu dispositivo StorSimple](storsimple-8000-power-cooling-module-replacement.md).
4. Após a conclusão da substituição, vá para o dispositivo e vá para **monitorar** > **integridade do hardware** no portal do Azure. Verifique o status da bateria para certificar-se de que a instalação foi bem-sucedida. Um status verde indica que a bateria está íntegra.

## <a name="maintain-the-backup-battery-module"></a>Manter o módulo de bateria de backup
Em seu dispositivo StorSimple, o módulo de bateria de backup fornece energia ao controlador durante um evento de perda de energia. Ele permite que o dispositivo StorSimple salve dados críticos antes de desligá-los de maneira controlada. Com duas baterias totalmente carregadas no PCMs, o sistema pode lidar com dois eventos de perda consecutivas.

Na portal do Azure, a **integridade do hardware** na folha **Monitor** indica se a bateria está funcionando corretamente ou se o fim da vida está se aproximando. O status da bateria é indicado pela **bateria no PCM 0** ou **na bateria no PCM 1** em **componentes compartilhados**. Esta folha mostrará um estado **degradado** para a abordagem do fim da vida útil e **falhará** no fim da vida útil.

> [!NOTE]
> A bateria pode relatar **falha** quando ela simplesmente precisa ser cobrada.


Se o estado **degradado** for exibido, recomendamos o seguinte curso de ação:

* O sistema pode ter tido uma perda de energia recente ou as baterias podem estar passando por manutenção periódica. Observe o sistema por 12 horas antes de continuar.
  
  * Se o estado ainda estiver **degradado** após 12 horas de conexão contínua para energia AC com os controladores e PCMs em execução, a bateria precisará ser substituída. [Entre em contato com suporte da Microsoft](storsimple-8000-contact-microsoft-support.md) para um módulo de bateria de backup de substituição.
  * Se o estado se tornar OK após 12 horas, a bateria estará operacional e precisará apenas de um encargo de manutenção.
* Se não houver uma perda associada de energia CA e o PCM estiver ligado e conectado à energia CA, a bateria precisará ser substituída. [Entre em contato com suporte da Microsoft](storsimple-8000-contact-microsoft-support.md) para solicitar um módulo de bateria de backup de substituição.

> [!IMPORTANT]
> Descarte a bateria com falha de acordo com as normas nacionais e regionais.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a [substituição de componentes de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).

