---
title: Analisar sua mídia usando o portal do Azure | Microsoft Docs
description: Este tópico discute como processar sua mídia com Análise de Mídia MPs (processadores de mídia) usando o portal do Azure.
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
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74462608"
---
# <a name="analyze-your-media-using-the-azure-portal"></a>Analise a sua multimédia com o portal do Azure 

> [!IMPORTANT]
> Examine os [planos de aposentadoria](media-services-analytics-overview.md#retirement-plans) de alguns processadores de mídia.

## <a name="overview"></a>Descrição geral
A análise dos serviços de mídia do Azure é uma coleção de componentes de fala e visão (em escala empresarial, conformidade, segurança e alcance global) que tornam mais fácil para as organizações e empresas obter informações acionáveis de seus arquivos de vídeo. Para obter uma visão geral mais detalhada da análise dos serviços de mídia do Azure, consulte [este](media-services-analytics-overview.md) tópico. 

Este tópico discute como processar sua mídia com Análise de Mídia MPs (processadores de mídia) usando o portal do Azure. Os MPs Análise de Mídia produzem arquivos MP4 ou arquivos JSON. Se um processador de mídia produzir um arquivo MP4, você baixará progressivamente o arquivo. Se um processador de mídia produzir um arquivo JSON, você baixará o arquivo do armazenamento de BLOBs do Azure. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Escolha um ativo que você deseja analisar
1. No [portal do Azure](https://portal.azure.com/) selecione a sua conta de Serviços de Multimédia do Azure.
2. Na janela **Definições**, selecione **Elementos**.  
   
    ![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. Selecione o ativo que você deseja analisar e pressione o botão **analisar** .
   
    ![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. Na janela **processar ativo de mídia com análise de mídia** , selecione o processador. 
   
    O restante do artigo explica por que e como usar cada processador. 
5. Pressione **criar** para iniciar um trabalho.

## <a name="azure-media-indexer"></a>Azure Media Indexer
O processador de mídia **Azure Media indexer** permite que você torne os arquivos de mídia e o conteúdo pesquisáveis, além de gerar faixas de legendas ocultas. Esta seção fornece alguns detalhes sobre as opções que você especificar para este MP.

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Idioma
O idioma natural a ser reconhecido no arquivo multimídia. Por exemplo, inglês ou espanhol. 

### <a name="captions"></a>Legendas
Você pode escolher um formato de legenda que será gerado a partir do seu conteúdo. Um trabalho de indexação pode gerar arquivos de legenda oculta nos seguintes formatos:  
 
* **TTML**
* **WebVTT**

Arquivos de legenda oculta (CC) nesses formatos podem ser usados para tornar os arquivos de áudio e vídeo acessíveis a pessoas com deficiência auditiva.

### <a name="keywords"></a>Palavras-chave
Selecione esta opção se desejar gerar um arquivo XML de palavras-chave. Esse arquivo contém palavras-chave extraídas do conteúdo de fala, com informações de frequência e deslocamento.

### <a name="job-name"></a>Nome da tarefa
Um nome amigável que permite identificar o trabalho. [Este](media-services-portal-check-job-progress.md) artigo descreve como você pode monitorar o progresso de um trabalho. 

### <a name="output-file"></a>Ficheiro de saída
Um nome amigável que permite identificar o conteúdo de saída. 

### <a name="speed"></a>Velocidade
Especifique a velocidade com a qual acelerar o vídeo de entrada. A saída é uma representação estabilizada e com lapso de tempo do vídeo de entrada.

### <a name="job-name"></a>Nome da tarefa
Um nome amigável que permite identificar o trabalho. [Este](media-services-portal-check-job-progress.md) artigo descreve como você pode monitorar o progresso de um trabalho. 

### <a name="output-file"></a>Ficheiro de saída
Um nome amigável que permite identificar o conteúdo de saída. 

## <a name="azure-media-face-detector"></a>Azure Media Face Detector
O MP (processador de mídia **Azure Media face detector** ) permite que você conte, acompanhe movimentos e até mesmo avalie a participação e a reação do público por meio de expressões faciais. Esse serviço contém dois recursos: 

* **Detecção facial**
  
    A detecção facial localiza e rastreia rostos humanas em um vídeo. Várias faces podem ser detectadas e, posteriormente, acompanhadas à medida que elas se movimentam, com os metadados de hora e local retornados em um arquivo JSON. Durante o acompanhamento, ele tentará fornecer uma ID consistente para a mesma face enquanto a pessoa estiver se movendo pela tela, mesmo que esteja obstruída ou saia do quadro rapidamente.
  
  > [!NOTE]
  > Esses serviços não executam o reconhecimento facial. Uma pessoa que deixa o quadro ou ficará obstruída por muito tempo receberá uma nova ID quando retornar.
  > 
  > 
* **Detecção de emoções**
  
    A detecção de emoções é um componente opcional do processador de mídia Detecção Facial que retorna a análise em vários atributos emocional dos rostos detectados, incluindo felicidade, tristeza, medo, raiva e muito mais. 

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Modo de detecção
Um dos seguintes modos pode ser usado pelo processador:

* detecção facial
* detecção de emoções de acordo com a face
* detecção de emoções agregada

### <a name="job-name"></a>Nome da tarefa
Um nome amigável que permite identificar o trabalho. [Este](media-services-portal-check-job-progress.md) artigo descreve como você pode monitorar o progresso de um trabalho. 

### <a name="output-file"></a>Ficheiro de saída
Um nome amigável que permite identificar o conteúdo de saída. 

## <a name="azure-media-motion-detector"></a>Azure Media Motion Detector
O MP (processador de mídia **Azure Media Motion detector** ) permite que você identifique com eficiência as seções de interesse em um vídeo muito longo e não-evento. A detecção de movimento pode ser usada em seqüências de imagens de câmera estática para identificar as seções do vídeo em que ocorre o movimento. Ele gera um arquivo JSON contendo metadados com carimbos de data/hora e a região delimitadora em que o evento ocorreu.

Direcionado para feeds de vídeo de segurança, essa tecnologia é capaz de categorizar o movimento em eventos relevantes e falsos positivos, como sombras e mudanças de iluminação. Isso permite que você gere alertas de segurança de feeds de câmera sem ser spam com eventos irrelevantes intermináveis, ao mesmo tempo em que pode extrair momentos de interesse de vídeos de vigilância extremamente longos.

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure Media Video Thumbnails
Esse processador pode ajudá-lo a criar resumos de vídeos longos selecionando automaticamente trechos interessantes do vídeo de origem. Isso é útil quando você deseja fornecer uma visão geral rápida do que esperar em um vídeo longo. Para obter informações detalhadas e exemplos, consulte [usar Azure Media Video thumbnails para criar um resumo de vídeo](media-services-video-summarization.md)

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Nome da tarefa
Um nome amigável que permite identificar o trabalho. [Este](media-services-portal-check-job-progress.md) artigo descreve como você pode monitorar o progresso de um trabalho. 

### <a name="output-file"></a>Ficheiro de saída
Um nome amigável que permite identificar o conteúdo de saída. 

## <a name="azure-media-content-moderator"></a>Content Moderator de mídia do Azure
Esse processador ajuda você a detectar possíveis conteúdos de conteúdo adulto e erótico em vídeos. O processador detecta automaticamente capturas de tela e quadros-chave no vídeo. Ele classifica os quadros-chave para conteúdo adulto ou erótico possível e sugere revisões com base nos limites padrão. Para obter informações detalhadas e exemplos, consulte [usar o Azure Media Content moderator para vídeos moderados](media-services-content-moderation.md)

![Vídeos moderados](./media/media-services-portal-analyze/media-services-portal-analyze-content-moderator.PNG)

### <a name="version"></a>Versão 
Use "2,0".

### <a name="mode"></a>Modo
A versão 2,0 ignora a configuração de `Mode`.

## <a name="next-steps"></a>Passos seguintes
Exibir os roteiros de aprendizagem dos serviços de mídia.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
