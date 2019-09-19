---
title: Erro do Jupyter Server 404 devido a "bloqueio da API de origem cruzada" no Azure HDInsight
description: Erro do Jupyter Server 404 "não encontrado" devido a "bloqueio da API de origem cruzada" no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: f5ac70381f0879a896e96cb37a70c1b1a1f0b5da
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090995"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>Cenário: Erro do Jupyter Server 404 "não encontrado" devido a "bloqueio da API de origem cruzada" no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao usar os componentes do Apache Spark nos clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Ao acessar o serviço Jupyter no HDInsight, você verá uma caixa de erro dizendo "não encontrado". Se você verificar os logs do Jupyter, verá algo assim:

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: hn0-pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

Você também pode ver um endereço IP no campo "origem" no log do Jupyter.

## <a name="cause"></a>Causa

Esse erro pode ser causado por algumas coisas:

- Se você tiver configurado regras de NSG (grupo de segurança de rede) para restringir o acesso ao cluster. Restringir o acesso com regras NSG ainda permitirá que você acesse diretamente o Apache Ambari e outros serviços usando o endereço IP em vez do nome do cluster. No entanto, ao acessar o Jupyter, você pode ver um erro 404 "não encontrado".

- Se você tiver dado ao seu gateway do HDInsight um nome DNS personalizado diferente do `xxx.azurehdinsight.net`padrão.

## <a name="resolution"></a>Resolução

1. Modifique os arquivos jupyter.py nestes dois locais:

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. Localize a linha que diz: `NotebookApp.allow_origin='\"https://{2}.{3}\"'`E altere-o para `NotebookApp.allow_origin='\"*\"'`:.

1. Reinicie o serviço Jupyter de Ambari.

1. Digitar `ps aux | grep jupyter` no prompt de comando deve mostrar que ele permite que qualquer URL se conecte a ele.

Isso é menos seguro do que a configuração que já tínhamos em vigor. Mas é presumido que o acesso ao cluster seja restrito e que um de fora tenha permissão para se conectar ao cluster, pois temos NSG em vigor.

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
