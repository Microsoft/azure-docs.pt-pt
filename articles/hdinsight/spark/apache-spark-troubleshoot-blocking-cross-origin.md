---
title: Erro jupyter 404 - "Blocking Cross Origin API" - Azure HDInsight
description: Erro do servidor Jupyter 404 "Não Encontrado" devido a "Blocking Cross Origin API" em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 472dde22e5df32e7d2a276b0515a0f1edafe52b3
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545672"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>Cenário: Erro do servidor Jupyter 404 "Não Encontrado" devido a "Blocking Cross Origin API" em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao utilizar componentes Apache Spark em clusters Azure HDInsight.

## <a name="issue"></a>Problema

Quando acede ao serviço Jupyter no HDInsight, vê-se uma caixa de erro a dizer "Não Encontrado". Se verificar os registos do Jupyter, verá algo assim:

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

Também pode ver um endereço IP no campo "Origem" no registo Jupyter.

## <a name="cause"></a>Causa

Este erro pode ser causado por algumas coisas:

- Se tiver configurado regras do Grupo de Segurança da Rede (NSG) para restringir o acesso ao cluster. Restringir o acesso às regras NSG ainda lhe permitirá aceder diretamente ao Apache Ambari e a outros serviços usando o endereço IP em vez do nome do cluster. No entanto, ao aceder ao Jupyter, pode ver-se um erro de 404 "Não Encontrado".

- Se tiver dado ao seu gateway HDInsight um nome DNS personalizado que não seja o padrão `xxx.azurehdinsight.net` .

## <a name="resolution"></a>Resolução

1. Modifique os ficheiros jupyter.py nestes dois locais:

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. Encontre a linha que diz: `NotebookApp.allow_origin='\"https://{2}.{3}\"'` E altere-a para: `NotebookApp.allow_origin='\"*\"'` .

1. Reinicie o serviço Jupyter de Ambari.

1. Digitar `ps aux | grep jupyter` na origem do comando deve mostrar que permite que qualquer URL se conecte a ele.

Este é um cenário menos seguro do que o que já tínhamos no lugar. Mas é assumido que o acesso ao cluster é restrito e que um de fora é permitido ligar-se ao cluster como temos NSG no lugar.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais detalhadas, [reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).