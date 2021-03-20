---
title: Substitua um PCM no seu dispositivo da série StorSimple 8000 | Microsoft Docs
description: Explica como remover e substituir o Módulo de Alimentação e Arrefecimento (PCM) no seu dispositivo StorSimple
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 045cec85174a88d1d608a4adc679461008852768
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85514584"
---
# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Substituir um módulo refrigerador de energia no seu dispositivo StorSimple
## <a name="overview"></a>Descrição Geral
O Módulo de Alimentação e Arrefecimento (PCM) no seu dispositivo Microsoft Azure StorSimple é composto por uma fonte de alimentação e ventiladores de arrefecimento que são controlados através dos compartimentos primários e EBOD. Há apenas um modelo de PCM que é certificado para cada recinto. O recinto primário é certificado para um PCM de 764 W e o recinto EBOD é certificado para um PCM de 580 W. Embora os PCMs para o recinto primário e o recinto EBOD sejam diferentes, o procedimento de substituição é idêntico.

Este tutorial explica como:

* Remover um PCM
* Instalar um PCM de substituição

> [!IMPORTANT]
> Antes de remover e substituir um PCM, reveja as informações de segurança na [substituição do componente de hardware StorSimple](storsimple-8000-hardware-component-replacement.md).


## <a name="before-you-replace-a-pcm"></a>Antes de substituir um PCM
Esteja atento às seguintes questões importantes antes de substituir o seu PCM:

* Se a alimentação do PCM falhar, deixe o módulo defeituoso instalado, mas retire o cabo de alimentação. A ventoinha continuará a receber energia do recinto e continuará a fornecer um arrefecimento adequado. Se o ventilador falhar, o PCM tem de ser substituído imediatamente.
* Antes de retirar o PCM, desligue a energia do PCM desligando o interruptor principal (quando presente) ou removendo fisicamente o cabo de alimentação. Isto fornece um aviso ao seu sistema de que uma paragem de energia é iminente.
* Certifique-se de que o outro PCM está funcional para continuar a funcionar do sistema antes de substituir o PCM defeituoso. Um PCM defeituoso deve ser substituído por um PCM totalmente operacional o mais rapidamente possível.
* A substituição do módulo PCM demora apenas alguns minutos a ser concluída, mas deve ser concluída no prazo de 10 minutos após a remoção do PCM falhado para evitar o sobreaquecimento.
* Note que os módulos PCM de substituição 764 W enviados da fábrica não contêm o módulo de bateria de reserva. Terá de retirar a bateria do seu PCM defeituoso e, em seguida, inseri-la no módulo de substituição antes de efetuar a substituição. Para obter mais informações, consulte como [remover e inserir um módulo de bateria de reserva](storsimple-8000-battery-replacement.md).

## <a name="remove-a-pcm"></a>Remover um PCM
Siga estas instruções quando estiver pronto para remover um Módulo de Alimentação e Arrefecimento (PCM) do seu dispositivo Microsoft Azure StorSimple.

> [!NOTE]
> Antes de remover o seu PCM, verifique se tem uma substituição correta (764 W para o compartimento primário ou 580 W para o recinto EBOD).

#### <a name="to-remove-a-pcm"></a>Para remover um PCM
1. No portal clássico Azure, clique em **Definições > Monitor > saúde do hardware**. Verifique o estado dos componentes do PCM em **componentes partilhados** para identificar quais pcm falhou:
   
   * Se uma fonte de alimentação em PCM 0 tiver falhado, o estado da **alimentação em PCM 0** será vermelho.
   * Se uma fonte de alimentação no PCM 1 tiver falhado, o estado da **alimentação no PCM 1** será vermelho.
   * Se o ventilador no PCM 1 tiver falhado, o estado de **arrefecimento de 0 para PCM 0** ou **Refrigeração 1 para PCM 0** será vermelho.
2. Localize o PCM falhado na parte de trás do recinto primário. Se estiver a executar um modelo 8600, identifique o recinto primário olhando para o Número de Identificação da Unidade do Sistema indicado no visor LED do painel frontal. O ID da unidade predefinido apresentado no compartimento primário é **00,** enquanto que o ID da unidade predefinido apresentado no compartimento EBOD é **01**. O diagrama e a tabela seguintes explicam o painel frontal do visor LED.
   
    ![ID do sistema no painel ops frontal](./media/storsimple-power-cooling-module-replacement/IC740991.png)
   
     **Figura 1** Painel frontal do dispositivo  
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |Botão de silenciamento |
   | 2 |Potência do sistema |
   | 3 |Falha no módulo |
   | 4 |Falha lógica |
   | 5 |Exibição de ID da unidade |
