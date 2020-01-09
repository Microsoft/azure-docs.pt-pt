---
title: Criar elementos de definição de interface do usuário
description: Descreve os elementos a serem usados ao construir definições de interface do usuário para portal do Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 0ceb8f5762bb6bc987757845426a0f2b380264f1
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650672"
---
# <a name="createuidefinition-elements"></a>Elementos CreateUiDefinition

Este artigo descreve o esquema e as propriedades de todos os elementos com suporte de um CreateUiDefinition. 

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

| Propriedade | Obrigatório | Descrição |
| -------- | -------- | ----------- |
| nome | Sim | Um identificador interno para fazer referência a uma instância específica de um elemento. O uso mais comum do nome do elemento está em `outputs`, em que os valores de saída dos elementos especificados são mapeados para os parâmetros do modelo. Você também pode usá-lo para associar o valor de saída de um elemento à `defaultValue` de outro elemento. |
| tipo | Sim | O controle da interface do usuário a ser renderizado para o elemento. Para obter uma lista de tipos com suporte, consulte [elementos](#elements). |
| label | Sim | O texto de exibição do elemento. Alguns tipos de elementos contêm vários rótulos, portanto, o valor pode ser um objeto contendo várias cadeias de caracteres. |
| defaultValue | Não | O valor padrão do elemento. Alguns tipos de elemento dão suporte a valores padrão complexos, portanto, o valor pode ser um objeto. |
| Dessa | Não | O texto a ser exibido na dica de ferramenta do elemento. Semelhante à `label`, alguns elementos dão suporte a várias cadeias de caracteres de dica de ferramenta. Links embutidos podem ser inseridos usando a sintaxe de redução.
| constraints | Não | Uma ou mais propriedades que são usadas para personalizar o comportamento de validação do elemento. As propriedades com suporte para restrições variam de acordo com o tipo de elemento. Alguns tipos de elementos não dão suporte à personalização do comportamento de validação e, portanto, não têm nenhuma propriedade de restrições. |
| options | Não | Propriedades adicionais que personalizam o comportamento do elemento. Semelhante à `constraints`, as propriedades com suporte variam de acordo com o tipo de elemento. |
| visível | Não | Indica se o elemento é exibido. Se `true`, o elemento e os elementos filho aplicáveis serão exibidos. O valor predefinido é `true`. Use [funções lógicas](create-uidefinition-functions.md#logical-functions) para controlar dinamicamente o valor dessa propriedade.

## <a name="elements"></a>Elementos

A documentação de cada elemento contém um exemplo de interface do usuário, esquema, comentários sobre o comportamento do elemento (geralmente relativo à validação e à personalização com suporte) e saída de exemplo.

- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft.Common.InfoBox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft. Common. TagsByResource](microsoft-common-tagsbyresource.md)
- [Microsoft.Common.TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Passos seguintes

Para obter uma introdução à criação de definições de interface do usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
