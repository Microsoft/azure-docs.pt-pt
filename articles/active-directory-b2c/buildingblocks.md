---
title: BuildingBlocks
titleSuffix: Azure AD B2C
description: Especifique o elemento BuildingBlocks de uma política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a42c1d06051c283f0e911c4cd166884ddd060f45
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633279"
---
# <a name="buildingblocks"></a>BuildingBlocks

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O elemento **BuildingBlocks** é adicionado dentro do elemento [TrustFrameworkPolicy.](trustframeworkpolicy.md)

```xml
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">

  <BuildingBlocks>
    <ClaimsSchema>
      ...
    </ClaimsSchema>
    <Predicates>
    ...
    </Predicates>
    <PredicateValidations>
    ...
    </PredicateValidations>
    <ClaimsTransformations>
      ...
    </ClaimsTransformations>
    <ContentDefinitions>
      ...
    </ContentDefinitions>
    <Localization>
      ...
    </Localization>
    <DisplayControls>
      ...
    </DisplayControls>
 </BuildingBlocks>
```

O elemento **Blocos de Construção** contém os seguintes elementos que devem ser especificados na ordem definida:

- [SinistrosSchema](claimsschema.md) - Define os tipos de reclamação que podem ser referenciados como parte da apólice. O esquema de reclamações é o local onde declara os seus tipos de reclamações. Um tipo de reivindicação é semelhante a uma variável em muitas línguas programáticas. Pode utilizar o tipo de reclamação para recolher dados do utilizador da sua aplicação, receber reclamações de fornecedores de identidade social, enviar e receber dados de uma API rest personalizada, ou armazenar quaisquer dados internos utilizados pela sua política personalizada.

- [Predicados e PredicateValidationsInput](predicates.md) - Permite-lhe executar um processo de validação para garantir que apenas os dados devidamente formados são introduzidos numa reclamação.

- [SinistrosTransformações](claimstransformations.md) - Contém uma lista de transformações de sinistros que podem ser usadas na sua política.  Uma transformação de sinistros converte uma reivindicação em outra. Na transformação de sinistros, especifica-se um método de transformação, como:
  - Alterando o caso de uma alegação de corda para a especificada. Por exemplo, mudar uma corda de minúscula para maiúscula.
  - Comparando duas reclamações e devolvendo uma reclamação com a verdade indicando que as alegações coincidem, caso contrário, falsas.
  - Criar uma reivindicação de cordas a partir do parâmetro fornecido na política.
  - Criar uma corda aleatória utilizando o gerador de números aleatórios.
  - Formatação de uma reclamação de acordo com a cadeia de formato fornecida. Esta transformação utiliza o `String.Format` método C#.

- InputValidation - Este elemento permite-lhe realizar agregações booleanas semelhantes a *e ou* *.*

- [ConteúdoDefinitions](contentdefinitions.md) - Contém URLs para modelos HTML5 para utilizar na sua jornada do utilizador. Numa política personalizada, uma definição de conteúdo define o URI de página HTML5 que é usado para um passo especificado na jornada do utilizador. Por exemplo, a inscrição ou inscrição, o reset da palavra-passe ou as páginas de erro. Pode modificar o aspeto e a sensação, sobrepor-se ao LoadUri para o ficheiro HTML5. Ou pode criar novas definições de conteúdo de acordo com as suas necessidades. Este elemento pode conter uma referência de recursos localizados utilizando um ID de localização.

- [Localização](localization.md) - Permite-lhe suportar vários idiomas. O apoio à localização nas políticas permite-lhe configurar a lista de línguas apoiadas numa política e escolher uma língua predefinida. As cordas e coleções específicas da linguagem também são suportadas.

- [Controlos do Visor](display-controls.md) - Define os controlos a exibir numa página. Os controlos do ecrã têm uma funcionalidade especial e interagem com perfis técnicos de validação de back-end. 
