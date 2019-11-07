---
title: Passar tokens de autenticação para os serviços de mídia do Azure | Microsoft Docs
description: Saiba como enviar tokens de autenticação do cliente para o serviço de entrega de chaves dos serviços de mídia do Azure
services: media-services
keywords: proteção de conteúdo, DRM, autenticação de token
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 15d4cbc372f5d5ec0d323170189329152ed436e3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684935"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Saiba como os clientes passam tokens para o serviço de entrega de chaves dos serviços de mídia do Azure
Os clientes geralmente perguntam como um jogador pode passar tokens para o serviço de entrega de chaves dos serviços de mídia do Azure para verificação, para que o Player possa obter a chave. Os serviços de mídia oferecem suporte aos formatos Simple Web token (SWT) e token Web JSON (JWT). A autenticação de token é aplicada a qualquer tipo de chave, independentemente de você usar criptografia comum ou criptografia de envelope de criptografia AES (AES) no sistema.

 Dependendo do Player e da plataforma de destino, você pode passar o token com o Player das seguintes maneiras:

- Por meio do cabeçalho de autorização HTTP.
    > [!NOTE]
    > O prefixo "portador" é esperado de acordo com as especificações do OAuth 2,0. Um player de exemplo com a configuração de token é hospedado na [página de demonstração](https://ampdemo.azureedge.net/)de player de mídia do Azure. Para definir a fonte de vídeo, escolha **AES (token JWT)** ou **AES (token SWT)** . O token é passado por meio do cabeçalho Authorization.

- Por meio da adição de um parâmetro de consulta de URL com "token = TokenValue".  
    > [!NOTE]
    > O prefixo "portador" não é esperado. Como o token é enviado por meio de uma URL, você precisa proteger a cadeia de caracteres do token. Aqui está um C# código de exemplo que mostra como fazer isso:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- Por meio do campo CustomData.
Essa opção é usada apenas para aquisição de licença do PlayReady, por meio do campo CustomData do desafio de aquisição de licença do PlayReady. Nesse caso, o token deve estar dentro do documento XML, conforme descrito aqui:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Coloque seu token de autenticação no elemento token.

- Por meio de uma playlist alternativa de HTTP Live Streaming (HLS). Se você precisar configurar a autenticação de token para reprodução AES + HLS no iOS/Safari, não há uma maneira de enviar diretamente no token. Para obter mais informações sobre como alternar a lista de reprodução para habilitar esse cenário, consulte esta [postagem no blog](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
