---
title: Defina um perfil técnico RESTful numa política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico RESTful numa política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eb6d82019cccd1da327461cb0a0635aea4f3647f
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174976"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico RESTful numa política personalizada do Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) fornece suporte para a integração do seu próprio serviço RESTful. A Azure AD B2C envia dados para o serviço RESTful numa recolha de reclamações de entrada e recebe dados de volta numa recolha de reclamações de saída. Para obter mais informações, consulte [a Integração de reclamações da API na sua política personalizada Azure AD B2C.](custom-policy-rest-api-intro.md)  

## <a name="protocol"></a>Protocolo

O **atributo nome** do elemento **Protocolo** tem de ser definido para `Proprietary` . O **atributo do manipulador** deve conter o nome totalmente qualificado do conjunto de manipuladores de protocolo utilizado pela Azure AD B2C: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null` .

O exemplo a seguir mostra um perfil técnico RESTful:

```xml
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>Reclamações de entrada

O elemento **InputClaims** contém uma lista de reclamações a enviar para a API REST. Também pode mapear o nome da sua reclamação para o nome definido na API REST. O exemplo a seguir mostra o mapeamento entre a sua política e a API REST. A reclamação **dada no Nome** é enviada para a API REST como primeiro **Nome,** enquanto **o apelido** é enviado como **último Nome**. A reclamação **de e-mail** está definida como está.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

O elemento **InputClaimsTransformations** pode conter uma coleção de **elementos inputClaimsTransformation** que são utilizados para modificar as reclamações de entrada ou gerar novos antes de enviar para a API REST.

## <a name="send-a-json-payload"></a>Envie uma carga JSON

O perfil técnico da API REST permite-lhe enviar uma carga complexa json para um ponto final.

Para enviar uma carga complexa json:

1. Construa a sua carga útil JSON com a transformação de reclamações [de GenerateJson.](json-transformations.md)
1. No perfil técnico da API REST:
    1. Adicione uma transformação de reclamações de entrada com uma referência à transformação de `GenerateJson` sinistros.
    1. Descodem a `SendClaimsIn` opção de metadados para `body`
    1. Desaver a `ClaimUsedForRequestPayload` opção de metadados para o nome da reclamação que contém a carga útil JSON.
    1. Na reclamação de entrada, adicione uma referência à reclamação de entrada que contém a carga útil JSON.

O exemplo a seguir `TechnicalProfile` envia um e-mail de verificação utilizando um serviço de e-mail de terceiros (neste caso, SendGrid).

