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
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a788134f64066b0469d34fbfbacacd8c45438bde
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85203151"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um elemento **TechnicalProfiles** contém um conjunto de perfis técnicos apoiados pelo fornecedor de sinistros. Todos os prestadores de sinistros devem ter um ou mais perfis técnicos que determinem os pontos finais e os protocolos necessários para comunicar com o fornecedor de sinistros. Um fornecedor de sinistros pode ter vários perfis técnicos.

```xml
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

O elemento **TécnicoProfile** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
|---------|---------|---------|
| Id | Sim | Um identificador único do perfil técnico. O perfil técnico pode ser referenciado utilizando este identificador a partir de outros elementos do ficheiro de política. Por exemplo, **OrquestrationSteps** e **ValidationTechnicalProfile**. |

O **Ficheiro Técnico** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Domínio | 0:1 | O nome de domínio para o perfil técnico. Por exemplo, se o seu perfil técnico especificar o fornecedor de identidade do Facebook, o nome de domínio é Facebook.com. |
| DisplayName | 1:1 | O nome do perfil técnico que pode ser apresentado aos utilizadores. |
| Descrição | 0:1 | A descrição do perfil técnico que pode ser apresentado aos utilizadores. |
| Protocolo | 0:1 | O protocolo usado para a comunicação com a outra parte. |
| Metadados | 0:1 | Uma coleção de pares chave/valor que são utilizados pelo protocolo para comunicar com o ponto final no decurso de uma transação. |
| InputTokenFormat | 0:1 | O formato do token de entrada. Valores possíveis: `JSON` `JWT` , , ou `SAML11` `SAML2` . O `JWT` valor representa um Token Web JSON de acordo com a especificação IETF. O `SAML11` valor representa um sinal de segurança SAML 1.1 de acordo com a especificação OASIS.  O `SAML2` valor representa um sinal de segurança SAML 2.0 de acordo com a especificação OASIS. |
| OutputTokenFormat | 0:1 | O formato do token de saída. Valores possíveis: `JSON` `JWT` , , ou `SAML11` `SAML2` . |
| Chaves Criptográficas | 0:1 | Uma lista de chaves criptográficas que são utilizadas no perfil técnico. |
| InputClaimsTransformações | 0:1 | Uma lista de referências previamente definidas a transformações de sinistros que devem ser executadas antes de quaisquer reclamações serem enviadas ao prestador de sinistros ou à parte de sinistros. |
| InputClaims | 0:1 | Uma lista das referências previamente definidas aos tipos de reclamação que são tomados como entrada no perfil técnico. |
| PersistidoClaims | 0:1 | Uma lista das referências previamente definidas aos tipos de reclamação que são persistidos pelo prestador de sinistros que se relaciona com o perfil técnico. |
| DisplayClaims | 0:1 | Uma lista das referências previamente definidas aos tipos de sinistros que são apresentados pelo prestador de sinistros que se relaciona com o [perfil técnico autoafirmado.](self-asserted-technical-profile.md) A função DisplayClaims encontra-se atualmente em **pré-visualização**. |
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

O elemento **Protocolo** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Nome | Sim | O nome de um protocolo válido suportado pelo Azure AD B2C que é utilizado como parte do perfil técnico. Valores possíveis: `OAuth1` , , , , ou `OAuth2` `SAML2` `OpenIdConnect` `Proprietary` `None` . |
| Processador | Não | Quando o nome do protocolo estiver definido para `Proprietary` , especifique o nome totalmente qualificado do conjunto que é utilizado pela Azure AD B2C para determinar o manipulador de protocolo. |

## <a name="metadata"></a>Metadados

Um elemento **metadados** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Item | 0:n | Os metadados que se relacionam com o perfil técnico. Cada tipo de perfil técnico tem um conjunto diferente de itens de metadados. Consulte a secção de tipos de perfis técnicos, para obter mais informações. |

### <a name="item"></a>Item

O elemento **item** do elemento **Metadados** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Chave | Sim | A chave dos metadados. Consulte cada tipo de perfil técnico, para obter a lista de itens de metadados. |

## <a name="cryptographickeys"></a>Chaves Criptográficas

O elemento **CryptographicKeys** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Chave | 1:n | Uma chave criptográfica utilizada neste perfil técnico. |

### <a name="key"></a>Chave

O elemento **chave** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Id | Não | Um identificador único de um determinado par de chaves referenciado a partir de outros elementos no ficheiro de política. |
| StorageReferenceId | Sim | Um identificador de um recipiente chave de armazenamento referenciado a partir de outros elementos do ficheiro de política. |

## <a name="inputclaimstransformations"></a>InputClaimsTransformações

O elemento **InputClaimsTransformations** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| InputClaimsTransformação | 1:n | O identificador de uma transformação de sinistros que deve ser executada antes de quaisquer reclamações serem enviadas ao prestador de sinistros ou à parte de confiança. Uma transformação de sinistros pode ser usada para modificar as reivindicações existentesSsSchema ou gerar novas. |

### <a name="inputclaimstransformation"></a>InputClaimsTransformação

O elemento **InputClaimsTransformation** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de uma transformação de sinistros já definida no ficheiro de política ou no ficheiro de política dos pais. |

## <a name="inputclaims"></a>InputClaims

O elemento **InputClaims** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | Um tipo de pedido de entrada esperado. |

### <a name="inputclaim"></a>InputClaim

O elemento **InputClaim** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Sim | O identificador de um tipo de reclamação já definido na secção ClaimsSchema no ficheiro de política ou no ficheiro de política dos pais. |
| PadrãoValue | Não | Um valor predefinido a utilizar para criar uma reclamação se a reclamação indicada pelo ClaimTypeReferenceId não existir para que a reclamação resultante possa ser utilizada como Um InputClaim pelo perfil técnico. |
| PartnerClaimType | Não | O identificador do tipo de reclamação do parceiro externo a que a apólice especificada tipo reivindicação mapeia. Se o atributo PartnerClaimType não for especificado, então o tipo de reclamação de política especificado é mapeado para o tipo de reclamação do parceiro com o mesmo nome. Use esta propriedade quando o seu nome de tipo reivindicação é diferente da outra parte. Por exemplo, o primeiro nome de reclamação é "dado Nome", enquanto o parceiro utiliza uma reclamação denominada "first_name". |

## <a name="displayclaims"></a>DisplayClaims

O elemento **DisplayClaims** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| DisplayClaim | 1:n | Um tipo de pedido de entrada esperado. |

A funcionalidade DislayClaims está atualmente em **pré-visualização**.

### <a name="displayclaim"></a>DisplayClaim

O elemento **DisplayClaim** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Não | O identificador de um tipo de reclamação já definido na secção ClaimsSchema no ficheiro de política ou no ficheiro de política dos pais. |
| DisplayControlReferenceId | Não | O identificador de um controlo de [exibição](display-controls.md) já definido na secção ClaimsSchema no ficheiro de política ou no ficheiro de política dos pais. |
| Necessário | Não | Indica se a reclamação do visor é necessária. |

O **DisplayClaim** requer que especifique um `ClaimTypeReferenceId` ou `DisplayControlReferenceId` .

### <a name="persistedclaims"></a>PersistidoClaims

O elemento **PersistedClaims** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Reclamação Persistiu | 1:n | O tipo de reivindicação para persistir. |

### <a name="persistedclaim"></a>Reclamação Persistiu

O elemento **PersistedClaim** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Sim | O identificador de um tipo de reclamação já definido na secção ClaimsSchema no ficheiro de política ou no ficheiro de política dos pais. |
| PadrãoValue | Não | Um valor predefinido a utilizar para criar uma reclamação se a reclamação indicada pelo ClaimTypeReferenceId não existir para que a reclamação resultante possa ser utilizada como Um InputClaim pelo perfil técnico. |
| PartnerClaimType | Não | O identificador do tipo de reclamação do parceiro externo a que a apólice especificada tipo reivindicação mapeia. Se o atributo PartnerClaimType não for especificado, então o tipo de reclamação de política especificado é mapeado para o tipo de reclamação do parceiro com o mesmo nome. Use esta propriedade quando o seu nome de tipo reivindicação é diferente da outra parte. Por exemplo, o primeiro nome de reclamação é "dado Nome", enquanto o parceiro utiliza uma reclamação denominada "first_name". |

## <a name="outputclaims"></a>Resultados

O elemento **OutputClaims** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| OutputClaim | 1:n | Um tipo de reivindicação de saída esperada. |

### <a name="outputclaim"></a>OutputClaim

O elemento **OutputClaim** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Sim | O identificador de um tipo de reclamação já definido na secção ClaimsSchema no ficheiro de política ou no ficheiro de política dos pais. |
| PadrãoValue | Não | Um valor predefinido a utilizar para criar uma reclamação se a reclamação indicada pelo ClaimTypeReferenceId não existir para que a reclamação resultante possa ser utilizada como Um InputClaim pelo perfil técnico. |
|AlwaysUseDefaultValue |Não |Forçar o uso do valor padrão.  |
| PartnerClaimType | Não | O identificador do tipo de reclamação do parceiro externo a que a apólice especificada tipo reivindicação mapeia. Se o atributo PartnerClaimType não for especificado, então o tipo de reclamação de política especificado é mapeado para o tipo de reclamação do parceiro com o mesmo nome. Use esta propriedade quando o seu nome de tipo reivindicação é diferente da outra parte. Por exemplo, o primeiro nome de reclamação é "dado Nome", enquanto o parceiro utiliza uma reclamação denominada "first_name". |

## <a name="outputclaimstransformations"></a>ResultadosClaimsTransformações

O elemento **OutputClaimsTransformations** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| SaídaClaimsTransformação | 1:n | Os identificadores das transformações de sinistros que devem ser executados antes de quaisquer reclamações serem enviadas ao prestador de sinistros ou à parte de confiança. Uma transformação de sinistros pode ser usada para modificar as reivindicações existentesSsSchema ou gerar novas. |

### <a name="outputclaimstransformation"></a>SaídaClaimsTransformação

O elemento **OutputClaimsTransformation** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de uma transformação de sinistros já definida no ficheiro de política ou no ficheiro de política dos pais. |

## <a name="validationtechnicalprofiles"></a>ValidaçãoTechnicalProfiles

O elemento **ValidationTechnicalProfiles** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| ValidaçãoTechnicalProfile | 1:n | Os identificadores de perfis técnicos utilizados validam algumas ou todas as reivindicações de saída do perfil técnico de referência. Todas as alegações de entrada do perfil técnico referenciado devem figurar nas alegações de saída do perfil técnico de referência. |

### <a name="validationtechnicalprofile"></a>ValidaçãoTechnicalProfile

O elemento **ValidationTechnicalProfile** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de um perfil técnico já definido no ficheiro de política ou no ficheiro de política dos pais. |

## <a name="subjectnaminginfo"></a>Nomeação de AssuntosInfo

O **SubjectNamingInfo** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ClaimType | Sim | Um identificador de um tipo de reclamação já definido na secção ClaimsSchema no ficheiro de política. |

## <a name="includetechnicalprofile"></a>Incluir TecnologiaProfile

O elemento **IncludeTechnicalProfile** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de um perfil técnico já definido no ficheiro de política ou no ficheiro de política dos pais. |

## <a name="usetechnicalprofileforsessionmanagement"></a>UseTechnicalProfileForSsionManagement

O elemento **UseTechnicalProfileForSessionManagement** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de um perfil técnico já definido no ficheiro de política ou no ficheiro de política dos pais. |

## <a name="enabledforuserjourneys"></a>EnabledForUserJourneys

As **ReivindicaçõesProviderSelecções** numa jornada de utilizador define a lista de opções de seleção do fornecedor de sinistros e a sua encomenda. Com o elemento **EnabledForUserJourneys** filtra-se, que o fornecedor alega estar disponível para o utilizador. O elemento **EnabledForUserJourneys** contém um dos seguintes valores:

- **Sempre,** execute o perfil técnico.
- **Nunca,** ignore o perfil técnico.
- **O OnClaimsExistence** executa apenas quando existe uma determinada reclamação, especificada no perfil técnico.
- **OnItemExistenceInStringCollectionClaim,** execute apenas quando um item existe numa reclamação de coleção de cordas.
- **OnItemAbsenceInStringCollectionClaim** executam apenas quando um item não existe numa reclamação de coleção de cordas.

Utilizando **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** ou **OnItemAbsenceInStringCollectionClaim,** exige que forneça os seguintes metadados: **ClaimTypeOnWhichToEnable** especifica o tipo de reclamação que deve ser avaliado, **ClaimValueOnWhichToEnable** especifica o valor que deve ser comparado.

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
