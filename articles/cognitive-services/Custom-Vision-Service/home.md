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
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 829921ae5600873fdc8f2946ae3ff355bffb06a8
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978599"
---
# <a name="what-is-custom-vision"></a>O que é a Visão Personalizada?

A Visão Personalizada é um serviço cognitivo que lhe permite criar, implementar e melhorar os seus classificadores de imagens. Um classificador de imagem é um serviço de ia que aplica rótulos (que representam _classes_) a imagens, de acordo com suas características visuais. Ao contrário do serviço de [Pesquisa Visual computacional](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) , visão personalizada permite que você determine os rótulos a serem aplicados.

## <a name="what-it-does"></a>O que faz

O serviço de Visão Personalizada usa um algoritmo de aprendizado de máquina para aplicar rótulos às imagens. Você, o desenvolvedor, deve enviar grupos de imagens que apresentam e não têm as características em questão. Você mesmo rotula as imagens no momento do envio. Em seguida, o algoritmo treina esses dados e calcula sua própria precisão testando-se nessas mesmas imagens. Depois que o algoritmo é treinado, você pode testar, treinar novamente e, eventualmente, usá-lo para classificar novas imagens de acordo com as necessidades de seu aplicativo. Também pode exportar o próprio modelo para utilização offline.

### <a name="classification-and-object-detection"></a>Classificação e deteção de objetos

A funcionalidade de Visão Personalizada pode ser dividida em duas funcionalidades. A **classificação de imagem** aplica um ou mais rótulos a uma imagem. A **detecção de objeto** é semelhante, mas também retorna as coordenadas na imagem em que os rótulos aplicados podem ser encontrados.

### <a name="optimization"></a>Otimização

O serviço Visão Personalizada está otimizado para reconhecer rapidamente as principais diferenças entre imagens, pelo que pode começar a prototipar o seu modelo com uma pequena quantidade de dados. 50 imagens por rótulo geralmente são um bom começo. No entanto, o serviço não é ideal para detectar diferenças sutis em imagens (por exemplo, detectar pequenas rachaduras ou recuos em cenários de garantia de qualidade).

Além disso, você pode escolher entre várias variedades do Visão Personalizada algoritmo otimizado para imagens com determinado material de assunto&mdash;por exemplo, pontos de referência ou itens de varejo. Para obter mais informações, consulte a guia [criar um classificador](getting-started-build-a-classifier.md) .

## <a name="what-it-includes"></a>O que inclui

O Serviço de Visão Personalizada está disponível como um conjunto de SDKs nativos, bem como através de uma interface baseada na Web sobre a [home page de Visão Personalizada](https://customvision.ai/). Você pode criar, testar e treinar um modelo por meio de uma interface ou usar ambos juntos.

![Home page da Visão Personalizada numa janela do browser Chrome](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Privacidade e segurança de dados

Assim como acontece com todos os serviços cognitivas, os desenvolvedores que usam o serviço de Visão Personalizada devem estar cientes das políticas da Microsoft nos dados do cliente. Consulte a [página serviços cognitivas](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na central de confiabilidade da Microsoft para saber mais.

## <a name="next-steps"></a>Passos seguintes

Siga o guia [criar um classificador](getting-started-build-a-classifier.md) para começar a usar o visão personalizada na Web ou concluir um [tutorial de classificação de imagem](csharp-tutorial.md) para implementar um cenário básico no código.
