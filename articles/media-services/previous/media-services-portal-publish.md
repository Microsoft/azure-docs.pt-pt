---
title: Publicar conteúdo no portal do Azure | Microsoft Docs
description: Este tutorial orienta você pelas etapas de publicação de conteúdo no portal do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 92c364eb-5a5f-4f4e-8816-b162c031bb40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 5f242018abfb15cea1b76cbcaad00942ec25d78d
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2019
ms.locfileid: "69015064"
---
# <a name="publish-content-in-the-azure-portal"></a>Publicar conteúdo no portal do Azure  
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-publish.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> 
> 

## <a name="overview"></a>Descrição geral
> [!NOTE]
> Para concluir este tutorial, precisa de uma conta do Azure. Para obter mais detalhes, veja [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Para fornecer ao seu utilizador um URL que ele possa utilizar para transmitir ou transferir o seu conteúdo, tem primeiro de publicar o elemento mediante a criação de um localizador. Os localizadores fornecem acesso a arquivos de ativo. Os Serviços de Multimédia do Azure suportam dois tipos de localizadores: 

* **Localizadores de transmissão em fluxo (OnDemandOrigin)** . Os localizadores de transmissão em fluxo são utilizados para transmissões em velocidade adaptável. Exemplos de streaming adaptável incluem Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming e streaming adaptável dinâmico sobre HTTP (DASH, também chamado de MPEG-DASH). Para criar um localizador de transmissão em fluxo, o seu elemento tem de incluir um ficheiro .ism. Por exemplo, http://amstest.streaming.mediaservices.windows.net/61b3da1d-96c7-489e-bd21-c5f8a7494b03/scott.ism/manifest.
* **Localizadores progressivos (assinatura de acesso partilhado)** . Os localizadores progressivos são utilizados para entregar vídeos através de transferências progressivas.

Para compilar um URL de transmissão em fluxo HLS, acrescente *(format=m3u8-aapl)* ao URL.

    {streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest(format=m3u8-aapl)

Para compilar um URL de transmissão em fluxo que reproduza elementos de Smooth Streaming, utilize o formato de URL abaixo:

    {streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest

Para compilar um URL de transmissão em fluxo MPEG DASH, acrescente *(format=mpd-time-csf)* ao URL.

    {streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest(format=mpd-time-csf)

Os URLs de assinatura de acesso partilhado têm o formato seguinte:

    {blob container name}/{asset name}/{file name}/{shared access signature}

Para obter mais informações, consulte [visão geral do fornecimento de conteúdo](media-services-deliver-content-overview.md).

> [!NOTE]
> Os localizadores que foram criados no portal do Azure antes de março de 2015 têm uma data de expiração de dois anos.  
> 
> 

Para atualizar uma data de expiração em um localizador, use o pode usar uma [API REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) ou uma [API do .net](https://go.microsoft.com/fwlink/?LinkID=533259). 

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

## <a name="play-content-in-the-portal"></a>Reproduzir conteúdo no portal
Pode testar o vídeo num leitor de conteúdos no portal do Azure.

Selecione o vídeo e, em seguida, selecione **OK**.

![Reproduzir o vídeo no portal do Azure](./media/media-services-portal-vod-get-started/media-services-play.png)

São aplicáveis algumas considerações:

* Confirme que o vídeo foi publicado.
* O leitor de multimédia do portal do Azure reproduz a partir do ponto final da transmissão em fluxo predefinido. Se quiser reproduzir a partir de um ponto final da transmissão em fluxo não predefinido, selecione e copie o URL e cole-o noutro leitor. Por exemplo, pode testar o vídeo no [Leitor de Multimédia do Azure](https://aka.ms/azuremediaplayer).
* O ponto de extremidade de streaming do qual você está transmitindo deve estar em execução.  

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passos Seguintes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