```xml
<TechnicalProfile Id="SendGrid">
  <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
    <Item Key="DefaultUserMessageIfRequestFailed">Cannot process your request right now, please try again later.</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridApiKey" />
  </CryptographicKeys>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="output-claims"></a>Reclamações de saída

O elemento **OutputClaims** contém uma lista de reclamações devolvidas pela API REST. Poderá ser necessário mapear o nome da reclamação definida na sua política para o nome definido na API REST. Também pode incluir reclamações que não são devolvidas pelo provedor de identidade REST API, desde que desemque o `DefaultValue` atributo.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos de **saídaClaimsTransformation** que são utilizados para modificar as alegações de saída ou gerar novos.

O exemplo a seguir mostra a reclamação devolvida pela API REST:

- A **Associação afirma** que está mapeada para o nome de **reivindicação de loyaltyNumber.**

O perfil técnico também devolve reclamações, que não são devolvidas pelo fornecedor de identidade:

- A **fidelidadeNumberIsNew** afirmação que tem um valor padrão definido para `true` .

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ServiceUrl | Sim | O URL do ponto final da API REST. |
| AutenticaçãoType | Sim | O tipo de autenticação que está a ser realizada pelo provedor de reclamações RESTful. Valores possíveis: `None` , , , ou `Basic` `Bearer`  `ClientCertificate` `ApiKeyHeader` . <br /><ul><li>O `None` valor indica que a API REST é anónima. </li><li>O `Basic` valor indica que a API REST está protegida com autenticação básica HTTP. Apenas os utilizadores verificados, incluindo o Azure AD B2C, podem aceder à sua API. </li><li>O `ClientCertificate` valor (recomendado) indica que a API REST restringe o acesso através da autenticação do certificado do cliente. Apenas os serviços que possuam os certificados adequados, por exemplo Azure AD B2C, podem aceder à sua API. </li><li>O `Bearer` valor indica que a API REST restringe o acesso utilizando o token do cliente OAuth2 Bearer. </li><li>O `ApiKeyHeader` valor indica que a API REST está protegida com o cabeçalho HTTP da tecla API, tal como a *tecla x-funções*. </li></ul> |
| AllowInsecureAuthInProduction| Não| Indica se o `AuthenticationType` pode ser definido em ambiente de `none` produção ( da `DeploymentMode` [TrustFrameworkPolicy](trustframeworkpolicy.md) está definido para `Production` , ou não especificado). Valores possíveis: verdadeiros ou falsos (padrão). |
| SendClaimsIn | Não | Especifica como os pedidos de entrada são enviados ao provedor de reclamações RESTful. Valores possíveis: `Body` (padrão), `Form` `Header` ou `Url` `QueryString` . O `Body` valor é a reclamação de entrada que é enviada no organismo de pedido em formato JSON. O `Form` valor é a alegação de entrada que é enviada no organismo de pedido num formato de valor chave separado "&". O `Header` valor é a reclamação de entrada que é enviada no cabeçalho do pedido. O `Url` valor é a alegação de entrada que é enviada no URL, por exemplo, https://{claim1}.exemplo.com/{claim2}/{claim3}?{ claim4}={claim5}. O `QueryString` valor é a reclamação de entrada que é enviada na cadeia de consulta de pedido. Os verbos HTTP invocados por cada um são os seguintes:<br /><ul><li>`Body`: POST</li><li>`Form`: POST</li><li>`Header`: GET</li><li>`Url`: GET</li><li>`QueryString`: GET</li></ul> |
| SinistrosFormat | Não | Não atualmente usado, pode ser ignorado. |
| ReclamaçãoUsedForRequestPayload| Não | Nome de uma alegação de cadeia que contém a carga útil a enviar para a API REST. |
| DebugMode | Não | Executa o perfil técnico no modo depuragem. Valores possíveis: `true` , ou `false` (predefinição). No modo de depuragem, a API REST pode devolver mais informações. Consulte a secção [de mensagem de erro de retorno.](#returning-validation-error-message) |
| IncluirClaimResolvingInClaimsHandling  | Não | Para pedidos de entradas e saídas, especifica se a [resolução de sinistros](claim-resolver-overview.md) está incluída no perfil técnico. Valores possíveis: `true` , ou `false` (predefinição). Se pretender utilizar uma reclamação no perfil técnico, desa um pouco `true` para . |
| ResolveJsonPathsInJsonTokens  | Não | Indica se o perfil técnico resolve os caminhos do JSON. Valores possíveis: `true` , ou `false` (predefinição). Utilize estes metadados para ler dados a partir de um elemento JSON aninhado. Em um [OutputClaim](technicalprofiles.md#output-claims), desapedaça `PartnerClaimType` o elemento de caminho JSON que pretende obter. Por exemplo: `firstName.localized` ou `data.0.to.0.email` . .|
| UseClaimAsBearerToken| Não| O nome da reivindicação que contém o símbolo do portador.|

## <a name="error-handling"></a>Processamento de erros

Os metadados a seguir podem ser utilizados para configurar as mensagens de erro exibidas após a falha da API REST. As mensagens de erro podem ser [localizadas](localization-string-ids.md#restful-service-error-messages).

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| DefaultUserMessageIfRequestFailed | Não | Uma mensagem de erro personalizada por defeito para todas as exceções da API REST.|
| UserMessageIfCircuitOpen | Não | Mensagem de erro quando a API REST não está acessível. Se não for especificado, o DefaultUserMessageIfRequestFailed será devolvido. |
| UserMessageIfDnsResolutionFailed | Não | Mensagem de erro para a exceção de resolução dns. Se não for especificado, o DefaultUserMessageIfRequestFailed será devolvido. | 
| UserMessageIfRequestTimeout | Não | Mensagem de erro quando a ligação é cronometrada. Se não for especificado, o DefaultUserMessageIfRequestFailed será devolvido. | 

## <a name="cryptographic-keys"></a>Chaves criptográficas

Se o tipo de autenticação estiver `None` definido, o elemento **CryptographicKeys** não é utilizado.

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
</TechnicalProfile>
```

