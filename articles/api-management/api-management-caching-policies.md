---
title: Políticas de cache do gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre as políticas de cache disponíveis para uso no gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 8147199c-24d8-439f-b2a9-da28a70a890c
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: 06c4ede12f939e48973d3e0b502d90b848d199bb
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072615"
---
# <a name="api-management-caching-policies"></a>Políticas de cache de gerenciamento de API
Este tópico fornece uma referência para as seguintes políticas de gerenciamento de API. Para obter informações sobre como adicionar e configurar políticas, consulte [políticas no gerenciamento de API](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="CachingPolicies"></a>Políticas de cache

- Políticas de cache de resposta
    - [Obter do cache](api-management-caching-policies.md#GetFromCache) – executar pesquisa de cache e retornar respostas válidas em cache quando disponíveis.
    - [Armazenar](api-management-caching-policies.md#StoreToCache) em cache – armazena em cache as respostas de acordo com a configuração de controle de cache especificada.
- Políticas de cache de valor
    - [Obter valor do cache](#GetFromCacheByKey) – recuperar um item armazenado em cache por chave.
    - [Armazenar valor no cache](#StoreToCacheByKey) – armazene um item no cache por chave.
    - [Remover valor do cache](#RemoveCacheByKey) -remove um item do cache por chave.

## <a name="GetFromCache"></a>Obter do cache
Use a `cache-lookup` política para executar a pesquisa de cache e retornar uma resposta válida em cache quando disponível. Essa política pode ser aplicada em casos em que o conteúdo da resposta permaneça estático ao longo de um período de tempo. O cache de resposta reduz a largura de banda e os requisitos de processamento impostos no servidor Web de back-end e reduz a latência percebida pelos consumidores de API.

> [!NOTE]
> Essa política deve ter uma política de [repositório correspondente para armazenar em cache](api-management-caching-policies.md#StoreToCache) .

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

#### <a name="example-using-policy-expressions"></a>Exemplo usando expressões de política
Este exemplo mostra como configurar a duração do cache de resposta do gerenciamento de API que corresponde ao cache de resposta do serviço de back-end conforme `Cache-Control` especificado pela diretiva do serviço de backup. Para ver uma demonstração de como configurar e usar essa política [, confira o episódio 177 do Cloud Cover: Mais recursos de gerenciamento de API com](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) Vlad Vinogradsky e avança para 25:25.

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

Para obter mais informações, consulte [expressões de política](api-management-policy-expressions.md) e variável de [contexto](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elementos

|Name|Descrição|Requerido|
|----------|-----------------|--------------|
|pesquisa de cache|Elemento raiz.|Sim|
|variar por cabeçalho|Comece a armazenar em cache as respostas por valor do cabeçalho especificado, como aceitar, Accept-charset, Accept-Encoding, Accept-Language, Authorization, espera, from, host, If-Match.|Não|
|vary-by-query-parameter|Iniciar cache de respostas por valor de parâmetros de consulta especificados. Insira um único parâmetro ou vários. Use o ponto e vírgula como um separador. Se nenhum for especificado, todos os parâmetros de consulta serão usados.|Não|

### <a name="attributes"></a>Atributos

| Name                           | Descrição                                                                                                                                                                                                                                                                                                                                                 | Requerido | Predefinição           |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| allow-private-response-caching | Quando definido como `true`, permite o cache de solicitações que contêm um cabeçalho de autorização.                                                                                                                                                                                                                                                                        | Não       | false             |
| tipo de cache               | Escolha entre os seguintes valores do atributo:<br />- `internal`para usar o cache interno de gerenciamento de API,<br />- `external`para usar o cache externo conforme descrito em [usar um cache externo do Azure para Redis no gerenciamento de API do Azure](api-management-howto-cache-external.md),<br />- `prefer-external`para usar o cache externo, se for o cache interno ou configurado. | Não       | `prefer-external` |
| downstream-caching-type        | Esse atributo deve ser definido como um dos valores a seguir.<br /><br /> -nenhum-cache downstream não é permitido.<br />-Private – o cache privado de downstream é permitido.<br />-público-o cache downstream privado e compartilhado é permitido.                                                                                                          | Não       | nenhum              |
| deve ser revalidado                | Quando o cache downstream está habilitado, esse atributo ativa ou desativa `must-revalidate` a diretiva de controle de cache nas respostas do gateway.                                                                                                                                                                                                                      | Não       | true              |
| variação por desenvolvedor              | Defina como `true` para armazenar em cache as respostas por [chave de assinatura](https://docs.microsoft.com/azure/api-management/api-management-subscriptions).                                                                                                                                                                                                                                                                                                         | Sim      |         False          |
| grupos de variação por desenvolvedor       | Defina como `true` para armazenar em cache as respostas por [grupo de usuários](https://docs.microsoft.com/azure/api-management/api-management-howto-create-groups).                                                                                                                                                                                                                                                                                                             | Sim      |       False            |

### <a name="usage"></a>Utilização
Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos escopos de política a seguir. [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)

- **Seções de política:** entrada
- Escopos de **política:** todos os escopos

## <a name="StoreToCache"></a>Armazenar em cache
A `cache-store` política armazena em cache as respostas de acordo com as configurações de cache especificadas. Essa política pode ser aplicada em casos em que o conteúdo da resposta permaneça estático ao longo de um período de tempo. O cache de resposta reduz a largura de banda e os requisitos de processamento impostos no servidor Web de back-end e reduz a latência percebida pelos consumidores de API.

> [!NOTE]
> Essa política deve ter uma política [obter do cache](api-management-caching-policies.md#GetFromCache) correspondente.

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

#### <a name="example-using-policy-expressions"></a>Exemplo usando expressões de política
Este exemplo mostra como configurar a duração do cache de resposta do gerenciamento de API que corresponde ao cache de resposta do serviço de back-end conforme `Cache-Control` especificado pela diretiva do serviço de backup. Para ver uma demonstração de como configurar e usar essa política [, confira o episódio 177 do Cloud Cover: Mais recursos de gerenciamento de API com](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) Vlad Vinogradsky e avança para 25:25.

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

Para obter mais informações, consulte [expressões de política](api-management-policy-expressions.md) e variável de [contexto](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elementos

|Name|Descrição|Requerido|
|----------|-----------------|--------------|
|armazenamento em cache|Elemento raiz.|Sim|

### <a name="attributes"></a>Atributos

| Name             | Descrição                                                                                                                                                                                                                                                                                                                                                 | Requerido | Predefinição           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| duration         | Vida útil das entradas armazenadas em cache, especificadas em segundos.                                                                                                                                                                                                                                                                                                   | Sim      | N/A               |

### <a name="usage"></a>Utilização
Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos escopos de política a seguir. [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)

- **Seções de política:** saída
- Escopos de **política:** todos os escopos

## <a name="GetFromCacheByKey"></a>Obter valor do cache
Use a `cache-lookup-value` política para executar a pesquisa de cache por chave e retornar um valor armazenado em cache. A chave pode ter um valor de cadeia de caracteres arbitrário e normalmente é fornecida usando uma expressão de política.

> [!NOTE]
> Essa política deve ter um [valor de repositório correspondente na política de cache](#StoreToCacheByKey) .

### <a name="policy-statement"></a>Declaração de política

```xml
<cache-lookup-value key="cache key value"
    default-value="value to use if cache lookup resulted in a miss"
    variable-name="name of a variable looked up value is assigned to"
    caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Exemplo
Para obter mais informações e exemplos dessa política, consulte [cache personalizado no gerenciamento de API do Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).

```xml
<cache-lookup-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    variable-name="userprofile" />

```

### <a name="elements"></a>Elementos

|Name|Descrição|Requerido|
|----------|-----------------|--------------|
|cache-pesquisa-valor|Elemento raiz.|Sim|

### <a name="attributes"></a>Atributos

| Name             | Descrição                                                                                                                                                                                                                                                                                                                                                 | Requerido | Predefinição           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| tipo de cache | Escolha entre os seguintes valores do atributo:<br />- `internal`para usar o cache interno de gerenciamento de API,<br />- `external`para usar o cache externo conforme descrito em [usar um cache externo do Azure para Redis no gerenciamento de API do Azure](api-management-howto-cache-external.md),<br />- `prefer-external`para usar o cache externo, se for o cache interno ou configurado. | Não       | `prefer-external` |
| valor padrão    | Um valor que será atribuído à variável se a pesquisa de chave de cache resultar em um erro. Se esse atributo não for especificado, `null` será atribuído.                                                                                                                                                                                                           | Não       | `null`            |
| key              | Valor de chave de cache a ser usado na pesquisa.                                                                                                                                                                                                                                                                                                                       | Sim      | N/A               |
| nome da variável    | Nome da [variável de contexto](api-management-policy-expressions.md#ContextVariables) à qual o valor pesquisado será atribuído, se a pesquisa for bem-sucedida. Se a pesquisa resultar em um erro, a variável será atribuída ao valor do `default-value` atributo ou `null`, se o `default-value` atributo for omitido.                                       | Sim      | N/A               |

### <a name="usage"></a>Utilização
Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos escopos de política a seguir. [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)

- **Seções de política:** entrada, saída, back-end, em caso de erro
- Escopos de **política:** todos os escopos

## <a name="StoreToCacheByKey"></a>Armazenar valor no cache
O `cache-store-value` executa o armazenamento em cache por chave. A chave pode ter um valor de cadeia de caracteres arbitrário e normalmente é fornecida usando uma expressão de política.

> [!NOTE]
> Essa política deve ter um [valor get correspondente da política de cache](#GetFromCacheByKey) .

### <a name="policy-statement"></a>Declaração de política

```xml
<cache-store-value key="cache key value" value="value to cache" duration="seconds" caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Exemplo
Para obter mais informações e exemplos dessa política, consulte [cache personalizado no gerenciamento de API do Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />

```

### <a name="elements"></a>Elementos

|Name|Descrição|Requerido|
|----------|-----------------|--------------|
|cache-Store-Value|Elemento raiz.|Sim|

### <a name="attributes"></a>Atributos

| Name             | Descrição                                                                                                                                                                                                                                                                                                                                                 | Requerido | Predefinição           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| tipo de cache | Escolha entre os seguintes valores do atributo:<br />- `internal`para usar o cache interno de gerenciamento de API,<br />- `external`para usar o cache externo conforme descrito em [usar um cache externo do Azure para Redis no gerenciamento de API do Azure](api-management-howto-cache-external.md),<br />- `prefer-external`para usar o cache externo, se for o cache interno ou configurado. | Não       | `prefer-external` |
| duration         | O valor será armazenado em cache para o valor Duration fornecido, especificado em segundos.                                                                                                                                                                                                                                                                                 | Sim      | N/A               |
| key              | Chave de cache em que o valor será armazenado.                                                                                                                                                                                                                                                                                                                   | Sim      | N/A               |
| value            | O valor a ser armazenado em cache.                                                                                                                                                                                                                                                                                                                                     | Sim      | N/A               |
### <a name="usage"></a>Utilização
Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos escopos de política a seguir. [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)

- **Seções de política:** entrada, saída, back-end, em caso de erro
- Escopos de **política:** todos os escopos

### <a name="RemoveCacheByKey"></a>Remover valor do cache
O `cache-remove-value` exclui um item armazenado em cache identificado por sua chave. A chave pode ter um valor de cadeia de caracteres arbitrário e normalmente é fornecida usando uma expressão de política.

#### <a name="policy-statement"></a>Declaração de política

```xml

<cache-remove-value key="cache key value" caching-type="prefer-external | external | internal"  />

```

#### <a name="example"></a>Exemplo

```xml

<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>

```

#### <a name="elements"></a>Elementos

|Name|Descrição|Requerido|
|----------|-----------------|--------------|
|cache-remove-value|Elemento raiz.|Sim|

#### <a name="attributes"></a>Atributos

| Name             | Descrição                                                                                                                                                                                                                                                                                                                                                 | Requerido | Predefinição           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| tipo de cache | Escolha entre os seguintes valores do atributo:<br />- `internal`para usar o cache interno de gerenciamento de API,<br />- `external`para usar o cache externo conforme descrito em [usar um cache externo do Azure para Redis no gerenciamento de API do Azure](api-management-howto-cache-external.md),<br />- `prefer-external`para usar o cache externo, se for o cache interno ou configurado. | Não       | `prefer-external` |
| key              | A chave do valor anteriormente armazenado em cache a ser removido do cache.                                                                                                                                                                                                                                                                                        | Sim      | N/A               |

#### <a name="usage"></a>Utilização
Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos escopos de política a seguir. [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)

- **Seções de política:** entrada, saída, back-end, em caso de erro
- Escopos de **política:** todos os escopos

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como trabalhar com políticas, consulte:

+ [Políticas no gerenciamento de API](api-management-howto-policies.md)
+ [APIs de transformação](transform-api.md)
+ [Referência de política](api-management-policy-reference.md) para uma lista completa de instruções de política e suas configurações
+ [Exemplos de política](policy-samples.md)
