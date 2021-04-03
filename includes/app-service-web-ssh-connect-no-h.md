---
title: incluir ficheiro
description: ficheiro de inclusão
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 458cd36a35ea37b2a317fe98fdeb5acc69a36ce8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "91822776"
---
Para abrir uma sessão SSH direta com o seu contentor, a sua aplicação deve estar em execução.

Cole o seguinte URL no browser e substitua `<app-name>` pelo nome da aplicação:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Se ainda não estiver autenticado, é necessário fazê-lo com a sua subscrição do Azure para se ligar. Uma vez autenticado, pode ver uma shell no browser, na qual pode executar comandos dentro do seu contentor.

![Ligação SSH](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
