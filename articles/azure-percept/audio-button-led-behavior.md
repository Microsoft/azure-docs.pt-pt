---
title: Botão de áudio Azure Percept e comportamento de LED
description: Saiba mais sobre o botão e os estados LED da Azure Percept Audio
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: fa919acb527084d19ab88b2e7895d4e6ab0b72d3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105609076"
---
# <a name="azure-percept-audio-button-and-led-behavior"></a>Botão de áudio Azure Percept e comportamento de LED

Consulte as seguintes orientações para obter informações sobre os estados do botão e LED do dispositivo Azure Percept Audio.

## <a name="button-behavior"></a>Comportamento do botão

Utilize os botões para controlar o comportamento do dispositivo.

|Estado do botão|Comportamento|
|------------|----------|
|Mudo|Pressione para silenciar/desativar a matriz do microfone. O evento do botão é acionado quando pressionado.|
|PTT/PTS|Prima PTT para contornar o estado de visualização da palavra-chave e ativar o estado de audição do comando. Pressione novamente para parar o diálogo ativo do agente e volte ao estado de observação de palavras-chave. O evento do botão é acionado quando pressionado. O PTS só funciona quando o botão é premido enquanto o agente fala, não quando o agente está a ouvir ou a pensar.|

## <a name="led-behavior"></a>Comportamento do LED

Utilize os indicadores LED para perceber em que estado se encontra o dispositivo.

|LED|Estado LED|Estado do Som do Ouvido|
|---|------------|----------------|
|L02|1x branco, estática em|Ligado |
|L02|1x branco, 0,5 Hz piscando|Autenticação em curso |
|L01 & L02 & L03|3x azul, estática em|À espera da palavra-chave|
|L01 & L02 & L03|Matriz LED piscando, 20fps |Ouvir ou falar|
|L01 & L02 & L03|Corrida de matriz led, 20fps|Pensar|
|L01 & L02 & L03|3x vermelho, estática em |Mudo|

## <a name="next-steps"></a>Passos seguintes

Para obter dicas de resolução de problemas para o seu dispositivo Azure Percept Audio, consulte este [guia](./troubleshoot-audio-accessory-speech-module.md).