---
title: Deteção de caracteres animados com Indexador de Vídeo
titleSuffix: Azure Media Services
description: Este tópico demonstra como usar a deteção de caracteres animados com o Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: f1b19f178a60671108ec32ef6fbed1afe73a0cee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96854755"
---
# <a name="animated-character-detection-preview"></a>Deteção de carateres animados (pré-visualização)

O Azure Media Services Video Indexer suporta a deteção, agrupamento e reconhecimento de personagens em conteúdo animado através da integração com [a visão personalizada dos Serviços Cognitivos.](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) Esta funcionalidade encontra-se disponível tanto através do portal como através da API.

Depois de carregar um vídeo animado com um modelo de animação específico, o Video Indexer extrai quadros-chave, deteta personagens animados nestes quadros, agruma personagem semelhante e escolhe a melhor amostra. Em seguida, envia os caracteres agrupados para a Visão Personalizada que identifica caracteres com base nos modelos em que foi treinado. 

Antes de começar a treinar o seu modelo, os caracteres são detetados sem nome. À medida que adiciona nomes e treina o modelo, o Video Indexer reconhecerá os caracteres e nomeá-los-á em conformidade.

## <a name="flow-diagram"></a>Diagrama de fluxo

O diagrama seguinte demonstra o fluxo do processo de deteção de caracteres animados.

![Diagrama de fluxo](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Contas

Dependendo de um tipo da sua conta de Indexer de Vídeo, estão disponíveis diferentes conjuntos de funcionalidades. Para obter informações sobre como ligar a sua conta ao Azure, consulte [Criar uma conta de Indexer de Vídeo ligada ao Azure](connect-to-azure.md).

* Conta de ensaio: O Indexante de Vídeo utiliza uma conta interna de Visão Personalizada para criar um modelo e conectá-lo à sua conta de Indexer de Vídeo. 
* Conta paga: liga a sua conta De Visão Personalizada à sua conta de Indexer de Vídeo (se ainda não tiver uma, tem de criar uma conta primeiro).

### <a name="trial-vs-paid"></a>Julgamento vs. pago

|Funcionalidade|Avaliação|Paga|
|---|---|---|
|Conta Visão Personalizada|Gerido nos bastidores por Video Indexer. |A sua conta De Visão Personalizada está ligada ao Indexador de Vídeo.|
|Número de modelos de animação|Um|Até 100 modelos por conta (limitação de Visão Personalizada).|
|Preparar o modelo|O Video Indexer treina o modelo para novos personagens exemplos adicionais de caracteres existentes.|O proprietário da conta treina o modelo quando está pronto para fazer alterações.|
|Opções avançadas em Visão Personalizada|Sem acesso ao portal Visão Personalizada.|Pode ajustar os modelos sozinho no portal Visão Personalizada.|

## <a name="use-the-animated-character-detection-with-portal--and-api"></a>Use a deteção de caracteres animados com portal e API

Para mais detalhes, consulte [a deteção de caracteres animados com portal e API](animated-characters-recognition-how-to.md).

## <a name="limitations"></a>Limitações

* Atualmente, a capacidade de "identificação de animação" não é suportada na região East-Asia.
* As personagens que parecem ser pequenas ou distantes no vídeo podem não ser identificadas corretamente se a qualidade do vídeo for fraca.
* A recomendação é usar um modelo por conjunto de caracteres animados (por exemplo, por uma série animada).

## <a name="next-steps"></a>Passos seguintes

[Descrição geral do Video Indexer](video-indexer-overview.md)