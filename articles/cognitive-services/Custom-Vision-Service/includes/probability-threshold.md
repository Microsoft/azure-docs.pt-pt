---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 07e7cc991f127bf4bb4f466c0108962786e45bce
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68423565"
---
Note o slider do limiar de **probabilidade** no painel esquerdo do separador **Performance.** Este é o nível de confiança que uma previsão deve ter para ser considerada correta (para efeitos de cálculo da precisão e da recolha). 

Quando interpreta as chamadas de previsão com um limiar de alta probabilidade,&mdash;tendem a devolver resultados com alta precisão em detrimento da recuperação, as classificações detetadas estão corretas, mas muitas permanecem por detetar. Um limiar de baixa&mdash;probabilidade faz o oposto que a maioria das classificações reais são detetadas, mas há mais falsos positivos dentro desse conjunto. Com isto em mente, deve definir o limiar de probabilidade de acordo com as necessidades específicas do seu projeto. Mais tarde, quando estiver a receber os resultados da previsão do lado do cliente, deve utilizar o mesmo valor de probabilidade que utilizou aqui.