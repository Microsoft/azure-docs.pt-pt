---
title: Criar elementos de definição de UI
description: Descreve os elementos a utilizar na construção de definições de UI para o portal Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: 12bf5d131001d95a3f4327f95c24125dbf3fb510
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77086701"
---
# <a name="createuidefinition-elements"></a>Elementos CreateUiDefini

Este artigo descreve o esquema e as propriedades para todos os elementos suportados de uma CreateUiDefinition. 

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
| nome | Sim | Um identificador interno para fazer referência a uma instância específica de um elemento. O uso mais comum do `outputs`nome do elemento está em , onde os valores de saída dos elementos especificados são mapeados para os parâmetros do modelo. Também pode usá-lo para ligar o valor `defaultValue` de saída de um elemento ao de outro elemento. |
| tipo | Sim | O controlo da UI para renderizar para o elemento. Para obter uma lista de tipos suportados, consulte [Elementos](#elements). |
| label | Sim | O texto de exibição do elemento. Alguns tipos de elementos contêm várias etiquetas, por isso o valor pode ser um objeto que contém várias cordas. |
| defaultValor | Não | O valor padrão do elemento. Alguns tipos de elementos suportam valores padrão complexos, para que o valor possa ser um objeto. |
| ferramentaTip | Não | O texto a exibir na ponta da ferramenta do elemento. Semelhante `label`a, alguns elementos suportam várias cordas de ponta da ferramenta. As ligações inline podem ser incorporadas usando a sintaxe Markdown.
| constrangimentos | Não | Uma ou mais propriedades que são usadas para personalizar o comportamento de validação do elemento. As propriedades suportadas para constrangimentos variam por tipo de elemento. Alguns tipos de elementos não suportam a personalização do comportamento de validação, pelo que não têm nenhuma propriedade de restrições. |
| opções | Não | Propriedades adicionais que personalizam o comportamento do elemento. Semelhante `constraints`a, as propriedades suportadas variam por tipo de elemento. |
| visível | Não | Indica se o elemento é apresentado. Se, `true`o elemento e os elementos infantis aplicáveis forem apresentados. O valor predefinido é `true`. Use [funções lógicas](create-uidefinition-functions.md#logical-functions) para controlar dinamicamente o valor desta propriedade.

## <a name="elements"></a>Elementos

A documentação para cada elemento contém uma amostra de UI, esquema, observações sobre o comportamento do elemento (geralmente no que diz respeito à validação e personalização suportada) e à saída da amostra.

- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft.Common.InfoBox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft.Common.TagsByResource](microsoft-common-tagsbyresource.md)
- [Microsoft.Common.TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.ManagedIdentity.IdentitySelector](microsoft-managedidentity-identityselector.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Passos seguintes

Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
