---
title: Exceção ao executar consultas da Vista da Colmeia Apache Ambari em Azure HDInsight
description: Passos de resolução de problemas ao executar consultas apache hive através de Apache Ambari Hive View em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: 809b2e383eb57b730fd76ec2194764178aa810c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895037"
---
# <a name="exception-when-running-queries-from-apache-ambari-hive-view-in-azure-hdinsight"></a>Exceção ao executar consultas da Vista da Colmeia Apache Ambari em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Ao executar uma consulta apache hive da Apache Ambari Hive View, você recebe a seguinte mensagem de erro intermitentemente:

```error
Cannot create property 'errors' on string '<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Strict//EN' 'http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'>
<html xmlns='http://www.w3.org/1999/xhtml'>
<head>
<title>IIS 8.5 Detailed Error - 502.3 - Bad Gateway</title>
```

## <a name="cause"></a>Causa

Um intervalo de entrada.

O valor do tempo de saída do Gateway é de 2 minutos. As consultas da Ambari Hive View `/hive2` são submetidas ao ponto final através do portal. Uma vez que a consulta é compilada e `queryid`aceite com sucesso, o HiveServer devolve um . Os clientes continuam então a sondar o estado da consulta. Durante este processo, se o HiveServer não devolver uma resposta HTTP dentro de 2 minutos, o Gateway HDI lança um erro de tempo de 502.3 gateway para o chamador. Os erros podem ocorrer quando a consulta é submetida para processamento (mais provável) e também na chamada de estado de obter (menos provável). Os utilizadores podiam ver qualquer um deles.

O fio http handler é suposto ser rápido: `queryid`preparar o trabalho e devolver a . No entanto, devido a várias razões, todos os fios do manipulador podem estar ocupados, resultando em intervalos para novas consultas e chamadas de estado.

### <a name="responsibilities-of-the-http-handler-thread"></a>Responsabilidades do fio manipulador HTTP

Quando o cliente submete uma consulta ao HiveServer, faz o seguinte no fio de primeiro plano:

* Analisar o pedido, fazer verificação semântica
* Adquirir fechadura
* Procura de metastore, se necessário
* Compilar a consulta (DDL ou DML)
* Preparar um plano de consulta
* Executar a autorização (Execute todas as políticas aplicáveis dos rangers em clusters seguros)

## <a name="resolution"></a>Resolução

Algumas recomendações gerais para melhorar a situação:

* Se utilizar uma metaloja externa de colmeia, verifique as métricas de DB e certifique-se de que a base de dados não está sobrecarregada. Considere escalar a camada de base de dados da metaloja.

* Certifique-se de que as operações paralelas são ligadas (isto permite que os fios de manipulador http funcionam em paralelo). Para verificar o valor, lance [O Apache Ambari](../hdinsight-hadoop-manage-ambari.md) e navegue até**hive****avançada** > da **Hive** > **Configs** > . O valor `hive.server2.parallel.ops.in.session` para `true`deve ser.

* Certifique-se de que o VM SKU do cluster não é muito pequeno para a carga. Considere dividir o trabalho entre vários aglomerados. Para mais informações, consulte [Escolha um tipo de cluster](../hdinsight-capacity-planning.md#choose-a-cluster-type).

* Se o Ranger estiver instalado no cluster, verifique se há demasiadas políticas ranger que precisam de ser avaliadas para cada consulta. Procure políticas duplicadas ou desnecessárias.

* Verifique o valor do tamanho do **Monte HiveServer2** de Ambari. Navegue até à**otimização**das**definições** > da **Hive** > **Configs.** >  Certifique-se de que o valor é superior a 10 GB. Ajuste-se conforme necessário para otimizar o desempenho.

* Certifique-se de que a consulta da Colmeia está bem afinada. Para mais informações, consulte [otimize consultas da Hive Apache no Azure HDInsight](../hdinsight-hadoop-optimize-hive-query.md).

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligar a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
