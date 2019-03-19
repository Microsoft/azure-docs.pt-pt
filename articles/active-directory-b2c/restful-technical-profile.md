---
title: Definir um perfil técnico RESTful numa política personalizada no Azure Active Directory B2C | Documentos da Microsoft
description: Defina um perfil técnico RESTful numa política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 7ff14af756a55ccc6bbf40dd39d49c5168f4af1f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58076331"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico RESTful em políticas personalizadas do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory (Azure AD) B2C fornece suporte para o seu próprio serviço RESTful. O Azure AD B2C envia dados para o serviço RESTful numa entrada de coleção de afirmações e recebe dados numa coleção de declarações de saída. Com a integração de serviço RESTful, pode:

- **Validar dados de entrada do usuário** -impede dados mal formados de persistência para o Azure AD B2C. Se o valor do usuário não é válido, o seu serviço RESTful devolve uma mensagem de erro que indica ao utilizador para fornecer uma entrada. Por exemplo, pode verificar que o endereço de e-mail fornecido pelo utilizador existe na base de dados do seu cliente.
- **Substituir afirmações de entrada** -permite-lhe reformatar valores nas afirmações de entrada. Por exemplo, se um utilizador introduz o nome em letras minúsculas ou todas as letras maiúsculas, pode formatar o nome com a primeira letra em maiúsculas.
- **Enriquecer os dados de utilizador** -permite-lhe ainda mais a integrar com aplicações de linha de negócio empresariais. Por exemplo, seu serviço RESTful pode receber o endereço de e-mail do utilizador, consultar base de dados do cliente e retornar o número de fidelização do utilizador para o Azure AD B2C. As declarações de retornadas podem ser armazenadas, avaliadas nos passos seguintes de orquestração ou incluídas no token de acesso.
- **Executar a lógica de negócio personalizada** - permite-lhe enviar notificações push, Atualizar bancos de dados corporativos, executar um processo de migração de utilizador, gerir as permissões, bases de dados de auditoria e efetuar outras ações.

A política de enviar afirmações de entrada para a API REST. A API REST também podem devolver afirmações de saída que pode utilizar mais tarde na sua política, ou ele pode emitir uma mensagem de erro. Pode projetar a integração com os serviços RESTful das seguintes formas:

- **Perfil de técnica de validação** -um perfil de técnicas de validação chama o serviço RESTful. O perfil técnico de validação valida os dados fornecidos pelo usuário antes do percurso do utilizador continua. Com o perfil técnico de validação, uma mensagem de erro é apresentar uma página de declaração própria e devolvido nas afirmações de saída.
- **Afirmações do exchange** -é feita uma chamada para o serviço RESTful através de um passo de orquestração. Neste cenário, não há nenhuma interface do usuário para processar a mensagem de erro. Se a API REST retornar um erro, o utilizador é redirecionado para a aplicação da entidade confiadora de terceiros com a mensagem de erro.

## <a name="protocol"></a>Protocolo

O **Name** atributo da **protocolo** elemento tem de ser definido como `Proprietary`. O **manipulador** atributo tem de conter o nome completamente qualificado da assemblagem de manipulador de protocolo que é utilizado pelo Azure AD B2C: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

