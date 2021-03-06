---
title: Passe fichas de autenticação para a Azure Media Services | Microsoft Docs
description: Saiba como enviar fichas de autenticação do cliente para o serviço de entrega chave Azure Media Services
services: media-services
keywords: proteção de conteúdos, DRM, autenticação simbólica
author: IngridAtMicrosoft
manager: femila
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 1fe692e1eb20956f339c9b861f50163cee9c5063
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564658"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Saiba como os clientes passam fichas para o serviço de entrega chave Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Os clientes perguntam frequentemente como um jogador pode passar fichas para o serviço de entrega chave Azure Media Services para verificação para que o jogador possa obter a chave. Os Media Services suportam os formatos web token simples (SWT) e JSON Web Token (JWT). A autenticação token é aplicada a qualquer tipo de chave, independentemente de utilizar encriptação comum ou encriptação padrão de encriptação avançada (AES) no sistema.

 Dependendo do jogador e da plataforma que visa, pode passar o token com o seu jogador das seguintes formas:

- Através do cabeçalho de autorização HTTP.
    > [!NOTE]
    > O prefixo "Portador" é esperado de acordo com as especificações OAuth 2.0. Para definir a fonte de vídeo, escolha **AES (JWT Token)** ou **AES (SWT Token)**. O sinal é passado através do cabeçalho de autorização.

- Através da adição de um parâmetro de consulta DE URL com "token=tokenvalue".  
    > [!NOTE]
    > O prefixo "Portador" não é esperado. Como o símbolo é enviado através de uma URL, tens de blindar a corda simbólica. Aqui está um código de amostra C# que mostra como fazê-lo:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- Através do campo CustomData.
Esta opção é utilizada apenas para aquisição de licenças PlayReady, através do campo CustomData do PlayReady License Acquisition Challenge. Neste caso, o símbolo deve estar dentro do documento XML, conforme descrito aqui:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Coloque o seu símbolo de autenticação no elemento Token.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
