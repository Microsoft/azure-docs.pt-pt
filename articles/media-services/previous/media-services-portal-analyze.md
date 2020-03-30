---
title: Analise os seus meios através do portal Azure [ Microsoft Docs
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74462608"
---
# <a name="analyze-your-media-using-the-azure-portal"></a>Analise os seus meios de comunicação usando o portal Azure 

> [!IMPORTANT]
> Reveja os [planos](media-services-analytics-overview.md#retirement-plans) de reforma de alguns processadores de mídia.

## <a name="overview"></a>Descrição geral
O Azure Media Services Analytics é uma coleção de componentes de fala e visão (à escala empresarial, conformidade, segurança e alcance global) que facilitam às organizações e empresas obter insights atuais dos seus ficheiros de vídeo. Para uma visão mais detalhada da Azure Media Services Analytics consulte [este](media-services-analytics-overview.md) tema. 

Este tópico discute como processar os seus meios de comunicação com processadores de media Media Analytics (MPs) utilizando o portal Azure. Os MPs da Media Analytics produzem ficheiros MP4 ou ficheiros JSON. Se um processador de mídia produzir um ficheiro MP4, você descarregue progressivamente o ficheiro. Se um processador de mídia produzir um ficheiro JSON, faça o download do ficheiro a partir do armazenamento de blob Azure. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Escolha um ativo que queira analisar
1. No [portal do Azure](https://portal.azure.com/) selecione a sua conta de Serviços de Multimédia do Azure.
2. Na janela **Definições**, selecione **Elementos**.  
   
    ![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. Selecione o ativo que gostaria de analisar e premir o botão **Analisar.**
   
    ![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. No ativo de **meio-dia do Processo com** a janela Media Analytics, selecione o processador. 
   
    O resto do artigo explica porquê e como usar cada processador. 
5. Pressione **Criar** para começar um trabalho.

## <a name="azure-media-indexer"></a>Azure Media Indexer
O processador de mídia **Azure Media Indexer** permite-lhe tornar os ficheiros de mídia e conteúdos pesquisáveis, bem como gerar faixas de legendagem fechadas. Esta secção fornece alguns detalhes sobre as opções que especifica para este MP.

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Idioma
A linguagem natural a ser reconhecida no ficheiro multimédia. Por exemplo, inglês ou espanhol. 

### <a name="captions"></a>Legendas
Pode escolher um formato de legenda que será gerado a partir do seu conteúdo. Um trabalho de indexação pode gerar ficheiros de legendas fechados nos seguintes formatos:  
 
* **TTML**
* **WebVTT**

Os ficheiros de Legenda Fechada (CC) nestes formatos podem ser utilizados para tornar os ficheiros de áudio e vídeo acessíveis a pessoas com deficiência auditiva.

### <a name="keywords"></a>Palavras-chave
Selecione esta opção se quiser gerar um ficheiro XML de palavras-chave. Este ficheiro contém palavras-chave extraídas do conteúdo da fala, com informações de frequência e compensação.

### <a name="job-name"></a>Nome da tarefa
Um nome amigável que lhe permite identificar o trabalho. [Este](media-services-portal-check-job-progress.md) artigo descreve como se pode monitorizar o progresso de um trabalho. 

### <a name="output-file"></a>Ficheiro de saída
Um nome amigável que permite identificar o conteúdo de saída. 

### <a name="speed"></a>Velocidade
Especifique a velocidade com que acelerar o vídeo de entrada. A saída é uma versão estabilizada e com lapso de tempo do vídeo de entrada.

### <a name="job-name"></a>Nome da tarefa
Um nome amigável que lhe permite identificar o trabalho. [Este](media-services-portal-check-job-progress.md) artigo descreve como se pode monitorizar o progresso de um trabalho. 

### <a name="output-file"></a>Ficheiro de saída
Um nome amigável que permite identificar o conteúdo de saída. 

## <a name="azure-media-face-detector"></a>Azure Media Face Detector
O processador de mídia **Azure Media Face Detetor** (MP) permite-lhe contar, rastrear movimentos e até avaliar a participação e a reação do público através de expressões faciais. Este serviço contém duas funcionalidades: 

* **Deteção de rostos**
  
    A deteção facial encontra e rastreia rostos humanos dentro de um vídeo. Várias faces podem ser detetadas e posteriormente rastreadas à medida que se deslocam, com a hora e os metadados de localização devolvidos num ficheiro JSON. Durante o rastreio, tentará dar um ID consistente ao mesmo rosto enquanto a pessoa se desloca no ecrã, mesmo que esteja obstruída ou deixe brevemente a moldura.
  
  > [!NOTE]
  > Estes serviços não realizam reconhecimento facial. Um indivíduo que deixe a moldura ou fique obstruído por muito tempo receberá um novo ID quando regressar.
  > 
  > 
* **Deteção de emoção**
  
    A Deteção de Emoções é um componente opcional do Processador de Media de Deteção facial que devolve a análise de múltiplos atributos emocionais dos rostos detetados, incluindo felicidade, tristeza, medo, raiva e muito mais. 

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Modo de deteção
Um dos seguintes modos pode ser utilizado pelo processador:

* deteção facial
* por deteção de emoção facial
* deteção de emoção agregada

### <a name="job-name"></a>Nome da tarefa
Um nome amigável que lhe permite identificar o trabalho. [Este](media-services-portal-check-job-progress.md) artigo descreve como se pode monitorizar o progresso de um trabalho. 

### <a name="output-file"></a>Ficheiro de saída
Um nome amigável que permite identificar o conteúdo de saída. 

## <a name="azure-media-motion-detector"></a>Azure Media Motion Detector
O processador de mídia **Azure Media Motion Detetor** (MP) permite identificar de forma eficiente secções de interesse dentro de um vídeo longo e tranquilo. A deteção de movimentos pode ser usada em imagens estáticas de câmaras para identificar secções do vídeo onde o movimento ocorre. Gera um ficheiro JSON contendo metadados com selos temporais e a região de delimitação onde ocorreu o evento.

Direcionada para feeds de vídeo de segurança, esta tecnologia é capaz de categorizar o movimento em eventos relevantes e falsos positivos, como sombras e mudanças de iluminação. Isto permite-lhe gerar alertas de segurança a partir de imagens de câmaras sem ser enviado spam com eventos irrelevantes intermináveis, ao mesmo tempo que é capaz de extrair momentos de interesse de vídeos de vigilância extremamente longos.

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure Media Video Thumbnails
Este processador pode ajudá-lo a criar resumos de vídeos longos selecionando automaticamente fragmentos interessantes do vídeo de origem. Isto é útil quando se pretende fornecer uma visão geral rápida do que esperar num longo vídeo. Para obter informações detalhadas e exemplos, consulte Utilize miniaturas de [vídeo azure media para criar uma sumtualização](media-services-video-summarization.md) de vídeo

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Nome da tarefa
Um nome amigável que lhe permite identificar o trabalho. [Este](media-services-portal-check-job-progress.md) artigo descreve como se pode monitorizar o progresso de um trabalho. 

### <a name="output-file"></a>Ficheiro de saída
Um nome amigável que permite identificar o conteúdo de saída. 

## <a name="azure-media-content-moderator"></a>Moderador de Conteúdo dos Meios de Comunicação Social Azure
Este processador ajuda-o a detetar potenciais conteúdos adultos e picantes em vídeos. O processador deteta automaticamente imagens e quadros de chaves no vídeo. Ele marca os quadros-chave para possíveis conteúdos adultos ou picantes, e sugere avaliações com base em limiares de padrão. Para obter informações detalhadas e exemplos, consulte [Use Azure Media Content Moderador para moderar vídeos](media-services-content-moderation.md)

![Vídeos moderados](./media/media-services-portal-analyze/media-services-portal-analyze-content-moderator.PNG)

### <a name="version"></a>Versão 
Use "2.0".

### <a name="mode"></a>Modo
A versão 2.0 `Mode` ignora a definição.

## <a name="next-steps"></a>Passos seguintes
Ver caminhos de aprendizagem dos Serviços de Media.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
