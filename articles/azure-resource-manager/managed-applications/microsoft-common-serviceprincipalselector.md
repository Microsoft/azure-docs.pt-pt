---
title: Elemento UI do ServiçoPrincipalSelector
description: Descreve o elemento UI do Microsoft.Common.ServicePrincipalSelector para o portal Azure. Fornece um controlo para escolher uma aplicação e uma caixa de texto para inserir uma palavra-passe ou impressão digital de certificado.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: tomfitz
ms.openlocfilehash: 2fdbbad467d8c762db485fc7935e9cef78313fd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96184455"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>Microsoft.Common.ServicePrincipalSelector UI elemento

Um controlo que permite aos utilizadores selecionarem um [principal de serviço](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) existente ou registarem uma nova aplicação. Quando selecionar **Criar Novo,** siga os passos para registar uma nova aplicação. Quando seleciona uma aplicação existente, o controlo fornece uma caixa de texto para inserir uma palavra-passe ou uma impressão digital de certificado.

## <a name="ui-samples"></a>Amostras de UI

Pode utilizar uma aplicação predefinida, criar uma nova aplicação ou utilizar uma aplicação existente.

### <a name="use-default-application-or-create-new"></a>Use a aplicação padrão ou crie uma nova

A vista padrão é determinada pelos valores do `defaultValue` imóvel e o **Tipo Principal** de Serviço está definido para **Criar Novo**. Se a `principalId` propriedade contiver um identificador globalmente único válido (GUID), o controlo procura o da aplicação `objectId` . O valor predefinido aplica-se se o utilizador não fizer uma seleção a partir do controlo.

Se pretender registar uma nova aplicação, selecione **Alterar** e é apresentada uma caixa de diálogo de **aplicação.** Introduzir **Nome**, **Tipo de conta suportada,** e selecione o botão **Registar.**

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-default.png" alt-text="Microsoft.Common.ServicePrincipalSelector vista inicial.":::

Depois de registar uma nova aplicação, utilize o **Tipo de Autenticação** para introduzir uma impressão digital de senha ou certificado.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-authenticate.png" alt-text="Autenticação microsoft.common.servicePrincipalSelector.":::

### <a name="use-existing-application"></a>Utilizar a aplicação existente

Para utilizar uma aplicação existente, escolha **Selecione Existing** e, em seguida, **selecione Fazer seleção**. Utilize a caixa de diálogo **de aplicação Selecione** para procurar o nome da aplicação. A partir dos resultados, selecione a aplicação e, em seguida, o botão **Seleção.** Depois de selecionar uma aplicação, o controlo apresenta o **Tipo de Autenticação** para introduzir uma impressão digital de senha ou certificado.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-existing.png" alt-text="Microsoft.Common.ServicePrincipalSelector selecione a aplicação existente.":::

## <a name="schema"></a>Esquema

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
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

- As propriedades necessárias são as seguintes:
  - `name`
  - `type`
  - `label`
  - `defaultValue`: Especifica o predefinição `principalId` e `name` .

- As propriedades opcionais são as seguintes:
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
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
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

É `appId` o ID do registo de candidatura que selecionou ou criou. `objectId`Trata-se de um conjunto de identificações de objetos para os principais de serviço configurados para o registo de candidatura selecionado.

Quando não é feita nenhuma seleção a partir do controlo, o `newOrExisting` valor da propriedade é **novo:**

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

Quando **a Criação de uma** aplicação nova ou existente é selecionada a partir do controlo o valor da propriedade é `newOrExisting` **existente:**

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