---
title: Obter o nome de utilizador e a palavra-passe para estabelecer ligação ao Controlador de Dados do Arc
description: Obter o nome de utilizador e a palavra-passe para estabelecer ligação ao Controlador de Dados do Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 8cabb48e3620f1a17d1bb9b87e1646ce2793143b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761707"
---
# <a name="retrieve-the-user-name-and-password-to-connect-to-the-arc-data-controller"></a>Obter o nome de utilizador e a palavra-passe para estabelecer ligação ao Controlador de Dados do Arc

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

Experimente [outros cenários](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory-domain-services/scenarios.md)
