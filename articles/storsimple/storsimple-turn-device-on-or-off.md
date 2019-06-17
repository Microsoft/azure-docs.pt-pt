---
title: Ativar ou desativar o seu dispositivo da série StorSimple 8000 | Documentos da Microsoft
description: Explica como ativar um novo dispositivo StorSimple, ativar um dispositivo que foi encerrado ou perdido de energia e desativar um dispositivo em execução.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 8e9c6e6c-965c-4a81-81bd-e1c523a14c82
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 29a45c0d32e35b5d321670bf25334a2976b93e56
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64693667"
---
# <a name="turn-on-or-turn-off-your-storsimple-8000-series-device"></a>Ativar ou desativar o seu dispositivo da série StorSimple 8000

## <a name="overview"></a>Descrição geral
Encerrar um dispositivo do Microsoft Azure StorSimple não é necessário como parte da operação de normais do sistema. No entanto, terá de ativar um novo dispositivo ou um dispositivo que tinham que ser desligado. Em geral, um encerramento é necessária em casos em que tem substituir a falha de hardware, fisicamente mover uma unidade ou tirar um dispositivo fora de serviço. Este tutorial descreve o procedimento necessário para ativar e desligar o dispositivo StorSimple em cenários diferentes.

## <a name="turn-on-a-new-device"></a>Ativar um novo dispositivo
Os passos para ativar um dispositivo StorSimple pela primeira vez, ser diferente dependendo se o dispositivo está um 8100 ou um modelo 8600. O 8100 tem um bastidor principal único, ao passo que o 8600 é um dispositivo de bastidor dupla com um bastidor principal e um bastidor EBOD. Os passos detalhados para ambos os modelos são abordados nas seções a seguir.

