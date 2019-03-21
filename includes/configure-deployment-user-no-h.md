---
title: incluir ficheiro
description: incluir ficheiro
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: e8888a0505a3a38d2844f82c0f7fff255d05353d
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58261322"
---
No Azure Cloud Shell, configure as credenciais de implementação com o [ `az webapp deployment user set` ](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) comando. Para a implementação de FTP e Git local numa aplicação Web, é preciso este utilizador de implementação. O nome de utilizador e palavra-passe são ao nível da conta. _Eles são diferentes das credenciais da sua subscrição do Azure._

No exemplo seguinte, substitua  *\<nome de utilizador >* e  *\<palavra-passe >*, incluindo os parênteses, com um novo nome de utilizador e palavra-passe. O nome de utilizador tem de ser exclusivo no Azure. A palavra-passe tem de ser, pelo menos, oito carateres de comprimento, com dois dos seguintes três elementos: letras, números e símbolos. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Obter um resultado JSON com a palavra-passe apresentada como `null`. Se obtiver o erro `'Conflict'. Details: 409`, altere o nome de utilizador. Se obtiver o ` 'Bad Request'. Details: 400` erro, utilize uma palavra-passe mais forte.

Este utilizador de implementação é configurar apenas uma vez. Pode usá-lo para todas as suas implementações do Azure.

> [!NOTE]
> Registe o nome de utilizador e palavra-passe. Irá necessitar deles para implementar a aplicação Web mais tarde.
>
>
