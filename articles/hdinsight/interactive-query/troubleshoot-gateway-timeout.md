---
title: Exceção ao executar consultas de Apache Ambari Hive View em Azure HDInsight
description: Etapas de resolução de problemas ao executar consultas apache Hive através de Apache Ambari Hive View em Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: 40ef380cd2bd4743b92daf44a0a5b70ade1cbb35
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98933011"
---
# <a name="exception-when-running-queries-from-apache-ambari-hive-view-in-azure-hdinsight"></a>Exceção ao executar consultas de Apache Ambari Hive View em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Ao executar uma consulta apache hive de Apache Ambari Hive View, você recebe a seguinte mensagem de erro intermitentemente:

```error
Cannot create property 'errors' on string '<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Strict//EN' 'http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'>
<html xmlns='http://www.w3.org/1999/xhtml'>
<head>
<title>IIS 8.5 Detailed Error - 502.3 - Bad Gateway</title>
```

## <a name="cause"></a>Causa

Um tempo limite de Gateway.

O valor de tempo limite gateway é de 2 minutos. As consultas de Ambari Hive View são submetidas ao `/hive2` ponto final através do gateway. Uma vez que a consulta é compilada e aceite com sucesso, o HiveServer devolve a `queryid` . Os clientes continuam a sondar o estado da consulta. Durante este processo, se o HiveServer não devolver uma resposta HTTP dentro de 2 minutos, o Gateway HDI lança um erro de tempo limite de gateway 502.3 para o chamador. Os erros podem ocorrer quando a consulta é submetida para processamento (mais provável) e também na chamada de estado de obter (menos provável). Os utilizadores podiam ver qualquer um deles.

O fio do manipulador de http supõe-se que seja rápido: prepare o trabalho e devolva um `queryid` . No entanto, devido a várias razões, todos os fios do manipulador podem estar ocupados, resultando em intervalos de tempo para novas consultas e as chamadas de estado.

### <a name="responsibilities-of-the-http-handler-thread"></a>Responsabilidades do fio do manipulador HTTP

Quando o cliente submete uma consulta à HiveServer, faz o seguinte no fio de primeiro plano:

* Parse o pedido, faça verificação semântica
* Adquirir fechadura
* Procura de metassestore, se necessário
* Compilar a consulta (DDL ou DML)
* Preparar um plano de consulta
* Executar a autorização (Executar todas as políticas de ranger aplicáveis em clusters seguros)

## <a name="resolution"></a>Resolução

Algumas recomendações gerais para melhorar a situação:

* Se utilizar uma metástaria de colmeia externa, verifique as métricas DB e certifique-se de que a base de dados não está sobrecarregada. Considere escalar a camada de base de dados de metastagem.

* Certifique-se de que as operações paralelas estão ligadas (isto permite que as linhas do manipulador HTTP corram paralelamente). Para verificar o valor, lance [Apache Ambari](../hdinsight-hadoop-manage-ambari.md) e navegue até   >    >  hive-site **hive**  >  **Advanced Custom Hive** Configs. O valor para `hive.server2.parallel.ops.in.session` deve ser `true` .

* Certifique-se de que o VM SKU do cluster não é muito pequeno para a carga. Considere dividir o trabalho entre vários aglomerados. Para obter mais informações, consulte [Escolha um tipo de cluster.](../hdinsight-capacity-planning.md#choose-a-cluster-type)

* Se o Ranger estiver instalado no cluster, verifique se há demasiadas políticas ranger que precisam de ser avaliadas para cada consulta. Procure políticas duplicadas ou não necessidades.

* Verifique o valor do tamanho do **heap hiveServer2** de Ambari. Navegue para a otimização de configurações de configurações **da Hive**  >  **Configs**  >    >  . Certifique-se de que o valor é superior a 10 GB. Ajuste conforme necessário para otimizar o desempenho.

* Certifique-se de que a consulta da Colmeia está bem afinada. Para obter mais informações, consulte [as consultas de Otimize Apache Hive em Azure HDInsight](../hdinsight-hadoop-optimize-hive-query.md).

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligação da comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).