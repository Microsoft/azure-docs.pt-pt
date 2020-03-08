---
title: TechnicalProfiles
titleSuffix: Azure AD B2C
description: Especifique o elemento TechnicalProfiles de uma política personalizada no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5196615b6b935e4d37565298be03ad315163d132
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78668867"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um elemento **TechnicalProfiles** contém um conjunto de perfis técnicos suportados pelo fornecedor de sinistros. Cada prestador de sinistros deve ter um ou mais perfis técnicos que determinem os pontos finais e os protocolos necessários para comunicar com o prestador de sinistros. Um fornecedor de sinistros pode ter vários perfis técnicos.

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

O elemento **Perfil Técnico** contém o seguinte atributo:

| Atributo | Necessário | Descrição |
|---------|---------|---------|
| Id | Sim | Um identificador único do perfil técnico. O perfil técnico pode ser referenciado utilizando este identificador a partir de outros elementos do ficheiro de política. Por exemplo, **OrquestraçõesPassos** e **ValidaçãoPerfil Técnico**. |

O **Perfil Técnico** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Domain | 0:1 | O nome de domínio para o perfil técnico. Por exemplo, se o seu perfil técnico especificar o fornecedor de identidade do Facebook, o nome de domínio é Facebook.com. |
| DisplayName | 1:1 | O nome do perfil técnico que pode ser apresentado aos utilizadores. |
| Descrição | 0:1 | A descrição do perfil técnico que pode ser apresentado aos utilizadores. |
| Protocolo | 0:1 | O protocolo usado para a comunicação com a outra parte. |
| Metadados | 0:1 | Uma coleção de pares chave/valor que são utilizados pelo protocolo para comunicar com o ponto final no decurso de uma transação. |
| InputTokenFormat | 0:1 | O formato do símbolo de entrada. Valores possíveis: `JSON`, `JWT`, `SAML11`ou `SAML2`. O valor `JWT` representa um Token Web JSON de acordo com a especificação IETF. O valor `SAML11` representa um símbolo de segurança SAML 1.1 de acordo com a especificação OASIS.  O valor `SAML2` representa um símbolo de segurança SAML 2.0 de acordo com a especificação OASIS. |
| OutputTokenFormat | 0:1 | O formato do símbolo de saída. Valores possíveis: `JSON`, `JWT`, `SAML11`ou `SAML2`. |
| CryptographicKeys | 0:1 | Uma lista de chaves criptográficas que são usadas no perfil técnico. |
| InputClaimsTransformations | 0:1 | Uma lista de referências previamente definidas às transformações de sinistros que devem ser executadas antes de quaisquer reclamações serem enviadas ao prestador de sinistros ou à parte que depende. |
| Créditos de entrada | 0:1 | Uma lista das referências previamente definidas aos tipos de reclamação que são tomados como entrada no perfil técnico. |
| Reclamações Persistidas | 0:1 | Uma lista das referências previamente definidas aos tipos de reclamação que são persistidas pelo prestador de sinistros que se relacionam com o perfil técnico. |
| DisplayClaims | 0:1 | Uma lista das referências previamente definidas aos tipos de reclamação apresentados pelo prestador de sinistros que se relacionam com o [perfil técnico autoafirmado](self-asserted-technical-profile.md). A função DisplayClaims encontra-se atualmente em **pré-visualização**. |
| OutputClaims | 0:1 | Uma lista das referências previamente definidas aos tipos de reclamação que são tomados como saída no perfil técnico. |
| OutputClaimsTransformations | 0:1 | Uma lista de referências previamente definidas às transformações de sinistros que devem ser executadas após a recebida dos sinistros do prestador de sinistros. |
| ValidationTechnicalProfiles | 0:n | Uma lista de referências a outros perfis técnicos que o perfil técnico utiliza para fins de validação. Para mais informações, consulte [o perfil técnico de validação](validation-technical-profile.md)|
| SubjectNamingInfo | 0:1 | Controla a produção do nome do objeto em fichas em que o nome do assunto é especificado separadamente dos sinistros. Por exemplo, OAuth ou SAML.  |
| Incluir InSso | 0:1 |  Se a utilização deste perfil técnico deve aplicar um comportamento de inscrição única (SSO) para a sessão, ou, em vez disso, exigir interação explícita. Este elemento é válido apenas em perfis Autoafirmado utilizados dentro de um perfil técnico de Validação. Valores possíveis: `true` (predefinido) ou `false`. |
| IncludeClaimsFromTechnicalProfile | 0:1 | Um identificador de um perfil técnico a partir do qual pretende que todas as alegações de entrada e saída sejam adicionadas a este perfil técnico. O perfil técnico referenciado deve ser definido no mesmo ficheiro político. |
| Incluir Perfil Técnico |0:1 | Um identificador de um perfil técnico a partir do qual pretende adicionar todos os dados a este perfil técnico. |
| UseTechnicalProfileForSessionManagement | 0:1 | Um perfil técnico diferente a ser usado para a gestão de sessões. |
|EnabledForUserJourneys| 0:1 |Controle se o perfil técnico for executado numa viagem de utilizador.  |

