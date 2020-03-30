---
title: Artefacto CreateUiDefinition
description: Mostra como criar o artefacto createUiDefinition para uma Aplicação Gerida Azure. O ficheiro é nomeado createUiDefinition.json.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 9be1cb02c419314a33b9487bf183ef3659b2b549
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651491"
---
# <a name="reference-user-interface-elements-artifact"></a>Referência: Artefacto de elementos de interface do utilizador

Este artigo é uma referência para um artefacto *createUiDefinition.json* em Aplicações Geridas azure. Para obter mais informações sobre a autoria de elementos de interface do utilizador, consulte [Criar elementos](create-uidefinition-elements.md)de interface do utilizador .

## <a name="user-interface-elements"></a>Elementos da interface de utilizador

O seguinte JSON mostra um exemplo de *ficheiro createUiDefinition.json* para Aplicações Geridas Azure:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "applicationSettings",
        "label": "Application Settings",
        "subLabel": {
          "preValidation": "Configure your application settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Application Settings",
        "elements": [
          {
            "name": "funcname",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the function to be created",
            "toolTip": "Name of the function to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "storagename",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the storage to be created",
            "toolTip": "Name of the storage to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "zipFileBlobUri",
            "type": "Microsoft.Common.TextBox",
            "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
            "label": "The Uri to the uploaded function zip file",
            "toolTip": "The Uri to the uploaded function zip file",
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "funcname": "[steps('applicationSettings').funcname]",
      "storageName": "[steps('applicationSettings').storagename]",
      "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
    }
  }
}
```

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Criar aplicação gerida com ações e recursos personalizados](tutorial-create-managed-app-with-custom-provider.md)
- [Referência: Artefacto do modelo de implantação](reference-main-template-artifact.md)
- [Referência: Ver artefacto de definição](reference-view-definition-artifact.md)