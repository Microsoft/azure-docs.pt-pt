---
title: Ativar redundância de zona para Azure Cache para Redis (Pré-visualização)
description: Saiba como configurar a redundância de zona para o seu Azure Cache de nível Premium para instâncias Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 3c396d6d5b9da9a48e0d68a2d7d49561d6f688de
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91347467"
---
# <a name="enable-zone-redundancy-for-azure-cache-for-redis-preview"></a>Ativar redundância de zona para Azure Cache para Redis (Pré-visualização)
Neste artigo, você aprenderá a configurar uma instância Azure Cache redundante de zona usando o portal Azure.

A azure Cache para os níveis Redis Standard e Premium fornecem redundância incorporada, hospedando cada cache em duas máquinas virtuais dedicadas (VMs). Apesar de estes VMs estarem localizados em [domínios separados de falha e atualização do Azure](/azure/virtual-machines/windows/manage-availability) e altamente disponíveis, são suscetíveis a falhas de nível do datacenter. A Azure Cache para Redis também apoia a redundância de zona no seu nível Premium. Uma cache redundante de zona corre em VMs espalhados por [várias zonas de disponibilidade](/azure/virtual-machines/windows/manage-availability#use-availability-zones-to-protect-from-datacenter-level-failures). Proporciona maior resiliência e disponibilidade.

> [!IMPORTANT]
> Esta pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 
> 

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
   
1. Na página **New Redis Cache,** configufique as definições para o seu novo cache.
   
    | Definição      | Valor sugerido  | Descrição |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome DNS** | Introduza um nome globalmente exclusivo. | O nome da cache deve ser uma cadeia entre 1 e 63 caracteres que contenha apenas números, letras ou hífenes. O nome deve começar e terminar com um número ou letra, e não pode conter hífenes consecutivos. O nome de *anfitrião* do seu caso de cache será * \<DNS name> .redis.cache.windows.net*. | 
    | **Subscrição** | Desça e selecione a sua subscrição. | A subscrição sob a qual criar este novo Azure Cache para a instância Redis. | 
    | **Grupo de recursos** | Desça e selecione um grupo de recursos, ou **selecione Criar novo** e introduza um novo nome de grupo de recursos. | Nome para o grupo de recursos para criar o seu cache e outros recursos. Ao colocar todos os recursos da sua aplicação num único grupo de recursos, pode facilmente geri-los ou eliminá-los em conjunto. | 
    | **Localização** | Desça e selecione um local. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que utilizarão o seu cache. |
    | **Escalão de preço** | Desça e selecione uma cache [de nível Premium.](https://azure.microsoft.com/pricing/details/cache/) |  O escalão de preço determina o tamanho, o desempenho e as funcionalidades que estão disponíveis para a cache. Para mais informações, consulte [Azure Cache para ver visão geral do Redis](cache-overview.md). |
    | **Contagem de réplicas** | Deslize para escolher o número de réplicas. | O padrão é 1. |
    | **Zonas de disponibilidade** | Desça e selecione quais as zonas a utilizar. | Os VMs para o seu cache serão distribuídos pelas zonas selecionadas da forma mais homogénea possível. Por exemplo, se o seu cache tiver três réplicas e utilizar duas zonas, haverá dois VMs em cada zona. |
   
1. Depois de selecionar uma cache de nível Premium, perguntar-lhe-á se ativa ou não o agrupamento redis. Deixe **o Agrupamento** como *Desativado*. 
   
    :::image type="content" source="media/cache-how-to-premium-clustering/redis-clustering-disabled.png" alt-text="Configurar o aglomerado Redis.":::

    > [!NOTE]
    > O suporte de redundância de zona só funciona com caches não agrupados e não replicados atualmente. Além disso, não suporta ligações privadas, escala, persistência de dados ou importação/exportação.
    >

1. Clique em **Create** (Criar). 
   
    :::image type="content" source="media/cache-how-to-zone-redundancy/create-zones.png" alt-text="Crie cache Azure para Redis.":::
   
    Demora um pouco para a cache criar. Pode monitorizar o progresso na cache Azure para a página Redis **Overview.** Quando **o Estado** aparece como **Running,** a cache está pronta a ser utilizada.

    > [!NOTE]
    > As zonas de disponibilidade não podem ser alteradas após a criação de uma cache.
    >

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre o Azure Cache para funcionalidades redis.

> [!div class="nextstepaction"]
> [Cache Azure para os níveis de serviço Redis Premium](cache-overview.md#service-tiers)
