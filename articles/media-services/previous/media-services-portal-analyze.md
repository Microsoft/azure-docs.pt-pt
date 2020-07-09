---
title: Analise os seus meios de comunicação utilizando o portal Azure ! Microsoft Docs
description: Este tópico discute como processar os seus meios de comunicação com processadores de media Media Analytics (MPs) utilizando o portal Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 0e49e90209c7337081458b7c214d27b37d3b4da1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74462608"
---
# <a name="analyze-your-media-using-the-azure-portal"></a>Analise os seus meios de comunicação utilizando o portal Azure 

> [!IMPORTANT]
> Reveja os [planos](media-services-analytics-overview.md#retirement-plans) de reforma de alguns processadores de media.

## <a name="overview"></a>Descrição geral
A Azure Media Services Analytics é uma coleção de componentes de fala e visão (à escala de empresas, conformidade, segurança e alcance global) que facilitam a produção de informações atuais a partir dos seus ficheiros de vídeo. Para uma visão geral mais detalhada da Azure Media Services Analytics consulte [este](media-services-analytics-overview.md) tópico. 

Este tópico discute como processar os seus meios de comunicação com processadores de media Media Analytics (MPs) utilizando o portal Azure. Os MPs media Analytics produzem ficheiros MP4 ou ficheiros JSON. Se um processador de mídia produziu um ficheiro MP4, descarregue progressivamente o ficheiro. Se um processador de mídia produziu um ficheiro JSON, descarregue o ficheiro do armazenamento da bolha Azure. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Escolha um ativo que queira analisar
1. No [portal do Azure](https://portal.azure.com/) selecione a sua conta de Serviços de Multimédia do Azure.
2. Na janela **Definições**, selecione **Elementos**.  
   
    ![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. Selecione o ativo que pretende analisar e prima o botão **Analisar.**
   
    ![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. No **ativo de mídia processa com** a janela Media Analytics, selecione o processador. 
   
    O resto do artigo explica porquê e como usar cada processador. 
5. Pressione **Criar** para iniciar um trabalho.

## <a name="azure-media-indexer"></a>Azure Media Indexer
O processador de mídia **Azure Media Indexer** permite-lhe tornar os ficheiros de mídia e conteúdos pesjáveis, bem como gerar faixas de legendas fechadas. Esta secção dá alguns detalhes sobre as opções que especifica para este MP.

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Linguagem
A linguagem natural a ser reconhecida no ficheiro multimédia. Por exemplo, inglês ou espanhol. 

### <a name="captions"></a>Legendas
Pode escolher um formato de legenda que será gerado a partir do seu conteúdo. Um trabalho de indexação pode gerar ficheiros de legendas fechados nos seguintes formatos:  
 
* **TTML**
* **WebVTT**

Os ficheiros de legenda fechada (CC) nestes formatos podem ser utilizados para tornar os ficheiros de áudio e vídeo acessíveis a pessoas com deficiência auditiva.

### <a name="keywords"></a>Palavras-chave
Selecione esta opção se quiser gerar um ficheiro XML de palavras-chave. Este ficheiro contém palavras-chave extraídas do conteúdo da fala, com informações de frequência e compensação.

### <a name="job-name"></a>Nome da tarefa
Um nome amigável que lhe permite identificar o trabalho. [Este](media-services-portal-check-job-progress.md) artigo descreve como se pode monitorizar o progresso de um trabalho. 

### <a name="output-file"></a>Ficheiro de saída
Um nome amigável que permite identificar o conteúdo da saída. 

### <a name="speed"></a>Velocidade
Especifique a velocidade para acelerar o vídeo de entrada. A saída é uma rendição estabilizada e com caducidade temporal do vídeo de entrada.

### <a name="job-name"></a>Nome da tarefa
Um nome amigável que lhe permite identificar o trabalho. [Este](media-services-portal-check-job-progress.md) artigo descreve como se pode monitorizar o progresso de um trabalho. 

### <a name="output-file"></a>Ficheiro de saída
Um nome amigável que permite identificar o conteúdo da saída. 

## <a name="azure-media-face-detector"></a>Azure Media Face Detector
O processador **de mídia Azure Media Face Detetor** (MP) permite-lhe contar, rastrear movimentos e até avaliar a participação e a reação do público através de expressões faciais. Este serviço contém duas características: 

* **Deteção de rostos**
  
    A deteção facial encontra e rastreia rostos humanos dentro de um vídeo. Várias faces podem ser detetadas e posteriormente rastreadas à medida que se deslocam, com os metadados de hora e localização devolvidos num ficheiro JSON. Durante o rastreio, tentará dar uma identificação consistente à mesma face enquanto a pessoa se desloca no ecrã, mesmo que esteja obstruída ou deixe brevemente a moldura.
  
  > [!NOTE]
  > Estes serviços não realizam reconhecimento facial. Um indivíduo que sai da moldura ou fica obstruído por muito tempo receberá uma nova identificação quando regressar.
  > 
  > 
* **Deteção de emoções**
  
    A Deteção de Emoção é um componente opcional do Processador de Meios de Deteção facial que devolve a análise de múltiplos atributos emocionais dos rostos detetados, incluindo felicidade, tristeza, medo, raiva e muito mais. 

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Modo de deteção
Um dos seguintes modos pode ser utilizado pelo processador:

* deteção de rosto
* por deteção de emoções de rosto
* deteção de emoções agregadas

### <a name="job-name"></a>Nome da tarefa
Um nome amigável que lhe permite identificar o trabalho. [Este](media-services-portal-check-job-progress.md) artigo descreve como se pode monitorizar o progresso de um trabalho. 

### <a name="output-file"></a>Ficheiro de saída
Um nome amigável que permite identificar o conteúdo da saída. 

## <a name="azure-media-motion-detector"></a>Azure Media Motion Detector
O processador **de mídia Azure Media Motion Detetor** (MP) permite identificar eficientemente secções de interesse dentro de um vídeo longo e sem incidentes. A deteção de movimentos pode ser usada em imagens de câmaras estáticas para identificar secções do vídeo onde ocorre movimento. Gera um ficheiro JSON contendo um metadados com timetamps e a região limite onde o evento ocorreu.

Direcionada para feeds de vídeo de segurança, esta tecnologia é capaz de categorizar o movimento em eventos relevantes e falsos positivos, como sombras e mudanças de iluminação. Isto permite-lhe gerar alertas de segurança a partir de feeds de câmaras sem ser spam com eventos irrelevantes intermináveis, ao mesmo tempo que é capaz de extrair momentos de interesse de vídeos de vigilância extremamente longos.

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure Media Video Thumbnails
Este processador pode ajudá-lo a criar resumos de vídeos longos selecionando automaticamente snippets interessantes a partir do vídeo de origem. Isto é útil quando você quer fornecer uma visão geral rápida do que esperar em um vídeo longo. Para obter informações detalhadas e exemplos, consulte [use miniaturas de vídeo Azure Media para criar uma sumização de vídeo](media-services-video-summarization.md)

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Nome da tarefa
Um nome amigável que lhe permite identificar o trabalho. [Este](media-services-portal-check-job-progress.md) artigo descreve como se pode monitorizar o progresso de um trabalho. 

### <a name="output-file"></a>Ficheiro de saída
Um nome amigável que permite identificar o conteúdo da saída. 

## <a name="azure-media-content-moderator"></a>Moderador de Conteúdo de Azure Media
Este processador ajuda a detetar potenciais conteúdos adultos e picantes em vídeos. O processador deteta automaticamente imagens e quadros-chave no vídeo. Ele marca os quadros-chave para possíveis conteúdos adultos ou picantes, e sugere revisões com base em limiares padrão. Para obter informações detalhadas e exemplos, consulte [o Moderador de Conteúdo de Azure Media para moderar vídeos](media-services-content-moderation.md)

![Vídeos moderados](./media/media-services-portal-analyze/media-services-portal-analyze-content-moderator.PNG)

### <a name="version"></a>Versão 
Use "2.0".

### <a name="mode"></a>Modo
A versão 2.0 ignora a `Mode` definição.

## <a name="next-steps"></a>Próximos passos
Ver caminhos de aprendizagem dos Serviços de Comunicação Social.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