O exemplo seguinte mostra um perfil técnico RESTful:

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...    
```

## <a name="input-claims"></a>Afirmações de entrada

O **InputClaims** elemento contém uma lista das declarações para enviar para a API REST. Também pode mapear o nome da sua afirmação o nome definido na REST API. Exemplo a seguir mostra o mapeamento entre a sua política e a API REST. O **givenName** declaração é enviada para a API de REST como **firstName**, enquanto **Apelido** é enviado como **lastName**. O **e-mail** afirmação está definida como está.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

O **InputClaimsTransformations** elemento pode conter uma coleção de **InputClaimsTransformation** elementos que são utilizados para modificar as afirmações de entrada ou gerar novos antes de enviar para a API REST.

## <a name="output-claims"></a>Afirmações de saída

O **OutputClaims** elemento contém uma lista de afirmações devolvidas pela REST API. Terá de mapear o nome da afirmação definido na sua política para o nome definido na REST API. Também pode incluir declarações que não são devolvidas pelo fornecedor de identidade de REST API, desde que definir o `DefaultValue` atributo.

O **OutputClaimsTransformations** elemento pode conter uma coleção de **OutputClaimsTransformation** elementos que são utilizados para modificar as afirmações de saída ou gerar novas etiquetas.

O exemplo seguinte mostra a afirmação devolvida pela REST API:

- O **MembershipId** afirmação que está mapeada para o **loyaltyNumber** de afirmação de nome.

O perfil técnico também retorna afirmações, que não são devolvidas pelo fornecedor de identidade: 

- O **loyaltyNumberIsNew** afirmação que tem um valor padrão definido como `true`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metadados

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ServiceUrl | Sim | O URL do ponto de final de REST API. | 
| AuthenticationType | Sim | O tipo de autenticação a ser executada pelo fornecedor de afirmações profundas. Valores possíveis: `None`, `Basic`, ou `ClientCertificate`. O `None` valor indica que a API REST não é anônima. O `Basic` valor indica que a API REST está protegida com autenticação básica HTTP. Verificar apenas os utilizadores, incluindo o Azure AD B2C, pode aceder à sua API. O `ClientCertificate` (recomendado) valor indica que a API REST restringe o acesso com a autenticação de certificado de cliente. Apenas os serviços que têm os certificados apropriados, como o Azure AD B2C podem acessar seu serviço. | 
| SendClaimsIn | Não | Especifica como as afirmações de entrada são enviadas para o fornecedor de afirmações profundas. Valores possíveis: `Body` (predefinição), `Form`, `Header`, ou `QueryString`. O `Body` valor é a afirmação de entrada que é enviada no corpo do pedido no formato JSON. O `Form` valor é a afirmação de entrada que é enviada no corpo do pedido num e comercial "&" separados o formato do valor da chave. O `Header` valor é a afirmação de entrada que é enviada no cabeçalho do pedido. O `QueryString` valor é a afirmação de entrada que é enviada numa cadeia de caracteres de consulta da solicitação. | 
| ClaimsFormat | Não | Especifica o formato para as afirmações de saída. Valores possíveis: `Body` (predefinição), `Form`, `Header`, ou `QueryString`. O `Body` valor é a declaração de saída que é enviada no corpo do pedido no formato JSON. O `Form` valor é a declaração de saída que é enviada no corpo do pedido num e comercial "&" separados o formato do valor da chave. O `Header` valor é a declaração de saída que é enviada no cabeçalho do pedido. O `QueryString` valor é a declaração de saída que é enviada numa cadeia de caracteres de consulta da solicitação. | 
| DebugMode | Não | Executa o perfil técnico no modo de depuração. No modo de depuração, a API REST pode retornar a obter mais informações. Consulte a secção de mensagem de erro devolvidos. | 

## <a name="cryptographic-keys"></a>Chaves criptográficas

Se o tipo de autenticação está definido como `None`, o **CryptographicKeys** elemento não é utilizado.

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

Se o tipo de autenticação está definido como `Basic`, o **CryptographicKeys** elemento contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | Sim | O nome de utilizador que é utilizado para autenticar. | 
| BasicAuthenticationPassword | Sim | A palavra-passe que é utilizada para autenticar. |

O exemplo seguinte mostra um perfil técnico com a autenticação básica:

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

Se o tipo de autenticação está definido como `ClientCertificate`, o **CryptographicKeys** elemento contém o atributo seguinte:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ClientCertificate | Sim | O X509 certificado (conjunto de chaves RSA) para utilizar para se autenticar. | 

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

## <a name="returning-error-message"></a>Devolver a mensagem de erro

A API REST poderá ter devolver uma mensagem de erro, como "o utilizador não foi encontrado no sistema de CRM". Num erro ocorre, a API REST deve devolver uma mensagem de erro de HTTP 409 (código de estado de resposta de conflito) com os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| versão | Sim | 1.0.0 | 
| status | Sim | 409 | 
| Código | Não | Um código de erro do fornecedor de ponto de extremidade RESTful, que é apresentado quando `DebugMode` está ativada. | 
| requestId | Não | Um identificador de pedido do fornecedor de ponto de extremidade RESTful, que é apresentado quando `DebugMode` está ativada. | 
| userMessage | Sim | Uma mensagem de erro que é mostrada ao usuário. | 
| developerMessage | Não | A descrição detalhada do problema e como corrigi-lo, o que é apresentado quando `DebugMode` está ativada. | 
| moreInfo | Não | Um URI que aponta para obter informações adicionais, que é apresentado quando `DebugMode` está ativada. | 

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

O exemplo seguinte mostra uma classe c# que retorna uma mensagem de erro:

```C#
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
- [Integrar a REST API trocas de afirmações no seu percurso do utilizador do Azure AD B2C como validação de entrada do usuário](active-directory-b2c-custom-rest-api-netfw.md) 
- [Proteger os serviços RESTful com autenticação básica HTTP](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
- [Proteger o seu serviço RESTful usando certificados de cliente](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
- [Passo a passo: Integrar a REST API trocas de afirmações no seu percurso do utilizador do Azure AD B2C como validação na entrada do usuário](active-directory-b2c-rest-api-validation-custom.md)

 














