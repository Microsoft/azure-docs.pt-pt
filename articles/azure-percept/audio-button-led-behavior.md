---
title: Botão de áudio Azure Percept e comportamento de LED
description: Saiba mais sobre o botão e os estados LED da Azure Percept Audio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: c7ee2289680bb50fabb8e6eb2a3ea0466bd58afb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663434"
---
# <a name="azure-percept-audio-button-and-led-behavior"></a>Botão de áudio Azure Percept e comportamento de LED

Consulte as seguintes orientações para obter informações sobre os estados do botão e LED do Azure Percept Audio.

## <a name="button-behavior"></a>Comportamento do botão

Pode utilizar os botões para controlar o comportamento do dispositivo.

|Estado do botão|  Comportamento|
|------------|----------|
|Mudo|  Pressione para silenciar/desativar o microfone-matriz. O evento do botão é acionado quando pressionado.|
|PTT/PTS|   Prima PTT para contornar o estado de visualização da palavra-chave e ativar o estado de audição do comando. Pressione novamente para parar o diálogo ativo do agente e volte ao estado de deteção de palavras-chave.|

## <a name="led-behavior"></a>Comportamento do LED

Pode utilizar indicadores LED para perceber em que estado se encontra o dispositivo.

|LED|   Estado LED|  Estado do Som do Ouvido|
|---|------------|----------------| 
|L02|   1x branco, estática em |Ligado |
|L02|   1x branco, 0,5 Hz piscando|  Autenticação em curso |
|L01 & L02 & L03|   3x azul, estática em|     Palavra-chave detetada|
|L01 & L02 & L03|   Matriz LED piscando, 20fps | Ouvir ou falar|
|L01 & L02 & L03|   Corrida de matriz led, 20fps|    Pensar|
|L01 & L02 & L03|   3x vermelho, estática em | Mudo|

## <a name="next-steps"></a>Passos seguintes

Para obter dicas de resolução de problemas para o seu dispositivo Azure Percept Audio, consulte este [guia](./troubleshoot-audio-accessory-speech-module.md).