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
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 97209dca7d30de037dbd21f5cc145b2941060e70
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015405"
---
# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>Utilize indicadores de monitorização StorSimple para gerir o seu dispositivo


## <a name="overview"></a>Descrição geral
O seu dispositivo StorSimple inclui díodos emissores de luz (LEDs) e alarmes que pode utilizar para monitorizar os módulos e o estado geral do dispositivo StorSimple. Os indicadores de monitorização podem ser encontrados nos componentes de hardware do invólucro primário do dispositivo e no recinto EBOD. Os indicadores de monitorização podem ser LED ou alarmes sonoros.

Existem três estados LED usados para indicar o estado de um módulo: verde, verde intermitente a vermelho-âmbar, ou âmbar-vermelho.  

* Os LED verdes representam um estado de funcionamento saudável.  
* Os LED verdes intermitentes a leds vermelho-âmbar representam a presença de condições não críticas que podem exigir a intervenção do utilizador.  
* Os LEDs vermelho-âmbar indicam que há uma falha crítica presente dentro do módulo.  

O restante deste artigo descreve os vários LED indicadores de monitorização, as suas localizações no dispositivo StorSimple, o estado do dispositivo com base nos estados LED e quaisquer alarmes sonoros associados.

## <a name="front-panel-indicator-leds"></a>LED indicadores de painel frontal
O painel frontal, também conhecido como painel de *operações* ou *painel de operações,* apresenta o estado agregado de todos os módulos do sistema. O painel frontal é idêntico no recinto primário StorSimple e no recinto EBOD, e é ilustrado abaixo.  

   ![Painel frontal do dispositivo][1]

O painel frontal contém os seguintes indicadores:  

