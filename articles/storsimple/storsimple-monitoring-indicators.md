---
title: Indicadores de monitorização StorSimple / Microsoft Docs
description: Descreve os díodos emissores de luz (LEDs) e os alarmes sonoros utilizados para monitorizar o estado do dispositivo StorSimple.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60630658"
---
# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>Utilize indicadores de monitorização StorSimple para gerir o seu dispositivo


## <a name="overview"></a>Descrição geral
O seu dispositivo StorSimple inclui díodos emissores de luz (LEDs) e alarmes que pode utilizar para monitorizar os módulos e o estado geral do dispositivo StorSimple. Os indicadores de monitorização podem ser encontrados nos componentes de hardware do recinto primário do dispositivo e no recinto EBOD. Os indicadores de monitorização podem ser LEDs ou alarmes sonoros.

Existem três estados LED usados para indicar o estado de um módulo: verde, verde intermitente para vermelho-âmbar, ou vermelho-âmbar.  

* Os LED verdes representam um estado de funcionamento saudável.  
* Os LED verde-âmbar-vermelho intermitentes representam a presença de condições não críticas que podem exigir a intervenção do utilizador.  
* Os LEDs vermelho-âmbar indicam que existe uma falha crítica dentro do módulo.  

O restante deste artigo descreve os vários LED indicadores de monitorização, as suas localizações no dispositivo StorSimple, o estado do dispositivo com base nos estados LED e quaisquer alarmes sonoros associados.

## <a name="front-panel-indicator-leds"></a>LEDIndicador do painel frontal
O painel frontal, também conhecido como painel de *operações* ou *painel de operações,* apresenta o estado agregado de todos os módulos do sistema. O painel frontal é idêntico nas primárias StorSimple e no recinto EBOD, e é ilustrado abaixo.  

   ![Painel frontal do dispositivo][1]

O painel frontal contém os seguintes indicadores:  

