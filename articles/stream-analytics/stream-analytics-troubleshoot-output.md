---
title: Saídas de Troubleshoot Azure Stream Analytics
description: Este artigo descreve técnicas para resolver problemas as suas ligações de saída em empregos de Azure Stream Analytics.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: fb01bf613ef007be6eafa98e12f4c5f4b5c607ea
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83831178"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Saídas de Troubleshoot Azure Stream Analytics

Este artigo descreve problemas comuns com ligações de saída do Azure Stream Analytics e como resolver problemas de saída. Muitos passos de resolução de problemas requerem recursos e outros registos de diagnóstico para o seu trabalho de Stream Analytics. Se não tiver registos de recursos ativados, consulte [o Troubleshoot Azure Stream Analytics utilizando registos](stream-analytics-job-diagnostic-logs.md)de recursos .

## <a name="the-job-doesnt-produce-output"></a>O trabalho não produz produção

1. Verifique a conectividade com as saídas utilizando o botão **de ligação** de teste para cada saída.
1. Veja as [métricas de monitorização](stream-analytics-monitoring.md) no separador **Monitor.** Como os valores são agregados, as métricas são adiadas por alguns minutos.

   * Se o valor **dos Eventos de Entrada** for superior a zero, o trabalho pode ler os dados de entrada. Se o valor dos **Eventos de Entrada** não for maior do que zero, há um problema com a entrada do trabalho. Consulte as ligações de [entrada de Troubleshoot](stream-analytics-troubleshoot-input.md) para obter mais informações.
   * Se o valor dos Erros de Conversão de **Dados** for superior a zero e subir, consulte erros de dados do [Azure Stream Analytics](data-errors.md) para obter informações detalhadas sobre erros de conversão de dados.
   * Se o valor dos Erros de Tempo de **Execução** for superior a zero, o seu trabalho recebe dados, mas gera erros durante o processamento da consulta. Para encontrar os erros, aceda aos [registos](../azure-resource-manager/management/view-activity-logs.md)de auditoria e, em seguida, filtre no estado **falhado.**
   * Se o valor **dos Eventos de Entrada** for superior a zero e o valor dos **Eventos** de Saída for igual a zero, uma das seguintes declarações é verdadeira:
      * O processamento de consulta resultou em eventos de saída zero.
      * Os eventos ou campos podem ser mal formados, resultando numa saída zero após o processamento da consulta.
      * O trabalho não foi capaz de empurrar os dados para o lavatório de saída por razões de conectividade ou autenticação.

   As mensagens de registo de operações explicam detalhes adicionais, incluindo o que está a acontecer, exceto nos casos em que a lógica da consulta filtra todos os eventos. Se o processamento de vários eventos gerar erros, os erros agregam-se a cada 10 minutos.

## <a name="the-first-output-is-delayed"></a>A primeira saída está atrasada

Quando um trabalho de Stream Analytics começa, os eventos de entrada são lidos. Mas pode haver um atraso na produção, em certas circunstâncias.

Grandes valores de tempo em elementos de consulta temporal podem contribuir para o atraso de saída. Para produzir a saída correta em grandes janelas de tempo, o trabalho de streaming lê dados do mais recente tempo possível para preencher a janela de tempo. Os dados podem ter até sete dias de vida. Nenhuma saída produz até que os eventos de entrada pendentes sejam lidos. Este problema pode surgir quando o sistema atualiza os trabalhos de streaming. Quando ocorre uma atualização, o trabalho recomeça. Tais atualizações geralmente ocorrem uma vez a cada dois meses.

Use a discrição ao conceber a sua consulta Stream Analytics. Se utilizar uma grande janela de tempo para elementos temporais na sintaxe de consulta do trabalho, pode levar a um atraso na primeira saída quando o trabalho começa ou reinicia. Mais de várias horas, até sete dias, é considerado uma grande janela temporal.

Uma mitigação para este tipo de primeiro atraso de saída é usar técnicas de paraleção de consultas, como a divisão dos dados. Ou, pode adicionar mais Unidades de Streaming para melhorar a entrada até que o trabalho recupere.  Para mais informações, consulte [considerações ao criar empregos](stream-analytics-concepts-checkpoint-replay.md)stream analytics .

Estes fatores afetam a oportunidade da primeira saída:

