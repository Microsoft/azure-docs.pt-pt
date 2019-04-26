---
title: Exemplos de transformação de afirmações de conta de redes sociais para a identidade experiência de estrutura de esquema do Azure Active Directory B2C | Documentos da Microsoft
description: Conta de rede social de exemplos de transformação de afirmações para a identidade experiência de estrutura de esquema do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 53608654392d7efb73b6dadac14f01a94bb035a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60360372"
---
# <a name="social-accounts-claims-transformations"></a>Transformação de declarações de contas de redes sociais

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

No Azure Active Directory (Azure AD) B2C, as identidades de contas de redes sociais são armazenadas num `userIdentities` atributo de um **alternativeSecurityIdCollection** tipo de afirmação. Cada item da **alternativeSecurityIdCollection** Especifica o emissor (identity provider nome, como facebook.com) e o `issuerUserId`, que é um identificador de utilizador exclusivo para o emissor.

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

Este artigo fornece exemplos para usar as transformações de afirmações de conta de rede social do esquema de arquitetura de experiências de identidade no Azure AD B2C. Para obter mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="createalternativesecurityid"></a>CreateAlternativeSecurityId

Cria uma representação JSON de propriedade de alternativeSecurityId do utilizador que pode ser utilizada nas chamadas para o Azure Active Directory. Para obter mais informações, consulte [esquema do AlternativeSecurityId](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#AlternativeSecurityIdType).

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | key | string | ClaimType que especifica o identificador de utilizador exclusivo utilizado pelo fornecedor de identidade de redes sociais. |
| InputClaim | identityProvider | string | ClaimType que especifica o nome de fornecedor de identidade de conta de redes sociais, como facebook.com. |
| OutputClaim | alternativeSecurityId | string | O ClaimType é produzido a ClaimsTransformation po vyvolání. Contém informações sobre a identidade de um utilizador de conta de redes sociais. O **emissor** é o valor da `identityProvider` de afirmação. O **issuerUserId** é o valor da `key` de afirmação no formato base64. |

Utilize este afirmações de transformação para gerar um `alternativeSecurityId` ClaimType. É utilizada por todas as identidades sociais fornecedor perfis técnicos, como `Facebook-OAUTH`. A transformação de afirmações seguintes recebe o ID de conta de rede social do utilizador e o nome do fornecedor de identidade. A saída deste perfil técnico é um formato de cadeia de caracteres do JSON que pode ser utilizado em serviços de diretório do Azure AD.

```XML
<ClaimsTransformation Id="CreateAlternativeSecurityId" TransformationMethod="CreateAlternativeSecurityId">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="socialIdpUserId" TransformationClaimType="key" />
    <InputClaim ClaimTypeReferenceId="identityProvider" TransformationClaimType="identityProvider" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="alternativeSecurityId" TransformationClaimType="alternativeSecurityId" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Afirmações de entrada:
    - **key**: 12334
    - **identityProvider**: Facebook.com
- Afirmações de saída:
    - **alternativeSecurityId**: { "issuer": "facebook.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection

Adiciona uma `AlternativeSecurityId` para um `alternativeSecurityIdCollection` de afirmação.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | string | ClaimType sejam adicionados ao afirmações de saída. |
| InputClaim | coleção | alternativeSecurityIdCollection | Os ClaimTypes que são utilizados pela transformação de declarações, se disponíveis na política. Se for fornecido, a transformação de declarações adiciona o `item` no final da coleção. |
| OutputClaim | coleção | alternativeSecurityIdCollection | Os ClaimTypes que são produzidos este ClaimsTransformation po vyvolání. A nova coleção que contém tanto os itens de entrada `collection` e `item`. |

O exemplo seguinte liga a uma nova identidade de redes sociais com uma conta existente. Para ligar uma nova identidade de redes sociais:
1. Na **AAD UserReadUsingAlternativeSecurityId** e **AAD UserReadUsingObjectId** perfis técnicos, de saída do utilizador **alternativeSecurityIds** de afirmação.
1. Pedir ao utilizador para iniciar sessão com um dos fornecedores de identidade que não estão associados este utilizador.
1. Utilizar o **CreateAlternativeSecurityId** afirmações de transformação, crie uma nova **alternativeSecurityId** tipo com o nome de afirmação `AlternativeSecurityId2`
1. Chamar o **AddItemToAlternativeSecurityIdCollection** afirmações de transformação para adicionar o **AlternativeSecurityId2** alegam existente **AlternativeSecurityIds** afirmação.
1. Manter o **alternativeSecurityIds** de afirmação para a conta de utilizador

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

- Afirmações de entrada:
    - **item**: { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" }
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
- Afirmações de saída:
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation

Devolve a lista de emissores do **alternativeSecurityIdCollection** afirmação num novo **stringCollection** de afirmação.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | alternativeSecurityIdCollection | alternativeSecurityIdCollection | ClaimType a ser utilizado para obter a lista de fornecedores de identidade (emissor). |
| OutputClaim | identityProvidersCollection | stringCollection | Os ClaimTypes que são produzidos este ClaimsTransformation po vyvolání. Lista de fornecedores de identidade associar alternativeSecurityIdCollection de afirmações de entrada |

A seguinte transformação de afirmações lê o usuário **alternativeSecurityIds** afirmação e extrai a lista de nomes do fornecedor de identidade associada à mesma. Utilize a saída **identityProvidersCollection** para mostrar a lista de fornecedores de identidade associado à conta de utilizador. Ou, na página de seleção do fornecedor de identidade, filtrar a lista de fornecedores de identidade com base na saída **identityProvidersCollection** de afirmação. Por isso, o usuário pode selecionar para a nova identidade de redes sociais que já não está associada com a conta de ligação.

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

- Afirmações de entrada:
    - **alternativeSecurityIdCollection**: [ { "issuer": "google.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Afirmações de saída:
    - **identityProvidersCollection**: [ "facebook.com", "google.com" ]

## <a name="removealternativesecurityidbyidentityprovider"></a>RemoveAlternativeSecurityIdByIdentityProvider

Remove uma **AlternativeSecurityId** de um **alternativeSecurityIdCollection** de afirmação.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | identityProvider | string | ClaimType que contém o nome do fornecedor de identidade a ser removido da coleção. |
| InputClaim | coleção | alternativeSecurityIdCollection | Os ClaimTypes que são utilizados pela transformação de declarações. A transformação de declarações remove o identityProvider da coleção. |
| OutputClaim | coleção | alternativeSecurityIdCollection | Os ClaimTypes que são produzidos este ClaimsTransformation po vyvolání. A nova coleção, depois do identityProvider removido da coleção. |

O exemplo seguinte ser desvinculada uma identidade de redes sociais com uma conta existente. Para desassociar uma identidade de redes sociais:
1. Na **AAD UserReadUsingAlternativeSecurityId** e **AAD UserReadUsingObjectId** perfis técnicos, de saída do utilizador **alternativeSecurityIds** de afirmação.
2. Peça ao utilizador que selecione a conta de redes sociais para remover os fornecedores de identidade de lista que estão associados este utilizador.
3. Chamar um perfil técnico o transformação de afirmações que chama o **RemoveAlternativeSecurityIdByIdentityProvider** transformação, que remover a identidade de redes sociais selecionada, com o nome do fornecedor de identidade de afirmações.
4. Manter o **alternativeSecurityIds** de afirmação para a conta de utilizador.

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

- Afirmações de entrada:
    - **identityProvider**: facebook.com
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Afirmações de saída:
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
