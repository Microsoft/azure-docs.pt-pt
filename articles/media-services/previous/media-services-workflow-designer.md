---
title: Criar fluxos de trabalho de codificação avançadas com | de designers de fluxo de trabalho Microsoft Docs
description: Saiba como criar fluxos de trabalho de codificação avançados com o Workflow Designer.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 004815f2-0761-4706-87a1-675ba36e0322
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: anilmur
ms.reviewer: juliako;johndeu
ms.openlocfilehash: 744d302afd21e6521fb17bf7bef6e68d21fb8372
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639390"
---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Criar Fluxos de Trabalho de Codificação Avançados com o Estruturador de Fluxos de Trabalho

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Descrição Geral
O **Workflow Designer** é uma ferramenta de ambiente de trabalho do Windows que é usada para conceber e construir fluxos de trabalho personalizados para codificação com o fluxo de **trabalho Premium Media Encoder**.
Ao utilizar a potência da ferramenta de design de fluxo de trabalho, pode projetar e criar fluxos de trabalho complexos que irão funcionar no **Media Encoder Premium.**  

Os fluxos de trabalho podem incluir a lógica de decisão do cliente e a ramificação com base nas propriedades do ficheiro de origem de entrada. Pode criar fluxos de trabalho com propriedades e valores dinâmicos supervaveis para tornar até as tarefas de codificação mais complexas fáceis de repetir e personalizar na nuvem.

Exemplo de fluxos de trabalho que pode criar incluem:

* Fluxos de trabalho baseados em decisão que inspecionam o conteúdo de origem para resolução e codificam apenas as faixas de saída desejadas.  Isto é útil eliminando as faixas desperdiçadas que seriam geradas aumentando inadvertidamente o conteúdo da fonte.
* Vários ficheiros de entrada podem ser usados para suportar legendas, sobreposições e costurar conteúdo. 

Esta ferramenta também pode ser usada para modificar qualquer um dos [nossos fluxos de trabalho publicados.](media-services-workflow-designer.md#existing_workflows) 

> [!NOTE]
> Para obter a sua cópia da ferramenta Workflow Designer, mepd@microsoft.com contacte.

Uma vez criado um ficheiro de fluxo de trabalho, pode ser carregado como Um Ativo e, em seguida, ser usado para codificar ficheiros de mídia. Para obter informações sobre como codificar com o Fluxo de **Trabalho Premium Media Encoder** utilizando **.NET**, consulte codificação avançada com o fluxo de [trabalho premium media Encoder](media-services-encode-with-premium-workflow.md).

## <a name="modify-existing-workflows"></a><a id="existing_workflows"></a>Modificar os fluxos de trabalho existentes
Os [fluxos de trabalho publicados por defeito](media-services-workflow-designer.md#existing_workflows) podem ser modificados utilizando a ferramenta de design. Pode obter os ficheiros de fluxo de trabalho [predefinidos aqui.](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/media-services/previous/media-services-encode-with-premium-workflow.md) A pasta também contém a descrição destes ficheiros.

Os seguintes vídeos demonstram como usar o designer.

### <a name="day-1--getting-started"></a>Dia 1 - Começar
Capas de vídeo do dia 1:

* Visão geral do designer
* Fluxos básicos de trabalho – "Hello World"
* Criação de vários ficheiros MP4 de saída para utilização com o streaming Azure Media Services

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>Dia 2
Capas de vídeo do dia 2:

* Diferentes cenários de ficheiros Source – manuseamento de áudio
* Fluxos de trabalho com Lógica avançada
* Estágios de gráfico

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>Dia 3
Capas de vídeo do dia 3:

* Scripting dentro de workflows/Plantas
* Restrições com o atual Codificar
* Perguntas e Respostas

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="need-help"></a>Precisa de ajuda?

Você pode abrir um bilhete de apoio navegando para [novo pedido de apoio](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-step"></a>Passo seguinte
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consulte também
[Vídeos de formação de designers de fluxo de trabalho Azure Premium](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

