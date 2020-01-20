---
title: Ativar ou desativar seu dispositivo StorSimple série 8000
description: Explica como ativar um novo dispositivo StorSimple, ativar um dispositivo que foi desligado ou com energia perdida e desativar um dispositivo em execução.
author: alkohli
ms.assetid: 8e9c6e6c-965c-4a81-81bd-e1c523a14c82
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1319583569a8abc619ad902a87ee551b476f88eb
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276888"
---
# <a name="turn-on-or-turn-off-your-storsimple-8000-series-device"></a>Ativar ou desativar seu dispositivo StorSimple série 8000

## <a name="overview"></a>Visão geral
Não é necessário desligar um dispositivo Microsoft Azure StorSimple como parte da operação normal do sistema. No entanto, talvez seja necessário ativar um novo dispositivo ou um dispositivo que precise ser desligado. Geralmente, um desligamento é necessário em casos em que você deve substituir o hardware com falha, mover fisicamente uma unidade ou retirar um dispositivo do serviço. Este tutorial descreve o procedimento necessário para ligar e desligar seu dispositivo StorSimple em cenários diferentes.

## <a name="turn-on-a-new-device"></a>Ativar um novo dispositivo
As etapas para ativar um dispositivo StorSimple pela primeira vez são diferentes, dependendo se o dispositivo for um modelo 8100 ou 8600. O 8100 tem um único compartimento principal, enquanto o 8600 é um dispositivo de compartimento duplo com um compartimento principal e um compartimento EBOD. As etapas detalhadas para ambos os modelos são abordadas nas seções a seguir.

