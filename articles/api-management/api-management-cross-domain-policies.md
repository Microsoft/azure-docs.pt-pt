---
title: Políticas entre domínios do gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre as políticas entre domínios disponíveis para uso no gerenciamento de API do Azure.
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
ms.openlocfilehash: 86c61679a73f03f7e54bba746107685796ec07c9
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442321"
---
# <a name="api-management-cross-domain-policies"></a>Políticas entre domínios da API Management
Este tópico fornece uma referência para as seguintes políticas de gerenciamento de API. Para obter informações sobre como adicionar e configurar políticas, consulte [políticas no gerenciamento de API](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="CrossDomainPolicies"></a>Políticas entre domínios

- [Permitir chamadas entre domínios](api-management-cross-domain-policies.md#AllowCrossDomainCalls) – torna a API acessível a partir de clientes do Adobe Flash e do Microsoft Silverlight baseados em navegador.
- [CORS](api-management-cross-domain-policies.md#CORS) -adiciona suporte a CORS (compartilhamento de recursos entre origens) a uma operação ou a uma API para permitir chamadas entre domínios de clientes baseados em navegador.
- [JSONP](api-management-cross-domain-policies.md#JSONP) -adiciona suporte a JSON com preenchimento (JSONP) a uma operação ou a uma API para permitir chamadas entre domínios de clientes baseados em navegador JavaScript.

## <a name="AllowCrossDomainCalls"></a>Permitir chamadas entre domínios
Use a `cross-domain` política para tornar a API acessível por meio de clientes do Adobe Flash e do Microsoft Silverlight baseados em navegador.

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
    <cross-domain>
        <allow-http-request-headers-from domain='*' headers='*' />
    </cross-domain>
</cross-domain>
```

### <a name="elements"></a>Elementos

|Nome|Descrição|Requerido|
|----------|-----------------|--------------|
|entre domínios|Elemento raiz. Os elementos filho devem estar em conformidade com a [especificação de arquivo de política entre domínios do Adobe](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Sim|

### <a name="usage"></a>Utilização
Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos escopos de política a seguir. [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)

- **Seções de política:** entrada
- Escopos de **política:** todos os escopos

## <a name="CORS"></a> CORS
A `cors` política adiciona suporte a CORS (compartilhamento de recursos entre origens) a uma operação ou a uma API para permitir chamadas entre domínios de clientes baseados em navegador.

O CORS permite que um navegador e um servidor interajam e determinem se devem ou não permitir solicitações entre origens específicas (ou seja, chamadas de XMLHttpRequest feitas do JavaScript em uma página da Web para outros domínios). Isso permite mais flexibilidade do que permitir apenas solicitações de mesma origem, mas é mais seguro do que permitir todas as solicitações entre origens.

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
Este exemplo demonstra como dar suporte a solicitações de simulação, como aquelas com cabeçalhos personalizados ou métodos diferentes de GET e POST. Para dar suporte a cabeçalhos personalizados e verbos HTTP adicionais, use `allowed-methods` as `allowed-headers` seções e, conforme mostrado no exemplo a seguir.

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

|Nome|Descrição|Requerido|Predefinição|
|----------|-----------------|--------------|-------------|
|CORS|Elemento raiz.|Sim|N/A|
|origens permitidas|Contém `origin` elementos que descrevem as origens permitidas para solicitações entre domínios. `allowed-origins`pode conter um único `origin` elemento que especifica `*` para permitir qualquer origem, ou um ou mais `origin` elementos que contenham um URI.|Sim|N/A|
|origem|O valor pode ser `*` para permitir todas as origens ou um URI que especifique uma única origem. O URI deve incluir um esquema, um host e uma porta.|Sim|Se a porta for omitida em um URI, a porta 80 será usada para HTTP e a porta 443 será usada para HTTPS.|
|métodos permitidos|Esse elemento será necessário se forem permitidos métodos diferentes de GET ou POST. Contém `method` elementos que especificam os verbos HTTP com suporte.|Não|Se esta seção não estiver presente, GET e POST terão suporte.|
|method|Especifica um verbo HTTP.|Pelo menos um `method` elemento será necessário se a `allowed-methods` seção estiver presente.|N/A|
|cabeçalhos permitidos|Esse elemento contém `header` elementos que especificam os nomes dos cabeçalhos que podem ser incluídos na solicitação.|Não|N/A|
|expose-headers|Esse elemento contém `header` elementos que especificam os nomes dos cabeçalhos que poderão ser acessados pelo cliente.|Não|N/A|
|cabeçalho|Especifica um nome de cabeçalho.|Pelo menos um `header` elemento é necessário no `allowed-headers` ou `expose-headers` se a seção estiver presente.|N/A|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Requerido|Predefinição|
|----------|-----------------|--------------|-------------|
|allow-credentials|O `Access-Control-Allow-Credentials` cabeçalho na resposta de simulação será definido como o valor desse atributo e afetará a capacidade do cliente de enviar credenciais em solicitações entre domínios.|Não|false|
|simulação-resultado-máx-idade|O `Access-Control-Max-Age` cabeçalho na resposta de simulação será definido como o valor desse atributo e afetará a capacidade do agente do usuário de armazenar em cache a resposta de simulação.|Não|0|

### <a name="usage"></a>Utilização
Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos escopos de política a seguir. [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)

- **Seções de política:** entrada
- Escopos de **política:** todos os escopos

## <a name="JSONP"></a> JSONP
A `jsonp` política adiciona o suporte a JSON com preenchimento (JSONP) a uma operação ou a uma API para permitir chamadas entre domínios de clientes baseados em navegador JavaScript. JSONP é um método usado em programas JavaScript para solicitar dados de um servidor em um domínio diferente. JSONP ignora a limitação imposta pela maioria dos navegadores da Web onde o acesso a páginas da Web deve estar no mesmo domínio.

### <a name="policy-statement"></a>Declaração de política

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Exemplo

```xml
<jsonp callback-parameter-name="cb" />
```

Se você chamar o método sem o parâmetro de retorno de chamada? CB = XXX, ele retornará um JSON simples (sem um wrapper de chamada de função).

Se você adicionar o parâmetro `?cb=XXX` de retorno de chamada, ele retornará um resultado de JSONP, encapsulando os resultados JSON originais em torno da função de retorno de chamada como`XYZ('<json result goes here>');`

### <a name="elements"></a>Elementos

|Nome|Descrição|Requerido|
|----------|-----------------|--------------|
|JSONP|Elemento raiz.|Sim|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Requerido|Predefinição|
|----------|-----------------|--------------|-------------|
|retorno de chamada-nome-do-parâmetro|A chamada de função JavaScript entre domínios prefixada com o nome de domínio totalmente qualificado em que a função reside.|Sim|N/A|

### <a name="usage"></a>Utilização
Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos escopos de política a seguir. [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)

- **Seções de política:** saída
- Escopos de **política:** todos os escopos

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre como trabalhar com políticas, consulte:

+ [Políticas no gerenciamento de API](api-management-howto-policies.md)
+ [APIs de transformação](transform-api.md)
+ [Referência de política](api-management-policy-reference.md) para uma lista completa de instruções de política e suas configurações
+ [Exemplos de política](policy-samples.md)
