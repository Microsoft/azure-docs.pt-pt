---
title: REST API reclama trocas na política personalizada B2C
titleSuffix: Azure AD B2C
description: Uma introdução à criação de uma viagem de utilizador Azure AD B2C que interage com serviços RESTful.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6a6cc8e5931f3e29c242f51a6e062441953228ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337418"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>Integrar rest API reclama trocas de reclamações na sua política personalizada Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Quadro de Experiência de Identidade, que está subjacente ao Diretório Ativo Azure B2C (Azure AD B2C), pode integrar-se com APIs RESTful dentro de uma viagem de utilizador. Este artigo mostra como criar uma viagem de utilizador que interage com um serviço RESTful usando um [perfil técnico RESTful](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster).

Utilizando o Azure AD B2C, pode adicionar a sua própria lógica de negócio a uma viagem de utilizador, chamando o seu próprio serviço RESTful. O Quadro de Experiência de Identidade pode enviar e receber dados do seu serviço RESTful para trocar reclamações. Pode, por exemplo:

- **Validar os dados de entrada do utilizador**. Por exemplo, pode verificar se o endereço de e-mail fornecido pelo utilizador existe na base de dados do seu cliente e, caso contrário, apresentar um erro.
- **Processo sinistros.** Se um utilizador introduzir o seu primeiro nome em todas as letras minúsculas ou maiúsculas, a sua API REST pode formatar o nome apenas com a primeira letra capitalizada e devolvê-la ao Azure AD B2C.
- **Enriqueça os dados dos utilizadores integrando-se ainda mais com aplicações de linha de negócio corporativas.** O seu serviço RESTful pode receber o endereço de e-mail do utilizador, consultar a base de dados do cliente e devolver o número de fidelização do utilizador ao Azure AD B2C. Em seguida, as reclamações de devolução podem ser armazenadas na conta AD Azure do utilizador, avaliadas nos próximos passos de orquestração, ou incluídas no token de acesso.
- **Executar a lógica de negócio personalizada.** Pode enviar notificações push, atualizar bases de dados corporativas, executar um processo de migração de utilizadores, gerir permissões, auditar bases de dados e realizar quaisquer outros fluxos de trabalho.