* [Novo dispositivo com somente o compartimento primário](#new-device-with-primary-enclosure-only)
* [Novo dispositivo com compartimento EBOD](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Novo dispositivo com somente o compartimento primário
O modelo StorSimple 8100 é um dispositivo de compartimento único. Seu dispositivo inclui módulos de energia e resfriamento redundantes (PCMs). Ambos os PCMs devem ser instalados e conectados a diferentes fontes de alimentação para garantir a alta disponibilidade.

Execute as etapas a seguir para conectar o dispositivo à energia.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

> [!NOTE]
> Para obter instruções completas de instalação e cabeamento do dispositivo, vá para [instalar o dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md). Certifique-se de seguir as instruções exatamente.
> 
> 

### <a name="new-device-with-ebod-enclosure"></a>Novo dispositivo com compartimento EBOD
O modelo StorSimple 8600 tem um compartimento principal e um compartimento EBOD. Isso exige que as unidades sejam cabeadas para conectividade e energia SAS (Serial Attached SCSI).

Ao configurar este dispositivo pela primeira vez, execute as etapas para cabeamento SAS primeiro e, em seguida, conclua as etapas para cabeamento de energia.

[!INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

> [!NOTE]
> Para obter instruções completas de instalação e cabeamento do dispositivo, vá para [instalar o dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md). Certifique-se de seguir as instruções exatamente.

## <a name="turn-on-a-device-after-shutdown"></a>Ativar um dispositivo após o desligamento
As etapas para ativar um dispositivo StorSimple após sua desligamento são diferentes dependendo se o dispositivo for um modelo 8100 ou 8600. O 8100 tem um único compartimento principal, enquanto o 8600 é um dispositivo de compartimento duplo com um compartimento principal e um compartimento EBOD.

* [Dispositivo somente com o compartimento primário](#device-with-primary-enclosure-only)
* [Dispositivo com compartimento EBOD](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Dispositivo somente com o compartimento primário
Após um desligamento, use o procedimento a seguir para ativar um dispositivo StorSimple com um compartimento primário e nenhum compartimento EBOD.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Para ativar um dispositivo somente com um compartimento primário
1. Verifique se os interruptores de energia em ambos os módulos de energia e resfriamento (PCMs) estão na posição desligado. Se as opções não estiverem na posição desligado, vire-as para a posição desativado e aguarde até que as luzes sejam desligadas.
2. Ligue o dispositivo invertendo os interruptores de energia em ambos os PCMs para a posição ligado. O dispositivo deve ser ligado.
3. Verifique o seguinte para verificar se o dispositivo está totalmente em:
   
   1. Os LEDs OK em ambos os módulos PCM estão verdes.
   2. Os LEDs de status em ambos os controladores estão verdes estáveis.
   3. O LED azul em um dos controladores está piscando, o que indica que o controlador está ativo.
      
      Se qualquer uma dessas condições não for atendida, o dispositivo não estará íntegro. [Entre em contato com suporte da Microsoft](storsimple-8000-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>Dispositivo com compartimento EBOD
Após um desligamento, use o procedimento a seguir para ativar um dispositivo StorSimple com um compartimento primário e um compartimento EBOD. Execute cada etapa na sequência exatamente conforme descrito. Não fazer isso pode resultar em perda de dados.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Para ativar um dispositivo com um compartimento primário e um EBOD
1. Verifique se o compartimento EBOD está conectado ao compartimento principal. Para obter mais informações, consulte [instalar o dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).
2. Verifique se os módulos de energia e resfriamento (PCMs) nos compartimentos primário e EBOD estão na posição desligado. Se as opções não estiverem na posição desligado, vire-as para a posição desativado e aguarde até que as luzes sejam desligadas.
3. Ligue o compartimento EBOD primeiro invertendo os interruptores de energia em ambos os PCMs para a posição ligado. Os LEDs do PCM devem estar verdes. Um LED do controlador EBOD verde nessa unidade indica que o compartimento EBOD está ativado.
4. Ative o compartimento primário invertendo os interruptores de energia em ambos os PCMs para a posição ligado. Agora, o sistema inteiro deve estar ativado.
5. Verifique se os LEDs de SAS estão verdes, o que garante que a conexão entre o compartimento EBOD e o compartimento principal seja boa.

## <a name="turn-on-a-device-after-a-power-loss"></a>Ativar um dispositivo após uma perda de energia
Uma queda de energia ou interrupção pode desligar um dispositivo StorSimple. A interrupção de energia pode ocorrer em uma das fontes de alimentação ou em ambas as fontes de alimentação. As etapas de recuperação são diferentes dependendo se o dispositivo é um modelo 8100 ou 8600. O 8100 tem um único compartimento principal, enquanto o 8600 é um dispositivo de compartimento duplo com um compartimento principal e um compartimento EBOD. Esta seção descreve o procedimento de recuperação para cada cenário.

* [Dispositivo somente com o compartimento primário](#8100)
* [Dispositivo com compartimento EBOD](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>Dispositivo com somente o compartimento primário <a name="8100">
O sistema pode continuar sua operação normal se houver perda de energia para uma de suas fontes de alimentação. No entanto, para garantir a alta disponibilidade do dispositivo, restaure a energia para a fonte de alimentação assim que possível.

Se houver uma queda de energia ou interrupção de energia em ambas as fontes de alimentação, o sistema será desligado de maneira ordenada e controlada. Quando a energia for restaurada, o sistema será ativado automaticamente.

### <a name="device-with-ebod-enclosure-a-name8600"></a>Dispositivo com <a name="8600"> de compartimento do EBOD
#### <a name="power-loss-on-one-power-supply"></a>Perda de energia em uma fonte de alimentação
O sistema pode continuar sua operação normal se houver perda de energia para uma de suas fontes de alimentação no compartimento primário ou no compartimento EBOD. No entanto, para garantir a alta disponibilidade do dispositivo, restaure a energia para a fonte de alimentação assim que possível.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Perda de energia em ambas as fontes de alimentação nos compartimentos primário e EBOD
Se houver uma queda de energia ou interrupção de energia em ambas as fontes de alimentação, o compartimento EBOD será desligado imediatamente e o compartimento primário será desligado de maneira ordenada e controlada. Quando a energia for restaurada, o dispositivo será iniciado automaticamente.

Se a energia for desligada manualmente, execute as etapas a seguir para restaurar a energia para o sistema.

1. Ative o compartimento EBOD.
2. Depois que o compartimento EBOD estiver ativado, ative o compartimento primário.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Perda de energia em ambas as fontes de alimentação no compartimento EBOD
Ao configurar seus cabos, você deve garantir que o EBOD nunca esteja conectado sozinha a um PDU separado. Se o EBOD e o compartimento primário falharem ao mesmo tempo, o sistema será recuperado.

Se apenas o compartimento EBOD falhar em ambas as fontes de alimentação, o sistema não será recuperado automaticamente. Execute as seguintes etapas para ligar o sistema e restaurá-lo para um estado íntegro:

1. Se o compartimento primário estiver ativado, desative os módulos de energia e resfriamento (PCMs).
2. Aguarde alguns minutos para o sistema desligar.
3. Ative o compartimento EBOD.
4. Depois que o compartimento EBOD estiver ativado, ative o compartimento primário.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Ativar um dispositivo após a conexão do compartimento primário e do EBOD ser perdida
Se a conexão for perdida entre o controlador em espera e o controlador EBOD correspondente, o dispositivo continuará a funcionar. Se a conexão entre o controlador ativo do sistema e o controlador EBOD correspondente for perdida, o failover deverá ocorrer e o dispositivo deverá continuar funcionando normalmente.

Quando os cabos de SCSI anexado em série (SAS) são removidos ou a conexão entre o compartimento EBOD e o compartimento principal é severa, o dispositivo para de funcionar. Neste ponto, execute as etapas a seguir.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Para ligar o dispositivo após a conexão ser perdida
1. Acesse a parte traseira do dispositivo.
2. Se a conexão de cabo SAS entre o compartimento EBOD e o compartimento primário for interrompida, todos os LEDs de rota SAS no compartimento EBOD serão desativados.
3. Desligue os módulos de energia e resfriamento (PCMs) no compartimento EBOD e no compartimento principal.
4. Aguarde até que todas as luzes na parte posterior de ambos os compartimentos sejam desligadas.
5. Reinsira os cabos SAS e verifique se há uma boa conexão entre o compartimento EBOD e o compartimento principal.
6. Ative o compartimento EBOD primeiro invertendo ambos os comutadores PCM para a posição ligado.
7. Verifique se o compartimento EBOD está ligado verificando se o LED verde está aceso.
8. Ative o compartimento primário.
9. Verifique se o compartimento primário está ligado verificando se o LED verde do controlador está ligado.
10. Verifique se a conexão do compartimento EBOD com o compartimento principal é boa verificando se os LEDs da pista SAS (quatro por controlador EBOD) estão todos ATIVAdos.

> [!IMPORTANT]
> Se os cabos SAS estiverem com defeito ou se a conexão entre o compartimento EBOD e o compartimento primário não for boa, quando você ligar o sistema, ele entrará no modo de recuperação. [Entre em contato com suporte da Microsoft](storsimple-8000-contact-microsoft-support.md) se isso acontecer.


## <a name="turn-off-a-running-device"></a>Desligar um dispositivo em execução
Um dispositivo StorSimple em execução pode precisar ser desligado se estiver sendo movido, retirado do serviço ou tiver um componente com mau funcionamento que precisa ser substituído. As etapas são diferentes dependendo se o dispositivo StorSimple é um modelo 8100 ou 8600. O 8100 tem um único compartimento principal, enquanto o 8600 é um dispositivo de compartimento duplo com um compartimento principal e um compartimento EBOD. Esta seção detalha as etapas para desligar um dispositivo em execução.

* [Dispositivo com compartimento primário](#8100a)
* [Dispositivo com compartimento EBOD](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>Dispositivo com o compartimento primário <a name="8100a">
Para desligar o dispositivo de maneira ordenada e controlada, você pode fazer isso por meio da portal do Azure ou por meio do Windows PowerShell para StorSimple. 

> [!IMPORTANT]
> Não desligue um dispositivo em execução usando o botão de energia na parte posterior do dispositivo.
> 
> Antes de desligar o dispositivo, verifique se todos os componentes do dispositivo estão íntegros. No portal do Azure, navegue até **dispositivos** > **Monitor** > **integridade do hardware**e verifique se o status de todos os componentes está verde. Isso é verdadeiro apenas para um sistema íntegro. Se o sistema estiver sendo desligado para substituir um componente com problemas de funcionamento, você verá um status de falha (vermelho) ou degradado (amarelo) para o respectivo componente no **status de hardware**.
> 
> 

Depois de acessar o Windows PowerShell para StorSimple ou o portal do Azure, siga as etapas em [desligar um dispositivo StorSimple](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device). 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>Dispositivo com <a name="8600a"> de compartimento do EBOD
> [!IMPORTANT]
> Antes de desligar o compartimento primário e o compartimento EBOD, verifique se todos os componentes do dispositivo estão íntegros. No portal do Azure, navegue até **dispositivos** > **Monitor** > **integridade do hardware**e verifique se todos os componentes estão íntegros.


#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Para desligar um dispositivo em execução com o compartimento EBOD
1. Siga todas as etapas listadas em [desligar um dispositivo StorSimple](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) para o compartimento primário.
2. Depois que o compartimento primário for desligado, desligue o EBOD invertendo os comutadores de PCM (módulo de energia e resfriamento).
3. Para verificar se o EBOD foi desligado, verifique se todas as luzes na parte posterior do compartimento EBOD estão desligadas.

> [!NOTE]
> Os cabos SAS que são usados para conectar o compartimento EBOD ao compartimento primário não devem ser removidos até que o sistema seja desligado.

## <a name="next-steps"></a>Passos seguintes
[Contate a suporte da Microsoft](storsimple-8000-contact-microsoft-support.md) se você encontrar problemas ao ligar ou desligar um dispositivo StorSimple.

