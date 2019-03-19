---
title: Criar fluxos de trabalho de codificação avançados com o estruturador de fluxo de trabalho | Documentos da Microsoft
description: Saiba mais sobre como criar fluxos de trabalho de codificação avançados com o estruturador de fluxo de trabalho.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.assetid: 004815f2-0761-4706-87a1-675ba36e0322
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: juliako;johndeu;anilmur
ms.openlocfilehash: fb24c856138bb9e59d9cb3ea07c9182454e6463a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58080206"
---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Criar Fluxos de Trabalho de Codificação Avançados com o Estruturador de Fluxos de Trabalho  
## <a name="overview"></a>Descrição geral
O **Designer de fluxo de trabalho** é uma ferramenta de ambiente de trabalho do Windows que é utilizada para conceber e criar fluxos de trabalho personalizados para a codificação com **Media Encoder Premium Workflow**.
Ao utilizar o poder da ferramenta de designer de fluxo de trabalho, pode conceber e criar fluxos de trabalho complexos que serão executadas no **Premium de codificador de multimédia**.  

Fluxos de trabalho podem incluir a lógica de decisão de cliente e ramificação com base nas propriedades do ficheiro de origem de entrada. Pode criar fluxos de trabalho com propriedades substituíveis e os valores dinâmicos para facilitar até mesmo as tarefas de codificação mais complexas repetir e personalizar na cloud.

Os fluxos de trabalho de exemplo que pode criar incluem:

* Decisão com base em fluxos de trabalho que inspecionar o conteúdo de origem para a resolução e codificar apenas as faixas de saída desejada.  Isso é útil, eliminando os desperdício roteiros que seriam gerados pelo der o conteúdo de origem inadvertidamente.
* Vários ficheiros de entrada podem ser utilizados para suportar legendas, sobreposições e clips conteúdo em conjunto. 

Essa ferramenta também pode ser usada para modificar qualquer um dos nossos [publicados fluxos de trabalho](media-services-workflow-designer.md#existing_workflows). 

> [!NOTE]
> Para obter sua cópia da ferramenta de Designer de fluxo de trabalho, entre em contato com mepd@microsoft.com.

Assim que for criado um ficheiro de fluxo de trabalho, ele pode ser carregado como um recurso e, em seguida, ser utilizado para ficheiros de multimédia de codificação. Para obter informações sobre como codificar com **Media Encoder Premium Workflow** usando **.NET**, consulte [codificação avançada com o Media Encoder Premium Workflow](media-services-encode-with-premium-workflow.md).

## <a id="existing_workflows"></a>Modificar fluxos de trabalho existentes
A predefinição [publicados fluxos de trabalho](media-services-workflow-designer.md#existing_workflows) podem ser modificados usando a ferramenta. Pode obter a predefinição arquivos de fluxo de trabalho [aqui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). A pasta também contém a descrição destes ficheiros.

Os vídeos seguintes demonstram como usar o designer.

### <a name="day-1--getting-started"></a>Dia 1 – Introdução
Dia 1 vídeo abrange:

* Descrição geral do Designer
* Fluxos de trabalho básicos – "Hello World"
* Criação de vários ficheiros MP4 para utilização com serviços de multimédia do Azure de transmissão em fluxo de saída

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>Dia 2
Vídeo de 2 dias abrange:

* Vários cenários de ficheiros de origem – processamento de áudio
* Fluxos de trabalho com lógica avançada
* Fases de gráfico

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>Dia 3
Vídeo de 3 dias abrange:

* Dentro de fluxos de trabalho/esquemas de scripts
* Restrições com o codificador atual
* AS PERGUNTAS E RESPOSTAS

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="next-step"></a>Passo seguinte
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

Se precisar de suporte ou tiver dúvidas sobre a criação de fluxos de trabalho personalizados nas ferramentas de designer de fluxo de trabalho, envie um email para mepd@microsoft.com.

## <a name="see-also"></a>Consultar Também
[Vídeos de treinamento de Designer de fluxo de trabalho de codificador Premium do Azure](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