![Diagrama de uma troca de reclamações de serviço restful](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

## <a name="calling-a-restful-service"></a>Chamando um serviço RESTful

A interação inclui uma troca de reclamações de informações entre as alegações da API REST e o Azure AD B2C. Pode projetar a integração com os serviços RESTful das seguintes formas:

- **Perfil técnico de validação**. A chamada para o serviço RESTful ocorre dentro de um perfil técnico de [validação](validation-technical-profile.md) do [perfil técnico autoafirmado](self-asserted-technical-profile.md)especificado, ou um controlo de [visualização](display-control-verification.md) de um controlo de [visualização](display-controls.md). O perfil técnico de validação valida os dados fornecidos pelo utilizador antes da viagem do utilizador avançar. Com o perfil técnico de validação, pode:

  - Envie reclamações para a sua Rest API.
  - Valide as reclamações e lance mensagens de erro personalizadas que são exibidas ao utilizador.
  - Envie de volta as reclamações da API REST para os passos de orquestração subsequentes.

- **Troca de reclamações.** Uma troca direta de reclamações pode ser configurada chamando um perfil técnico REST API diretamente a partir de um passo de orquestração de uma [viagem de utilizador](userjourneys.md). Esta definição limita-se a:

  - Envie reclamações para a sua Rest API.
  - Valide reclamações e lance mensagens de erro personalizadas que sejam devolvidas à aplicação.
  - Envie de volta as reclamações da API REST para os passos de orquestração subsequentes.

Pode adicionar uma chamada REST API em qualquer passo da viagem de utilizador definida por uma política personalizada. Por exemplo, pode chamar uma API REST:

- Durante o início de sessão, pouco antes do Azure AD B2C validar as credenciais.
- Imediatamente após o inessão.
- Antes do Azure AD B2C criar uma nova conta no diretório.
- Depois do Azure AD B2C criar uma nova conta no diretório.
- Antes do Azure AD B2C emitir um sinal de acesso.

![Recolha de perfil técnico de validação](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>Envio de dados

No [perfil técnico RESTful,](restful-technical-profile.md)o `InputClaims` elemento contém uma lista de reclamações a enviar para o seu serviço RESTful. Pode mapear o nome da sua reclamação para o nome definido no serviço RESTful, definir um valor predefinido e utilizar [as reclamações](claim-resolver-overview.md).

Pode configurar como os pedidos de entrada são enviados para o fornecedor de reclamações RESTful utilizando o atributo SendClaimsIn. Os valores possíveis são:

- **Corpo,** enviado no organismo de pedido HTTP POST em formato JSON.
- **Formulário**, enviado no organismo de pedido HTTP POST num formato de valor-chave separado '&'.
- **Cabeçalho,** enviado no cabeçalho de pedido HTTP GET.
- **ConsultaString**, enviada na cadeia de consulta de pedido HTTP GET.

Quando a opção **Body** está configurada, o perfil técnico REST API permite-lhe enviar uma carga útil complexa da JSON para um ponto final. Para mais informações, consulte [Enviar uma carga útil JSON](restful-technical-profile.md#send-a-json-payload).

## <a name="receiving-data"></a>Dados de receção

O `OutputClaims` elemento do [perfil técnico RESTful](restful-technical-profile.md) contém uma lista de reclamações devolvidas pela API REST. Poderá ter de mapear o nome da reclamação definida na sua política para o nome definido na API REST. Também pode incluir reclamações que não são devolvidas pelo fornecedor de identidade REST API, desde que detetete o atributo DefaultValue.

Os pedidos de saída analisados pelo fornecedor de sinistros RESTful esperam sempre analisar uma resposta plana do Corpo JSON, tais como:

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

As alegações de saída devem parecer as seguintes:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

Para analisar uma resposta corporal aninhada da JSON, defino os metadados ResolveJsonPathsInJsonTokens como verdadeiros. Na alegação de saída, detete o PartnerClaimType para o elemento de caminho JSON que pretende ser desaída.

```json
"contacts": [
  {
    "id": "MAINCONTACT_1",
    "person": {
      "name": "Emily Smith",
      "loyaltyNumber":  1234,
      "emails": [
        {
          "id": "EMAIL_1",
          "type": "WORK",
          "email": "email@domain.com"
        }
      ]
    }
  }
],
```


As alegações de saída devem parecer as seguintes:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts.0.person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts.0.person.emails.0.email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts.0.person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>Considerações de segurança

Deve proteger o seu ponto final REST API para que apenas os clientes autenticados possam comunicar com ele. A API REST deve utilizar um ponto final HTTPS. Detete os metadados AutenticaçãoType num dos seguintes métodos de autenticação:

- **O certificado de cliente** restringe o acesso através da autenticação do certificado de cliente. Só os serviços que possuem os certificados adequados podem aceder à sua API. Armazena o certificado de cliente numa chave de política Azure AD B2C. Saiba mais sobre como [garantir o seu serviço RESTful utilizando certificados](secure-rest-api.md#https-client-certificate-authentication)de cliente .
- **Basic** protege a API REST com autenticação básica HTTP. Apenas utilizadores verificados, incluindo O Azure AD B2C, podem aceder à sua API. O nome de utilizador e a palavra-passe são armazenados em teclas políticas Azure AD B2C. Saiba como [proteger os seus serviços RESTful utilizando](secure-rest-api.md#http-basic-authentication)a autenticação básica HTTP .
- **O portador** restringe o acesso usando um token de acesso OAuth2 cliente. O sinal de acesso é armazenado numa chave política Azure AD B2C. Saiba mais sobre como [garantir o seu serviço RESTful utilizando o token Bearer](secure-rest-api.md#oauth2-bearer-authentication).

## <a name="rest-api-platform"></a>Plataforma REST API
A Sua API REST pode basear-se em qualquer plataforma e escrita em qualquer idioma de programação, desde que seja segura e possa enviar e receber reclamações conforme especificado no [perfil técnico RESTful](restful-technical-profile.md).

## <a name="localize-the-rest-api"></a>Localize a API REST
Num perfil técnico RESTful, pode querer enviar a língua/local da sessão atual e, se necessário, levantar uma mensagem de erro localizada. Utilizando o [resolver das reclamações,](claim-resolver-overview.md)pode enviar uma reclamação contextual, como a linguagem do utilizador. O exemplo que se segue mostra um perfil técnico RESTful que demonstra este cenário.

```XML
<TechnicalProfile Id="REST-ValidateUserData">
  <DisplayName>Validate user input data</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="handling-error-messages"></a>Manipulação de mensagens de erro

A Sua API REST poderá ter de devolver uma mensagem de erro, como "O utilizador não foi encontrado no sistema CRM." Se ocorrer um erro, a API REST deve devolver uma mensagem de erro HTTP 409 (código de estado de resposta a conflitos). Para mais informações, consulte o [perfil técnico RESTful](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster&anchor=returning-error-message).

Isto só pode ser conseguido chamando um perfil técnico REST API a partir de um perfil técnico de validação. Isto permite ao utilizador corrigir os dados na página e executar a validação novamente após a submissão da página.

É necessária uma resposta HTTP 409 para impedir o processamento de quaisquer perfis técnicos de validação subsequentes dentro desta etapa de orquestração.

Se consultar um perfil técnico REST API diretamente de uma viagem de utilizador, o utilizador é redirecionado para a aplicação da parte de fideira com a mensagem de erro relevante.

## <a name="publishing-your-rest-api"></a>Publicação da sua API REST

O pedido ao seu serviço REST API provém de servidores Azure AD B2C. O serviço REST API deve ser publicado num ponto final HTTPS acessível ao público. As chamadas REST API chegarão a partir de um endereço IP do centro de dados Azure.

Desenhe o seu serviço REST API e os seus componentes subjacentes (como a base de dados e o sistema de ficheiros) para estarem altamente disponíveis.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos, por exemplo, utilizando um perfil técnico RESTful:

- [Walkthrough: Integrar rest API reclama trocas de créditos na sua viagem de utilizador Azure AD B2C como validação da entrada do utilizador](custom-policy-rest-api-claims-validation.md)
- [Walkthrough: Adicionar REST API reclama trocas a políticas personalizadas no Diretório Ativo Azure B2C](custom-policy-rest-api-claims-validation.md)
- [Proteja os seus serviços REST API](secure-rest-api.md)
- [Referência: Perfil técnico RESTful](restful-technical-profile.md)