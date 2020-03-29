---
title: Gere segurança para aceder ao ioT Plug e play Preview repositório / Microsoft Docs
description: Gere uma assinatura de acesso partilhado para usar quando aceder a um repositório do modelo IoT Plug e Play Preview programáticamente.
author: Philmea
ms.author: philmea
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f008627317588467d731ccc03aec7738f58e46e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159205"
---
# <a name="generate-sas-token"></a>Gerar ficha SAS

Este guia de como orientar mostra como gerar programáticamente uma assinatura de acesso partilhado (SAS) para usar com as APIs repositórios do modelo IoT Plug e Play Preview.

## <a name="python"></a>Python

O seguinte corte mostra-lhe como gerar um token SAS usando Python:

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

O seguinte corte mostra-lhe como gerar um token SAS utilizando C:\#

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

## <a name="use-the-sas-token"></a>Use o símbolo SAS

Depois de gerar um token SAS, pode usá-lo para fazer um pedido HTTP POST. Por exemplo:

```text
POST https:///models/{modelId}?repositoryId={repositoryId}&api-version=2019-07-01-preview
```

Se você dá a um cliente um token SAS, o cliente não tem a chave principal do recurso, e não pode reverter o hash para obtê-lo. Um token SAS dá-lhe controlo sobre o que o cliente pode aceder, e por quanto tempo. Quando se muda a chave primária da política, quaisquer tokens SAS criados a partir dela são invalidados.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a gerar fichas de segurança para usar para aceder aos repositórios do modelo IoT Plug e Play Preview, um próximo passo sugerido é aprender mais no guia de [modelação IoT Plug e Play Preview.](concepts-developer-guide.md)
