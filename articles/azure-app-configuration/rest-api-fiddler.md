---
title: Azure Ative Directory REST API - Teste usando violinista
description: Use o Violinista para testar a API de Configuração de Aplicação Azure
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 1142aa25212d87c5484963cda4e172df3d1fbafc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932613"
---
# <a name="test-the-azure-app-configuration-rest-api-using-fiddler"></a>Teste a configuração de aplicativo Azure REST API usando Fiddler

Para testar a API REST utilizando [o Fiddler,](https://www.telerik.com/fiddler)terá de incluir os cabeçalhos HTTP necessários para [a autenticação](./rest-api-authentication-hmac.md) nos seus pedidos. Eis como configurar o Fiddler para testar a API REST, gerando automaticamente os cabeçalhos de autenticação:

1. Certifique-se de que o TLS 1.2 é um protocolo permitido:
    1. Aceda às  >  **opções de**  >  **ferramentas HTTPS**).
    1. Certifique-se de que o **tráfego HTTPS desencriptado** é verificado.
    1. Na lista de protocolos, adicione **tls1.2** se não estiver presente.
1. **Editor de script de violino aberto** ou pressione **Ctrl-R** dentro do Violinista
1. Adicione o seguinte código dentro da `Handlers` classe antes da `OnBeforeRequest` função

    ```js
    static function SignRequest(oSession: Session, credential: String, secret: String) {
        var utcNow = DateTimeOffset.UtcNow.ToString("r", System.Globalization.DateTimeFormatInfo.InvariantInfo);
        var contentHash = ComputeSHA256Hash(oSession.RequestBody);
        var stringToSign = oSession.RequestMethod.ToUpperInvariant() + "\n" + oSession.PathAndQuery + "\n" + utcNow +";" + oSession.hostname + ";" + contentHash;
        var signature = ComputeHMACHash(secret, stringToSign);

        oSession.oRequest.headers["x-ms-date"] = utcNow;
        oSession.oRequest.headers["x-ms-content-sha256"] = contentHash;
        oSession.oRequest.headers["Authorization"] = "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature=" + signature;
    }

    static function ComputeSHA256Hash(content: Byte[]) {
        var sha256 = System.Security.Cryptography.SHA256.Create();
        try {
            return Convert.ToBase64String(sha256.ComputeHash(content));
        }
        finally {
            sha256.Dispose();
        }
    }

    static function ComputeHMACHash(secret: String, content: String) {
        var hmac = new System.Security.Cryptography.HMACSHA256(Convert.FromBase64String(secret));
        try {
            return Convert.ToBase64String(hmac.ComputeHash(System.Text.Encoding.ASCII.GetBytes(content)));
        }
        finally {
            hmac.Dispose();
        }
    }
    ```

1. Adicione o seguinte código no final da `OnBeforeRequest` função. Atualizar a chave de acesso como indicado pelo comentário TODO.

    ```js
    if (oSession.isFlagSet(SessionFlags.RequestGeneratedByFiddler) &&
        oSession.hostname.EndsWith(".azconfig.io", StringComparison.OrdinalIgnoreCase)) {

        // TODO: Replace the following placeholders with your access key
        var credential = "<Credential>"; // Id
        var secret = "<Secret>"; // Value

        SignRequest(oSession, credential, secret);
    }
    ```
1. Use [o Compositor do Violinista](https://docs.telerik.com/fiddler/Generate-Traffic/Tasks/CreateNewRequest) para gerar e enviar um pedido