1. Botão de silenciamento
2. LED indicador de energia (verde/vermelho-âmbar)
3. LED indicador de falha do módulo (ON red-amber/OFF)
4. LED indicador de avaria lógica (ON red-amber/OFF
5. Exibição de ID da unidade  

A grande diferença entre os LED do painel frontal para o dispositivo e os do invólucro EBOD é o **Número de Identificação da Unidade** do Sistema indicado no visor LED. O ID da unidade predefinido apresentado no dispositivo é **00,** enquanto que o ID da unidade predefinido apresentado no compartimento EBOD é **01**. Isto permite-lhe diferenciar rapidamente entre o dispositivo e o recinto EBOD quando o dispositivo está ligado. Se o seu dispositivo estiver desligado, utilize as informações [fornecidas](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) por Turn on um novo dispositivo para diferenciar o dispositivo do recinto EBOD.  

## <a name="front-panel-led-status"></a>Estado do LED do painel frontal
Utilize a seguinte tabela para identificar o estado indicado pelos LEDs no painel frontal do dispositivo ou no recinto EBOD.  

| Potência do sistema | Falha no módulo | Falha lógica | Alarme | Estado |
| --- | --- | --- | --- | --- |
| Âmbar-vermelho |OFF |OFF |N/D |A energia ca perdeu, funcionando com energia de reserva, ou alimentação de CA ON e os módulos do controlador foram removidos. |
| Green |ON |ON |N/D |Potência do painel de ops no estado de teste (5s) |
| Green |OFF |OFF |N/D |Ligado, todas as funções são boas |
| Green |ON |N/D |LEDs de falha de PCM, LEDs de falha do ventilador |Qualquer falha no PCM, falha do ventilador, acima ou abaixo da temperatura |
| Green |ON |N/D |LEDs de módulos de I/O |Qualquer falha no módulo do controlador |
| Green |ON |N/D |N/D |Falha lógica do recinto |
| Green |Flash |N/D |LED de estado do módulo no módulo do controlador. LEDs de falha de PCM, LEDs de falha do ventilador |Tipo de módulo controlador desconhecido instalado, falha de autocarro I2C, erro de configuração de dados vitais do produto do módulo controlador (VPD) |

## <a name="power-cooling-module-pcm-indicator-leds"></a>LEDs indicadores do módulo de arrefecimento de energia (PCM)
Os LED indicadores do módulo de arrefecimento de energia (PCM) podem ser encontrados na parte de trás do recinto primário ou do recinto EBOD em cada módulo PCM. Este tópico discute como utilizar os seguintes LEDs para monitorizar o estado do seu dispositivo StorSimple.  

* LEDs pcm para o recinto primário
* LEDs pcm para o recinto EBOD

## <a name="pcm-leds-for-the-primary-enclosure"></a>LEDs pcm para o recinto primário
O dispositivo StorSimple tem um módulo PCM de 764W com uma bateria adicional. A seguinte ilustração mostra o painel LED para o dispositivo.  

   ![LEDs de PCM no recinto primário][2]

Lenda led:

1. Falha de energia ca
2. Falha do ventilador
3. Falha na bateria
4. PCM OK
5. Falha em DC
6. Bateria boa  

O estado do PCM é indicado no painel LED. O painel LED PCM do dispositivo tem seis LEDs. Quatro destes LEDs mostram o estado da alimentação e da ventoinha. Os dois LEDs restantes indicam o estado do módulo de bateria de reserva no PCM. Pode utilizar as seguintes tabelas para determinar o estado do PCM.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>LEDs indicadores de PCM para alimentação e ventilador
| Estado | PCM OK (verde) | Falha de CA (âmbar) | Falha do ventilador (âmbar) | DC fail (âmbar) |
| --- | --- | --- | --- | --- |
| Sem potência CA (para o recinto) |OFF |OFF |OFF |OFF |
| Sem potência CA (apenas pcm) |OFF |ON |OFF |ON |
| AC presente PCM ON - OK |ON |OFF |OFF |OFF |
| Falha no PCM (falha do ventilador) |OFF |OFF |ON |N/D |
| Falha de PCM (sobre amp, sobre tensão, sobre corrente) |OFF |ON |ON |ON |
| PCM (ventilador fora de tolerância) |ON |OFF |OFF |ON |
| Modo de espera |Piscando |OFF |OFF |OFF |
| Download de firmware pcM |OFF |Piscando |Piscando |Piscando |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>LEDs indicadores de PCM para a bateria de reserva
| Estado | Bateria boa (verde) | Falha na bateria (âmbar) |
| --- | --- | --- |
| Bateria não presente |OFF |OFF |
| Bateria presente e carregada |ON |OFF |
| Descarga de carga ou manutenção de baterias |Piscando |OFF |
| Falha "macia" da bateria (recuperável) |OFF |Piscando |
| Falha "dura" da bateria (não recuperável) |OFF |ON |
| Bateria desarmada |Piscando |OFF |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>LEDs pcm para o recinto EBOD
O recinto EBOD tem um PCM de 580W e nenhuma bateria adicional. O painel PCM para o recinto EBOD tem LED indicadores apenas para as fontes de alimentação e ventilador. A seguinte ilustração mostra estes LEDs.

   ![LEDs de PCM no recinto EBOD][3] 

Pode utilizar a tabela seguinte para determinar o estado do PCM.  

| Estado | PCM OK (verde) | Falha de CA (âmbar) | Falha do ventilador (âmbar) | DC fail (âmbar) |
| --- | --- | --- | --- | --- |
| Sem potência CA (para o recinto) |OFF |OFF |OFF |OFF |
| Sem potência CA (apenas pcm) |OFF |ON |OFF |ON |
| AC presente PCM ON – OK |ON |OFF |OFF |OFF |
| Falha no PCM (falha do ventilador) |OFF |OFF |ON |X |
| Falha de PCM (sobre amp, sobre tensão, sobre corrente |OFF |ON |ON |ON |
| PCM (ventilador fora de tolerância) |ON |OFF |OFF |ON |
| Modelo de standby |Piscando |OFF |OFF |OFF |
| Download de firmware pcM |OFF |Piscando |Piscando |Piscando |

## <a name="controller-module-indicator-leds"></a>LED indicador de módulo do controlador
O dispositivo StorSimple contém LEDs para o controlador primário e os módulos controladores EBOD.   

### <a name="monitoring-leds-for-the-primary-controller"></a>LeDs de monitorização do controlador primário
A seguinte ilustração ajuda-o a identificar os LEDs no controlador primário. (Todos os componentes estão listados para ajudar na orientação.)  

   ![LeDs de monitorização - controlador primário][4]

Utilize a seguinte tabela para determinar se o módulo do controlador está a funcionar corretamente.  

### <a name="controller-indicator-leds"></a>LED indicadores do controlador
| LED | Descrição |
| --- | --- |
| LED de ID (azul) |Indica que o módulo está a ser identificado. Se o LED azul estiver a piscar num controlador de funcionamento, o controlador é o controlador ativo e o outro é o controlador de espera. Para obter mais informações, consulte [Identificar o controlador ativo no seu dispositivo.](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device) |
| LED de avaria (âmbar) |Indica uma falha no controlador. |
| LED OK (verde) |O verde constante indica que o controlador está bem. O verde intermitente indica um erro de configuração VPD do controlador. |
| LEDs de atividade SAS (verde) |O verde constante indica uma ligação sem atividade atual. O verde intermitente indica que a ligação tem atividade contínua. |
| LEDs de estado Ethernet |O lado direito indica atividade de ligação/rede: ligação (verde estável) ativa, (verde intermitente) atividade da rede. O lado esquerdo indica velocidade de rede: (amarelo) 1000 Mb/s, (verde) 100 Mb/s e (OFF) 10 Mb/s. Dependendo do modelo do componente, esta luz pode piscar mesmo que a interface de rede não esteja ativada. |
| POST LEDs |Indica o progresso da bota quando o controlador está ligado. Se o dispositivo StorSimple não arrancar, este LED ajudará o Microsoft Support a identificar o ponto no processo de arranque em que ocorreu a falha. |

> [!IMPORTANT]
> Se o LED de avaria estiver aceso, existe um problema com o módulo do controlador que pode ser resolvido reiniciando o controlador. Entre em contato com o Microsoft Support se reiniciar o controlador não resolver este problema.  
> 
> 

### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>LeDs de monitorização para o EBOD (recinto EBOD)
Cada um dos controladores SAS EBOD de 6 Gb/s tem LED que indicam o seu estado como mostrado na ilustração seguinte.  

  ![LeDs de monitorização - Recinto EBOD][5]

Utilize a tabela seguinte para determinar se o módulo controlador EBOD está a funcionar normalmente.  

### <a name="ebod-controller-module-indicator-leds"></a>LED indicador de módulo de controlador EBOD
| Estado | Módulo de I/O OK (verde) | Falha no módulo I/O (âmbar) | Atividade portuária de acolhimento (verde) |
| --- | --- | --- | --- |
| Módulo controlador OK |ON |OFF |- |
| Falha do módulo do controlador |OFF |ON |- |
| Sem ligação externa à porta de hospedeiro |- |- |OFF |
| Conexão portuária de hospedeiro externo – sem atividade |- |- |ON |
| Conexão portuária de hospedeiro externo - atividade |- |- |Piscando |
| Erro de metadados do módulo do controlador |Piscando |- |- |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>LEDs indicadores de unidade de disco para o recinto primário e o recinto EBOD
O dispositivo StorSimple tem discos localizados tanto no recinto primário como no recinto EBOD. Cada unidade de disco contém LED indicadores de monitorização, conforme descrito nesta secção. 

Para as unidades de disco, o estado da unidade é indicado por um LED verde e um LED âmbar vermelho montado na parte da frente de cada módulo portador de acionamento. A seguinte ilustração mostra estes LEDs.

  ![LEDs de unidade de disco][6]

Utilize a seguinte tabela para determinar o estado de cada unidade de disco, o que por sua vez afeta o estado geral do LED do painel frontal.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>LEDs indicadores de unidade de disco para o recinto EBOD
| Estado | Led OK de atividade (verde) | LED de avaria (âmbar-vermelho) | LED do painel de ops associado |
| --- | --- | --- | --- |
| Nenhuma unidade instalada |OFF |OFF |Nenhum |
| Unidade instalada e operacional |Piscando ligado/desligado com atividade |X |Nenhum |
| Conjunto de identidade de dispositivo SCSI Enclosure Services (SES) |ON |Piscando 1 segundo em/1 segundo fora |Nenhum |
| Conjunto de bits de falha do dispositivo SES |ON |ON |Falha lógica (vermelho) |
| Falha do circuito de controlo de energia |OFF |ON |Falha do módulo (vermelho) |

## <a name="audible-alarms"></a>Alarmes audíveis
Um dispositivo StorSimple contém alarmes sonoros associados tanto ao recinto primário como ao recinto EBOD. Um alarme sonoro está localizado no painel frontal (também conhecido como painel de operações) de ambos os recintos. O alarme sonoro indica quando existe uma avaria. As seguintes condições ativarão o alarme:  

* Falha ou falha do ventilador
* Tensão fora do alcance
* Acima ou em condições de temperatura
* Ultrapassagem térmica
* Falha no sistema
* Falha lógica
* Falha na alimentação
* Remoção de um módulo de arrefecimento de potência (PCM)  

A tabela seguinte descreve os vários estados de alarme.  

### <a name="alarm-states"></a>Estados de alarme
| Estado de alarme | Ação | Ação com botão mudo pressionado |
| --- | --- | --- |
| S0 |Modo normal: silencioso |Bip duas vezes |
| S1 |Modo de avaria: 1 segundo em/1 segundo fora |Transição para S2 ou S3 (ver notas) |
| S2 |Modo de lembrete: bip intermitente |Nenhum |
| S3 |Modo silenciado: silencioso |Nenhum |
| S4 |Modo de avaria crítico: alarme contínuo |Não disponível: mudo não ativo |

> [!NOTE]
> * Em estado de alarme S1, se não premir o mudo dentro de 2 minutos, o estado transita automaticamente para S2 ou S3.  
> * O alarme indica que o S1 ao S4 regressa ao S0 depois de a falha estar limpa.  
> * O estado de falha crítica S4 pode ser introduzido em qualquer outro estado.  


Pode silenciar o alarme sonoro premindo o botão de silenciamento no painel de operações. O silenciamento automático ocorrerá após dois minutos se o interruptor de silenciamento não for operado manualmente. Quando o alarme for silenciado, continuará a soar com breves sinais sonoros intermitentes para indicar que ainda existe um problema. O alarme será silencioso quando todos os problemas estiverem limpos.

A tabela a seguir descreve as várias condições de alarme.

### <a name="alarm-conditions"></a>Condições de alarme
| Estado | Gravidade | Alarme | LED do painel de ops |
| --- | --- | --- | --- |
| Alerta pcm – perda de energia DC de um único PCM |Culpa – sem perda de redundância |S1 |Falha no módulo |
| Alerta pcm – perda de energia DC de um único PCM |Falha – perda de redundância |S1 |Falha no módulo |
| Falha do ventilador do PCM |Falha – perda de redundância |S1 |Falha no módulo |
| Módulo SBB detetado falha pcm |Falha |S1 |Falha no módulo |
| PCM removido |Erro de configuração |Nenhum |Falha no módulo |
| Erro de configuração do recinto |Falha – crítica |S1 |Falha no módulo |
| Alerta de temperatura de aviso baixo |Aviso |S1 |Falha no módulo |
| Alerta de temperatura de aviso elevado |Aviso |S1 |Falha no módulo |
| Sobre o alarme de temperatura |Falha – crítica |S1 |Falha no módulo |
| Falha no autocarro I2C |Falha – perda de redundância |S1 |Falha no módulo |
| Erro de comunicação do painel de ops (I2C) |Falha – crítica |S1 |Falha no módulo |
| Erro do controlador |Falha – crítica |S1 |Falha no módulo |
| Falha no módulo de interface SBB |Falha – crítica |S1 |Falha no módulo |
| Falha no módulo de interface SBB – Não restam módulos funcionais |Falha – crítica |S4 |Falha no módulo |
| Módulo de interface SBB removido |Aviso |Nenhum |Falha no módulo |
| Falha no controlo de potência da unidade |Aviso – sem perda de potência de unidade |S1 |Falha no módulo |
| Falha no controlo de potência da unidade |Falha – crítica; perda de poder de unidade |S1 |Falha no módulo |
| Unidade removida |Aviso |Nenhum |Falha no módulo |
| Energia insuficiente disponível |Aviso |nenhum |Falha no módulo |

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [os componentes e o estado do hardware StorSimple](storsimple-8000-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png


