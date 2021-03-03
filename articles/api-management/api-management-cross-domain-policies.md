---
title: Azure API Management cruza políticas de domínio | Microsoft Docs
description: Conheça as políticas de domínio transversal disponíveis para utilização na Azure API Management.
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
ms.date: 03/01/2021
ms.author: apimpm
ms.openlocfilehash: 85abf30d792b24b92685e191f5b460a42dc29142
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101688421"
---
# <a name="api-management-cross-domain-policies"></a>Políticas entre domínios da API Management
Este tópico fornece uma referência para as seguintes políticas de Gestão da API. Para obter informações sobre políticas de adição e configuração, consulte [Políticas em Gestão de API.](./api-management-policies.md)

## <a name="cross-domain-policies"></a><a name="CrossDomainPolicies"></a> Políticas de domínio transversal

- [Permitir chamadas de domínio transversal](api-management-cross-domain-policies.md#AllowCrossDomainCalls) - Torna a API acessível a partir de clientes baseados no navegador Adobe Flash e Microsoft Silverlight.
- [CORS](api-management-cross-domain-policies.md#CORS) - Adiciona suporte de partilha de recursos de origem cruzada (CORS) a uma operação ou a uma API para permitir chamadas de domínio transversal de clientes baseados no navegador.
- [JSONP](api-management-cross-domain-policies.md#JSONP) - Adiciona JSON com suporte de enchimento (JSONP) a uma operação ou a uma API para permitir chamadas de domínio transversal de clientes baseados no navegador JavaScript.

## <a name="allow-cross-domain-calls"></a><a name="AllowCrossDomainCalls"></a> Permitir chamadas de domínio transversal
Utilize a `cross-domain` política para tornar a API acessível a clientes baseados no navegador Adobe Flash e Microsoft Silverlight.

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
        <allow-http-request-headers-from domain='*' headers='*' />
</cross-domain>
```

### <a name="elements"></a>Elementos

|Nome|Descrição|Obrigatório|
|----------|-----------------|--------------|
|domínio transversal|Elemento de raiz. Os elementos infantis devem estar em conformidade com a [especificação de ficheiro de política de domínio cruzado da Adobe](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Sim|

### <a name="usage"></a>Utilização
Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

- **Secções políticas:** entrada
- **Âmbitos de política:** todos os âmbitos

## <a name="cors"></a><a name="CORS"></a> CORS
A `cors` política adiciona suporte de partilha de recursos de origem cruzada (CORS) a uma operação ou a uma API para permitir chamadas de domínio transversal de clientes baseados no navegador. 

> [!NOTE]
> Se o pedido corresponder a uma operação com um método OPTIONS definido na API, a lógica de processamento de pedidos de pré-voo associada às políticas CORS não será executada. Por conseguinte, tais operações podem ser utilizadas para implementar uma lógica de processamento personalizado antes do voo.

O CORS permite que um navegador e um servidor interajam e determinem se permitem ou não pedidos específicos de origem cruzada (ou seja, chamadas XMLHttpRequests feitas a partir de JavaScript numa página web para outros domínios). Isto permite uma maior flexibilidade do que apenas permitir pedidos de origem do mesmo, mas é mais seguro do que permitir todos os pedidos de origem cruzada.

É necessário aplicar a política CORS para ativar a consola interativa no portal do desenvolvedor. Consulte a [documentação](./api-management-howto-developer-portal.md#cors) do portal do desenvolvedor para obter mais detalhes.

### <a name="policy-statement"></a>Declaração política

```xml
<cors allow-credentials="false|true" terminate-unmatched-request="true|false">
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
Este exemplo demonstra como apoiar pedidos de pré-voo, tais como aqueles com cabeçalhos personalizados ou métodos que não o GET e o POST. Para suportar cabeçalhos personalizados e verbos HTTP adicionais, utilize as `allowed-methods` secções e `allowed-headers` secções como mostrado no exemplo seguinte.

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

|Nome|Descrição|Obrigatório|Predefinição|
|----------|-----------------|--------------|-------------|
|cors|Elemento de raiz.|Sim|N/D|
|de origem permitida|Contém `origin` elementos que descrevem as origens permitidas para pedidos de domínio cruzado. `allowed-origins` pode conter um único `origin` elemento que especifica para permitir qualquer `*` origem, ou um ou mais `origin` elementos que contenham um URI.|Sim|N/D|
|origem|O valor pode ser `*` para permitir todas as origens, ou um URI que especifica uma única origem. O URI deve incluir um esquema, hospedeiro e porto.|Sim|Se a porta for omitida num URI, a porta 80 é utilizada para HTTP e a porta 443 é utilizada para HTTPS.|
|métodos permitidos|Este elemento é necessário se forem permitidos métodos diferentes do GET ou DOM. Contém `method` elementos que especificam os verbos HTTP suportados. O valor `*` indica todos os métodos.|Não|Se esta secção não estiver presente, GET e POST são suportados.|
|método|Especifica um verbo HTTP.|É necessário pelo menos um `method` elemento se a secção estiver `allowed-methods` presente.|N/D|
|cabeçalhos permitidos|Este elemento contém `header` elementos que especificam nomes dos cabeçalhos que podem ser incluídos no pedido.|Não|N/D|
|cabeçalhos exposindo|Este elemento contém `header` elementos que especificam nomes dos cabeçalhos que serão acessíveis pelo cliente.|Não|N/D|
|cabeçalho|Especifica um nome de cabeçalho.|Pelo menos um `header` elemento é necessário dentro ou se a secção está `allowed-headers` `expose-headers` presente.|N/D|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Obrigatório|Predefinição|
|----------|-----------------|--------------|-------------|
|permitir credenciais|O `Access-Control-Allow-Credentials` cabeçalho na resposta de pré-voo será definido para o valor deste atributo e afetará a capacidade do cliente de apresentar credenciais em pedidos de domínio cruzado.|Não|false|
|pedido de fim-de-jogo|Este atributo controla o processamento de pedidos de origem cruzada que não correspondem às definições da política do CORS. Quando o pedido options é processado como um pedido pré-voo e não corresponde às definições da política cors: Se o atributo for definido `true` para, imediatamente, rescindir o pedido com uma resposta vazia de 200 OK; Se o atributo estiver definido para , verifique a `false` entrada para outras políticas cors de âmbito que são crianças diretas do elemento de entrada e aplique-as.  Se não forem encontradas políticas CORS, encerre o pedido com uma resposta vazia de 200 OK. Quando o pedido GET ou HEAD inclui o cabeçalho Origin (e, portanto, é processado como um pedido de origem cruzada) e não corresponde às definições de política do CORS: Se o atributo estiver definido `true` para, imediatamente, rescindir o pedido com uma resposta vazia de 200 OK; Se o atributo estiver definido para `false` , permita que o pedido proceda normalmente e não adicione cabeçalhos CORS à resposta.|Não|true|
|pré-voo-resultado-má-idade|O `Access-Control-Max-Age` cabeçalho na resposta de pré-voo será definido para o valor deste atributo e afetará a capacidade do agente utilizador de cache resposta antes do voo.|Não|0|

### <a name="usage"></a>Utilização
Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

- **Secções políticas:** entrada
- **Âmbitos de política:** todos os âmbitos

## <a name="jsonp"></a><a name="JSONP"></a> JSONP
A `jsonp` política adiciona o suporte JSON com estofamento (JSONP) a uma operação ou a uma API para permitir chamadas de domínio transversal de clientes baseados no navegador JavaScript. JSONP é um método utilizado nos programas JavaScript para solicitar dados de um servidor num domínio diferente. JSONP ignora a limitação imposta pela maioria dos navegadores web onde o acesso a páginas web deve estar no mesmo domínio.

### <a name="policy-statement"></a>Declaração política

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Exemplo

```xml
<jsonp callback-parameter-name="cb" />
```

Se ligar para o método sem o parâmetro de retorno ?cb=XXX, retornará o JSON simples (sem um invólucro de chamada de função).

Se adicionar o parâmetro de `?cb=XXX` retorno, retornará um resultado JSONP, envolvendo os resultados originais do JSON em torno da função de retorno como `XYZ('<json result goes here>');`

### <a name="elements"></a>Elementos

|Nome|Descrição|Obrigatório|
|----------|-----------------|--------------|
|jsonp|Elemento de raiz.|Sim|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Obrigatório|Predefinição|
|----------|-----------------|--------------|-------------|
|callback-parâmetro-nome|A chamada de função JavaScript de domínio cruzado pré-fixado com o nome de domínio totalmente qualificado onde reside a função.|Sim|N/D|

### <a name="usage"></a>Utilização
Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

- **Secções políticas:** saída
- **Âmbitos de política:** todos os âmbitos

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as políticas, consulte:

+ [Políticas em Gestão de API](api-management-howto-policies.md)
+ [Transformar APIs](transform-api.md)
+ [Referência política](./api-management-policies.md) para uma lista completa de declarações políticas e suas definições
+ [Exemplos de Políticas](./policy-reference.md)
