---
title: BuildingBlocks
titleSuffix: Azure AD B2C
description: Especifique o elemento BuildingBlocks de uma política personalizada no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cf9c333af48e1c148dcd20ccf5ce91b284bd728b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189876"
---
# <a name="buildingblocks"></a>BuildingBlocks

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O elemento **BuildingBlocks** é adicionado dentro do elemento [TrustFrameworkPolicy.](trustframeworkpolicy.md)

```XML
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

O elemento **BuildingBlocks** contém os seguintes elementos que devem ser especificados na ordem definida:

- [ClaimsSchema](claimsschema.md) - Define os tipos de reclamação que podem ser referenciados como parte da política. O esquema de reclamações é o lugar onde declara os seus tipos de reclamações. Um tipo de reivindicação é semelhante a uma variável em muitas línguas programáticas. Pode utilizar o tipo de reclamação para recolher dados do utilizador da sua aplicação, receber reclamações de fornecedores de identidade social, enviar e receber dados de uma API REST personalizada, ou armazenar quaisquer dados internos utilizados pela sua política personalizada.

- [Predicados e PredicadosValidaçõesInputput -](predicates.md) Permite-lhe realizar um processo de validação para garantir que apenas os dados devidamente formados são introduzidos numa reclamação.

- [ClaimsTransformations](claimstransformations.md) - Contém uma lista de transformações de sinistros que podem ser usadas na sua política.  Uma transformação de reivindicações converte uma reivindicação em outra. Na transformação de sinistros, especifica-se um método de transformação, como:
  - Alterar o caso de uma reclamação de cordas para a especificada. Por exemplo, mudar uma corda de minúscula para maiúscula.
  - Comparar duas reclamações e devolver uma reclamação com o facto de as reclamações corresponderem, caso contrário, falsas.
  - Criando uma reivindicação de cordas a partir do parâmetro fornecido na política.
  - Criar uma cadeia aleatória usando o gerador de números aleatórios.
  - Formando uma reclamação de acordo com a cadeia de formato fornecida. Esta transformação usa `String.Format` o método C#.

- Validação de entrada - Este elemento permite-lhe realizar agregações booleanas semelhantes *e e* *ou*.

- [Definições de conteúdo](contentdefinitions.md) - Contém URLs para modelos HTML5 para utilizar na sua viagem de utilizador. Numa política personalizada, uma definição de conteúdo define a página HTML5 URI que é usada para um passo especificado na viagem do utilizador. Por exemplo, o início ou inscrição, o reset da palavra-passe ou as páginas de erro. Pode modificar a aparência e sentir substituindo o LoadUri para o ficheiro HTML5. Ou pode criar novas definições de conteúdo de acordo com as suas necessidades. Este elemento pode conter uma referência de recursos localizados utilizando um ID de localização.

- [Localização](localization.md) - Permite-lhe apoiar várias línguas. O apoio à localização em políticas permite-lhe configurar a lista de línguas apoiadas numa política e escolher uma linguagem padrão. Também são apoiadas cordas e coleções específicas da linguagem.

- [DisplayControls](display-controls.md) - Define os controlos a exibir numa página. Os controlos de ecrã têm uma funcionalidade especial e interagem com perfis técnicos de validação de back-end. Os controlos de visualização estão atualmente em **pré-visualização**.
