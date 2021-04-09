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
ms.openlocfilehash: 5b537b88052ce4042e346732f3dc63aaec6621cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100529673"
---
1. No **Solution Explorer,** clique com o botão direito no projeto e selecione **Publicar** e no **Target**, selecione **Azure** em **seguida, Next**.

1. Para o **alvo específico**, escolha **Azure Function App (Windows)**, que cria uma aplicação de função que funciona no Windows.

1. Em **Instância de Função,** escolha **Criar uma nova função Azure...** 

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-new-resource.png" alt-text="Criar uma nova instância de aplicação de funções":::

1. Criar um novo exemplo utilizando os valores especificados no quadro seguinte:

    | Definição      | Valor  | Descrição                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Nome globalmente exclusivo | Nome que identifica exclusivamente a sua nova aplicação de funções. Aceite este nome ou insira um novo nome. Os caracteres válidos são: `a-z` `0-9` , e `-` . |
    | **Subscrição** | A sua subscrição | A subscrição do Azure que deve utilizar. Aceite esta subscrição ou selecione uma nova da lista de drop-down. |
    | **[Grupo de recursos](../articles/azure-resource-manager/management/overview.md)** | Nome do seu grupo de recursos |  O grupo de recursos para criar a sua aplicação de função. Selecione um grupo de recursos existente da lista de drop-down ou escolha **Novo** para criar um novo grupo de recursos.|
    | **[Tipo de Plano](../articles/azure-functions/functions-scale.md)** | Consumo | Quando publica o seu projeto numa aplicação de função que funciona num [plano de Consumo,](../articles/azure-functions/consumption-plan.md)paga apenas para execuções da sua aplicação de funções. Outros planos de hospedagem incorrem em custos mais elevados. |
    | **Localização** | Localização do serviço de aplicações | Escolha uma **Localização** numa [região](https://azure.microsoft.com/regions/) próxima ou noutros serviços a que as suas funções acedam. |
    | **[Armazenamento do Azure](../articles/azure-functions/storage-considerations.md)** | Conta de armazenamento para fins gerais | Uma conta de Armazenamento Azure é exigida pelo tempo de funcionamento das Funções. Selecione **Novo** para configurar uma conta de armazenamento para fins gerais. Também pode escolher uma conta existente que satisfaça os requisitos da [conta de armazenamento.](../articles/azure-functions/storage-considerations.md#storage-account-requirements)  |

    ![Caixa de diálogo Criar App Service](./media/functions-vstools-publish/functions-visual-studio-publish.png)

1. Selecione **Criar** para criar uma aplicação de função e seus recursos relacionados em Azure. O estado de criação de recursos é mostrado na parte inferior esquerda da janela. 

1. No **caso de Funções,** certifique-se de que **o Run a partir do ficheiro de embalagem** é verificado. A sua aplicação de função é implementada utilizando [o zip deploy](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) com o modo [Run-From-Package](../articles/azure-functions/run-functions-from-deployment-package.md) ativado. Este é o método de implementação recomendado para o seu projeto de funções, uma vez que resulta num melhor desempenho. 

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-4.png" alt-text="Terminar a criação de perfil":::

1. Selecione **Terminar**, e na página Publicar, selecione **Publicar** para implementar o pacote que contém os seus ficheiros de projeto para a sua nova aplicação de função em Azure. 

    Após a implementação completar o URL raiz da aplicação de função em Azure é mostrado no separador **Publicar.** 
    
1.  No separador Publicar, escolha **Gerir no Cloud Explorer.** Isto abre a nova aplicação de função Azure recurso no Cloud Explorer. 
    
    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Mensagem de publicação bem-sucedida":::
    
    O Cloud Explorer permite-lhe utilizar o Visual Studio para visualizar os conteúdos do site, iniciar e parar a aplicação de funções, e navegar diretamente para funcionar os recursos da aplicação no Azure e no portal Azure. 
