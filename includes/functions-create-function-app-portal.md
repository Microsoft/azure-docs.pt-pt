---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/05/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 2bfca370e8e89d98f700c8c5adfd29129e810d9b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66131574"
---
1. Selecione o **criar um recurso** botão encontrado no canto superior esquerdo do portal do Azure, em seguida, selecione **computação** > **aplicação de funções**.

    ![Criar uma aplicação de função no portal do Azure](./media/functions-create-function-app-portal/function-app-create-flow.png)

2. Utilize as definições da aplicação Function App, conforme especificado na tabela abaixo da imagem.

    ![Definir novas definições da aplicação de funções](./media/functions-create-function-app-portal/function-app-create-flow2.png)

    | Definição      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome da aplicação** | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Os carateres válidos são `a-z`, `0-9` e `-`.  | 
    | **Subscrição** | A sua subscrição | A subscrição sob a qual esta nova aplicação de função é criada. | 
    | **[Grupo de Recursos](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Nome do grupo de recursos novo no qual a aplicação Function App vai ser criada. |
    | **OS** | Windows | O alojamento sem servidor no Linux está atualmente em pré-visualização. Para obter mais informações, consulte [neste artigo de considerações](https://aka.ms/funclinux).|
    | **[Plano de alojamento](../articles/azure-functions/functions-scale.md)** | Plano de consumo | O plano de alojamento que define a forma como os recursos são alocados à sua aplicação Function App. No **Plano de Consumo** predefinido, os recursos são adicionados dinamicamente, conforme necessário por parte das suas funções. Neste alojamento [sem servidor](https://azure.microsoft.com/overview/serverless-computing/), só paga pelo tempo de execução das suas funções. Quando executa num plano do Serviço de Aplicações, tem de gerir o [dimensionamento da sua aplicação de funções](../articles/azure-functions/functions-scale.md).  |
    | **Location** | Europa Ocidental | Escolha uma [região](https://azure.microsoft.com/regions/) perto de si ou de outros serviços aos quais as suas funções acedem. |
    | **Pilha de tempo de execução** | Linguagem preferencial | Escolha um tempo de execução que suporte a sua linguagem de programação de funções favorita. Selecione **.NET** para funções C# e F#. |
    | **[Armazenamento](../articles/storage/common/storage-quickstart-create-account.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento para ser utilizada pela sua aplicação de funções. Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas. Também pode utilizar uma conta existente, a qual tem de cumprir os [requisitos de contas de armazenamento](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Predefinição | Cria um recurso do Application Insights do mesmo *nome da aplicação* na região suportada mais próxima. Ao expandir esta definição, pode alterar o **novo nome do recurso** ou escolha outro **localização** num [geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde pretende armazenar os seus dados. |

3. Selecione **Criar** para aprovisionar e implementar a aplicação de função.

4. Selecione o ícone de Notificação no canto superior direito do portal e procure a mensagem **Implementação concluída com êxito**.

    ![Definir novas definições da aplicação de funções](./media/functions-create-function-app-portal/function-app-create-notification.png)

5. Selecione **Ir para o recurso** para ver a sua nova aplicação de funções. Também pode selecionar **afixar ao dashboard**. A afixação torna mais fácil retornar a este recurso de aplicação de função a partir do seu dashboard.
