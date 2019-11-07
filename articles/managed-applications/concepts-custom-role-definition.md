---
title: Visão geral das definições de função personalizadas em aplicativos gerenciados do Azure | Microsoft Docs
description: Descreve o conceito de criação de definições de função personalizadas para aplicativos gerenciados.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: d645eebefde473e404f7760d2bc8a67c7e3e9087
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73609037"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Artefato de definição de função personalizada em aplicativos gerenciados do Azure

A definição de função personalizada é um artefato opcional em aplicativos gerenciados. Ele é usado para determinar quais permissões o aplicativo gerenciado precisa para executar suas funções.

Este artigo fornece uma visão geral do artefato de definição de função personalizada e seus recursos.

## <a name="custom-role-definition-artifact"></a>Artefato de definição de função personalizada

O artefato de definição de função personalizada deve ser nomeado **customRoleDefinition. JSON** e colocado no mesmo nível que **createUiDefinition. JSON** e **MainTemplate. JSON** no pacote. zip que cria uma definição de aplicativo gerenciado. Para saber como criar o pacote. zip e publicar uma definição de aplicativo gerenciado, consulte [publicar uma definição de aplicativo gerenciado.](publish-managed-app-definition-quickstart.md)

## <a name="custom-role-definition-schema"></a>Esquema de definição de função personalizada

O arquivo **customRoleDefinition. JSON** tem uma propriedade de nível superior `roles`, que é uma matriz de funções. Cada uma dessas funções são as permissões que o aplicativo gerenciado precisa para funcionar. Atualmente, somente funções internas são permitidas, mas várias funções podem ser especificadas. A função pode ser referenciada pela ID da definição de função ou pelo nome da função.

Exemplo de JSON para definição de função personalizada:

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

## <a name="role"></a>Função

Uma função é composta por um `$.properties.roleName` ou `id`.

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!Note]
> Somente um dos campos `id` ou `roleName` é necessário. Esses campos são usados para pesquisar a definição de função a ser aplicada. Se ambos forem fornecidos, o campo `id` será usado.

|Propriedade|Necessário|Descrição|
|---------|---------|---------|
|ID|*Ok*|A ID da função interna. Essa propriedade pode ser a ID completa ou apenas o GUID.|
|RoleName|*Ok*|O nome da função interna.|