3. Os LED indicadores de monitorização na parte de trás do recinto primário também podem ser utilizados para identificar o PCM defeituoso. Consulte o diagrama e a tabela seguintes para entender como utilizar os LEDs para localizar o PCM defeituoso. Por exemplo, se o LED correspondente ao **Ventilador Fail** estiver aceso, o ventilador falhou. Do mesmo modo, se o LED correspondente ao **CA Fail** estiver aceso, a alimentação falhou. 
   
    ![Backplane do dispositivo PCM indicador indicador LED](./media/storsimple-power-cooling-module-replacement/IC740992.png)
   
     **Figura 2** Parte de trás do PCM com LED indicador
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |Falha de energia ca |
   | 2 |Falha do ventilador |
   | 3 |Falha na bateria |
   | 4 |PCM OK |
   | 5 |Falha de energia em DC |
   | 6 |Bateria saudável |
4. Consulte o diagrama seguinte da parte de trás do dispositivo StorSimple para localizar o módulo PCM falhado. PCM 0 está à esquerda e PCM 1 à direita. A tabela que se segue explica os módulos.
   
     ![Backplane dos módulos de recinto primário do dispositivo](./media/storsimple-power-cooling-module-replacement/IC740994.png)
   
     **Figura 3** Parte de trás do dispositivo com módulos plug-in 
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Controlador 0 |
   | 4 |Controlador 1 |
5. Desligue o PCM defeituoso e desligue o cabo de alimentação. Agora pode remover o PCM.
6. Segure o trinco e o lado da pega pcm entre o polegar e o indicador, e aperte-os juntos para abrir a pega.
   
    ![Abertura do Cabo PCM](./media/storsimple-power-cooling-module-replacement/IC740995.png)
   
    **Figura 4** Abrir a pega PCM
7. Segure a pega e retire o PCM.
   
    ![Remoção do pcm do dispositivo](./media/storsimple-power-cooling-module-replacement/IC740996.png)
   
    **Figura 5** Remoção do PCM

## <a name="install-a-replacement-pcm"></a>Instalar um PCM de substituição
Siga estas instruções para instalar um PCM no seu dispositivo StorSimple. Certifique-se de que inseriu o módulo de bateria de reserva antes de instalar o PCM de substituição (aplica-se apenas a PCMs de 764 W). Para obter mais informações, consulte como [remover e inserir um módulo de bateria de reserva](storsimple-8000-battery-replacement.md).

#### <a name="to-install-a-pcm"></a>Para instalar um PCM
1. Verifique se tem o PCM de substituição correto para este recinto. O recinto primário precisa de um PCM de 764 W e o recinto EBOD precisa de um PCM de 580 W. Não deve tentar utilizar o PCM de 580 W no recinto primário ou o PCM de 764 W no recinto EBOD. A imagem a seguir mostra onde identificar esta informação no rótulo que é afixado no PCM.
   
    ![Etiqueta pcm do dispositivo](./media/storsimple-power-cooling-module-replacement/IC740973.png)
   
    **Figura 6** Etiqueta PCM
2. Verifique se há danos no recinto, prestando especial atenção aos conectores. 
   
   > [!NOTE]
   > **Não instale o módulo se houver pinos de conector dobrados.**
   > 
   > 
3. Com a pega PCM na posição aberta, deslize o módulo para dentro do compartimento.
   
    ![Instalação pcm dispositivo](./media/storsimple-power-cooling-module-replacement/IC740975.png)
   
    **Figura 7** Instalação do PCM
4. Feche manualmente a pega PCM. Deve ouvir um clique à medida que o trinco da pega se ativa.
   
   > [!NOTE]
   > Para se certificar de que os pinos do conector estão engados, pode puxar suavemente a pega sem soltar o trinco. Se o PCM deslizar para fora, implica que o trinco foi fechado antes dos conectores se engatarem.
   
5. Ligue os cabos de alimentação à fonte de alimentação e ao PCM.
6. Fixe os fardos de alívio da tensão.
7. Ligue o PCM.
8. Verifique se a substituição foi bem sucedida: no portal Azure do seu serviço StorSimple Device Manager, navegue para o seu dispositivo e, em seguida, para **Definições > Monitor > saúde do hardware**. Sob os **componentes Partilhados,** o estado do PCM deve ser verde.
   
   > [!NOTE]
   > Pode levar alguns minutos para que o PCM de substituição inicialize completamente.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a substituição do componente de hardware StorSimple](storsimple-8000-hardware-component-replacement.md).

