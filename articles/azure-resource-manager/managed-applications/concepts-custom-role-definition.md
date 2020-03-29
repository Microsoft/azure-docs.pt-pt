---
title: Visão geral das definições de papéis personalizados
description: Descreve o conceito de criar definições de papéis personalizados para aplicações geridas.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 88e42fd9626276f6c77b46b33c138407f91d06ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650763"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Artefacto de definição de papel personalizado em Aplicações Geridas de Azure

A definição de função personalizada é um artefacto opcional em aplicações geridas. É usado para determinar que permissões a aplicação gerida precisa para desempenhar as suas funções.

Este artigo fornece uma visão geral do artefacto de definição de papel personalizado e suas capacidades.

## <a name="custom-role-definition-artifact"></a>Artefacto de definição de papel personalizado

Você precisa nomear o artefacto de definição de papel personalizadoRoleDefinition.json. Coloque-o ao mesmo nível que o createUiDefinition.json e o mainTemplate.json no pacote .zip que cria uma definição de aplicação gerida. Para aprender a criar o pacote .zip e publicar uma definição de aplicação gerida, consulte [Publicar uma definição de aplicação gerida.](publish-managed-app-definition-quickstart.md)

## <a name="custom-role-definition-schema"></a>Esquema de definição de papel personalizado

O ficheiro CustomRoleDefinition.json tem `roles` uma propriedade de alto nível que é uma variedade de papéis. Estas funções são as permissões que a aplicação gerida precisa para funcionar. Atualmente, apenas são permitidas funções incorporadas, mas pode especificar várias funções. Um papel pode ser referenciado pela identificação da definição de papel ou pelo nome do papel.

Amostra JSON para definição de função personalizada:

```json
{
    "contentVersion": "0.0.0.1",
    "roles": [
        {
            "properties": {
                "roleName": "Contributor"
            }
        },
        {
            "id": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
        },
        {
            "id": "/providers/Microsoft.Authorization/roledefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
        }
    ]
}
```

## <a name="roles"></a>Funções

Um papel é composto `$.properties.roleName` por `id`um ou um:

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> Pode usar o `id` `roleName` campo ou o campo. Só é necessário um. Estes campos são utilizados para procurar a definição de papel que deve ser aplicada. Se ambos forem `id` fornecidos, o campo será utilizado.

|Propriedade|Necessário?|Descrição|
|---------|---------|---------|
|ID|Sim|A identificação do papel incorporado. Pode usar a identificação completa ou apenas o GUID.|
|roleName|Sim|O nome do papel incorporado.|