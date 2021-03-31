---
title: Visão geral das definições de funções personalizadas
description: Descreve o conceito de criar definições de funções personalizadas para aplicações geridas.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 7b7fff913c177703f959bfa103c8e310d01059e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "81391832"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Artefacto de definição de função personalizada em aplicações geridas azure

A definição de função personalizada é um artefacto opcional em aplicações geridas. É usado para determinar que permissões a aplicação gerida precisa para executar as suas funções.

Este artigo fornece uma visão geral do artefacto de definição de função personalizado e suas capacidades.

## <a name="custom-role-definition-artifact"></a>Artefacto de definição de função personalizada

Tens de nomear o artefacto de definição de papel personalizado customRoleDefinition.jsligado. Coloque-o ao mesmo nível que createUiDefinition.jse mainTemplate.jsno pacote de .zip que cria uma definição de aplicação gerida. Para aprender a criar o pacote .zip e publicar uma definição de aplicação gerida, consulte [publicar uma definição de aplicação gerida.](publish-service-catalog-app.md)

## <a name="custom-role-definition-schema"></a>Esquema de definição de função personalizada

O customRoleDefinition.jsem arquivo tem uma propriedade de alto nível `roles` que é uma variedade de papéis. Estas funções são as permissões que a aplicação gerida precisa de funcionar. Atualmente, apenas são permitidas funções incorporadas, mas pode especificar várias funções. Um papel pode ser referenciado pelo ID da definição de função ou pelo nome de função.

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

Um papel é composto por a `$.properties.roleName` ou `id` um:

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> Pode usar o `id` ou `roleName` o campo. Só é necessário um. Estes campos são utilizados para procurar a definição de papel que deve ser aplicada. Se ambos forem fornecidos, o `id` campo será utilizado.

|Propriedade|Necessário?|Description|
|---------|---------|---------|
|ID|Yes|A identificação do papel incorporado. Pode utilizar o ID completo ou apenas o GUID.|
|papelName|Yes|O nome do papel incorporado.|