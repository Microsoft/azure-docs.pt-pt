---
title: REST API reclama trocas na política personalizada B2C
titleSuffix: Azure AD B2C
description: Uma introdução à criação de uma viagem de utilizador Azure AD B2C que interage com os serviços RESTful.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/18/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bc2b72779460c2b7e3999204ace50ca57388b9a2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89594191"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>Integrar as bolsas de reclamações da REST API na sua política personalizada Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Quadro de Experiência de Identidade, subjacente ao Azure Ative Directory B2C (Azure AD B2C), pode integrar-se com APIs RESTful dentro de uma viagem de utilizador. Este artigo mostra como criar uma viagem de utilizador que interage com um serviço RESTful utilizando um [perfil técnico RESTful](restful-technical-profile.md).

Utilizando o Azure AD B2C, pode adicionar a sua própria lógica de negócio a uma viagem de utilizador, chamando o seu próprio serviço RESTful. O Quadro de Experiência de Identidade pode enviar e receber dados do seu serviço RESTful para trocar reclamações. Pode, por exemplo:

- **Validar os dados de entrada do utilizador.** Por exemplo, pode verificar se o endereço de e-mail fornecido pelo utilizador existe na base de dados do seu cliente e, caso contrário, apresentar um erro.
- **Reclamações de processos**. Se um utilizador introduzir o seu primeiro nome em todas as letras minúsculas ou maiúsculas, a sua API REST pode formatar o nome apenas com a primeira letra capitalizada e devolvê-la ao Azure AD B2C.
- **Enriqueça os dados dos utilizadores integrando-se ainda mais com aplicações de linha de negócio corporativa.** O seu serviço RESTful pode receber o endereço de e-mail do utilizador, consultar a base de dados do cliente e devolver o número de fidelização do utilizador ao Azure AD B2C. Em seguida, as reclamações de devolução podem ser armazenadas na conta Azure AD do utilizador, avaliadas nas próximas etapas de orquestração, ou incluídas no token de acesso.
- **Executar lógica de negócio personalizada**. Pode enviar notificações push, atualizar bases de dados corporativas, executar um processo de migração de utilizadores, gerir permissões, auditar bases de dados e realizar quaisquer outros fluxos de trabalho.

