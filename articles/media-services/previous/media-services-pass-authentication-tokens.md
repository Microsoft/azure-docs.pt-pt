---
title: Passar tokens de autenticação para serviços de multimédia do Azure | Documentos da Microsoft
description: Saiba como enviar tokens de autenticação do cliente para o serviço de entrega de chave de serviços de multimédia do Azure
services: media-services
keywords: autenticação de token de proteção de conteúdo, DRM,
documentationcenter: ''
author: dbgeorge
manager: jasonsue
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: dwgeo
ms.openlocfilehash: 6fc13010d5dd888f28b5634b1f70aaa3326e54cf
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57837512"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Saiba como os clientes passam tokens para o serviço de entrega de chave de serviços de multimédia do Azure
Os clientes costumam perguntar como um jogador pode passar tokens para o serviço de entrega de chave de serviços de multimédia do Azure para efeitos de verificação para que o jogador pode obter a chave. Serviços de multimédia suportam o simple web tokens (SWT) e formatos de JSON Web Token (JWT). Autenticação de token é aplicada a qualquer tipo de chave, independentemente de utilizar a encriptação comum ou encriptação de envelope Advanced Encryption Standard (AES) no sistema.

 Consoante a plataforma de destino e jogador, pode passar o token com seu player das seguintes formas:

- Por meio do cabeçalho de autorização de HTTP.
    > [!NOTE]
    > O prefixo de "Bearer" é esperado pelas especificações de OAuth 2.0. Um player de exemplo com a configuração de token está alojado no leitor de multimédia do Azure [página de demonstração](https://ampdemo.azureedge.net/). Para definir a origem do vídeo, escolha **(JWT Token) do AES** ou **AES (SWT Token)**. O token é passado com o cabeçalho de autorização.

- Por meio da adição de um URL de consulta parâmetro com "token = tokenvalue."  
    > [!NOTE]
    > Não se espera que o prefixo de "Bearer". Como o token é enviado através de um URL, precisa proteger a cadeia de caracteres de token. Aqui está um C# código de exemplo que mostra como fazê-lo:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- Através do campo CustomData.
Esta opção é utilizada para a aquisição de licença de PlayReady apenas, através do campo de CustomData do desafio de aquisição de licença PlayReady. Neste caso, o token deve ser dentro do documento XML, conforme descrito aqui:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Coloque o token de autenticação do elemento de Token.

- Por meio de uma lista de reprodução do HTTP Live Streaming (HLS) alternativo. Se tiver de configurar a autenticação de token para o AES + HLS reprodução no iOS/Safari, não uma maneira de enviar diretamente no token. Para obter mais informações sobre como alternativa a lista de reprodução para ativar este cenário, consulte esta [mensagem de blogue](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Passos Seguintes

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]