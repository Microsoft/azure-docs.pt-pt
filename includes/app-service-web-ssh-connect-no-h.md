---
title: incluir ficheiro
description: incluir ficheiro
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 060bc1039982cc0a77214d5dbe2a08de7a839c84
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184738"
---
Para que abra uma sessão SSH direta com o seu contentor, a aplicação deve estar em execução.

Cole o URL seguinte no seu browser e substitua \<nome da aplicação > pelo nome da aplicação:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Se ainda não estiver autenticado, tem de autenticar com a sua subscrição do Azure para ligar. Uma vez autenticado, verá um shell no browser, onde pode executar comandos no interior do contentor.

![Ligação SSH](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
