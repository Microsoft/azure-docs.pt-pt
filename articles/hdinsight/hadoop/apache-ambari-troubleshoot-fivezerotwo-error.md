---
title: Erro da Apache Ambari UI 502 em Azure HDInsight
description: Erro apache Ambari UI 502 ao tentar aceder ao seu cluster Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/05/2019
ms.openlocfilehash: 750232b19bd5ef0674a9df79fdf3972a679eda7d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946784"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>Cenário: Erro do Apache Ambari UI 502 em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Quando tenta aceder ao Apache Ambari UI para o seu cluster HDInsight, obtém uma mensagem semelhante a: "502 - Servidor Web recebeu uma resposta inválida enquanto agia como um gateway ou servidor de procuração."

## <a name="cause"></a>Causa

Em geral, o código de estado HTTP 502 significa que o servidor Ambari não está a funcionar corretamente no headnode ativo. Há algumas possíveis causas de raiz.

## <a name="resolution"></a>Resolução

Na maioria dos casos, para mitigar o problema, pode reiniciar o headnode ativo. Ou escolha um tamanho VM maior para o seu nome de cabeça.

### <a name="ambari-server-failed-to-start"></a>Servidor Ambari falhou no arranque

Pode verificar os registos do servidor ambari para descobrir por que razão o servidor Ambari não foi iniciado. Uma razão comum é o erro de verificação da consistência da base de dados. Pode descobrir isto neste ficheiro de registo: `/var/log/ambari-server/ambari-server-check-database.log` .

Se tiver feito modificações no nó de agrupamento, desfaça-as. Utilize sempre o UI Ambari para modificar quaisquer configurações relacionadas com Hadoop/Spark.

### <a name="ambari-server-taking-100-cpu-utilization"></a>Servidor Ambari tendo 100% de utilização de CPU

Em situações raras, vimos que o processo do servidor ambari tem uma utilização de CPU de cerca de 100% constantemente. Como mitigação, pode ssh para o headnode ativo, e matar o processo do servidor Ambari e reiniciá-lo novamente.

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>Servidor de Ambari morto por assassino de oom

Em alguns cenários, a tua cabeça fica sem memória, e o assassino do oom linux começa a escolher processos para matar. Pode verificar esta situação pesquisando o ID do processo AmbariServer, que não deve ser encontrado. Então olhe para o `/var/log/syslog` seu, e procure algo assim:

```
Jul 27 15:29:30 xxx-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

Em seguida, identifique quais os processos que estão a tirar memórias e tente mover-se ainda mais.

### <a name="other-issues-with-ambari-server"></a>Outros problemas com o servidor Ambari

Raramente o servidor Ambari não consegue lidar com o pedido de entrada, pode encontrar mais informações olhando para os registos do servidor ambari para qualquer erro. Um desses casos é um erro como este:

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>Próximos passos

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]