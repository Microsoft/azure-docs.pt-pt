---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 07e7cc991f127bf4bb4f466c0108962786e45bce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "96018383"
---
Note o slider **do limiar de probabilidade** no painel esquerdo do **separador Desempenho.** Este é o nível de confiança que uma previsão deve ter para ser considerada correta (para efeitos de cálculo da precisão e da recordação). 

Quando interpreta chamadas de previsão com um limiar de alta probabilidade, tendem a devolver resultados com alta precisão em detrimento da recuperação &mdash; das classificações detetadas, mas muitas permanecem indetectadas. Um limiar de baixa probabilidade faz com que &mdash; a maioria das classificações reais sejam detetadas, mas há mais falsos positivos dentro desse conjunto. Com isto em mente, deve definir o limiar de probabilidade de acordo com as necessidades específicas do seu projeto. Mais tarde, quando estiver a receber os resultados da previsão do lado do cliente, deve utilizar o mesmo valor limiar de probabilidade que utilizou aqui.