## <a name="protocol"></a>Protocolo

O elemento **Protocolo** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Nome | Sim | O nome de um protocolo válido suportado pelo Azure AD B2C que é utilizado como parte do perfil técnico. Valores possíveis: `OAuth1`, `OAuth2`, `SAML2`, `OpenIdConnect`, `Proprietary`ou `None`. |
| Manipulador | Não | Quando o nome do protocolo estiver definido para `Proprietary`, especifique o nome totalmente qualificado do conjunto que é utilizado pelo Azure AD B2C para determinar o manipulador de protocolos. |

## <a name="metadata"></a>Metadados

Um elemento **metadados** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Item | 0:n | Os metadados que se relacionam com o perfil técnico. Cada tipo de perfil técnico tem um conjunto diferente de itens de metadados. Consulte a secção de tipos de perfis técnicos, para obter mais informações. |

### <a name="item"></a>Item

O elemento **Item** do elemento **Metadados** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Chave | Sim | A chave dos metadados. Consulte cada tipo de perfil técnico, para obter a lista de itens de metadados. |

## <a name="cryptographickeys"></a>CryptographicKeys

O elemento **CryptographicKeys** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Chave | 1:n | Uma chave criptográfica usada neste perfil técnico. |

### <a name="key"></a>Chave

O elemento **Chave** contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Id | Não | Um identificador único de um par de chaves particular referenciado de outros elementos no ficheiro de política. |
| StorageReferenceId | Sim | Um identificador de um recipiente-chave de armazenamento referenciado de outros elementos no ficheiro de política. |

## <a name="inputclaimstransformations"></a>InputClaimsTransformations

O elemento **InputClaimsTransformations** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1:n | O identificador de uma transformação de sinistros que deve ser executado antes de quaisquer reclamações serem enviadas ao prestador de sinistros ou à parte que depende. Uma transformação de sinistros pode ser usada para modificar as reivindicações existentes da ClaimsSchema ou gerar novas. |

### <a name="inputclaimstransformation"></a>InputClaimsTransformation

O elemento **InputClaimsTransformation** contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de uma transformação de sinistros já definida no ficheiro de política ou no ficheiro de política dos pais. |

## <a name="inputclaims"></a>Créditos de entrada

O elemento **InputClaims** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| inputClaim | 1:n | Um tipo de reclamação de entrada esperado. |

### <a name="inputclaim"></a>inputClaim

O elemento **InputClaim** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Sim | O identificador de um tipo de reclamação já definido na secção ClaimsSchema no ficheiro de política ou no ficheiro da política dos pais. |
| Valor Padrão | Não | Um valor predefinido a utilizar para criar uma reclamação se a reclamação indicada pelo ClaimTypeReferenceId não existir para que a reclamação resultante possa ser usada como InputClaim pelo perfil técnico. |
| Tipo de reclamação de parceiros | Não | O identificador do tipo de reclamação do parceiro externo a que o tipo de reivindicação de política especificado mapeia. Se o atributo Do Tipo de Reclamação de Parceiros não for especificado, então o tipo de reclamação de política especificado é mapeado para o tipo de reclamação do parceiro com o mesmo nome. Utilize esta propriedade quando o nome do tipo de reclamação for diferente da outra parte. Por exemplo, o primeiro nome de reclamação é "givenName", enquanto o parceiro usa uma reclamação chamada 'first_name'. |

## <a name="displayclaims"></a>DisplayClaims

O elemento **DisplayClaims** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Reclamação de exibição | 1:n | Um tipo de reclamação de entrada esperado. |

A função DislayClaims encontra-se atualmente em **pré-visualização**.

### <a name="displayclaim"></a>Reclamação de exibição

O elemento **DisplayClaim** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Não | O identificador de um tipo de reclamação já definido na secção ClaimsSchema no ficheiro de política ou no ficheiro da política dos pais. |
| DisplayControlReferenceid | Não | O identificador de um controlo de [exibição](display-controls.md) já definido na secção ClaimsSchema no ficheiro de política ou no ficheiro de política dos pais. |
| Necessário | Não | Indica se a alegação de exibição é necessária. |

O **DisplayClaim** requer que especifique um `ClaimTypeReferenceId` ou `DisplayControlReferenceId`.

### <a name="persistedclaims"></a>Reclamações Persistidas

O elemento **PersistedClaims** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Reclamação persistida | 1:n | O tipo de reivindicação para persistir. |

### <a name="persistedclaim"></a>Reclamação persistida

