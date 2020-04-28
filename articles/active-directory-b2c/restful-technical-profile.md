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
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 410f413fc8450c0ee33c3ca95e860a3e8de34107
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80332615"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico RESTful numa política personalizada do Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) fornece suporte para integrar o seu próprio serviço RESTful. O Azure AD B2C envia dados para o serviço RESTful numa recolha de créditos de entrada e recebe dados de volta numa recolha de reclamações de saída. Para mais informações, consulte Integrar a [API reclama trocas na sua política personalizada Azure AD B2C](custom-policy-rest-api-intro.md).  

## <a name="protocol"></a>Protocolo

O **atributo** nome do elemento **protocolo** `Proprietary`tem de ser definido para . O atributo do **manipulador** deve conter o nome totalmente qualificado do conjunto de `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`manipulador esprotocolo utilizado pelo Azure AD B2C: .

O exemplo que se segue mostra um perfil técnico RESTful:

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>Reclamações de entrada

O elemento **InputClaims** contém uma lista de reclamações a enviar para a API REST. Também pode mapear o nome da sua reclamação para o nome definido na API REST. Seguir o exemplo mostra o mapeamento entre a sua política e a API REST. A reclamação **do nome dado** é enviada para a API REST como **primeiro nome,** enquanto o **sobrenome** é enviado como **apelido**. A reclamação de **e-mail** está definida como está.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos **de transformação inputClaims** que são usados para modificar as reclamações de entrada ou gerar novos antes de enviar para a API REST.

## <a name="send-a-json-payload"></a>Envie uma carga útil JSON

O perfil técnico REST API permite-lhe enviar uma carga útil complexa da JSON para um ponto final.

Para enviar uma carga útil complexa da JSON:

1. Construa a sua carga útil JSON com a transformação de reivindicações [Da GenerateJson.](json-transformations.md)
1. No perfil técnico rest API:
    1. Adicione uma transformação de créditos `GenerateJson` de entrada com uma referência à transformação de sinistros.
    1. Definir `SendClaimsIn` a opção de metadados para`body`
    1. Detete a opção `ClaimUsedForRequestPayload` metadados para o nome da reclamação que contém a carga útil JSON.
    1. Na alegação de entrada, adicione uma referência à alegação de entrada que contém a carga útil JSON.

O exemplo `TechnicalProfile` seguinte envia um e-mail de verificação utilizando um serviço de e-mail de terceiros (neste caso, SendGrid).

```XML
<TechnicalProfile Id="SendGrid">
  <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
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

## <a name="output-claims"></a>Reclamações de produção

O elemento **OutputClaims** contém uma lista de reclamações devolvidas pela API REST. Poderá ter de mapear o nome da reclamação definida na sua política para o nome definido na API REST. Também pode incluir reclamações que não são devolvidas pelo fornecedor de `DefaultValue` identidade REST API, desde que detete te deteta o atributo.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **outputClaimsTransformation** que são usados para modificar as reclamações de saída ou gerar novos.

O exemplo que se segue mostra a reclamação devolvida pela API REST:

- O **MembershipId** alega que está mapeado para o nome de reivindicação **de número de fidelidade.**

O perfil técnico também devolve reclamações, que não são devolvidas pelo fornecedor de identidade:

- A **alegação de fidelidadeNumberIsNew** que `true`tem um valor padrão definido para .

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metadados

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ServiceUrl | Sim | O URL do ponto final da API REST. |
| AutenticaçãoTipo | Sim | O tipo de autenticação que está a ser efetuada pelo prestador de sinistros RESTful. Valores `None`possíveis: , `Basic`, `Bearer`ou `ClientCertificate`. O `None` valor indica que a API REST não é anónima. O `Basic` valor indica que a API REST está segura com autenticação básica HTTP. Apenas utilizadores verificados, incluindo O Azure AD B2C, podem aceder à sua API. O `ClientCertificate` valor (recomendado) indica que a API REST restringe o acesso através da autenticação do certificado de cliente. Apenas os serviços que possuam os certificados apropriados, por exemplo, o Azure AD B2C, podem aceder à sua API. O `Bearer` valor indica que a API REST restringe o acesso utilizando o token do cliente OAuth2 Bearer. |
| AllowInsecureAuthInProduction| Não| Indica se `AuthenticationType` a pode `none` ser definida`DeploymentMode` em ambiente de produção `Production`(da [TrustFrameworkPolicy](trustframeworkpolicy.md) está definida para, ou não especificada). Valores possíveis: verdadeiros ou falsos (padrão). |
| SendClaimsIn | Não | Especifica como os pedidos de entrada são enviados para o fornecedor de sinistros RESTful. Valores `Body` possíveis: `Form` `Header`(padrão), ou `QueryString`. O `Body` valor é a reclamação de entrada que é enviada no organismo de pedido em formato JSON. O `Form` valor é a alegação de entrada que é enviada no organismo de pedido num formato de valor-chave separado amperse '&'. O `Header` valor é a reclamação de entrada que é enviada no cabeçalho do pedido. O `QueryString` valor é a alegação de entrada que é enviada na cadeia de consulta de pedido. Os verbos HTTP invocados por cada um são os seguintes:<br /><ul><li>`Body`: POST</li><li>`Form`: POST</li><li>`Header`: GET</li><li>`QueryString`: GET</li></ul> |
| Formato sinistro | Não | Não usado atualmente, pode ser ignorado. |
| ClaimUsedForRequestPayload| Não | Nome de uma reivindicação de cordas que contém a carga útil a enviar para a API REST. |
| DebugMode | Não | Executa o perfil técnico no modo de depuração. Valores `true`possíveis: ou `false` (padrão). No modo de depuração, a API REST pode devolver mais informações. Consulte a secção de [mensagem de erro de retorno.](#returning-error-message) |
| Incluir Requerer Resolução de Reclamações  | Não | Para pedidos de entrada e saída, especifica se a resolução de [sinistros](claim-resolver-overview.md) está incluída no perfil técnico. Valores `true`possíveis: ou `false`  (padrão). Se pretender utilizar uma reclamação no perfil técnico, desempente-a para `true`. |
| ResolveJsonPathsInJsonTokens  | Não | Indica se o perfil técnico resolve os caminhos da JSON. Valores `true`possíveis: ou `false` (padrão). Utilize estes metadados para ler dados de um elemento JSON aninhado. Num [OutputClaim,](technicalprofiles.md#outputclaims)detete o `PartnerClaimType` elemento de caminho JSON que pretende ser de si. Por exemplo: `firstName.localized` `data.0.to.0.email`ou .|
| UseClaimAsBearerToken| Não| O nome da alegação que contém o símbolo do portador.|

## <a name="cryptographic-keys"></a>Chaves criptográficas

Se o tipo de autenticação estiver `None`definido, o elemento **CryptographicKeys** não é utilizado.

```XML
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

