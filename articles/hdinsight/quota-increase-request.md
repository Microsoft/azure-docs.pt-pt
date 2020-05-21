---
title: CPU Core quota aumentar pedido - Azure HDInsight
description: Aprenda o processo para solicitar um aumento para os núcleos cpu atribuídos à sua subscrição.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/07/2020
ms.openlocfilehash: a708ad9626f4d6750de64097a7e1e9177c350e5e
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83717810"
---
# <a name="requesting-quota-increases-for-azure-hdinsight"></a>Solicitação de aumentos de quota para Azure HDInsight

As quotas centrais da CPU ajudam a garantir que a utilização de recursos seja distribuída de forma justa entre todos os clientes de uma determinada região do Azure. No entanto, em certos casos, os seus requisitos de negócio podem exigir mais recursos de cluster do que a sua quota atual permitirá. Nesses casos, pode solicitar um aumento de quota central da CPU para que possa implementar clusters, que correspondam aos seus requisitos de processamento de dados.

Quando se atinge um limite de quota, não se pode implantar novos clusters ou escalar os clusters existentes adicionando mais nós de trabalhadores. O único limite de quota é a quota CPU Cores que existe ao nível da região para cada subscrição. Por exemplo, a sua subscrição pode ter um limite de 30 CPU na região leste dos EUA, com outros 30 núcleos cpu permitidos no Leste dos EUA 2.

## <a name="gather-required-information"></a>Recolher informações necessárias

Se recebeu um erro que indique que atingiu um limite de quota, utilize o processo descrito nesta secção para recolher informações importantes e apresentar um pedido de aumento de quota.

1. Determine o tamanho, escala e tipo de VM do cluster desejado.
1. Verifique os atuais limites de capacidade de quota da sua subscrição. Para verificar os seus núcleos disponíveis, faça os seguintes passos:

    1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
    1. Navegue para a página **de visão geral** do cluster HDInsight.
    1. No menu esquerdo, selecione **limites de quota**. A página apresenta o número de núcleos em utilização, o número de núcleos disponíveis e o total de núcleos.

Para solicitar um aumento de quota, faça os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Selecione **Ajuda + suporte** no lado inferior esquerdo da página.

    ![botão de ajuda e apoio](./media/quota-increase-request/help-support-button.png)

1. Selecione **Novo pedido de suporte**.
1. Na página **Novo pedido de suporte**, no separador **Básicos**, selecione as seguintes opções:

   - **Tipo de emissão**: Limites de **serviço e subscrição (quotas)**
   - **Subscrição**: a subscrição que pretende modificar
   - **Tipo**de quota : **HDInsight**

     ![Criar um pedido de suporte para aumentar a quota central do HDInsight](./media/quota-increase-request/hdinsight-quota-support-request.png)

1. Selecione **Seguinte: Soluções >>**.
1. Na página **Detalhes,** introduza uma descrição do problema, selecione a gravidade do problema, o seu método de contacto preferido e outros campos necessários. Utilize o modelo listado abaixo, para garantir que fornece as informações necessárias. Os pedidos de aumento de quota são avaliados pela equipa de capacidade do Azure, e não pela equipa de produtos HDInsight. Quanto mais informações fornecerem, maior é a probabilidade de o seu pedido ser aprovado.

    ```
    I would like to request [SPECIFY DESIRED AMOUNT] on [DESIRED SKU] for [SUBSCRIPTION ID].
    
    My current quota on this subscription is [CURRENT QUOTA AMOUNT].
    
    I would like to use the extra cores for [DETAIL REASON].
    ```

    ![detalhes do problema](./media/quota-increase-request/problem-details.png)

1. Selecione **Seguinte: Rever + criar >>**.
1. No **separador Review + criar,** selecione **Criar**.

> [!NOTE]  
> Se precisar de aumentar a quota central do HDInsight numa região privada, [apresente um pedido de whitelist](https://aka.ms/canaryintwhitelist).

Pode [contactar o suporte para solicitar um aumento](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)de quota.

Há alguns limites de quota fixas. Por exemplo, uma única subscrição azure pode ter no máximo 10.000 núcleos. Para mais detalhes sobre estes limites, consulte [os limites de subscrição e serviço do Azure, quotas e restrições.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)

## <a name="next-steps"></a>Próximos passos

* [Configure clusters em HDInsight com Apache Hadoop, Spark, Kafka e muito mais:](hdinsight-hadoop-provision-linux-clusters.md)Saiba como configurar e configurar clusters no HDInsight.
* [Monitorize](hdinsight-key-scenarios-to-monitor.md)o desempenho do cluster : Saiba sobre cenários-chave para monitorizar o seu cluster HDInsight que possam afetar a capacidade do seu cluster.