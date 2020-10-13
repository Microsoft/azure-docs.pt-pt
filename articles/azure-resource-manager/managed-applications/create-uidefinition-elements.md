---
title: Criar elementos de definição de UI
description: Descreve os elementos a utilizar ao construir definições de UI para o portal Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 8b6c6e78c64f83ca39a29f319a103e893d8d8b38
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88923726"
---
# <a name="createuidefinition-elements"></a>Criar elementos dedefinição CreateUi

Este artigo descreve o esquema e propriedades para todos os elementos suportados de uma CreateUiDefinition. 

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
| name | Sim | Um identificador interno para referir uma instância específica de um elemento. A utilização mais comum do nome do elemento está em `outputs` , onde os valores de saída dos elementos especificados são mapeados para os parâmetros do modelo. Também pode usá-lo para ligar o valor de saída de um elemento ao `defaultValue` de outro elemento. |
| tipo | Sim | O controlo da UI para renderizar o elemento. Para obter uma lista de tipos [suportados,](#elements)consulte Elementos . |
| etiqueta | Sim | O texto de exibição do elemento. Alguns tipos de elementos contêm várias etiquetas, pelo que o valor pode ser um objeto que contém várias cordas. |
| padrãoValue | Não | O valor predefinido do elemento. Alguns tipos de elementos suportam valores padrão complexos, para que o valor possa ser um objeto. |
| ferramentaTip | Não | O texto a visualizar na ponta da ferramenta do elemento. Semelhante a `label` , alguns elementos suportam várias cordas de ponta da ferramenta. As ligações inline podem ser incorporadas usando a sintaxe markdown.
| constrangimentos | Não | Uma ou mais propriedades que são usadas para personalizar o comportamento de validação do elemento. As propriedades suportadas para constrangimentos variam de acordo com o tipo de elemento. Alguns tipos de elementos não suportam a personalização do comportamento de validação, pelo que não têm restrições de propriedade. |
| opções | Não | Propriedades adicionais que personalizam o comportamento do elemento. `constraints`Similares, as propriedades suportadas variam por tipo de elemento. |
| visível | Não | Indica se o elemento é apresentado. Se `true` for apresentado o elemento e os elementos infantis aplicáveis. O valor predefinido é `true`. Use [funções lógicas](create-uidefinition-functions.md#logical-functions) para controlar dinamicamente o valor desta propriedade.

## <a name="elements"></a>Elementos

A documentação de cada elemento contém uma amostra de UI, esquema, observações sobre o comportamento do elemento (geralmente relativo à validação e personalização suportada) e à saída da amostra.

- [Microsoft.Common.CheckBox](microsoft-common-checkbox.md)
- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.EditableGrid](microsoft-common-editablegrid.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft.Common.InfoBox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft.Common.Slider](microsoft-common-slider.md)
- [Microsoft.Common.TagsByResource](microsoft-common-tagsbyresource.md)
- [Microsoft.Common.TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.ManagedIdentity.IdentitySelector](microsoft-managedidentity-identityselector.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Solutions.ArmApiControl](microsoft-solutions-armapicontrol.md)
- [Microsoft.Solutions.ResourceSelector](microsoft-solutions-resourceselector.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Passos seguintes

Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