* [Novo dispositivo com apenas uma deteção de compartimento primário](#new-device-with-primary-enclosure-only)
* [Novo dispositivo com deteção de compartimento EBOD](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Novo dispositivo com apenas uma deteção de compartimento primário
O modelo de StorSimple 8100 é um dispositivo de bastidor único. O dispositivo inclui com redundância de energia e refrigeração módulos (PCMs). Ambos os PCMs tem de estar instalados e ligados a diferentes origens de dados para garantir a elevada disponibilidade.

Execute os seguintes passos para instalar os cabos do dispositivo para poder.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

> [!NOTE]
> Para concluir a configuração dispositivo e cabeamento instruções, aceda a [instalar o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md). Certifique-se de que siga as instruções exatamente.
> 
> 

### <a name="new-device-with-ebod-enclosure"></a>Novo dispositivo com deteção de compartimento EBOD
O modelo de StorSimple 8600 tem um bastidor principal e um bastidor EBOD. Isto requer que as unidades ser instalou os cabos em conjunto para conectividade Serial Attached SCSI (SAS) e de energia.

Ao configurar este dispositivo pela primeira vez, execute os passos para cablagem de SAS primeiro e, em seguida, conclua os passos para cablagem de energia.

[!INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

> [!NOTE]
> Para concluir a configuração dispositivo e cabeamento instruções, aceda a [instalar o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md). Certifique-se de que siga as instruções exatamente.

## <a name="turn-on-a-device-after-shutdown"></a>Ligue um dispositivo após encerramento
Os passos para ativar um dispositivo StorSimple depois que ele foi encerrado são diferentes, dependendo se o dispositivo está um 8100 ou um modelo 8600. O 8100 tem um bastidor principal único, ao passo que o 8600 é um dispositivo de bastidor dupla com um bastidor principal e um bastidor EBOD.

* [Dispositivo com apenas o bastidor principal](#device-with-primary-enclosure-only)
* [Dispositivo com deteção de compartimento EBOD](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Dispositivo com apenas o bastidor principal
Após um encerramento, utilize o procedimento seguinte para ativar um dispositivos do StorSimple com um bastidor principal e não bastidor EBOD.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Para ativar um dispositivos com apenas um bastidor principal
1. Certifique-se de que o poder muda em quer poder e módulos de refrigeração (PCMs) estão na posição OFF. Se os comutadores não estiverem na posição OFF, em seguida, flip-los para a posição de OFF e aguardar que as luzes desativar.
2. Ative o dispositivo ao mudar as as opções de energia em ambos os PCMs para a posição de ON. Deve ativar o dispositivo.
3. Verifique o seguinte para verificar se o dispositivo está totalmente em:
   
   1. O LEDs OK em ambos os módulos do PCM são verdes.
   2. O estado LEDs em ambos os controladores são um verde sólido.
   3. O LED azul em um dos controladores está intermitente, que indica que o controlador está ativo.
      
      Se alguma dessas condições não forem cumpridas, o dispositivo não está em bom estado. Tente [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>Dispositivo com deteção de compartimento EBOD
Após um encerramento, utilize o procedimento seguinte para ativar um dispositivos do StorSimple com um bastidor principal e um bastidor EBOD. Executa cada passo na sequência exatamente como descrito. Falha ao fazer isso pode resultar em perda de dados.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Para ativar um dispositivos com um site primário e um bastidor EBOD
1. Certifique-se de que o bastidor EBOD está ligado ao bastidor principal. Para obter mais informações, consulte [instalar o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).
2. Certifique-se de que o poder e a refrigeração módulos (PCMs) no EBOD e inclusões primários são na posição OFF. Se os comutadores não estiverem na posição OFF, em seguida, flip-los para a posição de OFF e aguardar que as luzes desativar.
3. Ative o bastidor EBOD primeiro por mudar as as opções de energia em ambos os PCMs para a posição de ON. O LEDs PCM deve estar verdes. Um controlador EBOD LED verde esta unidade indica que o bastidor EBOD é na.
4. Ative o bastidor principal por mudar as as opções de energia em ambos os PCMs para a posição de ON. Todo o sistema deverá estar agora no.
5. Certifique-se de que a SAS LEDs são verdes, o que garante que a ligação entre o bastidor EBOD e o bastidor principal é boa.

## <a name="turn-on-a-device-after-a-power-loss"></a>Ligue um dispositivo após uma queda de energia
Uma queda de energia ou uma interrupção, pode encerrar um dispositivo StorSimple. A queda de energia pode ser efetuadas em um das fontes de alimentação ou ambas as fontes de alimentação. Os passos de recuperação são diferentes, dependendo do dispositivo é um modelo 8100 ou um 8600. O 8100 tem um bastidor principal único, ao passo que o 8600 é um dispositivo de bastidor dupla com um bastidor principal e um bastidor EBOD. Esta secção descreve o procedimento de recuperação para cada cenário.

* [Dispositivo com apenas o bastidor principal](#8100)
* [Dispositivo com deteção de compartimento EBOD](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>Dispositivo com apenas o bastidor principal <a name="8100">
O sistema poderá continuar sua operação normal, se houver perda de energia a uma das suas fontes de alimentação. No entanto, para garantir a elevada disponibilidade do dispositivo, restaure a energia para a fonte de alimentação logo que possível.

Se houver uma queda de energia ou uma interrupção de energia em ambas as fontes de alimentação, o sistema será encerrado de maneira ordenada e controlada. Quando a energia é restaurada, o sistema irá ligar automaticamente.

### <a name="device-with-ebod-enclosure-a-name8600"></a>Dispositivo com deteção de compartimento EBOD <a name="8600">
#### <a name="power-loss-on-one-power-supply"></a>Fornecer a perda de energia no poder de um
O sistema poderá continuar sua operação normal, se houver perda de energia a uma das suas fontes de alimentação no bastidor principal ou o bastidor EBOD. No entanto, para garantir a elevada disponibilidade do dispositivo, tente restaure a energia para a fonte de alimentação logo que possível.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Perda de energia em ambas as fontes de alimentação no principal e as inclusões EBOD
Se houver uma queda de energia ou uma interrupção de energia em ambas as fontes de alimentação, o bastidor EBOD irá encerrar imediatamente e o bastidor principal será desligado de uma forma ordenada e controlada. Quando power é restaurado, a aplicação será iniciada automaticamente.

Se o poder é desativado manualmente, em seguida, tire os seguintes passos para restaurar a energia para o sistema.

1. Ative o bastidor EBOD.
2. Após a inclusão EBOD no, ative o bastidor principal.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Perda de energia em ambas as fontes de alimentação no bastidor EBOD
Ao configurar os cabos, certifique-se de que nunca está ligado a EBOD sozinho para uma PDU separada. Se o bastidor principal e de EBOD falharem ao mesmo tempo, o sistema será recuperada.

Se apenas a inclusão EBOD falha em ambas as fontes de alimentação, não vai recuperar automaticamente o sistema de segurança. Siga os passos seguintes para ativar o sistema e restaurá-lo para um bom estado de funcionamento:

1. Se o bastidor principal estiver ativado, optar por desativar a energia e refrigeração módulos (PCMs).
2. Aguarde alguns minutos para que o sistema encerrar.
3. Ative o bastidor EBOD.
4. Após a inclusão EBOD no, ative o bastidor principal.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Ativar um dispositivo após a primária e a ligação de bastidor EBOD se perder
Se a ligação for perdida entre o controlador em modo de espera e o controlador EBOD correspondente, o dispositivo continua a funcionar. Se a ligação entre o controlador ativo do sistema e o controlador EBOD correspondente for perdida, deve ocorrer a ativação pós-falha e o dispositivo deve continuar a funcionar normalmente.

Quando os dois cabos Serial Attached SCSI (SAS) são removidos ou a ligação entre o bastidor EBOD e o bastidor principal é severed, o dispositivo deixa de funcionar. Neste momento, execute os seguintes passos.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Para ativar o dispositivo após a ligação foi perdida
1. Aceda a parte de trás do dispositivo.
2. Se a ligação de cabo SAS entre o bastidor EBOD e o bastidor principal for interrompida, todos os lane SAS LEDs na caixa EBOD estará desativada.
3. Encerre energia e refrigeração módulos (PCMs) sobre a inclusão EBOD e o bastidor principal.
4. Aguarde até que todas as luzes na parte de trás de ambas as caixas de desativar o limite de tempo.
5. Reinserir os cabos SAS e certifique-se de que existe uma boa ligação entre o bastidor EBOD e o bastidor principal.
6. Ative o bastidor EBOD primeiro por mudar as ambas as opções PCM para a posição de ON.
7. Certifique-se de que o bastidor EBOD está ativada, verificando que o LED verde está ON.
8. Ative o bastidor principal.
9. Certifique-se de que o bastidor principal está ativada, verificando que o LED de controlador verde está ON.
10. Certifique-se de que a ligação de bastidor EBOD com o bastidor principal é boa, verificando que o lane SAS LEDs (quatro por controlador de EBOD) são Diante de todos os.

> [!IMPORTANT]
> Se os cabos SAS estão com defeito ou a ligação entre o bastidor EBOD e o bastidor principal é não bom, quando ativar o sistema, ele irá entrar em modo de recuperação. Volte [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) se isso acontecer.


## <a name="turn-off-a-running-device"></a>Desativar um dispositivo em execução
Um dispositivo StorSimple em execução poderá ter de ser encerradas se esta for a ser movida, retirado de serviço, ou tem um componente com funcionamento incorreto, que tem de ser substituído. Os passos são diferentes, dependendo se o dispositivo StorSimple é um 8100 ou de um modelo 8600. O 8100 tem um bastidor principal único, ao passo que o 8600 é um dispositivo de bastidor dupla com um bastidor principal e um bastidor EBOD. Esta seção detalha os passos para encerrar um dispositivo em execução.

* [Dispositivo com o bastidor principal](#8100a)
* [Dispositivo com deteção de compartimento EBOD](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>Dispositivo com o bastidor principal <a name="8100a">
Para encerrar o dispositivo de maneira ordenada e controlada, pode fazê-lo através do portal do Azure ou através do Windows PowerShell para StorSimple. 

> [!IMPORTANT]
> Não encerre um dispositivo em execução ao utilizar o botão de energia na parte de trás do dispositivo.
> 
> Antes de encerrar o dispositivo, certifique-se de que todos os componentes de dispositivo estão em bom Estados. No portal do Azure, navegue até **dispositivos** > **Monitor** > **estado de funcionamento do Hardware**e certifique-se de que o estado de todos os componentes está verde. Isso é verdade apenas para um sistema bom estado de funcionamento. Se o sistema está a ser desligado para substituir um componente com funcionamento incorreto, verá um falha (vermelho) ou degradação de estado (amarelo) para o componente respectivo no **estado do Hardware**.
> 
> 

Depois de acessar o Windows PowerShell para StorSimple ou o portal do Azure, siga os passos em [encerrar um dispositivo StorSimple](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device). 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>Dispositivo com deteção de compartimento EBOD <a name="8600a">
> [!IMPORTANT]
> Antes de encerrar o bastidor principal e a inclusão EBOD, certifique-se de que todos os componentes de dispositivo estão em bom Estados. No portal do Azure, navegue até **dispositivos** > **Monitor** > **estado de funcionamento do Hardware**e certifique-se de que todos os componentes estão em bom Estados.


#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Para encerrar um dispositivo em execução com deteção de compartimento EBOD
1. Siga todos os passos listados na [encerrar um dispositivo StorSimple](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) para o bastidor principal.
2. Depois do bastidor principal é encerrado, encerre o EBOD ao inversão desativar comutadores de energia e refrigeração módulo PCM ().
3. Para verificar que o EBOD foi encerrado, verifique que todas as luzes atrás do bastidor EBOD estão desativadas.

> [!NOTE]
> Não devem ser removidos os cabos SAS que são utilizados para ligar o bastidor EBOD ao bastidor principal até depois do sistema é desligado.

## <a name="next-steps"></a>Passos Seguintes
[Contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) se encontrar problemas ao ligar ou desligar um dispositivo StorSimple.

