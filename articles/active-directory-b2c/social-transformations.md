---
title: Exemplos de transformação de declarações de conta social para o esquema de estrutura de experiência de identidade de Azure Active Directory B2C | Microsoft Docs
description: Exemplos de transformação de declarações de conta social para o esquema de estrutura de experiência de identidade de Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: cd4839e2c8ad6605a29f3c8b824375185384f78c
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71258155"
---
# <a name="social-accounts-claims-transformations"></a>Transformações de declarações de contas sociais

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

No Azure Active Directory B2C (Azure ad B2C), as identidades de conta social são `userIdentities` armazenadas em um atributo de um tipo de Declaração **alternativeSecurityIdCollection** . Cada item no **alternativeSecurityIdCollection** especifica o emissor (nome do provedor de identidade, como Facebook.com) e o `issuerUserId`, que é um identificador de usuário exclusivo para o emissor.

```JSON
"userIdentities": [{
    "issuer": "google.com",
    "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"
  },
  {
    "issuer": "facebook.com",
    "issuerUserId": "MTIzNDU="
  }]
```

Este artigo fornece exemplos de como usar as transformações de declarações de conta social do esquema de estrutura de experiência de identidade no Azure AD B2C. Para obter mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="createalternativesecurityid"></a>CreateAlternativeSecurityId

