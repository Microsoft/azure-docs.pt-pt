---
title: Indicadores de monitorização do StorSimple | Documentos da Microsoft
description: Descreve a luz – emitting diodes (LEDs) e alarmes sonoro utilizados para monitorizar o estado do dispositivo StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 59dee7b9-ca6d-4fd9-96e6-a0071e8d248e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: ef8acf1c3c9211168ebacc8d62647f6789c745a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60630658"
---
# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>Utilizar o StorSimple indicadores de monitorização para gerir o seu dispositivo


## <a name="overview"></a>Descrição geral
O dispositivo StorSimple inclui light – emitting diodes (LEDs) e que pode utilizar para monitorizar o estado geral do dispositivo StorSimple e módulos de alarmes. Os indicadores de monitorização podem ser encontrados nos componentes de hardware do bastidor de principal do dispositivo e a inclusão EBOD. Os indicadores de monitorização podem ser LEDs ou alarmes sonoro.

Existem três Estados de LED utilizados para indicar o estado de um módulo: verde, piscar verde para vermelho amber ou red-amber.  

* Verde LEDs representam um bom estado de funcionamento.  
* Piscar verde para representam de LEDs red-amber a presença de condições não críticas que podem exigir a intervenção do utilizador.  
* Red amber LEDs indicarem que existe uma falha crítica presente no módulo.  

O restante deste artigo descreve os vários LEDs de indicador de monitorização, suas localizações no dispositivo StorSimple, o estado do dispositivo com base nos Estados de LED e quaisquer alarmes sonoro associados.

## <a name="front-panel-indicator-leds"></a>Indicadores LED de painel frontal
Painel de frente, também conhecido como o *painel de operações* ou *painel de ops*, apresenta o estado agregado de todos os módulos no sistema. O painel frontal é idêntico no StorSimple primário e o bastidor EBOD e está ilustrado abaixo.  

   ![Painel frontal do dispositivo][1]

O painel frontal contém os indicadores seguintes:  

