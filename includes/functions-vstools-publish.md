---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 8a4fbb3803f2eed4f961792a29a6bf8a327ea208
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80657581"
---
1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Publicar**.

2. Na **Escolha de um alvo de publicação,** utilize as opções de publicação especificadas no seguinte quadro: 

    | Opção      | Descrição                                |
    | ------------ |  -------------------------------------------------- |
    | **App de função Azure** | Crie uma aplicação de função num ambiente de nuvem Azure. | 
    | **Criar novos** | Uma nova aplicação de funções, com recursos relacionados, é criada no Azure. <br/>Se escolher **O Select Existing**, todos os ficheiros da aplicação de função existente no Azure são substituídos por ficheiros do projeto local. Utilize esta opção apenas quando republicar as atualizações para uma aplicação de função existente. |
    | **Executar a partir de arquivo de pacote** | A sua aplicação de funções é implementada utilizando [o Zip Deploy](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) com o modo [Run-From-Package](../articles/azure-functions/run-functions-from-deployment-package.md) ativado. Esta implementação, que resulta num melhor desempenho, é a forma recomendada de executar as suas funções. <br/>Se não utilizar esta opção, certifique-se de impedir que o seu projeto de aplicação de funções funcione localmente antes de publicar no Azure. |

    ![Escolher um destino de publicação](./media/functions-vstools-publish/functions-visual-studio-publish-profile.png)


3. Selecione **Publicar**. Se ainda não se inscreveu na sua conta Azure do Visual Studio, selecione **Sign-in**. Também pode criar uma conta Azure gratuita.

4. No Serviço de **Aplicações Azure: Criar novos**, utilize os valores especificados no seguinte quadro:

    | Definição      | Valor  | Descrição                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Nome globalmente exclusivo | Nome que identifica exclusivamente a sua nova aplicação de funções. Aceite este nome ou insira um novo nome. Os caracteres `a-z`válidos são: , `0-9`e `-`. |
    | **Subscrição** | A sua subscrição | A subscrição do Azure que deve utilizar. Aceite esta subscrição ou selecione uma nova da lista de lançamentos. |
    | **[Grupo de Recursos](../articles/azure-resource-manager/management/overview.md)** | Nome do seu grupo de recursos |  O grupo de recursos para criar a sua aplicação de função. Selecione um grupo de recursos existente a partir da lista de drop-down ou escolha **new** para criar um novo grupo de recursos.|
    | **[Plano de Hospedagem](../articles/azure-functions/functions-scale.md)** | Nome do seu plano de hospedagem | Selecione **Novo** para configurar um plano sem servidores. Certifique-se de escolher o **Consumo** em **tamanho**. Quando publica o seu projeto numa aplicação de função que funciona num plano de [consumo,](../articles/azure-functions/functions-scale.md#consumption-plan)paga apenas pelas execuções da sua aplicação de funções. Outros planos de hospedagem incorrem em custos mais elevados. Se executar um plano diferente do **Consumo,** deve gerir a [escala da sua aplicação de funções](../articles/azure-functions/functions-scale.md). Escolha um **Local** numa [região](https://azure.microsoft.com/regions/) próxima ou de outros serviços a que as suas funções acedam.  |
    | **[Armazenamento Azure](../articles/storage/common/storage-account-create.md)** | Conta de armazenamento para fins gerais | Uma conta de Armazenamento Azure é exigida pelo tempo de funcionamento das funções. Selecione **Novo** para configurar uma conta de armazenamento de uso geral. Também pode escolher uma conta existente que satisfaça os requisitos da conta de [armazenamento.](../articles/azure-functions/functions-scale.md#storage-account-requirements)  |

    ![Caixa de diálogo Criar App Service](./media/functions-vstools-publish/functions-visual-studio-publish.png)

5. Selecione **Criar** para criar uma aplicação de função e os seus recursos relacionados em Azure com estas definições e implementar o código do projeto de função. 

6. Selecione Publicar e depois de concluída a implementação, tome nota do valor URL do **Site,** que é o endereço da sua aplicação de função no Azure.

    ![Mensagem de publicação bem-sucedida](./media/functions-vstools-publish/functions-visual-studio-publish-complete.png)
