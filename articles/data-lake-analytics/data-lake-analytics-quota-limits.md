---
title: Ajuste de quotas e limites no Azure Data Lake Analytics
description: Saiba como ajustar e aumentar as quotas e limites em contas do Azure Data Lake Analytics (ADLA).
services: data-lake-analytics
ms.service: data-lake-analytics
author: omidm1
ms.author: omidm
ms.reviewer: jasonwhowell
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: d3601fd8c32c70cf828cd08fada71258ec8fa5d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60812714"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>Ajuste de quotas e limites no Azure Data Lake Analytics

Saiba como ajustar e aumentar a quota e limites de contas do Azure Data Lake Analytics (ADLA). Conhecer estes limites pode ajudá-lo a compreender o comportamento da sua tarefa U-SQL. Todos os limites de quota são flexíveis, pelo que pode aumentar os limites máximos contactando o suporte do Azure.

## <a name="azure-subscriptions-limits"></a>Limites de subscrições do Azure

**Número máximo de contas do ADLA por subscrição por região:**  5

Se tentar criar uma conta ADLA sexta, obterá um erro "Que atingiu o número máximo de contas de Data Lake Analytics permitido (5) na região em nome da subscrição". 

Se pretender que ultrapassem este limite, pode experimentar estas opções:
* Escolha outra região, se adequado
* Contacte o suporte do Azure por [abrir um pedido de suporte](#increase-maximum-quota-limits) para pedir um aumento de quota.

## <a name="default-adla-account-limits"></a>Limites de conta do ADLA predefinidos

**Número máximo de unidades de análise (UA) por conta:** 32

Este é o número máximo de UAS que podem ser executadas em simultâneo na sua conta. Se o número total de execução de AUs em todas as tarefas exceder este limite, as tarefas mais recentes são colocados em fila automaticamente. Por exemplo:

* Se tiver apenas uma tarefa em execução com 32 UAS, ao submeter um segundo trabalho que irá aguardar na fila de tarefas até que a primeira tarefa é concluída.
* Se já tiver quatro tarefas em execução e cada um está a utilizar 8 UAS, ao submeter uma tarefa Quinta, que tem 8 UAS que ele espera na fila de tarefas até que haja 8 UAS disponíveis.

**Número máximo de unidades de análise (UA) por tarefa:** 32

Este é o número máximo de padrão de UAS que pode ser atribuída a cada tarefa individual na sua conta. Tarefas que foram atribuídas mais do que este limite serão rejeitadas, a menos que o emissor é afetado por uma política de computação (limite de envio de trabalhos) que lhes dá mais UAS por tarefa. O limite superior deste valor é o limite de AU da conta.

**Número máximo de tarefas de U-SQL em simultâneo por conta:** 20

Este é o número máximo de tarefas que podem ser executadas em simultâneo na sua conta. Acima este valor, as tarefas mais recentes são colocados em fila automaticamente.

## <a name="adjust-adla-account-limits"></a>Ajustar os limites de conta ADLA

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Escolha uma conta existente do ADLA.
3. Clique em **Propriedades**.
4. Ajustar os valores para **AUs máximos**, **número máximo de tarefas em execução**, e **limites de submissão da tarefa** para se adequar às suas necessidades.

## <a name="increase-maximum-quota-limits"></a>Aumentar limites de quota máxima

Pode encontrar mais informações sobre os limites do Azure no [específico do serviço de Azure limita documentação](../azure-subscription-service-limits.md#data-lake-analytics-limits).

1. Abra um pedido de suporte no portal do Azure.

    ![Página do portal do Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Página do portal do Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Selecione o tipo de problema **Quota**.
3. Selecione seu **subscrição** (Certifique-se de que não é uma subscrição de "avaliação").
4. Selecione o tipo de quota **do Data Lake Analytics**.

    ![Página do portal do Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. Na página do problema, explique o seu limite de aumento pedido com **detalhes** por que motivo é necessário capacidade extra.

    ![Página do portal do Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Verifique se as informações de contacto e criar o pedido de suporte.

A Microsoft analisa o seu pedido e tenta acomodar as necessidades da sua empresa logo que possível.

## <a name="next-steps"></a>Passos Seguintes

* [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Gerir o Azure Data Lake Analytics com o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Monitorizar e resolver problemas das tarefas de Azure Data Lake Analytics com o portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
