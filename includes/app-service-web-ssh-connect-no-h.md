---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 060bc1039982cc0a77214d5dbe2a08de7a839c84
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67184738"
---
Para abrir uma sessão SSH direta com o seu recipiente, a sua aplicação deve estar em funcionamento.

Cole o seguinte URL no seu navegador e \<app-name> substitua-o pelo nome da sua aplicação:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Se ainda não estiver autenticado, é obrigado a autenticar com a sua assinatura Azure para se ligar. Uma vez autenticado, vê uma concha no navegador, onde pode executar comandos dentro do seu contentor.

![Ligação SSH](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
