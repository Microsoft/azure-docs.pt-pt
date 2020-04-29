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
ms.openlocfilehash: 3c064aa8e57a77b96161da06847f543816be1217
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73719107"
---
1. Para criar uma cache, inscreva-se no [portal Azure](https://portal.azure.com) e selecione **Criar um recurso**. 
   
   ![Selecione Criar um recurso](media/redis-cache-create/create-a-resource.png)
   
1. Na **página Nova,** selecione Bases de **Dados** e, em seguida, selecione **Azure Cache para Redis**.
   
   ![Selecione Azure Cache para Redis](media/redis-cache-create/redis-cache-new-cache-menu.png)
   
1. Na página **New Redis Cache,** configure as definições para a sua nova cache.
   
   | Definição      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nome DNS** | Introduza um nome globalmente exclusivo. | O nome cache deve ser uma cadeia entre 1 e 63 caracteres que contenha apenas números, letras ou hífenes. O nome deve começar e terminar com um número ou letra, e não pode conter hífenes consecutivos. O nome de *anfitrião* da sua instância de cache será * \<o nome DNS>.redis.cache.windows.net*. | 
   | **Subscrição** | Desça e selecione a sua subscrição. | A subscrição sob a qual criar este novo Azure Cache para redis exemplo. | 
   | **Grupo de recursos** | Desça e selecione um grupo de recursos, ou selecione **Criar novo** e insira um novo nome de grupo de recursos. | Nome para o grupo de recursos no qual criar a sua cache e outros recursos. Ao colocar todos os recursos da sua aplicação num só grupo de recursos, pode facilmente geri-los ou eliminá-los juntos. | 
   | **Localização** | Desça e selecione um local. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que utilizarão a sua cache. |
   | **Nível de preços** | Desça e selecione um [nível](https://azure.microsoft.com/pricing/details/cache/)de preços . |  O escalão de preço determina o tamanho, o desempenho e as funcionalidades que estão disponíveis para a cache. Para mais informações, consulte [Azure Cache para veravista de Redis](../articles/azure-cache-for-redis/cache-overview.md). |
   
1. Selecione **Criar**. 
   
   ![Criar cache azure para redis](media/redis-cache-create/redis-cache-cache-create.png) 
   
   Leva um tempo para a cache criar. Pode monitorizar o progresso na página azure cache para redis **overview.** Quando **o Estado** aparecer como **Funcionamento,** a cache está pronta a ser utilizada.
   
   ![Azure Cache para Redis criado](media/redis-cache-create/redis-cache-cache-created.png)

