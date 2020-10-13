---
title: Definir versão Redis para Azure Cache para Redis (Pré-visualização)
description: Saiba como configurar a versão Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: ed0f486afe466d31388fa99b4ce5f5754210533f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91571505"
---
# <a name="set-redis-version-for-azure-cache-for-redis-preview"></a>Definir versão Redis para Azure Cache para Redis (Pré-visualização)
Neste artigo, você vai aprender a configurar a versão de software Redis para ser usada com a sua instância cache. Azure Cache para Redis oferece a mais recente versão principal do Redis e pelo menos uma versão anterior. Irá atualizar estas versões regularmente à medida que o novo software Redis for lançado. Pode escolher entre as duas versões disponíveis. Tenha em mente que o seu cache será atualizado automaticamente para a próxima versão se a versão que está a utilizar atualmente já não estiver suportada.

## <a name="prerequisites"></a>Pré-requisitos
* Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)

> [!NOTE]
> Esta funcionalidade está neste momento em pré-visualização - [contacte-nos](mailto:azurecache@microsoft.com) se estiver interessado.
>

## <a name="create-a-cache"></a>Criar uma cache
Para criar uma cache, siga estes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e selecione **Criar um recurso**.
  
1. Na página **Nova,** selecione **Bases de Dados** e, em seguida, selecione **Azure Cache para Redis**.

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Selecione Azure Cache para Redis.":::
   
1. Na página **Basics,** configufique as definições para o seu novo cache.
   
    | Definição      | Valor sugerido  | Descrição |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Subscrição** | Selecione a sua subscrição. | A subscrição sob a qual criar este novo Azure Cache para a instância Redis. | 
    | **Grupo de recursos** | Selecione um grupo de recursos ou selecione **Criar novo** e introduza um novo nome de grupo de recursos. | Nome para o grupo de recursos para criar o seu cache e outros recursos. Ao colocar todos os recursos da sua aplicação num único grupo de recursos, pode facilmente geri-los ou eliminá-los em conjunto. | 
    | **Nome DNS** | Introduza um nome globalmente exclusivo. | O nome da cache deve ser uma cadeia entre 1 e 63 caracteres que contenha apenas números, letras ou hífenes. O nome deve começar e terminar com um número ou letra, e não pode conter hífenes consecutivos. O nome de *anfitrião* do seu caso de cache será * \<DNS name> .redis.cache.windows.net*. | 
    | **Localização** | Selecione uma localização. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que utilizarão o seu cache. |
    | **Tipo cache** | Selecione um [nível e tamanho de cache](https://azure.microsoft.com/pricing/details/cache/). |  O escalão de preço determina o tamanho, o desempenho e as funcionalidades que estão disponíveis para a cache. Para mais informações, consulte [Azure Cache para ver visão geral do Redis](cache-overview.md). |
   
1. Na página **Advanced,** escolha uma versão Redis para usar.
   
    :::image type="content" source="media/cache-how-to-version/select-redis-version.png" alt-text="Selecione Azure Cache para Redis.":::

1. Clique em **Criar**. 
   
    Demora um pouco para a cache criar. Pode monitorizar o progresso na cache Azure para a página Redis **Overview.** Quando **o Estado** aparece como **Running,** a cache está pronta a ser utilizada.

    > [!NOTE]
    > Neste momento, a versão Redis não pode ser alterada uma vez que uma cache é criada.
    >

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre o Azure Cache para funcionalidades redis.

> [!div class="nextstepaction"]
> [Cache Azure para os níveis de serviço Redis Premium](cache-overview.md#service-tiers)