Se o tipo de autenticação estiver definido `Basic` para, o elemento **CryptographicKeys** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | Sim | O nome de utilizador que é usado para autenticar. |
| BásicoAuthenticationPassword | Sim | A palavra-passe que é usada para autenticar. |

O exemplo a seguir mostra um perfil técnico com autenticação básica:

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Basic</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
  </CryptographicKeys>
</TechnicalProfile>
```

Se o tipo de autenticação for definido `ClientCertificate` para, o elemento **CryptographicKeys** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Certificado de Clientes | Sim | O certificado X509 (conjunto de teclas RSA) para utilizar para autenticar. |

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ClientCertificate</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
  </CryptographicKeys>
</TechnicalProfile>
```

Se o tipo de autenticação for definido `Bearer` para, o elemento **CryptographicKeys** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| PortadorAuthenticationToken | Não | O Símbolo do Portador da OAuth 2.0. |

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_B2cRestClientAccessToken" />
  </CryptographicKeys>
</TechnicalProfile>
```

Se o tipo de autenticação for definido `ApiKeyHeader` para, o elemento **CryptographicKeys** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| O nome do cabeçalho HTTP, como `x-functions-key` , ou `x-api-key` . | Sim | A chave que é usada para autenticar. |

> [!NOTE]
> Neste momento, o Azure AD B2C suporta apenas um cabeçalho HTTP para autenticação. Se a sua chamada RESTful necessitar de vários cabeçalhos, como um ID de cliente e segredo de cliente, terá de recorrer ao pedido de alguma forma.

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ApiKeyHeader</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
  </CryptographicKeys>
</TechnicalProfile>
```

## <a name="returning-validation-error-message"></a>Mensagem de erro de validação de retorno

A API rest pode ter de devolver uma mensagem de erro, tal como "O utilizador não foi encontrado no sistema CRM". Se ocorrer um erro, a API REST deve devolver uma mensagem de erro HTTP 4xx, como, por exemplo, 400 (mau pedido) ou 409 código de estado de resposta (conflito). O organismo de resposta contém uma mensagem de erro formatada em JSON:

```json
{
  "version": "1.0.0",
  "status": 409,
  "code": "API12345",
  "requestId": "50f0bd91-2ff4-4b8f-828f-00f170519ddb",
  "userMessage": "Message for the user",
  "developerMessage": "Verbose description of problem and how to fix it.",
  "moreInfo": "https://restapi/error/API12345/moreinfo"
}
```

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| versão | Sim | A sua versão REST API. Por exemplo: 1.0.1 |
| status | Sim | Deve ser 409. |
| code | Não | Um código de erro do provedor de pontos finais RESTful, que é apresentado quando `DebugMode` está ativado. |
| requestId | Não | Um identificador de pedido do provedor de pontos finais RESTful, que é apresentado quando `DebugMode` está ativado. |
| userMessage | Sim | Uma mensagem de erro que é mostrada ao utilizador. |
| developerMessage | Não | A descrição verbosa do problema e como corrigi-lo, que é exibido quando `DebugMode` está ativado. |
| moreInfo | Não | Um URI que aponta para informações adicionais, que são exibidas quando `DebugMode` estão ativadas. |


O exemplo a seguir mostra uma classe C# que devolve uma mensagem de erro:

```csharp
public class ResponseContent
{
  public string version { get; set; }
  public int status { get; set; }
  public string code { get; set; }
  public string userMessage { get; set; }
  public string developerMessage { get; set; }
  public string requestId { get; set; }
  public string moreInfo { get; set; }
}
```

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para exemplos de utilização de um perfil técnico RESTful:

- [Integrar as bolsas de reclamações da REST API na sua política personalizada Azure AD B2C](custom-policy-rest-api-intro.md)
- [Walkthrough: Integrar as trocas de reclamações da API REST na sua viagem de utilizador Azure AD B2C como validação da entrada do utilizador](custom-policy-rest-api-claims-validation.md)
- [Walkthrough: Add REST API reclama trocas de trocas para políticas personalizadas no Azure Ative Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Proteja os seus serviços REST API](secure-rest-api.md)
