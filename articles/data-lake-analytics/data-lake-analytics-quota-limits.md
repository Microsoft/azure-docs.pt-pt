---
title: Ajustar cotas e limites no Azure Data Lake Analytics
description: Saiba como ajustar e aumentar cotas e limites em contas de Azure Data Lake Analytics (ADLA).
services: data-lake-analytics
ms.service: data-lake-analytics
author: omidm1
ms.author: omidm
ms.reviewer: jasonwhowell
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: c1d7c8cc4b50682a74ac88b2113f0d44ebc55199
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644720"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>Ajustar cotas e limites no Azure Data Lake Analytics

Saiba como ajustar e aumentar a cota e os limites em contas de Azure Data Lake Analytics (ADLA). Conhecer esses limites ajudará você a entender seu comportamento de trabalho do U-SQL. Todos os limites de cota são flexíveis, portanto, você pode aumentar os limites máximos entrando em contato com o suporte do Azure.

## <a name="azure-subscriptions-limits"></a>Limites de assinaturas do Azure

**Número máximo de contas adla por assinatura por região:** 5

Se você tentar criar uma conta do sexto ADLA, receberá um erro "você atingiu o número máximo de contas de Data Lake Analytics permitidas (5) na região sob o nome da assinatura".

Se quiser ir além desse limite, você pode tentar estas opções:
* Escolha outra região, se adequado
* entre em contato com o suporte do Azure [abrindo um tíquete de suporte](#increase-maximum-quota-limits) para solicitar um aumento de cota.

## <a name="default-adla-account-limits"></a>Limites de conta padrão do ADLA

**Número máximo de unidades de análise (AUS) por conta:** 250, padrão 32

Este é o número máximo de AUs que pode ser executado simultaneamente em sua conta. Se o número total de AUs em execução em todos os trabalhos exceder esse limite, os trabalhos mais recentes serão enfileirados automaticamente. Por exemplo:

* Se você tiver apenas um trabalho em execução com 32 AUs, quando enviar um segundo trabalho, ele aguardará na fila de trabalhos até que o primeiro trabalho seja concluído.
* Se você já tiver quatro trabalhos em execução e cada um estiver usando 8 AUs, ao enviar um quinto trabalho que precise de 8 AUs, ele aguardará na fila de trabalho até que haja 8 AUs disponíveis.

    ![Azure Data Lake Analytics a página limites e cotas](./media/data-lake-analytics-quota-limits/adjust-quota-limits.png)

**Número máximo de unidades de análise (AUS) por trabalho:** 250, padrão 32

Esse é o número máximo de AUs que cada trabalho individual pode ser atribuído em sua conta. Os trabalhos atribuídos a mais do que esse limite serão rejeitados, a menos que o emissor seja afetado por uma política de computação (limite de envio do trabalho) que forneça mais AUs por trabalho. O limite superior desse valor é o limites de AU para a conta.

**Número máximo de trabalhos U-SQL simultâneos por conta:** 20

Este é o número máximo de trabalhos que podem ser executados simultaneamente em sua conta. Acima desse valor, os trabalhos mais recentes são enfileirados automaticamente.

## <a name="adjust-adla-account-limits"></a>Ajustar limites de conta do ADLA

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Escolha uma conta existente do ADLA.
3. Clique em **Propriedades**.
4. Ajuste os valores para o **máximo AUS**, o **número máximo de trabalhos em execução**e **os limites de envio do trabalho** para atender às suas necessidades.

## <a name="increase-maximum-quota-limits"></a>Aumentar os limites máximos de cota

Você pode encontrar mais informações sobre os limites do Azure na [documentação limites específicos do serviço do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-analytics-limits).

1. Abra uma solicitação de suporte no portal do Azure.

    ![Página do portal Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Página do portal Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Selecione a **cota**do tipo de problema.
3. Selecione sua **assinatura** (verifique se ela não é uma assinatura de "avaliação").
4. Selecione o tipo de cota **Data Lake Analytics**.

    ![Página do portal Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. Na página problema, explique o limite de aumento solicitado com **detalhes** de por que você precisa dessa capacidade extra.

    ![Página do portal Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Verifique suas informações de contato e crie a solicitação de suporte.

A Microsoft revisa sua solicitação e tenta acomodar suas necessidades de negócios assim que possível.

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Gerir o Azure Data Lake Analytics com o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Monitorizar e resolver problemas das tarefas de Azure Data Lake Analytics com o portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
