---
title: Passe fichas de autenticação aos Serviços de Comunicação Social v3 | Microsoft Docs
description: Saiba como enviar fichas de autenticação do cliente para o serviço de entrega de chaves V3 dos Serviços de Comunicação Social
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 590309ad392876ba3c5cb6d21abe777ab5a93efb
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572544"
---
# <a name="pass-tokens-to-the-azure-media-services-v3-key-delivery-service"></a>Passe fichas para o serviço de entrega de chaves Azure Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Os clientes perguntam frequentemente como um jogador pode passar fichas para o serviço de entrega chave Azure Media Services para verificação para que o jogador possa obter a chave. Os Media Services suportam os formatos web token simples (SWT) e JSON Web Token (JWT). A autenticação token é aplicada a qualquer tipo de chave, independentemente de utilizar encriptação comum ou encriptação padrão de encriptação avançada (AES) no sistema.

 Dependendo do jogador e da plataforma que visa, pode passar o token com o seu jogador das seguintes formas:

## <a name="pass-a-token-through-the-http-authorization-header"></a>Passe um token através do cabeçalho de autorização HTTP

> [!NOTE]
> O prefixo "Portador" é esperado de acordo com as especificações OAuth 2.0. Para definir a fonte de vídeo, escolha **AES (JWT Token)** ou **AES (SWT Token)**. O sinal é passado através do cabeçalho de autorização.

## <a name="pass-a-token-via-the-addition-of-a-url-query-parameter-with-tokentokenvalue"></a>Passe um token através da adição de um parâmetro de consulta URL com "token=tokenvalue".

> [!NOTE]
> O prefixo "Portador" não é esperado. Como o símbolo é enviado através de uma URL, tens de blindar a corda simbólica. Aqui está um código de amostra C# que mostra como fazê-lo:

```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
```

## <a name="pass-a-token-through-the-customdata-field"></a>Passe um símbolo através do campo CustomData

Esta opção é utilizada apenas para aquisição de licenças PlayReady, através do campo CustomData do PlayReady License Acquisition Challenge. Neste caso, o símbolo deve estar dentro do documento XML, conforme descrito aqui:

```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
```

Coloque o seu símbolo de autenticação no elemento Token.
