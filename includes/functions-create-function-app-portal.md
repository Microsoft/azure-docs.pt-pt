---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/04/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c590b61ee1424d32d83dc5f758682fde37492c3a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80057201"
---
1. A partir do menu do portal do Azure ou a partir da **Home page**, selecione **Criar um recurso**.

1. Na **nova** página, selecione **Compute** > **Function App**.

1. Na página **Basics,** utilize as definições da aplicação de função conforme especificado na tabela seguinte.

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual esta nova aplicação de função é criada. |
    | **[Grupo de Recursos](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Nome do grupo de recursos novo no qual a aplicação Function App vai ser criada. |
    | **Nome da app de função** | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Os caracteres `a-z` válidos são `0-9`(caso insensível), e `-`.  |
    |**Publicar**| Código | Opção para publicar ficheiros de código ou um contentor de Docker. |
    | **Pilha de tempo de execução** | Linguagem preferencial | Escolha um tempo de execução que suporte a sua linguagem de programação de funções favorita. Escolha **o Núcleo .NET** para funções C# e F#. |
    |**Versão**| Número da versão | Escolha a versão do seu tempo de execução instalado.  |
    |**Região**| Região preferida | Escolha uma [região](https://azure.microsoft.com/regions/) perto de si ou de outros serviços aos quais as suas funções acedem. |

    ![Noções básicas](./media/functions-create-function-app-portal/function-app-create-basics.png)

1. Selecione **Seguinte : Hospedagem**. Na página **de Hospedagem,** introduza as seguintes definições.

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Conta de armazenamento](../articles/storage/common/storage-account-create.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento para ser utilizada pela sua aplicação de funções. Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres de comprimento e podem conter números e letras minúsculas apenas. Também pode utilizar uma conta existente, que deve satisfazer os requisitos da conta de [armazenamento.](../articles/azure-functions/functions-scale.md#storage-account-requirements) |
    |**Sistema Operativo**| Sistema operativo preferido | Um sistema operativo é pré-selecionado para si com base na sua seleção de pilhas de tempo de funcionamento, mas pode alterar a definição se necessário. |
    | **[Planear](../articles/azure-functions/functions-scale.md)** | **Consumo (Servidor)** | O plano de alojamento que define a forma como os recursos são alocados à sua aplicação Function App. No plano de **consumo** padrão, os recursos são adicionados dinamicamente conforme exigido pelas suas funções. Neste alojamento [sem servidor,](https://azure.microsoft.com/overview/serverless-computing/) paga-se apenas pelo tempo em que as suas funções funcionam. Quando executa num plano do Serviço de Aplicações, tem de gerir o [dimensionamento da sua aplicação de funções](../articles/azure-functions/functions-scale.md).  |

    ![Alojamento](./media/functions-create-function-app-portal/function-app-create-hosting.png)

1. Selecione **Seguinte : Monitorização**. Na página **de Monitorização,** introduza as seguintes definições.

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Predefinição | Cria um recurso Application Insights com o mesmo *nome app* na região mais próxima suportada. Ao expandir este cenário, pode alterar o **nome de novo recurso** ou escolher um **Local** diferente numa [geografia Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde pretende armazenar os seus dados. |

    ![Monitorização](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

1. Selecione **Review + crie** para rever as seleções de configuração da aplicação.

1. Na página **Review + criar,** rever as suas definições e, em seguida, selecionar **Criar** para fornecer e implementar a aplicação de funções.

1. Selecione o ícone de Notificação no canto superior direito do portal e procure a mensagem **Implementação concluída com êxito**.

1. Selecione **Ir para o recurso** para ver a sua nova aplicação de funções. Também pode selecionar **Pin para painel de instrumentos**. A fixação facilita o regresso a esta função de recurso da aplicação a partir do seu painel de instrumentos.

    ![Notificação de implantação](./media/functions-create-function-app-portal/function-app-create-notification2.png)