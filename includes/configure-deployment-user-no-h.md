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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67836941"
---
FtP e Git local podem implantar-se numa aplicação web Azure utilizando um utilizador de *implementação*. Assim que configurar o utilizador de implementação, pode utilizá-lo para todas as suas implementações Azure. O seu nome de utilizador de implementação de nível de conta e palavra-passe são diferentes das credenciais de subscrição do Azure. 

Para configurar o utilizador de implementação, execute o comando de conjunto de utilizadores de [implementação de webapp az](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) em Azure Cloud Shell. Substitua \<o \<nome de utilizador> e a palavra-passe> por um nome de utilizador e palavra-passe de implementação. 

- O nome de utilizador deve ser único dentro do Azure e, para os pushs git locais, não deve conter o símbolo '@'. 
- A palavra-passe deve ter pelo menos oito caracteres de comprimento, com dois dos seguintes três elementos: letras, números e símbolos. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

A saída JSON mostra `null`a palavra-passe como . Se obtiver o erro `'Conflict'. Details: 409`, altere o nome de utilizador. Se obtiver o `'Bad Request'. Details: 400` erro, utilize uma palavra-passe mais forte. 

Grave o seu nome de utilizador e palavra-passe para utilizar para implementar as suas aplicações web.
