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
ms.openlocfilehash: 97387e24d5b55c1438a69da1a1fd0a9bc1720e47
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56751371"
---
1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Publicar**.

1. Na **Publish** caixa de diálogo, selecione **serviço de aplicações do Microsoft Azure**, escolha **criar novo**e, em seguida, selecione **publicar**.

   ![Escolher Publicar destino](./media/webjobs-publish-netcore/pick-publish-target.png)

1. Na **criar serviço de aplicações** caixa de diálogo, use as alojamento definições especificadas na tabela abaixo da imagem:

    ![Caixa de diálogo Criar App Service](./media/webjobs-publish-netcore/app-service-dialog.png)

    | Definição      | Valor sugerido  | Descrição                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome da Aplicação** | Nome globalmente exclusivo | Nome que identifica exclusivamente a sua nova aplicação de funções. |
    | **Subscrição** | Escolher a sua subscrição | A subscrição do Azure que deve utilizar. |
    | **[Grupo de Recursos](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Nome do grupo de recursos no qual a sua aplicação de funções será criada. Escolha **Novo** para criar um grupo de recursos novo.|
    | **[Plano de alojamento](../articles/app-service/overview-hosting-plans.md)** | Plano do App Service | Um [plano do serviço de aplicações](../articles/app-service/overview-hosting-plans.md) especifica o local, tamanho e funcionalidades da farm de servidores Web que aloja a aplicação. Pode economizar dinheiro ao alojar várias aplicações, configurando as aplicações Web para partilhar um único plano do serviço de aplicações. Planos de serviço de aplicações definem a região, o tamanho da instância, a contagem de escalas e o SKU (gratuito, partilhado, básico, Standard ou Premium). Escolher **New** para criar um novo plano do serviço de aplicações. |

1. Clique em **criar** para criar um WebJob e recursos relacionados no Azure com estas definições e implementar o código do projeto.