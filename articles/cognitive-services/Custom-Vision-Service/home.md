---
title: O que é a Visão Personalizada?
titleSuffix: Azure Cognitive Services
description: Aprenda a utilizar o serviço de Visão Personalizada para criar classificadores de imagem personalizada na cloud do Azure.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: bdb2b31ee3abb30e6e7339f641b778ff5dc522b0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81403795"
---
# <a name="what-is-custom-vision"></a>O que é a Visão Personalizada?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

A Visão Personalizada do Azure é um serviço cognitivo que lhe permite criar, implementar e melhorar os seus classificadores de imagem. Um classificador de imagem é um serviço de IA que aplica etiquetas (que representam _classes_) às imagens, de acordo com as suas características visuais. Ao contrário do serviço [Computer Vision,](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) a Custom Vision permite especificar as etiquetas a aplicar.

## <a name="what-it-does"></a>O que faz

O serviço Custom Vision utiliza um algoritmo de aprendizagem automática para aplicar etiquetas em imagens. Você, o desenvolvedor, deve submeter grupos de imagens que apresentam e não têm as características em questão. Você mesmo rotula as imagens no momento da submissão. Em seguida, o algoritmo treina para estes dados e calcula a sua própria precisão testando-se nessas mesmas imagens. Uma vez treinado o algoritmo, pode testar, treinar e eventualmente usá-lo para classificar novas imagens de acordo com as necessidades da sua aplicação. Também pode exportar o próprio modelo para utilização offline.

### <a name="classification-and-object-detection"></a>Classificação e deteção de objetos

A funcionalidade de Visão Personalizada pode ser dividida em duas funcionalidades. **A classificação da imagem** aplica um ou mais rótulos a uma imagem. **A deteção** de objetos é semelhante, mas também devolve as coordenadas na imagem onde o ou os rótulos aplicados podem ser encontrados.

### <a name="optimization"></a>Otimização

O serviço Custom Vision está otimizado para reconhecer rapidamente grandes diferenças entre imagens, para que possa começar a protótipo do seu modelo com uma pequena quantidade de dados. 50 imagens por rótulo são geralmente um bom começo. No entanto, o serviço não é ideal para detetar diferenças subtis nas imagens (por exemplo, detetar pequenas fissuras ou amassadas em cenários de garantia de qualidade).

Além disso, pode escolher entre várias variedades do algoritmo Custom Vision&mdash;que são otimizadas para imagens com determinado material sujeito, por exemplo, marcos ou itens de retalho. Para mais informações, consulte o [Guia de Classificação.](getting-started-build-a-classifier.md)

## <a name="what-it-includes"></a>O que inclui

O Serviço de Visão Personalizada está disponível como um conjunto de SDKs nativos, bem como através de uma interface baseada na Web sobre a [home page de Visão Personalizada](https://customvision.ai/). Pode criar, testar e treinar um modelo através de qualquer interface ou utilizar ambos juntos.

![Home page da Visão Personalizada numa janela do browser Chrome](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Privacidade e segurança de dados

Tal como acontece com todos os Serviços Cognitivos, os desenvolvedores que utilizam o serviço Custom Vision devem estar cientes das políticas da Microsoft em dados de clientes. Consulte a página de [Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) no Microsoft Trust Center para saber mais.

## <a name="next-steps"></a>Passos seguintes

Siga o guia [De classificação](getting-started-build-a-classifier.md) Para começar a usar visão personalizada na web, ou complete um tutorial de [classificação de imagem](csharp-tutorial.md) para implementar um cenário básico em código.
