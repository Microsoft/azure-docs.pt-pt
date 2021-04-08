---
title: Ajustar quotas e limites no Azure Data Lake Analytics
description: Saiba como ajustar e aumentar quotas e limites nas contas Azure Data Lake Analytics (ADLA).
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 03/15/2018
ms.openlocfilehash: 416b77fd86316acb8098531257e12b591a0ff052
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101710180"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>Ajustar quotas e limites no Azure Data Lake Analytics

Saiba como ajustar e aumentar a quota e os limites nas contas Azure Data Lake Analytics (ADLA). Conhecer estes limites vai ajudá-lo a entender o seu comportamento de trabalho U-SQL. Todos os limites de quota são suaves, para que possa aumentar os limites máximos contactando o suporte da Azure.

## <a name="azure-subscriptions-limits"></a>Limites de subscrições do Azure

**Número máximo de contas ADLA por subscrição por região:**  5

Se tentar criar uma sexta conta ADLA, terá um erro "Atingiu o número máximo de contas data lake analytics permitidas (5) na região com nome de subscrição".

Se quiser ir além deste limite, pode experimentar estas opções:

- escolher outra região, se adequado
- contacte o apoio da Azure [abrindo um bilhete de apoio](#increase-maximum-quota-limits) para solicitar um aumento de quota.

## <a name="default-adla-account-limits"></a>Limites de conta ADLA padrão

**Número máximo de Unidades de Análise (AUs) por conta:** 250, padrão 32

Este é o número máximo de AUs que pode ser executado simultaneamente na sua conta. Se o seu número total de AUs em todos os postos de trabalho exceder este limite, os novos empregos são automaticamente postos de trabalho em fila. Por exemplo:

- Se tiver apenas um emprego a funcionar com 32 AUs, quando submeter um segundo emprego, vai esperar na fila do trabalho até que o primeiro trabalho termine.
- Se já tem quatro empregos em execução e cada um está a usar 8 AUs, quando apresenta um quinto emprego que precisa de 8 AUs, espera na fila de trabalho até que haja 8 AUs disponíveis.

    ![Limites e quota do Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/adjust-quota-limits.png)

**Número máximo de Unidades de Análise (AUs) por trabalho:** 250, padrão 32

Este é o número máximo de AUs que cada trabalho individual pode ser atribuído na sua conta. Os postos de trabalho atribuídos mais do que este limite serão rejeitados, a menos que o requerente seja afetado por uma política de computação (limite de submissão de emprego) que lhes dê mais AUs por trabalho. O limite superior deste valor é o limite da AU para a conta.

**Número máximo de empregos U-SQL simultâneos por conta:** 20

Este é o número máximo de empregos que podem ser executados simultaneamente na sua conta. Acima deste valor, os novos postos de trabalho são automaticamente postos de trabalho.

## <a name="adjust-adla-account-limits"></a>Ajustar limites de conta ADLA

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Escolha uma conta ADLA existente.
3. Clique em **Propriedades**.
4. Ajuste os valores para **AUs máximos,** **número máximo de trabalhos em execução** e **limites de submissão de emprego para** atender às suas necessidades.

## <a name="increase-maximum-quota-limits"></a>Aumentar os limites máximos de quota

Pode encontrar mais informações sobre os limites do Azure na [documentação de limites específica do serviço Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-analytics-limits)

1. Abra um pedido de apoio no portal Azure.

   ![Portal Azure Data Lake Analytics - Ajuda e suporte](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

   ![Azure Data Lake Analytics novo pedido de suporte](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)

2. Selecione o tipo de emissão **Quota**.

3. Selecione a sua **Subscrição** (certifique-se de que não é uma subscrição de "teste").

4. Selecione o tipo de quota **Data Lake Analytics**.

   ![Azure Data Lake Analytics apoiar tipo de quota de pedido](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. Na página de problemas, explique o limite de aumento solicitado com **detalhes** do porquê de precisar desta capacidade extra.

   ![Detalhes do pedido de apoio do Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Verifique os seus dados de contacto e crie o pedido de suporte.

A Microsoft revê o seu pedido e tenta acomodar as suas necessidades de negócio o mais rapidamente possível.

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Gerir o Azure Data Lake Analytics com o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
- [Monitorizar e resolver problemas das tarefas de Azure Data Lake Analytics com o portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
