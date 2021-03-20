---
title: incluir ficheiro
description: incluir ficheiro
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: e208b52c67f173bd0d289715b63562df656b1ec9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88009798"
---
1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Publicar**.

1. Na caixa de diálogo **publicar,** selecione **Azure** for **Target** e, em seguida, selecione **Seguinte**. 

1. Selecione **Azure WebJobs** para **alvo específico** e, em seguida, selecione **Next**.

1. Selecione **Criar um novo Azure WebJob**.

   ![Escolha o alvo de publicação](./media/webjobs-publish-netcore/pick-publish-target.png)

1. Na caixa de diálogo **do Serviço de Aplicações (Windows),** utilize as definições de hospedagem na tabela seguinte.

    | Definição      | Valor sugerido  | Descrição                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Nome globalmente exclusivo | Nome que identifica exclusivamente a sua nova aplicação de funções. |
    | **Subscrição** | Escolher a sua subscrição | A subscrição do Azure que deve utilizar. |
    | **[Grupo de recursos](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  Nome do grupo de recursos no qual a sua aplicação de funções será criada. Escolha **Novo** para criar um grupo de recursos novo.|
    | **[Plano de Alojamento](../articles/app-service/overview-hosting-plans.md)** | Plano do Serviço de Aplicações | Um [plano do serviço de aplicações](../articles/app-service/overview-hosting-plans.md) especifica o local, tamanho e funcionalidades da farm de servidores Web que aloja a aplicação. Pode economizar dinheiro ao alojar várias aplicações, configurando as aplicações Web para partilhar um único plano do serviço de aplicações. Os planos do Serviço de Aplicações definem a região, tamanho de exemplo, contagem de escalas e SKU (Grátis, Compartilhados, Básicos, Padrão ou Premium). Escolha **Novo** para criar um novo plano de Serviço de Aplicações. |

    ![Caixa de diálogo Criar Serviço de Aplicações](./media/webjobs-publish-netcore/app-service-dialog.png)

1. Selecione **Criar** para criar um WebJob e recursos relacionados em Azure com estas definições e implementar o seu código de projeto.