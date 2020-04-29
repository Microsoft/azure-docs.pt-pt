---
title: Gerir o espaço do disco em Azure HDInsight
description: Passos de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 577bed7ce342be14a50077a3ffd841cd901b5b31
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77473015"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>Gerir o espaço do disco em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="hive-log-configurations"></a>Configurações de log da colmeia

1. De um navegador web, navegue até, `https://CLUSTERNAME.azurehdinsight.net`onde `CLUSTERNAME` está o nome do seu cluster.

1. Navegue até **Hive** > **Configs** > **Advanced** > **Hive-log4j**. Reveja as seguintes definições:

    * `hive.root.logger=DEBUG,RFA`. Este é o valor predefinido, `INFO` modificar o [nível de registo](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html) para imprimir entradas de registos menos.

    * `log4jhive.log.maxfilesize=1024MB`. Este é o valor padrão, modificar como desejado.

    * `log4jhive.log.maxbackupindex=10`. Este é o valor padrão, modificar como desejado. Se o parâmetro tiver sido omitido, os ficheiros de registo gerados serão infinitos.

## <a name="yarn-log-configurations"></a>Configurações de log de fios

Reveja as seguintes configurações:

* Apache Ambari

    1. De um navegador web, navegue até, `https://CLUSTERNAME.azurehdinsight.net`onde `CLUSTERNAME` está o nome do seu cluster.

    1. Navegue para **hive** > **Configs** > **Advanced** > Resource**Manager**. Certifique-se de que a **agregação** de registo activaé verificada. Se desativado, os nós de nome mantêm os registos localmente e não os agregam em loja remota no final ou na rescisão da aplicação.

* Verifique se o tamanho do cluster é apropriado para a carga de trabalho. A carga de trabalho pode ter mudado recentemente ou o cluster pode ter sido redimensionado. [Aumentar](../hdinsight-scaling-best-practices.md) o cluster para corresponder a uma carga de trabalho mais elevada.

* `/mnt/resource`pode ser preenchido com ficheiros órfãos (como no caso do reinício do gestor de recursos). Se necessário, `/mnt/resource/hadoop/yarn/log` limpe `/mnt/resource/hadoop/yarn/local`manualmente e .

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligar a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
