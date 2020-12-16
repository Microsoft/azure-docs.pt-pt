---
title: O que é a Visão Personalizada?
titleSuffix: Azure Cognitive Services
description: Aprenda a usar o serviço Azure Custom Vision para construir classificadores de imagem de IA personalizados e detetores de objetos na nuvem Azure.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: reconhecimento de imagem, identificador de imagem, app de reconhecimento de imagem, visão personalizada
ms.openlocfilehash: 7274fed961c4bbac8841e1c741f54031614cea5c
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562995"
---
# <a name="what-is-custom-vision"></a>O que é a Visão Personalizada?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

A Azure Custom Vision é um serviço de reconhecimento de imagem que permite construir, implementar e melhorar os seus próprios identificadores de imagem. Um identificador de imagem aplica etiquetas (que representam classes ou objetos) a imagens, de acordo com as suas características visuais. Ao contrário do serviço [de Visão Computacional,](../computer-vision/overview.md) a Visão Personalizada permite especificar as etiquetas e treinar modelos personalizados para os detetar.

## <a name="what-it-does"></a>O que faz

O serviço Custom Vision utiliza um algoritmo de aprendizagem automática para analisar imagens. Você, o desenvolvedor, submete grupos de imagens que apresentam e não têm as características em questão. Você próprio rotula as imagens no momento da submissão. Em seguida, o algoritmo treina para estes dados e calcula a sua própria precisão testando-se nessas mesmas imagens. Uma vez treinado o algoritmo, pode testar, retreinar e eventualmente usá-lo na sua app de reconhecimento de imagem para classificar novas imagens. Também pode exportar o próprio modelo para utilização offline.

### <a name="classification-and-object-detection"></a>Classificação e deteção de objetos

A funcionalidade de Visão Personalizada pode ser dividida em duas funcionalidades. **A classificação de** imagem aplica uma ou mais etiquetas a uma imagem. **A deteção de objetos** é semelhante, mas também devolve as coordenadas na imagem onde a etiqueta(s) aplicada pode ser encontrada.

### <a name="optimization"></a>Otimização

O serviço Custom Vision está otimizado para reconhecer rapidamente grandes diferenças entre imagens, para que possa começar a protótipo do seu modelo com uma pequena quantidade de dados. 50 imagens por etiqueta são geralmente um bom começo. No entanto, o serviço não é o ideal para detetar diferenças subtis nas imagens (por exemplo, detetar pequenas fissuras ou amassados em cenários de garantia de qualidade).

Além disso, pode escolher entre várias variedades do algoritmo De Visão Personalizada que são otimizadas para imagens com determinados materiais &mdash; sujeitos, por exemplo, marcos ou artigos de retalho. Para obter mais informações, consulte o [Coser um classificador](getting-started-build-a-classifier.md) ou [construa guias de detetores de objetos.](get-started-build-detector.md)

## <a name="what-it-includes"></a>O que inclui

O Serviço de Visão Personalizada está disponível como um conjunto de SDKs nativos, bem como através de uma interface baseada na web no site da [Visão Personalizada.](https://customvision.ai/) Pode criar, testar e treinar um modelo através de qualquer interface ou usar ambos juntos.

![Site da Visão Personalizada em uma janela do navegador Chrome](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Privacidade e segurança dos dados

Tal como acontece com todos os Serviços Cognitivos, os desenvolvedores que usam o serviço De Visão Personalizada devem estar cientes das políticas da Microsoft sobre os dados dos clientes. Consulte a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) no Microsoft Trust Center para saber mais.

## <a name="next-steps"></a>Passos seguintes

Siga o guia [Build a classifier](getting-started-build-a-classifier.md) para começar a usar a Visão Personalizada no portal web, ou preencha um [arranque rápido da biblioteca](quickstarts/image-classification.md) do cliente para implementar os cenários básicos em código.