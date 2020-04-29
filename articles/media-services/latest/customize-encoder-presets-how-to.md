---
title: Transformação personalizada em código utilizando Media Services v3 .NET - Azure Microsoft Docs
description: Este tópico mostra como usar o Azure Media Services v3 para codificar uma transformação personalizada usando .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/03/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ebe701032e6416b3e007a28db62f5a8235bb1bb1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80068038"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>Como codificar com uma transformação personalizada - .NET

Ao codificar com a Azure Media Services, pode começar rapidamente com uma das predefinições incorporadas recomendadas com base nas melhores práticas da indústria, como demonstrado no tutorial de [ficheiros streaming.](stream-files-tutorial-with-api.md) Também pode construir um preset personalizado para direcionar os seus requisitos específicos para o seu cenário ou dispositivo.

## <a name="considerations"></a>Considerações

Ao criar predefinições personalizadas, aplicam-se as seguintes considerações:

* Todos os valores para a altura e largura no teor de AVC devem ser múltiplos de 4.
* No Azure Media Services v3, todos os bitrates de codificação estão em bits por segundo. Isto é diferente dos presets com as nossas V2 APIs, que usavam quilobits/segundo como unidade. Por exemplo, se o bitrate em v2 fosse especificado como 128 (quilobits/segundo), em v3 seria fixado em 128000 (bits/segundo).

## <a name="prerequisites"></a>Pré-requisitos 

[Criar uma conta dos Media Services](create-account-cli-how-to.md)

## <a name="download-the-sample"></a>Transferir o exemplo

Clone um repositório GitHub que contenha a amostra completa .NET Core para a sua máquina utilizando o seguinte comando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
A amostra predefinida personalizada está localizada na pasta [EncodeCustomTransform.](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/)

## <a name="create-a-transform-with-a-custom-preset"></a>Criar uma transformação com um preset personalizado 

Ao criar um novo [Transform,](https://docs.microsoft.com/rest/api/media/transforms)precisa especificar o que pretende que produza como uma saída. O parâmetro necessário é um objeto [TransformOutput](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#transformoutput), conforme apresentado no código abaixo. Cada **TransformOutput** contém uma **Predefinição**. O **Preset** descreve as instruções passo-a-passo das operações de processamento de vídeo e/ou áudio que devem ser utilizadas para gerar a **TransformOutput**desejada . O seguinte **TransformOutput** cria definições personalizadas de codec e de saída de camadas.

Ao criar uma [Transformação](https://docs.microsoft.com/rest/api/media/transforms), primeiro deve verificar se já existe uma com o método **Get**, conforme apresentado no código que se segue. Nos Serviços de Media v3, **obtenha** métodos sobre entidades que reempenem **o nulo** se a entidade não existir (uma verificação de caso insensível no nome).

### <a name="example"></a>Exemplo

O exemplo que se segue define um conjunto de saídas que queremos ser geradas quando este Transform é usado. Primeiro adicionamos uma camada AacAudio para a codificação de áudio e duas camadas H264Video para a codificação de vídeo. Nas camadas de vídeo, atribuímos etiquetas para que possam ser usadas nos nomes dos ficheiros de saída. Em seguida, queremos que a saída também inclua miniaturas. No exemplo abaixo especificamos imagens em formato PNG, geradas a 50% da resolução do vídeo de entrada, e em três selos de tempo - {25%, 50%, 75} do comprimento do vídeo de entrada. Por último, especificamos o formato para os ficheiros de saída - um para vídeo + áudio e outro para as miniaturas. Como temos vários H264Layers, temos que usar macros que produzem nomes únicos por camada. Podemos usar `{Label}` um `{Bitrate}` ou macro, o exemplo mostra o primeiro.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Passos seguintes

[Transmitir ficheiros em fluxo](stream-files-tutorial-with-api.md) 