![Diagrama de uma troca de reclamações de serviço RESTful](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

> [!NOTE]
> Se houver uma resposta lenta ou nenhuma do serviço RESTful ao Azure AD B2C, o tempo limite é de 30 segundos e a contagem de repetição é de 2 vezes (o que significa que há 3 tentativas no total). As definições de contagem de tempo limite e de repetição não são atualmente configuráveis.

## <a name="calling-a-restful-service"></a>Chamando um serviço RESTful

A interação inclui uma troca de informações entre as reclamações da API REST e a Azure AD B2C. Pode projetar a integração com os serviços RESTful das seguintes formas:

- **Perfil técnico de validação**. A chamada para o serviço RESTful ocorre dentro de um perfil técnico de [validação](validation-technical-profile.md) do [perfil técnico autoafirmado](self-asserted-technical-profile.md)especificado, ou um controlo de visualização de [verificação](display-control-verification.md) de um controlo de [exibição](display-controls.md). O perfil técnico de validação valida os dados fornecidos pelo utilizador antes da viagem do utilizador avançar. Com o perfil técnico de validação, pode:

  - Envie reclamações para a sua API REST.
  - Valide as reclamações e lance mensagens de erro personalizadas que sejam apresentadas ao utilizador.
  - Remem, reca em seguida, as reclamações da API PARA as etapas de orquestração subsequentes.

- **Troca de reclamações**. Uma troca de reclamações diretas pode ser configurada chamando um perfil técnico da API REST diretamente de um passo de orquestração de uma [viagem](userjourneys.md)de utilizador . Esta definição limita-se a:

  - Envie reclamações para a sua API REST.
  - Validar reclamações e lançar mensagens de erro personalizadas que são devolvidas à aplicação.
  - Remem, reca em seguida, as reclamações da API PARA as etapas de orquestração subsequentes.

Pode adicionar uma chamada de API REST a qualquer passo da jornada do utilizador definida por uma política personalizada. Por exemplo, pode chamar uma API DE REPOUSO:

- Durante a entrada, pouco antes de Azure AD B2C validar as credenciais.
- Imediatamente após a sinsind.
- Antes de Azure AD B2C criar uma nova conta no diretório.
- Depois de Azure AD B2C criar uma nova conta no diretório.
- Antes de Azure AD B2C emitir um token de acesso.

![Coleção de perfis técnicos de validação](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>Envio de dados

No [perfil técnico RESTful,](restful-technical-profile.md)o `InputClaims` elemento contém uma lista de reclamações a enviar para o seu serviço RESTful. Pode mapear o nome da sua reclamação para o nome definido no serviço RESTful, definir um valor padrão e utilizar [reclamações .](claim-resolver-overview.md)

Pode configurar como as reclamações de entrada são enviadas para o provedor de reclamações RESTful utilizando o atributo SendClaimsIn. Os valores possíveis são:

- **Corpo**, enviado no órgão de pedido HTTP POST em formato JSON.
- **Formulário**, enviado no órgão de pedido HTTP POST num formato de valor-chave separado '&'.
- **Cabeçalho**, enviado no cabeçalho de pedido HTTP GET.
- **QueryString**, enviado no string de consulta de pedido HTTP GET.

Quando a opção **Corpo** está configurada, o perfil técnico da API REST permite-lhe enviar uma carga complexa JSON para um ponto final. Para mais informações, consulte [Enviar uma carga útil JSON](restful-technical-profile.md#send-a-json-payload).

## <a name="receiving-data"></a>Receber dados

O `OutputClaims` elemento do perfil técnico [RESTful](restful-technical-profile.md) contém uma lista de reclamações devolvidas pela API REST. Poderá ser necessário mapear o nome da reclamação definida na sua política para o nome definido na API REST. Também pode incluir reclamações que não são devolvidas pelo provedor de identidade REST API, desde que desemfina o atributo DefaultValue.

As alegações de saída analisadas pelo provedor de sinistros RESTful esperam sempre analisar uma resposta plana do Corpo JSON, tais como:

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

As alegações de saída devem ser as seguintes:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

Para analisar uma resposta aninhada do Corpo JSON, defina os metadados ResolveJsonPathsInJsonTokens para a verdade. Na reclamação de saída, desacorda o PartnerClaimType no elemento de trajetória JSON que pretende obter.

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


As alegações de saída devem parecer seguintes:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts[0].person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts[0].person.emails[0].email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts[0].person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>Considerações de segurança

Deve proteger o seu ponto final da API REST para que apenas os clientes autenticados possam comunicar com ele. A API REST deve utilizar um ponto final HTTPS. Desaver os metadados Do Tipo de Autenticação para um dos seguintes métodos de autenticação:

- **O certificado do cliente** restringe o acesso através da autenticação do certificado de cliente. Apenas os serviços que possuam os certificados apropriados podem aceder à sua API. Guarde o certificado de cliente numa chave de política Azure AD B2C. Saiba mais sobre como [garantir o seu serviço RESTful utilizando certificados de cliente.](secure-rest-api.md#https-client-certificate-authentication)
- **A basic** garante a API REST com autenticação básica HTTP. Apenas os utilizadores verificados, incluindo o Azure AD B2C, podem aceder à sua API. O nome de utilizador e a palavra-passe são armazenados nas teclas de política Azure AD B2C. Saiba como [proteger os seus serviços RESTful utilizando a autenticação básica HTTP.](secure-rest-api.md#http-basic-authentication)
- **O Portador** restringe o acesso usando um token de acesso OAuth2 do cliente. O token de acesso está armazenado numa chave de política Azure AD B2C. Saiba mais sobre como [garantir o seu serviço RESTful utilizando o token Bearer](secure-rest-api.md#oauth2-bearer-authentication).

## <a name="rest-api-platform"></a>Plataforma REST API
A sua API REST pode basear-se em qualquer plataforma e escrita em qualquer idioma de programação, desde que seja segura e possa enviar e receber reclamações conforme especificado no [perfil técnico RESTful](restful-technical-profile.md).

## <a name="localize-the-rest-api"></a>Localize a API REST
Num perfil técnico RESTful, pode querer enviar o idioma/local da sessão atual e, se necessário, levantar uma mensagem de erro localizada. Utilizando as [reclamações,](claim-resolver-overview.md)pode enviar uma reclamação contextual, como o idioma do utilizador. O exemplo a seguir mostra um perfil técnico RESTful que demonstra este cenário.

```xml
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

A API rest pode ter de devolver uma mensagem de erro, tal como "O utilizador não foi encontrado no sistema CRM." Se ocorrer um erro, a API REST deve devolver uma mensagem de erro HTTP 409 (Código de Estado de resposta a conflitos). Para mais informações, consulte o [perfil técnico RESTful](restful-technical-profile.md#returning-validation-error-message).

Tal só pode ser conseguido através da chamada de um perfil técnico da API REST a partir de um perfil técnico de validação. Isto permite ao utilizador corrigir os dados na página e executar novamente a validação após a submissão da página.

É necessária uma resposta HTTP 409 para impedir o processamento de quaisquer perfis técnicos de validação subsequentes dentro desta etapa de orquestração.

Se referenciar um perfil técnico da API REST diretamente de uma viagem de utilizador, o utilizador é redirecionado de volta para a aplicação da parte de suporte com a mensagem de erro relevante.

## <a name="publishing-your-rest-api"></a>Publicando a sua API REST

O pedido para o seu serviço REST API provém de servidores Azure AD B2C. O serviço REST API deve ser publicado num ponto final HTTPS acessível ao público. As chamadas DE API do REST chegarão a partir de um endereço IP do Centro de Dados Azure.

Desenhe o seu serviço REST API e os seus componentes subjacentes (como a base de dados e o sistema de ficheiros) para estar altamente disponível.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para exemplos de utilização de um perfil técnico RESTful:

- [Walkthrough: Integrar as trocas de reclamações da API REST na sua viagem de utilizador Azure AD B2C como validação da entrada do utilizador](custom-policy-rest-api-claims-validation.md)
- [Walkthrough: Add REST API reclama trocas de trocas para políticas personalizadas no Azure Ative Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Proteja os seus serviços REST API](secure-rest-api.md)
- [Referência: Perfil técnico RESTful](restful-technical-profile.md)
