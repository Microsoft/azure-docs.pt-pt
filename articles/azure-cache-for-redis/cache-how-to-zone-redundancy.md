---
title: Ativar redundância de zona para Azure Cache para Redis (Pré-visualização)
description: Saiba como configurar a redundância de zona para o seu Azure Cache de nível Premium para instâncias Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 33c346fa2e4572799ad6341bd5115cdd6e3b9ec9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91569980"
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
   
1. Na página **Basics,** configufique as definições para o seu novo cache.
   
    | Definição      | Valor sugerido  | Descrição |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Subscrição** | Selecione a sua subscrição. | A subscrição sob a qual criar este novo Azure Cache para a instância Redis. | 
    | **Grupo de recursos** | Selecione um grupo de recursos ou selecione **Criar novo** e introduza um novo nome de grupo de recursos. | Nome para o grupo de recursos para criar o seu cache e outros recursos. Ao colocar todos os recursos da sua aplicação num único grupo de recursos, pode facilmente geri-los ou eliminá-los em conjunto. | 
    | **Nome DNS** | Introduza um nome globalmente exclusivo. | O nome da cache deve ser uma cadeia entre 1 e 63 caracteres que contenha apenas números, letras ou hífenes. O nome deve começar e terminar com um número ou letra, e não pode conter hífenes consecutivos. O nome de *anfitrião* do seu caso de cache será * \<DNS name> .redis.cache.windows.net*. | 
    | **Localização** | Selecione uma localização. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que utilizarão o seu cache. |
    | **Tipo cache** | Selecione uma cache [de nível Premium.](https://azure.microsoft.com/pricing/details/cache/) |  O escalão de preço determina o tamanho, o desempenho e as funcionalidades que estão disponíveis para a cache. Para mais informações, consulte [Azure Cache para ver visão geral do Redis](cache-overview.md). |
   
1. Na página **Advanced,** escolha **a contagem de réplicas.**
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="Selecione Azure Cache para Redis.":::

1. Selecione **zonas de disponibilidade**. 
   
    :::image type="content" source="media/cache-how-to-zone-redundancy/create-zones.png" alt-text="Selecione Azure Cache para Redis.":::

1. Deixe outras opções nas suas definições predefinitivos. 

    > [!NOTE]
    > O suporte de redundância de zona só funciona com caches não agrupados e não replicados atualmente. Além disso, não suporta ligações privadas, escala, persistência de dados ou importação/exportação.
    >

1. Clique em **Criar**. 
   
    Demora um pouco para a cache criar. Pode monitorizar o progresso na cache Azure para a página Redis **Overview.** Quando **o Estado** aparece como **Running,** a cache está pronta a ser utilizada.
   
    > [!NOTE]
    > As zonas de disponibilidade não podem ser alteradas após a criação de uma cache.
    >

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre o Azure Cache para funcionalidades redis.

> [!div class="nextstepaction"]
> [Cache Azure para os níveis de serviço Redis Premium](cache-overview.md#service-tiers)
