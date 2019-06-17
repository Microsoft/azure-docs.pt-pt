---
title: TechnicalProfiles | Microsoft Docs
description: Especifique o elemento os TechnicalProfiles de uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f712634c83fa290ab24d5e8437a82d5f93af0b7f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66512288"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

R **os TechnicalProfiles** elemento contém um conjunto de perfis técnicos suportado pelo fornecedor de afirmações. Cada fornecedor de afirmações tem de ter um ou mais perfis técnicos que determinam os pontos finais e os protocolos necessários para comunicar com o fornecedor de afirmações. Um fornecedor de afirmações pode ter vários perfis técnicos.

```XML
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">URL of service</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <InputTokenFormat>JWT</InputTokenFormat>
      <OutputTokenFormat>JWT</OutputTokenFormat>
      <CryptographicKeys>
        <Key ID="Key identifier" StorageReferenceId="Storage key container identifier"/>
        ...
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </PersistedClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <OutputClaimsTransformations>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="Technical profile identifier" />
        ...
      </ValidationTechnicalProfiles>
      <SubjectNamingInfo ClaimType="Claim type identifier" />
      <IncludeTechnicalProfile ReferenceId="Technical profile identifier" />
      <UseTechnicalProfileForSessionManagement ReferenceId="Technical profile identifier" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

O **TechnicalProfile** elemento contém o atributo seguinte:

| Atributo | Necessário | Descrição |
|---------|---------|---------|
| Id | Sim | Um identificador exclusivo do perfil técnico. O perfil técnico pode ser referenciado com este identificador de outros elementos no arquivo de política. Por exemplo, **OrchestrationSteps** e **ValidationTechnicalProfile**. |

O **TechnicalProfile** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Domain | 0:1 | O nome de domínio para o perfil técnico. Por exemplo, se o perfil técnico Especifica o fornecedor de identidade do Facebook, o nome de domínio é Facebook.com. |
| displayName | 0:1 | O nome do perfil técnico que pode ser apresentado aos utilizadores. |
| Descrição | 0:1 | A descrição do perfil técnico que pode ser apresentado aos utilizadores. |
| Protocol | 0:1 | O protocolo utilizado para a comunicação com a outra entidade. |
| Metadados | 0:1 | Uma coleção de pares chave/valor que são utilizados pelo protocolo de comunicação com o ponto final no decorrer de uma transação. |
| InputTokenFormat | 0:1 | O formato do token de entrada. Valores possíveis: `JSON`, `JWT`, `SAML11`, ou `SAML2`. O `JWT` valor representa um JSON Web Token de acordo com a especificação IETF. O `SAML11` valor representa um token de segurança de SAML 1.1 de acordo com a especificação OASIS.  O `SAML2` valor representa um token de segurança de acordo com a especificação OASIS SAML 2.0. |
| OutputTokenFormat | 0:1 | O formato do token de saída. Valores possíveis: `JSON`, `JWT`, `SAML11`, ou `SAML2`. |
| CryptographicKeys | 0:1 | Uma lista de chaves criptográficas que são utilizados no perfil técnico. |
| InputClaimsTransformations | 0:1 | Uma lista de referências definidas anteriormente a transformação de declarações que deve ser executada antes de quaisquer afirmações são enviadas para o fornecedor de afirmações ou a entidade confiadora. |
| InputClaims | 0:1 | Uma lista das referências previamente definidas para tipos que são executados como de afirmação de entrada do perfil técnico. |
| PersistedClaims | 0:1 | Uma lista das referências previamente definidas para tipos que são mantidos pelo fornecedor de afirmações que se relaciona com o perfil técnico de afirmação. |
| OutputClaims | 0:1 | Uma lista das referências previamente definidas para tipos que são executados como saída no perfil técnico de afirmação. |
| OutputClaimsTransformations | 0:1 | Uma lista de referências definidas anteriormente a transformação de declarações que deve ser executada após as declarações são recebidas do fornecedor de afirmações. |
| ValidationTechnicalProfiles | 0:n | Uma lista de referências a outros perfis técnicos que o perfil técnico utiliza para fins de validação. Para obter mais informações, consulte [perfil técnico de validação](validation-technical-profile.md)|
| SubjectNamingInfo | 0:1 | Controla a produção de nome do requerente tokens em que o nome do requerente é especificado em separado de afirmações. Por exemplo, OAuth ou SAML.  |
| IncludeClaimsFromTechnicalProfile | 0:1 | Um identificador de um perfil técnico a partir do qual pretende que todas as declarações de entrada e saídas a ser adicionado a este perfil técnico. O perfil técnico referenciado tem de ser definido no mesmo ficheiro de política. |
| IncludeTechnicalProfile |0:1 | Um identificador de um perfil técnico a partir do qual pretende que todos os dados a serem adicionados a este perfil técnico. O perfil técnico referenciado tem de existir no mesmo ficheiro de política. |
| UseTechnicalProfileForSessionManagement | 0:1 | Um perfil de técnico diferente para ser utilizado para gestão de sessão. |
|EnabledForUserJourneys| 0:1 |Controla se o perfil técnico é executado num percurso do utilizador.  |

### <a name="protocol"></a>Protocol

O **protocolo** elemento contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Name | Sim | O nome de um protocolo válido suportado pelo Azure AD B2C, que é utilizado como parte do perfil técnico. Valores possíveis: `OAuth1`, `OAuth2`, `SAML2`, `OpenIdConnect`, `WsFed`, `WsTrust`, `Proprietary`, `session management`, `self-asserted`, ou `None`. |
| Processador | Não | Quando o nome de protocolo está definido como `Proprietary`, especifique o nome completamente qualificado do assembly que é utilizado pelo Azure AD B2C para determinar o manipulador de protocolo. |

### <a name="metadata"></a>Metadados

R **metadados** elemento contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Item | 0:n | Os metadados que se relaciona com o perfil técnico. Cada tipo de perfil técnico tem um conjunto diferente de itens de metadados. Consulte a secção de tipos de perfil técnico, para obter mais informações. |

#### <a name="item"></a>Item

O **Item** elemento da **metadados** elemento contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Chave | Sim | A chave de metadados. Ver cada tipo de perfil técnico, para obter a lista de itens de metadados. |

### <a name="cryptographickeys"></a>CryptographicKeys

O **CryptographicKeys** elemento contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Chave | 1:n | Uma chave criptográfica utilizada neste perfil técnico. |

#### <a name="key"></a>Chave

O **chave** elemento contém o atributo seguinte:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Id | Não | Um identificador exclusivo de um par de chaves específico referenciado a partir de outros elementos no arquivo de política. |
| StorageReferenceId | Sim | Um identificador de um contentor de chaves de armazenamento referenciado a partir de outros elementos no arquivo de política. |

### <a name="inputclaimstransformations"></a>InputClaimsTransformations

O **InputClaimsTransformations** elemento contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1:n | O identificador de uma transformação de afirmações que deve ser executado antes de quaisquer afirmações são enviadas para o fornecedor de afirmações ou a entidade confiadora. Uma transformação de afirmações pode ser utilizada para modificar ClaimsSchema nas declarações existente ou gerar novas etiquetas. |

#### <a name="inputclaimstransformation"></a>InputClaimsTransformation

O **InputClaimsTransformation** elemento contém o atributo seguinte:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de uma transformação de afirmações já definido no ficheiro de política ou ficheiro de política do principal. |

### <a name="inputclaims"></a>InputClaims

O **InputClaims** elemento contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | Tipo de afirmação de entrada esperada. |

#### <a name="inputclaim"></a>InputClaim

O **InputClaim** elemento contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Sim | O identificador de um tipo de afirmação já definido na secção ClaimsSchema no ficheiro de política ou ficheiro de política do principal. |
| defaultValue | Não | Um valor predefinido a utilizar para criar uma afirmação se a afirmação indicado pelo ClaimTypeReferenceId não existe para que a afirmação resultante pode ser utilizada como um InputClaim pelo perfil técnico. |
| PartnerClaimType | Não | É mapeado para o identificador de tipo de afirmação de parceiro externo que a política especificada tipo de afirmação. Se o atributo PartnerClaimType não for especificado, em seguida, o tipo de afirmação de política especificado é mapeado para o tipo de afirmação de parceiro o mesmo nome. Utilize esta propriedade quando o seu nome de tipo de afirmação é diferente da outra parte. Por exemplo, o primeiro nome de afirmação é "givenName", enquanto o parceiro usa uma afirmação com o nome 'first_name'. |

### <a name="persistedclaims"></a>PersistedClaims

O **PersistedClaims** elemento contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| PersistedClaim | 1:n | O tipo de afirmação para manter. |

#### <a name="persistedclaim"></a>PersistedClaim

O **PersistedClaim** elemento contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Sim | O identificador de um tipo de afirmação já definido na secção ClaimsSchema no ficheiro de política ou ficheiro de política do principal. |
| defaultValue | Não | Um valor predefinido a utilizar para criar uma afirmação se a afirmação indicado pelo ClaimTypeReferenceId não existe para que a afirmação resultante pode ser utilizada como um InputClaim pelo perfil técnico. |
| PartnerClaimType | Não | É mapeado para o identificador de tipo de afirmação de parceiro externo que a política especificada tipo de afirmação. Se o atributo PartnerClaimType não for especificado, em seguida, o tipo de afirmação de política especificado é mapeado para o tipo de afirmação de parceiro o mesmo nome. Utilize esta propriedade quando o seu nome de tipo de afirmação é diferente da outra parte. Por exemplo, o primeiro nome de afirmação é "givenName", enquanto o parceiro usa uma afirmação com o nome 'first_name'. |

### <a name="outputclaims"></a>OutputClaims

O **OutputClaims** elemento contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| OutputClaim | 1:n | Tipo de afirmação de um resultado esperado. |

#### <a name="outputclaim"></a>OutputClaim

O **OutputClaim** elemento contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Sim | O identificador de um tipo de afirmação já definido na secção ClaimsSchema no ficheiro de política ou ficheiro de política do principal. |
| defaultValue | Não | Um valor predefinido a utilizar para criar uma afirmação se a afirmação indicado pelo ClaimTypeReferenceId não existe para que a afirmação resultante pode ser utilizada como um InputClaim pelo perfil técnico. |
|AlwaysUseDefaultValue |Não |Força a utilização do valor predefinido.  |
| PartnerClaimType | Não | É mapeado para o identificador de tipo de afirmação de parceiro externo que a política especificada tipo de afirmação. Se o atributo PartnerClaimType não for especificado, em seguida, o tipo de afirmação de política especificado é mapeado para o tipo de afirmação de parceiro o mesmo nome. Utilize esta propriedade quando o seu nome de tipo de afirmação é diferente da outra parte. Por exemplo, o primeiro nome de afirmação é "givenName", enquanto o parceiro usa uma afirmação com o nome 'first_name'. |

### <a name="outputclaimstransformations"></a>OutputClaimsTransformations

O **OutputClaimsTransformations** elemento contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1:n | Os identificadores de transformações de afirmações que devem ser executados antes de quaisquer afirmações são enviadas para o fornecedor de afirmações ou a entidade confiadora. Uma transformação de afirmações pode ser utilizada para modificar ClaimsSchema nas declarações existente ou gerar novas etiquetas. |

#### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

O **OutputClaimsTransformation** elemento contém o atributo seguinte:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de uma transformação de afirmações já definido no ficheiro de política ou ficheiro de política do principal. |

### <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

O **ValidationTechnicalProfiles** elemento contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | Os identificadores de perfis de técnicos que são utilizados validam algumas ou todas as afirmações de saída do perfil técnico referência. Todas as declarações de entrada do perfil técnico referenciado tem de aparecer nas afirmações de saída do perfil técnico referência. |

#### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

O **ValidationTechnicalProfile** elemento contém o atributo seguinte:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de um perfil técnico já definido no ficheiro de política ou ficheiro de política do principal. |

###  <a name="subjectnaminginfo"></a>SubjectNamingInfo

O **SubjectNamingInfo** contém o atributo seguinte:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ClaimType | Sim | Um identificador de um tipo de afirmação já definido na secção ClaimsSchema no ficheiro de política. |

### <a name="includetechnicalprofile"></a>IncludeTechnicalProfile

O **IncludeTechnicalProfile** elemento contém o atributo seguinte:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de um perfil técnico já definido no ficheiro de política ou ficheiro de política do principal. |

### <a name="usetechnicalprofileforsessionmanagement"></a>UseTechnicalProfileForSessionManagement

O **UseTechnicalProfileForSessionManagement** elemento contém o atributo seguinte:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de um perfil técnico já definido no ficheiro de política ou ficheiro de política do principal. |

### <a name="enabledforuserjourneys"></a>EnabledForUserJourneys
O **ClaimsProviderSelections** sessão de um utilizador jornada define a lista de opções de seleção do fornecedor de afirmações e sua ordem. Com o **EnabledForUserJourneys** elemento filtrar, qual o fornecedor de afirmações está disponível para o utilizador. O **EnabledForUserJourneys** elemento contém um dos seguintes valores:

- **Sempre**, execute o perfil técnico.
- **Nunca**, ignore o perfil técnico.
- **OnClaimsExistence** ser executado apenas quando existe uma determinada afirmação, especificada no perfil técnico.
- **OnItemExistenceInStringCollectionClaim**, executar apenas quando existe um item numa afirmação de coleção de cadeia de caracteres.
- **OnItemAbsenceInStringCollectionClaim** executar apenas quando um item não existe uma afirmação de coleção de cadeia de caracteres.

Usando **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** ou **OnItemAbsenceInStringCollectionClaim**, é necessário fornecer o seguinte metadados: **ClaimTypeOnWhichToEnable** Especifica o tipo da afirmação que está a ser avaliado **ClaimValueOnWhichToEnable** Especifica o valor que está a ser comparado.

O perfil técnico seguinte é executado apenas se o **identityProviders** coleção de cadeia de caracteres contém o valor da `facebook.com`:

```XML
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
