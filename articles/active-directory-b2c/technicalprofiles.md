---
title: TechnicalProfiles
titleSuffix: Azure AD B2C
description: Especificar o elemento TécnicoProfiles de uma política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5eff20ecb1366114ead80877b684ef512742803b
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805399"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um perfil técnico fornece um quadro com um mecanismo incorporado para comunicar com diferentes tipos de festas. Os perfis técnicos são utilizados para comunicar com o seu inquilino Azure AD B2C, para criar um utilizador ou ler um perfil de utilizador. Um perfil técnico pode ser autoafirmado para permitir a interação com o utilizador. Por exemplo, colete a credencial do utilizador para iniciar sessão e, em seguida, torne a página de inscrição ou a página de reset da palavra-passe.

## <a name="type-of-technical-profiles"></a>Tipo de perfis técnicos

Um perfil técnico permite este tipo de cenários:

- [Application Insights](analytics-with-application-insights.md) - Envio de dados de eventos para [Insights de Aplicação](../azure-monitor/app/app-insights-overview.md).
- [Azure Ative Directory](active-directory-technical-profile.md) - Fornece suporte para a gestão de utilizadores Azure Ative Directory B2C.
- [Azure AD Multi-Factor Authentication](multi-factor-auth-technical-profile.md) - fornece suporte para verificar um número de telefone utilizando a autenticação multi-factor Azure AD (MFA). 
- [Transformação de reclamações](claims-transformation-technical-profile.md) - A produção de chamadas reclama transformações para manipular valores de sinistros, validar reclamações ou definir valores padrão para um conjunto de reclamações de saída.
- [Dica de iD token](id-token-hint.md) - Valida `id_token_hint` a assinatura simbólica JWT, o nome do emitente, e o público simbólico e extrai a reivindicação do token de entrada.
- [Emitente JWT token](jwt-issuer-technical-profile.md) - Emite um símbolo JWT que é devolvido à aplicação do partido em gestão.
- [OAuth1](oauth1-technical-profile.md) - Federação com qualquer provedor de identidade do protocolo OAuth 1.0.
- [OAuth2](oauth2-technical-profile.md) - Federação com qualquer provedor de identidade do protocolo OAuth 2.0.
- [Palavra-passe única](one-time-password-technical-profile.md) - Fornece suporte para gerir a geração e verificação de uma senha única.
- [OpenID Connect](openid-connect-technical-profile.md) - Federação com qualquer fornecedor de identidade de protocolo OpenID Connect.
- [Fator telefone](phone-factor-technical-profile.md) - Suporte para inscrição e verificação de números de telefone.
- [RESTful provider](restful-technical-profile.md) - Ligue para os serviços de API REST, tais como validar a entrada do utilizador, enriquecer os dados dos utilizadores ou integrar-se com aplicações de linha de negócio.
- [Fornecedor de identidade SAML](saml-identity-provider-technical-profile.md) - Federação com qualquer fornecedor de identidade de protocolo SAML.
- [Emissor de token SAML](saml-issuer-technical-profile.md) - Emite um token SAML que é devolvido à aplicação do partido em gestão.
- [Auto-Assertado](self-asserted-technical-profile.md) - Interaja com o utilizador. Por exemplo, recolher a credencial do utilizador para iniciar sessão, fazer a página de inscrição ou reiniciar a palavra-passe.
- [Gestão de sessão](custom-policy-reference-sso.md) - Lidar com diferentes tipos de sessões.

## <a name="technical-profile-flow"></a>Fluxo de perfil técnico

Todos os tipos de perfis técnicos partilham o mesmo conceito. Comece por ler as alegações de entrada, executar a transformação de sinistros. Em seguida, comunique com a parte configurada, como um fornecedor de identidade, REST API, ou serviços de diretório AD Azure. Após o processo estar concluído, o perfil técnico devolve os pedidos de saída e pode executar a transformação de sinistros de saída. O diagrama seguinte mostra como as transformações e mapeamentos referenciados no perfil técnico são processados. Após a transformação dos sinistros, os pedidos de saída são imediatamente armazenados no saco de reclamações. Independentemente do partido, o perfil técnico interage.

