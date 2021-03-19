---
title: Elemento de UI checkBox
description: Descreve o elemento UI Microsoft.Common.CheckBox para o portal Azure. Permite que os utilizadores selecionem para verificar ou desmarcar uma opção.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tomfitz
ms.openlocfilehash: 9f40f223cca34df58d2af7373d8f60fd7f383162
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87098545"
---
# <a name="microsoftcommoncheckbox-ui-element"></a>Microsoft.Common.CheckBox UI elemento

O controlo CheckBox permite que os utilizadores verifiquem ou desmarquem uma opção. O controlo **retorna quando** o controlo é verificado ou **falso** quando não verificado.

## <a name="ui-sample"></a>Amostra de UI

:::image type="content" source="./media/managed-application-elements/microsoft-common-checkbox.png" alt-text="Microsoft.Common.CheckBox":::

## <a name="schema"></a>Esquema

```json
{
    "name": "legalAccept",
    "type": "Microsoft.Common.CheckBox",
    "label": "I agree to the terms and conditions.",
    "constraints": {
        "required": true,
        "validationMessage": "Please acknowledge the legal conditions."
    }
}
```

## <a name="sample-output"></a>Saída de exemplo

```json
true
```

## <a name="remarks"></a>Observações

Quando definir **o necessário** para ser **verdadeiro,** o utilizador deve selecionar a caixa de verificação. Se o utilizador não selecionar a caixa de verificação, a mensagem de validação é apresentada.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).
