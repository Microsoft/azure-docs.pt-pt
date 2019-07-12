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
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836941"
---
FTP e local Git podem implementar uma aplicação web do Azure ao utilizar um *utilizador de implementação*. Depois de configurar o seu utilizador de implementação, pode usá-lo para todas as suas implementações do Azure. O nome de utilizador de implementação ao nível da conta e palavra-passe são diferentes das credenciais da sua subscrição do Azure. 

Para configurar o utilizador de implementação, execute o [conjunto de usuários do az webapp deployment](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) comando no Azure Cloud Shell. Substitua \<nome de utilizador > e \<palavra-passe > com um nome de utilizador de utilizador de implementação e a palavra-passe. 

- O nome de utilizador tem de ser exclusivo no Azure e para local Git pushes, não pode conter o ' @' símbolo. 
- A palavra-passe tem de ser, pelo menos, oito carateres de comprimento, com dois dos seguintes três elementos: letras, números e símbolos. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

A saída JSON mostra a palavra-passe como `null`. Se obtiver o erro `'Conflict'. Details: 409`, altere o nome de utilizador. Se obtiver o `'Bad Request'. Details: 400` erro, utilize uma palavra-passe mais forte. 

Registe o seu nome de utilizador e palavra-passe para utilizar para implementar as suas aplicações web.
