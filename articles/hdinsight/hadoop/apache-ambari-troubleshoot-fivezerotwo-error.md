---
title: Erro Apache Ambari UI 502 no Azure HDInsight
description: Erro Apache Ambari UI 502 quando tenta aceder ao seu cluster Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 2b17c2488e47148e8845433f9c7613e1127fbffa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75895763"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>Cenário: Erro Apache Ambari UI 502 no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Ao tentar aceder ao Apache Ambari UI para o seu cluster HDInsight, obtém-se uma mensagem semelhante a: "502 - Servidor Web recebeu uma resposta inválida enquanto atua como um gateway ou servidor proxy."

## <a name="cause"></a>Causa

Em geral, o código de estado HTTP 502 significa que o servidor Ambari não está a funcionar corretamente no nódeo ativo. Há algumas possíveis causas de raiz.

## <a name="resolution"></a>Resolução

Na maioria dos casos, para mitigar o problema, pode reiniciar o nódoado ativo. Ou escolha um tamanho VM maior para o seu headnode.

### <a name="ambari-server-failed-to-start"></a>Servidor Ambari não começou

Pode verificar os registos do servidor ambari para saber porque é que o servidor Ambari não conseguiu arrancar. Uma razão comum é o erro de verificação da consistência da base de dados. Pode descobrir isto neste ficheiro `/var/log/ambari-server/ambari-server-check-database.log`de registo: .

Se fez alguma modificação no nó do cluster, por favor, desfaça-as. Utilize sempre a Ambari UI para modificar quaisquer configurações relacionadas com Hadoop/Spark.

### <a name="ambari-server-taking-100-cpu-utilization"></a>Servidor Ambari a tomar 100% de utilização de CPU

Em situações raras, vimos que o processo ambari-server tem quase 100% de utilização de CPU constantemente. Como mitigação, pode shh ao nódoo ativo, e matar o processo do servidor Ambari e reiniciá-lo novamente.

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>Servidor ambari morto por assassino de oom

Em alguns cenários, o seu cabeçada fica sem memória, e o assassino do linux começa a escolher processos para matar. Pode verificar esta situação pesquisando o ID do processo AmbariServer, que não deve ser encontrado. Então olhe para o seu `/var/log/syslog`e procure algo assim:

```
Jul 27 15:29:30 xxx-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

Em seguida, identifique quais os processos que estão tirando memórias e tente mais causa selada.

### <a name="other-issues-with-ambari-server"></a>Outros problemas com o servidor Ambari

Raramente o servidor Ambari não consegue lidar com o pedido de entrada, pode encontrar mais informações olhando para os registos do servidor ambari para qualquer erro. Um desses casos é um erro como este:

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, por favor reveja [como criar um pedido de apoio Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
