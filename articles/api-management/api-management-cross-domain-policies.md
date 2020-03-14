---
title: Políticas de domínio transversal da Azure API Management  Microsoft Docs
description: Conheça as políticas de domínio transversal disponíveis para utilização na Gestão aPI Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 7689d277-8abe-472a-a78c-e6d4bd43455d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: b72abf4e208c57987375a105865046f194460058
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79265990"
---
# <a name="api-management-cross-domain-policies"></a>Políticas entre domínios da API Management
Este tópico fornece uma referência para as seguintes políticas de Gestão da API. Para obter informações sobre a adição e configuração de políticas, consulte [Políticas na Gestão da API](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="CrossDomainPolicies"></a>Políticas de domínio transversal

- [Permitir chamadas cruzadas](api-management-cross-domain-policies.md#AllowCrossDomainCalls) - Torna a API acessível a partir de clientes baseados no navegador Adobe Flash e Microsoft Silverlight.
- [CORS](api-management-cross-domain-policies.md#CORS) - Adiciona suporte de partilha de recursos de origem cruzada (CORS) a uma operação ou a uma API para permitir chamadas de domínio cruzado de clientes baseados no navegador.
- [JSONP](api-management-cross-domain-policies.md#JSONP) - Adiciona jSON com suporte de estofamento (JSONP) a uma operação ou a uma API para permitir chamadas cruzadas de clientes baseados no navegador JavaScript.

## <a name="AllowCrossDomainCalls"></a>Permitir chamadas de domínio transversal
Utilize a política `cross-domain` para tornar a API acessível a partir de clientes baseados no navegador Adobe Flash e Microsoft Silverlight.

### <a name="policy-statement"></a>Declaração política

```xml
<cross-domain>
    <!-Policy configuration is in the Adobe cross-domain policy file format,
        see https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->
</cross-domain>
```

### <a name="example"></a>Exemplo

```xml
<cross-domain>
    <cross-domain>
        <allow-http-request-headers-from domain='*' headers='*' />
    </cross-domain>
</cross-domain>
```

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|domínio transversal|Elemento de raiz. Os elementos infantis devem estar em conformidade com a [especificação de ficheiros de domínio transversal adobe](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Sim|

### <a name="usage"></a>Utilização
Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

- **Secções políticas:** entrada
- **Âmbitos de política:** todos os âmbitos

## <a name="CORS"></a>CORS
A política `cors` adiciona suporte de partilha de recursos de origem cruzada (CORS) a uma operação ou a uma API para permitir chamadas de domínio cruzado de clientes baseados no navegador.

O CORS permite que um navegador e um servidor interajam e determinem se permitem ou não pedidos específicos de origem cruzada (isto é, XMLHttpRequests chamadas feitas a partir de JavaScript numa página web para outros domínios). Isto permite mais flexibilidade do que apenas permitir pedidos de origem mesma, mas é mais seguro do que permitir todos os pedidos de origem cruzada.

### <a name="policy-statement"></a>Declaração política

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
Este exemplo demonstra como apoiar pedidos de pré-voo, como aqueles com cabeçalhos ou métodos personalizados que não o GET e O POST. Para suportar cabeçalhos personalizados e verbos HTTP adicionais, utilize as secções `allowed-methods` e `allowed-headers`, como mostra o seguinte exemplo.

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

|Nome|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|cors|Elemento de raiz.|Sim|N/D|
|origens permitidas|Contém elementos `origin` que descrevem as origens permitidas para pedidos de domínio cruzado. `allowed-origins` pode conter um único elemento `origin` que especifica `*` permitir qualquer origem, ou um ou mais elementos `origin` que contenham um URI.|Sim|N/D|
|origem|O valor pode ser `*` permitir todas as origens, ou um URI que especifica uma única origem. O URI deve incluir um esquema, hospedeiro e porto.|Sim|Se a porta for omitida num URI, a porta 80 é utilizada para HTTP e a porta 443 é utilizada para HTTPS.|
|métodos permitidos|Este elemento é necessário se forem permitidos métodos que não o GET ou o POST. Contém `method` elementos que especificam os verbos HTTP suportados. O valor `*` indica todos os métodos.|Não|Se esta secção não estiver presente, o GET e o POST são suportados.|
|método|Especifica um verbo HTTP.|Pelo menos um elemento `method` é necessário se a secção `allowed-methods` estiver presente.|N/D|
|cabeçalhos permitidos|Este elemento contém elementos `header` especificando os nomes dos cabeçalhos que podem ser incluídos no pedido.|Não|N/D|
|expose-headers|Este elemento contém elementos `header` especificando nomes dos cabeçalhos que serão acessíveis pelo cliente.|Não|N/D|
|cabeçalho|Especifica um nome cabeçalho.|É necessário pelo menos um elemento `header` em `allowed-headers` ou `expose-headers` se a secção estiver presente.|N/D|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|allow-credentials|O cabeçalho `Access-Control-Allow-Credentials` na resposta pré-voo será definido para o valor deste atributo e afetará a capacidade do cliente de apresentar credenciais em pedidos de domínio cruzado.|Não|false|
|pré-voo-resultado-idade máxima|O cabeçalho `Access-Control-Max-Age` na resposta pré-voo será definido para o valor deste atributo e afetará a capacidade do agente utilizador de cache resposta pré-voo.|Não|0|

### <a name="usage"></a>Utilização
Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

- **Secções políticas:** entrada
- **Âmbitos de política:** todos os âmbitos

## <a name="JSONP"></a>JSONP
A política `jsonp` adiciona o Suporte jSON com estofamento (JSONP) a uma operação ou a uma API para permitir chamadas cruzadas de clientes baseados no navegador JavaScript. JSONP é um método usado em programas JavaScript para solicitar dados de um servidor num domínio diferente. JSONP ignora a limitação imposta pela maioria dos navegadores da Web onde o acesso às páginas web deve estar no mesmo domínio.

### <a name="policy-statement"></a>Declaração política

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Exemplo

```xml
<jsonp callback-parameter-name="cb" />
```

Se ligar para o método sem o parâmetro de chamada ?cb=XXX, devolverá o JSON simples (sem um invólucro de chamada de função).

Se adicionar o parâmetro de retorno de chamada `?cb=XXX` devolverá um resultado JSONP, envolvendo os resultados originais do JSON em torno da função de chamada como `XYZ('<json result goes here>');`

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|jsonp|Elemento de raiz.|Sim|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|callback-parâmetro-nome|A função JavaScript de domínio cruzado prefixada com o nome de domínio totalmente qualificado onde a função reside.|Sim|N/D|

### <a name="usage"></a>Utilização
Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

- **Secções políticas:** saída
- **Âmbitos de política:** todos os âmbitos

## <a name="next-steps"></a>Passos seguintes

Para mais informações que trabalhem com políticas, consulte:

+ [Políticas em Gestão aPi](api-management-howto-policies.md)
+ [Transforme APIs](transform-api.md)
+ [Referência política](api-management-policy-reference.md) para uma lista completa de declarações políticas e suas configurações
+ [Amostras políticas](policy-samples.md)
