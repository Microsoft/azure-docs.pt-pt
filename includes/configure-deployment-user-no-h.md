---
title: incluir ficheiro
description: incluir ficheiro
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/14/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 4e699707db02de07f3d1ebb7d1fa8d0575a10aa3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993478"
---
A FTP e o Git local podem implementar para uma aplicação web Azure utilizando um *utilizador de implementação.* Uma vez configurar o utilizador de implementação, pode usá-lo para todas as suas implementações Azure. O nome de utilizador e palavra-passe de implementação ao nível da sua conta são diferentes das suas credenciais de subscrição Azure. 

Para configurar o utilizador de implementação, executar o comando [de configuração de implementação az webapp](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) em Azure Cloud Shell. Substitua \<username> e por um nome de utilizador de \<password> implementação e senha. 

- O nome de utilizador deve ser único dentro do Azure, e para os pushes git locais, não deve conter o símbolo '@'. 
- A palavra-passe deve ter pelo menos oito caracteres, com dois dos seguintes três elementos: letras, números e símbolos. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

A saída JSON mostra a palavra-passe como `null` . Se obtiver o erro `'Conflict'. Details: 409`, altere o nome de utilizador. Se obtiver o `'Bad Request'. Details: 400` erro, utilize uma palavra-passe mais forte. 

Grave o seu nome de utilizador e palavra-passe para usar para implementar as suas aplicações web.
