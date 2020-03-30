---
title: Erro Jupyter 404 - "Blocking Cross Origin API" - Azure HDInsight
description: Erro do servidor Jupyter 404 "Não Encontrado" devido ao "Blocking Cross Origin API" no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: e241657186582955d21981f7dfe18856724aa692
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894420"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>Cenário: Erro do servidor Jupyter 404 "Não Encontrado" devido ao "Blocking Cross Origin API" no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao usar componentes Apache Spark em clusters Azure HDInsight.

## <a name="issue"></a>Problema

Ao aceder ao serviço Jupyter no HDInsight, vê uma caixa de erro a dizer "Não Encontrada". Se verificar os troncos de Jupyter, verá algo assim:

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

Pode também ver um endereço IP no campo "Origem" no diário de jupyter.

## <a name="cause"></a>Causa

Este erro pode ser causado por algumas coisas:

- Se tiver configurado as regras do Network Security Group (NSG) para restringir o acesso ao cluster. Restringir o acesso com as regras do NSG ainda lhe permitirá aceder diretamente a Apache Ambari e outros serviços usando o endereço IP em vez do nome do cluster. No entanto, ao aceder ao Jupyter, pode ver-se um erro 404 "Não Encontrado".

- Se tiver dado ao seu portal HDInsight um `xxx.azurehdinsight.net`nome DNS personalizado que não seja o padrão .

## <a name="resolution"></a>Resolução

1. Modifique os ficheiros jupyter.py nestes dois locais:

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. Encontre a linha `NotebookApp.allow_origin='\"https://{2}.{3}\"'` que diz: `NotebookApp.allow_origin='\"*\"'`E mude-a para: .

1. Reinicie o serviço Jupyter de Ambari.

1. A `ps aux | grep jupyter` dactilografia no pedido de comando deve mostrar que permite que qualquer URL se conectem a ele.

Este é um lugar menos seguro do que o cenário que já tínhamos no lugar. Mas presume-se que o acesso ao cluster é restrito e que um de fora é permitido ligar-se ao cluster como temos NSG no lugar.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, por favor reveja [como criar um pedido de apoio Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
