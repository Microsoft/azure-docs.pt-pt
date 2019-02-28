---
title: Codificar de transformação personalizados usando os serviços de multimédia v3 - Azure | Documentos da Microsoft
description: Este tópico mostra como utilizar serviços de multimédia do Azure v3 para codificar uma transformação personalizada.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 02/26/2019
ms.author: juliako
ms.openlocfilehash: a0843e6c641ded75ded01da4c8a54cd4c0f48ee1
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957475"
---
# <a name="how-to-encode-with-a-custom-transform"></a>Como codificar com uma transformação personalizados

Ao codificar com os serviços de multimédia do Azure, pode começar a utilizar rapidamente com um dos pré-visualizando incorporada recomendado com base nas práticas recomendadas do setor, como demonstrado na [ficheiros de transmissão em fluxo](stream-files-tutorial-with-api.md) tutorial, ou pode optar por criar um personalizado configuração predefinida para os seus requisitos específicos de cenário ou dispositivo de destino. 

> [!Note]
> Em serviços de multimédia do Azure v3, todas as taxas de bits de codificação são em bits por segundo. Isto é diferente da v2 REST que predefine Media Encoder Standard. Por exemplo, a velocidade de transmissão no v2 teria de ser especificada como 128, mas em v3 seria 128000.

## <a name="download-the-sample"></a>Transferir o exemplo

Clone um repositório do GitHub que contém o exemplo completo do .NET Core para o seu computador com o seguinte comando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
O exemplo predefinido personalizado está localizado no [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) pasta.

## <a name="create-a-transform-with-a-custom-preset"></a>Criar uma transformação com uma configuração predefinida personalizada 

Ao criar um novo [transformar](https://docs.microsoft.com/rest/api/media/transforms), tem de especificar o que deseja produzir como saída. O parâmetro necessário é um objeto [TransformOutput](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#transformoutput), conforme apresentado no código abaixo. Cada **TransformOutput** contém uma **Predefinição**. A **Predefinição** descreve as instruções passo a passo das operações de processamento de áudio e/ou vídeo que estão a ser utilizadas para gerir o **TransformOutput** pretendido. O seguinte procedimento **TransformOutput** cria definições personalizadas de codec e a camada da saída.

Ao criar uma [Transformação](https://docs.microsoft.com/rest/api/media/transforms), primeiro deve verificar se já existe uma com o método **Get**, conforme apresentado no código que se segue.  Em serviços de multimédia v3, **Obtenha** métodos em entidades retornar **nulo** se a entidade não existe (uma verificação de maiúsculas e minúsculas no nome).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Passos Seguintes

[Ficheiros de transmissão em fluxo](stream-files-tutorial-with-api.md) 
