---
title: O que é a Visão Personalizada do Azure?
titlesuffix: Azure Cognitive Services
description: Aprenda a utilizar o serviço de Visão Personalizada para criar classificadores de imagem personalizada na cloud do Azure.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 50935aca20af931eec63717921ef7a73267d2373
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58350475"
---
# <a name="what-is-azure-custom-vision"></a>O que é a Visão Personalizada do Azure?

Visão de personalizado do Azure é um serviço cognitivo que lhe permite criar, implementar e melhorar a suas própria classificadores de imagem. Um classificador de imagem é um serviço de IA que aplica etiquetas (que representam _classes_) para imagens, de acordo com suas características visual. Ao contrário do [de imagem digitalizada](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) visão service, personalizado permite-lhe determinar as etiquetas para aplicar.

## <a name="what-it-does"></a>O que faz

O serviço de visão personalizada utiliza um algoritmo de aprendizagem para aplicar etiquetas a imagens. , O desenvolvedor, tem de submeter grupos de imagens que apresentam um e não têm as características em questão. Coloca uma etiqueta as imagens por conta própria no momento da submissão. Em seguida, o algoritmo prepara a estes dados e calcula seu próprio precisão ao testar em si nessas imagens mesmo. Assim que o algoritmo é preparado, pode testar, voltar a preparar e, eventualmente, utilizá-lo para classificar as novas imagens de acordo com as necessidades da sua aplicação. Também pode exportar o próprio modelo para utilização offline.

### <a name="classification-and-object-detection"></a>Classificação e deteção de objetos

A funcionalidade de Visão Personalizada pode ser dividida em duas funcionalidades. **Classificação de imagens** aplica-se uma ou mais etiquetas para uma imagem. **Deteção de objeto** é semelhante, mas também devolve as coordenadas na imagem em que pode ser encontrado o label(s) aplicada.

### <a name="optimization"></a>Otimização

O serviço de visão personalizada está otimizado para reconhece rapidamente as principais diferenças entre as imagens. Isto permite-lhe para iniciar a criação de protótipos seu modelo com uma pequena quantidade de dados. 50 imagens por etiqueta geralmente são um bom começo. Isto significa que, no entanto, o serviço não é ideal para a deteção de diferenças subtis nas imagens (por exemplo, detetar falhas pequenas ou dents em cenários de garantia de qualidade).

Além disso, pode escolher entre diversas variedades do algoritmo de visão personalizada que estão otimizadas para imagens com determinados material de assunto&mdash;por exemplo, os pontos de referência ou itens de varejo. Veja o guia [Criar um classificador](getting-started-build-a-classifier.md) para obter mais informações sobre os mesmos.

## <a name="what-it-includes"></a>O que inclui

O Serviço de Visão Personalizada está disponível como um conjunto de SDKs nativos, bem como através de uma interface baseada na Web sobre a [home page de Visão Personalizada](https://customvision.ai/). Pode criar, testar e formar um modelo através de qualquer interface ou utilizar ambos em conjunto.

![Home page da Visão Personalizada numa janela do browser Chrome](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Segurança e privacidade dos dados

Como com todos os serviços cognitivos, os desenvolvedores que usam o serviço de visão personalizada devem estar ciente das políticas da Microsoft em dados do cliente. Consulte a [página dos serviços cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) no Microsoft Trust Center para obter mais informações.

## <a name="next-steps"></a>Passos Seguintes

Siga os [criar um classificador](getting-started-build-a-classifier.md) guia de introdução à utilização de visão personalizada na web ou concluir um [tutorial de classificação de imagem](csharp-tutorial.md) de implementar um cenário básico em código.
