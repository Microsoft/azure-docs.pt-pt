---
title: Ver referência de artefacto de definição
description: Fornece um exemplo de artefacto de definição de visualização para aplicações geridas azure. O nome do ficheiro é viewDefinition.json.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 5173db54abef132a4a4d5d117881352ca37d6b23
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75651205"
---
# <a name="reference-view-definition-artifact"></a>Referência: Ver artefacto de definição

Este artigo é uma referência para um artefacto *viewDefinition.json* em Aplicações Geridas azure. Para obter mais informações sobre a configuração de vistas de autor, consulte o artefacto de [definição de visualização](concepts-view-definition.md).

## <a name="view-definition"></a>Ver definição

O seguinte JSON mostra um exemplo de *ficheiro viewDefinition.json* para aplicações geridas pelo Azure:

```json
{
  "views": [{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  },
  {
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
        "parameters": {
          "steps": [{
            "name": "add",
            "label": "Add user",
            "elements": [{
              "name": "name",
              "label": "User's Full Name",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a full user name.",
              "constraints": { "required": true }
            },
            {
              "name": "location",
              "label": "User's Location",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a Location.",
              "constraints": { "required": true }
            }]
          }],
          "outputs": {
            "name": "[steps('add').name]",
            "properties": {
              "FullName": "[steps('add').name]",
              "Location": "[steps('add').location]"
            }
          }
        }
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }]
}
```

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Criar aplicação gerida com ações e recursos personalizados](tutorial-create-managed-app-with-custom-provider.md)
- [Referência: Artefacto de elementos de interface do utilizador](reference-createuidefinition-artifact.md)
- [Referência: Artefacto do modelo de implantação](reference-main-template-artifact.md)