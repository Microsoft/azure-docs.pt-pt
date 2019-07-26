---
title: Definir um perfil técnico RESTful em uma política personalizada no Azure Active Directory B2C | Microsoft Docs
description: Defina um perfil técnico RESTful em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f535bc7d67198b3fe06326260bc1910b6afd36f2
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68346766"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico RESTful em uma política personalizada de Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory (Azure AD) B2C fornece suporte para seu próprio serviço RESTful. Azure AD B2C envia dados para o serviço RESTful em uma coleção de declarações de entrada e recebe dados de volta em uma coleção de declarações de saída. Com a integração do serviço RESTful, você pode:

- **Validar dados de entrada do usuário** -impede que dados malformados persistam no Azure ad B2C. Se o valor do usuário não for válido, seu serviço RESTful retornará uma mensagem de erro que instrui o usuário a fornecer uma entrada. Por exemplo, você pode verificar se o endereço de email fornecido pelo usuário existe no banco de dados do cliente.
- **Substituir declarações de entrada** – permite reformatar valores em declarações de entrada. Por exemplo, se um usuário inserir o primeiro nome em letras minúsculas ou maiúsculas, você poderá formatar o nome somente com a primeira letra em maiúscula.
- **Enriquecer dados do usuário** – permite que você integre-se ainda mais com aplicativos corporativos de linha de negócios. Por exemplo, o serviço RESTful pode receber o endereço de email do usuário, consultar o banco de dados do cliente e retornar o número de fidelidade do usuário para Azure AD B2C. As declarações de retorno podem ser armazenadas, avaliadas nas próximas etapas de orquestração ou incluídas no token de acesso.
- **Executar lógica de negócios personalizada** – permite que você envie notificações por push, atualize bancos de dados corporativos, execute um processo de migração do usuário, gerencie permissões, bancos de dados de auditoria e execute outras ações.

Sua política pode enviar declarações de entrada para sua API REST. A API REST também pode retornar declarações de saída que você pode usar posteriormente em sua política ou pode gerar uma mensagem de erro. Você pode criar a integração com os serviços RESTful das seguintes maneiras:

- **Perfil técnico de validação** -um perfil técnico de validação chama o serviço RESTful. O perfil técnico de validação valida os dados fornecidos pelo usuário antes de o percurso do usuário continuar. Com o perfil técnico de validação, uma mensagem de erro é exibida em uma página autodeclarada e retornada em declarações de saída.
- **Troca de declarações** – uma chamada é feita ao serviço RESTful por meio de uma etapa de orquestração. Nesse cenário, não há nenhuma interface de usuário para processar a mensagem de erro. Se a API REST retornar um erro, o usuário será Redirecionado de volta para o aplicativo de terceira parte confiável com a mensagem de erro.

## <a name="protocol"></a>Protocol

O atributo **Name** do elemento **Protocol** precisa ser definido como `Proprietary`. O atributo **Handler** deve conter o nome totalmente qualificado do assembly do manipulador de protocolo que é usado pelo Azure ad B2C `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`:.

