---
title: CpU Core pedido de aumento de quota - Azure HDInsight
description: Aprenda o processo para solicitar um aumento para os núcleos do CPU atribuídos à sua subscrição.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/07/2020
ms.openlocfilehash: a539acc423feeab500830fb33262d1b824101072
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98935834"
---
# <a name="requesting-quota-increases-for-azure-hdinsight"></a>Pedido de aumento de quota para Azure HDInsight

As quotas centrais da CPU ajudam a garantir que a utilização de recursos seja distribuída de forma justa entre todos os clientes de uma determinada região do Azure. No entanto, em certos casos, os seus requisitos de negócio podem exigir mais recursos de cluster do que a sua quota atual permitirá. Nesses casos, pode solicitar um aumento de quota de base da CPU para que possa implantar clusters, que correspondam aos seus requisitos de processamento de dados.

Quando se atinge um limite de quota, não se pode implantar novos clusters ou escalar os aglomerados existentes adicionando mais nós de trabalhadores. O único limite de quota é a quota cpu Cores que existe a nível regional para cada subscrição. Por exemplo, a sua subscrição pode ter um limite de núcleo de 30 CPU na região leste dos EUA, com outros 30 núcleos de CPU permitidos no Leste dos EUA.

## <a name="gather-required-information"></a>Recolher informações necessárias

Se recebeu um erro que indica que atingiu um limite de quota, utilize o processo descrito nesta secção para recolher informações importantes e apresentar um pedido de aumento de quota.

1. Determine o tamanho, escala e tipo de VM do seu cluster desejado.
1. Verifique os limites de capacidade de quota atuais da sua subscrição. Para verificar os seus núcleos disponíveis, faça os seguintes passos:

    1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
    1. Navegue para a página **de visão geral** para o cluster HDInsight.
    1. No menu à esquerda, **selecione Os limites de Quota**. A página apresenta o número de núcleos em utilização, o número de núcleos disponíveis e o total de núcleos.

Para solicitar um aumento de quota, faça as seguintes etapas:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Selecione **Help + suporte** no lado inferior esquerdo da página.

    ![botão de ajuda e suporte](./media/quota-increase-request/help-support-button.png)

1. Selecione **Novo pedido de suporte**.
1. Na página **Novo pedido de suporte**, no separador **Básicos**, selecione as seguintes opções:

   - **Tipo de emissão**: **Limites de serviço e de subscrição (quotas)**
   - **Subscrição**: a subscrição que pretende modificar
   - **Tipo de** quota : **HDInsight**

     ![Crie um pedido de apoio para aumentar a quota de base HDInsight](./media/quota-increase-request/hdinsight-quota-support-request.png)

1. Selecione **Seguinte: Soluções >>**.
1. Na página **Detalhes,** introduza uma descrição do problema, selecione a gravidade do problema, o seu método de contacto preferido e outros campos necessários. Utilize o modelo listado abaixo, para garantir que fornece as informações necessárias. Os pedidos de aumento de quota são avaliados pela equipa de capacidade da Azure, e não pela equipa de produtos HDInsight. Quanto mais completa for a informação, mais provável será o seu pedido.

    ```
    I would like to request [SPECIFY DESIRED AMOUNT] on [DESIRED SKU] for [SUBSCRIPTION ID].
    
    My current quota on this subscription is [CURRENT QUOTA AMOUNT].
    
    I would like to use the extra cores for [DETAIL REASON].
    ```

    ![detalhes do problema](./media/quota-increase-request/problem-details.png)

1. Selecione **Seguinte: Revisão + criar >>**.
1. No **separador 'Rever + criar',** selecione **Criar.**

> [!NOTE]  
> Se precisar de aumentar a quota-base HDInsight numa região privada, [apresente um pedido de lista aprovado.](https://aka.ms/canaryintwhitelist)

Pode [contactar o suporte para solicitar um aumento de quota.](../azure-portal/supportability/resource-manager-core-quotas-request.md)

Há alguns limites de quota fixa. Por exemplo, uma única subscrição do Azure pode ter no máximo 10.000 núcleos. Para obter mais informações sobre estes limites, consulte [os limites de subscrição e serviços da Azure, quotas e constrangimentos.](../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="next-steps"></a>Passos seguintes

* [Configurar clusters em HDInsight com Apache Hadoop, Spark, Kafka, e muito mais](hdinsight-hadoop-provision-linux-clusters.md): Aprenda a configurar e configurar clusters em HDInsight.
* [Monitor cluster performance](hdinsight-key-scenarios-to-monitor.md): Saiba mais sobre cenários-chave para monitorizar o seu cluster HDInsight que pode afetar a capacidade do seu cluster.