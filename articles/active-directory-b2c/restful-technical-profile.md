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
ms.date: 03/02/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b24a08ee0367cc3dbb1c845854a0fbc91e0f1d2c
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2020
ms.locfileid: "78227080"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico RESTful numa política personalizada do Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) fornece suporte para o seu próprio serviço RESTful. O Azure AD B2C envia dados para o serviço RESTful numa recolha de créditos de entrada e recebe dados de volta numa recolha de reclamações de saída. Com integração de serviçoreres RESTful, pode:

- **Validar os dados** de entrada do utilizador - Evita que os dados mal formados persistam no Azure AD B2C. Se o valor do utilizador não for válido, o seu serviço RESTful devolve uma mensagem de erro que instrui o utilizador a fornecer uma entrada. Por exemplo, pode verificar se o endereço de e-mail fornecido pelo utilizador existe na base de dados do seu cliente.
- **Sobrepor as reclamações** de entrada - Permite-lhe reformar valores em reivindicações de entrada. Por exemplo, se um utilizador introduzir o primeiro nome em todas as letras minúsculas ou maiúsculas, pode formatar o nome apenas com a primeira letra capitalizada.
- **Enriquecer os dados dos utilizadores** - Permite-lhe integrar-se ainda mais com aplicações de linha de negócio corporativas. Por exemplo, o seu serviço RESTful pode receber o endereço de e-mail do utilizador, consultar a base de dados do cliente e devolver o número de fidelização do utilizador ao Azure AD B2C. As reclamações de devolução podem ser armazenadas, avaliadas nas próximas Etapas de Orquestração, ou incluídas no token de acesso.
- **Execute a lógica de negócio personalizada** - Permite-lhe enviar notificações push, atualizar bases de dados corporativas, executar um processo de migração de utilizadores, gerir permissões, auditar bases de dados e realizar outras ações.

A sua política pode enviar reclamações de entrada para a sua API REST. A API REST também pode devolver alegações de saída que pode utilizar mais tarde na sua política, ou pode lançar uma mensagem de erro. Pode projetar a integração com os serviços RESTful das seguintes formas:

- **Perfil técnico de validação** - Um perfil técnico de validação chama o serviço RESTful. O perfil técnico de validação valida os dados fornecidos pelo utilizador antes da viagem do utilizador continuar. Com o perfil técnico de validação, uma mensagem de erro é exibida numa página autoafirmada e devolvida em reclamações de saída.
- **Troca de reclamações** - É feita uma chamada ao serviço RESTful através de um passo de orquestração. Neste cenário, não existe interface de utilizador para renderizar a mensagem de erro. Se a Sua API REST devolver um erro, o utilizador é redirecionado para a aplicação da parte de fideira com a mensagem de erro.

## <a name="protocol"></a>Protocolo

O **nome** atributo do elemento **protocolo** tem de ser definido para `Proprietary`. O atributo do **manipulador** deve conter o nome totalmente qualificado do conjunto de manipulador esprotocolo utilizado pelo Azure AD B2C: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

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
    1. Adicione uma transformação de créditos de entrada com uma referência à transformação de reivindicações `GenerateJson`.
    1. Detete a opção de metadados `SendClaimsIn` para `body`
    1. Detete a opção de metadados `ClaimUsedForRequestPayload` para o nome da reclamação que contém a carga útil JSON.
    1. Na alegação de entrada, adicione uma referência à alegação de entrada que contém a carga útil JSON.

O exemplo seguinte `TechnicalProfile` envia um e-mail de verificação utilizando um serviço de e-mail de terceiros (neste caso, SendGrid).

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

O elemento **OutputClaims** contém uma lista de reclamações devolvidas pela API REST. Poderá ter de mapear o nome da reclamação definida na sua política para o nome definido na API REST. Também pode incluir reclamações que não são devolvidas pelo fornecedor de identidade REST API, desde que detetete o `DefaultValue` atributo.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **outputClaimsTransformation** que são usados para modificar as reclamações de saída ou gerar novos.

O exemplo que se segue mostra a reclamação devolvida pela API REST:

- O **MembershipId** alega que está mapeado para o nome de reivindicação **de número de fidelidade.**

O perfil técnico também devolve reclamações, que não são devolvidas pelo fornecedor de identidade:

