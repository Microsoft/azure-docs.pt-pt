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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "67184738"
---
Para abrir uma sessão sSH direta com o seu recipiente, a sua aplicação deve estar em funcionamento.

Colhe o seguinte URL no \<seu navegador e substitua o nome da aplicação> pelo nome da sua aplicação:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Se ainda não tiver sido autenticado, é obrigado a autenticar com a sua assinatura Azure para se ligar. Uma vez autenticado, você vê uma concha no navegador, onde você pode executar comandos dentro do seu recipiente.

![Conexão SSH](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
