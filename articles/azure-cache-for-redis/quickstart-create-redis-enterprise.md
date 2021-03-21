---
title: 'Quickstart: Criar uma cache Redis Enterprise'
description: Neste quickstart, aprenda a criar um exemplo de Azure Cache para Redis em níveis enterprise
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: mvc
ms.topic: quickstart
ms.date: 02/08/2021
ms.openlocfilehash: 8b450ddff0952777652a957ba2ed554a4bc6497d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584827"
---
# <a name="quickstart-create-a-redis-enterprise-cache"></a>Quickstart: Criar uma cache Redis Enterprise

Azure Cache para os níveis da Redis Enterprise fornecem a [Redis Enterprise](https://redislabs.com/redis-enterprise/) totalmente integrada e gerida em Azure. Estes novos níveis são:
* Enterprise, que usa memória volátil (DRAM) numa máquina virtual para armazenar dados
* Enterprise Flash, que utiliza memória volátil e não volátil (NVMe ou SSD) para armazenar dados.

## <a name="prerequisites"></a>Pré-requisitos

Vai precisar de uma assinatura Azure antes de começar. Se não tiver uma, crie uma [conta.](https://azure.microsoft.com/) Para mais informações, consulte [considerações especiais para os níveis da Enterprise](cache-overview.md#special-considerations-for-enterprise-tiers).

## <a name="create-a-cache"></a>Criar uma cache
1. Para criar uma cache, inscreva-se no portal Azure e selecione **Criar um recurso.**

1. Na página **Nova,** selecione **Bases de Dados** e, em seguida, selecione **Azure Cache para Redis**.
   
   :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Selecione Azure Cache para Redis":::
   
1. Na página **New Redis Cache,** configufique as definições para o seu novo cache.
   
   | Definição      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Subscrição** | Desça e selecione a sua subscrição. | A subscrição sob a qual criar este novo Azure Cache para a instância Redis. | 
   | **Grupo de recursos** | Desça e selecione um grupo de recursos, ou **selecione Criar novo** e introduza um novo nome de grupo de recursos. | Nome para o grupo de recursos para criar o seu cache e outros recursos. Ao colocar todos os recursos da sua aplicação num único grupo de recursos, pode facilmente geri-los ou eliminá-los em conjunto. | 
   | **Nome DNS** | Introduza um nome globalmente exclusivo. | O nome da cache deve ser uma cadeia entre 1 e 63 caracteres que contenha apenas números, letras ou hífenes. O nome deve começar e terminar com um número ou letra, e não pode conter hífenes consecutivos. O nome de *anfitrião* do seu cache *\<DNS name> <Azure region> será. . . redisenterprise.cache.azure.net.* | 
   | **Localização** | Desça e selecione um local. | Os níveis de empresa estão disponíveis nas regiões de Azure selecionadas. |
   | **Tipo cache** | Desça e selecione um nível *Enterprise* ou *Enterprise Flash* e um tamanho. |  O nível determina o tamanho, desempenho e funcionalidades que estão disponíveis para a cache. |
   
   :::image type="content" source="media/cache-create/enterprise-tier-basics.png" alt-text="Separador básico de nível de empresa":::

   > [!NOTE] 
   > Certifique-se de verificar a caixa em "Termos" antes de prosseguir.
   >

1. Selecione **Seguinte: Networking** e saltar.

1. Selecione **Seguinte: Política avançada** e definida **de clustering** para a **Enterprise**. Ativar **o acesso não-TLS apenas** se pretender ligar-se à nova cache sem utilizar o TLS. No entanto, isto não é recomendado.

   :::image type="content" source="media/cache-create/enterprise-tier-advanced.png" alt-text="Separador avançado de nível empresarial":::

1. Selecione **Seguinte: Etiquetas** e saltar.

1. Selecione **Seguinte: Rever + criar**.

   :::image type="content" source="media/cache-create/enterprise-tier-summary.png" alt-text="Análise de nível de empresa + Criar separador":::

1. Reveja as definições e clique em **Criar**.
   
   Leva algum tempo para a cache criar. Pode monitorizar o progresso na cache Azure para a página Redis **Overview.** Quando **o Estado** aparece como **Running,** a cache está pronta a ser utilizada.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a criar uma instância de nível empresarial de Azure Cache para Redis.

> [!div class="nextstepaction"]
> [Crie uma aplicação web ASP.NET que utilize um Cache Azure para Redis.](./cache-web-app-howto.md)

