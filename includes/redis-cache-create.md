---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 11/05/2019
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 3c064aa8e57a77b96161da06847f543816be1217
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "73719107"
---
1. Para criar uma cache, inscreva-se no [portal Azure](https://portal.azure.com) e selecione **Criar um recurso.** 
   
   ![Selecione Criar um recurso](media/redis-cache-create/create-a-resource.png)
   
1. Na página **Nova,** selecione **Bases de Dados** e, em seguida, selecione **Azure Cache para Redis**.
   
   ![Selecione Azure Cache para Redis](media/redis-cache-create/redis-cache-new-cache-menu.png)
   
1. Na página **New Redis Cache,** configufique as definições para o seu novo cache.
   
   | Definição      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nome DNS** | Introduza um nome globalmente exclusivo. | O nome da cache deve ser uma cadeia entre 1 e 63 caracteres que contenha apenas números, letras ou hífenes. O nome deve começar e terminar com um número ou letra, e não pode conter hífenes consecutivos. O nome de *anfitrião* do seu caso de cache será * \<DNS name> .redis.cache.windows.net*. | 
   | **Subscrição** | Desça e selecione a sua subscrição. | A subscrição sob a qual criar este novo Azure Cache para a instância Redis. | 
   | **Grupo de recursos** | Desça e selecione um grupo de recursos, ou **selecione Criar novo** e introduza um novo nome de grupo de recursos. | Nome para o grupo de recursos para criar o seu cache e outros recursos. Ao colocar todos os recursos da sua aplicação num único grupo de recursos, pode facilmente geri-los ou eliminá-los em conjunto. | 
   | **Localização** | Desça e selecione um local. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que utilizarão o seu cache. |
   | **Nível de preços** | Desça e selecione um [nível de preços](https://azure.microsoft.com/pricing/details/cache/). |  O escalão de preço determina o tamanho, o desempenho e as funcionalidades que estão disponíveis para a cache. Para mais informações, consulte [Azure Cache para ver visão geral do Redis](../articles/azure-cache-for-redis/cache-overview.md). |
   
1. Selecione **Criar**. 
   
   ![Criar cache Azure para Redis](media/redis-cache-create/redis-cache-cache-create.png) 
   
   Demora um pouco para a cache criar. Pode monitorizar o progresso na cache Azure para a página Redis **Overview.** Quando **o Estado** aparece como **Running,** a cache está pronta a ser utilizada.
   
   ![Azure Cache para Redis criado](media/redis-cache-create/redis-cache-cache-created.png)

