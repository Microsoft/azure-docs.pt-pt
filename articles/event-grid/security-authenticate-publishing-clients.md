---
title: Autenticar clientes que publicam eventos para tópicos ou domínios personalizados da Grade de Eventos
description: Este artigo descreve diferentes formas de autenticar clientes que publicam eventos para tópicos personalizados da Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 2c415b7e1bb6bd7a2116da82c7d8f1de205009d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94886265"
---
# <a name="authenticate-publishing-clients-azure-event-grid"></a>Autenticar clientes editoriais (Azure Event Grid)
Este artigo fornece informações sobre clientes autenticados que publicam eventos para tópicos ou domínios da Azure Event Grid utilizando **a chave de acesso** ou o token de Assinatura de Acesso Partilhado **(SAS).** Recomendamos a utilização de token SAS, mas a autenticação chave fornece uma programação simples, e é compatível com muitos editores webhook existentes.  

## <a name="authenticate-using-an-access-key"></a>Autenticar usando uma chave de acesso
A autenticação da chave de acesso é a forma mais simples de autenticação. Pode passar a tecla de acesso como cabeçalho HTTP ou parâmetro de consulta URL. 

### <a name="access-key-in-a-http-header"></a>Chave de acesso num cabeçalho HTTP
Passe a chave de acesso como um valor para o cabeçalho HTTP: `aeg-sas-key` .

```
aeg-sas-key: XXXXXXXXXXXXXXXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="access-key-as-a-query-parameter"></a>Chave de acesso como parâmetro de consulta
Também pode especificar `aeg-sas-key` como parâmetro de consulta. 

```
https://<yourtopic>.<region>.eventgrid.azure.net/api/events?aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

Para obter instruções sobre como obter as teclas de acesso para um tópico ou domínio, consulte [as teclas de acesso](get-access-keys.md).

## <a name="authenticate-using-a-sas-token"></a>Autenticar usando um token SAS
Os tokens SAS para um recurso de Grade de Evento incluem o recurso, o tempo de validade e uma assinatura. O formato do símbolo SAS é: `r={resource}&e={expiration}&s={signature}` .

O recurso é o caminho para o tópico da grelha de eventos para o qual está a enviar eventos. Por exemplo, um caminho de recurso válido é: `https://<yourtopic>.<region>.eventgrid.azure.net/api/events` . Para ver todas as versões API suportadas, consulte os [tipos de recursos microsoft.EventGrid](/azure/templates/microsoft.eventgrid/allversions). 

Primeiro, gere programáticamente um token SAS e, em seguida, use o `aeg-sas-token` cabeçalho ou `Authorization SharedAccessSignature` cabeçalho para autenticar com a Grade de Eventos. 

### <a name="generate-sas-token-programmatically"></a>Gerar símbolo SAS programáticamente
O exemplo a seguir cria um token SAS para utilização com grade de eventos:

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

```python
def generate_sas_token(uri, key, expiry=3600):
    ttl = datetime.datetime.utcnow() + datetime.timedelta(seconds=expiry)
    encoded_resource = urllib.parse.quote_plus(uri)
    encoded_expiration_utc = urllib.parse.quote_plus(ttl.isoformat())

    unsigned_sas = f'r={encoded_resource}&e={encoded_expiration_utc}'
    signature = b64encode(HMAC(b64decode(key), unsigned_sas.encode('utf-8'), sha256).digest())
    encoded_signature = urllib.parse.quote_plus(signature)
    
    token = f'r={encoded_resource}&e={encoded_expiration_utc}&s={encoded_signature}'

    return token
```

### <a name="using-aeg-sas-token-header"></a>Usando cabeçalho aeg-sas-token
Aqui está um exemplo de passar o símbolo SAS como um valor para o `aeg-sas-token` cabeçalho. 

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevents&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

### <a name="using-authorization-header"></a>Usando o cabeçalho de autorização
Aqui está um exemplo de passar o símbolo SAS como um valor para o `Authorization` cabeçalho. 

```http
Authorization: SharedAccessSignature r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevents&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

## <a name="next-steps"></a>Passos seguintes
Consulte [a autenticação de entrega do Evento](security-authentication.md) para saber sobre a autenticação com os manipuladores de eventos para entregar eventos. 