- A **alegação de fidelidadeNumberIsNew** que tem um valor padrão definido para `true`.

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
| AutenticaçãoTipo | Sim | O tipo de autenticação que está a ser efetuada pelo prestador de sinistros RESTful. Valores possíveis: `None`, `Basic`, `Bearer`ou `ClientCertificate`. O valor `None` indica que a API REST não é anónima. O valor `Basic` indica que a API REST está segura com autenticação básica HTTP. Apenas utilizadores verificados, incluindo O Azure AD B2C, podem aceder à sua API. O valor `ClientCertificate` (recomendado) indica que a API REST restringe o acesso através da autenticação do certificado de cliente. Apenas os serviços que possuam os certificados apropriados, por exemplo, o Azure AD B2C, podem aceder à sua API. O valor `Bearer` indica que a API REST restringe o acesso utilizando o token do cliente OAuth2 Bearer. |
| AllowInsecureAuthInProduction| Não| Indica se o `AuthenticationType` pode ser definido para `none` em ambiente de produção (`DeploymentMode` da [TrustFrameworkPolicy](trustframeworkpolicy.md) está definida para `Production`, ou não especificado). Valores possíveis: verdadeiros ou falsos (padrão). |
| SendClaimsIn | Não | Especifica como os pedidos de entrada são enviados para o fornecedor de sinistros RESTful. Valores possíveis: `Body` (predefinido), `Form`, `Header`ou `QueryString`. O valor `Body` é a reclamação de entrada que é enviada no organismo de pedido em formato JSON. O valor `Form` é a alegação de entrada que é enviada no organismo de pedido num formato de valor-chave separado ampersand '&'. O valor `Header` é a reclamação de entrada que é enviada no cabeçalho do pedido. O valor `QueryString` é a alegação de entrada que é enviada na cadeia de consulta de pedido. Os verbos HTTP invocados por cada um são os seguintes:<br /><ul><li>`Body`: POST</li><li>`Form`: POST</li><li>`Header`: GET</li><li>`QueryString`: GET</li></ul> |
| Formato sinistro | Não | Especifica o formato para as reclamações de saída. Valores possíveis: `Body` (predefinido), `Form`, `Header`ou `QueryString`. O valor `Body` é a reivindicação de saída que é enviada no organismo de pedido em formato JSON. O valor `Form` é a reivindicação de saída que é enviada no organismo de pedido num formato de valor-chave separado ampersand '&'. O valor `Header` é a reivindicação de saída que é enviada no cabeçalho do pedido. O valor `QueryString` é a reivindicação de saída que é enviada na cadeia de consulta de pedido. |
| ClaimUsedForRequestPayload| Não | Nome de uma reivindicação de cordas que contém a carga útil a enviar para a API REST. |
| DebugMode | Não | Executa o perfil técnico no modo de depuração. Valores possíveis: `true`ou `false` (predefinido). No modo de depuração, a API REST pode devolver mais informações. Consulte a secção de [mensagem de erro de retorno.](#returning-error-message) |
| Incluir Requerer Resolução de Reclamações  | Não | Para pedidos de entrada e saída, especifica se a resolução de [sinistros](claim-resolver-overview.md) está incluída no perfil técnico. Valores possíveis: `true`, ou `false` (predefinido). Se pretender utilizar uma reclamação no perfil técnico, desempente-a para `true`. |
| ResolveJsonPathsInJsonTokens  | Não | Indica se o perfil técnico resolve os caminhos da JSON. Valores possíveis: `true`ou `false` (predefinido). Utilize estes metadados para ler dados de um elemento JSON aninhado. Num [OutputClaim,](technicalprofiles.md#outputclaims)detete a `PartnerClaimType` para o elemento de caminho JSON que pretende ser saída. Por exemplo: `firstName.localized`, ou `data.0.to.0.email`.|

## <a name="cryptographic-keys"></a>Chaves criptográficas

Se o tipo de autenticação estiver definido para `None`, o elemento **CryptographicKeys** não é utilizado.

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

Se o tipo de autenticação for definido para `Basic`, o elemento **CryptographicKeys** contém os seguintes atributos:

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

Se o tipo de autenticação for definido para `ClientCertificate`, o elemento **CryptographicKeys** contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ClientCertificate | Sim | O certificado X509 (conjunto de teclas RSA) para utilizar para autenticar. |

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

Se o tipo de autenticação for definido para `Bearer`, o elemento **CryptographicKeys** contém o seguinte atributo:

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

A Sua API REST poderá ter de devolver uma mensagem de erro, como "O utilizador não foi encontrado no sistema CRM". Se ocorrer um erro, a API REST deve devolver uma mensagem de erro HTTP 409 (código de estado de resposta a conflitos) com seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| version | Sim | 1.0.0 |
| status | Sim | 409 |
| code | Não | Um código de erro do fornecedor restful endpoint, que é apresentado quando `DebugMode` está ativado. |
| requestId | Não | Um identificador de pedido do fornecedor restful endpoint, que é apresentado quando `DebugMode` está ativado. |
| userMessage | Sim | Uma mensagem de erro que é mostrada ao utilizador. |
| developerMessage | Não | A descrição verbosa do problema e como corrigi-lo, que é exibida quando `DebugMode` está ativada. |
| moreInfo | Não | Um URI que aponta para informações adicionais, que são exibidas quando `DebugMode` está ativada. |

O exemplo seguinte mostra uma API REST que devolve uma mensagem de erro formatada em JSON:

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

O exemplo seguinte C# mostra uma classe que devolve uma mensagem de erro:

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

- [Integrar as trocas de reclamações da Rest API na sua viagem de utilizador Azure AD B2C como validação da entrada do utilizador](rest-api-claims-exchange-dotnet.md)
- [Proteja os seus serviços RESTful utilizando a autenticação básica HTTP](secure-rest-api-dotnet-basic-auth.md)
- [Proteja o seu serviço RESTful utilizando certificados de cliente](secure-rest-api-dotnet-certificate-auth.md)
- [Walkthrough: Integrar rest API reclama trocas de créditos na sua viagem de utilizador Azure AD B2C como validação na entrada do utilizador](custom-policy-rest-api-claims-validation.md)
