---
title: Streaming de dados em Azure SQL Edge (Pré-visualização)
description: Saiba mais sobre o streaming de dados em Azure SQL Edge (Pré-visualização)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: ea1bb05e111432a45fffa3b770e01c6da9a076b6
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233134"
---
# <a name="data-streaming-in-azure-sql-edge-preview"></a>Streaming de dados em Azure SQL Edge (Pré-visualização)

O Azure SQL Edge (Preview) oferece duas opções diferentes para implementar o streaming de dados. 

1. Implementação de empregos Azure Streaming Analytics Edge criados em Azure. Para obter mais informações sobre como implementar empregos Azure Streaming Analytics Edge em Azure SQL Edge consulte [Deploy Azure Stream Analytics Jobs](deploy-dacpac.md).
2. Utilizando a nova funcionalidade **de Streaming T-SQL** para criar trabalhos de streaming em SQL Edge, sem a necessidade de configurar trabalhos de streaming em Azure. 

Embora seja possível utilizar ambas as opções para implementar o streaming de dados em SQL Edge, é altamente recomendado usar apenas uma. Ao utilizar ambos, pode haver condições de corrida possíveis que impactem o funcionamento das operações de streaming de dados.

O resto deste documento refere-se à nova funcionalidade, **O Streaming T-SQL,** que fornece streaming de dados em tempo real, análise e processamento de eventos para analisar e processar volumes elevados de dados de streaming rápido de várias fontes simultaneamente. *O T-SQL Streaming* é construído com o mesmo motor de streaming de alto desempenho que alimenta o [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction) no Microsoft Azure, e suporta um conjunto de capacidades semelhantes oferecidos pelo Azure Stream Analytics que corre na borda.

Tal como acontece com o Azure Stream Analytics, o Streaming T-SQL permite o reconhecimento de padrões e relações em informações extraídas de várias fontes de entrada IoT, incluindo dispositivos, sensores e aplicações. Estes padrões podem ser usados para desencadear ações e iniciar fluxos de trabalho, tais como criar alertas, fornecer informações a uma solução de reporte ou visualização, ou armazenar os dados para posterior utilização. 

Os seguintes cenários são exemplos de quando pode utilizar o Streaming T-SQL:

* Analise os fluxos de telemetria em tempo real a partir de dispositivos IoT.
* Análise em tempo real de dados gerados a partir de veículos autónomos e sem condutor.
* Monitorização remota e manutenção preditiva de ativos industriais ou industriais de elevado valor.
* Deteção de anomalias e/ou reconhecimento de padrões de leituras de sensores IoT numa agricultura ou numa exploração energética.

## <a name="how-does-t-sql-streaming-work"></a>Como funciona o Streaming T-SQL?

O Streaming T-SQL funciona exatamente da mesma forma que o [Azure Stream Analytics,](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction#how-does-stream-analytics-work)por exemplo, usa o conceito de trabalhos de streaming para o processamento de streaming de dados em tempo real. 

Um trabalho de análise de fluxo, consiste em:

- Entrada de fluxo - Uma entrada de fluxo define as ligações a uma fonte de dados para ler o fluxo de dados a partir. A Azure SQL Edge suporta atualmente os seguintes tipos de entrada de fluxo:
    - Hub de borda
    - Kafka - Suporte para entradas Kafka está atualmente disponível apenas nas versões Intel/AMD64 do Azure SQL Edge.

- Stream Output - Uma saída de stream define as ligações a uma fonte de dados para escrever o fluxo de dados para. A Azure SQL Edge suporta atualmente os seguintes tipos de saída de fluxo
    - Hub de borda
    - SQL - A saída SQL pode ser uma base de dados local dentro da instância SQL Edge ou um servidor SQL remoto ou base de dados Azure SQL. 
    - Armazenamento de Blobs do Azure

- Consulta de fluxo - A consulta de fluxo define a transformação, agregações, filtro, triagem e junções que precisam ser aplicadas no fluxo de entrada antes de ser escrita para a saída do fluxo. A consulta de fluxo baseia-se na mesma linguagem de consulta utilizada pelo Azure Stream Analytics. Para obter mais informações sobre o idioma de consulta Azure Stream Analytics, consulte [o Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

> [!IMPORTANT]
> O Streaming T-SQL, ao contrário do Azure Stream Analytics, atualmente não suporta [a utilização de dados de referência para procuras](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data) ou [a utilização do trabalho de streaming da UDF e da UDA.](https://docs.microsoft.com/azure/stream-analytics/streaming-technologies#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c)

> [!NOTE]
> O Streaming T-SQL suporta apenas um subconjunto da área de superfície do idioma suportado pelo Azure Stream Analytics. Para obter mais informações sobre o idioma de consulta Azure Stream Analytics, consulte [o Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

## <a name="limitations-and-restrictions"></a>Limitações e restrições

As seguintes limitações e restrições aplicam-se ao Streaming T-SQL. 

- Apenas um trabalho de streaming pode ser ativo a qualquer momento. Os postos de trabalho que já estão a ser executados têm de ser interrompidos antes de iniciar outro trabalho.
- Cada execução de trabalho em streaming é apenas roscada. Se o trabalho de streaming contiver múltiplas consultas, cada consulta será avaliada por ordem de série.

## <a name="next-steps"></a>Passos seguintes

- [Criar um trabalho stream analytics em Azure SQL Edge (Pré-visualização)](create-stream-analytics-job.md)
- [Visualização de metadados associados a trabalhos de streaming em Azure SQL Edge (Pré-visualização)](streaming-catalog-views.md)
- [Criar fluxo externo](create-external-stream-transact-sql.md)