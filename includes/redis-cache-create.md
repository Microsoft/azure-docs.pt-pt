---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: redis-cache
author: curib
ms.service: cache
ms.topic: include
ms.date: 10/06/2020
ms.author: cauribeg
ms.custom: include file
ms.openlocfilehash: ae9526a47a77118503274fc824538980ebcd50de
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91829276"
---
1. Para criar uma cache, inscreva-se no [portal Azure](https://portal.azure.com) e selecione **Criar um recurso.**

    :::image type="content" source="media/redis-cache-create/create-resource.png" alt-text="Selecione Azure Cache para Redis.":::

   
1. Na página **Nova,** selecione **Bases de Dados** e, em seguida, selecione **Azure Cache para Redis**.

    :::image type="content" source="media/redis-cache-create/select-cache.png" alt-text="Selecione Azure Cache para Redis.":::
   
1. Na página **New Redis Cache,** configufique as definições para o seu novo cache.
   
   | Definição      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nome DNS** | Introduza um nome globalmente exclusivo. | O nome da cache deve ser uma cadeia entre 1 e 63 caracteres que contenha apenas números, letras ou hífenes. O nome deve começar e terminar com um número ou letra, e não pode conter hífenes consecutivos. O nome de *anfitrião* do seu caso de cache será * \<DNS name> .redis.cache.windows.net*. | 
   | **Subscrição** | Desça e selecione a sua subscrição. | A subscrição sob a qual criar este novo Azure Cache para a instância Redis. | 
   | **Grupo de recursos** | Desça e selecione um grupo de recursos, ou **selecione Criar novo** e introduza um novo nome de grupo de recursos. | Nome para o grupo de recursos para criar o seu cache e outros recursos. Ao colocar todos os recursos da sua aplicação num único grupo de recursos, pode facilmente geri-los ou eliminá-los em conjunto. | 
   | **Localização** | Desça e selecione um local. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que utilizarão o seu cache. |
   | **Escalão de preço** | Desça e selecione um [nível de preços](https://azure.microsoft.com/pricing/details/cache/). |  O escalão de preço determina o tamanho, o desempenho e as funcionalidades que estão disponíveis para a cache. Para mais informações, consulte [Azure Cache para ver visão geral do Redis](../articles/azure-cache-for-redis/cache-overview.md). |

1. Selecione o **separador 'Rede'** ou clique no botão **'Rede'** na parte inferior da página.

1. No **separador 'Rede',** selecione o seu método de conectividade.

1. Selecione o **Seguinte: Separador avançado** ou clique no **seguinte: Botão avançado** na parte inferior da página.

1. No separador **Avançado** para uma instância de cache básica ou padrão, selecione o interruptor de ativação se pretender ativar uma porta não TLS.

1. No separador **Avançado** para a instância de cache premium, configurar as definições para a porta não-TLS, clustering e persistência de dados.

1. Selecione o **separador Seguinte: Tags** ou clique no botão **Seguinte: Tags** na parte inferior da página.

1. Opcionalmente, no separador **Tags, insira** o nome e o valor se desejar categorizar o recurso. 

1. Selecione **Review + criar**. É levado para o separador 'Rever +' onde o Azure valida a sua configuração.

1. Depois de aparecer a mensagem de validação verde, selecione **Criar**.

Demora um pouco para a cache criar. Pode monitorizar o progresso na cache Azure para a página Redis **Overview.**   Quando **o Estado**aparece como    **Running,** a cache está pronta a ser utilizada. 