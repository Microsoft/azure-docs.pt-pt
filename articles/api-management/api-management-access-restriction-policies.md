---
title: Azure API Management políticas de restrição de acesso Microsoft Docs
description: Conheça as políticas de restrição de acesso disponíveis para utilização na Azure API Management.
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
ms.openlocfilehash: 711a973f13c8e292578703518df4c4302c31eb57
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92071392"
---
# <a name="api-management-access-restriction-policies"></a>Políticas de restrição de acesso à Gestão de API

Este tópico fornece uma referência para as seguintes políticas de Gestão da API. Para obter informações sobre políticas de adição e configuração, consulte [Políticas em Gestão de API.](./api-management-policies.md)

## <a name="access-restriction-policies"></a><a name="AccessRestrictionPolicies"></a> Políticas de restrição de acesso

-   [Ver cabeçalho HTTP](api-management-access-restriction-policies.md#CheckHTTPHeader) - Executa a existência e/ou o valor de um cabeçalho HTTP.
-   [Taxa de chamada limite por subscrição](api-management-access-restriction-policies.md#LimitCallRate) - Previne picos de utilização da API limitando a taxa de chamada, por subscrição.
-   [Limitar a taxa de chamada por chave](#LimitCallRateByKey) - Previne picos de utilização da API limitando a taxa de chamada, por cada chave.
-   [Restringir os IPs do chamador](api-management-access-restriction-policies.md#RestrictCallerIPs) - Filtros (permite/nega) chamadas de endereços IP específicos e/ou intervalos de endereços.
-   [Definir quota de utilização por subscrição](api-management-access-restriction-policies.md#SetUsageQuota) - Permite-lhe impor um volume de chamada renovável ou vitalício e/ou quota de largura de banda, por subscrição.
-   [Definir quota de utilização por chave](#SetUsageQuotaByKey) - Permite-lhe impor um volume de chamada renovável ou vitalício e/ou quota de largura de banda, por chave.
-   [Validar JWT](api-management-access-restriction-policies.md#ValidateJWT) - Impõe a existência e validade de um JWT extraído de um cabeçalho HTTP especificado ou de um parâmetro de consulta especificado.

> [!TIP]
> Pode utilizar políticas de restrição de acesso em diferentes âmbitos para diferentes finalidades. Por exemplo, pode proteger toda a API com autenticação AAD aplicando a `validate-jwt` política ao nível da API ou pode aplicá-la ao nível de operação da API e usá-la `claims` para um controlo mais granular.

## <a name="check-http-header"></a><a name="CheckHTTPHeader"></a> Verifique o cabeçalho HTTP

Utilize a `check-header` política para impor que um pedido tem um cabeçalho HTTP especificado. Pode verificar opcionalmente se o cabeçalho tem um valor específico ou se verifica uma gama de valores permitidos. Se a verificação falhar, a apólice termina o processamento do pedido e devolve o código de estado HTTP e a mensagem de erro especificada pela apólice.

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

| Nome         | Descrição                                                                                                                                   | Obrigatório |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| cabeçalho de verificação | Elemento de raiz.                                                                                                                                 | Yes      |
| valor        | Valor do cabeçalho HTTP permitido. Quando vários elementos de valor são especificados, a verificação é considerada um sucesso se algum dos valores for compatível. | No       |

### <a name="attributes"></a>Atributos

| Nome                       | Descrição                                                                                                                                                            | Obrigatório | Predefinição |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| falha na verificação de erro-mensagem | Error message to return in the HTTP response body if the header don't exist or has a invalid value. Esta mensagem deve ter quaisquer caracteres especiais devidamente escapados. | Yes      | N/D     |
| falha no cheque-httpcode      | HTTP Código de estado para devolver se o cabeçalho não existir ou tiver um valor inválido.                                                                                        | Yes      | N/D     |
| cabeçalho-nome                | O nome do cabeçalho HTTP para verificar.                                                                                                                                  | Yes      | N/D     |
| ignorar caso                | Pode ser definido como Verdadeiro ou Falso. Se definido para o caso Verdadeiro é ignorado quando o valor do cabeçalho é comparado com o conjunto de valores aceitáveis.                                    | Yes      | N/D     |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada, saída

-   **Âmbitos de política:** todos os âmbitos

## <a name="limit-call-rate-by-subscription"></a><a name="LimitCallRate"></a> Taxa de chamada limite por subscrição

A `rate-limit` política impede picos de utilização da API por subscrição, limitando a taxa de chamada a um número especificado por um período de tempo especificado. Quando esta política é desencadeada, o chamador recebe um `429 Too Many Requests` código de estado de resposta.

> [!IMPORTANT]
> Esta política só pode ser utilizada uma vez por documento político.
>
> [As expressões políticas](api-management-policy-expressions.md) não podem ser utilizadas em nenhum dos atributos políticos para esta política.

> [!CAUTION]
> Devido à natureza distribuída da arquitetura de estrangulamento, a limitação das taxas nunca é completamente precisa. A diferença entre configurado e o número real de pedidos permitidos varia em função do volume e taxa de pedido, da latência de backend e de outros fatores.

> [!NOTE]
> Para compreender a diferença entre limites de taxa e quotas, [consulte limites de taxa e quotas.](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

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

| Nome       | Descrição                                                                                                                                                                                                                                                                                              | Obrigatório |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| limite de taxa | Elemento de raiz.                                                                                                                                                                                                                                                                                            | Yes      |
| api        | Adicione um ou mais destes elementos para impor um limite de taxa de chamada às APIs dentro do produto. Os limites de taxa de chamada de produto e API são aplicados de forma independente. A API pode ser referenciada através `name` ou `id` . . Se ambos os atributos forem fornecidos, `id` serão utilizados e `name` serão ignorados.                    | No       |
| operation  | Adicione um ou mais destes elementos para impor um limite de taxa de chamada às operações dentro de uma API. Os limites de taxa de chamada de produto, API e operação são aplicados de forma independente. A operação pode ser referenciada através `name` ou `id` . . Se ambos os atributos forem fornecidos, `id` serão utilizados e `name` serão ignorados. | No       |

### <a name="attributes"></a>Atributos

| Nome           | Descrição                                                                                           | Obrigatório | Predefinição |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| name           | O nome da API para a aplicação do limite de taxa.                                                | Yes      | N/D     |
| chama          | O número máximo total de chamadas permitidas durante o intervalo de tempo especificado no `renewal-period` . | Yes      | N/D     |
| período de renovação | O período de tempo em segundos após o qual a quota se repõe.                                              | Yes      | N/D     |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada

-   **Âmbitos de política:** produto, api, operação

## <a name="limit-call-rate-by-key"></a><a name="LimitCallRateByKey"></a> Taxa de chamada limite por chave

> [!IMPORTANT]
> Esta funcionalidade não está disponível no nível de **Consumo** da Gestão da API.

A `rate-limit-by-key` política impede picos de utilização da API numa base-chave, limitando a taxa de chamada a um número especificado por um período de tempo especificado. A chave pode ter um valor de cadeia arbitrária e é normalmente fornecida usando uma expressão política. A condição de incremento opcional pode ser adicionada para especificar quais os pedidos que devem ser contados para o limite. Quando esta política é desencadeada, o chamador recebe um `429 Too Many Requests` código de estado de resposta.

Para obter mais informações e exemplos desta política, consulte [o pedido avançado de estrangulamento com a Azure API Management](./api-management-sample-flexible-throttling.md).

> [!CAUTION]
> Devido à natureza distribuída da arquitetura de estrangulamento, a limitação das taxas nunca é completamente precisa. A diferença entre configurado e o número real de pedidos permitidos varia em função do volume e taxa de pedido, da latência de backend e de outros fatores.

> [!NOTE]
> Para compreender a diferença entre limites de taxa e quotas, [consulte limites de taxa e quotas.](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

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

| Nome              | Descrição   | Obrigatório |
| ----------------- | ------------- | -------- |
| taxa-limite-por-chave | Elemento de raiz. | Yes      |

### <a name="attributes"></a>Atributos

| Nome                | Descrição                                                                                           | Obrigatório | Predefinição |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| chama               | O número máximo total de chamadas permitidas durante o intervalo de tempo especificado no `renewal-period` . | Yes      | N/D     |
| contra-chave         | A chave a utilizar para a política de limite de taxas.                                                             | Yes      | N/D     |
| incremento condição | A expressão booleana especificando se o pedido deve ser contado para a quota `true` ().        | No       | N/D     |
| período de renovação      | O período de tempo em segundos após o qual a quota se repõe.                                              | Yes      | N/D     |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada

-   **Âmbitos de política:** todos os âmbitos

## <a name="restrict-caller-ips"></a><a name="RestrictCallerIPs"></a> Restringir os IPs de chamadas

Os `ip-filter` filtros de política (permite/nega) chamadas de endereços IP específicos e/ou intervalos de endereços.

### <a name="policy-statement"></a>Declaração política

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>Exemplo

No exemplo seguinte, a política apenas permite pedidos provenientes do único endereço IP ou intervalo de endereços IP especificados

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>Elementos

| Nome                                      | Descrição                                         | Obrigatório                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| ip-filtro                                 | Elemento de raiz.                                       | Yes                                                            |
| address                                   | Especifica um único endereço IP para filtrar.   | Pelo menos um `address` ou `address-range` elemento é necessário. |
| endereço-gama de="endereço" para ="endereço" | Especifica uma gama de endereços IP para filtrar. | Pelo menos um `address` ou `address-range` elemento é necessário. |

### <a name="attributes"></a>Atributos

| Nome                                      | Descrição                                                                                 | Obrigatório                                           | Predefinição |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| endereço-gama de="endereço" para ="endereço" | Uma gama de endereços IP para permitir ou negar acesso.                                        | Requerido quando o `address-range` elemento é utilizado. | N/D     |
| ip-filter action="permitir &#124; proibir"    | Especifica se as chamadas devem ser permitidas ou não para os endereços e intervalos IP especificados. | Yes                                                | N/D     |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada
-   **Âmbitos de política:** todos os âmbitos

## <a name="set-usage-quota-by-subscription"></a><a name="SetUsageQuota"></a> Definir quota de utilização por subscrição

A `quota` política aplica um volume de chamadas renováveis ou vitalícios e/ou quota de largura de banda, por subscrição.

> [!IMPORTANT]
> Esta política só pode ser utilizada uma vez por documento político.
>
> [As expressões políticas](api-management-policy-expressions.md) não podem ser utilizadas em nenhum dos atributos políticos para esta política.

> [!NOTE]
> Para compreender a diferença entre limites de taxa e quotas, [consulte limites de taxa e quotas.](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

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

| Nome      | Descrição                                                                                                                                                                                                                                                                                  | Obrigatório |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| quota     | Elemento de raiz.                                                                                                                                                                                                                                                                                | Yes      |
| api       | Adicione um ou mais destes elementos para impor quotas de chamada em APIs dentro do produto. As quotas de chamadas de produtos e API são aplicadas de forma independente. A API pode ser referenciada através `name` ou `id` . . Se ambos os atributos forem fornecidos, `id` serão utilizados e `name` serão ignorados.                    | No       |
| operation | Adicione um ou mais destes elementos para impor quota de chamada em operações dentro de uma API. As quotas de chamadas de produtos, API e operação são aplicadas de forma independente. A operação pode ser referenciada através `name` ou `id` . . Se ambos os atributos forem fornecidos, `id` serão utilizados e `name` serão ignorados. | No       |

### <a name="attributes"></a>Atributos

| Nome           | Descrição                                                                                               | Obrigatório                                                         | Predefinição |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| name           | O nome da API ou da operação para a qual o contingente se aplica.                                             | Yes                                                              | N/D     |
| largura de banda      | O número total máximo de quilobytes permitido durante o intervalo de tempo especificado no `renewal-period` . | Ou `calls` , ou ambos `bandwidth` juntos devem ser especificados. | N/D     |
| chama          | O número máximo total de chamadas permitidas durante o intervalo de tempo especificado no `renewal-period` .     | Ou `calls` , ou ambos `bandwidth` juntos devem ser especificados. | N/D     |
| período de renovação | O período de tempo em segundos após o qual a quota se repõe.                                                  | Yes                                                              | N/D     |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada
-   **Âmbitos de política:** produto

## <a name="set-usage-quota-by-key"></a><a name="SetUsageQuotaByKey"></a> Definir quota de utilização por chave

> [!IMPORTANT]
> Esta funcionalidade não está disponível no nível de **Consumo** da Gestão da API.

A `quota-by-key` política aplica, por título, um volume de chamadas renováveis ou vitalícios e/ou uma quota de largura de banda. A chave pode ter um valor de cadeia arbitrária e é normalmente fornecida usando uma expressão política. A condição de incremento opcional pode ser adicionada para especificar quais os pedidos que devem ser contabilizados para o contingente. Se várias políticas incrementar o mesmo valor-chave, é incrementada apenas uma vez por pedido. Quando o limite de chamada é atingido, o chamador recebe um `403 Forbidden` código de estado de resposta.

Para obter mais informações e exemplos desta política, consulte [o pedido avançado de estrangulamento com a Azure API Management](./api-management-sample-flexible-throttling.md).

> [!NOTE]
> Para compreender a diferença entre limites de taxa e quotas, [consulte limites de taxa e quotas.](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

### <a name="policy-statement"></a>Declaração política

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### <a name="example"></a>Exemplo

No exemplo seguinte, a quota é chaveda pelo endereço IP do autor da chamada.

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

| Nome  | Descrição   | Obrigatório |
| ----- | ------------- | -------- |
| quota | Elemento de raiz. | Yes      |

### <a name="attributes"></a>Atributos

| Nome                | Descrição                                                                                               | Obrigatório                                                         | Predefinição |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| largura de banda           | O número total máximo de quilobytes permitido durante o intervalo de tempo especificado no `renewal-period` . | Ou `calls` , ou ambos `bandwidth` juntos devem ser especificados. | N/D     |
| chama               | O número máximo total de chamadas permitidas durante o intervalo de tempo especificado no `renewal-period` .     | Ou `calls` , ou ambos `bandwidth` juntos devem ser especificados. | N/D     |
| contra-chave         | A chave a utilizar para a política de quotas.                                                                      | Yes                                                              | N/D     |
| incremento condição | A expressão booleana especificando se o pedido deve ser contado para a quota `true` ()             | No                                                               | N/D     |
| período de renovação      | O período de tempo em segundos após o qual a quota se repõe.                                                  | Yes                                                              | N/D     |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada
-   **Âmbitos de política:** todos os âmbitos

## <a name="validate-jwt"></a><a name="ValidateJWT"></a> Validar JWT

A `validate-jwt` política impõe a existência e a validade de um JWT extraído de um cabeçalho HTTP especificado ou de um parâmetro de consulta especificado.

> [!IMPORTANT]
> A `validate-jwt` política exige que a `exp` reclamação registada esteja incluída no token JWT, a menos que o `require-expiration-time` atributo seja especificado e definido para `false` .
> A `validate-jwt` política suporta algoritmos de assinatura HS256 e RS256. Para o HS256, a chave deve ser fornecida em linha dentro da política na forma codificada base64. Para o RS256, a chave tem de ser disponibilizada através de um ponto final de configuração Open ID.
> A `validate-jwt` política suporta fichas encriptadas com chaves simétricas utilizando os seguintes algoritmos de encriptação A128CBC-HS256, A192CBC-HS384, A256CBC-HS512.

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

#### <a name="simple-token-validation"></a>Validação simples do símbolo

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

#### <a name="azure-active-directory-token-validation"></a>Validação do token do Azure Ative Directory

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

#### <a name="azure-active-directory-b2c-token-validation"></a>Validação do símbolo do Azure Ative Directory B2C

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

Este exemplo mostra como utilizar a política [validada do JWT](api-management-access-restriction-policies.md#ValidateJWT) para autorizar o acesso a operações com base no valor das reclamações simbólicas.

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

| Elemento             | Descrição                                                                                                                                                                                                                                                                                                                                           | Obrigatório |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validar-jwt        | Elemento de raiz.                                                                                                                                                                                                                                                                                                                                         | Yes      |
| públicos           | Contém uma lista de reclamações aceitáveis de audiência que podem estar presentes no token. Se estiverem presentes vários valores de audiência, cada valor é tentado até que todos estejam esgotados (caso em que a validação falha) ou até que um tenha sucesso. Pelo menos uma audiência deve ser especificada.                                                                     | No       |
| emitentes-assinatura-chaves | Uma lista de chaves de segurança codificadas pela Base64 usadas para validar fichas assinadas. Se estiverem presentes várias teclas de segurança, cada tecla é tentada até que todas estejam esgotadas (caso em que a validação falha) ou até que uma delas tenha sucesso (útil para o capotamento do token). Os elementos-chave têm um atributo opcional `id` usado para corresponder à `kid` reclamação.               | No       |
| desencriptação-chaves     | Uma lista de chaves codificadas da Base64 usadas para desencriptar os tokens. Se estiverem presentes várias teclas de segurança, cada tecla é experimenta até que todas as teclas estejam esgotadas (caso em que a validação falha) ou até que uma chave tenha sucesso. Os elementos-chave têm um atributo opcional `id` usado para corresponder à `kid` reclamação.                                                 | No       |
| emitentes             | Uma lista de princípios aceitáveis que emitiu o símbolo. Se estiverem presentes vários valores emitentes, cada valor é tentado até que todos estejam esgotados (caso em que a validação falha) ou até que um tenha sucesso.                                                                                                                                         | No       |
| openid-config       | O elemento utilizado para especificar um ponto final de configuração open id compatível a partir do qual podem ser obtidas teclas de assinatura e emitente.                                                                                                                                                                                                                        | No       |
| reivindicações necessárias     | Contém uma lista de reclamações que se espera estarem presentes no token para que seja considerada válida. Quando o `match` atributo for definido para cada valor de `all` reclamação na apólice deve estar presente no símbolo para que a validação tenha sucesso. Quando o `match` atributo for definido para pelo menos uma `any` reclamação deve estar presente no símbolo para validação ter sucesso. | No       |

### <a name="attributes"></a>Atributos

| Nome                            | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                                            | Obrigatório                                                                         | Predefinição                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| relógio-distorcer                      | Tempo. Utilize para especificar a diferença de tempo máxima esperada entre os relógios do sistema do emitente simbólico e a instância de Gestão da API.                                                                                                                                                                                                                                                                                                               | No                                                                               | 0 segundos                                                                         |
| falha-validação-erro-mensagem | Error message to return in the HTTP response body if the JWT does passes validation. Esta mensagem deve ter quaisquer caracteres especiais devidamente escapados.                                                                                                                                                                                                                                                                                                 | No                                                                               | A mensagem de erro por defeito depende de problema de validação, por exemplo "JWT não está presente". |
| código de validação-httpcode falhado      | HTTP Código de estado para retornar se o JWT não passar na validação.                                                                                                                                                                                                                                                                                                                                                                                         | No                                                                               | 401                                                                               |
| cabeçalho-nome                     | O nome do cabeçalho HTTP segurando o símbolo.                                                                                                                                                                                                                                                                                                                                                                                                         | Um dos `header-name` , ou deve ser `query-parameter-name` `token-value` especificado. | N/D                                                                               |
| consulta-parâmetro-nome            | O nome do parâmetro de consulta que mantém o símbolo.                                                                                                                                                                                                                                                                                                                                                                                                     | Um dos `header-name` , ou deve ser `query-parameter-name` `token-value` especificado. | N/D                                                                               |
| valor simbólico                     | Expressão devolvendo uma corda contendo ficha JWT                                                                                                                                                                                                                                                                                                                                                                                                     | Um dos `header-name` , ou deve ser `query-parameter-name` `token-value` especificado. | N/D                                                                               |
| ID                              | O `id` atributo no elemento permite `key` especificar a cadeia que será correspondida com `kid` a reclamação no token (se presente) para descobrir a chave adequada a usar para validação de assinatura.                                                                                                                                                                                                                                           | No                                                                               | N/D                                                                               |
| combinar                           | O `match` atributo sobre o elemento especifica se cada valor de reclamação na apólice deve estar presente no símbolo para que a `claim` validação tenha sucesso. Os valores possíveis são:<br /><br /> - `all` - todos os valores de reclamação da apólice devem estar presentes no símbolo para que a validação seja bem sucedida.<br /><br /> - `any` - pelo menos um valor de reclamação deve estar presente no símbolo para que a validação tenha êxito.                                                       | No                                                                               | all                                                                               |
| exigir tempo de expiração         | O Boolean. Especifica se é necessário um pedido de caducidade no token.                                                                                                                                                                                                                                                                                                                                                                               | No                                                                               | true                                                                              |
| exigir esquema                  | O nome do esquema simbólico, por exemplo, "Portador". Quando este atributo for definido, a política garantirá que o regime especificado esteja presente no valor do cabeçalho de autorização.                                                                                                                                                                                                                                                                                    | No                                                                               | N/D                                                                               |
| exigir fichas assinadas           | O Boolean. Especifica se um símbolo é necessário para ser assinado.                                                                                                                                                                                                                                                                                                                                                                                           | No                                                                               | true                                                                              |
| separador                       | Cadeia. Especifica um separador (por exemplo, "") a utilizar para extrair um conjunto de valores de uma reivindicação multivalores.                                                                                                                                                                                                                                                                                                                                          | No                                                                               | N/D                                                                               |
| url                             | Open ID configuração URL de onde podem ser obtidos metadados de configuração de ID aberto. A resposta deve ser de acordo com as especificações definidas na URL: `https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata` . Para o Azure Ative Directory utilize o seguinte URL: `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` substituindo o nome do seu inquilino de diretório, por `contoso.onmicrosoft.com` exemplo. | Yes                                                                              | N/D                                                                               |
| nome variável-ficha de saída      | Cadeia. Nome da variável de contexto que receberá valor simbólico como objeto de tipo [`Jwt`](api-management-policy-expressions.md) após validação de símbolos bem sucedido                                                                                                                                                                                                                                                                                     | No                                                                               | N/D                                                                               |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada
-   **Âmbitos de política:** todos os âmbitos

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as políticas, consulte:

-   [Políticas em Gestão de API](api-management-howto-policies.md)
-   [Transformar APIs](transform-api.md)
-   [Referência política](./api-management-policies.md) para uma lista completa de declarações políticas e suas definições
-   [Amostras de política](./policy-reference.md)