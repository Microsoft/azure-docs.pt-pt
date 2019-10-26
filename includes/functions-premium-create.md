---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 10/25/2019
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: 911db285d1ca885142e8a80345926ce76379e3bc
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72958727"
---
1. Abrir o portal do Azure de [https://portal.azure.com](https://portal.azure.com)

1. Selecione o botão **criar um recurso**

    ![Criar recurso](./media/functions-create-function-app-portal/function-app-create-resource.png)

1. Selecione > de **computação** **aplicativo de funções**.

    ![Criar uma aplicação de função no portal do Azure](./media/functions-premium-create/function-app-create-start.png)

1. Utilize as definições da aplicação Function App, conforme especificado na tabela abaixo da imagem.

    ![Noções básicas](./media/functions-premium-create/function-app-create-basics.png)

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual esta nova aplicação de função é criada. |
    | **[Grupo de Recursos](../articles/azure-resource-manager/resource-group-overview.md)** |  *myResourceGroup* | Nome do grupo de recursos novo no qual a aplicação Function App vai ser criada. |
    | **Nome do Aplicativo de funções** | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Os caracteres válidos são `a-z` (não diferencia maiúsculas de minúsculas), `0-9`e `-`.  |
    |**Publicar**| Código | Opção para publicar arquivos de código ou um contêiner do Docker. |
    | **Pilha de tempo de execução** | Linguagem preferencial | Escolha um tempo de execução que suporte a sua linguagem de programação de funções favorita. Selecione **.NET** para funções C# e F#. |
    |**Região**| Região preferida | Escolha uma [região](https://azure.microsoft.com/regions/) perto de si ou de outros serviços aos quais as suas funções acedem. |

    Selecione o botão **Avançar: hospedagem >** .

1. Insira as seguintes configurações de hospedagem.

    ![Alojamento](./media/functions-premium-create/function-app-premium-create-hosting.png)

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Conta de armazenamento](../articles/storage/common/storage-quickstart-create-account.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento para ser utilizada pela sua aplicação de funções. Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas. Você também pode usar uma conta existente, que deve atender aos [requisitos da conta de armazenamento](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    |**Sistema operativo**| Sistema operacional preferencial | Um sistema operacional é previamente selecionado para você com base em sua seleção de pilha de tempo de execução, mas você pode alterar a configuração, se necessário. |
    | **[Intenção](../articles/azure-functions/functions-scale.md)** | Premium | Para tipo de plano, selecione **Premium (visualização)** e selecione padrões para as seleções de plano e *SKU e tamanho* do *Windows* . |

    Selecione o botão **Avançar: monitoramento >** .

1. Insira as configurações de monitoramento a seguir.

    ![Monitorização](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Predefinição | Cria um recurso Application Insights do mesmo *nome de aplicativo* na região com suporte mais próximo. Ao expandir essa configuração, você pode alterar o **nome do novo recurso** ou escolher um **local** diferente em uma [Geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde você deseja armazenar seus dados. |

    Selecione **examinar + criar** para revisar as seleções de configuração de aplicativo.

1. Selecione **Criar** para aprovisionar e implementar a aplicação de função.