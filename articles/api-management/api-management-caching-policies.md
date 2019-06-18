---
title: Políticas de colocação em cache a gestão de API do Azure | Documentos da Microsoft
description: Saiba mais sobre as políticas de colocação em cache disponíveis para utilização na gestão de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 8147199c-24d8-439f-b2a9-da28a70a890c
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: 08b6f803d6994015432bf68c7b3edae14af8f976
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61095457"
---
# <a name="api-management-caching-policies"></a>Políticas de gestão de API de colocação em cache
Este tópico fornece uma referência para as seguintes políticas de gestão de API. Para informações sobre como adicionar e configurar as políticas, consulte [políticas de gestão de API](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="CachingPolicies"></a> Políticas de colocação em cache

- Políticas de colocação em cache de resposta
    - [Obter a partir do cache](api-management-caching-policies.md#GetFromCache) -realizar o cache de procurar e devolver respostas válidas de em cache, quando disponível.
    - [Store à cache](api-management-caching-policies.md#StoreToCache) -armazena em cache respostas de acordo com a configuração de controlo de cache especificado.
- Valor de políticas de colocação em cache
    - [Obter o valor da cache](#GetFromCacheByKey) -obter um item em cache por chave.
    - [Store valor em cache](#StoreToCacheByKey) -Store um item na cache por chave.
    - [Remover o valor da cache](#RemoveCacheByKey) -remover um item na cache por chave.

## <a name="GetFromCache"></a> Obter a partir do cache
Utilize o `cache-lookup` política para realizar o cache de procurar e devolver uma resposta em cache válida quando disponível. Esta política pode ser aplicada em casos em que o conteúdo de resposta permanece estático durante um período de tempo. Resposta de colocação em cache reduz a largura de banda e os requisitos de processamento imposta na back-end web server e reduz a latência percebida pelos consumidores de API.

> [!NOTE]
> Esta política tem de ter um correspondente [Store à cache](api-management-caching-policies.md#StoreToCache) política.

### <a name="policy-statement"></a>Declaração de política

```xml
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" caching-type="prefer-external | external | internal" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">
  <vary-by-header>Accept</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Accept-Charset</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Authorization</vary-by-header>
  <!-- should be present when allow-private-response-caching is "true"-->
  <vary-by-header>header name</vary-by-header>
  <!-- optional, can repeated several times -->
  <vary-by-query-parameter>parameter name</vary-by-query-parameter>
  <!-- optional, can repeated several times -->
</cache-lookup>
```

### <a name="examples"></a>Exemplos

#### <a name="example"></a>Exemplo

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true" caching-type="internal" >
            <vary-by-query-parameter>version</vary-by-query-parameter>
        </cache-lookup>
    </inbound>
    <outbound>
        <cache-store duration="seconds" />
        <base />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Exemplo de uso de expressões de política
Este exemplo mostra como configurar a gestão de API colocação em cache duração da resposta que corresponda ao cache de resposta do serviço de back-end conforme especificado pelo serviço de segurança `Cache-Control` diretiva. Para uma demonstração de configurar e utilizar esta política, consulte [177 do Cloud Cover episódio: Mais funcionalidades de gestão de API com Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e avanço rápido para 25:25.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

Para obter mais informações, consulte [expressões de diretriz](api-management-policy-expressions.md) e [variável de contexto](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elementos

|Name|Descrição|Necessário|
|----------|-----------------|--------------|
|cache-lookup|Elemento raiz.|Sim|
|vary-by-header|Iniciar a colocação em cache as respostas por valor de cabeçalho especificado, como Accept, Accept-conjunto de carateres, Accept-Encoding, Accept-Language, autorização, Expect, do Host, If-Match.|Não|
|vary-by-query-parameter|Inicie a colocação em cache as respostas por valor dos parâmetros de consulta especificada. Introduza um único ou vários parâmetros. Utilize ponto e vírgula como separador. Se nenhum forem especificados, todos os parâmetros de consulta são utilizados.|Não|

### <a name="attributes"></a>Atributos

| Name                           | Descrição                                                                                                                                                                                                                                                                                                                                                 | Necessário | Predefinição           |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| allow-private-response-caching | Quando definido como `true`, permite a colocação em cache de pedidos que contenham um cabeçalho de autorização.                                                                                                                                                                                                                                                                        | Não       | false             |
| caching-type               | Escolha entre os seguintes valores do atributo:<br />- `internal` utilizar a cache de gestão de API interna,<br />- `external` Para utilizar a cache de externa, conforme descrito em [utilizar uma Cache do Azure externo para Redis na gestão de API do Azure](api-management-howto-cache-external.md),<br />- `prefer-external` Para utilizar a cache externo se configurado ou cache interna; caso contrário. | Não       | `prefer-external` |
| downstream-caching-type        | Este atributo tem de ser definido para um dos seguintes valores.<br /><br /> -nenhum - não é permitida a colocação em cache downstream.<br />-privada - downstream colocação em cache privada é permitida.<br />-público - particular e compartilhado downstream colocação em cache é permitida.                                                                                                          | Não       | Nenhum              |
| must-revalidate                | Se a colocação em cache downstream está ativada neste atributo transforma ou desativar o `must-revalidate` diretiva de controle de cache nas respostas do gateway.                                                                                                                                                                                                                      | Não       | true              |
| variar pelo desenvolvedor              | Defina como `true` a respostas de cache por [chave de subscrição](https://docs.microsoft.com/azure/api-management/api-management-subscriptions).                                                                                                                                                                                                                                                                                                         | Sim      |         Falso          |
| vary-by-developer-groups       | Defina como `true` a respostas de cache por [grupo de utilizadores](https://docs.microsoft.com/azure/api-management/api-management-howto-create-groups).                                                                                                                                                                                                                                                                                                             | Sim      |       Falso            |

### <a name="usage"></a>Utilização
Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **As secções de política:** entrada
- **Âmbitos de política:** API, operação, o produto

## <a name="StoreToCache"></a> Store à cache
O `cache-store` política coloca em cache respostas de acordo com as definições de cache especificado. Esta política pode ser aplicada em casos em que o conteúdo de resposta permanece estático durante um período de tempo. Resposta de colocação em cache reduz a largura de banda e os requisitos de processamento imposta na back-end web server e reduz a latência percebida pelos consumidores de API.

> [!NOTE]
> Esta política tem de ter um correspondente [obter a partir do cache](api-management-caching-policies.md#GetFromCache) política.

### <a name="policy-statement"></a>Declaração de política

```xml
<cache-store duration="seconds" />
```

### <a name="examples"></a>Exemplos

#### <a name="example"></a>Exemplo

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false">
            <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
        </cache-lookup>
    </inbound>
    <outbound>
        <base />
        <cache-store duration="3600" />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Exemplo de uso de expressões de política
Este exemplo mostra como configurar a gestão de API colocação em cache duração da resposta que corresponda ao cache de resposta do serviço de back-end conforme especificado pelo serviço de segurança `Cache-Control` diretiva. Para uma demonstração de configurar e utilizar esta política, consulte [177 do Cloud Cover episódio: Mais funcionalidades de gestão de API com Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e avanço rápido para 25:25.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

Para obter mais informações, consulte [expressões de diretriz](api-management-policy-expressions.md) e [variável de contexto](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elementos

|Name|Descrição|Necessário|
|----------|-----------------|--------------|
|cache-store|Elemento raiz.|Sim|

### <a name="attributes"></a>Atributos

| Name             | Descrição                                                                                                                                                                                                                                                                                                                                                 | Necessário | Predefinição           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| Duração         | Tempo de vida das entradas em cache, especificados em segundos.                                                                                                                                                                                                                                                                                                   | Sim      | N/A               |

### <a name="usage"></a>Utilização
Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **As secções de política:** saída
- **Âmbitos de política:** API, operação, o produto

## <a name="GetFromCacheByKey"></a> Obter o valor da cache
Utilize o `cache-lookup-value` política para efetuar a pesquisa de cache por chave e retornam um valor em cache. A chave pode ter um valor de cadeia de caracteres arbitrária e é, geralmente, fornecida com uma expressão de política.

> [!NOTE]
> Esta política tem de ter um correspondente [Store valor em cache](#StoreToCacheByKey) política.

### <a name="policy-statement"></a>Declaração de política

```xml
<cache-lookup-value key="cache key value"
    default-value="value to use if cache lookup resulted in a miss"
    variable-name="name of a variable looked up value is assigned to"
    caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Exemplo
Para obter mais informações e exemplos desta política, consulte [personalizadas de colocação em cache na gestão de API do Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).

```xml
<cache-lookup-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    variable-name="userprofile" />

```

### <a name="elements"></a>Elementos

|Name|Descrição|Necessário|
|----------|-----------------|--------------|
|cache-lookup-value|Elemento raiz.|Sim|

### <a name="attributes"></a>Atributos

| Name             | Descrição                                                                                                                                                                                                                                                                                                                                                 | Necessário | Predefinição           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| caching-type | Escolha entre os seguintes valores do atributo:<br />- `internal` utilizar a cache de gestão de API interna,<br />- `external` Para utilizar a cache de externa, conforme descrito em [utilizar uma Cache do Azure externo para Redis na gestão de API do Azure](api-management-howto-cache-external.md),<br />- `prefer-external` Para utilizar a cache externo se configurado ou cache interna; caso contrário. | Não       | `prefer-external` |
| valor predefinido    | Um valor que será atribuído à variável se a pesquisa de chave de cache resultou numa falha de acerto na. Se este atributo não for especificado, `null` é atribuído.                                                                                                                                                                                                           | Não       | `null`            |
| key              | Valor da chave a utilizar na pesquisa em cache.                                                                                                                                                                                                                                                                                                                       | Sim      | N/A               |
| nome da variável    | Nome da [variável de contexto](api-management-policy-expressions.md#ContextVariables) o valor looked cópia de segurança será atribuído ao, se a pesquisa é efetuada com êxito. Se a pesquisa resulta num erro, a variável será atribuída o valor do `default-value` atributo ou `null`, se o `default-value` atributo for omitido.                                       | Sim      | N/A               |

### <a name="usage"></a>Utilização
Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **As secções de política:** entrada, saída, back-end, no caso de erro
- **Âmbitos de política:** global, API, a operação, a produtos

## <a name="StoreToCacheByKey"></a> Valor de Store na cache
O `cache-store-value` executa o armazenamento de cache por chave. A chave pode ter um valor de cadeia de caracteres arbitrária e é, geralmente, fornecida com uma expressão de política.

> [!NOTE]
> Esta política tem de ter um correspondente [obter o valor da cache](#GetFromCacheByKey) política.

### <a name="policy-statement"></a>Declaração de política

```xml
<cache-store-value key="cache key value" value="value to cache" duration="seconds" caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Exemplo
Para obter mais informações e exemplos desta política, consulte [personalizadas de colocação em cache na gestão de API do Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />

```

### <a name="elements"></a>Elementos

|Name|Descrição|Necessário|
|----------|-----------------|--------------|
|cache-store-value|Elemento raiz.|Sim|

### <a name="attributes"></a>Atributos

| Name             | Descrição                                                                                                                                                                                                                                                                                                                                                 | Necessário | Predefinição           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| caching-type | Escolha entre os seguintes valores do atributo:<br />- `internal` utilizar a cache de gestão de API interna,<br />- `external` Para utilizar a cache de externa, conforme descrito em [utilizar uma Cache do Azure externo para Redis na gestão de API do Azure](api-management-howto-cache-external.md),<br />- `prefer-external` Para utilizar a cache externo se configurado ou cache interna; caso contrário. | Não       | `prefer-external` |
| Duração         | Valor serão colocadas em cache para o valor de duração fornecido, especificado em segundos.                                                                                                                                                                                                                                                                                 | Sim      | N/A               |
| key              | O valor da chave de cache será armazenada em.                                                                                                                                                                                                                                                                                                                   | Sim      | N/A               |
| value            | O valor a ser colocados em cache.                                                                                                                                                                                                                                                                                                                                     | Sim      | N/A               |
### <a name="usage"></a>Utilização
Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **As secções de política:** entrada, saída, back-end, no caso de erro
- **Âmbitos de política:** global, API, a operação, a produtos

### <a name="RemoveCacheByKey"></a> Remover o valor da cache
O `cache-remove-value` elimina um item em cache identificado pela respetiva chave. A chave pode ter um valor de cadeia de caracteres arbitrária e é, geralmente, fornecida com uma expressão de política.

#### <a name="policy-statement"></a>Declaração de política

```xml

<cache-remove-value key="cache key value" caching-type="prefer-external | external | internal"  />

```

#### <a name="example"></a>Exemplo

```xml

<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>

```

#### <a name="elements"></a>Elementos

|Name|Descrição|Necessário|
|----------|-----------------|--------------|
|cache-remove-value|Elemento raiz.|Sim|

#### <a name="attributes"></a>Atributos

| Name             | Descrição                                                                                                                                                                                                                                                                                                                                                 | Necessário | Predefinição           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| caching-type | Escolha entre os seguintes valores do atributo:<br />- `internal` utilizar a cache de gestão de API interna,<br />- `external` Para utilizar a cache de externa, conforme descrito em [utilizar uma Cache do Azure externo para Redis na gestão de API do Azure](api-management-howto-cache-external.md),<br />- `prefer-external` Para utilizar a cache externo se configurado ou cache interna; caso contrário. | Não       | `prefer-external` |
| key              | A chave do valor armazenado em cache a ser removido do cache.                                                                                                                                                                                                                                                                                        | Sim      | N/A               |

#### <a name="usage"></a>Utilização
Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .

- **As secções de política:** entrada, saída, back-end, no caso de erro
- **Âmbitos de política:** global, API, a operação, a produtos

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações para trabalhar com políticas, consulte:

+ [Políticas de gestão de API](api-management-howto-policies.md)
+ [Transforme as APIs](transform-api.md)
+ [Referência de política](api-management-policy-reference.md) para uma lista completa das declarações de política e suas configurações
+ [Exemplos de política](policy-samples.md)
