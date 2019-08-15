---
title: Redação faces com instruções passo a Análise de Mídia do Azure | Microsoft Docs
description: Este tópico mostra as instruções passo a passo sobre como executar um fluxo de trabalho de edição completo usando o AMSE (Gerenciador de serviços de mídia do Azure) e o Visualizador de Azure Media Redactor (ferramenta de código-fonte aberto).
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
ms.openlocfilehash: 3f40c69900b0d7f1c3bf446c1153e21dd7fd4d1b
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "69014928"
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Redação faces com Análise de Mídia do Azure instruções

## <a name="overview"></a>Descrição geral

**Azure Media redactor** é um processador de mídia [análise de mídia do Azure](media-services-analytics-overview.md) (MP) que oferece uma edição facial escalonável na nuvem. A edição facial permite que você modifique seu vídeo para desfocar rostos de indivíduos selecionados. Talvez você queira usar o serviço de edição facial em cenários de segurança pública e de mídia de notícias. Alguns minutos de seqüência de imagens que contém vários rostos podem levar horas para serem editados manualmente, mas com esse serviço, o processo de edição facial exigirá apenas algumas etapas simples. Para obter mais informações, consulte [este](https://azure.microsoft.com/blog/azure-media-redactor/) blog.

Para obter detalhes sobre **Azure Media redactor**, consulte o tópico [visão geral de edição facial](media-services-face-redaction.md) .

Este tópico mostra as instruções passo a passo sobre como executar um fluxo de trabalho de edição completo usando o AMSE (Gerenciador de serviços de mídia do Azure) e o Visualizador de Azure Media Redactor (ferramenta de código-fonte aberto).

Para obter mais informações, consulte [este](https://azure.microsoft.com/blog/redaction-preview-available-globally) blog.

## <a name="azure-media-services-explorer-workflow"></a>Fluxo de trabalho do Gerenciador de serviços de mídia do Azure

A maneira mais fácil de começar com o redação é usar a ferramenta AMSE de código-fonte aberto no GitHub. Você pode executar um fluxo de trabalho simplificado por meio do modo **combinado** se não precisar de acesso à anotação JSON ou às imagens do jpg de face.

### <a name="download-and-setup"></a>Baixar e configurar

1. Baixe a ferramenta AMSE [aqui](https://github.com/Azure/Azure-Media-Services-Explorer).
1. Faça logon na sua conta de serviços de mídia usando sua chave de serviço.

    Para obter o nome de conta e as informações da chave, aceda ao [portal do Azure](https://portal.azure.com/) e selecione a sua conta AMS. Em seguida, selecione Configurações > chaves. A janela Gerir chaves mostra o nome da conta e as chaves primária e secundária são apresentadas. Copie os valores do nome da conta e a chave primária.

![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>Primeira aprovação – modo de análise

1. Carregue o arquivo de mídia por meio do ativo – > carregar ou por meio de arrastar e soltar. 
1. Clique com o botão direito do mouse e processe o arquivo de mídia usando Análise de Mídia – > Azure Media Redactor – > modo de análise. 


![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

A saída incluirá um arquivo JSON de anotações com dados de localização de face, bem como um jpg de cada face detectada. 

![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>Segunda passagem – modo redação

1. Carregue seu ativo de vídeo original na saída da primeira passagem e defina como um ativo primário. 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. Adicional Carregue um arquivo ' Dance_idlist. txt ' que inclui uma lista delimitada por nova linha das IDs que você deseja redigir. 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. Adicional Faça qualquer edição no arquivo annotations. JSON, como aumentar os limites da caixa delimitadora. 
4. Clique com o botão direito do mouse no ativo de saída da primeira passagem, selecione o Redigidor e execute com o modo de **edição** . 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Baixe ou compartilhe o ativo de saída redigido final. 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Ferramenta de código-fonte aberto do Azure Media Redactor Visualizer

Uma ferramenta de [Visualizador](https://github.com/Microsoft/azure-media-redactor-visualizer) de código-fonte aberto foi projetada para ajudar os desenvolvedores a começarem a usar o formato de anotações com a análise e o uso da saída.

Depois de clonar o repositório, para executar o projeto, você precisará baixar o FFMPEG de seu [site oficial](https://ffmpeg.org/download.html).

Se você for um desenvolvedor tentando analisar os dados de anotação JSON, procure em modelos. metadados para exemplos de código de exemplo.

### <a name="set-up-the-tool"></a>Configurar a ferramenta

1.  Baixe e Compile a solução inteira. 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  Baixe o FFMPEG [aqui](https://ffmpeg.org/download.html). Este projeto foi originalmente desenvolvido com a versão be1d324 (2016-10-04) com vinculação estática. 
3.  Copie ffmpeg. exe e ffprobe. exe para a mesma pasta de saída que AzureMediaRedactor. exe. 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. Execute AzureMediaRedactor. exe. 

### <a name="use-the-tool"></a>Usar a ferramenta

1. Processe seu vídeo em sua conta dos serviços de mídia do Azure com o pacote de gerenciamento do redação no modo de análise. 
2. Baixe o arquivo de vídeo original e a saída do trabalho de análise-analisar. 
3. Execute o aplicativo visualizador e escolha os arquivos acima. 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. Visualize o arquivo. Selecione quais faces você gostaria de desfocar por meio da barra lateral à direita. 
    
    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  O campo de texto inferior será atualizado com as IDs de face. Crie um arquivo chamado "idlist. txt" com essas IDs como uma lista delimitada por nova linha. 

    >[!NOTE]
    > O idlist. txt deve ser salvo em ANSI. Você pode usar o bloco de notas para salvar em ANSI.
    
6.  Carregue esse arquivo no ativo de saída da etapa 1. Carregue o vídeo original para esse ativo também e defina como ativo primário. 
7.  Executar trabalho de edição neste ativo com o modo "redigir" para obter o vídeo final redigido. 

## <a name="next-steps"></a>Passos seguintes 

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Ligações relacionadas
[Visão geral da análise dos serviços de mídia do Azure](media-services-analytics-overview.md)

[Demonstrações de Análise de Mídia do Azure](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

[Anunciando a edição facial para Análise de Mídia do Azure](https://azure.microsoft.com/blog/azure-media-redactor/)
