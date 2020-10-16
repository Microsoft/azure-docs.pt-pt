---
title: Elemento UI do UI do UI do ServiçoPrincipalSelector
description: Descreve o elemento UI do Microsoft.Common.ServicePrincipalSelector para o portal Azure. Fornece um dropdown para escolher um identificador de aplicação e uma caixa de texto para inserir uma palavra-passe ou impressão digital de certificado.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: tomfitz
ms.openlocfilehash: 73b242754bfae53b6df5abd9c2c8dee33b973dad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91576001"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>Microsoft.Common.ServicePrincipalSelector UI elemento

Um controlo que permite aos utilizadores selecionarem um principal de serviço existente ou registarem um novo. Quando selecionar **Create New,** passa pelos passos para registar uma nova aplicação. Quando seleciona uma aplicação existente, o controlo fornece uma caixa de texto para inserir uma palavra-passe ou uma impressão digital de certificado.

## <a name="ui-sample"></a>Amostra de UI

A vista padrão é determinada pelos valores da `defaultValue` propriedade. Se a `principalId` propriedade contiver um identificador globalmente único válido (GUID), o controlo procura o ID do objeto da aplicação. O valor predefinido aplica-se se o utilizador não fizer uma seleção a partir do dropdown.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-initial.png" alt-text="Microsoft.Common.ServicePrincipalSelector vista inicial":::

Quando selecionar **Criar um novo** ou um identificador de aplicações existente a partir do dropdown o Tipo de **Autenticação** é apresentado para introduzir uma palavra-passe ou impressão digital de certificado na caixa de texto.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-selection.png" alt-text="Microsoft.Common.ServicePrincipalSelector vista inicial":::

## <a name="schema"></a>Esquema

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "principalId": "App Id",
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
    "principalId": "App Id",
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type"
  },
  "defaultValue": {
    "principalId": "<default guid>",
    "name": "(New) default App Id"
  },
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideCertificate": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Observações

- As propriedades necessárias são:
  - `name`
  - `type`
  - `label`
  - `defaultValue`: Especifica o predefinição `principalId` e `name` .

- As propriedades opcionais são:
  - `toolTip`: Anexa uma ponta de ferramenta `infoBalloon` a cada etiqueta.
  - `visible`: Ocultar ou visualizar o controlo.
  - `options`: Especifica se deve ou não ser disponibilizada a opção de impressão digital do certificado.
  - `constraints`: Restrições da Regex para validação de passwords.

## <a name="example"></a>Exemplo

Segue-se um exemplo do `Microsoft.Common.ServicePrincipalSelector` controlo. A `defaultValue` propriedade `principalId` `<default guid>` define-se como um espaço reservado para um identificador de aplicação padrão GUID.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "SPNcontrol",
        "label": "SPNcontrol",
        "elements": [
          {
            "name": "ServicePrincipal",
            "type": "Microsoft.Common.ServicePrincipalSelector",
            "label": {
              "principalId": "App Id",
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
              "principalId": "App Id",
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type"
            },
            "defaultValue": {
              "principalId": "<default guid>",
              "name": "(New) default App Id"
            },
            "constraints": {
              "required": true,
              "regex": "^[a-zA-Z0-9]{8,}$",
              "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
            },
            "options": {
              "hideCertificate": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "appId": "[steps('SPNcontrol').ServicePrincipal.appId]",
      "objectId": "[steps('SPNcontrol').ServicePrincipal.objectId]",
      "password": "[steps('SPNcontrol').ServicePrincipal.password]",
      "certificateThumbprint": "[steps('SPNcontrol').ServicePrincipal.certificateThumbprint]",
      "newOrExisting": "[steps('SPNcontrol').ServicePrincipal.newOrExisting]",
      "authenticationType": "[steps('SPNcontrol').ServicePrincipal.authenticationType]"
    }
  }
}
```

## <a name="example-output"></a>Saída de exemplo

É `appId` o ID do registo de candidatura que selecionou ou criou. `objectId`Trata-se de um conjunto de objectIds para os Principais de Serviço configurados para o registo de candidatura selecionado.

Quando nenhuma seleção é feita a partir da queda, o `newOrExisting` valor da propriedade é **novo:**

```json
{
  "appId": {
    "value": "<default guid>"
  },
  "objectId": {
    "value": ["<default guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "new"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

Quando **criar um novo** ou um identificador de aplicações existente é selecionado a partir do dropdown o valor da propriedade é `newOrExisting` **existente:**

```json
{
  "appId": {
    "value": "<guid>"
  },
  "objectId": {
    "value": ["<guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "existing"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

## <a name="next-steps"></a>Passos seguintes

- Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
- Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).
