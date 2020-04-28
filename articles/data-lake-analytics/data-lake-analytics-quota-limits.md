---
title: Ajustar quotas e limites no Azure Data Lake Analytics
description: Aprenda a ajustar e aumentar quotas e limites nas contas Azure Data Lake Analytics (ADLA).
services: data-lake-analytics
ms.service: data-lake-analytics
author: omidm1
ms.author: omidm
ms.reviewer: jasonwhowell
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: c1d7c8cc4b50682a74ac88b2113f0d44ebc55199
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75644720"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>Ajustar quotas e limites no Azure Data Lake Analytics

Aprenda a ajustar e aumentar a quota e os limites nas contas Azure Data Lake Analytics (ADLA). Conhecer estes limites vai ajudá-lo a entender o seu comportamento profissional U-SQL. Todos os limites de quota são macios, para que possa aumentar os limites máximos contactando o suporte azure.

## <a name="azure-subscriptions-limits"></a>Limites de subscrições do Azure

**Número máximo de contas ADLA por subscrição por região:** 5

Se tentar criar uma sexta conta ADLA, terá um erro "Atingiu o número máximo de contas Data Lake Analytics permitidas (5) na região sob nome de subscrição".

Se quiser ir além deste limite, pode experimentar estas opções:
* escolher outra região se adequado
* contacte o suporte do Azure [abrindo um bilhete](#increase-maximum-quota-limits) de apoio para solicitar um aumento de quota.

## <a name="default-adla-account-limits"></a>Limites de conta ADLA por defeito

**Número máximo de Unidades de Análise (UA) por conta:** 250, padrão 32

Este é o número máximo de UsA que pode ser executado simultaneamente na sua conta. Se o seu número total de UsA em todos os postos de trabalho exceder este limite, os empregos mais recentes são automaticamente feitos em fila. Por exemplo:

* Se tiver apenas um emprego a funcionar com 32 U.A., quando apresentar um segundo emprego, vai esperar na fila do trabalho até que o primeiro emprego termine.
* Se já tem quatro postos de trabalho em funcionamento e cada um está a usar 8 UA, quando apresenta um quinto emprego que precisa de 8 Us, espera na fila de trabalho até que existam 8 U.A. disponíveis.

    ![Limites de Análise do Lago de Dados Azure e página de quotas](./media/data-lake-analytics-quota-limits/adjust-quota-limits.png)

**Número máximo de Unidades de Análise (UA) por trabalho:** 250, padrão 32

Este é o número máximo de UA que cada trabalho individual pode ser atribuído na sua conta. Os postos de trabalho que lhe forem atribuídos mais do que este limite serão rejeitados, a menos que o requerente seja afetado por uma política de cálculo (limite de submissão de emprego) que lhes dê mais US por trabalho. O limite superior deste valor é o limite da UA para a conta.

**Número máximo de empregos u-SQL simultâneos por conta:** 20

Este é o número máximo de postos de trabalho que podem ser executados simultaneamente na sua conta. Acima deste valor, os novos postos de trabalho são automaticamente feitos em fila.

## <a name="adjust-adla-account-limits"></a>Ajustar os limites da conta ADLA

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Escolha uma conta ADLA existente.
3. Clique em **Propriedades**.
4. Ajuste os valores para **Máximas DeUs,** Número máximo de postos de trabalho em **funcionamento**e limites de submissão de emprego para atender às suas **necessidades.**

## <a name="increase-maximum-quota-limits"></a>Aumentar os limites máximos de quota

Pode encontrar mais informações sobre os limites do Azure na [documentação de limites de serviço azure.](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-analytics-limits)

1. Abra um pedido de apoio no portal Azure.

    ![Página do portal Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Página do portal Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Selecione o tipo de problema **Quota**.
3. Selecione a sua **Subscrição** (certifique-se de que não é uma subscrição "trial").
4. Selecione o tipo de quota **Data Lake Analytics**.

    ![Página do portal Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. Na página de problemas, explique o limite de aumento solicitado com **detalhes** do porquê de precisar desta capacidade extra.

    ![Página do portal Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Verifique as suas informações de contacto e crie o pedido de apoio.

A Microsoft analisa o seu pedido e tenta acomodar as suas necessidades de negócio o mais rapidamente possível.

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Gerir o Azure Data Lake Analytics com o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Monitorizar e resolver problemas das tarefas de Azure Data Lake Analytics com o portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
