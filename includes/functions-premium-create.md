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
ms.openlocfilehash: 4eaea46044f0da4766fda051655d57caee9d98b6
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937024"
---
1. A partir do menu do portal do Azure ou a partir da **Home page**, selecione **Criar um recurso**.

1. Na **nova** página, selecione App de Função **computacional**  >  .

1. Na página **Basics,** utilize as definições da aplicação de função conforme especificado no quadro seguinte:

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **Subscrição** | A sua subscrição | A subscrição no âmbito da qual esta nova aplicação de funções é criada. |
    | **[Grupo de Recursos](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Nome do grupo de recursos novo no qual a aplicação Function App vai ser criada. |
    | **Nome da Aplicação de Funções** | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Os carateres válidos são `a-z` (não sensível a maiúsculas e minúsculas), `0-9` e `-`.  |
    |**Publicar**| Código | Opção para publicar ficheiros de código ou um contentor de Docker. |
    | **Pilha de runtime** | Linguagem preferencial | Escolha um tempo de execução que suporte a sua linguagem de programação de funções favorita. Selecione **.NET** para funções C# e F#. |
    |**Região**| Região preferida | Escolha uma [região](https://azure.microsoft.com/regions/) perto de si ou perto de outros serviços a que as suas funções acedam. |

    ![Página básica](./media/functions-premium-create/function-app-create-basics.png)

1. Selecione **Seguinte: Hospedagem**. Na página **'Hospedagem',** insira as seguintes definições:

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Conta de armazenamento](../articles/storage/common/storage-account-create.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento para ser utilizada pela sua aplicação de funções. Os nomes das contas de armazenamento têm de ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas. Também pode utilizar uma conta existente, que deve satisfazer os requisitos da [conta de armazenamento.](../articles/azure-functions/storage-considerations.md#storage-account-requirements) |
    |**Sistema operativo**| Sistema operativo preferido | Um sistema operativo é pré-selecionado para si com base na seleção da sua pilha de tempo de execução, mas pode alterar a definição se necessário. Python só é apoiado em Linux. |
    | **[Planear](../articles/azure-functions/functions-scale.md)** | Premium | O plano de alojamento que define a forma como os recursos são alocados à sua aplicação Function App. Selecione **Premium**. Por padrão, é criado um novo plano de Serviço de Aplicações. O **Sku padrão e o tamanho** é **EP1,** onde EP significa _prémio elástico_. Para saber mais, consulte a [lista de SKUs Premium.](../articles/azure-functions/functions-premium-plan.md#available-instance-skus)<br/>Ao executar funções JavaScript num plano Premium, deve escolher um caso que tenha menos vCPUs. Para obter mais informações, consulte [Escolha os planos Premium de núcleo único.](../articles/azure-functions/functions-reference-node.md#considerations-for-javascript-functions)  |

    ![Página de hospedagem](./media/functions-premium-create/function-app-premium-create-hosting.png)

1. Selecione **Seguinte: Monitorização**. Na página **de Monitorização,** insira as seguintes definições:

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Predefinição | Cria um recurso Application Insights com o mesmo *nome app* na região suportada mais próxima. Ao expandir esta definição, pode alterar o nome de **novo recurso** ou escolher uma **Localização** diferente numa [geografia Azure](https://azure.microsoft.com/global-infrastructure/geographies/) para armazenar os seus dados. |

    ![Página de monitorização](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

1. Selecione **Review + crie** para rever as seleções de configuração da aplicação.

1. Na página **'Rever + criar',** rever as definições e, em seguida, selecionar **Criar** para provisão e implementar a aplicação de função.

1. Selecione o ícone **notificações** no canto superior direito do portal e observe a mensagem **de implementação conseguida.**

1. Selecione **Ir para o recurso** para ver a sua nova aplicação de funções. Também pode selecionar **Pin para painel de instrumentos.** A fixação facilita o regresso a esta função de recurso de aplicação a partir do seu dashboard.

    ![Notificação de implantação](./media/functions-premium-create/function-app-create-notification2.png)
