---
title: Políticas de restrição de acesso de gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre as políticas de restrição de acesso disponíveis para uso no gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/21/2019
ms.author: apimpm
ms.openlocfilehash: 3201edd3b90d6db1393286db688b24065ea8dc6b
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273533"
---
# <a name="api-management-access-restriction-policies"></a>Políticas de restrição de acesso de gerenciamento de API

Este tópico fornece uma referência para as seguintes políticas de gerenciamento de API. Para obter informações sobre como adicionar e configurar políticas, consulte [políticas no gerenciamento de API](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="AccessRestrictionPolicies"></a>Políticas de restrição de acesso

-   [Verificar cabeçalho http](api-management-access-restriction-policies.md#CheckHTTPHeader) – impõe a existência e/ou valor de um cabeçalho http.
-   [Limitar a taxa de chamada por assinatura](api-management-access-restriction-policies.md#LimitCallRate) -impede picos de uso da API limitando a taxa de chamada, por assinatura.
-   [Limitar a taxa de chamada por chave](#LimitCallRateByKey) -impede picos de uso da API limitando a taxa de chamada, de acordo com a chave.
-   [Restringir IPS de chamador](api-management-access-restriction-policies.md#RestrictCallerIPs) – filtra (permite/nega) chamadas de endereços IP específicos e/ou intervalos de endereços.
-   [Definir cota de uso por assinatura](api-management-access-restriction-policies.md#SetUsageQuota) – permite impor uma cota renovável ou de tempo de vida de volume de chamada e/ou largura de banda, por assinatura.
-   [Definir a cota de uso por chave](#SetUsageQuotaByKey) – permite que você aplique uma cota renovável ou de tempo de vida de volume de chamada e/ou largura de banda, de acordo com a chave.
-   [Validar JWT](api-management-access-restriction-policies.md#ValidateJWT) – impõe a existência e a validade de um JWT extraído de um cabeçalho HTTP especificado ou um parâmetro de consulta especificado.

> [!TIP]
> Você pode usar políticas de restrição de acesso em escopos diferentes para finalidades diferentes. Por exemplo, você pode proteger toda a API com a autenticação do AAD aplicando a `validate-jwt` política no nível de API ou pode aplicá-la no nível de operação da API e usá `claims` -la para um controle mais granular.

## <a name="CheckHTTPHeader"></a>Verificar cabeçalho HTTP

Use a `check-header` política para impor que uma solicitação tenha um cabeçalho HTTP especificado. Opcionalmente, você pode verificar se o cabeçalho tem um valor específico ou verificar se há um intervalo de valores permitidos. Se a verificação falhar, a política encerrará o processamento da solicitação e retornará o código de status HTTP e a mensagem de erro especificada pela política.

### <a name="policy-statement"></a>Declaração de política

```xml
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="true">
    <value>Value1</value>
    <value>Value2</value>
</check-header>
```

### <a name="example"></a>Exemplo

```xml
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>
</check-header>
```

### <a name="elements"></a>Elementos

| Name         | Descrição                                                                                                                                   | Requerido |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| cabeçalho de verificação | Elemento raiz.                                                                                                                                 | Sim      |
| value        | Valor de cabeçalho HTTP permitido. Quando vários elementos de valor são especificados, a verificação é considerada um sucesso se qualquer um dos valores for uma correspondência. | Não       |

### <a name="attributes"></a>Atributos

| Name                       | Descrição                                                                                                                                                            | Requerido | Predefinição |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| failed-check-error-message | Mensagem de erro para retornar no corpo da resposta HTTP se o cabeçalho não existir ou tiver um valor inválido. Esta mensagem deve ter caracteres especiais com escape correto. | Sim      | N/A     |
| failed-check-httpcode      | Código de status HTTP para retornar se o cabeçalho não existir ou tiver um valor inválido.                                                                                        | Sim      | N/A     |
| nome do cabeçalho                | O nome do cabeçalho HTTP a ser verificado.                                                                                                                                  | Sim      | N/A     |
| Ignorar maiúsculas/minúsculas                | Pode ser definido como true ou false. Se definido como true Case é ignorado quando o valor do cabeçalho é comparado com o conjunto de valores aceitáveis.                                    | Sim      | N/A     |

### <a name="usage"></a>Utilização

Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada, saída

-   **Escopos de política:** todos os escopos

## <a name="LimitCallRate"></a>Limitar a taxa de chamada por assinatura

A `rate-limit` política impede picos de uso da API por assinatura, limitando a taxa de chamada a um número especificado por um período de tempo especificado. Quando essa política é disparada, o `429 Too Many Requests` chamador recebe um código de status de resposta.

> [!IMPORTANT]
> Essa política pode ser usada apenas uma vez por documento de política.
>
> As [expressões de política](api-management-policy-expressions.md) não podem ser usadas em nenhum dos atributos de política para esta política.

> [!CAUTION]
> Devido à natureza distribuída da arquitetura de limitação, a limitação de taxa nunca é completamente precisa. A diferença entre a configuração e o número real de solicitações permitidas varia de acordo com o volume e a taxa de solicitação, a latência de back-end e outros fatores.

### <a name="policy-statement"></a>Declaração de política

```xml
<rate-limit calls="number" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</rate-limit>
```

### <a name="example"></a>Exemplo

```xml
<policies>
    <inbound>
        <base />
        <rate-limit calls="20" renewal-period="90" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementos

| Name      | Descrição                                                                                                                                                                                                                                                                                              | Requerido |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| taxa-limite | Elemento raiz.                                                                                                                                                                                                                                                                                            | Sim      |
| api       | Adicione um ou mais desses elementos para impor um limite de taxa de chamada em APIs dentro do produto. Os limites de taxa de chamada de API e produto são aplicados de forma independente. A API pode ser referenciada `name` via `id`ou. Se ambos os atributos forem fornecidos `id` , serão usados e `name` serão ignorados.                    | Não       |
| operation | Adicione um ou mais desses elementos para impor um limite de taxa de chamada em operações em uma API. Os limites de taxa de chamada de produto, API e operação são aplicados de forma independente. A operação pode ser referenciada `name` via `id`ou. Se ambos os atributos forem fornecidos `id` , serão usados e `name` serão ignorados. | Não       |

### <a name="attributes"></a>Atributos

| Name           | Descrição                                                                                           | Requerido | Predefinição |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| name           | O nome da API para a qual aplicar o limite de taxa.                                                | Sim      | N/A     |
| chamadas          | O número total máximo de chamadas permitidas durante o intervalo de tempo especificado `renewal-period`em. | Sim      | N/A     |
| renovação-período | O período de tempo em segundos após o qual a cota é redefinida.                                              | Sim      | N/A     |

### <a name="usage"></a>Utilização

Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada

-   **Escopos de política:** produto, API, operação

## <a name="LimitCallRateByKey"></a>Limitar a taxa de chamada por chave

> [!IMPORTANT]
> Esse recurso não está disponível na camada de **consumo** do gerenciamento de API.

A `rate-limit-by-key` política impede picos de uso de API por chave, limitando a taxa de chamada a um número especificado por um período de tempo especificado. A chave pode ter um valor de cadeia de caracteres arbitrário e normalmente é fornecida usando uma expressão de política. Uma condição de incremento opcional pode ser adicionada para especificar quais solicitações devem ser contadas em direção ao limite. Quando essa política é disparada, o `429 Too Many Requests` chamador recebe um código de status de resposta.

Para obter mais informações e exemplos dessa política, consulte [limitação de solicitação avançada com o gerenciamento de API do Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).

> [!CAUTION]
> Devido à natureza distribuída da arquitetura de limitação, a limitação de taxa nunca é completamente precisa. A diferença entre a configuração e o número real de solicitações permitidas varia de acordo com o volume e a taxa de solicitação, a latência de back-end e outros fatores.

### <a name="policy-statement"></a>Declaração de política

```xml
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" />

```

### <a name="example"></a>Exemplo

No exemplo a seguir, o limite de taxa é codificado pelo endereço IP do chamador.

```xml
<policies>
    <inbound>
        <base />
        <rate-limit-by-key  calls="10"
              renewal-period="60"
              increment-condition="@(context.Response.StatusCode == 200)"
              counter-key="@(context.Request.IpAddress)"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementos

| Name              | Descrição   | Requerido |
| ----------------- | ------------- | -------- |
| taxa-limite por chave | Elemento raiz. | Sim      |

### <a name="attributes"></a>Atributos

| Name                | Descrição                                                                                           | Requerido | Predefinição |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| chamadas               | O número total máximo de chamadas permitidas durante o intervalo de tempo especificado `renewal-period`em. | Sim      | N/A     |
| chave do contador         | A chave a ser usada para a política de limite de taxa.                                                             | Sim      | N/A     |
| condição de incremento | A expressão booliana que especifica se a solicitação deve ser contada em`true`direção à cota ().        | Não       | N/A     |
| renovação-período      | O período de tempo em segundos após o qual a cota é redefinida.                                              | Sim      | N/A     |

### <a name="usage"></a>Utilização

Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada

-   **Escopos de política:** todos os escopos

## <a name="RestrictCallerIPs"></a>Restringir IPs do chamador

A `ip-filter` política filtra (permite/nega) chamadas de endereços IP específicos e/ou intervalos de endereços.

### <a name="policy-statement"></a>Declaração de política

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>Exemplo

No exemplo a seguir, a política só permite solicitações provenientes do endereço IP único ou do intervalo de endereços IP especificados

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>Elementos

| Name                                      | Descrição                                         | Requerido                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| filtro de IP                                 | Elemento raiz.                                       | Sim                                                            |
| endereço                                   | Especifica um único endereço IP no qual filtrar.   | Pelo menos um `address` elemento `address-range` ou é necessário. |
| Endereço-intervalo de = "endereço" para = "endereço" | Especifica um intervalo de endereços IP no qual filtrar. | Pelo menos um `address` elemento `address-range` ou é necessário. |

### <a name="attributes"></a>Atributos

| Name                                      | Descrição                                                                                 | Requerido                                           | Predefinição |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| Endereço-intervalo de = "endereço" para = "endereço" | Um intervalo de endereços IP para permitir ou negar acesso.                                        | Necessário quando o `address-range` elemento é usado. | N/A     |
| ip-filter action="allow &#124; forbid"    | Especifica se as chamadas devem ser permitidas ou não para os endereços IP e intervalos especificados. | Sim                                                | N/A     |

### <a name="usage"></a>Utilização

Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada
-   **Escopos de política:** todos os escopos

## <a name="SetUsageQuota"></a>Definir cota de uso por assinatura

A `quota` política impõe um volume de chamada renovável ou de tempo de vida e/ou cota de largura de banda, por assinatura.

> [!IMPORTANT]
> Essa política pode ser usada apenas uma vez por documento de política.
>
> As [expressões de política](api-management-policy-expressions.md) não podem ser usadas em nenhum dos atributos de política para esta política.

### <a name="policy-statement"></a>Declaração de política

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</quota>
```

### <a name="example"></a>Exemplo

```xml
<policies>
    <inbound>
        <base />
        <quota calls="10000" bandwidth="40000" renewal-period="3600" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementos

| Name      | Descrição                                                                                                                                                                                                                                                                                  | Requerido |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| quota     | Elemento raiz.                                                                                                                                                                                                                                                                                | Sim      |
| api       | Adicione um ou mais desses elementos para impor a cota de chamada em APIs dentro do produto. As cotas de chamada de API e produto são aplicadas de forma independente. A API pode ser referenciada `name` via `id`ou. Se ambos os atributos forem fornecidos `id` , serão usados e `name` serão ignorados.                    | Não       |
| operation | Adicione um ou mais desses elementos para impor a cota de chamada em operações em uma API. As cotas de chamada de produto, API e operação são aplicadas de forma independente. A operação pode ser referenciada `name` via `id`ou. Se ambos os atributos forem fornecidos `id` , serão usados e `name` serão ignorados. | Não       |

### <a name="attributes"></a>Atributos

| Name           | Descrição                                                                                               | Requerido                                                         | Predefinição |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| name           | O nome da API ou operação à qual a cota se aplica.                                             | Sim                                                              | N/A     |
| Largura de banda      | O número total máximo de kilobytes permitidos durante o intervalo de tempo especificado em `renewal-period`. | Ou `calls` ,`bandwidth`ou ambos juntos devem ser especificados. | N/A     |
| chamadas          | O número total máximo de chamadas permitidas durante o intervalo de tempo especificado `renewal-period`em.     | Ou `calls` ,`bandwidth`ou ambos juntos devem ser especificados. | N/A     |
| renovação-período | O período de tempo em segundos após o qual a cota é redefinida.                                                  | Sim                                                              | N/A     |

### <a name="usage"></a>Utilização

Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada
-   **Escopos de política:** produto

## <a name="SetUsageQuotaByKey"></a>Definir cota de uso por chave

> [!IMPORTANT]
> Esse recurso não está disponível na camada de **consumo** do gerenciamento de API.

A `quota-by-key` política impõe um volume de chamada renovável ou de tempo de vida e/ou uma cota de largura de banda, em uma base por chave. A chave pode ter um valor de cadeia de caracteres arbitrário e normalmente é fornecida usando uma expressão de política. É possível adicionar uma condição de incremento opcional para especificar quais solicitações devem ser contadas em direção à cota. Se várias políticas incrementarem o mesmo valor de chave, elas serão incrementadas apenas uma vez por solicitação. Quando o limite de chamada é atingido, o chamador recebe `403 Forbidden` um código de status de resposta.

Para obter mais informações e exemplos dessa política, consulte [limitação de solicitação avançada com o gerenciamento de API do Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).

### <a name="policy-statement"></a>Declaração de política

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### <a name="example"></a>Exemplo

No exemplo a seguir, a cota é codificada pelo endereço IP do chamador.

```xml
<policies>
    <inbound>
        <base />
        <quota-by-key calls="10000" bandwidth="40000" renewal-period="3600"
                      increment-condition="@(context.Response.StatusCode >= 200 && context.Response.StatusCode < 400)"
                      counter-key="@(context.Request.IpAddress)" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementos

| Name  | Descrição   | Requerido |
| ----- | ------------- | -------- |
| quota | Elemento raiz. | Sim      |

### <a name="attributes"></a>Atributos

| Name                | Descrição                                                                                               | Requerido                                                         | Predefinição |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| Largura de banda           | O número total máximo de kilobytes permitidos durante o intervalo de tempo especificado em `renewal-period`. | Ou `calls` ,`bandwidth`ou ambos juntos devem ser especificados. | N/A     |
| chamadas               | O número total máximo de chamadas permitidas durante o intervalo de tempo especificado `renewal-period`em.     | Ou `calls` ,`bandwidth`ou ambos juntos devem ser especificados. | N/A     |
| chave do contador         | A chave a ser usada para a política de cota.                                                                      | Sim                                                              | N/A     |
| condição de incremento | A expressão booliana que especifica se a solicitação deve ser contada em`true`direção à cota ()             | Não                                                               | N/A     |
| renovação-período      | O período de tempo em segundos após o qual a cota é redefinida.                                                  | Sim                                                              | N/A     |

### <a name="usage"></a>Utilização

Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada
-   **Escopos de política:** todos os escopos

## <a name="ValidateJWT"></a>Validar JWT

A `validate-jwt` política impõe a existência e a validade de um JWT extraído de um cabeçalho HTTP especificado ou um parâmetro de consulta especificado.

> [!IMPORTANT]
> A `validate-jwt` política requer que a `exp` declaração registrada seja incluída no token JWT, a `require-expiration-time` menos que o atributo seja especificado `false`e definido como.
> A `validate-jwt` política dá suporte a algoritmos de assinatura HS256 e RS256. Para HS256, a chave deve ser fornecida embutida na política na forma codificada em base64. Para RS256, a chave deve ser fornecida por meio de um ponto de extremidade de configuração do Open ID.
> A `validate-jwt` política dá suporte a tokens criptografados com chaves simétricas usando os seguintes algoritmos de criptografia A128CBC-HS256, A192CBC-HS384, A256CBC-HS512.

### <a name="policy-statement"></a>Declaração de política

```xml
<validate-jwt
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"
    failed-validation-httpcode="http status code to return on failure"
    failed-validation-error-message="error message to return on failure"
    token-value="expression returning JWT token as a string"
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"
    clock-skew="allowed clock skew in seconds"
    output-token-variable-name="name of a variable to receive a JWT object representing successfully validated token">
  <issuer-signing-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </issuer-signing-keys>
  <decryption-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </decryption-keys>
  <audiences>
    <audience>audience string</audience>
    <!-- if there are multiple possible audiences, then add additional audience elements -->
  </audiences>
  <issuers>
    <issuer>issuer string</issuer>
    <!-- if there are multiple possible issuers, then add additional issuer elements -->
  </issuers>
  <required-claims>
    <claim name="name of the claim as it appears in the token" match="all|any" separator="separator character in a multi-valued claim">
      <value>claim value as it is expected to appear in the token</value>
      <!-- if there is more than one allowed values, then add additional value elements -->
    </claim>
    <!-- if there are multiple possible allowed values, then add additional value elements -->
  </required-claims>
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />
  <zumo-master-key id="key identifier">key value</zumo-master-key>
</validate-jwt>

```

### <a name="examples"></a>Exemplos

#### <a name="simple-token-validation"></a>Validação de token simples

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key>  <!-- signing key specified as a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="azure-active-directory-token-validation"></a>Validação de token Azure Active Directory

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration" />
    <audiences>
        <audience>25eef6e4-c905-4a07-8eb4-0d08d5df8b3f</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="azure-active-directory-b2c-token-validation"></a>Validação de token Azure Active Directory B2C

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/b2c_1_signin/v2.0/.well-known/openid-configuration" />
    <audiences>
        <audience>d313c4e4-de5f-4197-9470-e509a2f0b806</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="authorize-access-to-operations-based-on-token-claims"></a>Autorizar o acesso a operações com base em declarações de token

Este exemplo mostra como usar a política [validar JWT](api-management-access-restriction-policies.md#ValidateJWT) para autorizar o acesso a operações com base no valor de declarações de token.

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer" output-token-variable-name="jwt">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key> <!-- signing key is stored in a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>
    </audiences>
    <issuers>
        <issuer>contoso.com</issuer>
    </issuers>
    <required-claims>
        <claim name="group" match="any">
            <value>finance</value>
            <value>logistics</value>
        </claim>
    </required-claims>
</validate-jwt>
<choose>
    <when condition="@(context.Request.Method == "POST" && !((Jwt)context.Variables["jwt"]).Claims["group"].Contains("finance"))">
        <return-response>
            <set-status code="403" reason="Forbidden" />
        </return-response>
    </when>
</choose>
```

#### <a name="azure-mobile-services-token-validation"></a>Validação de token dos serviços móveis do Azure

```xml
<validate-jwt header-name="x-zumo-auth" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Supplied access token is invalid.">
    <issuers>
        <issuer>urn:microsoft:windows-azure:zumo</issuer>
    </issuers>
    <audiences>
        <audience>Facebook</audience>
    </audiences>
    <issuer-signing-keys>
        <zumo-master-key id="0">insert key here</zumo-master-key>
    </issuer-signing-keys>
</validate-jwt>
```

### <a name="elements"></a>Elementos

| Elemento             | Descrição                                                                                                                                                                                                                                                                                                                                           | Requerido |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-jwt        | Elemento raiz.                                                                                                                                                                                                                                                                                                                                         | Sim      |
| compostos           | Contém uma lista de declarações de audiência aceitáveis que podem estar presentes no token. Se vários valores de audiência estiverem presentes, cada valor será tentado até que todos sejam esgotados (nesse caso, a validação falhará) ou até obter êxito. Pelo menos um público deve ser especificado.                                                                     | Não       |
| issuer-signing-keys | Uma lista de chaves de segurança codificadas em base64 usada para validar tokens assinados. Se várias chaves de segurança estiverem presentes, cada chave será tentada até que todas sejam esgotadas (nesse caso, a validação falhará) ou até que uma tenha êxito (útil para substituição de token). Os elementos principais têm um `id` atributo opcional usado para correspondência `kid` com a declaração.               | Não       |
| decryption-keys     | Uma lista de chaves codificadas em base64 usada para descriptografar os tokens. Se várias chaves de segurança estiverem presentes, cada chave será tentada até que todas as chaves sejam esgotadas (nesse caso, a validação falhará) ou até que uma chave seja bem-sucedida. Os elementos principais têm um `id` atributo opcional usado para correspondência `kid` com a declaração.                                                 | Não       |
| emissores             | Uma lista de entidades aceitáveis que emitiram o token. Se vários valores de emissor estiverem presentes, cada valor será tentado até que todos estejam esgotados (nesse caso, a validação falhará) ou até obter êxito.                                                                                                                                         | Não       |
| OpenID-config       | O elemento usado para especificar um ponto de extremidade de configuração de Open ID em conformidade do qual as chaves de assinatura e o emissor podem ser obtidos.                                                                                                                                                                                                                        | Não       |
| solicitações obrigatórias     | Contém uma lista de declarações que devem estar presentes no token para que ele seja considerado válido. Quando o `match` atributo é definido para `all` cada valor de declaração na política, deve estar presente no token para que a validação seja realizada com sucesso. Quando o `match` atributo é definido como `any` pelo menos uma declaração deve estar presente no token para que a validação seja realizada com sucesso. | Não       |
| Zumo-Master-Key     | Chave mestra para tokens emitidos pelos serviços móveis do Azure                                                                                                                                                                                                                                                                                                 | Não       |

### <a name="attributes"></a>Atributos

| Name                            | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                                            | Requerido                                                                         | Predefinição                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| distorção de relógio                      | Período. Use para especificar a diferença máxima de tempo esperada entre os relógios do sistema do emissor do token e a instância de gerenciamento de API.                                                                                                                                                                                                                                                                                                               | Não                                                                               | 0 segundo                                                                         |
| failed-validation-error-message | Mensagem de erro para retornar no corpo da resposta HTTP se o JWT não passar na validação. Esta mensagem deve ter caracteres especiais com escape correto.                                                                                                                                                                                                                                                                                                 | Não                                                                               | A mensagem de erro padrão depende do problema de validação, por exemplo "JWT não está presente". |
| falha-validação-códigohttp      | Código de status HTTP para retornar se o JWT não passar na validação.                                                                                                                                                                                                                                                                                                                                                                                         | Não                                                                               | 401                                                                               |
| nome do cabeçalho                     | O nome do cabeçalho HTTP que contém o token.                                                                                                                                                                                                                                                                                                                                                                                                         | `header-name` Umou`token-value`deveserespecificado. `query-parameter-name` | N/A                                                                               |
| nome do parâmetro de consulta            | O nome do parâmetro de consulta que contém o token.                                                                                                                                                                                                                                                                                                                                                                                                     | `header-name` Umou`token-value`deveserespecificado. `query-parameter-name` | N/A                                                                               |
| valor do token                     | Expressão que retorna uma cadeia de caracteres que contém o token JWT                                                                                                                                                                                                                                                                                                                                                                                                     | `header-name` Umou`token-value`deveserespecificado. `query-parameter-name` | N/A                                                                               |
| id                              | O `id` atributo `kid` no elemento permite que você especifique a cadeia de caracteres que será correspondida com a declaração no token (se presente) para descobrir a chave apropriada a ser usada para validação de assinatura. `key`                                                                                                                                                                                                                                           | Não                                                                               | N/A                                                                               |
| Correspondência                           | O `match` atributo`claim` no elemento especifica se cada valor de declaração na política deve estar presente no token para que a validação seja realizada com sucesso. Os valores possíveis são:<br /><br /> - `all`-cada valor de declaração na política deve estar presente no token para que a validação seja realizada com sucesso.<br /><br /> - `any`-pelo menos um valor de declaração deve estar presente no token para que a validação seja concluída com sucesso.                                                       | Não                                                                               | all                                                                               |
| require-expiration-time         | Boolean. Especifica se uma declaração de expiração é necessária no token.                                                                                                                                                                                                                                                                                                                                                                               | Não                                                                               | true                                                                              |
| exigir-esquema                  | O nome do esquema de token, por exemplo, "Portador". Quando esse atributo for definido, a política garantirá que o esquema especificado esteja presente no valor do cabeçalho de autorização.                                                                                                                                                                                                                                                                                    | Não                                                                               | N/A                                                                               |
| require-signed-tokens           | Boolean. Especifica se um token deve ser assinado.                                                                                                                                                                                                                                                                                                                                                                                           | Não                                                                               | true                                                                              |
| caractere                       | Strings. Especifica um separador (por exemplo, ",") a ser usado para extrair um conjunto de valores de uma declaração com vários valores.                                                                                                                                                                                                                                                                                                                                          | Não                                                                               | N/A                                                                               |
| url                             | URL de ponto de extremidade de configuração de ID aberta de onde os metadados de configuração de ID aberta podem ser obtidos. A resposta deve ser de acordo com as especificações, conforme definido na`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`URL:. Por Azure Active Directory use a seguinte URL: `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` substituindo o nome do locatário do diretório, `contoso.onmicrosoft.com`por exemplo,. | Sim                                                                              | N/A                                                                               |
saída-token-variável-nome|Strings. Nome da variável de contexto que receberá o valor de token como um [`Jwt`](api-management-policy-expressions.md) objeto do tipo após a validação de token bem-sucedida|Não|N/A

### <a name="usage"></a>Utilização

Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política a seguir.

-   **Seções de política:** entrada
-   **Escopos de política:** todos os escopos

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como trabalhar com políticas, consulte:

-   [Políticas no gerenciamento de API](api-management-howto-policies.md)
-   [APIs de transformação](transform-api.md)
-   [Referência de política](api-management-policy-reference.md) para uma lista completa de instruções de política e suas configurações
-   [Exemplos de política](policy-samples.md)