Cria uma representação JSON da propriedade alternativeSecurityId do usuário que pode ser usada nas chamadas para Azure Active Directory. Para obter mais informações, consulte [esquema do AlternativeSecurityId](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#alternativesecurityid-type).

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | key | Cadeia de caracteres | O ClaimType que especifica o identificador de usuário exclusivo usado pelo provedor de identidade social. |
| InputClaim | identityProvider | Cadeia de caracteres | O ClaimType que especifica o nome do provedor de identidade da conta social, como facebook.com. |
| OutputClaim | alternativeSecurityId | Cadeia de caracteres | O ClaimType que é produzido após a chamada de ClaimsTransformation. Contém informações sobre a identidade de um usuário de conta social. O **emissor** é o valor da `identityProvider` declaração. O **issuerUserId** é o valor da `key` declaração no formato base64. |

Use essa transformação de declarações para gerar `alternativeSecurityId` um ClaimType. Ele é usado por todos os perfis técnicos do provedor de identidade social `Facebook-OAUTH`, como. A transformação declarações a seguir recebe a ID da conta social do usuário e o nome do provedor de identidade. A saída desse perfil técnico é um formato de cadeia de caracteres JSON que pode ser usado nos serviços de diretório do Azure AD.

```XML
<ClaimsTransformation Id="CreateAlternativeSecurityId" TransformationMethod="CreateAlternativeSecurityId">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="key" />
    <InputClaim ClaimTypeReferenceId="identityProvider" TransformationClaimType="identityProvider" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="alternativeSecurityId" TransformationClaimType="alternativeSecurityId" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **chave**: 12334
    - **identityProvider**: Facebook.com
- Declarações de saída:
    - **alternativeSecurityId**: { "issuer": "facebook.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection

Adiciona um `AlternativeSecurityId` a uma `alternativeSecurityIdCollection` declaração.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | Cadeia de caracteres | O ClaimType a ser adicionado à declaração de saída. |
| InputClaim | coleção | alternativeSecurityIdCollection | Os ClaimTypes que são usados pela transformação de declarações, se disponíveis na política. Se fornecido, a transformação declarações adiciona o `item` no final da coleção. |
| OutputClaim | coleção | alternativeSecurityIdCollection | O ClaimTypes que é produzido depois desse ClaimsTransformation foi invocado. A nova coleção que contém os itens de entrada `collection` e. `item` |

O exemplo a seguir vincula uma nova identidade social a uma conta existente. Para vincular uma nova identidade social:
1. Nos perfis técnicos **AAD-UserReadUsingAlternativeSecurityId** e **AAD-UserReadUsingObjectId** , gere a Declaração **alternativeSecurityIds** do usuário.
1. Peça ao usuário para entrar com um dos provedores de identidade que não estão associados a esse usuário.
1. Usando a transformação de declarações **CreateAlternativeSecurityId** , crie um novo tipo de Declaração **alternativeSecurityId** com um nome de`AlternativeSecurityId2`
1. Chame a transformação declarações **AddItemToAlternativeSecurityIdCollection** para adicionar a Declaração **AlternativeSecurityId2** à Declaração **AlternativeSecurityIds** existente.
1. Persistir a Declaração **alternativeSecurityIds** para a conta de usuário

```XML
<ClaimsTransformation Id="AddAnotherAlternativeSecurityId" TransformationMethod="AddItemToAlternativeSecurityIdCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId2" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **item**: { "issuer": "facebook.com", "issuerUserId": "MTIzNDU ="}
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
- Declarações de saída:
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation

Retorna a lista de emissores da Declaração **alternativeSecurityIdCollection** para uma nova declaração **StringCollection** .

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | alternativeSecurityIdCollection | alternativeSecurityIdCollection | O ClaimType a ser usado para obter a lista de provedores de identidade (emissor). |
| OutputClaim | identityProvidersCollection | stringCollection | O ClaimTypes que é produzido depois desse ClaimsTransformation foi invocado. Lista de provedores de identidade associados à declaração de entrada alternativeSecurityIdCollection |

A transformação declarações a seguir lê a Declaração **alternativeSecurityIds** do usuário e extrai a lista de nomes de provedores de identidade associados a essa conta. Use **identityProvidersCollection** de saída para mostrar ao usuário a lista de provedores de identidade associados à conta. Ou, na página de seleção do provedor de identidade, filtre a lista de provedores de identidade com base na declaração de **identityProvidersCollection** de saída. Portanto, o usuário pode optar por vincular uma nova identidade social que ainda não está associada à conta.

```XML
<ClaimsTransformation Id="ExtractIdentityProviders" TransformationMethod="GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityIds" TransformationClaimType="alternativeSecurityIdCollection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="identityProviders" TransformationClaimType="identityProvidersCollection" />
  </OutputClaims>
</ClaimsTransformation>
```

- Declarações de entrada:
    - **alternativeSecurityIdCollection**: [ { "issuer": "google.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Declarações de saída:
    - **identityProvidersCollection**: [ "facebook.com", "google.com" ]

## <a name="removealternativesecurityidbyidentityprovider"></a>RemoveAlternativeSecurityIdByIdentityProvider

Remove um **AlternativeSecurityId** de uma declaração **alternativeSecurityIdCollection** .

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | identityProvider | Cadeia de caracteres | O ClaimType que contém o nome do provedor de identidade a ser removido da coleção. |
| InputClaim | coleção | alternativeSecurityIdCollection | O ClaimTypes que são usados pela transformação de declarações. A transformação declarações remove a identidadeprovider da coleção. |
| OutputClaim | coleção | alternativeSecurityIdCollection | O ClaimTypes que é produzido depois desse ClaimsTransformation foi invocado. A nova coleção, após o identityprovider ser removido da coleção. |

O exemplo a seguir desvincula uma das identidades sociais com uma conta existente. Para desvincular uma identidade social:
1. Nos perfis técnicos **AAD-UserReadUsingAlternativeSecurityId** e **AAD-UserReadUsingObjectId** , gere a Declaração **alternativeSecurityIds** do usuário.
2. Peça ao usuário para selecionar qual conta social deve ser removida da lista de provedores de identidade que estão associados a esse usuário.
3. Chamar um perfil técnico de transformação de declarações que chama a transformação de declarações **RemoveAlternativeSecurityIdByIdentityProvider** , que removeu a identidade social selecionada, usando o nome do provedor de identidade.
4. Persistir a Declaração **alternativeSecurityIds** para a conta de usuário.

```XML
<ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider" TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="secondIdentityProvider" TransformationClaimType="identityProvider" />
        <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformations>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **identityProvider**: facebook.com
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Declarações de saída:
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