1. Botão de sem som
2. Indicador de Power LED (vermelho/verde-amber)
3. Indicador de falha do módulo LEVOU (no red-amber/OFF)
4. Indicador de falha lógicos LEVOU (no red-amber/OFF
5. Exibição de ID de unidade  

A principal diferença entre os painel frontal LEDs para o dispositivo e do bastidor EBOD é o **número de identificação de unidade de sistema** mostrado no ecrã do LED. A unidade padrão é apresentado no dispositivo de ID **00**, ao passo que o ID de unidade de predefinição apresentado na caixa EBOD é **01**. Isto permite-lhe rapidamente diferenciar entre o dispositivo e a inclusão EBOD quando o dispositivo é ativado. Se o dispositivo estiver desativado, utilize as informações fornecidas [ativar um novo dispositivo](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) para diferenciar o dispositivo da inclusão EBOD.  

## <a name="front-panel-led-status"></a>Painel frontal LED de estado
Utilize a tabela seguinte para identificar o Estado indicado por LEDs no painel de front-para o dispositivo ou o bastidor EBOD.  

| Energia do sistema | Falha do módulo | Falhas de lógica | Alarme | Estado |
| --- | --- | --- | --- | --- |
| Red amber |OFF |OFF |N/A |Energia CA perdido, operando no power cópia de segurança ou a energia CA e o controlador de módulos foram removidos. |
| Verde |ON |ON |N/A |Estado de teste de ativação painel OPS (5s) |
| Verde |OFF |OFF |N/A |Ligar, todas as funções de boa |
| Verde |ON |N/A |Falhas do PCM LEDs, falhas de ventoinha LEDs |Quaisquer falhas PCM, falhas de ventoinha, pela ou em temperatura |
| Verde |ON |N/A |Módulo de e/s LEDs |Quaisquer falhas de módulo de controlador |
| Verde |ON |N/A |N/A |Falhas de lógica de bastidor |
| Verde |Flash |N/A |Estado do módulo LED no módulo de controlador. Falhas do PCM LEDs, falhas de ventoinha LEDs |Tipo de módulo de controlador desconhecido instalado, falha de bus de I2C, erro de configuração do controlador módulo vital produto dados (VPD) |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Energia, refrigeração indicadores LED de módulo (PCM)
Indicador de módulo (PCM) LEDs refrigeração energia pode ser encontrado na parte de trás do bastidor principal ou o bastidor EBOD em cada módulo PCM. Este tópico descreve como utilizar os LEDs seguintes para monitorizar o estado do dispositivo StorSimple.  

* PCM LEDs para o bastidor principal
* PCM LEDs para a inclusão EBOD

## <a name="pcm-leds-for-the-primary-enclosure"></a>PCM LEDs para o bastidor principal
O dispositivo StorSimple tem um módulo PCM 764W com uma bateria adicional. A ilustração seguinte mostra o painel de LED para o dispositivo.  

   ![LEDs PCM no bastidor principal][2]

Legenda de LED:

1. Falha de energia de AC
2. Falha na ventoinha
3. Falhas de bateria
4. PCM OK
5. Falha do controlador de domínio
6. Boa de bateria  

O estado do PCM é indicado no painel LED. O painel do PCM LED de dispositivo tem seis LEDs. Quatro destes LEDs apresentar o estado de alimentação e a ventoinha. Os restantes dois LEDs indicam o estado do módulo de bateria de segurança no PCM. Pode utilizar as tabelas seguintes para determinar o estado do PCM.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>PCM LED indicadores de fonte de alimentação e ventoinha
| Estado | PCM OK (verde) | Falha de AC (amber) | Ventoinha falhar (amber) | Falha do controlador de domínio (amber) |
| --- | --- | --- | --- | --- |
| Não existem alternada (ao bastidor) |OFF |OFF |OFF |OFF |
| Não existem alternada (apenas para este PCM) |OFF |ON |OFF |ON |
| AC apresentar PCM ON - OK |ON |OFF |OFF |OFF |
| Falha do PCM (ventoinha ativação) |OFF |OFF |ON |N/A |
| Falhas do PCM (sobre amp, ao longo de tensão, ao longo do atual) |OFF |ON |ON |ON |
| PCM (ventoinha fora da tolerância) |ON |OFF |OFF |ON |
| Modo de reserva dinâmica |Flashing |OFF |OFF |OFF |
| Transferência de firmware do PCM |OFF |Flashing |Flashing |Flashing |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>PCM LED indicadores para a bateria da cópia de segurança
| Estado | Bateria bom (verde) | Falhas de bateria (amber) |
| --- | --- | --- |
| Bateria não está presente |OFF |OFF |
| Bateria presente e cobrada |ON |OFF |
| Altas de manutenção ou de cobrança de consumo da bateria |Flashing |OFF |
| Falhas de "soft" da bateria (recuperável) |OFF |Flashing |
| Falhas de "disco rígida" da bateria (não recuperável) |OFF |ON |
| Bateria disarmed |Flashing |OFF |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>PCM LEDs para a inclusão EBOD
A inclusão EBOD tem um PCM 580W e não bateria adicional. O painel do PCM para a inclusão EBOD tem indicador LEDs apenas para as fontes de alimentação e a ventoinha. A ilustração seguinte mostra esses LEDs.

   ![PCM LEDs na caixa EBOD][3] 

Pode usar a tabela seguinte para determinar o estado do PCM.  

| Estado | PCM OK (verde) | Falha de AC (amber) | Ventoinha falhar (amber) | Falha do controlador de domínio (amber) |
| --- | --- | --- | --- | --- |
| Não existem alternada (ao bastidor) |OFF |OFF |OFF |OFF |
| Não existem alternada (apenas para este PCM) |OFF |ON |OFF |ON |
| AC apresentar PCM Diante – OK |ON |OFF |OFF |OFF |
| Falha do PCM (ventoinha ativação) |OFF |OFF |ON |X |
| Falhas do PCM (sobre amp, ao longo de tensão, ao longo do atual |OFF |ON |ON |ON |
| PCM (ventoinha fora da tolerância) |ON |OFF |OFF |ON |
| Modelo de reserva dinâmica |Flashing |OFF |OFF |OFF |
| Transferência de firmware do PCM |OFF |Flashing |Flashing |Flashing |

## <a name="controller-module-indicator-leds"></a>Indicadores LED do módulo de controlador
O dispositivo StorSimple contém LEDs para o controlador principal e os módulos de controlador EBOD.   

### <a name="monitoring-leds-for-the-primary-controller"></a>LEDs de monitorização para o controlador primário
A ilustração a seguir ajuda a identificar os LEDs no controlador primário. (Todos os componentes estão relacionados para ajudar na orientação.)  

   ![LEDs de monitorização - controlador primário][4]

Utilize a tabela seguinte para determinar se o módulo de controlador está a funcionar corretamente.  

### <a name="controller-indicator-leds"></a>Indicadores LED de controlador
| LED | Descrição |
| --- | --- |
| ID LED (azul) |Indica que o módulo está a ser identificado. Se o LED azul é intermitente num controlador de em execução, em seguida, o controlador é o controlador ativo e o outro é o controlador em modo de espera. Para obter mais informações, consulte [identificar o controlador ativo no seu dispositivo](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| Falhas LED (amber) |Indica uma falha no controlador. |
| LED OK (verde) |Constante verde indica que o controlador está OK. Piscar verde indica um erro de configuração de VPD de controlador. |
| Atividade SAS LEDs (verde) |Constante verde indica uma ligação sem nenhuma atividade atual. Piscar verde indica que a ligação tem atividade em curso. |
| Estado de Ethernet LEDs |Direita indica atividade de ligação/rede: Active Directory, (piscar verde) de ligação (constante verde) atividade de rede. Lado esquerdo indica a velocidade da rede: (amarelo) 1000 Mb/s (verde) 100 Mb/s e (OFF) 10 Mb/s. Consoante o modelo de componente, essa perspectiva poderá blink, mesmo que a interface de rede não está ativada. |
| LEDs de POSTAGEM |Indica o progresso de arranque quando o controlador estiver ativado. Se falhar o dispositivo StorSimple efetuar o arranque, esta LED ajudará a identificar o ponto do processo de arranque a que a falha ocorreu Support da Microsoft. |

> [!IMPORTANT]
> Se a LED de falha está ativada, é um problema com o módulo de controlador que pode ser resolvido reiniciando o controlador. Entre em contato com Support da Microsoft se reiniciar o controlador não resolver este problema.  
> 
> 

### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>LEDs de monitorização para o EBOD (bastidor EBOD)
Cada um dos controladores de 6 Gb/s SAS EBOD tem LEDs indicam o respetivo estado conforme mostrado na ilustração seguinte.  

  ![LEDs de monitorização - bastidor EBOD][5]

Utilize a tabela seguinte para determinar se o módulo de controlador EBOD está a funcionar normalmente.  

### <a name="ebod-controller-module-indicator-leds"></a>Indicadores de módulo LED de controlador EBOD
| Estado | Módulo de e/s OK (verde) | Falhas de módulo de e/s (amber) | Atividade de porta de anfitrião (verde) |
| --- | --- | --- | --- |
| Módulo de controlador OK |ON |OFF |- |
| Falha do módulo de controlador |OFF |ON |- |
| Nenhuma ligação de porta de anfitrião externo |- |- |OFF |
| Ligação de porta de anfitrião externo – nenhuma atividade |- |- |ON |
| Ligação de porta de anfitrião externo - atividade |- |- |Flashing |
| Erro de metadados do módulo de controlador |Flashing |- |- |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Indicadores LED de unidade de disco para o bastidor principal e a inclusão EBOD
O dispositivo StorSimple tem unidades de disco localizadas no bastidor principal e a inclusão EBOD. Cada unidade de disco contém a monitorização LEDs indicador, conforme descrito nesta secção. 

Para as unidades de disco, o estado de unidade é indicado por um verde LED e um LED red-amber montada na frente de cada módulo de deteção de carrier da unidade. A ilustração seguinte mostra esses LEDs.

  ![LEDs de unidade de disco][6]

Utilize a tabela seguinte para determinar o estado de cada unidade de disco, que por sua vez, afeta o painel geral frontal LED de estado.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>Indicadores LED de unidade de disco para a inclusão EBOD
| Estado | Atividade LED de OK (verde) | Falhas LED (red-amber) | Painel de ops LED de associados |
| --- | --- | --- | --- |
| Nenhuma unidade instalado |OFF |OFF |Nenhuma |
| Unidade instalada e operacional |Piscar liga/desliga com a atividade |X |Nenhuma |
| Conjunto de identidade de dispositivo de serviços de bastidor SCSI (SES) |ON |Piscar 1 segundo em/1 segundo desativado |Nenhuma |
| Conjunto de bits de falhas de dispositivo SES |ON |ON |Lógica falha (vermelho) |
| Falha de circuito de controlo de energia |OFF |ON |Falha do módulo (vermelho) |

## <a name="audible-alarms"></a>Alarmes sonoro
Um dispositivo StorSimple contém alarmes sonoro associados com o bastidor principal e a inclusão EBOD. Um alarme sonoro se encontra no painel frontal (também conhecido como o painel do ops) de ambas as inclusões. O alarme sonoro indica quando uma condição de falha está presente. As seguintes condições ativará o alarme:  

* Falha de ventoinha ou falha
* Tensão fora do intervalo
* Ao longo ou na condição de temperatura
* Estouro térmicos
* Falha de sistema
* Falhas de lógica
* Falha de fonte de alimentação de energia
* Remoção de uma potência de refrigeração módulo (PCM)  

A tabela seguinte descreve os diversos Estados de alarme.  

### <a name="alarm-states"></a>Estados de alarme
| Estado de alarme | Ação | Ação com o botão de sem som premido |
| --- | --- | --- |
| S0 |Modo normal: silenciosa |Emitir aviso sonoro duas vezes |
| S1 |Modo de falhas: 1 segundo/1 segundo desativado |Transição para S2 ou S3 (consulte as notas de) |
| S2 |Modo de lembrar: emitir aviso sonoro intermitente |Nenhuma |
| S3 |Modo muted: silenciosa |Nenhuma |
| S4 |Modo de falhas críticas: alarme contínua |Não está disponível: desativar som não está ativa |

> [!NOTE]
> * Estado de alarme S1, se não pressionar Mute (mudo) em dois minutos, o estado transita automaticamente para S2 ou S3.  
> * Estados de alarme S1 para S4, volte a S0, depois da condição de falha está desmarcada.  
> * Estado de falha crítica S4 pode ser introduzido do qualquer outro Estado.  


Pode retirar o áudio o alarme sonoro, premindo o botão de sem som no painel de ops. A desativação automáticas ocorrerá depois de dois minutos se o comutador Mute (mudo) não é operado manualmente. Quando ficará sem áudio o alarme, irá continuar a som com o alarme sonoro de intermitente curto para indicar que ainda existe um problema. O alarme será silencioso quando todos os problemas são limpos.

A tabela seguinte descreve as várias condições de alarme.

### <a name="alarm-conditions"></a>Condições de alarme
| Estado | Gravidade | Alarme | Painel de OPS LED |
| --- | --- | --- | --- |
| Alerta PCM – queda de energia do controlador de domínio de um único PCM |Índice de falhas – sem perda de redundância |S1 |Falha do módulo |
| Alerta PCM – queda de energia do controlador de domínio de um único PCM |Índice de falhas – perda de redundância |S1 |Falha do módulo |
| Falha do PCM ventoinha |Índice de falhas – perda de redundância |S1 |Falha do módulo |
| Falhas PCM SBB módulo detetado |Índice de falhas |S1 |Falha do módulo |
| Removido do PCM |Erro de configuração |Nenhuma |Falha do módulo |
| Erro de configuração de bastidor |Falhas – crítica |S1 |Falha do módulo |
| Alerta de temperatura baixa do aviso |Aviso |S1 |Falha do módulo |
| Alerta de temperatura alta do aviso |Aviso |S1 |Falha do módulo |
| Ao longo do alarme de temperatura |Falhas – crítica |S1 |Falha do módulo |
| Falha de bus de I2C |Índice de falhas – perda de redundância |S1 |Falha do módulo |
| Erro de comunicação (I2C) de painel de ops |Falhas – crítica |S1 |Falha do módulo |
| Erro de controlador |Falhas – crítica |S1 |Falha do módulo |
| Falhas de módulo de interface SBB |Falhas – crítica |S1 |Falha do módulo |
| Falha no módulo de interface SBB – não funcional módulos restantes |Falhas – crítica |S4 |Falha do módulo |
| Módulo de interface SBB removido |Aviso |Nenhuma |Falha do módulo |
| Falha de controlo de energia de unidade |Aviso – sem perda de energia de unidade |S1 |Falha do módulo |
| Falha de controlo de energia de unidade |Falhas – crítica; Falha de energia de unidade |S1 |Falha do módulo |
| Disco removido |Aviso |Nenhuma |Falha do módulo |
| Energia insuficiente disponível |Aviso |nenhum |Falha do módulo |

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [StorSimple componentes de hardware e o estado](storsimple-8000-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png


