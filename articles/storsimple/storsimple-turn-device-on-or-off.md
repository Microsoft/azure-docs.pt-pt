---
title: Ligue ou desligue o seu dispositivo da série StorSimple 8000
description: Explica como ligar um novo dispositivo StorSimple, ligar um dispositivo que foi desligado ou perdido de energia e desligar um dispositivo de funcionamento.
author: alkohli
ms.assetid: 8e9c6e6c-965c-4a81-81bd-e1c523a14c82
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1319583569a8abc619ad902a87ee551b476f88eb
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79254628"
---
# <a name="turn-on-or-turn-off-your-storsimple-8000-series-device"></a>Ligue ou desligue o seu dispositivo da série StorSimple 8000

## <a name="overview"></a>Descrição geral
O desfecho de um dispositivo Microsoft Azure StorSimple não é necessário como parte do funcionamento normal do sistema. No entanto, poderá ter de ligar um novo dispositivo ou um dispositivo que teve de ser desligado. Geralmente, é necessária uma paragem nos casos em que deve substituir o hardware falhado, mover fisicamente uma unidade ou retirar um dispositivo de serviço. Este tutorial descreve o procedimento necessário para ligar e desligar o seu dispositivo StorSimple em diferentes cenários.

## <a name="turn-on-a-new-device"></a>Ligue um novo dispositivo
Os passos para ligar um dispositivo StorSimple pela primeira vez diferem consoante o dispositivo seja um modelo 8100 ou 8600. O 8100 tem um único recinto primário, enquanto o 8600 é um dispositivo de duplo recinto com um recinto primário e um recinto EBOD. As etapas detalhadas para ambos os modelos estão cobertas nas seguintes secções.

