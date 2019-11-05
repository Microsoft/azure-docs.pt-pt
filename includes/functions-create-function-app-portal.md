---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/17/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: cd118def8b9caf97f26aaa5ad5dc1f6fb015316d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469328"
---
1. No menu portal do Azure, selecione **criar um recurso**.

    ![Adicionar recurso usando o menu portal do Azure](./media/functions-create-function-app-portal/create-function-app-resource.png)

1. Na página **novo** , selecione **computação** > **aplicativo de funções**.

1. Utilize as definições da aplicação Function App, conforme especificado na tabela abaixo da imagem.

    ![Noções básicas](./media/functions-create-function-app-portal/function-app-create-basics.png)

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual esta nova aplicação de função é criada. |
    | **[Grupo de Recursos](../articles/azure-resource-manager/resource-group-overview.md)** |  *myResourceGroup* | Nome do grupo de recursos novo no qual a aplicação Function App vai ser criada. |
    | **Nome do Aplicativo de funções** | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Os caracteres válidos são `a-z` (não diferencia maiúsculas de minúsculas), `0-9`e `-`.  |
    |**Publicar**| Código | Opção para publicar arquivos de código ou um contêiner do Docker. |
    | **Pilha de tempo de execução** | Linguagem preferencial | Escolha um tempo de execução que suporte a sua linguagem de programação de funções favorita. Selecione **.NET** para funções C# e F#. |
    |**Região**| Região preferida | Escolha uma [região](https://azure.microsoft.com/regions/) perto de si ou de outros serviços aos quais as suas funções acedem. |

    Selecione o botão **Avançar: hospedagem >** .

1. Insira as configurações a seguir para hospedagem.

    ![Alojamento](./media/functions-create-function-app-portal/function-app-create-hosting.png)

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Conta de armazenamento](../articles/storage/common/storage-quickstart-create-account.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento para ser utilizada pela sua aplicação de funções. Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas. Você também pode usar uma conta existente, que deve atender aos [requisitos da conta de armazenamento](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    |**Sistema operativo**| Sistema operacional preferencial | Um sistema operacional é previamente selecionado para você com base em sua seleção de pilha de tempo de execução, mas você pode alterar a configuração, se necessário. |
    | **[Intenção](../articles/azure-functions/functions-scale.md)** | Plano de consumo | O plano de alojamento que define a forma como os recursos são alocados à sua aplicação Function App. No **Plano de Consumo** predefinido, os recursos são adicionados dinamicamente, conforme necessário por parte das suas funções. Neste alojamento [sem servidor](https://azure.microsoft.com/overview/serverless-computing/), só paga pelo tempo de execução das suas funções. Quando executa num plano do Serviço de Aplicações, tem de gerir o [dimensionamento da sua aplicação de funções](../articles/azure-functions/functions-scale.md).  |

    Selecione o botão **Avançar: monitoramento >** .

1. Insira as configurações a seguir para o monitoramento.

    ![Monitorização](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Predefinição | Cria um recurso Application Insights do mesmo *nome de aplicativo* na região com suporte mais próximo. Ao expandir essa configuração, você pode alterar o **nome do novo recurso** ou escolher um **local** diferente em uma [Geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde você deseja armazenar seus dados. |

    Selecione **examinar + criar** para revisar as seleções de configuração de aplicativo.

1. Selecione **Criar** para aprovisionar e implementar a aplicação de função.

1. Selecione o ícone de Notificação no canto superior direito do portal e procure a mensagem **Implementação concluída com êxito**.

    ![Notificação de implantação](./media/functions-create-function-app-portal/function-app-create-notification2.png)

1. Selecione **Ir para o recurso** para ver a sua nova aplicação de funções. Você também pode selecionar **fixar no painel**. A fixação torna mais fácil retornar a esse recurso do aplicativo de funções do seu painel.
