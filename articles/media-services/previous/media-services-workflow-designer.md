---
title: Criar fluxos de trabalho de codificação avançados com o Designer de Fluxo de Trabalho | Microsoft Docs
description: Saiba mais sobre como criar fluxos de trabalho de codificação avançados com o Designer de Fluxo de Trabalho.
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
ms.openlocfilehash: 246a1461973dba129a5b1d12be8a09703eb99267
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "69015981"
---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Criar Fluxos de Trabalho de Codificação Avançados com o Estruturador de Fluxos de Trabalho  
## <a name="overview"></a>Descrição geral
O **Designer de fluxo de trabalho** é uma ferramenta de área de trabalho do Windows que é usada para criar e criar fluxos de trabalho personalizados para codificação com **Media Encoder Premium Workflow**.
Usando o poder da ferramenta designer de fluxo de trabalho, você pode projetar e criar fluxos de trabalho complexos que serão executados no **Media Encoder Premium**.  

Os fluxos de trabalho podem incluir a lógica de decisão do cliente e a ramificação com base nas propriedades do arquivo de origem de entrada. Você pode criar fluxos de trabalho com propriedades substituíveis e valores dinâmicos para tornar até mesmo as tarefas de codificação mais complexas fáceis de repetir e personalizar na nuvem.

Os fluxos de trabalho de exemplo que você pode criar incluem:

* Fluxos de trabalho baseados em decisão que inspecionam o conteúdo de origem para resolução e codificam apenas as faixas de saída desejadas.  Isso é útil ao eliminar as faixas desperdiçadas que seriam geradas por meio do dimensionamento inadvertidamente do conteúdo de origem.
* Vários arquivos de entrada podem ser usados para dar suporte a legendas, sobreposições e unir conteúdo. 

Essa ferramenta também pode ser usada para modificar qualquer um dos nossos [fluxos de trabalho publicados](media-services-workflow-designer.md#existing_workflows). 

> [!NOTE]
> Para obter sua cópia da ferramenta de Designer de Fluxo de Trabalho, entre em mepd@microsoft.comcontato com.

Depois que um arquivo de fluxo de trabalho é criado, ele pode ser carregado como um ativo e, em seguida, usado para codificar arquivos de mídia. Para obter informações sobre como codificar com **Media Encoder Premium Workflow** usando o **.net**, consulte [Codificação avançada com Media Encoder Premium Workflow](media-services-encode-with-premium-workflow.md).

## <a id="existing_workflows"></a>Modificar fluxos de trabalho existentes
Os [fluxos de trabalho publicados](media-services-workflow-designer.md#existing_workflows) padrão podem ser modificados usando a ferramenta de designer. Você pode obter os arquivos de fluxo de trabalho padrão [aqui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). A pasta também contém a descrição desses arquivos.

Os vídeos a seguir demonstram como usar o designer.

### <a name="day-1--getting-started"></a>Dia 1 – Introdução
O vídeo do dia 1 abrange:

* Visão geral do designer
* Fluxos de trabalho básicos – "Olá, Mundo"
* Criando vários arquivos MP4 de saída para uso com o streaming dos serviços de mídia do Azure

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>Dia 2
O vídeo do dia 2 abrange:

* Diferentes cenários de arquivo de origem – lidando com áudio
* Fluxos de trabalho com lógica avançada
* Estágios do grafo

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>Dia 3
O vídeo do dia 3 abrange:

* Criando scripts dentro de fluxos de trabalho/plantas
* Restrições com o codificador atual
* P & A

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="next-step"></a>Passo seguinte
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

Se você precisar de suporte ou tiver dúvidas sobre a criação de fluxos de trabalho personalizados na ferramenta designer de fluxo de mepd@microsoft.comtrabalho, envie um email para.

## <a name="see-also"></a>Consultar Também
[Vídeos de treinamento Designer de Fluxo de Trabalho o codificador Premium do Azure](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

