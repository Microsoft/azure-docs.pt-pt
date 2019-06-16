---
title: Elemento de interface do Usuário de CredentialsCombo do Azure | Documentos da Microsoft
description: Descreve o elemento de interface do Usuário de Microsoft.Compute.CredentialsCombo para o portal do Azure.
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
ms.openlocfilehash: 0412d55fe60524cde404e6a640723d3259e020e1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60251404"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Elemento de interface do Usuário de Microsoft.Compute.CredentialsCombo
Um grupo de controles de validação incorporada para as palavras-passe do Windows e Linux e chaves públicas SSH.

## <a name="ui-sample"></a>Exemplo de interface do Usuário

Para Windows, os utilizadores veem:

![Microsoft.Compute.CredentialsCombo Windows](./media/managed-application-elements/microsoft.compute.credentialscombo-windows.png)

Para Linux com a palavra-passe selecionada, os utilizadores veem:

![Palavra-passe do Microsoft.Compute.CredentialsCombo Linux](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-password.png)

Para Linux com a chave pública SSH selecionado, os utilizadores veem:

![Chave de Microsoft.Compute.CredentialsCombo Linux](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-key.png)

## <a name="schema"></a>Esquema
Para Windows, utilize o esquema seguinte:

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
    "customValidationMessage": "The password must contain at least 12 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false
  },
  "osPlatform": "Windows",
  "visible": true
}
```

Para **Linux**, utilize o esquema seguinte:

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
    "customValidationMessage": "The password must contain at least 12 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false,
    "hidePassword": false
  },
  "osPlatform": "Linux",
  "visible": true
}
```

## <a name="remarks"></a>Observações
- `osPlatform` tem de ser especificado e pode ser uma **Windows** ou **Linux**.
- Se `constraints.required` está definido como **true**, em seguida, a palavra-passe ou caixas de texto da chave pública SSH tem de ter valores para validar com êxito. O valor predefinido é **true**.
- Se `options.hideConfirmation` está definido como **true**, em seguida, a segunda caixa de texto para confirmar a palavra-passe do utilizador está oculto. O valor predefinido é **false**.
- Se `options.hidePassword` está definido como **true**, em seguida, a opção para utilizar a autenticação de palavra-passe está oculto. Ele pode ser usado apenas quando `osPlatform` é **Linux**. O valor predefinido é **false**.
- Restrições adicionais sobre as palavras-passe permitidas podem ser implementadas usando o `customPasswordRegex` propriedade. A cadeia de caracteres `customValidationMessage` é apresentado quando uma palavra-passe ocorre uma falha de validação personalizada. É o valor predefinido para ambas as propriedades **nulo**.

## <a name="sample-output"></a>Resultado da amostra
Se `osPlatform` é **Windows**, ou `osPlatform` é **Linux** e o utilizador forneceu uma palavra-passe em vez de uma chave pública SSH, o controle retorna o resultado seguinte:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rddem0",
}
```

Se `osPlatform` é **Linux** e o utilizador forneceu uma chave pública SSH, o controle retorna o resultado seguinte:

```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="next-steps"></a>Passos Seguintes
* Para obter uma introdução à criação de definições de interface do Usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do Usuário, consulte [CreateUiDefinition elementos](create-uidefinition-elements.md).