1. Botão de silenciamento
2. LED indicador de potência (verde/vermelho-âmbar)
3. LED indicador de avaria do módulo (ON red-amber/OFF)
4. LED indicador de falha lógica (ON red-amber/OFF
5. Ecrã id da unidade  

A grande diferença entre os LEDs do painel frontal para o dispositivo e os do recinto EBOD é o Número de **Identificação** da Unidade do Sistema mostrado no visor LED. O ID da unidade predefinido apresentado no dispositivo é **de 00,** enquanto o ID da unidade predefinido apresentado no recinto EBOD é **01**. Isto permite-lhe diferenciar rapidamente entre o dispositivo e o recinto EBOD quando o dispositivo está ligado. Se o seu dispositivo estiver desligado, utilize as informações fornecidas [por sua vez num novo dispositivo](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) para diferenciar o dispositivo do recinto ebod.  

## <a name="front-panel-led-status"></a>Estado LED do painel frontal
Utilize o quadro seguinte para identificar o estado indicado pelos LEDs no painel frontal para o dispositivo ou para o recinto EBOD.  

| Poder do sistema | Falha no módulo | Falha lógica | Alarme | Estado |
| --- | --- | --- | --- | --- |
| Âmbar-vermelho |OFF |OFF |N/D |A energia CA perdida, operando com energia de reserva, ou energia CA ON e os módulos do controlador foram removidos. |
| Verde |ON |ON |N/D |Poder do painel ops no estado de teste (5s) |
| Verde |OFF |OFF |N/D |Poder ligado, todas as funções boas |
| Verde |ON |N/D |LEDs de falha de PCM, LEDs de falha de ventilador |Qualquer falha de PCM, falha do ventilador, mais ou menos de temperatura |
| Verde |ON |N/D |Módulo I/O LEDs |Qualquer falha no módulo controlador |
| Verde |ON |N/D |N/D |Falha lógica do recinto |
| Verde |Flash |N/D |LED de estado do módulo no módulo do controlador. LEDs de falha de PCM, LEDs de falha de ventilador |Módulo de controlador desconhecido tipo instalado, falha de autocarro I2C, erro de configuração de dados vitais do produto do módulo controlador (VPD) |

## <a name="power-cooling-module-pcm-indicator-leds"></a>LeDs indicador de módulo de arrefecimento de energia (PCM)
Os LED indicadores do módulo de arrefecimento de energia (PCM) podem ser encontrados na parte de trás do recinto primário ou do recinto EBOD em cada módulo PCM. Este tópico discute como utilizar os seguintes LEDs para monitorizar o estado do seu dispositivo StorSimple.  

* LEDs pcm para o recinto primário
* LEDs pcm para o recinto EBOD

## <a name="pcm-leds-for-the-primary-enclosure"></a>LEDs pcm para o recinto primário
O dispositivo StorSimple tem um módulo PCM de 764W com uma bateria adicional. A seguinte ilustração mostra o painel LED do dispositivo.  

   ![LEDs pcm no recinto primário][2]

Lenda led:

1. Falha de energia do AC
2. Falha do ventilador
3. Falha na bateria
4. PCM OK
5. Falha em DC
6. Bateria boa  

O estado do PCM está indicado no painel LED. O painel LED PCM do dispositivo tem seis LED. Quatro destes LEDs apresentam o estado da alimentação e da ventoinha. Os dois LEDs restantes indicam o estado do módulo da bateria de reserva no PCM. Pode utilizar as seguintes tabelas para determinar o estado do PCM.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>LEDs indicador DE PCM para alimentação e ventilador
| Estado | PCM OK (verde) | Falha de AC (âmbar) | Falha da ventoinha (âmbar) | Falha em DC (âmbar) |
| --- | --- | --- | --- | --- |
| Sem energia CA (para o recinto) |OFF |OFF |OFF |OFF |
| Sem potência ca (apenas pcm) |OFF |ON |OFF |ON |
| AC apresentar PCM ON - OK |ON |OFF |OFF |OFF |
| Falha no PCM (falha do ventilador) |OFF |OFF |ON |N/D |
| Falha de PCM (sobre amp, sobre tensão, sobre corrente) |OFF |ON |ON |ON |
| PCM (ventilador fora da tolerância) |ON |OFF |OFF |ON |
| Modo de espera |Piscando |OFF |OFF |OFF |
| Download de firmware PCM |OFF |Piscando |Piscando |Piscando |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>LEDs indicador DE PCM para a bateria de reserva
| Estado | Bateria boa (verde) | Falha na bateria (âmbar) |
| --- | --- | --- |
| Bateria não presente |OFF |OFF |
| Bateria presente e carregada |ON |OFF |
| Carga da bateria ou descarga de manutenção |Piscando |OFF |
| Falha "macia" da bateria (recuperável) |OFF |Piscando |
| Falha "dura" da bateria (não recuperável) |OFF |ON |
| Bateria desarmada |Piscando |OFF |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>LEDs pcm para o recinto EBOD
O recinto EBOD tem um PCM de 580W e nenhuma bateria adicional. O painel PCM para o recinto EBOD tem LED indicador apenas para as fontes de alimentação e a ventoinha. A seguinte ilustração mostra estes LEDs.

   ![LEDs pcm no recinto ebod][3] 

Pode utilizar a tabela seguinte para determinar o estado do PCM.  

| Estado | PCM OK (verde) | Falha de AC (âmbar) | Falha da ventoinha (âmbar) | Falha em DC (âmbar) |
| --- | --- | --- | --- | --- |
| Sem energia CA (para o recinto) |OFF |OFF |OFF |OFF |
| Sem potência ca (apenas pcm) |OFF |ON |OFF |ON |
| AC apresentar PCM ON - OK |ON |OFF |OFF |OFF |
| Falha no PCM (falha do ventilador) |OFF |OFF |ON |X |
| Falha de PCM (sobre amp, sobre tensão, sobre corrente |OFF |ON |ON |ON |
| PCM (ventilador fora da tolerância) |ON |OFF |OFF |ON |
| Modelo de espera |Piscando |OFF |OFF |OFF |
| Download de firmware PCM |OFF |Piscando |Piscando |Piscando |

## <a name="controller-module-indicator-leds"></a>LEDs indicador de módulo de controlador
O dispositivo StorSimple contém LEDs para o controlador primário e os módulos do controlador EBOD.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Monitorização de LEDs para o controlador primário
A ilustração que se segue ajuda a identificar os LEDs no controlador primário. (Todos os componentes estão listados para ajudar na orientação.)  

   ![LEDs de monitorização - controlador primário][4]

Utilize a tabela seguinte para determinar se o módulo do controlador está a funcionar corretamente.  

### <a name="controller-indicator-leds"></a>LEDs indicador de controlador
| LED | Descrição |
| --- | --- |
| LED ID (azul) |Indica que o módulo está a ser identificado. Se o LED azul estiver a piscar num controlador de corrente, então o controlador é o controlador ativo e o outro é o controlador de espera. Para mais informações, consulte [Identificar o controlador ativo no seu dispositivo](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| LED de falha (âmbar) |Indica uma falha no controlador. |
| LED OK (verde) |O verde firme indica que o controlador está bem. O verde intermitente indica um erro de configuração VPD do controlador. |
| LeDs de atividade sas (verde) |O verde estável indica uma ligação sem atividade corrente. O verde intermitente indica que a ligação tem atividade contínua. |
| LeDs de estado ethernet |O lado direito indica a atividade de ligação/rede: ligação (verde constante) ativa, (verde intermitente) atividade de rede. O lado esquerdo indica a velocidade da rede: (amarelo) 1000 Mb/s, (verde) 100 Mb/s e (OFF) 10 Mb/s. Dependendo do modelo do componente, esta luz pode piscar mesmo que a interface de rede não esteja ativada. |
| POST LEDs |Indica o progresso da bota quando o controlador está ligado. Se o dispositivo StorSimple não arrancar, este LED ajudará o Microsoft Support a identificar o ponto do processo de arranque no qual ocorreu a falha. |

> [!IMPORTANT]
> Se o LED de avaria estiver aceso, existe um problema com o módulo do controlador que pode ser resolvido reiniciando o controlador. Contacte o Microsoft Support se reiniciar o controlador não resolver este problema.  
> 
> 

### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>LeDs de monitorização para o EBOD (recinto EBOD)
Cada um dos controladores SAS EBOD de 6 Gb/s tem LEDs que indicam o seu estado como mostrado na ilustração seguinte.  

  ![LEDs de monitorização - Recinto EBOD][5]

Utilize a tabela seguinte para determinar se o módulo de comando EBOD está a funcionar normalmente.  

### <a name="ebod-controller-module-indicator-leds"></a>LeDs indicador de módulo de controlador EBOD
| Estado | Módulo I/O OK (verde) | Falha do módulo I/O (âmbar) | Atividade portuária de acolhimento (verde) |
| --- | --- | --- | --- |
| Módulo de controlador OK |ON |OFF |- |
| Falha no módulo do controlador |OFF |ON |- |
| Nenhuma ligação externa à porta do hospedeiro |- |- |OFF |
| Conexão externa do porto de acolhimento - sem atividade |- |- |ON |
| Conexão externa do porto de acolhimento - atividade |- |- |Piscando |
| Erro de metadados do módulo de controlador |Piscando |- |- |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>LED indicador de acionamento de disco para o recinto primário e o recinto EBOD
O dispositivo StorSimple tem unidades de disco localizadas tanto no recinto primário como no recinto EBOD. Cada unidade de disco contém LED indicador estonteantes de monitorização, conforme descrito nesta secção. 

Para as unidades de disco, o estado de acionamento é indicado por um LED verde e um LED vermelho-âmbar montado na parte frontal de cada módulo de porta-accionamento. A seguinte ilustração mostra estes LEDs.

  ![LEDs de acionamento de disco][6]

Utilize a tabela seguinte para determinar o estado de cada unidade de disco, que por sua vez afeta o estado led do painel frontal.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>LED indicador de acionamento de disco para o recinto EBOD
| Estado | LED OK de atividade (verde) | LED de falha (vermelho-âmbar) | LED do painel de operações associado |
| --- | --- | --- | --- |
| Sem unidade instalada |OFF |OFF |Nenhuma |
| Unidade instalada e operacional |Piscando/desligado com atividade |X |Nenhuma |
| Conjunto de identidade de dispositivo scsi enclosure Services (SES) |ON |Piscando 1 segundo em/1 segundo de desconto |Nenhuma |
| Conjunto de bits de avaria do dispositivo SES |ON |ON |Falha lógica (vermelho) |
| Falha no circuito de controlo de energia |OFF |ON |Falha do módulo (vermelho) |

## <a name="audible-alarms"></a>Alarmes audíveis
Um dispositivo StorSimple contém alarmes sonoros associados tanto ao recinto primário como ao recinto EBOD. Um alarme sonoro está localizado no painel frontal (também conhecido como painel de operações) de ambos os recintos. O alarme sonoro indica quando existe uma falha. As seguintes condições ativarão o alarme:  

* Falha ou falha do ventilador
* Tensão fora de alcance
* Sobre ou em condições de temperatura
* Sobreatropelamento térmico
* Falha do sistema
* Falha lógica
* Falha de alimentação
* Remoção de um módulo de arrefecimento de energia (PCM)  

A tabela seguinte descreve os vários estados de alarme.  

### <a name="alarm-states"></a>Estados de alarme
| Estado de alarme | Ação | Ação com botão de silenciamento premido |
| --- | --- | --- |
| S0 |Modo normal: silencioso |Bip duas vezes |
| S1 |Modo de avaria: 1 segundo em/1 segundo de desconto |Transição para S2 ou S3 (ver notas) |
| S2 |Modo de relembrar: sinal sonoro intermitente |Nenhuma |
| S3 |Modo silencioso: silencioso |Nenhuma |
| S4 |Modo de avaria crítica: alarme contínuo |Não disponível: mudo não ativo |

> [!NOTE]
> * Em estado de alarme S1, se não pressionar o mudo dentro de 2 minutos, o estado passa automaticamente para S2 ou S3.  
> * O alarme indica que S1 a S4 regressam à S0 depois da condição de avaria ser desmarcada.  
> * Estado de falha crítica S4 pode ser introduzido em qualquer outro estado.  


Pode silenciar o alarme sonoro premindo o botão de silenciamento no painel de operações. Osilencia automática ocorrerá após dois minutos se o interruptor de silenciamento não for acionado manualmente. Quando o alarme é silenciado, continuará a soar com pequenos sinalsonoro intermitente para indicar que ainda existe um problema. O alarme será silencioso quando todos os problemas estiverem limpos.

A tabela seguinte descreve as várias condições de alarme.

### <a name="alarm-conditions"></a>Condições de alarme
| Estado | Gravidade | Alarme | LED do painel ops |
| --- | --- | --- | --- |
| Alerta PCM – perda de energia dc de um único PCM |Culpa – sem perda de redundância |S1 |Falha no módulo |
| Alerta PCM – perda de energia dc de um único PCM |Culpa – perda de redundância |S1 |Falha no módulo |
| Falha do ventilador PCM |Culpa – perda de redundância |S1 |Falha no módulo |
| Módulo SBB detetado falha pcm |Falha |S1 |Falha no módulo |
| PCM removido |Erro de configuração |Nenhuma |Falha no módulo |
| Erro de configuração do recinto |Falha – crítica |S1 |Falha no módulo |
| Alerta de baixa temperatura de alerta |Aviso |S1 |Falha no módulo |
| Alerta de alta temperatura de alerta |Aviso |S1 |Falha no módulo |
| Sobre o alarme de temperatura |Falha – crítica |S1 |Falha no módulo |
| Falha no autocarro I2C |Culpa – perda de redundância |S1 |Falha no módulo |
| Erro de comunicação do painel ops (I2C) |Falha – crítica |S1 |Falha no módulo |
| Erro do controlador |Falha – crítica |S1 |Falha no módulo |
| Falha no módulo de interface SBB |Falha – crítica |S1 |Falha no módulo |
| Falha no módulo de interface SBB – Não restam módulos de funcionamento |Falha – crítica |S4 |Falha no módulo |
| Módulo de interface SBB removido |Aviso |Nenhuma |Falha no módulo |
| Falha no controlo de potência |Aviso – sem perda de potência de condução |S1 |Falha no módulo |
| Falha no controlo de potência |Falha – crítica; perda de potência de condução |S1 |Falha no módulo |
| Unidade removida |Aviso |Nenhuma |Falha no módulo |
| Energia insuficiente disponível |Aviso |nenhuma |Falha no módulo |

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [os componentes de hardware StorSimple e](storsimple-8000-monitor-hardware-status.md)o estado .

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png


