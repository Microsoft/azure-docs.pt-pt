---
title: Streaming de dados em Azure SQL Edge
description: Saiba mais sobre o streaming de dados em Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: f63ab040e750c0c642c9656a5482529b926e9295
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93392117"
---
# <a name="data-streaming-in-azure-sql-edge"></a>Streaming de dados em Azure SQL Edge

O Azure SQL Edge fornece uma implementação nativa das capacidades de streaming de dados chamada T-SQL Streaming. Fornece streaming de dados em tempo real, análise e processamento de eventos para analisar e processar volumes elevados de dados de streaming rápido de múltiplas fontes, simultaneamente. O streaming T-SQL é construído utilizando o mesmo motor de streaming de alto desempenho que alimenta o [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) no Microsoft Azure. A funcionalidade suporta um conjunto de capacidades semelhantes oferecidas pelo Azure Stream Analytics a correr no limite.

Tal como acontece com o Stream Analytics, o Streaming T-SQL reconhece padrões e relações na informação extraída de várias fontes de entrada IoT, incluindo dispositivos, sensores e aplicações. Pode utilizar estes padrões para desencadear ações e iniciar fluxos de trabalho. Por exemplo, pode criar alertas, dar informações a uma solução de reporte ou visualização ou armazenar os dados para posterior utilização. 

O streaming T-SQL pode ajudá-lo:

* Analise os fluxos de telemetria em tempo real a partir de dispositivos IoT.
* Utilize análises em tempo real de dados gerados a partir de veículos autónomos e sem condutor.
* Utilize monitorização remota e manutenção preditiva de ativos industriais ou de fabrico de alto valor.
* Utilize a deteção de anomalias e o reconhecimento de padrões de leituras de sensores IoT numa agricultura ou numa exploração energética.

## <a name="how-does-t-sql-streaming-work"></a>Como funciona o streaming T-SQL?

O streaming T-SQL funciona exatamente da mesma forma que o [Azure Stream Analytics.](../stream-analytics/stream-analytics-introduction.md#how-does-stream-analytics-work) Por exemplo, utiliza o conceito de *trabalhos* de streaming para o processamento de streaming de dados em tempo real. 

Um trabalho de análise de fluxo consiste em:

- **Entrada de fluxo**: Isto define as ligações a uma fonte de dados para ler o fluxo de dados a partir de. A Azure SQL Edge suporta atualmente os seguintes tipos de entrada de fluxo:
    - Hub de borda
    - Kafka (Suporte para entradas Kafka está atualmente disponível apenas nas versões Intel/AMD64 do Azure SQL Edge.)

- **Saída de fluxo**: Isto define as ligações a uma fonte de dados para escrever o fluxo de dados para. A Azure SQL Edge suporta atualmente os seguintes tipos de saída de fluxo
    - Hub de borda
    - SQL (A saída SQL pode ser uma base de dados local no caso de Azure SQL Edge, ou um servidor SQL remoto ou base de dados Azure SQL.) 

- **Consulta de fluxo**: Isto define a transformação, agregações, filtro, triagem e junções a aplicar no fluxo de entrada, antes de ser escrito para a saída do fluxo. A consulta de fluxo baseia-se na mesma linguagem de consulta que a utilizada pela Stream Analytics. Para obter mais informações, consulte [stream Analytics Consulta idioma](/stream-analytics-query/stream-analytics-query-language-reference).

> [!IMPORTANT]
> O streaming T-SQL, ao contrário do Stream Analytics, não suporta atualmente [o uso de dados de referência para procuras](../stream-analytics/stream-analytics-use-reference-data.md) ou [a utilização de UDF e UDA's num trabalho de streaming.](../stream-analytics/streaming-technologies.md#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c)

> [!NOTE]
> O streaming T-SQL suporta apenas um subconjunto da área de superfície do idioma suportado pelo Stream Analytics. Para obter mais informações, consulte [stream Analytics Consulta idioma](/stream-analytics-query/stream-analytics-query-language-reference).

## <a name="limitations-and-restrictions"></a>Limitações e restrições

As seguintes limitações e restrições aplicam-se ao streaming T-SQL. 

- Apenas um trabalho de streaming pode estar ativo a qualquer momento específico. Os postos de trabalho que já estão a decorrer têm de ser interrompidos antes de iniciarem outro trabalho.
- Cada execução de trabalho em streaming é desfasada. Se o trabalho de streaming contiver múltiplas consultas, cada consulta é avaliada por ordem de série.
- Quando interrompeu um trabalho de streaming em Azure SQL Edge, pode haver algum atraso antes do próximo trabalho de streaming poder ser iniciado. Este atraso é introduzido porque o processo de streaming subjacente tem de ser interrompido em resposta ao pedido de paragem do emprego e, em seguida, reiniciado em resposta ao pedido de emprego inicial. 
- T-SQL Streaming até 32 divisórias para um fluxo kafka. As tentativas de configurar uma contagem de divisórias mais elevada resultarão num erro. 

## <a name="next-steps"></a>Passos seguintes

- [Crie um trabalho stream analytics em Azure SQL Edge ](create-stream-analytics-job.md)
- [Visualização de metadados associados a trabalhos de streaming em Azure SQL Edge ](streaming-catalog-views.md)
- [Criar fluxo externo](create-external-stream-transact-sql.md)