Se o tipo de autenticação estiver `Basic`definido, o elemento **CryptographicKeys** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Nome de utilizador basicAuthenticationUsername | Sim | O nome de utilizador que é usado para autenticar. |
| BasicAuthenticationPassword | Sim | A palavra-passe que é usada para autenticar. |

O exemplo seguinte mostra um perfil técnico com autenticação básica:

```XML
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

Se o tipo de autenticação estiver definido, `ClientCertificate`o elemento **CryptographicKeys** contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Certificado de Cliente | Sim | O certificado X509 (conjunto de teclas RSA) para utilizar para autenticar. |

```XML
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

Se o tipo de autenticação estiver definido, `Bearer`o elemento **CryptographicKeys** contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Autenticação do PortadorToken | Não | O OAuth 2.0 Bearer Token. |

```XML
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

## <a name="returning-error-message"></a>Mensagem de erro de retorno

A Sua API REST poderá ter de devolver uma mensagem de erro, como "O utilizador não foi encontrado no sistema CRM". Se ocorrer um erro, a API REST deve devolver uma mensagem de erro HTTP 4xx, como, 400 (pedido mau) ou 409 (código de situação de resposta de conflito). O corpo de resposta contém uma mensagem de erro formatada em JSON:

```JSON
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

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| versão | Sim | A sua versão REST API. Por exemplo: 1.0.1 |
| status | Sim | Deve ser 409. |
| code | Não | Um código de erro do fornecedor restful endpoint, que é apresentado quando `DebugMode` está ativado. |
| requestId | Não | Um identificador de pedido do fornecedor restful endpoint, que é apresentado quando `DebugMode` está ativado. |
| userMessage | Sim | Uma mensagem de erro que é mostrada ao utilizador. |
| desenvolvedorMensagem | Não | A descrição verbosa do problema e como corrigi-lo, que é exibida quando `DebugMode` está ativada. |
| maisInfo | Não | Um URI que aponta para informações `DebugMode` adicionais, que são exibidas quando estão ativadas. |


O exemplo seguinte mostra uma classe C# que devolve uma mensagem de erro:

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

Consulte os seguintes artigos, por exemplo, utilizando um perfil técnico RESTful:

- [Integrar rest API reclama trocas de reclamações na sua política personalizada Azure AD B2C](custom-policy-rest-api-intro.md)
- [Walkthrough: Integrar rest API reclama trocas de créditos na sua viagem de utilizador Azure AD B2C como validação da entrada do utilizador](custom-policy-rest-api-claims-validation.md)
- [Walkthrough: Adicionar REST API reclama trocas a políticas personalizadas no Diretório Ativo Azure B2C](custom-policy-rest-api-claims-validation.md)
- [Proteja os seus serviços REST API](secure-rest-api.md)

