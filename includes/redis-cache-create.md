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
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719107"
---
1. Para criar um cache, entre no [portal do Azure](https://portal.azure.com) e selecione **criar um recurso**. 
   
   ![Selecione criar um recurso](media/redis-cache-create/create-a-resource.png)
   
1. Na página **novo** , selecione **bancos de dados** e, em seguida, selecione **cache do Azure para Redis**.
   
   ![Selecione o cache do Azure para Redis](media/redis-cache-create/redis-cache-new-cache-menu.png)
   
1. Na página **novo cache Redis** , defina as configurações para o novo cache.
   
   | Definição      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nome DNS** | Insira um nome globalmente exclusivo. | O nome do cache deve ser uma cadeia entre 1 e 63 caracteres que contenham apenas números, letras ou hifens. O nome deve começar e terminar com um número ou uma letra e não pode conter hifens consecutivos. O *nome do host* da instância do cache será *\<nome DNS >. Redis. cache. Windows. net*. | 
   | **Subscrição** | Clique na lista suspensa e selecione sua assinatura. | A assinatura na qual criar esse novo cache do Azure para a instância de Redis. | 
   | **Grupo de recursos** | Na lista suspensa, selecione um grupo de recursos ou selecione **criar novo** e insira um novo nome de grupo de recursos. | Nome do grupo de recursos no qual criar o cache e outros recursos. Ao colocar todos os seus recursos de aplicativo em um grupo de recursos, você pode facilmente gerenciá-los ou excluí-los juntos. | 
   | **Localização** | Na lista suspensa e selecione um local. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que usarão seu cache. |
   | **Escalão de preço** | Menu suspenso e selecione um [tipo de preço](https://azure.microsoft.com/pricing/details/cache/). |  O escalão de preço determina o tamanho, o desempenho e as funcionalidades que estão disponíveis para a cache. Para obter mais informações, consulte [visão geral do cache do Azure para Redis](../articles/azure-cache-for-redis/cache-overview.md). |
   
1. Selecione **Criar**. 
   
   ![Criar cache do Azure para Redis](media/redis-cache-create/redis-cache-cache-create.png) 
   
   Leva um tempo para que o cache seja criado. Você pode monitorar o progresso na página de **visão geral** do cache do Azure para Redis. Quando o **status** é mostrado como **em execução**, o cache está pronto para uso.
   
   ![Cache do Azure para Redis criado](media/redis-cache-create/redis-cache-cache-created.png)

