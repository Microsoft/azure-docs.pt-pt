---
title: Políticas de restrição de acesso à API Da Azure API Microsoft Docs
description: Conheça as políticas de restrição de acesso disponíveis para utilização na Gestão aPI azure.
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
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: 3ba620d66b84e6724751b2024059e8ecd66888cd
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374254"
---
# <a name="api-management-access-restriction-policies"></a>Políticas de restrição de acesso à gestão da API

Este tópico fornece uma referência para as seguintes políticas de Gestão da API. Para obter informações sobre a adição e configuração de políticas, consulte [Políticas na Gestão da API](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="AccessRestrictionPolicies"></a>Políticas de restrição de acesso

-   Consulte o [cabeçalho HTTP](api-management-access-restriction-policies.md#CheckHTTPHeader) - Aplica a existência e/ou o valor de um cabeçalho HTTP.
-   Limitar a taxa de [chamada por subscrição](api-management-access-restriction-policies.md#LimitCallRate) - Previne picos de utilização da API limitando a taxa de chamada, por subscrição.
-   [Limitar a taxa de chamada por chave](#LimitCallRateByKey) - Previne picos de utilização de API limitando a taxa de chamada, numa base chave.
-   [Restringir os IPs de chamada](api-management-access-restriction-policies.md#RestrictCallerIPs) - Filtrar (permite/nega) chamadas de endereços IP específicos e/ou intervalos de endereços.
-   Definir quota de [utilização por subscrição](api-management-access-restriction-policies.md#SetUsageQuota) - Permite-lhe impor uma quota de chamada renovável ou vitalícia e/ou largura de banda, por subscrição.
-   Definir a quota de [utilização por chave](#SetUsageQuotaByKey) - Permite-lhe impor uma quota de chamada renovável ou vitalícia e/ou largura de banda, por base chave.
-   [Validar jWT](api-management-access-restriction-policies.md#ValidateJWT) - Aplica a existência e validade de um JWT extraído de um cabeçalho HTTP especificado ou de um parâmetro de consulta especificado.

> [!TIP]
> Pode utilizar políticas de restrição de acesso em diferentes âmbitos para diferentes fins. Por exemplo, pode fixar toda a API com autenticação AAD aplicando a política `validate-jwt` no nível de API ou pode aplicá-la ao nível de operação da API e usá-la `claims` para um controlo mais granular.

## <a name="CheckHTTPHeader"></a>Verifique o cabeçalho HTTP

Utilize a política `check-header` para fazer valer que um pedido tem um cabeçalho HTTP especificado. Pode verificar opcionalmente se o cabeçalho tem um valor específico ou se verifica uma gama de valores permitidos. Se o cheque falhar, a apólice termina o processamento de pedidos e devolve o código de estado http e a mensagem de erro especificada pela apólice.

### <a name="policy-statement"></a>Declaração política

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

| Nome         | Descrição                                                                                                                                   | Necessário |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| cabeçalho de cheque | Elemento de raiz.                                                                                                                                 | Sim      |
| valor        | Valor permitido do cabeçalho HTTP. Quando vários elementos de valor são especificados, a verificação é considerada um sucesso se algum dos valores for compatível. | Não       |

### <a name="attributes"></a>Atributos

| Nome                       | Descrição                                                                                                                                                            | Necessário | Predefinição |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| failed-check-error-message | Mensagem de erro para devolver no corpo de resposta HTTP se o cabeçalho não existir ou tiver um valor inválido. Esta mensagem deve ter personagens especiais devidamente escapados. | Sim      | N/D     |
| failed-check-httpcode      | Código de Estado HTTP para devolver se o cabeçalho não existir ou tiver um valor inválido.                                                                                        | Sim      | N/D     |
| cabeçalho-nome                | O nome do Cabeçalho HTTP para verificar.                                                                                                                                  | Sim      | N/D     |
| ignorar caso                | Pode ser definido para verdade ou falso. Se definido para o caso True é ignorado quando o valor cabeçalho é comparado com o conjunto de valores aceitáveis.                                    | Sim      | N/D     |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada, saída

-   **Âmbitos de política:** todos os âmbitos

## <a name="LimitCallRate"></a>Limitar a taxa de chamada por subscrição

A política `rate-limit` impede os picos de utilização da API por subscrição, limitando a taxa de chamada a um número especificado por um período de tempo especificado. Quando esta política é desencadeada, o chamador recebe um código de estado de resposta `429 Too Many Requests`.

> [!IMPORTANT]
> Esta política só pode ser utilizada uma vez por documento político.
>
> [As expressões políticas](api-management-policy-expressions.md) não podem ser utilizadas em nenhum dos atributos políticos desta política.

> [!CAUTION]
> Devido à natureza distribuída da arquitetura de estrangulamento, a limitação das taxas nunca é completamente exata. A diferença entre configurado e o número real de pedidos permitidos varia em função do volume e taxa de pedido, latência de backend e outros fatores.

### <a name="policy-statement"></a>Declaração política

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

| Nome       | Descrição                                                                                                                                                                                                                                                                                              | Necessário |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| limite de taxas | Elemento de raiz.                                                                                                                                                                                                                                                                                            | Sim      |
| api        | Adicione um ou mais destes elementos para impor um limite de taxa de chamada às APIs dentro do produto. Os limites da taxa de chamada do produto e da API são aplicados de forma independente. A API pode ser referenciada através de `name` ou `id`. Se ambos os atributos forem fornecidos, `id` serão utilizados e `name` serão ignorados.                    | Não       |
| operation  | Adicione um ou mais destes elementos para impor um limite de taxa de chamada para operações dentro de uma API. Os limites da taxa de chamada de produto, API e operação são aplicados de forma independente. O funcionamento pode ser referenciado através de `name` ou `id`. Se ambos os atributos forem fornecidos, `id` serão utilizados e `name` serão ignorados. | Não       |

### <a name="attributes"></a>Atributos

| Nome           | Descrição                                                                                           | Necessário | Predefinição |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| nome           | O nome da API para aplicar o limite de taxa.                                                | Sim      | N/D     |
| chamadas          | O número total máximo de chamadas permitidas durante o intervalo de tempo especificado no `renewal-period`. | Sim      | N/D     |
| período de renovação | O período de tempo em segundos após o qual a quota repõe.                                              | Sim      | N/D     |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada

-   **Âmbitos de política:** produto, api, operação

## <a name="LimitCallRateByKey"></a>Limitar a taxa de chamada por chave

> [!IMPORTANT]
> Esta funcionalidade não está disponível no nível de **Consumo** da Gestão API.

A política `rate-limit-by-key` impede os picos de utilização da API numa base chave, limitando a taxa de chamada a um número especificado por um período de tempo especificado. A chave pode ter um valor de cadeia arbitrário e é normalmente fornecida usando uma expressão política. A condição de incremento opcional pode ser adicionada para especificar quais os pedidos que devem ser contados para o limite. Quando esta política é desencadeada, o chamador recebe um código de estado de resposta `429 Too Many Requests`.

Para mais informações e exemplos desta política, consulte o pedido avançado de estrangulamento com a [Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).

> [!CAUTION]
> Devido à natureza distribuída da arquitetura de estrangulamento, a limitação das taxas nunca é completamente exata. A diferença entre configurado e o número real de pedidos permitidos varia em função do volume e taxa de pedido, latência de backend e outros fatores.

### <a name="policy-statement"></a>Declaração política

```xml
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" />

```

### <a name="example"></a>Exemplo

No exemplo seguinte, o limite de taxa é fixado pelo endereço IP do autor da chamada.

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

| Nome              | Descrição   | Necessário |
| ----------------- | ------------- | -------- |
| taxa-limite-por-chave | Elemento de raiz. | Sim      |

### <a name="attributes"></a>Atributos

| Nome                | Descrição                                                                                           | Necessário | Predefinição |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| chamadas               | O número total máximo de chamadas permitidas durante o intervalo de tempo especificado no `renewal-period`. | Sim      | N/D     |
| contra-chave         | A chave a utilizar para a política de limite sanrá-lo.                                                             | Sim      | N/D     |
| incremento-condição | A expressão booleana especificando se o pedido deve ser contado para o contingente (`true`).        | Não       | N/D     |
| período de renovação      | O período de tempo em segundos após o qual a quota repõe.                                              | Sim      | N/D     |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada

-   **Âmbitos de política:** todos os âmbitos

## <a name="RestrictCallerIPs"></a>Restringir os IPs de chamada

Os filtros de política `ip-filter` (permite/nega) chamadas de endereços IP específicos e/ou intervalos de endereços.

### <a name="policy-statement"></a>Declaração política

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>Exemplo

No exemplo seguinte, a política apenas permite pedidos provenientes do endereço IP único ou do leque de endereços IP especificados

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>Elementos

| Nome                                      | Descrição                                         | Necessário                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| ip-filtro                                 | Elemento de raiz.                                       | Sim                                                            |
| endereço                                   | Especifica um único endereço IP para filtrar.   | É necessário pelo menos um elemento `address` ou `address-range`. |
| endereço-gama de="endereço" para="endereço" | Especifica uma gama de endereços IP para filtrar. | É necessário pelo menos um elemento `address` ou `address-range`. |

### <a name="attributes"></a>Atributos

| Nome                                      | Descrição                                                                                 | Necessário                                           | Predefinição |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| endereço-gama de="endereço" para="endereço" | Uma gama de endereços IP para permitir ou negar o acesso.                                        | Necessário quando o elemento `address-range` é utilizado. | N/D     |
| ip-filter action="allow &#124; forbid"    | Especifica se as chamadas devem ou não ser permitidas para os endereços e intervalos IP especificados. | Sim                                                | N/D     |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada
-   **Âmbitos de política:** todos os âmbitos

## <a name="SetUsageQuota"></a>Definir quota de utilização por subscrição

A política `quota` impõe um volume de chamadas renováveis ou vitalícios e/ou quota de largura de banda, por subscrição.

> [!IMPORTANT]
> Esta política só pode ser utilizada uma vez por documento político.
>
> [As expressões políticas](api-management-policy-expressions.md) não podem ser utilizadas em nenhum dos atributos políticos desta política.

### <a name="policy-statement"></a>Declaração política

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

| Nome      | Descrição                                                                                                                                                                                                                                                                                  | Necessário |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| quota     | Elemento de raiz.                                                                                                                                                                                                                                                                                | Sim      |
| api       | Adicione um ou mais destes elementos para impor quota de chamada sobre APIs dentro do produto. As quotas de chamada de produto e API são aplicadas de forma independente. A API pode ser referenciada através de `name` ou `id`. Se ambos os atributos forem fornecidos, `id` serão utilizados e `name` serão ignorados.                    | Não       |
| operation | Adicione um ou mais destes elementos para impor quotas de chamada em operações dentro de uma API. As quotas de chamada de produto, API e operação são aplicadas de forma independente. O funcionamento pode ser referenciado através de `name` ou `id`. Se ambos os atributos forem fornecidos, `id` serão utilizados e `name` serão ignorados. | Não       |

### <a name="attributes"></a>Atributos

| Nome           | Descrição                                                                                               | Necessário                                                         | Predefinição |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| nome           | O nome da API ou operação para a qual a quota se aplica.                                             | Sim                                                              | N/D     |
| largura de banda      | O número total máximo de quilobytes permitidos durante o intervalo de tempo especificado na `renewal-period`. | Devem ser especificados `calls`, `bandwidth`, ou ambos juntos. | N/D     |
| chamadas          | O número total máximo de chamadas permitidas durante o intervalo de tempo especificado no `renewal-period`.     | Devem ser especificados `calls`, `bandwidth`, ou ambos juntos. | N/D     |
| período de renovação | O período de tempo em segundos após o qual a quota repõe.                                                  | Sim                                                              | N/D     |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada
-   **Âmbitos de política:** produto

## <a name="SetUsageQuotaByKey"></a>Definir quota de utilização por chave

> [!IMPORTANT]
> Esta funcionalidade não está disponível no nível de **Consumo** da Gestão API.

A política `quota-by-key` impõe uma quota de chamada renovável ou vitalícia e/ou quota de largura de banda, numa base fundamental. A chave pode ter um valor de cadeia arbitrário e é normalmente fornecida usando uma expressão política. A condição de incremento opcional pode ser adicionada para especificar quais os pedidos que devem ser contados para a quota. Se várias políticas incrementassem o mesmo valor-chave, só é incrementada uma vez por pedido. Quando o limite de chamada é atingido, o chamador recebe um código de estado de resposta `403 Forbidden`.

Para mais informações e exemplos desta política, consulte o pedido avançado de estrangulamento com a [Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).

### <a name="policy-statement"></a>Declaração política

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### <a name="example"></a>Exemplo

No exemplo seguinte, a quota é chave do endereço IP do autor da chamada.

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

| Nome  | Descrição   | Necessário |
| ----- | ------------- | -------- |
| quota | Elemento de raiz. | Sim      |

### <a name="attributes"></a>Atributos

| Nome                | Descrição                                                                                               | Necessário                                                         | Predefinição |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| largura de banda           | O número total máximo de quilobytes permitidos durante o intervalo de tempo especificado na `renewal-period`. | Devem ser especificados `calls`, `bandwidth`, ou ambos juntos. | N/D     |
| chamadas               | O número total máximo de chamadas permitidas durante o intervalo de tempo especificado no `renewal-period`.     | Devem ser especificados `calls`, `bandwidth`, ou ambos juntos. | N/D     |
| contra-chave         | A chave a utilizar para a política de quotas.                                                                      | Sim                                                              | N/D     |
| incremento-condição | A expressão booleana especificando se o pedido deve ser contado para o contingente (`true`)             | Não                                                               | N/D     |
| período de renovação      | O período de tempo em segundos após o qual a quota repõe.                                                  | Sim                                                              | N/D     |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada
-   **Âmbitos de política:** todos os âmbitos

## <a name="ValidateJWT"></a>Validar JWT

A política `validate-jwt` impõe a existência e validade de um JWT extraído de um cabeçalho HTTP especificado ou de um parâmetro de consulta especificado.

> [!IMPORTANT]
> A política `validate-jwt` exige que o pedido registado `exp` seja incluído no símbolo JWT, a menos que `require-expiration-time` atributo seja especificado e definido para `false`.
> A política `validate-jwt` suporta algoritmos de assinatura HS256 e RS256. Para o HS256, a chave deve ser fornecida dentro da política no formulário codificado base64. Para o RS256, a chave tem de ser disponibilizada através de um ponto final de configuração open ID.
> A política `validate-jwt` suporta tokens encriptados com chaves simétricas usando os seguintes algoritmos de encriptação A128CBC-HS256, A192CBC-HS384, A256CBC-HS512.

### <a name="policy-statement"></a>Declaração política

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
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />
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
</validate-jwt>

```

### <a name="examples"></a>Exemplos

#### <a name="simple-token-validation"></a>Validação simples de fichas

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

#### <a name="azure-active-directory-token-validation"></a>Validação de token de diretório ativo Azure

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

#### <a name="azure-active-directory-b2c-token-validation"></a>Validação de token do Diretório Ativo Azure B2C

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

#### <a name="authorize-access-to-operations-based-on-token-claims"></a>Autorizar o acesso a operações com base em sinistros simbólicos

Este exemplo mostra como usar a política [JWT validada](api-management-access-restriction-policies.md#ValidateJWT) para autorizar o acesso a operações com base no valor dos créditos simbólicos.

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

### <a name="elements"></a>Elementos

| Elemento             | Descrição                                                                                                                                                                                                                                                                                                                                           | Necessário |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-jwt        | Elemento de raiz.                                                                                                                                                                                                                                                                                                                                         | Sim      |
| públicos           | Contém uma lista de reivindicações aceitáveis do público que podem estar presentes no símbolo. Se vários valores de audiência estiverem presentes, então cada valor é tentado até que todos estejam esgotados (caso em que a validação falha) ou até que um tenha sucesso. Pelo menos um público deve ser especificado.                                                                     | Não       |
| issuer-signing-keys | Uma lista de chaves de segurança codificadas pela Base64 usadas para validar fichas assinadas. Se estiverem presentes várias teclas de segurança, cada tecla é tentada até que todas estejam esgotadas (caso em que a validação falha) ou até que uma tenha sucesso (útil para capotamento de fichas). Os elementos-chave têm um atributo opcional `id` usado para corresponder à `kid` reivindicação.               | Não       |
| decryption-keys     | Uma lista de chaves codificadas base64 usadas para desencriptar os tokens. Se estiverem presentes várias teclas de segurança, cada tecla é tentada até que todas as teclas estejam esgotadas (caso em que a validação falha) ou até que uma chave tenha sucesso. Os elementos-chave têm um atributo opcional `id` usado para corresponder à `kid` reivindicação.                                                 | Não       |
| emitentes             | Uma lista de diretores aceitáveis que emitiu o símbolo. Se estiverem presentes vários valores emitentes, cada valor é tentado até que todos estejam esgotados (caso em que a validação falha) ou até que um tenha sucesso.                                                                                                                                         | Não       |
| openid-config       | O elemento utilizado para especificar um ponto final de configuração de Open ID compatível a partir do qual podem ser obtidas chaves de assinatura e emitente.                                                                                                                                                                                                                        | Não       |
| reivindicações necessárias     | Contém uma lista de reclamações que se espera estarem presentes no símbolo para que seja considerado válido. Quando o atributo `match` for definido para `all` todos os valores de reclamação da apólice devem estar presentes no símbolo para que a validação tenha sucesso. Quando o atributo `match` estiver definido para `any` pelo menos uma reclamação deve estar presente no símbolo para que a validação tenha sucesso. | Não       |

### <a name="attributes"></a>Atributos

| Nome                            | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                                            | Necessário                                                                         | Predefinição                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| relógio-skew                      | O Timespan. Utilize para especificar a diferença horária máxima esperada entre os relógios do sistema do emitente simbólico e a instância de Gestão API.                                                                                                                                                                                                                                                                                                               | Não                                                                               | 0 segundos                                                                         |
| failed-validation-error-message | Mensagem de erro para devolver no organismo de resposta HTTP se o JWT não passar na validação. Esta mensagem deve ter personagens especiais devidamente escapados.                                                                                                                                                                                                                                                                                                 | Não                                                                               | A mensagem de erro padrão depende do problema de validação, por exemplo "JWT não está presente". |
| falha-validação-httpcode      | Código de Estado HTTP para devolver se o JWT não passar na validação.                                                                                                                                                                                                                                                                                                                                                                                         | Não                                                                               | 401                                                                               |
| cabeçalho-nome                     | O nome do cabeçalho HTTP segurando o símbolo.                                                                                                                                                                                                                                                                                                                                                                                                         | Deve ser especificado um dos `header-name`, `query-parameter-name` ou `token-value`. | N/D                                                                               |
| consulta-parâmetro-nome            | O nome do parâmetro de consulta segurando o símbolo.                                                                                                                                                                                                                                                                                                                                                                                                     | Deve ser especificado um dos `header-name`, `query-parameter-name` ou `token-value`. | N/D                                                                               |
| valor simbólico                     | Expressão devolvendo uma corda contendo ficha JWT                                                                                                                                                                                                                                                                                                                                                                                                     | Deve ser especificado um dos `header-name`, `query-parameter-name` ou `token-value`. | N/D                                                                               |
| ID                              | O atributo `id` no elemento `key` permite especificar a cadeia que será compatível com `kid` reclamação no token (se presente) para descobrir a chave adequada para a validação da assinatura.                                                                                                                                                                                                                                           | Não                                                                               | N/D                                                                               |
| Jogo                           | O `match` atributo sobre o elemento `claim` especifica se todos os valores de reclamação na apólice devem estar presentes no símbolo para que a validação tenha êxito. Os valores possíveis são:<br /><br /> - `all` - todos os valores de reclamação na política devem estar presentes no símbolo para que a validação tenha êxito.<br /><br /> - `any` - pelo menos um valor de reclamação deve estar presente no símbolo para que a validação tenha sucesso.                                                       | Não                                                                               | all                                                                               |
| require-expiration-time         | Boolean. Especifica se é necessária uma reclamação de validade no símbolo.                                                                                                                                                                                                                                                                                                                                                                               | Não                                                                               | true                                                                              |
| esquema de necessidade                  | O nome do esquema simbólico, por exemplo, "Portador". Quando este atributo for definido, a política garantirá que o regime especificado está presente no valor cabeçalho da Autorização.                                                                                                                                                                                                                                                                                    | Não                                                                               | N/D                                                                               |
| require-signed-tokens           | Boolean. Especifica se é necessário assinar um símbolo.                                                                                                                                                                                                                                                                                                                                                                                           | Não                                                                               | true                                                                              |
| separador                       | Cadeia. Especifica um separador (por exemplo,"") a utilizar para extrair um conjunto de valores de uma reivindicação de vários valores.                                                                                                                                                                                                                                                                                                                                          | Não                                                                               | N/D                                                                               |
| url                             | Url final de ponto de configuração de ID aberto a partir do qual os metadados de configuração de Open ID podem ser obtidos. A resposta deve ser de acordo com as especificações definidas no URL:`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`. Para o Diretório Ativo Azure utilize o seguinte URL: `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` substituindo o nome do seu nome de inquilino de diretório, por exemplo, `contoso.onmicrosoft.com`. | Sim                                                                              | N/D                                                                               |
| nome de saída-token-variável      | Cadeia. Nome da variável de contexto que receberá valor simbólico como objeto de tipo [`Jwt`](api-management-policy-expressions.md) após validação simbólica bem sucedida                                                                                                                                                                                                                                                                                     | Não                                                                               | N/D                                                                               |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)de política.

-   **Secções políticas:** entrada
-   **Âmbitos de política:** todos os âmbitos

## <a name="next-steps"></a>Passos seguintes

Para mais informações que trabalhem com políticas, consulte:

-   [Políticas em Gestão aPi](api-management-howto-policies.md)
-   [Transforme APIs](transform-api.md)
-   [Referência política](api-management-policy-reference.md) para uma lista completa de declarações políticas e suas configurações
-   [Amostras políticas](policy-samples.md)
