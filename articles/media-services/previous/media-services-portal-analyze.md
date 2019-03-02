---
title: Analise a sua multimédia no portal do Azure | Documentos da Microsoft
description: Este tópico descreve como processar o seu suporte de dados com processadores de multimédia de análise de multimédia (MPs) com o portal do Azure.
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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: fa48713034de5660218d2d568be6673a7d39a7e3
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245306"
---
# <a name="analyze-your-media-using-the-azure-portal"></a>Analise a sua multimédia com o portal do Azure 
> [!NOTE]
> Para concluir este tutorial, precisa de uma conta do Azure. Para obter mais detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

## <a name="overview"></a>Descrição geral
Análise de serviços de multimédia do Azure é uma coleção de componentes de voz e visão (em escala empresarial, conformidade, segurança e alcance global) que seja mais fácil para as organizações e empresas obter análises acionáveis a partir dos ficheiros de vídeo. Consulte para obter mais de descrição geral da análise de serviços de multimédia do Azure [isso](media-services-analytics-overview.md) tópico. 

Este tópico descreve como processar o seu suporte de dados com processadores de multimédia de análise de multimédia (MPs) com o portal do Azure. Pacotes de gestão de análise de multimédia produzem ficheiros MP4 ou ficheiros JSON. Se um processador de multimédia produzir um ficheiro MP4, transferir progressivamente o ficheiro. Se um processador de multimédia produzir um ficheiro JSON, transfira o ficheiro do armazenamento de Blobs do Azure. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Escolha um recurso que pretende analisar
1. No [portal do Azure](https://portal.azure.com/) selecione a sua conta de Serviços de Multimédia do Azure.
2. Na janela **Definições**, selecione **Elementos**.  
   
    ![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. Selecione o elemento que gostaria de analisar e prima a **Analyze** botão.
   
    ![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. Na **processar elemento multimédia com a análise de multimédia** janela, selecione o processador. 
   
    O resto do artigo explica por que e como usar cada processador. 
5. Prima **criar** para o início de um trabalho.

## <a name="azure-media-indexer"></a>Azure Media Indexer
O **indexador de multimédia do Azure** processador de multimédia permite-lhe tornar pesquisável os arquivos de mídia e conteúdo, bem como gerar faixas de legenda codificadas fechadas. Esta secção fornece alguns detalhes sobre as opções que especificar para este pacote de gestão.

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Idioma
Linguagem natural a ser reconhecida no ficheiro multimédia. Por exemplo, inglês ou espanhol. 

### <a name="captions"></a>Legendagem
Pode escolher um formato de legenda será gerado a partir de seu conteúdo. Uma tarefa de indexação pode gerar ficheiros de legendas nos seguintes formatos:  

* **SAMI**
* **TTML**
* **WebVTT**

Fechado (CC) de legenda arquivos esses formatos podem ser usados para tornar os arquivos de áudio e vídeo acessível para pessoas portadoras de deficiência audição.

### <a name="aib-file"></a>Ficheiro AIB
Selecione esta opção se quiser gerar um arquivo de Blob de indexação de áudio para utilização com o servidor IFilter personalizado do SQL. Para obter mais informações, consulte [isso](https://azure.microsoft.com/blog/using-aib-files-with-azure-media-indexer-and-sql-server/) blog.

### <a name="keywords"></a>Palavras-chave
Selecione esta opção se quiser gerar um ficheiro XML de palavras-chave. Este ficheiro contém palavras-chave extraído do conteúdo de discurso, com frequência e informações de deslocamento.

### <a name="job-name"></a>Nome da tarefa
Um nome amigável que permita identificar a tarefa. [Isso](media-services-portal-check-job-progress.md) artigo descreve como pode monitorizar o progresso de uma tarefa. 

### <a name="output-file"></a>Ficheiro de saída
Um nome amigável que permita identificar o conteúdo de saída. 

## <a name="azure-media-hyperlapse"></a>Azure Media Hyperlapse
Hyperlapse de multimédia do Azure é um pacote de gestão que cria vídeos com time lapse uniformes de conteúdo de primeira pessoa ou ação câmara.  Para obter mais informações, veja [este](media-services-hyperlapse-content.md) tópico. Esta secção fornece alguns detalhes sobre as opções que especificar para este pacote de gestão.

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze004.png)

### <a name="speed"></a>Velocidade
Especifique a velocidade com que a acelerar o vídeo de entrada. O resultado é uma representação estabilizada e com time lapse e do vídeo de entrada.

### <a name="job-name"></a>Nome da tarefa
Um nome amigável que permita identificar a tarefa. [Isso](media-services-portal-check-job-progress.md) artigo descreve como pode monitorizar o progresso de uma tarefa. 

### <a name="output-file"></a>Ficheiro de saída
Um nome amigável que permita identificar o conteúdo de saída. 

## <a name="azure-media-face-detector"></a>Azure Media Face Detector
O **detetor de rostos de suporte de dados do Azure** processador de multimédia (MP) permite-lhe contar, controlar os movimentos e até mesmo avaliar a participação de público-alvo e reação por meio de expressões faciais. Este serviço contém dois recursos: 

* **Deteção de rostos**
  
    Deteção de rostos localiza e controla rostos humanos dentro de um vídeo. Várias faces podem ser detetadas e, em seguida, ser controladas quando eles passam, com os metadados de tempo e de localização devolvido num ficheiro JSON. Durante o controlo, ele irá tentar dar um ID de consistente para a mesma face, enquanto a pessoa está se movendo em torno na tela, mesmo que eles são obstructed ou deixam resumidamente o quadro.
  
  > [!NOTE]
  > Estes serviços não efetua o reconhecimento facial. Um indivíduo que deixa o quadro ou se torna obstructed para demasiado tempo terá um novo ID quando elas retornam.
  > 
  > 
* **Deteção de emoções**
  
    Deteção de emoções é um componente opcional do processador de suporte de dados de deteção de rostos que retorna análise em vários atributos emocional de rostos detetados, incluindo felicidade, tristeza, medo, raiva e muito mais. 

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Modo de deteção
Um dos seguintes modos de pode ser utilizado pelo processador:

* Deteção de rostos
* por deteção de emoções faciais
* Deteção de emoções agregada

### <a name="job-name"></a>Nome da tarefa
Um nome amigável que permita identificar a tarefa. [Isso](media-services-portal-check-job-progress.md) artigo descreve como pode monitorizar o progresso de uma tarefa. 

### <a name="output-file"></a>Ficheiro de saída
Um nome amigável que permita identificar o conteúdo de saída. 

## <a name="azure-media-motion-detector"></a>Azure Media Motion Detector
O **detetor de movimento de suporte de dados do Azure** processador de multimédia (MP) permite-lhe com eficiência identificar seções de interesse dentro de um vídeo de longo e rotineira, caso contrário. Deteção de movimento pode ser utilizada no filmagens de câmaras estático para identificar seções do vídeo que ocorre a equipe do motion. Gera um ficheiro JSON que contém uma metadados com carimbos de hora e a região delimitadora onde ocorreu o evento.

Essa tecnologia voltado para transmissões de vídeo de segurança, é possível categorizar o movimento em eventos relevantes e falsos positivos, como sombras e alterações de iluminação. Isto permite-lhe gerar alertas de segurança a partir de feeds de câmera sem a ser spammed com eventos irrelevantes intermináveis, enquanto a capacidade de extrair os momentos de interesse de vídeos de vigilância demasiado longo.

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure Media Video Thumbnails
Este processador pode ajudá-lo a criar resumos de vídeos longos selecionando automaticamente os trechos de código interessantes do vídeo de origem. Isto é útil quando deseja fornecer uma descrição geral rápida do que pode esperar de um vídeo longo. Para obter informações detalhadas e exemplos, consulte [utilização miniaturas de vídeo de multimédia do Azure para criar um resumo de vídeo](media-services-video-summarization.md)

![Analisar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Nome da tarefa
Um nome amigável que permita identificar a tarefa. [Isso](media-services-portal-check-job-progress.md) artigo descreve como pode monitorizar o progresso de uma tarefa. 

### <a name="output-file"></a>Ficheiro de saída
Um nome amigável que permita identificar o conteúdo de saída. 

## <a name="azure-media-content-moderator"></a>Moderador de conteúdos de multimédia do Azure
Este processador ajuda-o a detetar potenciais conteúdos para adultos nos vídeos. O processador Deteta automaticamente capturas e quadros-chave no vídeo. As notas de quadros-chave de conteúdo de adultos possíveis e sugere revisões com base nos limiares predefinidos. Para obter informações detalhadas e exemplos, consulte [utilização do Azure Media Content Moderator moderar vídeos](media-services-content-moderation.md)

![Vídeos moderados](./media/media-services-portal-analyze/media-services-portal-analyze-content-moderator.PNG)

### <a name="version"></a>Versão 
Utilize "2.0".

### <a name="mode"></a>Modo
A versão 2.0 Ignorar o `Mode` definição.

## <a name="next-steps"></a>Passos Seguintes
Serviços de multimédia de modo de exibição percursos de aprendizagem.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
