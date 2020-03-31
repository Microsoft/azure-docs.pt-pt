---
title: Elemento UI credenciaisCombo
description: Descreve o elemento Microsoft.Compute.CredentialsCombo UI para o portal Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/29/2018
ms.author: tomfitz
ms.openlocfilehash: baee83e08505ddabb4680b5bbb4101479010ea47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652141"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Microsoft.Compute.CredentialsCombo UI

Um grupo de controlos com validação incorporada para senhas Windows e Linux e chaves públicas SSH.

## <a name="ui-sample"></a>Amostra de UI

Para windows, os utilizadores vêem:

![Microsoft.Compute.CredentialsCombo Windows](./media/managed-application-elements/microsoft.compute.credentialscombo-windows.png)

Para o Linux com senha selecionada, os utilizadores vêem:

![Palavra-passe microsoft.Compute.CredentialsCombo Linux](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-password.png)

Para o Linux com a chave pública SSH selecionada, os utilizadores vêem:

![Microsoft.Compute.CredentialsCombo Linux](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-key.png)

## <a name="schema"></a>Esquema

Para windows, utilize o seguinte esquema:

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": {
    "password": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
    "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false
  },
  "osPlatform": "Windows",
  "visible": true
}
```

Para **o Linux,** utilize o seguinte esquema:

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "authenticationType": "Authentication type",
    "password": "Password",
    "confirmPassword": "Confirm password",
    "sshPublicKey": "SSH public key"
  },
  "toolTip": {
    "authenticationType": "",
    "password": "",
    "sshPublicKey": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
    "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false,
    "hidePassword": false
  },
  "osPlatform": "Linux",
  "visible": true
}
```

## <a name="sample-output"></a>Resultado da amostra

Se `osPlatform` **Windows**for windows `osPlatform` , ou for **o Linux** e o utilizador fornecer uma palavra-passe em vez de uma chave pública SSH, o controlo devolve a seguinte saída:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rddem0",
}
```

Se `osPlatform` for **o Linux** e o utilizador fornecer uma chave pública SSH, o controlo devolve a seguinte saída:

```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="remarks"></a>Observações

- `osPlatform`deve ser especificado e pode ser **Windows** ou **Linux**.
- Se `constraints.required` for definido como **verdadeiro,** então a palavra-passe ou as caixas de texto públicas SSH devem ter valores para validar com sucesso. O valor padrão é **verdadeiro.**
- Se `options.hideConfirmation` for definido como **verdadeiro,** a segunda caixa de texto para confirmar a palavra-passe do utilizador está escondida. O valor predefinido é **falso**.
- Se `options.hidePassword` for definido como **verdadeiro,** então a opção de utilizar a autenticação de senha está escondida. Só pode ser `osPlatform` usado quando é **Linux.** O valor predefinido é **falso**.
- Constrangimentos adicionais nas senhas permitidas podem `customPasswordRegex` ser implementados utilizando a propriedade. O string `customValidationMessage` in é apresentado quando uma palavra-passe falha na validação personalizada. O valor predefinido para ambas as propriedades é **nulo.**

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos UI, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).
