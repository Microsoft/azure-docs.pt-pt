---
title: Políticas de domínio transversal da Azure API Management [ Microsoft Docs
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79265990"
---
# <a name="api-management-cross-domain-policies"></a>Políticas entre domínios da API Management
Este tópico fornece uma referência para as seguintes políticas de Gestão da API. Para obter informações sobre a adição e configuração de políticas, consulte [Políticas na Gestão da API](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="cross-domain-policies"></a><a name="CrossDomainPolicies"></a>Políticas de domínio transversal

- [Permitir chamadas cruzadas](api-management-cross-domain-policies.md#AllowCrossDomainCalls) - Torna a API acessível a partir de clientes baseados no navegador Adobe Flash e Microsoft Silverlight.
- [CORS](api-management-cross-domain-policies.md#CORS) - Adiciona suporte de partilha de recursos de origem cruzada (CORS) a uma operação ou a uma API para permitir chamadas de domínio cruzado de clientes baseados no navegador.
- [JSONP](api-management-cross-domain-policies.md#JSONP) - Adiciona jSON com suporte de estofamento (JSONP) a uma operação ou a uma API para permitir chamadas cruzadas de clientes baseados no navegador JavaScript.

## <a name="allow-cross-domain-calls"></a><a name="AllowCrossDomainCalls"></a>Permitir chamadas de domínio transversal
Utilize `cross-domain` a política para tornar a API acessível a partir de clientes baseados no navegador Adobe Flash e Microsoft Silverlight.

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

## <a name="cors"></a><a name="CORS"></a>CORS
A `cors` política adiciona suporte de partilha de recursos de origem cruzada (CORS) a uma operação ou a uma API para permitir chamadas de domínio cruzado de clientes baseados no navegador.

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
Este exemplo demonstra como apoiar pedidos de pré-voo, como aqueles com cabeçalhos ou métodos personalizados que não o GET e O POST. Para suportar cabeçalhos personalizados e verbos HTTP adicionais, utilize as `allowed-methods` secções e secções `allowed-headers` conforme mostrado no exemplo seguinte.

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
|origens permitidas|Contém `origin` elementos que descrevem as origens permitidas para pedidos de domínio cruzado. `allowed-origins`pode conter um `origin` único elemento `*` que especifica para permitir `origin` qualquer origem, ou um ou mais elementos que contenham um URI.|Sim|N/D|
|origem|O valor pode `*` ser permitir todas as origens, ou um URI que especifica uma única origem. O URI deve incluir um esquema, hospedeiro e porto.|Sim|Se a porta for omitida num URI, a porta 80 é utilizada para HTTP e a porta 443 é utilizada para HTTPS.|
|métodos permitidos|Este elemento é necessário se forem permitidos métodos que não o GET ou o POST. Contém `method` elementos que especificam os verbos HTTP suportados. O `*` valor indica todos os métodos.|Não|Se esta secção não estiver presente, o GET e o POST são suportados.|
|método|Especifica um verbo HTTP.|Pelo menos `method` um elemento é `allowed-methods` necessário se a secção estiver presente.|N/D|
|cabeçalhos permitidos|Este elemento `header` contém elementos que especificam os nomes dos cabeçalhos que podem ser incluídos no pedido.|Não|N/D|
|expor cabeçalhos|Este elemento `header` contém elementos que especificam os nomes dos cabeçalhos que serão acessíveis pelo cliente.|Não|N/D|
|cabeçalho|Especifica um nome cabeçalho.|Pelo menos `header` um elemento `allowed-headers` é `expose-headers` necessário dentro ou se a secção estiver presente.|N/D|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Necessário|Predefinição|
|----------|-----------------|--------------|-------------|
|permitir credenciais|O `Access-Control-Allow-Credentials` cabeçalho na resposta pré-voo será definido para o valor deste atributo e afetará a capacidade do cliente de apresentar credenciais em pedidos de domínio cruzado.|Não|false|
|pré-voo-resultado-idade máxima|O `Access-Control-Max-Age` cabeçalho na resposta pré-voo será definido para o valor deste atributo e afetará a capacidade do agente utilizador de cache resposta pré-voo.|Não|0|

### <a name="usage"></a>Utilização
Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

- **Secções políticas:** entrada
- **Âmbitos de política:** todos os âmbitos

## <a name="jsonp"></a><a name="JSONP"></a>JSONP
A `jsonp` política adiciona o Suporte JSON com estofamento (JSONP) a uma operação ou a uma API para permitir chamadas cruzadas de clientes baseados no navegador JavaScript. JSONP é um método usado em programas JavaScript para solicitar dados de um servidor num domínio diferente. JSONP ignora a limitação imposta pela maioria dos navegadores da Web onde o acesso às páginas web deve estar no mesmo domínio.

### <a name="policy-statement"></a>Declaração política

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Exemplo

```xml
<jsonp callback-parameter-name="cb" />
```

Se ligar para o método sem o parâmetro de chamada ?cb=XXX, devolverá o JSON simples (sem um invólucro de chamada de função).

Se adicionar o parâmetro `?cb=XXX` de retorno de chamada, devolverá um resultado JSONP, envolvendo os resultados originais do JSON em torno da função de chamada como`XYZ('<json result goes here>');`

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
