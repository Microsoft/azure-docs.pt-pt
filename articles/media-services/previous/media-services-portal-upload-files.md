---
title: Carregar ficheiros para uma conta dos Serviços de Multimédia no portal do Azure | Microsoft Docs
description: Este tutorial orienta-o ao longo dos passos para carregar ficheiros para uma conta dos Serviços de Multimédia no portal do Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: c2dc193d65ff1c85837477c0a8fd345f11d59bcd
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009758"
---
# <a name="upload-files-to-a-media-services-account-in-the-azure-portal"></a>Carregar ficheiros para uma conta dos Serviços de Multimédia no portal do Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Portal](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> 

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. Para os ficheiros de upload atualizados com portal, consulte [o portal Use para carregar, codificar e transmitir conteúdo](../latest/manage-assets-quickstart.md).<br/>Além disso, confira: [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Nos Serviços de Multimédia do Azure, os ficheiros digitais são carregados para um elemento. O elemento pode conter ficheiros de vídeo, áudio, imagens, coleções de miniaturas, pistas de texto e legendas (e os metadados relativos a esses ficheiros). Assim que os ficheiros forem carregados, o seu conteúdo é armazenado em segurança na cloud, para processamento adicional e transmissão em fluxo.

Os Serviços de Multimédia têm um tamanho de ficheiro máximo para o processamento de ficheiros. Para obter mais informações sobre limites de tamanho de ficheiros, veja [Media Services quotas and limitations](media-services-quotas-and-limitations.md) (Quotas e limitações dos Serviços de Multimédia).

Para concluir este tutorial, precisa de uma conta do Azure. Para mais detalhes, consulte [o julgamento gratuito do Azure.](https://azure.microsoft.com/pricing/free-trial/) 

## <a name="upload-files"></a>Carregar ficheiros
1. No [portal do Azure](https://portal.azure.com/) selecione a sua conta de Serviços de Multimédia do Azure.
2. Selecione   >  **Configurações Ativos**. Em seguida, selecione o botão **Carregar**.
   
    ![Carregar ficheiros](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    A janela **Carregar um elemento de vídeo** é apresentada.
   
   > [!NOTE]
   > Os Serviços de Multimédia não limitam o tamanho de ficheiro para o carregamento de vídeos.
 
3. No seu computador, aceda ao vídeo que quer carregar. Selecione o vídeo e, em seguida, selecione **OK**.  
   
    O carregamento começa. Pode ver o progresso abaixo do nome do ficheiro.  

Quando o carregamento estiver concluído, o elemento novo é apresentado no painel **Elementos**. 

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passos seguintes
* Veja [Encode your uploaded assets](media-services-portal-encode.md) (Codificar os seus elementos carregados).

* Também pode utilizar as Funções do Azure para acionar um trabalho de codificação num ficheiro que esteja a chegar ao contentor configurado. Para obter mais informações, veja o exemplo em [Media Services: Integrating Azure Media Services with Azure Functions and Logic Apps](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/) (Serviços de Multimédia: Integrar os Serviços de Multimédia nas Funções do Azure e no Logic Apps).
