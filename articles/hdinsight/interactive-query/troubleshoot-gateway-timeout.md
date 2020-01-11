---
title: Exceção ao executar consultas do modo de exibição de hive do Apache Ambari no Azure HDInsight
description: Etapas de solução de problemas ao executar consultas de Apache Hive por meio da exibição de hive do Apache Ambari no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: 809b2e383eb57b730fd76ec2194764178aa810c0
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75895037"
---
# <a name="exception-when-running-queries-from-apache-ambari-hive-view-in-azure-hdinsight"></a>Exceção ao executar consultas do modo de exibição de hive do Apache Ambari no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Ao executar uma consulta de Apache Hive da exibição do hive do Apache Ambari, você receberá a seguinte mensagem de erro intermitentemente:

```error
Cannot create property 'errors' on string '<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Strict//EN' 'http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'>
<html xmlns='http://www.w3.org/1999/xhtml'>
<head>
<title>IIS 8.5 Detailed Error - 502.3 - Bad Gateway</title>
```

## <a name="cause"></a>Causa

Um tempo limite de gateway.

O valor de tempo limite do gateway é de 2 minutos. As consultas da exibição do hive do Ambari são enviadas para o ponto de extremidade `/hive2` por meio do gateway. Depois que a consulta for compilada e aceita com êxito, o HiveServer retornará um `queryid`. Em seguida, os clientes continuam sondando o status da consulta. Durante esse processo, se o HiveServer não retornar uma resposta HTTP em 2 minutos, o gateway HDI lançará um erro de tempo limite de gateway 502,3 para o chamador. Os erros podem acontecer quando a consulta é enviada para processamento (mais provável) e também na chamada Get status (menos provável). Os usuários podem ver qualquer um deles.

O thread do manipulador http deve ser rápido: preparar o trabalho e retornar um `queryid`. No entanto, devido a vários motivos, todos os threads de manipulador podem estar ocupados, resultando em tempos limite para novas consultas e as chamadas get status.

### <a name="responsibilities-of-the-http-handler-thread"></a>Responsabilidades do thread do manipulador HTTP

Quando o cliente envia uma consulta para HiveServer, ele faz o seguinte no thread em primeiro plano:

* Analisar a solicitação, executar verificação semântica
* Adquirir bloqueio
* Pesquisa de metastore, se necessário
* Compilar a consulta (DDL ou DML)
* Preparar um plano de consulta
* Executar autorização (executar todas as políticas aplicáveis do Ranger em clusters seguros)

## <a name="resolution"></a>Resolução

Algumas recomendações gerais para melhorar a situação:

* Se estiver usando um metastore externo do hive, verifique as métricas do BD e certifique-se de que o banco de dados não esteja sobrecarregado. Considere dimensionar a camada de banco de dados do metastore.

* Verifique se a OPS paralela está ativada (isso permite que os threads do manipulador HTTP sejam executados em paralelo). Para verificar o valor, inicie [o Apache Ambari](../hdinsight-hadoop-manage-ambari.md) e navegue até **hive** > **configurações** > **avançado** > **Hive personalizado-site**. O valor de `hive.server2.parallel.ops.in.session` deve ser `true`.

* Verifique se a SKU da VM do cluster não é muito pequena para a carga. Considere a divisão do trabalho entre vários clusters. Para obter mais informações, consulte [escolher um tipo de cluster](../hdinsight-capacity-planning.md#choose-a-cluster-type).

* Se o Ranger estiver instalado no cluster, verifique se há muitas políticas do Ranger que precisam ser avaliadas para cada consulta. Procure políticas duplicadas ou desnecessárias.

* Verifique o valor do **tamanho do heap HiveServer2** de Ambari. Navegue até **Hive** > **configs** > **configurações** > **otimização**. Verifique se o valor é maior que 10 GB. Ajuste conforme necessário para otimizar o desempenho.

* Verifique se a consulta do hive está bem ajustada. Para obter mais informações, consulte [otimizar Apache Hive consultas no Azure HDInsight](../hdinsight-hadoop-optimize-hive-query.md).

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) -a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
