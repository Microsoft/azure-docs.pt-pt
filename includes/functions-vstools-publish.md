---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: db6f5927e1cbdb3527014ccbb277c7ec20b02600
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84231607"
---
1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Publicar**.

1. Na **Escolha um alvo de publicação,** utilize as opções de publicação especificadas no quadro seguinte: 

    | Opção      | Descrição                                |
    | ------------ |  -------------------------------------------------- |
    | **Plano de Consumo de Funções Azure** | Crie uma aplicação de função num ambiente em nuvem Azure que funciona num [plano de Consumo.](../articles/azure-functions/functions-scale.md#consumption-plan) Quando utiliza um plano de consumo, paga-se apenas pelas execuções da aplicação de funções. Outros planos de hospedagem incorrem em custos mais elevados. Se correr num plano que não seja um plano de consumo, tem de gerir o [escalonamento da sua aplicação de função.](../articles/azure-functions/functions-scale.md)| 
    | **Criar Novo** | Uma nova aplicação de função, com recursos relacionados, é criada no Azure. <br/>Se escolher **Select Existing**, todos os ficheiros da aplicação de função existente em Azure são substituídos por ficheiros do projeto local. Utilize esta opção apenas quando republicar as atualizações para uma aplicação de função existente. |
    | **Executar a partir de arquivo de pacote** | A sua aplicação de função é implementada utilizando [o zip deploy](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) com o modo [Run-From-Package](../articles/azure-functions/run-functions-from-deployment-package.md) ativado. Esta implementação, que resulta num melhor desempenho, é a forma recomendada de executar as suas funções. <br/>Se não utilizar esta opção, certifique-se de impedir que o seu projeto de aplicação de função funcione localmente antes de publicar para o Azure. |

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile.png" alt-text="Criar um perfil de publicação":::


1. Selecione **Criar Perfil**. Se ainda não se inscreveu na sua conta Azure do Visual Studio, selecione **'Iniciar s-in'.** Também pode criar uma conta Azure gratuita.

1. No **Serviço de Aplicações: Criar novo,** utilizar os valores especificados no quadro seguinte:

    | Definição      | Valor  | Descrição                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Nome globalmente exclusivo | Nome que identifica exclusivamente a sua nova aplicação de funções. Aceite este nome ou insira um novo nome. Os caracteres válidos são: `a-z` `0-9` , e `-` . |
    | **Subscrição** | A sua subscrição | A subscrição do Azure que deve utilizar. Aceite esta subscrição ou selecione uma nova da lista de drop-down. |
    | **[Grupo de recursos](../articles/azure-resource-manager/management/overview.md)** | Nome do seu grupo de recursos |  O grupo de recursos para criar a sua aplicação de função. Selecione um grupo de recursos existente da lista de drop-down ou escolha **Novo** para criar um novo grupo de recursos.|
    | **[Plano de Alojamento](../articles/azure-functions/functions-scale.md)** | Nome do seu plano de hospedagem | Selecione **Novo** para configurar um plano sem servidor. Certifique-se de escolher o **Consumo** em **Tamanho**. Quando publica o seu projeto numa aplicação de função que funciona num [plano de Consumo,](../articles/azure-functions/functions-scale.md#consumption-plan)paga apenas para execuções da sua aplicação de funções. Outros planos de hospedagem incorrem em custos mais elevados. Se correr num plano diferente do **Consumo,** tem de gerir o [escalonamento da sua aplicação de função.](../articles/azure-functions/functions-scale.md)  |
    | **Localização** | Localização do serviço de aplicações | Escolha uma **Localização** numa [região](https://azure.microsoft.com/regions/) próxima ou noutros serviços a que as suas funções acedam. |
    | **[Storage do Azure](../articles/storage/common/storage-account-create.md)** | Conta de armazenamento para fins gerais | Uma conta de Armazenamento Azure é exigida pelo tempo de funcionamento das Funções. Selecione **Novo** para configurar uma conta de armazenamento para fins gerais. Também pode escolher uma conta existente que satisfaça os requisitos da [conta de armazenamento.](../articles/azure-functions/functions-scale.md#storage-account-requirements)  |

    ![Caixa de diálogo Criar App Service](./media/functions-vstools-publish/functions-visual-studio-publish.png)

1. Selecione **Criar** para criar uma aplicação de função e os seus recursos relacionados em Azure com estas definições e implementar o seu código de projeto de função. 

1. **Selecione Publicar** e aguarde que a implementação esteja concluída. 

    Após a implementação completar o URL raiz da aplicação de função em Azure é mostrado no separador **Publicar.** 
    
1.  No separador Publicar, escolha **Gerir no Cloud Explorer.** Isto abre a nova aplicação de função Azure recurso no Cloud Explorer. 
    
    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Criar um perfil de publicação":::
    
    O Cloud Explorer permite-lhe utilizar o Visual Studio para visualizar os conteúdos do site, iniciar e parar a aplicação de funções, e navegar diretamente para funcionar os recursos da aplicação no Azure e no portal Azure. 