---
title: Codificar de transformação personalizada usando os serviços de multimédia v3 .NET - Azure | Documentos da Microsoft
description: Este tópico mostra como utilizar serviços de multimédia do Azure v3 para codificar uma transformação personalizados usando o .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 03/11/2019
ms.author: juliako
ms.openlocfilehash: 848da2996b71b137c6112225c9bef7e93b457c7d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57837240"
---
# <a name="how-to-encode-with-a-custom-transform-by-using-net"></a>Como codificar com uma transformação personalizados com o .NET

Ao codificar com os serviços de multimédia do Azure, pode começar a utilizar rapidamente com um dos pré-visualizando incorporada recomendado com base nas práticas recomendadas do setor, como demonstrado na [ficheiros de transmissão em fluxo](stream-files-tutorial-with-api.md) tutorial. Também pode criar um personalizado predefinido para seus requisitos específicos de cenário ou dispositivo de destino.

## <a name="considerations"></a>Considerações

Ao criar configurações predefinidas personalizadas, aplicam-se as seguintes considerações:

* Todos os valores para a altura e largura no conteúdo de AVC tem de ser um múltiplo de 4.
* Em serviços de multimédia do Azure v3, todas as velocidades de transmissão de codificação são em bits por segundo. Isso é diferente de predefinições com nossas APIs v2, que utilizado kilobits por segundo, como a unidade. Por exemplo, se a velocidade de transmissão no v2 foi especificada como 128 (kilobits por segundo), na v3-la seria definido como 128000 (bits por segundo).

## <a name="prerequisites"></a>Pré-requisitos 

[Criar uma conta de Media Services](create-account-cli-how-to.md). <br/>Lembre-se de que não se esqueça de que o nome do grupo de recursos e o nome da conta dos serviços de multimédia. 

## <a name="download-the-sample"></a>Transferir o exemplo

Clone um repositório do GitHub que contém o exemplo completo do .NET Core para o seu computador com o seguinte comando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
O exemplo predefinido personalizado está localizado no [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) pasta.

## <a name="create-a-transform-with-a-custom-preset"></a>Criar uma transformação com uma configuração predefinida personalizada 

Ao criar um novo [transformar](https://docs.microsoft.com/rest/api/media/transforms), tem de especificar o que deseja produzir como saída. O parâmetro necessário é um objeto [TransformOutput](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#transformoutput), conforme apresentado no código abaixo. Cada **TransformOutput** contém uma **Predefinição**. A **Predefinição** descreve as instruções passo a passo das operações de processamento de áudio e/ou vídeo que estão a ser utilizadas para gerir o **TransformOutput** pretendido. O seguinte procedimento **TransformOutput** cria definições personalizadas de codec e a camada da saída.

Ao criar uma [Transformação](https://docs.microsoft.com/rest/api/media/transforms), primeiro deve verificar se já existe uma com o método **Get**, conforme apresentado no código que se segue. Em serviços de multimédia v3, **Obtenha** métodos em entidades retornar **nulo** se a entidade não existe (uma verificação de maiúsculas e minúsculas no nome).

### <a name="example"></a>Exemplo

O exemplo seguinte define um conjunto de saídas que Desejamos sejam gerados quando essa transformação é utilizada. Primeiro, vamos adicionar uma camada de AacAudio para a codificação de áudio e de duas camadas de H264Video para a codificação de vídeo. As camadas de vídeo, podemos atribuir etiquetas para que possa ser utilizados em nomes de ficheiros de saída. Em seguida, queremos que a saída para incluir também miniaturas. No exemplo abaixo, podemos especificar imagens em formato PNG, gerado a 50% da resolução de vídeo de entrada e em três carimbos de data / - {25%, 50%, 75} de comprimento do vídeo de entrada. Por último, podemos especificar o formato para os ficheiros de saída – um para vídeo + áudio e outro para as miniaturas. Uma vez que temos várias H264Layers, temos de usar macros que produzem nomes exclusivos por camada. Podemos utilizar um `{Label}` ou `{Bitrate}` macro, o exemplo mostra o primeiro.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Passos Seguintes

[Ficheiros de transmissão em fluxo](stream-files-tutorial-with-api.md) 
