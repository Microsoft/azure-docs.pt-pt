---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/02/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6fc1f4152b2e16e1597c018e5af6e0245b075c3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "78255801"
---
## <a name="robots933456-in-logs"></a>robots933456 nos registos

Pode ver a seguinte mensagem nos registos do contentor:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

Pode ignorar esta mensagem. `/robots933456.txt` é um caminho de URL fictício que o Serviço de Aplicações utiliza para verificar se o contentor consegue satisfazer pedidos. Uma resposta 404 indica simplesmente que o caminho não existe, mas permite que o Serviço de Aplicações saiba que o contentor está em bom estado de funcionamento e pronto para responder aos pedidos.

