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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73720450"
---
### <a name="retrieve-host-name-ports-and-access-keys-from-the-azure-portal"></a>Recupere o nome do anfitrião, portas e chaves de acesso do portal Azure

Para ligar a um Cache Azure para a instância Redis, os clientes em cache precisam do nome de anfitrião, portas e uma chave para a cache. Alguns clientes podem mencionar esses itens com nomes ligeiramente diferentes. Você pode obter o nome anfitrião, portas e chaves do [portal Azure](https://portal.azure.com).

- Para obter as teclas de acesso, a partir da sua cache left navigation, selecione **Teclas de acesso**. 
  
  ![Cache Azure para chaves Redis](media/redis-cache-access-keys/redis-cache-keys.png)

- Para obter o nome anfitrião e portas, a partir da sua cache navegação esquerda, **selecione Propriedades**. O nome do anfitrião é do * \<nome DNS>.redis.cache.windows.net*.

  ![Cache Azure para propriedades Redis](media/redis-cache-access-keys/redis-cache-hostname-ports.png)

