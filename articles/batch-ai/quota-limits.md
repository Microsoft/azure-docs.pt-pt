---
title: Quotas e limites de serviço do Azure Batch AI | Documentos da Microsoft
description: Saiba mais sobre quotas do Azure Batch AI predefinidas, limites e restrições e aumenta a como pedir quota
services: batch-ai
documentationcenter: ''
author: johnwu10
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/08/2018
ms.author: danlep
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 1f9bd8503c2e8f2aa3c54d41b8585c6fbf81c92b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542089"
---
# <a name="batch-ai-service-quotas-and-limits"></a>Limites e quotas de serviço do batch AI

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Como com outros serviços do Azure, existem limites em certos recursos associados com o serviço Batch AI. No Batch AI, estes limites são quotas predefinidas aplicadas ao nível da subscrição para cada região onde o serviço esteja [disponível](https://azure.microsoft.com/global-infrastructure/services/). Este artigo aborda os padrões e como pode solicitar quota aumenta.

Tenha presentes estas quotas em mente à medida que, design e aumentar verticalmente os recursos do Batch AI. Por exemplo, se o número de destino de nós que especificou não chega ao seu cluster, em seguida, poderá ter atingido um limite de núcleos do Batch AI para a sua subscrição.

Se planeja executar cargas de trabalho de produção no Batch AI, terá de aumentar a um ou mais das quotas acima da predefinição.

> [!NOTE]
> Uma quota é um limite de crédito, não uma garantia de capacidade. Se tiver necessidades de capacidade em grande escala, contacte o suporte do Azure.
> 
> 

## <a name="resource-quotas"></a>Quotas de recursos

[!INCLUDE [azure-batch-ai-limits](../../includes/azure-batch-ai-limits.md)]

## <a name="other-limits"></a>Outros limites

Seguem-se limites strict, que não podem ser excedidas uma vez atingido.

| **Recurso** | **Limite máximo** |
| --- | --- |
| Áreas de trabalho máximas por grupo de recursos | 800 |
| Tamanho máximo de cluster | 100 nós |
| Máximo de GPU de MPI processa por nó | 1 a 4 |
| Operadores de GPU máximos por nó | 1 a 4 |
| Duração de tarefas máxima | 7 dias<sup>1</sup> |
| Servidores de parâmetro máxima por nó | 1 |

<sup>1</sup> a duração máxima refere-se até ao momento em que uma tarefa começa em execução e quando terminar. Tarefas concluídas mantêm-se indefinidamente; dados de tarefas não concluídas dentro da duração máxima do não estão acessíveis.

## <a name="view-batch-ai-quotas"></a>Ver quotas de Batch AI

Ver as suas quotas de subscrição atuais do Batch AI no [portal do Azure][portal].

1. No painel esquerdo, clique em **todos os serviços**. Em seguida, procure **Batch AI** e clique para abrir o serviço.
2. Clique em **utilização + quotas** no menu do Batch AI.
3. Selecione a sua subscrição para ver os limites de quota.

## <a name="increase-a-batch-ai-cores-quota"></a>Aumentar uma quota de núcleos do Batch AI

Siga estas etapas para pedir uma quota de aumentam para a sua subscrição do Batch AI com o [portal do Azure][portal]. 

1. No painel esquerdo, clique em **todos os serviços**. Em seguida, procure **Batch AI** e clique para abrir o serviço.
2. Clique em **novo pedido de suporte** no menu do Batch AI.
3. Na **Noções básicas**:
   
    a. **Tipo de problema** > **Quota**
   
    b. **Subscrição** > selecione a sua subscrição.
   
    c. **Tipo de quota** > **Batch AI**
   
    d. **Plano de suporte** > selecione o seu plano de suporte.

    Clique em **Seguinte**.
4. Na **problema**:
   
    a. Selecione um **gravidade** acordo com a sua [impacto comercial][support_sev].
   
    b. Na **detalhes da Quota**, especifique a localização, o tipo de quota e o tipo de recurso. Especifique o novo limite que pretende fazer o pedido. Clique em **guardar e continuar**.

    c. Opcional – carregar os ficheiros relevantes com mais informações sobre o motivo de aumento.
   
    Clique em **Seguinte**.
5. Na **informações de contacto**:
   
    a. Selecione um **método preferencial de contacto**.
   
    b. Verificar e introduza os detalhes de contactos necessários.
   
    Clique em **Criar** para submeter o pedido de suporte.

Depois de ter enviado o pedido de suporte, o suporte do Azure irá contactá-lo. Concluir o pedido pode demorar até 2 dias úteis.


## <a name="next-steps"></a>Passos Seguintes

Depois de se familiarizar com os limites de quota, consulte os artigos seguintes para começar a utilizar o Batch AI.

> [!div class="nextstepaction"]
> [Tutorial de início rápido de IA do batch](quickstart-tensorflow-training-cli.md)
> [receitas de Batch AI](https://github.com/Azure/BatchAI/tree/master/recipes)
> [Saiba mais sobre os recursos do Batch AI](resource-concepts.md)

[portal]: https://portal.azure.com
[support_sev]: https://aka.ms/supportseverity