O exemplo a seguir mostra um perfil técnico RESTful:

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...    
```

## <a name="input-claims"></a>Declarações de entrada

O elemento **InputClaims** contém uma lista de declarações a serem enviadas para a API REST. Você também pode mapear o nome da sua declaração para o nome definido na API REST. O exemplo a seguir mostra o mapeamento entre sua política e a API REST. A Declaração **fornecida** é enviada para a API REST como **FirstName**, enquanto o **sobrenome** é enviado como **LastName**. A declaração de **email** é definida como está.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos **InputClaimsTransformation** que são usados para modificar as declarações de entrada ou gerar novas antes de enviar para a API REST.

## <a name="output-claims"></a>Declarações de saída

O elemento **OutputClaims** contém uma lista de declarações retornadas pela API REST. Talvez seja necessário mapear o nome da declaração definida em sua política para o nome definido na API REST. Você também pode incluir declarações que não são retornadas pelo provedor de identidade da API REST, desde que você `DefaultValue` defina o atributo.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** que são usados para modificar as declarações de saída ou gerar novas.

O exemplo a seguir mostra a declaração retornada pela API REST:

- A  declaração membershipid que é mapeada para o nome da Declaração **loyaltyNumber** .

O perfil técnico também retorna declarações que não são retornadas pelo provedor de identidade: 

- A Declaração **loyaltyNumberIsNew** que tem um valor padrão definido como `true`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metadados

| Atributo | Requerido | Descrição |
| --------- | -------- | ----------- |
| ServiceUrl | Sim | A URL do ponto de extremidade da API REST. | 
| AuthenticationType | Sim | O tipo de autenticação que está sendo executada pelo provedor de declarações RESTful. Valores possíveis: `None`, `Basic`ou `ClientCertificate`. O `None` valor indica que a API REST não é anônima. O `Basic` valor indica que a API REST é protegida com autenticação básica http. Somente os usuários verificados, incluindo Azure AD B2C, podem acessar sua API. O `ClientCertificate` valor (recomendado) indica que a API REST restringe o acesso usando a autenticação de certificado do cliente. Somente os serviços que têm os certificados apropriados, como Azure AD B2C podem acessar seu serviço. | 
| SendClaimsIn | Não | Especifica como as declarações de entrada são enviadas para o provedor de declarações RESTful. Valores possíveis: `Body` (padrão) `Header`, `Form`, ou `QueryString`. O `Body` valor é a declaração de entrada que é enviada no corpo da solicitação no formato JSON. O `Form` valor é a declaração de entrada que é enviada no corpo da solicitação em um formato de valor de chave separado "&" e comercial. O `Header` valor é a declaração de entrada que é enviada no cabeçalho da solicitação. O `QueryString` valor é a declaração de entrada que é enviada na cadeia de caracteres de consulta de solicitação. | 
| ClaimsFormat | Não | Especifica o formato para as declarações de saída. Valores possíveis: `Body` (padrão) `Header`, `Form`, ou `QueryString`. O `Body` valor é a declaração de saída que é enviada no corpo da solicitação no formato JSON. O `Form` valor é a declaração de saída que é enviada no corpo da solicitação em um formato de valor de chave separado "&" e comercial. O `Header` valor é a declaração de saída que é enviada no cabeçalho da solicitação. O `QueryString` valor é a declaração de saída enviada na cadeia de caracteres de consulta de solicitação. | 
| DebugMode | Não | Executa o perfil técnico no modo de depuração. No modo de depuração, a API REST pode retornar mais informações. Consulte a seção retornando mensagem de erro. | 

## <a name="cryptographic-keys"></a>Chaves de criptografia

Se o tipo de autenticação for definido como `None`, o elemento **CryptographicKeys** não será usado.

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

Se o tipo de autenticação for definido como `Basic`, o elemento **CryptographicKeys** conterá os seguintes atributos:

| Atributo | Requerido | Descrição |
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

Se o tipo de autenticação for definido como `ClientCertificate`, o elemento **CryptographicKeys** conterá o seguinte atributo:

| Atributo | Requerido | Descrição |
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

## <a name="returning-error-message"></a>Retornando mensagem de erro

Sua API REST pode precisar retornar uma mensagem de erro, como ' o usuário não foi encontrado no sistema de CRM '. Em caso de erro, a API REST deve retornar uma mensagem de erro HTTP 409 (código de status de resposta de conflito) com os seguintes atributos:

| Atributo | Requerido | Descrição |
| --------- | -------- | ----------- |
| version | Sim | 1.0.0 | 
| status | Sim | 409 | 
| code | Não | Um código de erro do provedor de ponto de extremidade RESTful, que `DebugMode` é exibido quando o está habilitado. | 
| requestId | Não | Um identificador de solicitação do provedor de ponto de extremidade RESTful, que `DebugMode` é exibido quando o está habilitado. | 
| userMessage | Sim | Uma mensagem de erro que é mostrada para o usuário. | 
| developerMessage | Não | A descrição detalhada do problema e como corrigi-lo, que é exibido quando `DebugMode` o está habilitado. | 
| moreInfo | Não | Um URI que aponta para informações adicionais, que são exibidas quando `DebugMode` o está habilitado. | 

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

## <a name="examples"></a>Exemplos:
- [Integre as trocas de declarações da API REST no percurso do usuário Azure AD B2C como validação da entrada do usuário](active-directory-b2c-custom-rest-api-netfw.md) 
- [Proteger seus serviços RESTful usando a autenticação básica HTTP](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
- [Proteger seu serviço RESTful usando certificados de cliente](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
- [Mostrou Integre as trocas de declarações da API REST no percurso do usuário Azure AD B2C como validação na entrada do usuário](active-directory-b2c-rest-api-validation-custom.md)

 














