---
title: Passe fichas de autenticação para a Azure Media Services Microsoft Docs
description: Saiba como enviar fichas de autenticação do cliente para o serviço de entrega chave da Azure Media Services
services: media-services
keywords: proteção de conteúdos, DRM, autenticação simbólica
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73684935"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Saiba como os clientes passam fichas para o serviço de entrega chave da Azure Media Services
Os clientes perguntam frequentemente como um jogador pode passar fichas para o serviço de entrega chave Azure Media Services para verificação para que o jogador possa obter a chave. Os Media Services suportam os formatos simples de token web (SWT) e JSON Web Token (JWT). A autenticação token é aplicada a qualquer tipo de chave, independentemente de usar encriptação comum ou encriptação de envelope sinuoso (AES) no sistema.

 Dependendo do jogador e da plataforma que você visa, você pode passar o símbolo com o seu jogador das seguintes formas:

- Através do cabeçalho de autorização HTTP.
    > [!NOTE]
    > O prefixo "Bearer" é esperado de acordo com as especificações OAuth 2.0. Um leitor de amostras com a configuração do token é hospedado na [página de demonstração](https://ampdemo.azureedge.net/)do Azure Media Player . Para definir a fonte de vídeo, escolha **AES (JWT Token)** ou **AES (SWT Token)**. O símbolo é passado através do cabeçalho de autorização.

- Através da adição de um parâmetro de consulta URL com "token=tokenvalue".  
    > [!NOTE]
    > O prefixo "Bearer" não é esperado. Como o símbolo é enviado através de uma URL, tens de armar a corda simbólica. Aqui está um código de amostra C# que mostra como fazê-lo:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- Através do campo CustomData.
Esta opção é utilizada apenas para aquisição de licença PlayReady, através do campo CustomData do PlayReady License Acquisition Challenge. Neste caso, o símbolo deve estar dentro do documento XML, tal como aqui descrito:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Coloque o símbolo da autenticação no elemento Token.

- Através de uma lista de reprodução alternativa http live streaming (HLS). Se precisar configurar a autenticação simbólica para reprodução AES + HLS no iOS/Safari, não existe uma forma de enviar diretamente o símbolo. Para obter mais informações sobre como alternar a lista de reprodução para permitir este cenário, consulte esta publicação de [blog](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
