---
title: Elemento de interface do usuário CredentialsCombo do Azure | Microsoft Docs
description: Descreve o elemento de interface do usuário Microsoft. COMPUTE. CredentialsCombo para portal do Azure.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2018
ms.author: tomfitz
ms.openlocfilehash: c1b4791f2ec80eba25a00e22cb4298b4c97da4de
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151093"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Elemento de interface do usuário Microsoft. COMPUTE. CredentialsCombo

Um grupo de controles com validação interna para senhas do Windows e do Linux e chaves públicas SSH.

## <a name="ui-sample"></a>Exemplo de interface do usuário

Para o Windows, os usuários veem:

![Microsoft. COMPUTE. CredentialsCombo Windows](./media/managed-application-elements/microsoft.compute.credentialscombo-windows.png)

Para o Linux com a senha selecionada, os usuários veem:

![Senha do Microsoft. COMPUTE. CredentialsCombo Linux](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-password.png)

Para Linux com chave pública SSH selecionada, os usuários veem:

![Chave do Linux Microsoft. COMPUTE. CredentialsCombo](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-key.png)

## <a name="schema"></a>Esquema

Para o Windows, use o seguinte esquema:

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

Para o **Linux**, use o seguinte esquema:

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

Se `osPlatform` for **Windows**ou `osPlatform` for **Linux** e o usuário tiver fornecido uma senha em vez de uma chave pública SSH, o controle retornará a seguinte saída:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rddem0",
}
```

Se `osPlatform` for **Linux** e o usuário forneceu uma chave pública SSH, o controle retornará a seguinte saída:

```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="remarks"></a>Observações

- `osPlatform` deve ser especificado e pode ser **Windows** ou **Linux**.
- Se `constraints.required` for definido como **true**, as caixas de texto senha ou chave pública SSH deverão ter valores para validar com êxito. O valor padrão é **true**.
- Se `options.hideConfirmation` for definido como **true**, a segunda caixa de texto para confirmar a senha do usuário ficará oculta. O valor predefinido é **false**.
- Se `options.hidePassword` for definido como **true**, a opção para usar a autenticação de senha ficará oculta. Ele pode ser usado somente quando `osPlatform` é **Linux**. O valor predefinido é **false**.
- Restrições adicionais sobre as senhas permitidas podem ser implementadas usando a propriedade `customPasswordRegex`. A cadeia de caracteres em `customValidationMessage` é exibida quando uma senha falha na validação personalizada. O valor padrão para ambas as propriedades é **NULL**.

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução à criação de definições de interface do usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos da interface do usuário, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).
