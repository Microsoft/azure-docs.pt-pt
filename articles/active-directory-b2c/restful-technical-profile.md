---
title: Definir um perfil técnico RESTful em uma política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico RESTful em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: edad748bc2192f98b9674b80dada5b03aa9ee2d1
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77197991"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico RESTful em uma política personalizada de Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory B2C (Azure AD B2C) fornece suporte para seu próprio serviço RESTful. Azure AD B2C envia dados para o serviço RESTful em uma coleção de declarações de entrada e recebe dados de volta em uma coleção de declarações de saída. Com integração de serviçoreres RESTful, pode:

- **Validar os dados** de entrada do utilizador - Evita que os dados mal formados persistam no Azure AD B2C. Se o valor do utilizador não for válido, o seu serviço RESTful devolve uma mensagem de erro que instrui o utilizador a fornecer uma entrada. Por exemplo, pode verificar se o endereço de e-mail fornecido pelo utilizador existe na base de dados do seu cliente.
- **Sobrepor as reclamações** de entrada - Permite-lhe reformar valores em reivindicações de entrada. Por exemplo, se um usuário inserir o primeiro nome em letras minúsculas ou maiúsculas, você poderá formatar o nome somente com a primeira letra em maiúscula.
- **Enriquecer os dados dos utilizadores** - Permite-lhe integrar-se ainda mais com aplicações de linha de negócio corporativas. Por exemplo, o serviço RESTful pode receber o endereço de email do usuário, consultar o banco de dados do cliente e retornar o número de fidelidade do usuário para Azure AD B2C. As declarações de retorno podem ser armazenadas, avaliadas nas próximas etapas de orquestração ou incluídas no token de acesso.
- **Execute a lógica de negócio personalizada** - Permite-lhe enviar notificações push, atualizar bases de dados corporativas, executar um processo de migração de utilizadores, gerir permissões, auditar bases de dados e realizar outras ações.

Sua política pode enviar declarações de entrada para sua API REST. A API REST também pode retornar declarações de saída que você pode usar posteriormente em sua política ou pode gerar uma mensagem de erro. Pode projetar a integração com os serviços RESTful das seguintes formas:

- **Perfil técnico de validação** - Um perfil técnico de validação chama o serviço RESTful. O perfil técnico de validação valida os dados fornecidos pelo utilizador antes da viagem do utilizador continuar. Com o perfil técnico de validação, uma mensagem de erro é exibida em uma página autodeclarada e retornada em declarações de saída.
- **Troca de reclamações** - É feita uma chamada ao serviço RESTful através de um passo de orquestração. Nesse cenário, não há nenhuma interface de usuário para processar a mensagem de erro. Se a API REST retornar um erro, o usuário será Redirecionado de volta para o aplicativo de terceira parte confiável com a mensagem de erro.

## <a name="protocol"></a>Protocolo

O **nome** atributo do elemento **protocolo** tem de ser definido para `Proprietary`. O atributo do **manipulador** deve conter o nome totalmente qualificado do conjunto de manipulador esprotocolo utilizado pelo Azure AD B2C: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

O exemplo a seguir mostra um perfil técnico RESTful:

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>Reclamações de entrada

O elemento **InputClaims** contém uma lista de reclamações a enviar para a API REST. Você também pode mapear o nome da sua declaração para o nome definido na API REST. O exemplo a seguir mostra o mapeamento entre sua política e a API REST. A reclamação **do nome dado** é enviada para a API REST como **primeiro nome,** enquanto o **sobrenome** é enviado como **apelido**. A reclamação de **e-mail** está definida como está.

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

O elemento **OutputClaims** contém uma lista de reclamações devolvidas pela API REST. Talvez seja necessário mapear o nome da declaração definida em sua política para o nome definido na API REST. Também pode incluir reclamações que não são devolvidas pelo fornecedor de identidade REST API, desde que detetete o `DefaultValue` atributo.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **outputClaimsTransformation** que são usados para modificar as reclamações de saída ou gerar novos.

O exemplo a seguir mostra a declaração retornada pela API REST:

- O **MembershipId** alega que está mapeado para o nome de reivindicação **de número de fidelidade.**