![Diagrama que ilustra o fluxo de perfil técnico](./media/technical-profiles/technical-profile-flow.png)

1. **Gestão única de sessão de sessão de sessão (SSO)** - Restaura o estado de sessão do perfil técnico, utilizando a gestão da [sessão SSO](custom-policy-reference-sso.md).
1. **A transformação de reclamações de entrada** - Antes do início do perfil técnico, o Azure AD B2C executa [a transformação de sinistros de](claimstransformations.md)entrada .
1. **Reclamações de entrada** - As reclamações são recolhidas no saco de reclamações que são utilizados para o perfil técnico.
1. **Execução de perfil técnico** - O perfil técnico troca as reclamações com a parte configurada. Por exemplo:
    - Redirecione o utilizador para o fornecedor de identidade para completar a inscrição. Após o sucesso da sessão, o utilizador regressa e a execução do perfil técnico continua.
    - Ligue para uma API REST enquanto envia parâmetros como InputClaims e obter informações de volta como OutputClaims.
    - Criar ou atualizar a conta do utilizador.
    - Envia e verifica a mensagem de texto do MFA.
1. **Perfis técnicos** de validação - Um [perfil técnico autoafirmado](self-asserted-technical-profile.md) pode chamar [perfis técnicos](validation-technical-profile.md) de validação para validar os dados perfilados pelo utilizador.
1. **Reclamações de saída** - As reclamações são devolvidas ao saco de reclamações. Pode usar essas reivindicações na próxima etapa de orquestrações, ou a produção reclama transformações.
1. **A output alega transformações** - Após a conclusão do perfil técnico, o Azure AD B2C executa a [transformação de sinistros de](claimstransformations.md)produção . 
1. **Gestão única da sessão de sessão de sessão ( SSO)** - Persiste os dados do perfil técnico para a sessão, utilizando a gestão da [sessão SSO](custom-policy-reference-sso.md).

Um elemento **TechnicalProfiles** contém um conjunto de perfis técnicos apoiados pelo fornecedor de sinistros. Todos os prestadores de sinistros devem ter pelo menos um perfil técnico. O perfil técnico determina os pontos finais e os protocolos necessários para comunicar com o fornecedor de sinistros. Um fornecedor de sinistros pode ter vários perfis técnicos.

