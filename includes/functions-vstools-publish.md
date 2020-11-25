---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/30/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: ef617b9ed6c35d33350b173efe6189b5b79b6ae6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008245"
---
1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Publicar**.

1. No **Target**, selecione **Azure** :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-1.png" alt-text="Select Azure target":::

1. No **alvo específico**, selecione **Azure Function App (Windows)**

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-2.png" alt-text="Selecione app de função Azure":::

1. Em **Instância de Função,** selecione **Criar uma nova Função Azure...** e, em seguida, utilizar os valores especificados no seguinte quadro:

    | Definição      | Valor  | Descrição                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Nome globalmente exclusivo | Nome que identifica exclusivamente a sua nova aplicação de funções. Aceite este nome ou insira um novo nome. Os caracteres válidos são: `a-z` `0-9` , e `-` . |
    | **Subscrição** | A sua subscrição | A subscrição do Azure que deve utilizar. Aceite esta subscrição ou selecione uma nova da lista de drop-down. |
    | **[Grupo de recursos](../articles/azure-resource-manager/management/overview.md)** | Nome do seu grupo de recursos |  O grupo de recursos para criar a sua aplicação de função. Selecione um grupo de recursos existente da lista de drop-down ou escolha **Novo** para criar um novo grupo de recursos.|
    | **[Tipo de Plano](../articles/azure-functions/functions-scale.md)** | Consumo | Quando publica o seu projeto numa aplicação de função que funciona num [plano de Consumo,](../articles/azure-functions/functions-scale.md#consumption-plan)paga apenas para execuções da sua aplicação de funções. Outros planos de hospedagem incorrem em custos mais elevados. |
    | **Localização** | Localização do serviço de aplicações | Escolha uma **Localização** numa [região](https://azure.microsoft.com/regions/) próxima ou noutros serviços a que as suas funções acedam. |
    | **[Storage do Azure](../articles/storage/common/storage-account-create.md)** | Conta de armazenamento para fins gerais | Uma conta de Armazenamento Azure é exigida pelo tempo de funcionamento das Funções. Selecione **Novo** para configurar uma conta de armazenamento para fins gerais. Também pode escolher uma conta existente que satisfaça os requisitos da [conta de armazenamento.](../articles/azure-functions/functions-scale.md#storage-account-requirements)  |

    ![Caixa de diálogo Criar App Service](./media/functions-vstools-publish/functions-visual-studio-publish.png)

1. Selecione **Criar** para criar uma aplicação de função e seus recursos relacionados em Azure. 
1. No **caso funções,** certifique-se de que **o Run a partir do ficheiro de embalagem** é verificado. A sua aplicação de função é implementada utilizando [o zip deploy](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) com o modo [Run-From-Package](../articles/azure-functions/run-functions-from-deployment-package.md) ativado. Este é o método de implementação recomendado para o seu projeto de funções, uma vez que resulta num melhor desempenho. 

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-4.png" alt-text="Terminar a criação de perfil":::

1. Selecione **Terminar**, e na página Publicar, selecione **Publicar** para implementar o pacote que contém os seus ficheiros de projeto para a sua nova aplicação de função em Azure. 

    Após a implementação completar o URL raiz da aplicação de função em Azure é mostrado no separador **Publicar.** 
    
1.  No separador Publicar, escolha **Gerir no Cloud Explorer.** Isto abre a nova aplicação de função Azure recurso no Cloud Explorer. 
    
    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Mensagem de publicação bem-sucedida":::
    
    O Cloud Explorer permite-lhe utilizar o Visual Studio para visualizar os conteúdos do site, iniciar e parar a aplicação de funções, e navegar diretamente para funcionar os recursos da aplicação no Azure e no portal Azure. 
