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
ms.openlocfilehash: 0f3303e7bc87ca0bd29f367405372568ed6da7a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66131564"
---
1. Aceda ao [Portal do Azure](https://portal.azure.com).

1. Selecione **+ criar um recurso** no lado esquerdo, em seguida, escolha **aplicação de funções**.

1. Para **plano de alojamento**, escolha **plano do App Service**, em seguida, selecione **plano do serviço de aplicações/localização**.

    ![Criar uma aplicação de função](./media/functions-premium-create/create-function-app-resource.png)

1. Selecione **criar novo**, tipo um **plano do App Service** nome, escolha um **localização** num [região](https://azure.microsoft.com/regions/) as suas funções dos serviços de perto de si ou perto de si aceder e, em seguida, selecione **escalão de preço**.

    ![Criar plano do App Service](./media/functions-premium-create/new-app-service-plan.png)

1. Escolha o **EP1** (elástico Premium) planear, em seguida, selecione **aplicar**.

    ![Selecione o plano premium](./media/functions-premium-create/hosting-plan.png) 

1. Selecione **OK** para criar o plano, em seguida, utilize as restantes definições de aplicação de função como especificado na tabela abaixo da imagem. 

    ![Plano do serviço de aplicação concluída](./media/functions-premium-create/create-function-app.png)  

    | Definição      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome da aplicação** | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Os carateres válidos são `a-z`, `0-9` e `-`.  | 
    | **Subscrição** | A sua subscrição | A subscrição sob a qual esta nova aplicação de função é criada. |
    | **[Grupo de Recursos](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Nome do grupo de recursos novo no qual a aplicação Function App vai ser criada. Também pode utilizar o valor sugerido. |
    | **OS** | Windows | Linux não é atualmente suportado no plano Premium. |
    | **Pilha de tempo de execução** | Linguagem preferencial | Escolha um tempo de execução que suporte a sua linguagem de programação de funções favorita. Selecione **.NET** para funções C# e F#. Apenas os idiomas suportados no seu escolhida **SO** são apresentados. |
    | **[Armazenamento](../articles/storage/common/storage-quickstart-create-account.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento para ser utilizada pela sua aplicação de funções. Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas. Também pode utilizar uma conta existente, a qual tem de cumprir os [requisitos de contas de armazenamento](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Predefinição | Cria um recurso do Application Insights do mesmo *nome da aplicação* na região suportada mais próxima. Ao expandir esta definição, pode alterar o **novo nome do recurso** ou escolha outro **localização** num [geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde pretende armazenar os seus dados. |

1. Depois das definições são validadas, selecione **criar**.

1. Selecione o ícone de Notificação no canto superior direito do portal e procure a mensagem **Implementação concluída com êxito**.

    ![Definir novas definições da aplicação de funções](./media/functions-premium-create/function-app-create-notification.png)

1. Selecione **Ir para o recurso** para ver a sua nova aplicação de funções. Também pode selecionar **afixar ao dashboard**. A afixação torna mais fácil retornar a este recurso de aplicação de função a partir do seu dashboard.