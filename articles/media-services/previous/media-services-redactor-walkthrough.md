---
title: Edite rostos com instruções de análise de multimédia do Azure | Documentos da Microsoft
description: Este tópico mostra-instruções passo a passo sobre como executar um fluxo de trabalho de redação completa com o Azure Media Services Explorer (AMSE) e o Azure Media Redactor Visualizer (ferramenta de código-fonte aberto).
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
ms.author: rli; juliako;
ms.openlocfilehash: 3e4844c3174e41ca7f6f5667a2777aba11f70f11
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58292993"
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Edite rostos com instruções de análise de multimédia do Azure

## <a name="overview"></a>Descrição geral

**O Azure Media Redactor** é um [análise de multimédia do Azure](media-services-analytics-overview.md) processador de multimédia (MP) que oferece a redação de rostos escalável na cloud. A redação de rostos permite-lhe modificar o vídeo para Desfoca rostos de indivíduos selecionados. Pode querer utilizar o serviço de redação de rostos em cenários de segurança e multimédia para notícias pública. Alguns minutos de imagens que contém vários rostos de podem demorar horas a edite manualmente, mas com este serviço, o processo de redação de rostos exigirá apenas alguns passos simples. Para obter mais informações, consulte [isso](https://azure.microsoft.com/blog/azure-media-redactor/) blog.

Para obter detalhes sobre **do Azure Media Redactor**, consulte a [descrição geral de redação de rostos](media-services-face-redaction.md) tópico.

Este tópico mostra-instruções passo a passo sobre como executar um fluxo de trabalho de redação completa com o Azure Media Services Explorer (AMSE) e o Azure Media Redactor Visualizer (ferramenta de código-fonte aberto).

Para obter mais informações, consulte [isso](https://azure.microsoft.com/blog/redaction-preview-available-globally) blog.

## <a name="azure-media-services-explorer-workflow"></a>Fluxo de trabalho de Explorador dos serviços de multimédia do Azure

A maneira mais fácil para começar a utilizar com o Editor de é usar a ferramenta AMSE do código-fonte aberto no GitHub. Pode executar um fluxo de trabalho simplificado através de **combinados** modo, se não precisar de acesso para o json de anotação ou as imagens de jpg face.

### <a name="download-and-setup"></a>Download e instalação

1. Transfira a ferramenta AMSE partir [aqui](https://github.com/Azure/Azure-Media-Services-Explorer).
1. Inicie sessão na sua conta de Media Services utilizando a sua chave de serviço.

    Para obter o nome de conta e as informações da chave, aceda ao [portal do Azure](https://portal.azure.com/) e selecione a sua conta AMS. Em seguida, selecione definições > chaves. A janela Gerir chaves mostra o nome da conta e as chaves primária e secundária são apresentadas. Copie os valores do nome da conta e a chave primária.

![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>Em primeiro lugar passar – analisar modo

1. Suporte de dados de ficheiro por meio do recurso de carregamento –> carregamento, ou através de arrastar e soltar. 
1. Clique com o botão direito do rato e processar o ficheiro de multimédia com a análise de multimédia –> Azure Media Redactor –> Analyze modo. 


![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

O resultado incluirá um ficheiro de json de anotações com dados de localização de rosto, bem como um jpg de cada rosto detetado. 

![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>Em segundo lugar passar – edite modo

1. Carregar o recurso de vídeo original para o resultado do primeiro passo e definido como um ativo primário. 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. (Opcional) Carregar um ficheiro de "Dance_idlist.txt" que inclui uma lista de nova linha delimitados por dos IDs que pretende edite. 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. (Opcional) Efetue as edições ao ficheiro annotations.json como aumentar os limites da caixa delimitadora. 
4. Clique com o botão direito do rato em elemento de saída do primeiro passo, selecione o Redator e executar com o **Redact** modo. 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Baixe ou Compartilhe o elemento de saída de ação final. 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Ferramenta de código-fonte aberto do Azure Media Redactor Visualizer

Uma código-fonte aberto [ferramenta de Visualizador](https://github.com/Microsoft/azure-media-redactor-visualizer) foi concebido para ajudar os desenvolvedores começando com o formato de anotações com análise e usa a saída.

Depois de clonar o repositório, para executar o projeto, precisará baixar o FFMPEG de seus [site oficial do](https://ffmpeg.org/download.html).

Se for um desenvolvedor tentar analisar os dados de anotação de JSON, olhar dentro Models.MetaData para obter exemplos de código de exemplo.

### <a name="set-up-the-tool"></a>Configurar a ferramenta

1.  Transfira e crie a solução inteira. 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  Transferir FFMPEG partir [aqui](https://ffmpeg.org/download.html). Este projeto foi desenvolvido originalmente com versão be1d324 (04 de 10 de 2016) com a vinculação estática. 
3.  Copie ffmpeg.exe e ffprobe.exe na mesma pasta de saída como AzureMediaRedactor.exe. 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. Execute AzureMediaRedactor.exe. 

### <a name="use-the-tool"></a>Utilize a ferramenta

1. Processe o seu vídeo na sua conta de Media Services do Azure com o pacote de gestão do Redactor no modo de análise. 
2. Transferir o ficheiro de vídeo original e a saída da redação - analisar a tarefa. 
3. Execute a aplicação de Visualizador e escolher os ficheiros acima. 

    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. Pré-visualize o ficheiro. Selecione a quais faces que gostaria de Desfoca via a barra lateral no lado direito. 
    
    ![Redação de rostos](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  O campo de texto na parte inferior é atualizados com os IDs de rostos. Crie um arquivo chamado "idlist.txt" com estes IDs de como uma lista delimitada por caractere de nova linha. 

    >[!NOTE]
    > O idlist.txt deverá ser guardada em ANSI. Pode usar o bloco de notas para guardar em ANSI.
    
6.  Carregar este ficheiro para o elemento de saída do passo 1. Carregar o vídeo original para este recurso também e definir como ativo primário. 
7.  Execute tarefa de redação em deste recurso com o modo de "Redact" para obter o último eliminado vídeo. 

## <a name="next-steps"></a>Passos Seguintes 

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Ligações relacionadas
[Descrição geral da análise dos serviços de multimédia do Azure](media-services-analytics-overview.md)

[Demonstrações de análise de multimédia do Azure](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

[Anunciando a redação de rostos para análise de multimédia do Azure](https://azure.microsoft.com/blog/azure-media-redactor/)
