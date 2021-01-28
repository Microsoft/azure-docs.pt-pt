---
title: Codificar a transformação personalizada .NET
description: Este tópico mostra como usar o Azure Media Services v3 para codificar uma transformação personalizada usando .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: c8c9f1deb1b776a818269909b3feb19f00a2c356
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954281"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>Como codificar com uma transformação personalizada - .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ao codificar com o Azure Media Services, pode começar rapidamente com uma das predefinições recomendadas com base nas melhores práticas da indústria, como demonstra o tutorial de [ficheiros streaming.](stream-files-tutorial-with-api.md) Também pode construir uma predefinição personalizada para direcionar o seu cenário específico ou requisitos do dispositivo.

## <a name="considerations"></a>Considerações

Ao criar predefinições personalizadas, aplicam-se as seguintes considerações:

* Todos os valores para altura e largura no conteúdo AVC devem ser múltiplos de 4.
* No Azure Media Services v3, todos os bitrates codificadores estão em bits por segundo. Isto é diferente das predefinições com as nossas APIs v2, que usavam quilobits/segundo como unidade. Por exemplo, se o bitrate em v2 fosse especificado como 128 (quilobits/segundo), em v3 seria definido para 128000 (bits/segundo).

## <a name="prerequisites"></a>Pré-requisitos 

[Criar uma conta dos Media Services](./create-account-howto.md)

## <a name="download-the-sample"></a>Transferir o exemplo

Clone um repositório GitHub que contenha a amostra completa do núcleo .NET à sua máquina utilizando o seguinte comando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
A amostra pré-configurada personalizada encontra-se na pasta [EncodeCustomTransform.](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/)

## <a name="create-a-transform-with-a-custom-preset"></a>Criar uma transformação com uma predefinição personalizada 

Ao criar uma nova [Transform,](/rest/api/media/transforms)tem de especificar o que pretende que produza como saída. O parâmetro necessário é um objeto [TransformOutput](/rest/api/media/transforms/createorupdate#transformoutput), conforme apresentado no código abaixo. Cada **TransformOutput** contém uma **Predefinição**. O **Predefinição** descreve as instruções passo a passo das operações de processamento de vídeo e/ou áudio que devem ser utilizadas para gerar o **TransformOutput** desejado . O seguinte **TransformOutput** cria definições personalizadas de saída de codec e camada.

Ao criar uma [Transformação](/rest/api/media/transforms), primeiro deve verificar se já existe uma com o método **Get**, conforme apresentado no código que se segue. Nos Serviços de Comunicação Social v3, **Obtenha** métodos sobre entidades que **retornem nulos** se a entidade não existir (uma verificação caso-insensível no nome).

### <a name="example"></a>Exemplo

O exemplo a seguir define um conjunto de saídas que queremos ser geradas quando esta Transformação é usada. Primeiro adicionamos uma camada AacAudio para a codificação de áudio e duas camadas H264Video para a codificação de vídeo. Nas camadas de vídeo, atribuímos etiquetas para que possam ser usadas nos nomes dos ficheiros de saída. Em seguida, queremos que a saída também inclua miniaturas. No exemplo abaixo especificamos imagens em formato PNG, geradas a 50% da resolução do vídeo de entrada, e em três vezes osmps - {25%, 50%, 75%} do comprimento do vídeo de entrada. Por último, especificamos o formato para os ficheiros de saída - um para vídeo + áudio e outro para as miniaturas. Como temos vários H264Layers, temos de usar macros que produzem nomes únicos por camada. Podemos usar um `{Label}` ou `{Bitrate}` macro, o exemplo mostra o primeiro.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Passos seguintes

[Transmitir ficheiros em fluxo](stream-files-tutorial-with-api.md) 
