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
ms.openlocfilehash: fca8c3b0b1bd4d22720a6d15313e297d05b7fac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76021063"
---
1. Abra o portal Azure a partir[https://portal.azure.com](https://portal.azure.com)

1. Selecione o botão **Criar um recurso**

    ![Criar um recurso](./media/functions-create-function-app-portal/function-app-create-resource.png)

1. Selecione**App de Funções** **Compute** > .

    ![Criar uma aplicação de função no portal do Azure](./media/functions-premium-create/function-app-create-start.png)

1. Utilize as definições da aplicação Function App, conforme especificado na tabela abaixo da imagem.

    ![Noções básicas](./media/functions-premium-create/function-app-create-basics.png)

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **Assinatura** | A sua subscrição | A subscrição sob a qual esta nova aplicação de função é criada. |
    | **[Grupo de Recursos](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Nome do grupo de recursos novo no qual a aplicação Function App vai ser criada. |
    | **Nome da app de função** | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Os caracteres `a-z` válidos são `0-9`(caso insensível), e `-`.  |
    |**Publicar**| Código | Opção para publicar ficheiros de código ou um contentor de Docker. |
    | **Pilha de tempo de execução** | Linguagem preferencial | Escolha um tempo de execução que suporte a sua linguagem de programação de funções favorita. Selecione **.NET** para funções C# e F#. |
    |**Região**| Região preferida | Escolha uma [região](https://azure.microsoft.com/regions/) perto de si ou de outros serviços aos quais as suas funções acedem. |

    Selecione o **botão Seguinte : Hospedagem >.**

1. Introduza as seguintes definições de hospedagem.

    ![Hosting](./media/functions-premium-create/function-app-premium-create-hosting.png)

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Conta de armazenamento](../articles/storage/common/storage-account-create.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento para ser utilizada pela sua aplicação de funções. Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas. Também pode utilizar uma conta existente, que deve satisfazer os requisitos da conta de [armazenamento.](../articles/azure-functions/functions-scale.md#storage-account-requirements) |
    |**Sistema Operativo**| Sistema operativo preferido | Um sistema operativo é pré-selecionado para si com base na sua seleção de pilhas de tempo de funcionamento, mas pode alterar a definição se necessário. |
    | **[Plano](../articles/azure-functions/functions-scale.md)** | Premium | Para o Tipo de Plano, selecione **Premium (Pré-visualização)** e selecione predefinições para o *Plano Windows* e *Seleções de Tamanhos.* |

    Selecione o botão **Seguinte : monitorização >.**

1. Introduza as seguintes definições de monitorização.

    ![Monitorização](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Predefinição | Cria um recurso Application Insights com o mesmo *nome app* na região mais próxima suportada. Ao expandir este cenário, pode alterar o **nome de novo recurso** ou escolher um **Local** diferente numa [geografia Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde pretende armazenar os seus dados. |

    Selecione **Review + Criar** para rever as seleções de configuração da aplicação.

1. Selecione **Criar** para aprovisionar e implementar a aplicação de função.