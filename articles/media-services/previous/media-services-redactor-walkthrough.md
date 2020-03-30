---
title: Redact enfrenta com passagem do Azure Media Analytics Microsoft Docs
description: Este tópico mostra instruções passo a passo sobre como executar um fluxo de trabalho de redação completo utilizando o Azure Media Services Explorer (AMSE) e o Azure Media Redator Visualizer (ferramenta open source).
services: media-services
documentationcenter: ''
author: Lichard
manager: femila
editor: ''
ms.assetid: d6fa21b8-d80a-41b7-80c1-ff1761bc68f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: ril
ms.reviewer: juliako
ms.openlocfilehash: a8db8de6ef062dcf757f3d264379677d6550ea3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "69997682"
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Redact enfrenta com passagem do Azure Media Analytics

## <a name="overview"></a>Descrição geral

**Azure Media Redator** é um processador de mídia [Azure Media Analytics](media-services-analytics-overview.md) (MP) que oferece redação facial escalável na nuvem. A redação facial permite modificar o seu vídeo de forma a desfocar rostos de indivíduos selecionados. Você pode querer usar o serviço de redação facial em cenários de segurança pública e meios de comunicação. Alguns minutos de filmagens que contêm várias faces podem levar horas a redigir manualmente, mas com este serviço o processo de redação facial exigirá apenas alguns passos simples. Para mais informações, consulte [este](https://azure.microsoft.com/blog/azure-media-redactor/) blog.

Para mais detalhes sobre **o Redator Azure Media,** consulte o tema da visão geral da [Redação Face.](media-services-face-redaction.md)

Este tópico mostra instruções passo a passo sobre como executar um fluxo de trabalho de redação completo utilizando o Azure Media Services Explorer (AMSE) e o Azure Media Redator Visualizer (ferramenta open source).

Para mais informações, consulte [este](https://azure.microsoft.com/blog/redaction-preview-available-globally) blog.

## <a name="azure-media-services-explorer-workflow"></a>Fluxo de trabalho do Azure Media Services Explorer

A maneira mais fácil de começar com redator é usar a ferramenta AMSE de código aberto no GitHub. Pode executar um fluxo de trabalho simplificado através do modo **combinado** se não precisar de acesso ao json de anotação ou às imagens jpg face.

### <a name="download-and-setup"></a>Baixar e configurar

1. Descarregue a ferramenta AMSE para ams v2 [daqui](https://aka.ms/amseforv2).
1. Inicie sessão na sua conta de Serviços de Media utilizando a sua chave de serviço.

    Para obter o nome de conta e as informações da chave, aceda ao [portal do Azure](https://portal.azure.com/) e selecione a sua conta AMS. Em seguida, selecione Definições > Teclas. A janela Gerir chaves mostra o nome da conta e as chaves primária e secundária são apresentadas. Copie os valores do nome da conta e a chave primária.

![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>Primeiro passe - analisar o modo

1. Faça upload do seu ficheiro de mídia através do Asset –> Upload, ou através de arrasto e queda. 
1. Clique e processe o seu ficheiro de mídia utilizando o modo Media Analytics –> Azure Media Redator –> Analisar. 


![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

A saída incluirá um ficheiro json de anotações com dados de localização facial, bem como um jpg de cada rosto detetado. 

![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>Segundo passe - modo redigido

1. Faça upload do seu ativo de vídeo original para a saída a partir do primeiro passe e definido como um ativo primário. 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. (Opcional) Faça upload de um ficheiro 'Dance_idlist.txt' que inclui uma lista deslimitada das iDs que pretende redigir. 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. (Opcional) Faça quaisquer edições para o ficheiro anotações.json, tais como aumentar os limites da caixa de limitador. 
4. Clique no ativo de saída a partir do primeiro passe, selecione o Redator e corra com o modo **Redact.** 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Descarregue ou partilhe o ativo final de saída redigido. 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Ferramenta de código aberto Azure Media Redator Visualizer

Uma ferramenta de [visualização](https://github.com/Microsoft/azure-media-redactor-visualizer) de código aberto foi concebida para ajudar os desenvolvedores a começar pelo formato de anotações com a análise e utilização da saída.

Depois de clonar o repo, para executar o projeto, terá de descarregar FFMPEG do seu [site oficial](https://ffmpeg.org/download.html).

Se for um desenvolvedor que tenta analisar os dados de anotação JSON, procure no Interior models.MetaData para exemplos de código de amostra.

### <a name="set-up-the-tool"></a>Configurar a ferramenta

1.  Descarregue e construa toda a solução. 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  Baixe FFMPEG [daqui](https://ffmpeg.org/download.html). Este projeto foi originalmente desenvolvido com a versão be1d324 (2016-10-04) com ligação estática. 
3.  Copie ffmpeg.exe e ffprobe.exe para a mesma pasta de saída que AzureMediaRedactor.exe. 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. Executar AzureMediaRedactor.exe. 

### <a name="use-the-tool"></a>Use a ferramenta

1. Processe o seu vídeo na sua conta Azure Media Services com o Redator MP no modo Analyze. 
2. Baixe tanto o ficheiro de vídeo original como a saída do Redaction - Analise o trabalho. 
3. Executar a aplicação de visualização e escolher os ficheiros acima. 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. Pré-visualizar o seu ficheiro. Selecione quais os rostos que gostaria de borrar através da barra lateral à direita. 
    
    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  O campo de texto inferior atualizar-se-á com as identificações faciais. Crie um ficheiro chamado "idlist.txt" com estas identidades como uma lista delimitada de nova linha. 

    >[!NOTE]
    > O idlist.txt deve ser guardado na ANSI. Pode utilizar o bloco de notas para economizar na ANSI.
    
6.  Faça o upload deste ficheiro para o ativo de saída a partir do passo 1. Faça o upload do vídeo original para este ativo também e definido como ativo primário. 
7.  Executa o trabalho da Redaction neste ativo com o modo "Redact" para obter o vídeo redigido final. 

## <a name="next-steps"></a>Passos seguintes 

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Ligações relacionadas
[Visão geral da Análise de Serviços de Mídia Azure](media-services-analytics-overview.md)

[Demonstrações azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

[Anunciando a redação facial para a Azure Media Analytics](https://azure.microsoft.com/blog/azure-media-redactor/)
