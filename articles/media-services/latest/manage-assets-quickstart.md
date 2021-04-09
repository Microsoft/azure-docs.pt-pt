---
title: Utilizar o portal para carregar, codificar e transmitir conteúdo em fluxo
description: Este quickstart mostra-lhe como usar o portal para carregar, codificar e transmitir conteúdo com a Azure Media Services.
ms.topic: quickstart
ms.date: 08/31/2020
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femila
ms.openlocfilehash: 9b66fa6cd688cf0680e742ffa9fbe4d61a444ab2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102633003"
---
# <a name="quickstart-upload-encode-and-stream-content-with-portal"></a>Quickstart: Carregar, codificar e transmitir conteúdo com portal

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este quickstart mostra-lhe como usar o portal Azure para carregar, codificar e transmitir conteúdo com a Azure Media Services.
  
## <a name="overview"></a>Descrição Geral

* Para começar a gerir, encriptar, codificar, analisar e transmitir conteúdos de mídia em Azure, é necessário criar uma conta de Media Services e enviar o seu ficheiro de meios digitais de alta qualidade para um **ativo.** 
    
    > [!NOTE]
    > Se o seu vídeo foi previamente enviado para a conta dos Media Services utilizando os Serviços de Comunicação v3 API ou se o conteúdo foi gerado com base numa saída ao vivo, não verá os botões **Encode**, **Analyze** ou **Encrypt** no portal Azure. Utilize os Serviços de Mídia v3 APIs para executar estas tarefas.

    Reveja o seguinte: 

  * [Carregamento e armazenamento na cloud](storage-account-concept.md)
  * [Conceito de ativos](assets-concept.md)
* Uma vez que faça o upload do seu ficheiro de mídia digital de alta qualidade para um ativo (um ativo de entrada), pode processá-lo (codificar ou analisar). O conteúdo processado vai para outro ativo (ativo de saída). 
    * [Codificar](encoding-concept.md) o seu ficheiro carregado em formatos que podem ser reproduzidos numa grande variedade de navegadores e dispositivos.
    * [Analise](analyzing-video-audio-files-concept.md) o seu ficheiro carregado. 

        Atualmente, ao utilizar o portal Azure, pode fazer o seguinte: gerar um TTML e ficheiros de legendas webVTT fechados. Os ficheiros nestes formatos podem ser utilizados para tornar os ficheiros de áudio e vídeo acessíveis a pessoas com deficiência auditiva. Também pode extrair palavras-chave do seu conteúdo.

        Para uma experiência rica que lhe permite extrair insights dos seus ficheiros de vídeo e áudio, utilize predefinições de Serviços de Comunicação (como descrito em [Tutorial: Analise vídeos com Serviços de Comunicação social v3).](analyze-videos-tutorial-with-api.md) <br/>Se quiser informações mais detalhadas, utilize o [Índice de Vídeo](../video-indexer/index.yml) diretamente.    
* Uma vez processado o seu conteúdo, pode entregar conteúdo sonoro aos jogadores clientes. Para disponibilizar vídeos no ativo de saída para os clientes para reprodução, tem de criar um **localizador de streaming**. Ao criar o **localizador de streaming,** é necessário especificar uma **política de streaming**. **As políticas de streaming** permitem-lhe definir protocolos de streaming e opções de encriptação (se houver) para os seus **localizadores de streaming**.
    
    Comentário:

    * [Localizadores de transmissão em fluxo](streaming-locators-concept.md)
    * [Políticas de transmissão em fluxo](streaming-policy-concept.md)
    * [Empacotamento e entrega](dynamic-packaging-overview.md)
    * [Filtros](filters-concept.md)
* Pode proteger o seu conteúdo encriptando-o com o Advanced Encryption Standard (AES-128) ou/e qualquer um dos três principais sistemas DRM: Microsoft PlayReady, Google Widevine e Apple FairPlay. O [conteúdo encrypt com o portal Azure](encrypt-content-quickstart.md) quickstart mostra como configurar a proteção de conteúdos.
        
## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[Criar uma conta dos Media Services](create-account-howto.md)

## <a name="upload"></a>Carregar

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Localize e clique na sua conta de Serviços de Mídia.
1. Selecione **Ativos (novos)**.
1. Prima **o Upload** na parte superior da janela. 
1. Arraste e deixe cair ou navegue para um ficheiro que pretende carregar.

Se navegar para a janela dos seus bens, verá que um novo ativo foi adicionado à lista:

![Screenshot do portal Azure mostrando a janela Ativos aberta selecionando Ativos (novos) e um novo ativo adicionado selecionando o botão Upload.](./media/manage-assets-quickstart/upload.png)

## <a name="encode"></a>Codificar

1. Selecione **Ativos (novos)**.
1. Selecione o seu novo ativo (adicionado no último passo).
1. Clique em **Codificação** na parte superior da janela.

    Premir este botão inicia o trabalho de codificação. Quando completa com sucesso gera um ativo de saída que contém o conteúdo codificado.

Se navegar para a janela dos seus ativos, verá que o ativo de saída foi adicionado à lista:

![Screenshot da janela Ativos no portal Azure mostrando o ativo ignite.mp4 Media Encoded Standard codificado adicionado à lista de ativos.](./media/manage-assets-quickstart/encode.png)

## <a name="monitor-the-job-progress"></a>Monitorizar o progresso do trabalho

Para ver o estado do trabalho, navegue para **Jobs**. O trabalho geralmente passa pelos seguintes estados: Agendado, Filadued, Processamento, Terminado (o estado final). Se a tarefa encontrar um erro, obterá um estado de Erro.

![Estado](./media/manage-assets-quickstart/job-status.png)

## <a name="publish-and-stream"></a>Publicar e transmitir

Para publicar um ativo, precisa agora de adicionar um localizador de streaming ao seu ativo.

### <a name="streaming-locator"></a>Localizador de streaming 

1. Na secção **de localizador streaming,** prima **+ Adicione um localizador de streaming**.
    Isto publica o ativo e gera os URLs de streaming.

    > [!NOTE]
    > Se quiser que o seu stream seja encriptado, tem de criar uma política de chave de conteúdo e defini-la no localizador de streaming. Para mais informações, consulte [conteúdo encriptar com o portal Azure](encrypt-content-quickstart.md).
1. Na janela **de localização de streaming Add,** escolha uma das políticas de streaming predefinidas. Para obter informações detalhadas, consulte [as políticas de streaming](streaming-policy-concept.md)

    ![Localizador de streaming](./media/manage-assets-quickstart/streaming-locator.png)

Uma vez publicado o ativo, pode transmiti-lo diretamente no portal. 

![Jogar](./media/manage-assets-quickstart/publish.png)

Ou, copie o URL de streaming e use-o no seu leitor cliente.

> [!NOTE]
> Certifique-se de que o [ponto final de streaming](streaming-endpoint-concept.md) está a funcionar. Quando cria uma conta De Serviço de Media pela primeira vez, o ponto final de streaming predefinido é criado e está num estado parado, pelo que tem de o iniciar antes de poder transmitir o seu conteúdo.<br/>Só és cobrado quando o teu ponto final de streaming está no estado de funcionamento.

## <a name="cleanup-resources"></a>Recursos de limpeza

Se pretende experimentar os outros quickstarts, deve manter os recursos criados. Caso contrário, vá ao portal Azure, navegue pelos seus grupos de recursos, selecione o grupo de recursos sob o qual executou este arranque rápido e elimine todos os recursos.

## <a name="next-steps"></a>Passos seguintes

[Utilizar o portal para encriptar conteúdo](encrypt-content-quickstart.md)
