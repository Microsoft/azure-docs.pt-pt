---
title: Escala HiveServer2 em Azure HDInsight
description: Escalar horizontalmente o HiveServer2 em clusters Azure HDInsight utilizando nós de borda para aumentar a tolerância e disponibilidade de falhas.
services: hdinsight
ms.service: hdinsight
ms.topic: conceptual
ms.reviewer: hrasheed-msft
ms.author: kecheung
author: kcheeeung
ms.date: 08/12/2020
ms.openlocfilehash: d0a0df4791492c1c9f0d600630d723024c46c1b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88227400"
---
# <a name="scale-hiveserver2-on-azure-hdinsight-clusters-for-high-availability"></a>Escala HiveServer2 em clusters Azure HDInsight para alta disponibilidade

Saiba como colocar um HiveServer2 adicional no seu cluster para aumentar a disponibilidade e a distribuição de carga. Ao aumentar o tamanho do cabeçano, também pode usar nós de borda para implantar o HiveServer2. 

> [!NOTE]
> Dependendo do seu uso, aumentar o número de HiveServer2 pode aumentar o número de ligações à metástase Hive. Certifique-se também de que a sua base de dados Azure SQL é adequadamente dimensionada.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este guia, terá de compreender o seguinte artigo:
- [Use os nódrupes de borda vazios em aglomerados Apache Hadoop em HDInsight](hdinsight-apps-use-edge-node.md)

## <a name="install-hiveserver2"></a>Instalar HiveServer2

Nesta secção, instalará um HiveServer2 adicional nos anfitriões-alvo.

1. Abra O Ambari no seu navegador e clique no anfitrião do seu alvo.

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-a.png" alt-text="Menu anfitrião de Ambari.":::

2. Clique no botão adicionar e clique no HiveServer2

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-b.png" alt-text="Menu anfitrião de Ambari.":::

3. Confirme e o processo será executado. Repita 1-3 para todos os anfitriões desejados.

4. Quando terminar de instalar, reinicie todos os serviços com configs e inicie o HiveServer2.

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-c.png" alt-text="Menu anfitrião de Ambari.":::

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a instalar o HiveServer2 no seu cluster. Para saber mais sobre os nosdes e aplicações de borda, consulte os seguintes artigos:

* [Instale o nó de borda](hdinsight-apps-use-edge-node.md): Aprenda a instalar um nó de borda no seu cluster HDInsight.
* [Instalar aplicações HDInsight](hdinsight-apps-install-applications.md): Saiba como instalar aplicações HDInsight nos clusters.
* [Limites de ligação Azure SQL DTU](../azure-sql/database/resource-limits-dtu-single-databases.md): Conheça os limites da base de dados Azure SQL utilizando o DTU.
* [Limites de ligação Azure SQL vCore](../azure-sql/database/resource-limits-vcore-elastic-pools.md): Conheça os limites da base de dados Azure SQL utilizando vCores.
