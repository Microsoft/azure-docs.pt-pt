---
title: Segurança e autenticação da Grelha de Eventos Azure
description: Este artigo descreve diferentes formas de autenticar o acesso aos seus recursos de Grade de Eventos (WebHook, subscrições, tópicos personalizados)
services: event-grid
author: femila
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: femila
ms.openlocfilehash: 8335d5a41dc2f322623c163e08f8a4a2c1be8360
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84559001"
---
# <a name="authenticating-access-to-azure-event-grid-resources"></a>Autenticação de acesso aos recursos da Grelha de Eventos Azure
Este artigo fornece informações sobre os seguintes cenários:  

- Autenticar clientes que publicam eventos para tópicos da Azure Event Grid utilizando a Assinatura de Acesso Partilhado (SAS) ou chave. 
- Proteja o ponto final webhook que é usado para receber eventos da Grade de Eventos usando O Diretório Ativo Azure (Azure AD) ou um segredo compartilhado.

## <a name="authenticate-publishing-clients-using-sas-or-key"></a>Autenticar clientes editoriais usando SAS ou chave
Os tópicos personalizados utilizam a Assinatura de Acesso Partilhado (SAS) ou a autenticação de chaves. Recomendamos SAS, mas a autenticação chave fornece uma programação simples, e é compatível com muitos editores webhook existentes.

Inclui o valor de autenticação no cabeçalho HTTP. Para SAS, utilize **a aeg-sas-token** para o valor do cabeçalho. Para autenticação de chave, utilize **a chave aeg-sas para** o valor do cabeçalho.

### <a name="key-authentication"></a>Autenticação de chaves

A autenticação da chave é a forma mais simples de autenticação. Utilize o formato: `aeg-sas-key: <your key>` no cabeçalho da mensagem.

Por exemplo, passa-se uma chave com:

```
aeg-sas-key: XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

Também pode especificar `aeg-sas-key` como parâmetro de consulta. 

```
https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01&&aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Tokens SAS

Os tokens SAS para a Grade de Eventos incluem o recurso, um tempo de validade e uma assinatura. O formato do símbolo SAS é: `r={resource}&e={expiration}&s={signature}` .

O recurso é o caminho para o tópico da grelha de eventos para o qual está a enviar eventos. Por exemplo, um caminho de recurso válido é: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01` . Para ver todas as versões API suportadas, consulte os [tipos de recursos microsoft.EventGrid](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions). 

Gera-se a assinatura a partir de uma chave.

Por exemplo, um valor **aeg-sas-token** válido é:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

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

### <a name="encryption-at-rest"></a>Encriptação inativa

Todos os eventos ou dados escritos em disco pelo serviço Desempenhado pelo Serviço de Grelha de Eventos são encriptados por uma chave gerida pela Microsoft, garantindo que está encriptada em repouso. Adicionalmente, o período máximo de tempo que os eventos ou dados retidos são de 24 horas de adesão à [política de relíndi da Grelha de Eventos.](delivery-and-retry.md) A Grelha de Eventos eliminará automaticamente todos os eventos ou dados após 24 horas, ou o tempo de vida do evento, o que for menor.

## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Autenticar entrega de eventos para pontos finais webhook
As secções seguintes descrevem como autenticar a entrega do evento nos pontos finais webhook. Tem de utilizar um mecanismo de aperto de mão de validação, independentemente do método utilizado. Consulte a [entrega do evento Webhook](webhook-event-delivery.md) para obter mais detalhes. 

### <a name="using-azure-active-directory-azure-ad"></a>Utilizando o Diretório Ativo Azure (Azure AD)
Você pode proteger o ponto final webhook que é usado para receber eventos da Grade de Eventos usando Azure AD. Você precisará criar uma aplicação AD Azure, criar uma função e principal de serviço na sua aplicação autorizando a Grade de Eventos, e configurar a subscrição do evento para usar a aplicação AZure AD. Saiba como [configurar o Azure Ative Directory com a Grade de Eventos.](secure-webhook-delivery.md)

### <a name="using-client-secret-as-a-query-parameter"></a>Usando o segredo do cliente como parâmetro de consulta
Também pode proteger o seu ponto final webhook adicionando parâmetros de consulta ao URL de destino webhook especificado como parte da criação de uma Subscrição de Eventos. Desacorde um dos parâmetros de consulta para ser um segredo de cliente, como um [token de acesso](https://en.wikipedia.org/wiki/Access_token) ou um segredo partilhado. O serviço 'Grade de Eventos' inclui todos os parâmetros de consulta em cada pedido de entrega de eventos ao webhook. O serviço webhook pode recuperar e validar o segredo. Se o segredo do cliente for atualizado, a subscrição do evento também precisa de ser atualizada. Para evitar falhas de entrega durante esta rotação secreta, faça com que o webhook aceite segredos antigos e novos por uma duração limitada antes de atualizar a subscrição do evento com o novo segredo. 

Como os parâmetros de consulta podem conter segredos de clientes, eles são tratados com cuidado extra. São armazenados como encriptados e não são acessíveis aos operadores de serviço. Não são registados como parte dos registos/vestígios de serviço. Ao recuperar as propriedades de Subscrição de Eventos, os parâmetros de consulta de destino não são devolvidos por padrão. For example: [--include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) parameter is to be used in Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Para obter mais informações sobre a entrega de eventos a webhooks, consulte [a entrega do evento Webhook](webhook-event-delivery.md)

> [!IMPORTANT]
A Azure Event Grid suporta apenas pontos finais **https** webhook. 

## <a name="next-steps"></a>Passos seguintes

- Para uma introdução à Grade de Eventos, consulte [Sobre a Grelha de Eventos](overview.md)
