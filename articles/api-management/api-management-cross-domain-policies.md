---
title: Gestão de API do Azure políticas entre domínios | Documentos da Microsoft
description: Saiba mais sobre as políticas entre domínios disponíveis para utilização na gestão de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 7689d277-8abe-472a-a78c-e6d4bd43455d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: ecbc1af97ce5ed158138f2bcf47f5729842c0fe9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60657497"
---
# <a name="api-management-cross-domain-policies"></a>Políticas entre domínios da API Management
Este tópico fornece uma referência para as seguintes políticas de gestão de API. Para informações sobre como adicionar e configurar as políticas, consulte [políticas de gestão de API](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="CrossDomainPolicies"></a> Políticas entre domínios

- [Permitir chamadas entre domínios](api-management-cross-domain-policies.md#AllowCrossDomainCalls) -torna a API acessível a partir de clientes baseada no browser e Adobe Flash e o Microsoft Silverlight.
- [CORS](api-management-cross-domain-policies.md#CORS) -adiciona recursos de várias origens (CORS) suporte a uma operação ou uma API para permitir chamadas entre domínios de clientes baseados em navegador de partilha.
- [JSONP](api-management-cross-domain-policies.md#JSONP) -adiciona JSON com o suporte de preenchimento (JSONP) para uma operação ou uma API para permitir chamadas entre domínios de clientes baseada no browser de JavaScript.

## <a name="AllowCrossDomainCalls"></a> Permitir chamadas entre domínios
Utilize o `cross-domain` política para tornar a API acessível a partir de clientes baseada no browser e Adobe Flash e o Microsoft Silverlight.

### <a name="policy-statement"></a>Declaração de política

```xml
<cross-domain>
    <!-Policy configuration is in the Adobe cross-domain policy file format,
        see https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->
</cross-domain>
```

### <a name="example"></a>Exemplo

```xml
<cross-domain>
    <cross-domain-policy>
        <allow-http-request-headers-from domain='*' headers='*' />
    </cross-domain-policy>
</cross-domain>
```

### <a name="elements"></a>Elementos

|Name|Descrição|Necessário|
|----------|-----------------|--------------|
|cross-domain|Elemento raiz. Elementos subordinados têm de cumprir os [especificação do ficheiro de política entre domínios de Adobe](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Sim|

### <a name="usage"></a>Utilização
Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **As secções de política:** entrada
- **Âmbitos de política:** global

## <a name="CORS"></a> CORS
O `cors` política adiciona recursos de várias origens (CORS) suporte a uma operação ou uma API para permitir chamadas entre domínios de clientes baseados em navegador de partilha.

CORS permite que um browser e um servidor para interagir e determinar se deve ou não permitir pedidos transversais à específicos (ou seja, XMLHttpRequests as chamadas feitas a partir do JavaScript numa página da web para outros domínios). Isso permite mais flexibilidade do que apenas permitir que os pedidos de mesma origem, mas é mais seguro que permitir que todos os pedidos de várias origens.

### <a name="policy-statement"></a>Declaração de política

```xml
<cors allow-credentials="false|true">
    <allowed-origins>
        <origin>origin uri</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="number of seconds">
        <method>http verb</method>
    </allowed-methods>
    <allowed-headers>
        <header>header name</header>
    </allowed-headers>
    <expose-headers>
        <header>header name</header>
    </expose-headers>
</cors>
```

### <a name="example"></a>Exemplo
Este exemplo demonstra como suportar pedidos de simulação, tais como aqueles com cabeçalhos personalizados ou métodos que não seja GET e POST. Para suportar os cabeçalhos personalizados e ações HTTP adicionais, utilize o `allowed-methods` e `allowed-headers` secções conforme mostrado no exemplo a seguir.

```xml
<cors allow-credentials="true">
    <allowed-origins>
        <!-- Localhost useful for development -->
        <origin>http://localhost:8080/</origin>
        <origin>http://example.com/</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="300">
        <method>GET</method>
        <method>POST</method>
        <method>PATCH</method>
        <method>DELETE</method>
    </allowed-methods>
    <allowed-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
        <header>x-zumo-version</header>
        <header>x-zumo-auth</header>
        <header>content-type</header>
        <header>accept</header>
    </allowed-headers>
    <expose-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
    </expose-headers>
</cors>
```

### <a name="elements"></a>Elementos

|Name|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|cors|Elemento raiz.|Sim|N/A|
|origens permitidas|Contém `origin` elementos que descrevem as origens permitidas para pedidos entre domínios. `allowed-origins` pode conter um único `origin` elemento que especifica `*` para permitir que qualquer origem, ou um ou mais `origin` elementos que contêm um URI.|Sim|N/A|
|origem|O valor pode ser uma `*` para permitir que todas as origens ou um URI que especifica uma única origem. O URI tem de incluir um esquema, o anfitrião e a porta.|Sim|Se a porta for omitida num URI, é utilizada porta 80 para HTTP e a porta 443 é utilizada para HTTPS.|
|métodos permitidos|Este elemento é necessário se métodos diferentes de introdução ou POST são permitidos. Contém `method` elementos que especifique os verbos HTTP suportados.|Não|Se não estiver presente nesta secção, são suportados GET e POST.|
|método|Especifica um verbo HTTP.|Pelo menos um `method` elemento é necessário se o `allowed-methods` secção está presente.|N/A|
|allowed-headers|Esse elemento contém `header` elementos especificando os nomes dos cabeçalhos que podem ser incluídos no pedido.|Não|N/A|
|expose-headers|Esse elemento contém `header` elementos especificando os nomes dos cabeçalhos que estarão acessíveis pelo cliente.|Não|N/A|
|cabeçalho|Especifica um nome de cabeçalho.|Pelo menos um `header` elemento é obrigatório `allowed-headers` ou `expose-headers` se a seção estiver presente.|N/A|

### <a name="attributes"></a>Atributos

|Name|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|allow-credentials|O `Access-Control-Allow-Credentials` cabeçalho na resposta de simulação será definido como o valor deste atributo e afetar a capacidade do cliente enviar credenciais nos pedidos entre domínios.|Não|false|
|preflight-result-max-age|O `Access-Control-Max-Age` cabeçalho na resposta de simulação será definido como o valor deste atributo e afetar a capacidade do agente de utilizador de resposta de simulação do cache.|Não|0|

### <a name="usage"></a>Utilização
Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **As secções de política:** entrada
- **Âmbitos de política:** global, API, operação

## <a name="JSONP"></a> JSONP
O `jsonp` política adiciona JSON com o suporte de preenchimento (JSONP) para uma operação ou uma API para permitir chamadas entre domínios de clientes baseada no browser de JavaScript. JSONP é um método usado em programas de JavaScript para dados de pedidos de um servidor num domínio diferente. JSONP ignora a limitação imposta pela maioria dos navegadores da web em que o acesso às páginas da web tem de estar no mesmo domínio.

### <a name="policy-statement"></a>Declaração de política

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Exemplo

```xml
<jsonp callback-parameter-name="cb" />
```

Se chamar o método sem o parâmetro de retorno de chamada? cb = XXX irá devolver JSON simples (sem um wrapper de chamada de função).

Se adicionar o parâmetro de retorno de chamada `?cb=XXX` irá devolver um resultado JSONP, o JSON original de encapsulamento de aplicações, como resultados relativos a função de retorno de chamada `XYZ('<json result goes here>');`

### <a name="elements"></a>Elementos

|Name|Descrição|Necessário|
|----------|-----------------|--------------|
|jsonp|Elemento raiz.|Sim|

### <a name="attributes"></a>Atributos

|Name|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|callback-parameter-name|A chamada de função de JavaScript entre domínios prefixada com o nome de domínio completamente qualificado, onde reside a função.|Sim|N/A|

### <a name="usage"></a>Utilização
Esta política pode ser utilizada na política de seguinte [secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **As secções de política:** saída
- **Âmbitos de política:** global, produto, API, operação

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações para trabalhar com políticas, consulte:

+ [Políticas de gestão de API](api-management-howto-policies.md)
+ [Transforme as APIs](transform-api.md)
+ [Referência de política](api-management-policy-reference.md) para uma lista completa das declarações de política e suas configurações
+ [Exemplos de política](policy-samples.md)   
