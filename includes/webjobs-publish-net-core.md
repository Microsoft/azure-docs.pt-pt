---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4860532e59227618ce819772887556719ecb53fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76021068"
---
1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Publicar**.

1. No diálogo **publicar,** selecione **o Microsoft Azure App Service**, escolha Create **New**e, em seguida, selecione **Publicar**.

   ![Escolha o alvo de publicação](./media/webjobs-publish-netcore/pick-publish-target.png)

1. No diálogo **'Serviço de aplicações Create',** utilize as definições de hospedagem conforme especificado na tabela abaixo da imagem:

    ![Caixa de diálogo Criar App Service](./media/webjobs-publish-netcore/app-service-dialog.png)

    | Definição      | Valor sugerido  | Descrição                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome da Aplicação** | Nome globalmente exclusivo | Nome que identifica exclusivamente a sua nova aplicação de funções. |
    | **Subscrição** | Escolher a sua subscrição | A subscrição do Azure que deve utilizar. |
    | **[Grupo de Recursos](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  Nome do grupo de recursos no qual a sua aplicação de funções será criada. Escolha **Novo** para criar um grupo de recursos novo.|
    | **[Plano de Hospedagem](../articles/app-service/overview-hosting-plans.md)** | Plano do App Service | Um [plano do serviço de aplicações](../articles/app-service/overview-hosting-plans.md) especifica o local, tamanho e funcionalidades da farm de servidores Web que aloja a aplicação. Pode economizar dinheiro ao alojar várias aplicações, configurando as aplicações Web para partilhar um único plano do serviço de aplicações. Os planos do Serviço de Aplicações definem a região, tamanho de exemplo, contagem de escalas e SKU (Grátis, Compartilhados, Básicos, Padrão ou Premium). Escolha **Novo** para criar um novo plano de Serviço de Aplicações. |

1. Clique em **Criar** para criar um WebJob e recursos relacionados em Azure com estas definições e implementar o seu código de projeto.