```xml
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        ...
      </Metadata>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

O elemento **TécnicoProfile** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
|---------|---------|---------|
| Id | Yes | Um identificador único do perfil técnico. O perfil técnico pode ser referenciado utilizando este identificador a partir de outros elementos do ficheiro de política. Por exemplo, **OrquestrationSteps** e **ValidationTechnicalProfile**. |

O **Ficheiro Técnico** contém os seguintes elementos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Domínio | 0:1 | O nome de domínio para o perfil técnico. Por exemplo, se o seu perfil técnico especificar o fornecedor de identidade do Facebook, o nome de domínio é Facebook.com. |
| DisplayName | 1:1 | O nome de exibição do perfil técnico. |
| Description | 0:1 | A descrição do perfil técnico. |
| Protocolo | 1:1 | O protocolo usado para a comunicação com a outra parte. |
| Metadados | 0:1 | Uma coleção de chave/valor que controla o comportamento do perfil técnico. |
| InputTokenFormat | 0:1 | O formato do token de entrada. Valores possíveis: `JSON` `JWT` , , ou `SAML11` `SAML2` . O `JWT` valor representa um Token Web JSON de acordo com a especificação IETF. O `SAML11` valor representa um sinal de segurança SAML 1.1 de acordo com a especificação OASIS.  O `SAML2` valor representa um sinal de segurança SAML 2.0 de acordo com a especificação OASIS. |
| OutputTokenFormat | 0:1 | O formato do token de saída. Valores possíveis: `JSON` `JWT` , , ou `SAML11` `SAML2` . |
| Chaves Criptográficas | 0:1 | Uma lista de chaves criptográficas que são utilizadas no perfil técnico. |
| InputClaimsTransformações | 0:1 | Uma lista de referências previamente definidas a transformações de sinistros que devem ser executadas antes de quaisquer reclamações serem enviadas ao prestador de sinistros ou à parte de sinistros. |
| InputClaims | 0:1 | Uma lista das referências previamente definidas aos tipos de reclamação que são tomados como entrada no perfil técnico. |
| PersistidoClaims | 0:1 | Uma lista das referências previamente definidas aos tipos de reclamação que serão persistidos pelo perfil técnico. |
| DisplayClaims | 0:1 | Uma lista das referências previamente definidas aos tipos de reclamação que são apresentados pelo [perfil técnico autoafirmado](self-asserted-technical-profile.md). A função DisplayClaims encontra-se atualmente em **pré-visualização**. |
| Resultados | 0:1 | Uma lista das referências previamente definidas aos tipos de reclamação que são tomados como saída no perfil técnico. |
| ResultadosClaimsTransformações | 0:1 | Uma lista de referências previamente definidas a transformações de sinistros que devem ser executadas após a recebida do prestador de sinistros. |
| ValidaçãoTechnicalProfiles | 0:n | Uma lista de referências a outros perfis técnicos que o perfil técnico utiliza para efeitos de validação. Para mais informações, consulte [o perfil técnico de validação](validation-technical-profile.md)|
| Nomeação de AssuntosInfo | 0:1 | Controla a produção do nome do sujeito em fichas onde o nome do sujeito é especificado separadamente das reclamações. Por exemplo, OAuth ou SAML.  |
| IncluirInSso | 0:1 |  Se o uso deste perfil técnico deve aplicar um comportamento único de sessão (SSO) ou, em vez disso, exigir interação explícita. Este elemento é válido apenas em perfis SelfAsserted utilizados num perfil técnico de Validação. Valores possíveis: `true` (padrão), ou `false` . |
| IncluirClaimsFromTechnicalProfile | 0:1 | Um identificador de um perfil técnico a partir do qual pretende que todas as alegações de entrada e saída sejam adicionadas a este perfil técnico. O perfil técnico referenciado deve ser definido no mesmo ficheiro político. |
| Incluir TecnologiaProfile |0:1 | Um identificador de um perfil técnico a partir do qual pretende que todos os dados sejam adicionados a este perfil técnico. |
| UseTechnicalProfileForSsionManagement | 0:1 | Um perfil técnico diferente para ser utilizado para a gestão da sessão. |
|EnabledForUserJourneys| 0:1 |Controla se o perfil técnico for executado numa viagem de utilizador.  |

## <a name="protocol"></a>Protocolo

O **Protocolo** especifica o protocolo a utilizar para a comunicação com a outra parte. O elemento **Protocolo** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Nome | Yes | O nome de um protocolo válido suportado pelo Azure AD B2C que é utilizado como parte do perfil técnico. Valores possíveis: `OAuth1` , , , , ou `OAuth2` `SAML2` `OpenIdConnect` `Proprietary` `None` . |
| Processador | No | Quando o nome do protocolo estiver definido para `Proprietary` , especifique o nome do conjunto que é utilizado pelo Azure AD B2C para determinar o manipulador de protocolo. |

## <a name="metadata"></a>Metadados

O elemento **Metadados** contém as opções de configuração relevantes para um protocolo específico. A lista de metadados suportados está documentada na especificação [de perfil técnico](#type-of-technical-profiles) correspondente. Um elemento **metadados** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Item | 0:n | Os metadados que se relacionam com o perfil técnico. Cada tipo de perfil técnico tem um conjunto diferente de itens de metadados. Para mais informações, consulte a secção de tipos de perfis técnicos.  |

### <a name="item"></a>Item

O **elemento item** do elemento **metadados** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Chave | Yes | A chave dos metadados. Consulte cada [tipo de perfil técnico,](#type-of-technical-profiles)para obter a lista de itens de metadados. |

O exemplo a seguir ilustra a utilização de metadados relevantes para [o perfil técnico da OAuth2](oauth2-technical-profile.md#metadata).

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  ...
  <Metadata>
    <Item Key="ProviderName">facebook</Item>
    <Item Key="authorization_endpoint">https://www.facebook.com/dialog/oauth</Item>
    <Item Key="AccessTokenEndpoint">https://graph.facebook.com/oauth/access_token</Item>
    <Item Key="HttpBinding">GET</Item>
    <Item Key="UsePolicyInRedirectUri">0</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

O exemplo a seguir ilustra a utilização de metadados relevantes para o [perfil técnico da API REST](restful-technical-profile.md#metadata).

```xml
<TechnicalProfile Id="REST-Validate-Email">
  ...
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

