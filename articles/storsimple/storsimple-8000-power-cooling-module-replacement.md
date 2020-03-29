---
title: Substitua um PCM no seu dispositivo da série StorSimple 8000 [ Microsoft Docs
description: Explica como remover e substituir o Módulo de Alimentação e Arrefecimento (PCM) no seu dispositivo StorSimple
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 42561570e24aec5edd33248ef1738e53175e480e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60632510"
---
# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Substituir um módulo refrigerador de energia no seu dispositivo StorSimple
## <a name="overview"></a>Descrição geral
O Módulo de Alimentação e Arrefecimento (PCM) no seu dispositivo Microsoft Azure StorSimple consiste numa alimentação e ventiladores de arrefecimento que são controlados através dos recintos primários e EBOD. Existe apenas um modelo de PCM que é certificado para cada recinto. O recinto primário é certificado para um PCM de 764 W e o recinto EBOD é certificado para um PCM de 580 W. Embora os PCMs para o recinto primário e o recinto EBOD sejam diferentes, o procedimento de substituição é idêntico.

Este tutorial explica como:

* Remover um PCM
* Instale um PCM de substituição

> [!IMPORTANT]
> Antes de remover e substituir um PCM, reveja as informações de segurança na substituição do componente de [hardware StorSimple](storsimple-8000-hardware-component-replacement.md).


## <a name="before-you-replace-a-pcm"></a>Antes de substituir um PCM
Esteja atento aos seguintes problemas importantes antes de substituir o seu PCM:

* Se a alimentação elétrica do PCM falhar, deixe o módulo defeituoso instalado, mas retire o cabo de alimentação. A ventoinha continuará a receber energia do recinto e continuará a fornecer um arrefecimento adequado. Se o ventilador falhar, o PCM tem de ser substituído imediatamente.
* Antes de retirar o PCM, desligue a energia do PCM desligando o interruptor principal (onde estiver presente) ou removendo fisicamente o cabo de alimentação. Isto fornece um aviso ao seu sistema de que uma paragem de energia é iminente.
* Certifique-se de que o outro PCM está funcional para o funcionamento contínuo do sistema antes de substituir o PCM defeituoso. Um PCM defeituoso deve ser substituído por um PCM totalmente operacional o mais rapidamente possível.
* A substituição do módulo PCM demora apenas alguns minutos a ser concluída, mas deve ser concluída no prazo de 10 minutos após a remoção do PCM falhado para evitar o sobreaquecimento.
* Note que os módulos PCM de substituição 764 W enviados da fábrica não contêm o módulo da bateria de reserva. Terá de retirar a bateria do seu PCM defeituoso e, em seguida, inseri-la no módulo de substituição antes de efetuar a substituição. Para mais informações, consulte como [remover e inserir um módulo](storsimple-8000-battery-replacement.md)de bateria de reserva .

## <a name="remove-a-pcm"></a>Remover um PCM
Siga estas instruções quando estiver pronto para remover um Módulo de Alimentação e Arrefecimento (PCM) do seu dispositivo Microsoft Azure StorSimple.

> [!NOTE]
> Antes de remover o seu PCM, verifique se tem uma substituição correta (764 W para o recinto primário ou 580 W para o recinto EBOD).

#### <a name="to-remove-a-pcm"></a>Para remover um PCM
1. No portal clássico do Azure, clique em **Definições > Monitor > saúde**de hardware . Verifique o estado dos componentes PCM em **componentes partilhados** para identificar qual pcm falhou:
   
   * Se uma fonte de alimentação no PCM 0 tiver falhado, o estado da **alimentação no PCM 0** será vermelho.
   * Se uma fonte de alimentação no PCM 1 tiver falhado, o estado da **alimentação no PCM 1** será vermelho.
   * Se o ventilador do PCM 1 tiver falhado, o estado de **arrefecimento 0 para PCM 0** ou **o arrefecimento 1 para PCM 0** será vermelho.
2. Localize o PCM falhado na parte de trás do recinto primário. Se estiver a executar um modelo 8600, identifique o recinto primário olhando para o Número de Identificação da Unidade do Sistema mostrado no visor LED do painel frontal. O ID da unidade predefinido apresentado no recinto primário é **de 00,** enquanto o ID da unidade predefinido apresentado no recinto EBOD é **01**. O diagrama e a tabela que se aseguir explicam o painel frontal do visor LED.
   
    ![ID do sistema no painel OPS frontal](./media/storsimple-power-cooling-module-replacement/IC740991.png)
   
     **Figura 1** Painel frontal do dispositivo  
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |Botão de silenciamento |
   | 2 |Poder do sistema |
   | 3 |Falha no módulo |
   | 4 |Falha lógica |
   | 5 |Ecrã id da unidade |
3. Os LED indicadores de monitorização na parte de trás do recinto primário também podem ser utilizados para identificar o PCM defeituoso. Consulte o diagrama e a tabela a seguir para entender como usar os LEDs para localizar o PCM defeituoso. Por exemplo, se o LED correspondente ao **'Falha de ventilador'** estiver aceso, a ventoinha falhou. Da mesma forma, se o LED correspondente ao **AC Fail** estiver aceso, a alimentação falhou. 
   
    ![Backplane do indicador de monitorização do pcm do dispositivo LEDs](./media/storsimple-power-cooling-module-replacement/IC740992.png)
   
     **Figura 2** Traseira do PCM com LED indicador
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |Falha de energia do AC |
   | 2 |Falha do ventilador |
   | 3 |Falha na bateria |
   | 4 |PCM OK |
   | 5 |Falha de energia em DC |
   | 6 |Bateria saudável |
4. Consulte o diagrama seguinte da parte de trás do dispositivo StorSimple para localizar o módulo PCM falhado. PcM 0 está à esquerda e PCM 1 à direita. A tabela que se segue explica os módulos.
   
     ![Backplane dos módulos de recinto primário do dispositivo](./media/storsimple-power-cooling-module-replacement/IC740994.png)
   
     **Figura 3** Parte traseira do dispositivo com módulos plug-in 
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Controlador 0 |
   | 4 |Controlador 1 |
5. Desligue o PCM defeituoso e desligue o cabo de alimentação. Agora pode remover o PCM.
6. Segure o trinco e o lado da pega pcm entre o polegar e o dedo indicador e aperte-os para abrir a pega.
   
    ![Punho de PCM de abertura](./media/storsimple-power-cooling-module-replacement/IC740995.png)
   
    **Figura 4** Abrir o cabo PCM
7. Segure a pega e retire o PCM.
   
    ![Remoção do dispositivo PCM](./media/storsimple-power-cooling-module-replacement/IC740996.png)
   
    **Figura 5** Remoção do PCM

## <a name="install-a-replacement-pcm"></a>Instale um PCM de substituição
Siga estas instruções para instalar um PCM no seu dispositivo StorSimple. Certifique-se de que inseriu o módulo da bateria de reserva antes de instalar o PCM de substituição (aplica-se apenas a 764 W PCMs). Para mais informações, consulte como [remover e inserir um módulo](storsimple-8000-battery-replacement.md)de bateria de reserva .

#### <a name="to-install-a-pcm"></a>Para instalar um PCM
1. Verifique se tem o PCM de substituição correto para este recinto. O recinto primário precisa de um PCM de 764 W e o recinto EBOD precisa de um PCM de 580 W. Não deve tentar utilizar o PCM de 580 W no recinto primário ou o PCM de 764 W no recinto EBOD. A imagem seguinte mostra onde identificar esta informação na etiqueta que é afixada no PCM.
   
    ![Etiqueta PCM do dispositivo](./media/storsimple-power-cooling-module-replacement/IC740973.png)
   
    **Figura 6** Etiqueta PCM
2. Verifique se há danos no recinto, prestando especial atenção aos conectores. 
   
   > [!NOTE]
   > **Não instale o módulo se os pinos do conector estiverem dobrados.**
   > 
   > 
3. Com a pega PCM na posição aberta, deslize o módulo para dentro do recinto.
   
    ![Instalação do dispositivo PCM](./media/storsimple-power-cooling-module-replacement/IC740975.png)
   
    **Figura 7** Instalação do PCM
4. Feche manualmente a pega PCM. Deve ouvir um clique à medida que o trinco do punho se engatar.
   
   > [!NOTE]
   > Para garantir que os pinos do conector estão engatados, pode puxar suavemente a pega sem soltar o trinco. Se o PCM deslizar para fora, implica que o trinco foi fechado antes dos conectores se ligarem.
   
5. Ligue os cabos de alimentação à fonte de alimentação e ao PCM.
6. Prenda os fardos de alívio da tensão.
7. Ligue o PCM.
8. Verifique se a substituição foi bem sucedida: no portal Azure do seu serviço StorSimple Device Manager, navegue para o seu dispositivo e, em seguida, para **Configurações > Monitor > saúde**de hardware . Sob os **componentes partilhados,** o estado do PCM deve ser verde.
   
   > [!NOTE]
   > Pode levar alguns minutos para que o PCM de substituição seja completamente inicializador.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a substituição do componente de [hardware StorSimple](storsimple-8000-hardware-component-replacement.md).

