---
title: HDFS local paralisado no modo de segurança no cluster do Azure HDInsight
description: Solucionar problemas do Apache HDFS local preso no modo de segurança no cluster do Apache no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: 2b420cc584c4514802e137212b5803e59869968b
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087412"
---
# <a name="scenario-local-hdfs-stuck-in-safe-mode-on-azure-hdinsight-cluster"></a>Cenário: HDFS local paralisado no modo de segurança no cluster do Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

O HDFS (Apache Hadoop local Sistema de Arquivos Distribuído) está preso no modo de segurança no cluster HDInsight. Você receberá uma mensagem de erro semelhante à seguinte:

```output
hdiuser@hn0-spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
...
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

## <a name="cause"></a>Causa

O cluster HDInsight foi reduzido para poucos nós abaixo, ou o número de nós está perto do fator de replicação HDFS.

## <a name="resolution"></a>Resolução

1. Relate o status do HDFS no cluster HDInsight com o seguinte comando:

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
    ```

1. Verifique a integridade do HDFS no cluster HDInsight com o seguinte comando:

    ```bash
    hdiuser@hn0-spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
    ```

1. Se determinado que não há blocos ausentes, corrompidos ou em replicados, ou esses blocos podem ser ignorados, execute o seguinte comando para retirar o nó de nome do modo de segurança:

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
    ```

## <a name="next-steps"></a>Passos Seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
