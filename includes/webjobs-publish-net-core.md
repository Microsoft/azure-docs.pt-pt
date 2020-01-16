---
title: incluir ficheiro
description: incluir ficheiro
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4860532e59227618ce819772887556719ecb53fc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021068"
---
1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Publicar**.

1. Na caixa de diálogo **publicar** , selecione **Microsoft Azure serviço de aplicativo**, escolha **criar novo**e, em seguida, selecione **publicar**.

   ![Escolher destino de publicação](./media/webjobs-publish-netcore/pick-publish-target.png)

1. Na caixa de diálogo **Criar serviço de aplicativo** , use as configurações de hospedagem conforme especificado na tabela abaixo da imagem:

    ![Caixa de diálogo Criar App Service](./media/webjobs-publish-netcore/app-service-dialog.png)

    | Definição      | Valor sugerido  | Descrição                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome da Aplicação** | Nome globalmente exclusivo | Nome que identifica exclusivamente a sua nova aplicação de funções. |
    | **Subscrição** | Escolher a sua subscrição | A subscrição do Azure que deve utilizar. |
    | **[Grupo de Recursos](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  Nome do grupo de recursos no qual a sua aplicação de funções será criada. Escolha **Novo** para criar um grupo de recursos novo.|
    | **[Plano de hospedagem](../articles/app-service/overview-hosting-plans.md)** | Plano do App Service | Um [plano do serviço de aplicações](../articles/app-service/overview-hosting-plans.md) especifica o local, tamanho e funcionalidades da farm de servidores Web que aloja a aplicação. Pode economizar dinheiro ao alojar várias aplicações, configurando as aplicações Web para partilhar um único plano do serviço de aplicações. Os planos do serviço de aplicativo definem a região, o tamanho da instância, a contagem de escala e a SKU (gratuito, compartilhado, básico, Standard ou Premium). Escolha **novo** para criar um novo plano do serviço de aplicativo. |

1. Clique em **criar** para criar um WebJob e recursos relacionados no Azure com essas configurações e implantar o código do projeto.