* A utilização de agregados com janelas, tais como uma cláusula GROUP BY de janelas caindo, pulando e deslizando:

  * Para os agregados de janelas caindo ou saltando, os resultados geram no final do período de tempo da janela.
  * Para uma janela deslizante, os resultados geram quando um evento entra ou sai da janela deslizante.
  * Se você está planejando usar um grande tamanho de janela, como mais de uma hora, o melhor é escolher uma janela de salto ou deslizamento. Estes tipos de janelas permitem-lhe ver a saída com mais frequência.

* A utilização de juntas temporais, tais como join com datediff:
  * Os fósforos geram assim que ambos os lados dos eventos combinados chegam.
  * Os dados que carecem de uma correspondência, como left outer join, são gerados no final da janela DATEDIFF, para cada evento no lado esquerdo.

* Utilização de funções analíticas temporais, tais como ISFIRST, LAST e LAG com DURAÇÃO LIMITE:
  * Para funções analíticas, a saída é gerada para cada evento. Não há atraso.

## <a name="the-output-falls-behind"></a>A saída fica para trás

Durante o funcionamento normal de um trabalho, a saída pode ter períodos de latência mais longos e longos. Se a saída ficar para trás assim, pode identificar as causas das raízes examinando os seguintes fatores:

* Se a pia a jusante está estrangulada
* Se a fonte a montante está acelerada
* Se a lógica de processamento na consulta é intensiva em computação

Para ver os detalhes da saída, selecione o trabalho de streaming no portal Azure e, em seguida, selecione o **diagrama**de Trabalho . Para cada entrada, há uma métrica de evento de atraso por partição. Se a métrica continuar a aumentar, é um indicador de que os recursos do sistema estão limitados. O aumento deve-se potencialmente ao estrangulamento do sumidouro da saída ou ao elevado uso de CPU. Para obter mais informações, consulte a [depuração orientada por dados utilizando o diagrama](stream-analytics-job-diagram-with-metrics.md)de trabalho .

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Aviso de violação chave com saída de base de dados Azure SQL

Quando configura uma base de dados Azure SQL como saída para um trabalho de Stream Analytics, insere registos a granel na tabela de destino. Em geral, o Azure Stream Analytics garante [pelo menos uma entrega](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) ao sumidouro de saída. Ainda pode [conseguir uma entrega exata mente]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) uma vez para uma saída SQL quando uma tabela SQL tem uma restrição única definida.

Quando cria constrangimentos de chave únicos na tabela SQL, o Azure Stream Analytics remove registos duplicados. Divide os dados em lotes e insere recursivamente os lotes até que seja encontrado um único registo duplicado. O processo de divisão e inserção ignora os duplicados um de cada vez. Para um trabalho de streaming que tem muitas filas duplicadas, o processo é ineficiente e demorado. Se vir várias mensagens de aviso de violação de chaves no registo de atividade seleções durante a hora anterior, é provável que a sua saída SQL esteja a atrasar todo o trabalho.

Para resolver este problema, [configure o índice]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) que está a causar a violação da chave, permitindo que a opção IGNORE_DUP_KEY. Esta opção permite ao SQL ignorar valores duplicados durante as inserções a granel. A Base de Dados Azure SQL simplesmente produz uma mensagem de aviso em vez de um erro. Como resultado, o Azure Stream Analytics já não produz erros de violação chave primárias.

Note as seguintes observações ao configurar IGNORE_DUP_KEY para vários tipos de índices:

* Não é possível colocar IGNORE_DUP_KEY numa chave primária ou numa restrição única que utilize o ALTER INDEX. Tens de baixar o índice e recriá-lo.  
* Pode definir IGNORE_DUP_KEY utilizando o ALTER INDEX para um índice único. Esta instância é diferente de uma chave primária/restrição única e é criada utilizando uma definição de ÍNDICE DE CRIAÇÃO ou INDEX.  
* A opção IGNORE_DUP_KEY não se aplica aos índices de lojas de colunas porque não se pode impor a singularidade deles.  

## <a name="column-names-are-lowercase-in-azure-stream-analytics-10"></a>Os nomes das colunas são minúsculos no Azure Stream Analytics (1.0)

Ao utilizar o nível de compatibilidade original (1.0), o Azure Stream Analytics altera os nomes das colunas para minúsculas. Este comportamento foi corrigido em níveis de compatibilidade posteriores. Para preservar o caso, passe para o nível de compatibilidade 1.1 ou posterior. Para obter mais informações, consulte o nível de [compatibilidade para os trabalhos do Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level).

## <a name="get-help"></a>Obter ajuda

Para mais assistência, experimente o nosso [Microsoft Q&Uma página de perguntas para o Azure Stream Analytics](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência da linguagem da consulta de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência rest API de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