O perfil técnico também retorna declarações que não são retornadas pelo provedor de identidade:

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
| ServiceUrl | Sim | A URL do ponto de extremidade da API REST. |
| AuthenticationType | Sim | O tipo de autenticação que está sendo executada pelo provedor de declarações RESTful. Valores possíveis: `None`, `Basic`, `Bearer`ou `ClientCertificate`. O valor `None` indica que a API REST não é anónima. O valor `Basic` indica que a API REST está segura com autenticação básica HTTP. Somente os usuários verificados, incluindo Azure AD B2C, podem acessar sua API. O valor `ClientCertificate` (recomendado) indica que a API REST restringe o acesso através da autenticação do certificado de cliente. Apenas os serviços que possuam os certificados apropriados, por exemplo, o Azure AD B2C, podem aceder à sua API. O valor `Bearer` indica que a API REST restringe o acesso utilizando o token do cliente OAuth2 Bearer. |
| SendClaimsIn | Não | Especifica como as declarações de entrada são enviadas para o provedor de declarações RESTful. Valores possíveis: `Body` (predefinido), `Form`, `Header`ou `QueryString`. O valor `Body` é a reclamação de entrada que é enviada no organismo de pedido em formato JSON. O valor `Form` é a alegação de entrada que é enviada no organismo de pedido num formato de valor-chave separado ampersand '&'. O valor `Header` é a reclamação de entrada que é enviada no cabeçalho do pedido. O valor `QueryString` é a alegação de entrada que é enviada na cadeia de consulta de pedido. Os verbos HTTP invocados por cada um são os seguintes:<br /><ul><li>`Body`: POST</li><li>`Form`: POST</li><li>`Header`: GET</li><li>`QueryString`: GET</li></ul> |
| ClaimsFormat | Não | Especifica o formato para as declarações de saída. Valores possíveis: `Body` (predefinido), `Form`, `Header`ou `QueryString`. O valor `Body` é a reivindicação de saída que é enviada no organismo de pedido em formato JSON. O valor `Form` é a reivindicação de saída que é enviada no organismo de pedido num formato de valor-chave separado ampersand '&'. O valor `Header` é a reivindicação de saída que é enviada no cabeçalho do pedido. O valor `QueryString` é a reivindicação de saída que é enviada na cadeia de consulta de pedido. |
| ClaimUsedForRequestPayload| Não | Nome de uma reivindicação de cordas que contém a carga útil a enviar para a API REST. |
| DebugMode | Não | Executa o perfil técnico no modo de depuração. Valores possíveis: `true`ou `false` (predefinido). No modo de depuração, a API REST pode retornar mais informações. Consulte a secção de [mensagem de erro de retorno.](#returning-error-message) |
| Incluir Requerer Resolução de Reclamações  | Não | Para pedidos de entrada e saída, especifica se a resolução de [sinistros](claim-resolver-overview.md) está incluída no perfil técnico. Valores possíveis: `true`, ou `false` (predefinido). Se pretender utilizar uma reclamação no perfil técnico, desempente-a para `true`. |

## <a name="cryptographic-keys"></a>Chaves de criptografia

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
| BasicAuthenticationUsername | Sim | O nome de usuário que é usado para autenticar. |
| BasicAuthenticationPassword | Sim | A senha que é usada para autenticar. |

O exemplo a seguir mostra um perfil técnico com autenticação básica:

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
| ClientCertificate | Sim | O certificado X509 (conjunto de chaves RSA) a ser usado para autenticação. |

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

Sua API REST pode precisar retornar uma mensagem de erro, como ' o usuário não foi encontrado no sistema de CRM '. Se ocorrer um erro, a API REST deverá retornar uma mensagem de erro HTTP 409 (código de status de resposta de conflito) com os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| versão | Sim | 1.0.0 |
| status | Sim | 409 |
| code | Não | Um código de erro do fornecedor restful endpoint, que é apresentado quando `DebugMode` está ativado. |
| requestId | Não | Um identificador de pedido do fornecedor restful endpoint, que é apresentado quando `DebugMode` está ativado. |
| userMessage | Sim | Uma mensagem de erro que é mostrada para o usuário. |
| developerMessage | Não | A descrição verbosa do problema e como corrigi-lo, que é exibida quando `DebugMode` está ativada. |
| moreInfo | Não | Um URI que aponta para informações adicionais, que são exibidas quando `DebugMode` está ativada. |

O exemplo a seguir mostra uma API REST que retorna uma mensagem de erro formatada em JSON:

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

O exemplo a seguir mostra C# uma classe que retorna uma mensagem de erro:

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