O elemento **PersistedClaim** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Sim | O identificador de um tipo de reclamação já definido na secção ClaimsSchema no ficheiro de política ou no ficheiro da política dos pais. |
| Valor Padrão | Não | Um valor predefinido a utilizar para criar uma reclamação se a reclamação indicada pelo ClaimTypeReferenceId não existir para que a reclamação resultante possa ser usada como InputClaim pelo perfil técnico. |
| Tipo de reclamação de parceiros | Não | O identificador do tipo de reclamação do parceiro externo a que o tipo de reivindicação de política especificado mapeia. Se o atributo Do Tipo de Reclamação de Parceiros não for especificado, então o tipo de reclamação de política especificado é mapeado para o tipo de reclamação do parceiro com o mesmo nome. Utilize esta propriedade quando o nome do tipo de reclamação for diferente da outra parte. Por exemplo, o primeiro nome de reclamação é "givenName", enquanto o parceiro usa uma reclamação chamada 'first_name'. |

## <a name="outputclaims"></a>OutputClaims

O elemento **OutputClaims** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| OutputClaim | 1:n | Um tipo de reivindicação de saída esperado. |

### <a name="outputclaim"></a>OutputClaim

O elemento **OutputClaim** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Sim | O identificador de um tipo de reclamação já definido na secção ClaimsSchema no ficheiro de política ou no ficheiro da política dos pais. |
| Valor Padrão | Não | Um valor predefinido a utilizar para criar uma reclamação se a reclamação indicada pelo ClaimTypeReferenceId não existir para que a reclamação resultante possa ser usada como InputClaim pelo perfil técnico. |
|AlwaysUseDefaultValue |Não |Forçar o uso do valor predefinido.  |
| Tipo de reclamação de parceiros | Não | O identificador do tipo de reclamação do parceiro externo a que o tipo de reivindicação de política especificado mapeia. Se o atributo Do Tipo de Reclamação de Parceiros não for especificado, então o tipo de reclamação de política especificado é mapeado para o tipo de reclamação do parceiro com o mesmo nome. Utilize esta propriedade quando o nome do tipo de reclamação for diferente da outra parte. Por exemplo, o primeiro nome de reclamação é "givenName", enquanto o parceiro usa uma reclamação chamada 'first_name'. |

## <a name="outputclaimstransformations"></a>OutputClaimsTransformations

O elemento **OutputClaimsTransformations** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1:n | Os identificadores de transformações de sinistros que devem ser executados antes de quaisquer reclamações serem enviadas ao prestador de sinistros ou à parte que depende. Uma transformação de sinistros pode ser usada para modificar as reivindicações existentes da ClaimsSchema ou gerar novas. |

### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

O elemento **OutputClaimsTransformation** contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de uma transformação de sinistros já definida no ficheiro de política ou no ficheiro de política dos pais. |

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

O elemento **ValidaçãoPerfis Técnicos** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | Os identificadores de perfis técnicos utilizados validam parte ou a toda a parte das alegações de saída do perfil técnico de referência. Todas as reclamações de entrada do perfil técnico referenciado devem figurar nas alegações de saída do perfil técnico de referência. |

### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

O elemento **ValidaçãoTechnicalProfile** contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de um perfil técnico já definido no ficheiro de política ou no ficheiro da política dos pais. |

## <a name="subjectnaminginfo"></a>SubjectNamingInfo

O **SujeitoNamingInfo** contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Tipo de reclamação | Sim | Um identificador de um tipo de reclamação já definido na secção ClaimsSchema no ficheiro de política. |

## <a name="includetechnicalprofile"></a>Incluir Perfil Técnico

O elemento **IncluirPerfil Técnico** contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de um perfil técnico já definido no ficheiro de política, ou ficheiro de política dos pais. |

## <a name="usetechnicalprofileforsessionmanagement"></a>UseTechnicalProfileForSessionManagement

O elemento **UseTechnicalProfileForSessionManagement** contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de um perfil técnico já definido no ficheiro de política ou no ficheiro da política dos pais. |

## <a name="enabledforuserjourneys"></a>EnabledForUserJourneys

As **Seleções Reclamações** numa viagem de utilizador definem a lista de opções de seleção do fornecedor de sinistros e a sua encomenda. Com o elemento **EnabledForUserJourneys** filtra-se, que o fornecedor de sinistros está disponível para o utilizador. O elemento **EnabledForUserJourneys** contém um dos seguintes valores:

- **Sempre,** execute o perfil técnico.
- **Nunca,** ignore o perfil técnico.
- **OnClaimsExistence** só executa quando existe uma determinada reclamação, especificada no perfil técnico.
- **OnItemExistenceInStringCollectionClaim**, execute apenas quando um item existe numa reclamação de recolha de cordas.
- **OnItemAbsenceInStringCollectionClaim** só executa quando um item não existe numa reclamação de recolha de cordas.

Utilizando **onClaimsExistence**, **OnItemExistenceInStringCollectionClaim** ou **OnItemAbsenceInStringCollectionClaim,** requer que forneça os seguintes metadados: **ClaimTypeOnWhichToEnable** especifica o tipo da reclamação que está a ser avaliado, **claimValueOnWhichToEnable** especifica o valor que deve ser comparado.

O seguinte perfil técnico só é executado se a coleção de cordas **identityProviders** contiver o valor de `facebook.com`:

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
