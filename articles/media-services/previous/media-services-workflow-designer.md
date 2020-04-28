---
title: Criar fluxos de trabalho de codificação avançada com workflow designer [ Microsoft Docs
description: Saiba como criar fluxos de trabalho avançados de codificação com o Workflow Designer.
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
ms.date: 03/20/2019
ms.author: anilmur
ms.reviewer: juliako;johndeu
ms.openlocfilehash: 4dceb558532305c6d2e84563e25ab05508423090
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "72801950"
---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Criar Fluxos de Trabalho de Codificação Avançados com o Estruturador de Fluxos de Trabalho  
## <a name="overview"></a>Descrição geral
O **Workflow Designer** é uma ferramenta de desktop windows que é usada para projetar e construir fluxos de trabalho personalizados para codificação com **media Encoder Premium Workflow**.
Ao utilizar o poder da ferramenta de designer de fluxo de trabalho, pode projetar e criar fluxos de trabalho complexos que serão executados em **Media Encoder Premium**.  

Os fluxos de trabalho podem incluir a lógica da decisão do cliente e a ramificação com base nas propriedades do ficheiro fonte de entrada. Pode criar fluxos de trabalho com propriedades demasiado divertáveis e valores dinâmicos para tornar até as tarefas de codificação mais complexas fáceis de repetir e personalizar na nuvem.

Exemplo de fluxos de trabalho que pode criar incluem:

* Fluxos de trabalho baseados na decisão que inspecionam o conteúdo de origem para resolução e codificam apenas as vias de saída desejadas.  Isto é útil eliminando as faixas desperdiçadas que seriam geradas através da subida inadvertida do conteúdo da fonte.
* Vários ficheiros de entrada podem ser usados para suportar legendas, sobreposições e costurar conteúdo. 

Esta ferramenta também pode ser usada para modificar qualquer um dos nossos fluxos de [trabalho publicados.](media-services-workflow-designer.md#existing_workflows) 

> [!NOTE]
> Para obter a sua cópia da ferramenta mepd@microsoft.comWorkflow Designer, contacte .

Uma vez criado um ficheiro de fluxo de trabalho, pode ser carregado como um Ativo e, em seguida, ser usado para codificar ficheiros de mídia. Para obter informações sobre como codificar com **media Encoder Premium Workflow** utilizando **.NET,** consulte Codificação avançada com fluxo de [trabalho Premium Media Encoder](media-services-encode-with-premium-workflow.md).

## <a name="modify-existing-workflows"></a><a id="existing_workflows"></a>Modificar os fluxos de trabalho existentes
Os [fluxos](media-services-workflow-designer.md#existing_workflows) de trabalho publicados por defeito podem ser modificados utilizando a ferramenta de design. Pode obter aqui os [ficheiros](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)de fluxo de trabalho padrão . A pasta também contém a descrição destes ficheiros.

Os seguintes vídeos demonstram como usar o designer.

### <a name="day-1--getting-started"></a>Dia 1 - Começar
Capas de vídeo do dia 1:

* Visão geral do designer
* Fluxos básicos de trabalho - "Hello World"
* Criação de vários ficheiros MP4 de saída para uso com o streaming Azure Media Services

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>Dia 2
Capas de vídeo do dia 2:

* Vários cenários de ficheiros Source – manipulação de áudio
* Fluxos de trabalho com lógica avançada
* Etapas de gráficos

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>Dia 3
Capas de vídeo do dia 3:

* Scripting dentro de Workflows/Blueprints
* Restrições com o atual Codificador
* Perguntas e Respostas

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="need-help"></a>Precisa de ajuda?

Você pode abrir um bilhete de apoio navegando para [novo pedido](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) de apoio

## <a name="next-step"></a>Passo seguinte
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Veja também
[Azure Premium Encoder Workflow Designer Training Videos](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

