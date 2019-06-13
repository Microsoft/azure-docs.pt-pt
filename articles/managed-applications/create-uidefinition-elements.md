---
title: Azure-criar o elemento de definição de interface do Usuário | Documentos da Microsoft
description: Descreve os elementos para utilizar durante a criação de definições de interface do Usuário para o portal do Azure.
services: managed-applications
documentationcenter: na
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: tomfitz
ms.openlocfilehash: 41a583a77f85bb1524112fa20d9098e18bc4f431
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587943"
---
# <a name="createuidefinition-elements"></a>Elementos de CreateUiDefinition
Este artigo descreve o esquema e propriedades para todos os elementos suportados um CreateUiDefinition. 

## <a name="schema"></a>Esquema

O esquema para a maioria dos elementos é o seguinte:

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "my value",
  "toolTip": "Provide a descriptive name.",
  "constraints": {},
  "options": {},
  "visible": true
}
```

| Propriedade | Necessário | Descrição |
| -------- | -------- | ----------- |
| name | Sim | Um identificador interno para fazer referência a uma instância específica de um elemento. O uso mais comum do nome do elemento está no `outputs`, onde os valores de saída dos elementos especificados estão mapeados para os parâmetros do modelo. Também pode ser usada para vincular o valor de saída de um elemento para a `defaultValue` de outro elemento. |
| type | Sim | O controle de interface do Usuário para processar para o elemento. Para obter uma lista dos tipos suportados, consulte [elementos](#elements). |
| label | Sim | O texto de exibição do elemento. Alguns tipos de elemento contenham várias etiquetas, para que o valor pode ser um objeto que contém várias cadeias de caracteres. |
| defaultValue | Não | O valor predefinido do elemento. Alguns tipos de elemento suportam valores predefinidos complexos, para que o valor pode ser um objeto. |
| toolTip | Não | O texto a apresentar na dica de ferramenta do elemento. Semelhante à `label`, alguns elementos de suportam várias cadeias de caracteres de dica de ferramenta. Ligações inline podem ser incorporadas usando sintaxe de Markdown.
| constraints | Não | Uma ou mais propriedades que são utilizadas para personalizar o comportamento de validação do elemento. As propriedades suportadas para restrições variam consoante o tipo de elemento. Alguns tipos de elemento não suporta a personalização do comportamento de validação e, portanto, não ter nenhuma propriedade de restrições. |
| options | Não | Propriedades adicionais que personalizar o comportamento do elemento. Semelhante à `constraints`, as propriedades suportadas variam consoante o tipo de elemento. |
| visible | Não | Indica se o elemento é apresentado. Se `true`, o elemento e elementos filho aplicáveis são apresentados. O valor predefinido é `true`. Uso [funções lógicas](create-uidefinition-functions.md#logical-functions) para controlar dinamicamente o valor desta propriedade.

## <a name="elements"></a>Elementos

A documentação para cada elemento contém um exemplo de interface do Usuário, comentários de esquema, o comportamento do elemento (normalmente sobre validação e personalização suportada) e saída de exemplo.

- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft.Common.InfoBox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft.Common.TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Passos Seguintes
Para obter uma introdução à criação de definições de interface do Usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
