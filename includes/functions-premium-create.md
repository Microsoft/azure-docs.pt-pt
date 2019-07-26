---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 04/01/2019
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: 9bac92bc1cc94b88dc694b468b795049db4ac9df
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443962"
---
1. Aceda ao [Portal do Azure](https://portal.azure.com).

1. Selecione **+ criar um recurso** no lado esquerdo e, em seguida, escolha **aplicativo de funções**.

1. Para o **plano de hospedagem**, escolha plano do serviço de **aplicativo**e, em seguida, selecione **plano/local do serviço de aplicativo**.

    ![Criar uma aplicação de função](./media/functions-premium-create/create-function-app-resource.png)

1. Selecione **criar novo**, digite um nome de **plano do serviço de aplicativo** , escolha um **local** em uma [região](https://azure.microsoft.com/regions/) perto de você ou perto de outros serviços que suas funções acessam e selecione **tipo de preço**.

    ![Criar plano do App Service](./media/functions-premium-create/new-app-service-plan.png)

1. Escolha o plano **ep1** (Premium elástico) e, em seguida, selecione **aplicar**.

    ![Selecionar plano Premium](./media/functions-premium-create/hosting-plan.png) 

1. Selecione **OK** para criar o plano e, em seguida, use as configurações do aplicativo de funções restantes, conforme especificado na tabela abaixo da imagem. 

    ![Plano do serviço de aplicativo concluído](./media/functions-premium-create/create-function-app.png)  

    | Definição      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome da aplicação** | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Os carateres válidos são `a-z`, `0-9` e `-`.  | 
    | **Subscrição** | A sua subscrição | A subscrição sob a qual esta nova aplicação de função é criada. |
    | **[Grupo de Recursos](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Nome do grupo de recursos novo no qual a aplicação Function App vai ser criada. Você também pode usar o valor sugerido. |
    | **OS** | Sistema operacional preferencial | O Linux e o Windows têm suporte no plano Premium. |
    | **Pilha de tempo de execução** | Linguagem preferencial | Escolha um tempo de execução que suporte a sua linguagem de programação de funções favorita. Selecione **.NET** para funções C# e F#. Somente os idiomas com suporte no **sistema operacional** escolhido são exibidos. |
    | **[Armazenamento](../articles/storage/common/storage-quickstart-create-account.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento para ser utilizada pela sua aplicação de funções. Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas. Também pode utilizar uma conta existente, a qual tem de cumprir os [requisitos de contas de armazenamento](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Predefinição | Cria um recurso Application Insights do mesmo *nome de aplicativo* na região com suporte mais próximo. Ao expandir essa configuração, você pode alterar o **nome do novo recurso** ou escolher um **local** diferente em uma [Geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde você deseja armazenar seus dados. |

1. Depois que as configurações forem validadas, selecione **criar**.

1. Selecione o ícone de Notificação no canto superior direito do portal e procure a mensagem **Implementação concluída com êxito**.

    ![Definir novas definições da aplicação de funções](./media/functions-premium-create/function-app-create-notification.png)

1. Selecione **Ir para o recurso** para ver a sua nova aplicação de funções. Você também pode selecionar **fixar no painel**. A fixação torna mais fácil retornar a esse recurso do aplicativo de funções do seu painel.