* [Novo dispositivo apenas com recinto primário](#new-device-with-primary-enclosure-only)
* [Novo dispositivo com recinto EBOD](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Novo dispositivo apenas com recinto primário
O modelo StorSimple 8100 é um único dispositivo de recinto. O seu dispositivo inclui módulos de potência e arrefecimento redundantes (PCMs). Ambos os PCMs devem ser instalados e ligados a diferentes fontes de energia para garantir uma elevada disponibilidade.

Execute os seguintes passos para cabo rédo o seu dispositivo para obter energia.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

> [!NOTE]
> Para instruções completas de configuração e cablagem do dispositivo, vá instalar [o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md). Certifique-se de que segue exatamente as instruções.
> 
> 

### <a name="new-device-with-ebod-enclosure"></a>Novo dispositivo com recinto EBOD
O modelo StorSimple 8600 tem um recinto primário e um recinto EBOD. Isto requer que as unidades sejam por cabo para conectividade e potência SCSI (SAS) em série anexadas.

Ao configurar este dispositivo pela primeira vez, execute primeiro os passos para a cablagem SAS e, em seguida, complete os passos para a cablagem de energia.

[!INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

> [!NOTE]
> Para instruções completas de configuração e cablagem do dispositivo, vá instalar [o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md). Certifique-se de que segue exatamente as instruções.

## <a name="turn-on-a-device-after-shutdown"></a>Ligue um dispositivo após a paragem
Os passos para ligar um dispositivo StorSimple depois de ter sido desligado são diferentes dependendo se o dispositivo é um modelo 8100 ou 8600. O 8100 tem um único recinto primário, enquanto o 8600 é um dispositivo de duplo recinto com um recinto primário e um recinto EBOD.

* [Dispositivo com apenas recinto primário](#device-with-primary-enclosure-only)
* [Dispositivo com recinto EBOD](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Dispositivo com apenas recinto primário
Após uma paragem, utilize o seguinte procedimento para ligar um dispositivo StorSimple com um recinto primário e sem um recinto EBOD.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Para ligar um dispositivo apenas com um recinto primário
1. Certifique-se de que os interruptores de alimentação dos módulos de alimentação e de arrefecimento (PCMs) estão na posição OFF. Se os interruptores não estiverem na posição OFF, vire-os para a posição OFF e aguarde que as luzes se acendam.
2. Ligue o dispositivo ligando os interruptores de alimentação de ambos os PCMs para a posição ON. O dispositivo deve ligar-se.
3. Verifique o seguinte para verificar se o dispositivo está totalmente ligado:
   
   1. Os LEDs OK em ambos os módulos PCM são verdes.
   2. Os LEDs de estado em ambos os controladores são verdes sólidos.
   3. O LED azul de um dos controladores está a piscar, o que indica que o controlador está ativo.
      
      Se alguma destas condições não for satisfeita, então o seu dispositivo não está saudável. Entre [em contato com](storsimple-8000-contact-microsoft-support.md)o Suporte da Microsoft .

### <a name="device-with-ebod-enclosure"></a>Dispositivo com recinto EBOD
Após uma paragem, utilize o seguinte procedimento para ligar um dispositivo StorSimple com um recinto primário e um recinto EBOD. Execute cada passo em sequência exatamente como descrito. Se não o fizer, pode resultar na perda de dados.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Para ligar um dispositivo com um recinto primário e um EBOD
1. Certifique-se de que o recinto EBOD está ligado ao recinto primário. Para mais informações, consulte [Instale o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).
2. Certifique-se de que os módulos de alimentação e arrefecimento (PCMs) tanto no EBOD como nos recintos primários estão na posição OFF. Se os interruptores não estiverem na posição OFF, vire-os para a posição OFF e aguarde que as luzes se acendam.
3. Ligue primeiro o recinto EBOD, ligando os interruptores de alimentação de ambos os PCMs para a posição ON. Os LEDs de PCM devem ser verdes. Um LED verde do controlador EBOD nesta unidade indica que o recinto EBOD está ligado.
4. Ligue o recinto primário, ligando os interruptores de alimentação de ambos os PCMs para a posição ON. Todo o sistema deve estar ligado agora.
5. Verifique se os LEDs SAS são verdes, o que garante que a ligação entre o recinto EBOD e o recinto primário é boa.

## <a name="turn-on-a-device-after-a-power-loss"></a>Ligue um dispositivo após uma perda de energia
Uma falha de energia ou interrupção pode desligar um dispositivo StorSimple. A falta de energia pode acontecer numa das fontes de alimentação ou em ambas as fontes de energia. As etapas de recuperação são diferentes dependendo se o dispositivo é um modelo 8100 ou 8600. O 8100 tem um único recinto primário, enquanto o 8600 é um dispositivo de duplo recinto com um recinto primário e um recinto EBOD. Esta secção descreve o procedimento de recuperação para cada cenário.

* [Dispositivo com apenas recinto primário](#8100)
* [Dispositivo com recinto EBOD](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>Dispositivo com recinto primário apenas <a name="8100">
O sistema pode continuar o seu funcionamento normal se houver perda de energia para uma das suas fontes de alimentação. No entanto, para garantir uma elevada disponibilidade do dispositivo, restaure a energia da alimentação o mais rapidamente possível.

Se houver uma falha de energia ou uma interrupção de energia em ambas as fontes de alimentação, o sistema desligar-se-á de forma ordenada e controlada. Quando a energia é restaurada, o sistema liga-se automaticamente.

### <a name="device-with-ebod-enclosure-a-name8600"></a>Dispositivo com <a name="8600"> de recinto EBOD
#### <a name="power-loss-on-one-power-supply"></a>Perda de energia numa fonte de alimentação
O sistema pode continuar o seu funcionamento normal se houver perda de energia para uma das suas fontes de alimentação no recinto primário ou no recinto EBOD. No entanto, para garantir uma elevada disponibilidade do dispositivo, por favor, restaure a energia da alimentação o mais rapidamente possível.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Perda de energia em ambos os fornecimentos de energia em recintos primários e EBOD
Se houver uma falha de energia ou interrupção de energia em ambas as fontes de alimentação, o recinto EBOD será desligado imediatamente e o recinto primário será desligado de forma ordenada e controlada. Quando a energia for restabelecida, o aparelho iniciará automaticamente.

Se a energia for desligada manualmente, então tome os seguintes passos para restaurar a energia do sistema.

1. Ligue o recinto do EBOD.
2. Depois do recinto do EBOD estar ligado, ligue o recinto primário.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Perda de energia em ambas as fontes de energia no recinto da EBOD
Quando configurar os seus cabos, certifique-se de que o EBOD nunca está ligado sozinho a um PDU separado. Se o EBOD e o recinto primário falharem ao mesmo tempo, o sistema recuperará.

Se apenas o recinto EBOD falhar em ambas as fontes de alimentação, o sistema não se recuperará automaticamente. Tome os seguintes passos para ligar o sistema e restaurá-lo para um estado saudável:

1. Se o recinto primário estiver ligado, desligue os módulos de alimentação e de arrefecimento (PCMs).
2. Espere alguns minutos para o sistema desligar.
3. Ligue o recinto do EBOD.
4. Depois do recinto do EBOD estar ligado, ligue o recinto primário.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Ligue um dispositivo após a ligação primária e ebod do recinto for perdida
Se a ligação for perdida entre o controlador de espera e o controlador EBOD correspondente, o dispositivo continua a funcionar. Se a ligação entre o controlador ativo do sistema e o controlador EBOD correspondente for perdida, deve ocorrer falha e o dispositivo deve continuar a funcionar normalmente.

Quando ambos os cabos SCSI (SAS) em série são removidos ou a ligação entre o recinto EBOD e o recinto primário é cortado, o dispositivo deixará de funcionar. Neste ponto, execute os seguintes passos.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Para ligar o dispositivo após a ligação ser perdida
1. Aceda à parte de trás do dispositivo.
2. Se a ligação do cabo SAS entre o recinto EBOD e o recinto primário estiver avariada, todos os LEDs da faixa SAS no recinto ebod estarão desligados.
3. Desligue os módulos de alimentação e arrefecimento (PCMs) no recinto ebod e no recinto primário.
4. Espere até que todas as luzes na parte de trás de ambos os recintos se acendam.
5. Volte a inserir os cabos SAS e certifique-se de que existe uma boa ligação entre o recinto EBOD e o recinto primário.
6. Ligue primeiro o recinto EBOD, virando os dois interruptores pcm para a posição ON.
7. Certifique-se de que o recinto EBOD está ligado, verificando se o LED verde está ligado.
8. Ligue o recinto primário.
9. Certifique-se de que o recinto primário está ligado, verificando se o LED verde do controlador está ligado.
10. Verifique se a ligação do recinto EBOD com o recinto primário é boa, verificando se os LEDs da faixa SAS (quatro por controlador EBOD) estão todos LIGADOS.

> [!IMPORTANT]
> Se os cabos SAS estiverem defeituosos ou se a ligação entre o recinto EBOD e o recinto primário não for bom, quando ligar o sistema, entrará em modo de recuperação. Contacte o [Microsoft Support](storsimple-8000-contact-microsoft-support.md) se isso acontecer.


## <a name="turn-off-a-running-device"></a>Desligue um dispositivo de corrida
Um dispositivo StorSimple em execução pode ter de ser desligado se estiver a ser deslocado, retirado de serviço ou tiver um componente avariado que precisa de ser substituído. Os passos são diferentes dependendo se o dispositivo StorSimple é um modelo 8100 ou 8600. O 8100 tem um único recinto primário, enquanto o 8600 é um dispositivo de duplo recinto com um recinto primário e um recinto EBOD. Esta secção detalha os passos para desligar um dispositivo de funcionamento.

* [Dispositivo com recinto primário](#8100a)
* [Dispositivo com recinto EBOD](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>Dispositivo com <a name="8100a"> de recinto primário
Para desligar o dispositivo de forma ordenada e controlada, pode fazê-lo através do portal Azure ou através do Windows PowerShell para o StorSimple. 

> [!IMPORTANT]
> Não desligue um dispositivo de funcionamento utilizando o botão de alimentação na parte de trás do dispositivo.
> 
> Antes de desligar o dispositivo, certifique-se de que todos os componentes do dispositivo estão saudáveis. No portal Azure, navegue para **dispositivos** > **Monitor** > saúde de **hardware**, e verifique se o estado de todos os componentes é verde. Isto só é verdade para um sistema saudável. Se o sistema estiver a ser desligado para substituir um componente avariado, verá um estado avariado (vermelho) ou degradado (amarelo) para o respetivo componente no Estado do **Hardware**.
> 
> 

Depois de aceder ao Windows PowerShell para StorSimple ou ao portal Azure, siga os passos de [desligar um dispositivo StorSimple](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device). 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>Dispositivo com <a name="8600a"> de recinto EBOD
> [!IMPORTANT]
> Antes de desligar o recinto primário e o recinto EBOD, certifique-se de que todos os componentes do dispositivo estão saudáveis. No portal Azure, navegue para **dispositivos** > **Monitor** > saúde de **hardware**, e verifique se todos os componentes estão saudáveis.


#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Para desligar um dispositivo de funcionamento com o recinto ebod
1. Siga todos os passos listados em [desligar um dispositivo StorSimple](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) para o recinto primário.
2. Depois de o recinto primário ser desligado, desligue o EBOD, desligando os interruptores do Módulo de Alimentação e Arrefecimento (PCM).
3. Para verificar se o EBOD desligou, verifique se todas as luzes da parte de trás do recinto do EBOD estão desligadas.

> [!NOTE]
> Os cabos SAS utilizados para ligar o recinto EBOD ao recinto primário só devem ser removidos depois de o sistema ser desligado.

## <a name="next-steps"></a>Passos seguintes
[Contacte](storsimple-8000-contact-microsoft-support.md) o Microsoft Support se encontrar problemas ao ligar ou desligar um dispositivo StorSimple.

