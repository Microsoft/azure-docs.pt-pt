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
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 86e2d54cfe7559675e5e878cbc8a0df4b4eb3d0b
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87834291"
---
# <a name="what-is-custom-vision"></a>O que é a Visão Personalizada?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

A Visão Personalizada do Azure é um serviço cognitivo que lhe permite criar, implementar e melhorar os seus classificadores de imagem. Um classificador de imagem é um serviço de IA que aplica rótulos (que representam _classes)_ a imagens, de acordo com as suas características visuais. Ao contrário do serviço [de Visão De Computador,](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) a Visão Personalizada permite especificar as etiquetas a aplicar.

## <a name="what-it-does"></a>O que faz

O serviço Custom Vision utiliza um algoritmo de aprendizagem automática para aplicar rótulos em imagens. Você, o desenvolvedor, deve submeter grupos de imagens que apresentam e não têm as características em questão. Você próprio rotula as imagens no momento da submissão. Em seguida, o algoritmo treina para estes dados e calcula a sua própria precisão testando-se nessas mesmas imagens. Uma vez treinado o algoritmo, pode testar, retreinar e eventualmente usá-lo para classificar novas imagens de acordo com as necessidades da sua app. Também pode exportar o próprio modelo para utilização offline.

### <a name="classification-and-object-detection"></a>Classificação e deteção de objetos

A funcionalidade de Visão Personalizada pode ser dividida em duas funcionalidades. **A classificação de** imagem aplica uma ou mais etiquetas a uma imagem. **A deteção de objetos** é semelhante, mas também devolve as coordenadas na imagem onde a etiqueta(s) aplicada pode ser encontrada.

### <a name="optimization"></a>Otimização

O serviço Custom Vision está otimizado para reconhecer rapidamente grandes diferenças entre imagens, para que possa começar a protótipo do seu modelo com uma pequena quantidade de dados. 50 imagens por etiqueta são geralmente um bom começo. No entanto, o serviço não é o ideal para detetar diferenças subtis nas imagens (por exemplo, detetar pequenas fissuras ou amassados em cenários de garantia de qualidade).

Além disso, pode escolher entre várias variedades do algoritmo De Visão Personalizada que são otimizadas para imagens com determinados materiais &mdash; sujeitos, por exemplo, marcos ou artigos de retalho. Para mais informações, consulte o guia [Build a classifier.](getting-started-build-a-classifier.md)

## <a name="what-it-includes"></a>O que inclui

O Serviço de Visão Personalizada está disponível como um conjunto de SDKs nativos, bem como através de uma interface baseada na Web sobre a [home page de Visão Personalizada](https://customvision.ai/). Pode criar, testar e treinar um modelo através de qualquer interface ou usar ambos juntos.

![Home page da Visão Personalizada numa janela do browser Chrome](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Privacidade e segurança dos dados

Tal como acontece com todos os Serviços Cognitivos, os desenvolvedores que usam o serviço De Visão Personalizada devem estar cientes das políticas da Microsoft sobre os dados dos clientes. Consulte a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) no Microsoft Trust Center para saber mais.

## <a name="next-steps"></a>Passos seguintes

Siga o guia [Build a classifier](getting-started-build-a-classifier.md) para começar a usar a Visão Personalizada na web, ou preencha um [tutorial de classificação de Imagem](quickstarts/image-classification.md) para implementar um cenário básico em código.
