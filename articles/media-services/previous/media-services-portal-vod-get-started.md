---
title: Introdução à entrega de vídeos a pedido com o portal do Azure | Microsoft Docs
description: Este tutorial mostra os passos para implementar um serviço básico de entrega de conteúdos de vídeo a pedido com uma aplicação dos Serviços de Multimédia do Azure no portal do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 6c98fcfa-39e6-43a5-83a5-d4954788f8a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: juliako
ms.openlocfilehash: 958d860252c7bfdb79d7ea59b8a3d230c839d296
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57845244"
---
# <a name="get-started-with-delivering-content-on-demand-by-using-the-azure-portal"></a>Introdução à entrega de conteúdos a pedido com o portal do Azure

[!INCLUDE [media-services-selector-get-started](../../../includes/media-services-selector-get-started.md)]

Este tutorial mostra os passos para implementar um serviço básico de entrega de conteúdos de vídeo a pedido com uma aplicação dos Serviços de Multimédia do Azure no portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
São necessários os itens seguintes para concluir o tutorial:

* Uma conta do Azure. Para obter mais detalhes, veja [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
* Uma conta dos Media Services. Para criar uma conta dos Serviços de Multimédia, veja [Como criar uma conta dos Serviços de Multimédia](media-services-portal-create-account.md).

Este tutorial inclui as seguintes tarefas:

1. Iniciar o ponto final da transmissão em fluxo.
2. Carregar um ficheiro de vídeo.
3. Codificar o ficheiro de origem para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável.
4. Publicar o elemento e obter os URLs de transferência progressiva e transmissão em fluxo.  
5. Reproduzir os conteúdos.

## <a name="start-the-streaming-endpoint"></a>Iniciar o ponto final da transmissão em fluxo

Um dos cenários mais comuns quando se trabalha com os Serviços de Multimédia do Azure é a entrega de vídeos através de transmissão em fluxo de velocidade de transmissão adaptável. Os Serviços de Multimédia proporcionam-lhe empacotamento dinâmico. Com o empacotamento dinâmico, pode entregar os seus conteúdos com codificação MP4 e transmissão em fluxo de velocidade de transmissão adaptável em formatos de transmissão just-in-time que os Serviços de Multimédia suportam. Os exemplos incluem Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming e Dynamic Adaptive Streaming over HTTP (DASH, também conhecido como MPEG-DASH). A utilização da transmissão em fluxo de velocidade adaptável permite-lhe entregar os seus vídeos sem que seja necessário armazenar versões pré-empacotadas de cada um destes formatos de transmissão em fluxo.

> [!NOTE]
> Quando cria a conta dos Serviços de Multimédia, é adicionado um ponto final de transmissão em fluxo predefinido à mesma, no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final da transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 

Para iniciar o ponto final da transmissão em fluxo:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **Definições** > **Streaming endpoints**. 
3. Selecione o ponto final da transmissão em fluxo predefinido. A janela **DETALHES DO PONTO FINAL DE TRANSMISSÃO EM FLUXO PREDEFINIDO** é apresentada.
4. Clique no ícone **Iniciar**.
5. Selecione o botão **Guardar**.

## <a name="upload-files"></a>Carregar ficheiros
Para transmitir vídeos em fluxo com os Serviços de Multimédia, tem de carregar os vídeos de origem, codificá-los em velocidades de transmissão múltiplas e publicar o resultado. O primeiro passo é abrangido nesta secção. 

1. No [portal do Azure](https://portal.azure.com/) selecione a sua conta de Serviços de Multimédia do Azure.
2. Selecione **Definições** > **Elementos**. Em seguida, selecione o botão **Carregar**.
   
    ![Carregar ficheiros](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    A janela **Carregar um elemento de vídeo** é apresentada.
   
   > [!NOTE]
   > Os Serviços de Multimédia não limitam o tamanho de ficheiro para o carregamento de vídeos.
   > 
   > 
3. No seu computador, aceda ao vídeo que quer carregar. Selecione o vídeo e, em seguida, selecione **OK**.  
   
    O carregamento começa. Pode ver o progresso abaixo do nome do ficheiro.  

Quando o carregamento estiver concluído, o elemento novo é apresentado no painel **Elementos**. 

## <a name="encode-assets"></a>Codificar elementos
Para tirar partido do empacotamento dinâmico, tem de codificar o ficheiro de origem num conjunto de ficheiros MP4 de velocidade de transmissão múltipla. Os passos da codificação são demonstrados nesta secção.

### <a name="encode-assets-in-the-portal"></a>Codificar elementos no portal
Para codificar os seus conteúdos com o Media Encoder Standard no portal do Azure:

1. No [portal do Azure](https://portal.azure.com/) selecione a sua conta de Serviços de Multimédia do Azure.
2. Selecione **Definições** > **Elementos**. Selecione o elemento que pretende codificar.
3. Selecione o botão **Codificar**.
4. No painel **Codificar um elemento**, selecione o processador **Media Encoder Standard** e uma predefinição. Para obter informações sobre as predefinições, veja [Auto-generate a bitrate ladder](media-services-autogen-bitrate-ladder-with-mes.md) (Gerar automaticamente uma escala de velocidade de transmissão) e [Task presets for Media Encoder Standard](media-services-mes-presets-overview.md) (Predefinições de tarefas para o Media Encoder Standard). É importante escolher a predefinição que funcionará melhor no seu vídeo de entrada. Por exemplo, se sabe que o seu vídeo de entrada tem uma resolução de 1920 &#215; 1080 pixels, pode escolher a predefinição **H264 Multiple Bitrate 1080p**. Se o vídeo for de baixa resolução (640 &#215; 360), não deve utilizar a predefinição **H264 Multiple Bitrate 1080p**.
   
   Para o ajudar a gerir os seus recursos, pode editar o nome do elemento de saída e o nome da tarefa.
   
   ![Codificar elementos](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Selecione **Criar**.

### <a name="monitor-encoding-job-progress"></a>Monitorizar o progresso da tarefa de codificação
Para monitorizar o progresso da tarefa de codificação, na parte superior da página, clique em **Definições** e selecione **Tarefas**.

![Tarefas](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>Publicar conteúdo
Para fornecer ao seu utilizador um URL que ele possa utilizar para transmitir ou transferir o seu conteúdo, tem primeiro de publicar o elemento mediante a criação de um localizador. Os localizadores fornecem acesso aos ficheiros contidos no elemento. Os Serviços de Multimédia do Azure suportam dois tipos de localizadores: 

* **Localizadores de transmissão em fluxo (OnDemandOrigin)**. Os localizadores de transmissão em fluxo são utilizados para transmissões em velocidade adaptável. Alguns exemplos de transmissões em fluxo de velocidade adaptável incluem HLS, Smooth Streaming e MPEG-DASH. Para criar um localizador de transmissão em fluxo, o seu elemento tem de incluir um ficheiro .ism. 
* **Localizadores progressivos (assinatura de acesso partilhado)**. Os localizadores progressivos são utilizados para entregar vídeos através de transferências progressivas.

Para compilar um URL de transmissão em fluxo HLS, acrescente *(format=m3u8-aapl)* ao URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=m3u8-aapl)

Para compilar um URL de transmissão em fluxo que reproduza elementos de Smooth Streaming, utilize o formato de URL abaixo:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest

Para compilar um URL de transmissão em fluxo MPEG DASH, acrescente *(format=mpd-time-csf)* ao URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=mpd-time-csf)

Os URLs de assinatura de acesso partilhado têm o formato seguinte:

    {blob container name}/{asset name}/{file name}/{shared access signature}

> [!NOTE]
> Os localizadores que foram criados no portal do Azure antes de março de 2015 têm uma data de expiração de dois anos.  
> 
> 

Para atualizar uma data de expiração num localizador, pode utilizar uma [API REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) ou uma [API .NET](https://go.microsoft.com/fwlink/?LinkID=533259). 

> [!NOTE]
> Quando atualiza a data de expiração de um localizador de assinatura de acesso partilhado, o URL é alterado.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Para utilizar o portal para publicar um elemento
1. No [portal do Azure](https://portal.azure.com/) selecione a sua conta de Serviços de Multimédia do Azure.
2. Selecione **Definições** > **Elementos**. Selecione o elemento que pretende publicar.
3. Selecione o botão **Publicar**.
4. Selecione o tipo de localizador.
5. Selecione **Adicionar**.
   
    ![Publicar o vídeo](./media/media-services-portal-vod-get-started/media-services-publish1.png)

O URL é adicionado à lista de **URLs Publicados**.

## <a name="play-content-from-the-portal"></a>Reproduzir conteúdos a partir do portal
Pode testar o vídeo num leitor de conteúdos no portal do Azure.

Selecione o vídeo e, em seguida, selecione **OK**.

![Reproduzir o vídeo no portal do Azure](./media/media-services-portal-vod-get-started/media-services-play.png)

São aplicáveis algumas considerações:

* Para começar a transmissão em fluxo, inicie a execução do ponto final da transmissão em fluxo predefinido.
* Confirme que o vídeo foi publicado.
* O leitor de multimédia do portal do Azure reproduz a partir do ponto final da transmissão em fluxo predefinido. Se quiser reproduzir a partir de um ponto final da transmissão em fluxo não predefinido, selecione e copie o URL e cole-o noutro leitor. Por exemplo, pode testar o vídeo no [Leitor de Multimédia do Azure](https://amsplayer.azurewebsites.net/azuremediaplayer.html).

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passos Seguintes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
