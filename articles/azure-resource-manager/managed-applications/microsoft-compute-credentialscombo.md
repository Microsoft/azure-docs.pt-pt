---
title: Elemento de UI de UI de Credenciais
description: Descreve o elemento microsoft.compute.credentialsCombo UI para o portal Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/29/2018
ms.author: tomfitz
ms.openlocfilehash: 47c88e08e5d2eac09fbcd5b60a8ccd73b46c9616
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87063778"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Microsoft.Compute.CredentialsDi elemento UI

Um grupo de controlos com validação incorporada para senhas Windows e Linux e chaves públicas SSH.

## <a name="ui-sample"></a>Amostra de UI

Para windows, os utilizadores vêem:

![Microsoft.Compute.CredentialsCombo Windows](./media/managed-application-elements/microsoft-compute-credentialscombo-windows.png)

Para Linux com senha selecionada, os utilizadores vêem:

![Microsoft.Compute.CredentialsCombo Linux password](./media/managed-application-elements/microsoft-compute-credentialscombo-linux-password.png)

Para Linux com chave pública SSH selecionada, os utilizadores vêem:

![Microsoft.Compute.CredentialsCombo Linux](./media/managed-application-elements/microsoft-compute-credentialscombo-linux-key.png)

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

Para **Linux,** utilize o seguinte esquema:

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

## <a name="sample-output"></a>Saída de exemplo

Se `osPlatform` for o **Windows**, ou se for `osPlatform` o **Linux** e o utilizador tiver fornecido uma palavra-passe em vez de uma chave pública SSH, o controlo devolve a seguinte saída:

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

- `osPlatform` deve ser especificado, e pode ser **windows** ou **Linux**.
- Se `constraints.required` estiver definido como **verdadeiro,** então a palavra-passe ou as caixas de texto de chaves públicas SSH devem ter valores para validar com sucesso. O valor predefinido é **verdadeiro.**
- Se `options.hideConfirmation` estiver definido como **verdadeiro**, então a segunda caixa de texto para confirmar a palavra-passe do utilizador é ocultada. O valor predefinido é **falso**.
- Se `options.hidePassword` estiver definido como **verdadeiro,** então a opção de utilização da autenticação de palavra-passe está oculta. Só pode ser utilizado quando `osPlatform` é **Linux.** O valor predefinido é **falso**.
- Constrangimentos adicionais nas palavras-passe permitidas podem ser implementados usando a `customPasswordRegex` propriedade. O string in `customValidationMessage` é apresentado quando uma palavra-passe falha na validação personalizada. O valor predefinido para ambas as propriedades é **nulo.**

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).
