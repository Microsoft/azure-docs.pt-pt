---
title: incluir ficheiro
description: incluir ficheiro
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 11/05/2019
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: a737e130d616a67bab28c7c96c0372216a6707af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002471"
---
### <a name="retrieve-host-name-ports-and-access-keys-from-the-azure-portal"></a>Recupere o nome do anfitrião, portas e chaves de acesso do portal Azure

Para se conectarem a um Cache Azure para a instância Redis, os clientes da cache precisam do nome de anfitrião, portas e uma chave para a cache. Alguns clientes podem mencionar esses itens com nomes ligeiramente diferentes. Pode obter o nome de anfitrião, portas e chaves do [portal Azure.](https://portal.azure.com)

- Para obter as teclas de acesso, a partir da sua cache navegação à esquerda, selecione **as teclas de acesso**. 
  
  ![Cache Azure para chaves Redis](media/redis-cache-access-keys/redis-cache-keys.png)

- Para obter o nome de anfitrião e portas, a partir da sua cache navegação à esquerda, selecione **Propriedades**. O nome do anfitrião é do formulário *\<DNS name> .redis.cache.windows.net*.

  ![Cache Azure para propriedades redis](media/redis-cache-access-keys/redis-cache-hostname-ports.png)

