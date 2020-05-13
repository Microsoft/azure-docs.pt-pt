---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2020
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: c53486bf3368039f172c7a13420e2291dd9c9892
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122731"
---
1. A partir do menu do portal do Azure ou a partir da **Home page**, selecione **Criar um recurso**.

1. Na **nova** página, **selecione Compute**  >  **Function App**.

1. Na página **Basics,** utilize as definições da aplicação de função conforme especificado no quadro seguinte:

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual esta nova aplicação de função é criada. |
    | **[Grupo de Recursos](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Nome do grupo de recursos novo no qual a aplicação Function App vai ser criada. |
    | **Nome da app de função** | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Os caracteres válidos são `a-z` (caso insensível), `0-9` e `-` .  |
    |**Publicar**| Código | Opção para publicar ficheiros de código ou um contentor de Docker. |
    | **Pilha de tempo de execução** | Linguagem preferencial | Escolha um tempo de execução que suporte a sua linguagem de programação de funções favorita. Selecione **.NET** para funções C# e F#. |
    |**Região**| Região preferida | Escolha uma [região](https://azure.microsoft.com/regions/) perto de si ou de outros serviços aos quais as suas funções acedem. |

    ![Página básica](./media/functions-premium-create/function-app-create-basics.png)

1. Selecione **Seguinte: Hospedagem**. Na página **de Hospedagem,** introduza as seguintes definições:

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Conta de armazenamento](../articles/storage/common/storage-account-create.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento para ser utilizada pela sua aplicação de funções. Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas. Também pode utilizar uma conta existente, que deve satisfazer os requisitos da conta de [armazenamento.](../articles/azure-functions/functions-scale.md#storage-account-requirements) |
    |**Sistema operativo**| Sistema operativo preferido | Um sistema operativo é pré-selecionado para si com base na sua seleção de pilhas de tempo de funcionamento, mas pode alterar a definição se necessário. |
    | **[Planear](../articles/azure-functions/functions-scale.md)** | Premium | O plano de alojamento que define a forma como os recursos são alocados à sua aplicação Function App. Selecione **Premium**, e, em seguida, selecione as predefinições para **Windows Plan** e **Sku e tamanho**. |

    ![Página de hospedagem](./media/functions-premium-create/function-app-premium-create-hosting.png)

1. Selecione **Seguinte: Monitorização**. Na página **de Monitorização,** introduza as seguintes definições:

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Predefinição | Cria um recurso Application Insights com o mesmo *nome app* na região mais próxima suportada. Ao expandir este cenário, pode alterar o **nome de novo recurso** ou escolher um **Local** diferente numa [geografia Azure](https://azure.microsoft.com/global-infrastructure/geographies/) para armazenar os seus dados. |

    ![Página de monitorização](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

1. Selecione **Review + crie** para rever as seleções de configuração da aplicação.

1. Na página **Review + criar,** rever as suas definições e, em seguida, selecionar **Criar** para fornecer e implementar a aplicação de funções.

1. Selecione o ícone **notificações** no canto superior direito do portal e observe a mensagem bem sucedida da **Implementação.**

1. Selecione **Ir para o recurso** para ver a sua nova aplicação de funções. Também pode selecionar **Pin para painel de instrumentos**. A fixação facilita o regresso a esta função de recurso da aplicação a partir do seu painel de instrumentos.

    ![Notificação de implantação](./media/functions-premium-create/function-app-create-notification2.png)