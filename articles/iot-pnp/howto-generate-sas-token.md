---
title: Gerar token de segurança para acessar o repositório do IoT Plug and Play Preview | Microsoft Docs
description: Gere um token de assinatura de acesso compartilhado para usar ao acessar um repositório de modelo de visualização de Plug and Play de IoT programaticamente.
author: YasinMSFT
ms.author: yahajiza
ms.date: 08/06/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e5d6e7087a7e3d5f4a001e16c5cfa19a6df6a68e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880543"
---
# <a name="generate-sas-token"></a>Gerar token SAS

Este guia de instruções mostra como gerar programaticamente um token SAS (assinatura de acesso compartilhado) para usar com as APIs de repositório do modelo de visualização de IoT Plug and Play.

## <a name="python"></a>Python

O trecho a seguir mostra como gerar um token SAS usando o Python:

```python
from base64 import b64decode, b64encode
from hashlib import sha256
from hmac import digest
from time import time
from urllib.parse import quote_plus, urlencode

def calculate_sas_token(hostname, repo_id, key_name, key, expiry_in_second):
    expire = int(time() + expiry_in_second)
    sign_value = "{0}\n{1}\n{2}".format(repo_id, quote_plus(hostname), expire)
    sig = b64encode(digest(b64decode(key), sign_value.encode("utf-8"), sha256))
    token = "SharedAccessSignature " + urlencode({
        "sr": hostname, 
        "sig": sig,
        "se": str(expire),
        "skn": key_name,
        "rid": repo_id
        })
    return token
```

## <a name="c"></a>C\#

O trecho a seguir mostra como gerar um token SAS usando C\#:

```csharp
public static string generateSasToken(string hostName, string repoId, string key, string keyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = repoId + "\n" + WebUtility.UrlEncode(hostName) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(
        CultureInfo.InvariantCulture,
        "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}&rid={4}",
        WebUtility.UrlEncode(hostName),
        WebUtility.UrlEncode(signature),
        expiry,
        keyName,
        repoId);

    return token;
}
```

## <a name="use-the-sas-token"></a>Usar o token SAS

Depois de gerar um token SAS, você pode usá-lo para fazer uma solicitação HTTP POST. Por exemplo:

```text
POST https:///models/{modelId}?repositoryId={repositoryId}&api-version=2019-07-01-preview
```

Se você fornecer a um cliente um token SAS, o cliente não terá a chave primária do recurso e não poderá reverter o hash para obtê-lo. Um token SAS dá a você controle sobre o que o cliente pode acessar e por quanto tempo. Quando você altera a chave primária na política, todos os tokens SAS criados a partir dela são invalidados.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu a gerar tokens de segurança a serem usados para acessar os repositórios do modelo IoT Plug and Play Preview, uma próxima etapa sugerida é saber mais no [Guia do desenvolvedor de modelagem da visualização de iot plug and Play Preview](concepts-developer-guide.md).
