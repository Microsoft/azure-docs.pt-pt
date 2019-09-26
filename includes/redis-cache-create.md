---
title: incluir ficheiro
description: incluir ficheiro
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 03/28/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: f059f23031c2cdd74daaa856213d7e06f87dc27c
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273921"
---
1. Para criar uma cache, primeiro, inicie sessão no [portal do Azure](https://portal.azure.com). Em seguida, selecione **criar um recurso** > **bases de dados** > **a Cache de Redis do Azure**.

    ![Novo cache do Azure para o menu Redis](media/redis-cache-create/redis-cache-new-cache-menu.png)

2. Na **nova Cache do Azure para Redis**, configure as definições para a nova cache.

    | Definição      | Valor sugerido  | Descrição |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome DNS** | Nome globalmente exclusivo | O nome da cache. Tem de ser uma cadeia entre 1 e 63 carateres e conter apenas números, letras e o caráter `-`. O nome da cache não pode começar nem terminar com o caráter `-` e os carateres `-` consecutivos não são válidos.  | 
    | **Subscrição** | A sua subscrição | A subscrição sob a qual esta nova Cache do Azure para a instância de Redis é criado. | 
    | **Grupo de recursos** |  *TestResources* | Nome do grupo de recursos novo no qual a cache será criada. Ao colocar todos os recursos de uma aplicação num grupo, pode geri-los em conjunto. Por exemplo, eliminar o grupo de recursos elimina todos os recursos associados à aplicação. | 
    | **Localização** | EUA Leste | Selecione uma [região](https://azure.microsoft.com/regions/) próxima de outros serviços que irão utilizar a cache. |
    | **[Escalão de preço](https://azure.microsoft.com/pricing/details/cache/)** |  C0 básico (Cache de 250 MB) |  O escalão de preço determina o tamanho, o desempenho e as funcionalidades que estão disponíveis para a cache. Para obter mais informações, consulte [Cache do Azure para a descrição geral do Redis](../articles/azure-cache-for-redis/cache-overview.md). |
    | **Afixar ao dashboard** |  Selecionado | Afixe a nova cache ao seu dashboard para que esta seja fácil de localizar. |

    ![Criar cache do Azure para Redis](media/redis-cache-create/redis-cache-cache-create.png) 

3. Depois de configurar as definições da nova cache, selecione **Criar**. 

    A criação da cache pode demorar alguns minutos. Para verificar o estado, pode monitorizar o progresso no dashboard. Uma vez criada, a nova cache apresenta o estado **Em Execução** e está pronta para ser utilizada.

    ![Cache do Azure para Redis criado](media/redis-cache-create/redis-cache-cache-created.png)