## <a name="cryptographic-keys"></a>Chaves criptográficas

Para estabelecer confiança com os serviços com os quais se integra, o Azure AD B2C armazena segredos e certificados sob a forma de [chaves de política.](policy-keys-overview.md) Durante a execução do perfil técnico, o Azure AD B2C recupera as chaves criptográficas das teclas de política Azure AD B2C. Em seguida, usa as chaves para estabelecer confiança, encriptar ou assinar um token. Estes fundos consistem em:

- Federação com [OAuth1](oauth1-technical-profile.md#cryptographic-keys), [OAuth2](oauth2-technical-profile.md#cryptographic-keys)e fornecedores de identidade [SAML](saml-identity-provider-technical-profile.md#cryptographic-keys)
- Garantir a ligação com [os serviços rest API](secure-rest-api.md)
- Assinatura e encriptação dos tokens [JWT](jwt-issuer-technical-profile.md#cryptographic-keys) e [SAML](saml-issuer-technical-profile.md#cryptographic-keys)

O elemento **CryptographicKeys** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Chave | 1:n | Uma chave criptográfica utilizada neste perfil técnico. |

### <a name="key"></a>Chave

O elemento **chave** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Id | No | Um identificador único de um determinado par de chaves referenciado a partir de outros elementos no ficheiro de política. |
| StorageReferenceId | Yes | Um identificador de um recipiente chave de armazenamento referenciado a partir de outros elementos do ficheiro de política. |

## <a name="input-claims-transformations"></a>Input reclama transformações

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos de transformação de pedidos de entrada que são usados para modificar alegações de entrada ou gerar um novo. 

As alegações de saída de uma transformação de sinistros anteriores na coleção de transformação de sinistros podem ser alegações de entrada subsequente de uma transformação de pedidos de entrada que lhe permite ter uma sequência de transformação de sinistros dependendo uns dos outros.

O elemento **InputClaimsTransformations** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| InputClaimsTransformação | 1:n | O identificador de uma transformação de sinistros que deve ser executada antes de quaisquer reclamações serem enviadas ao prestador de sinistros ou à parte de confiança. Uma transformação de sinistros pode ser usada para modificar as reivindicações existentesSsSchema ou gerar novas. |

### <a name="inputclaimstransformation"></a>InputClaimsTransformação

O elemento **InputClaimsTransformation** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Um identificador de uma transformação de sinistros já definida no ficheiro de política ou no ficheiro de política dos pais. |

Os seguintes perfis técnicos referem-se à transformação do **CreateOtherMailsFromEmail.** A transformação de sinistros acrescenta o valor da `email` reclamação à `otherMails` recolha, antes de persistir os dados no diretório.

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  ...
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  ...
</TechnicalProfile>
```

## <a name="input-claims"></a>Reclamações de entrada

O **InputClaims** recolhe reclamações do saco de reclamações e é utilizado para o perfil técnico. Por exemplo, um [perfil técnico autoafirmado](self-asserted-technical-profile.md) utiliza as alegações de entrada para pré-povoar as alegações de saída que o utilizador fornece. Um perfil técnico da API REST utiliza as alegações de entrada para enviar parâmetros de entrada para o ponto final da API REST. O Azure Ative Directory utiliza a reclamação de entrada como um identificador único para ler, atualizar ou apagar uma conta.

O elemento **InputClaims** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | Um tipo de pedido de entrada esperado. |

### <a name="inputclaim"></a>InputClaim

O elemento **InputClaim** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Yes | O identificador de um tipo de reclamação. A reclamação já está definida na secção de esquema de reclamações no ficheiro de política ou no ficheiro de política dos pais. |
| PadrãoValue | No | Um valor predefinido a utilizar para criar uma reclamação se a reclamação indicada pelo ClaimTypeReferenceId não existir para que a reclamação resultante possa ser utilizada como Um InputClaim pelo perfil técnico. |
| PartnerClaimType | No | O identificador do tipo de reclamação do parceiro externo a que a apólice especificada tipo reivindicação mapeia. Se o atributo PartnerClaimType não for especificado, então o tipo de reclamação de política especificado é mapeado para o tipo de reclamação do parceiro com o mesmo nome. Use esta propriedade quando o seu nome de tipo reivindicação é diferente da outra parte. Por exemplo, o primeiro nome de reclamação é "dado Nome", enquanto o parceiro utiliza uma reclamação denominada "first_name". |

## <a name="display-claims"></a>Apresentar reclamações

O elemento **DisplayClaims** contém uma lista de reclamações a apresentar no ecrã para recolher dados do utilizador. Na coleção de reclamações do visor, pode incluir uma referência a um [tipo de reclamação](claimsschema.md)ou a um [DisplayControl](display-controls.md) que criou. 

- Um tipo de reclamação é uma referência a uma reivindicação a ser exibida no ecrã. 
  - Para forçar o utilizador a fornecer um valor para uma reclamação específica, desa estale o atributo **exigido** do elemento **DisplayClaim** para `true` .
  - Para pré-povoar os valores das reclamações do visor, utilize as alegações de entrada que foram previamente descritas. O elemento também pode conter um valor predefinido.
  - O elemento **ClaimType** na coleção **DisplayClaims** necessita de definir o elemento **UserInputType** em qualquer tipo de entrada de utilizador suportado por Azure AD B2C. Por exemplo, `TextBox` ou `DropdownSingleSelect`.
- Um controlo de exibição é um elemento de interface do utilizador que tem uma funcionalidade especial e interage com o serviço de back-end Azure AD B2C. Permite ao utilizador executar ações na página que invocam um perfil técnico de validação na parte de trás. Por exemplo, verificar um endereço de e-mail, número de telefone ou número de fidelização do cliente.

A ordem dos elementos no **DisplayClaims** especifica a ordem em que a Azure AD B2C apresenta as reclamações no ecrã. 

O elemento **DisplayClaims** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| DisplayClaim | 1:n | Um tipo de pedido de entrada esperado. |

### <a name="displayclaim"></a>DisplayClaim

O elemento **DisplayClaim** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | No | O identificador de um tipo de reclamação já definido na secção ClaimsSchema no ficheiro de política ou no ficheiro de política dos pais. |
| DisplayControlReferenceId | No | O identificador de um controlo de [exibição](display-controls.md) já definido na secção ClaimsSchema no ficheiro de política ou no ficheiro de política dos pais. |
| Necessário | No | Indica se a reclamação do visor é necessária. |

O exemplo a seguir ilustra a utilização de reclamações de visualização e controlos de visualização com um perfil técnico autoafirmado.

![Um perfil técnico autoafirmado com alegações de exibição](./media/technical-profiles/display-claims.png)

No seguinte perfil técnico:

- A primeira alegação do visor faz uma referência ao controlo do `emailVerificationControl` ecrã, que recolhe e verifica o endereço de e-mail.
- A quinta alegação do visor faz uma referência ao controlo do `phoneVerificationControl` visor, que recolhe e verifica um número de telefone.
- As outras alegações de exibição são ClaimTypes a serem recolhidas junto do utilizador.

```xml
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

### <a name="persisted-claims"></a>Reivindicações persistentes

O elemento **PersistedClaims** contém todos os valores que devem ser persistidos pelo [perfil técnico Azure AD](active-directory-technical-profile.md) com possíveis informações de mapeamento entre um tipo de reclamação já definido na secção [ClaimsSchema](claimsschema.md) na política e o nome de atributo Azure AD.

O nome da reclamação é o nome do [atributo AZure AD,](user-profile-attributes.md) a menos que o atributo **PartnerClaimType** seja especificado, que contém o nome de atributo AD Azure.

O elemento **PersistedClaims** contém os seguintes elementos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Reclamação Persistiu | 1:n | O tipo de reivindicação para persistir. |

### <a name="persistedclaim"></a>Reclamação Persistiu

O elemento **PersistedClaim** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Yes | O identificador de um tipo de reclamação já definido na secção ClaimsSchema no ficheiro de política ou no ficheiro de política dos pais. |
| PadrãoValue | No | Um valor predefinido a utilizar para criar uma reclamação se a reclamação não existir. |
| PartnerClaimType | No | O identificador do tipo de reclamação do parceiro externo a que a apólice especificada tipo reivindicação mapeia. Se o atributo PartnerClaimType não for especificado, então o tipo de reclamação de política especificado é mapeado para o tipo de reclamação do parceiro com o mesmo nome. Use esta propriedade quando o seu nome de tipo reivindicação é diferente da outra parte. Por exemplo, o primeiro nome de reclamação é "dado Nome", enquanto o parceiro utiliza uma reclamação denominada "first_name". |

No exemplo seguinte, o perfil técnico **AAD-UserWriteUsingLogonEmail** ou o [pacote inicial](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts), que cria uma nova conta local, persiste na sequência de alegações:

```xml
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
  <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
  <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
  <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />
  <PersistedClaim ClaimTypeReferenceId="givenName" />
  <PersistedClaim ClaimTypeReferenceId="surname" />
</PersistedClaims>
```

## <a name="output-claims"></a>Reclamações de saída

Os **OutputClaims** são a recolha de reclamações que são devolvidas ao saco de reclamações após a conclusão do perfil técnico. Pode usar essas reivindicações na próxima etapa de orquestrações, ou a produção reclama transformações. O elemento **OutputClaims** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| OutputClaim | 1:n | Um tipo de reivindicação de saída esperada. |

### <a name="outputclaim"></a>OutputClaim

O elemento **OutputClaim** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Yes | O identificador de um tipo de reclamação já definido na secção ClaimsSchema no ficheiro de política ou no ficheiro de política dos pais. |
| PadrãoValue | No | Um valor predefinido a utilizar para criar uma reclamação se a reclamação não existir. |
|AlwaysUseDefaultValue |No |Forçar o uso do valor padrão.  |
| PartnerClaimType | No | O identificador do tipo de reclamação do parceiro externo a que a apólice especificada tipo reivindicação mapeia. Se o atributo do tipo de reclamação do parceiro não for especificado, o tipo de reclamação de política especificado é mapeado para o tipo de reclamação do parceiro com o mesmo nome. Use esta propriedade quando o seu nome de tipo reivindicação é diferente da outra parte. Por exemplo, o primeiro nome de reclamação é "dado Nome", enquanto o parceiro utiliza uma reclamação denominada "first_name". |

## <a name="output-claims-transformations"></a>Produção reclama transformações

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos de **saídaClaimsTransformation.** A produção alega que as transformações são usadas para modificar as alegações de saída ou gerar novas. Após a execução, os pedidos de saída são colocados de volta no saco de reclamações. Podes usar essas afirmações na próxima etapa das orquestrações.

As alegações de saída de uma transformação de sinistros anteriores na coleção de transformação de sinistros podem ser alegações de entrada subsequente de uma transformação de pedidos de entrada subsequente, permitindo-lhe ter uma sequência de transformação de sinistros dependendo uns dos outros.

O elemento **OutputClaimsTransformations** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| SaídaClaimsTransformação | 1:n | Os identificadores das transformações de sinistros que devem ser executados antes de quaisquer reclamações serem enviadas ao prestador de sinistros ou à parte de confiança. Uma transformação de sinistros pode ser usada para modificar as reivindicações existentesSsSchema ou gerar novas. |

### <a name="outputclaimstransformation"></a>SaídaClaimsTransformação

O elemento **OutputClaimsTransformation** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Um identificador de uma transformação de sinistros já definida no ficheiro de política ou no ficheiro de política dos pais. |

O perfil técnico que se segue refere a assertAccountEnabledIsTrue alega a transformação para avaliar se a conta está ativada ou não após a leitura `accountEnabled` do pedido do diretório.    

```xml
<TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
  ...
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="accountEnabled" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
  ...
</TechnicalProfile>
```

## <a name="validation-technical-profiles"></a>Perfis técnicos de validação

É utilizado um perfil técnico de validação para validar reclamações de saída num [perfil técnico autoafirmado](self-asserted-technical-profile.md#validation-technical-profiles). Um perfil técnico de validação é um perfil técnico ordinário de qualquer protocolo, como [o Azure Ative Directory](active-directory-technical-profile.md) ou um [REST API](restful-technical-profile.md). O perfil técnico de validação devolve as reclamações de saída ou devolve o código de erro. A mensagem de erro é entregue ao utilizador no ecrã, permitindo que o utilizador redoça.

O diagrama seguinte ilustra como o Azure AD B2C utiliza um perfil técnico de validação para validar as credenciais do utilizador

![Fluxo de perfil técnico de validação de diagrama](./media/technical-profiles/validation-technical-profile.png) 

O elemento **ValidationTechnicalProfiles** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| ValidaçãoTechnicalProfile | 1:n | Os identificadores de perfis técnicos utilizados validam algumas ou todas as reivindicações de saída do perfil técnico de referência. Todas as alegações de entrada do perfil técnico referenciado devem figurar nas alegações de saída do perfil técnico de referência. |

### <a name="validationtechnicalprofile"></a>ValidaçãoTechnicalProfile

O elemento **ValidationTechnicalProfile** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Um identificador de um perfil técnico já definido no ficheiro de política ou no ficheiro de política dos pais. |

## <a name="subjectnaminginfo"></a>Nomeação de AssuntosInfo

O **SubjectNamingInfo** define o nome do sujeito usado em fichas numa [política partidária em reserva.](relyingparty.md#subjectnaminginfo) O **SubjectNamingInfo** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ClaimType | Yes | Um identificador de um tipo de reclamação já definido na secção ClaimsSchema no ficheiro de política. |

## <a name="include-technical-profile"></a>Incluir perfil técnico

Um perfil técnico pode incluir outro perfil técnico para alterar definições ou adicionar uma nova funcionalidade. O elemento **IncludeTechnicalProfile** é uma referência ao perfil técnico comum a partir do qual é derivado um perfil técnico. Para reduzir a redundância e complexidade dos seus elementos políticos, utilize a inclusão quando tiver vários perfis técnicos que partilham os elementos fundamentais. Utilize um perfil técnico comum com o conjunto comum de configurações, juntamente com perfis técnicos de tarefas específicos que incluem o perfil técnico comum. 

Suponha que tenha um [perfil técnico da API REST](restful-technical-profile.md) com um único ponto final onde precisa enviar diferentes conjuntos de reclamações para diferentes cenários. Crie um perfil técnico comum com a funcionalidade partilhada, como, por exemplo, o ponto final da API REST URI, metadados, tipo de autenticação e teclas criptográficas. Criar perfis técnicos de tarefa específicos que incluam o perfil técnico comum. Em seguida, adicione as reclamações de entrada, as reclamações de saída ou substitua o ponto final rest API URI relevante para esse perfil técnico.

O elemento **IncludeTechnicalProfile** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Um identificador de um perfil técnico já definido no ficheiro de política ou no ficheiro de política dos pais. |


O exemplo a seguir ilustra a utilização da inclusão:

- *REST-API-Common* - um perfil técnico comum com a configuração básica.
- *REST-ValidateProfile* - inclui o perfil técnico *REST-API-Common,* e especifica os pedidos de entrada e saída.
- *REST-UpdateProfile* - inclui o perfil técnico *REST-API-Common,* especifica as alegações de entrada e substitui os `ServiceUrl` metadados.

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-API-Common">
      <DisplayName>Base REST API configuration</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
      </CryptographicKeys>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Validate the account and return promo code</DisplayName>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-UpdateProfile">
       <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/update</Item>
      </Metadata>
      <DisplayName>Update the user profile</DisplayName>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

### <a name="multi-level-inclusion"></a>Inclusão a vários níveis 

Um perfil técnico pode incluir um único perfil técnico. Não há limite para o número de níveis de inclusão. Por exemplo, o perfil técnico **AAD-UserReadUsingAlternativeSecurityId-NoError** inclui o perfil técnico **AAD-UserReadUsingAlternativeSecurityId**. Este perfil técnico define o `RaiseErrorIfClaimsPrincipalDoesNotExist` item dos metadados para `true` , e levanta um erro se uma conta social não existir no diretório. **AAD-UserReadUsingAlternativeSecurityId-NoError** substitui este comportamento e desativa essa mensagem de erro.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** inclui o `AAD-Common` perfil técnico.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Tanto **o AAD-UserReadUsingAlternativeSecurityId-NoError** e **o AAD-UserReadUsingAlternativeSecurityId** não especificam o elemento **de protocolo** exigido, uma vez que está especificado no perfil técnico **AAD-Common.**

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

## <a name="use-technical-profile-for-session-management"></a>Utilize o perfil técnico para a gestão da sessão

A referência do elemento **useTechnicalProfileForSessionManagement** ao [perfil técnico de sessão de sessão de inscrição única](custom-policy-reference-sso.md). O elemento **UseTechnicalProfileForSessionManagement** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Um identificador de um perfil técnico já definido no ficheiro de política ou no ficheiro de política dos pais. |

## <a name="enabled-for-user-journeys"></a>Habilitado para viagens de utilizador

As [ReivindicaçõesProviderSelecções](userjourneys.md#claimsproviderselection) numa jornada de utilizador define a lista de opções de seleção do fornecedor de sinistros e a sua encomenda. Com o elemento **EnabledForUserJourneys** filtra-se, que o fornecedor alega estar disponível para o utilizador. O elemento **EnabledForUserJourneys** contém um dos seguintes valores:

- **Sempre,** execute o perfil técnico.
- **Nunca,** ignore o perfil técnico.
- **O OnClaimsExistence** executa apenas quando existe uma determinada reclamação, especificada no perfil técnico.
- **OnItemExistenceInStringCollectionClaim,** execute apenas quando um item existe numa reclamação de coleção de cordas.
- **OnItemAbsenceInStringCollectionClaim** executam apenas quando um item não existe numa reclamação de coleção de cordas.

Utilizando **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim**, ou **OnItemAbsenceInStringCollectionClaim,** requer que forneça os seguintes metadados: 

- **ClaimTypeOnWhichToEnable** - especifica o tipo de reclamação que deve ser avaliado.
- **ClaimValueOnWhichToEnable** - especifica o valor que deve ser comparado.

O seguinte perfil técnico só é executado se a coleção de cordas **IdentityProviders** contiver o valor `facebook.com` de:

```xml
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
      <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
      <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>
```
