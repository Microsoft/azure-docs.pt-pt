---
title: Segurança e autenticação da Rede de Eventos Azure
description: Este artigo descreve diferentes formas de autenticar o acesso aos seus recursos da Rede de Eventos (WebHook, subscrições, tópicos personalizados)
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: bca450022322db7a7569fa1dc7ce80ec75a9ce69
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774316"
---
# <a name="authenticating-access-to-azure-event-grid-resources"></a>Autenticação de acesso aos recursos da Rede de Eventos Azure
Este artigo fornece informações sobre os seguintes cenários:  

- Autenticar clientes que publicam eventos para tópicos da Grelha de Eventos Azure usando a Assinatura de Acesso Partilhado (SAS) ou chave. 
- Proteja o ponto final do webhook que é usado para receber eventos da Event Grid usando o Azure Ative Directory (Azure AD) ou um segredo partilhado.

## <a name="authenticate-publishing-clients-using-sas-or-key"></a>Autenticar clientes editoriais usando SAS ou chave
Os tópicos personalizados utilizam a Assinatura de Acesso Partilhado (SAS) ou a autenticação chave. Recomendamos o SAS, mas a autenticação chave fornece uma programação simples, e é compatível com muitos editores de webhook existentes.

Inclui o valor de autenticação no cabeçalho HTTP. Para sas, utilize **aeg-sas-token** para o valor do cabeçalho. Para autenticação chave, utilize a **tecla aeg-sas** para o valor do cabeçalho.

### <a name="key-authentication"></a>Autenticação chave

A autenticação-chave é a forma mais simples de autenticação. Utilize o formato: `aeg-sas-key: <your key>` no cabeçalho da mensagem.

Por exemplo, passa-se uma chave com:

```
aeg-sas-key: XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

Também pode especificar `aeg-sas-key` como parâmetro de consulta. 

```
https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01&&aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Tokens SAS

As fichas SAS para A Grelha de Eventos incluem o recurso, um tempo de validade e uma assinatura. O formato do token SAS é: `r={resource}&e={expiration}&s={signature}` .

O recurso é o caminho para o tópico da grelha de eventos para o qual está a enviar eventos. Por exemplo, um caminho de recursos válido é: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01` . Para ver todas as versões API suportadas, consulte os tipos de [recursos Microsoft.EventGrid](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions). 

Gera-se a assinatura a partir de uma chave.

Por exemplo, um valor válido **aeg-sas-token** é:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

O exemplo seguinte cria um símbolo SAS para utilização com a Grelha de Eventos:

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

Todos os eventos ou dados escritos em disco pelo serviço Event Grid são encriptados por uma chave gerida pela Microsoft, garantindo que está encriptado em repouso. Adicionalmente, o período máximo de tempo que os eventos ou dados retidos é de 24 horas de adesão à política de [retry da Rede](delivery-and-retry.md)de Eventos . A Grelha de Eventos eliminará automaticamente todos os eventos ou dados após 24 horas, ou o evento tem tempo de vida, o que for menor.

## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Autenticar entrega de eventos em pontos finais webhook
As seguintes secções descrevem como autenticar a entrega do evento em pontos finais do webhook. Você precisa usar um mecanismo de aperto de mão de validação independentemente do método que você usar. Consulte a entrega do [evento Webhook](webhook-event-delivery.md) para mais detalhes. 

### <a name="using-azure-active-directory-azure-ad"></a>Utilização de Diretório Ativo Azure (Azure AD)
Pode fixar o ponto final do webhook que é usado para receber eventos da Rede de Eventos utilizando o Azure AD. Você precisará criar uma aplicação Azure AD, criar uma função e serviço principal na sua aplicação autorizando a Rede de Eventos, e configurar a subscrição do evento para usar a aplicação Azure AD. Saiba como configurar o [Diretório Ativo Azure com grelha](secure-webhook-delivery.md)de eventos.

### <a name="using-client-secret-as-a-query-parameter"></a>Usando o segredo do cliente como parâmetro de consulta
Também pode fixar o seu ponto final do webhook adicionando parâmetros de consulta ao URL de destino webhook especificado como parte da criação de uma Subscrição de Eventos. Estabeleça um dos parâmetros de consulta para ser um segredo de cliente, como um [sinal de acesso](https://en.wikipedia.org/wiki/Access_token) ou um segredo partilhado. O serviço De Rede de Eventos inclui todos os parâmetros de consulta em cada pedido de entrega de eventos ao webhook. O serviço webhook pode recuperar e validar o segredo. Se o segredo do cliente for atualizado, a subscrição do evento também precisa de ser atualizada. Para evitar falhas de entrega durante esta rotação secreta, faça com que o webhook aceite segredos antigos e novos por uma duração limitada antes de atualizar a subscrição do evento com o novo segredo. 

Como os parâmetros de consulta podem conter segredos de cliente, eles são tratados com cuidado extra. São armazenados como encriptados e não são acessíveis aos operadores de serviço. Não estão registados como parte dos registos/vestígios de serviço. Ao recuperar as propriedades de Subscrição de Eventos, os parâmetros de consulta de destino não são devolvidos por padrão. Por exemplo: [--incluir o](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) parâmetro de url de ponto final completo deve ser utilizado no Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Para obter mais informações sobre a entrega de eventos aos webhooks, consulte a entrega de [eventos webhook](webhook-event-delivery.md)

> [!IMPORTANT]
A Azure Event Grid suporta apenas pontos finais **https** webhook. 

## <a name="next-steps"></a>Passos seguintes

- Para uma introdução à Grelha de Eventos, consulte sobre a grelha de [eventos](overview.md)
