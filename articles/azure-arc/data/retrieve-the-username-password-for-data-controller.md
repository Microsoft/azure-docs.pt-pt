---
title: Recupere o nome de utilizador e a palavra-passe para ligar ao Controlador de Dados do Arco
description: Recupere o nome de utilizador e a palavra-passe para ligar ao Controlador de Dados do Arco
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 1c33cf21be0b4b1ea39a568d6df9fd90507dd454
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938957"
---
# <a name="retrieve-the-user-name-and-password-to-connect-to-the-arc-data-controller"></a>Recupere o nome de utilizador e a palavra-passe para ligar ao Controlador de Dados do Arco

Pode estar numa situação em que precisa de recuperar o nome de utilizador e a palavra-passe para o controlador de dados. Estes são os comandos de que precisa quando se corre. 

```console
azdata login
```

Se você é o administrador kubernetes para o cluster. Como tal, tem os privilégios de executar comandos para recuperar dos esconderijos de Kubernetes a informação que o Azure Arc aí persiste.

> [!NOTE]
>  Se usou um nome diferente para o espaço de nome onde o controlador de dados foi criado, não se esqueça de alterar o `-n arc` parâmetro nos comandos abaixo para usar o nome do espaço de nome a que criou o controlador de dados.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="linux"></a>Linux

Executar o seguinte comando para recuperar o nome de utilizador:

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}" | base64 -d
```

Executar o seguinte comando para recuperar a senha:

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}" | base64 -d
```

## <a name="powershell"></a>PowerShell

Executar o seguinte comando para recuperar o nome de utilizador:

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}")))
```

Executar o seguinte comando para recuperar a senha:

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}")))
```

## <a name="next-steps"></a>Passos seguintes

Experimente [outros cenários](https://github.com/microsoft/Azure-data-services-on-Azure-Arc/tree/master/scenarios)
