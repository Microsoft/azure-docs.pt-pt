---
title: Gerir o espaço do disco em Azure HDInsight
description: Etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com os clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 577bed7ce342be14a50077a3ffd841cd901b5b31
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77473015"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>Gerir o espaço do disco em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="hive-log-configurations"></a>Configurações de registo de colmeia

1. A partir de um navegador web, navegue `https://CLUSTERNAME.azurehdinsight.net` para, onde `CLUSTERNAME` está o nome do seu cluster.

1. Navegue até **Hive**  >  **Configs**  >  **Advanced**  >  **Advanced Hive-log4j**. Reveja as seguintes definições:

    * `hive.root.logger=DEBUG,RFA`. Este é o valor predefinido, modificar o [nível de registo](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html) `INFO` para imprimir menos entradas de registos.

    * `log4jhive.log.maxfilesize=1024MB`. Este é o valor predefinido, modificar conforme desejado.

    * `log4jhive.log.maxbackupindex=10`. Este é o valor predefinido, modificar conforme desejado. Se o parâmetro tiver sido omitido, os ficheiros de registo gerados serão infinitos.

## <a name="yarn-log-configurations"></a>Configurações de log de fios

Reveja as seguintes configurações:

* Apache Ambari

    1. A partir de um navegador web, navegue `https://CLUSTERNAME.azurehdinsight.net` para, onde `CLUSTERNAME` está o nome do seu cluster.

    1. Navegue para o Gestor de Recursos Avançados **Da Hive**  >  **Configs.**  >  **Advanced**  >  **Resource Manager** Certifique-se **de que a agregação de registos** é verificada. Se forem desativadas, os nós de nome manterão os registos localmente e não os agregarão na loja remota após a conclusão ou rescisão da aplicação.

* Verifique se o tamanho do cluster é apropriado para a carga de trabalho. A carga de trabalho pode ter mudado recentemente ou o cluster pode ter sido redimensionado. [Dimensione](../hdinsight-scaling-best-practices.md) o cluster para combinar com uma carga de trabalho mais alta.

* `/mnt/resource`pode ser preenchido com ficheiros órfãos (como no caso do reinício do gestor de recursos). Se necessário, limpe manualmente `/mnt/resource/hadoop/yarn/log` e `/mnt/resource/hadoop/yarn/local` . .

## <a name="next-steps"></a>Próximos passos

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligação da comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).
