---
title: Streaming de dados em Azure SQL Edge (Pré-visualização)
description: Saiba mais sobre o streaming de dados em Azure SQL Edge (Pré-visualização)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 15b6b80f9924b050b388d74adc1d67db6a386134
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597311"
---
# <a name="data-streaming-in-azure-sql-edge-preview"></a>Streaming de dados em Azure SQL Edge (Pré-visualização)

O Azure SQL Edge (Preview) fornece duas opções diferentes para implementar o streaming de dados. 

1. Implementação de postos de trabalho azure streaming analytics edge criados em Azure. Para obter mais informações sobre como implementar postos de trabalho azure streaming analytics edge em Azure SQL Edge consulte [Deploy Azure Stream Analytics Jobs](deploy-dacpac.md).
2. Utilizando a nova funcionalidade **de Streaming T-SQL** para criar trabalhos de streaming em SQL Edge, sem a necessidade de configurar trabalhos de streaming em Azure. 

Embora seja possível utilizar ambas as opções para implementar o streaming de dados no SQL Edge, é altamente recomendado utilizar apenas uma. Ao utilizar ambos, pode haver possíveis condições de corrida que impactam o funcionamento das operações de streaming de dados.

O resto deste documento refere-se à nova funcionalidade, **O Streaming T-SQL,** que fornece streaming de dados em tempo real, análise e processamento de eventos para analisar e processar grandes volumes de dados de streaming rápido de várias fontes simultaneamente. *O T-SQL Streaming* é construído utilizando o mesmo motor de streaming de alto desempenho que alimenta o [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction) no Microsoft Azure, e suporta um conjunto de capacidades semelhantes oferecidas pelo Azure Stream Analytics que corre no limite.

Tal como acontece com o Azure Stream Analytics, o Streaming T-SQL permite o reconhecimento de padrões e relações em informações extraídas de uma série de fontes de entrada ioT, incluindo dispositivos, sensores e aplicações. Estes padrões podem ser usados para desencadear ações e iniciar fluxos de trabalho como criar alertas, alimentar informações para uma solução de reporte ou visualização, ou armazenar os dados para posterior utilização. 

Os seguintes cenários são exemplos de quando pode utilizar o Streaming T-SQL:

* Analise os fluxos de telemetria em tempo real dos dispositivos IoT.
* Análise em tempo real dos dados gerados a partir de veículos autónomos e sem condutor.
* Monitorização remota e manutenção preditiva de ativos industriais ou industriais de alto valor.
* Deteção de anomalias e/ou reconhecimento de padrões de leituras de sensores IoT numa agricultura ou numa exploração energética.

## <a name="how-does-t-sql-streaming-work"></a>Como funciona o Streaming T-SQL?

O T-SQL Streaming funciona exatamente da mesma forma que o [Azure Stream Analytics,](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction#how-does-stream-analytics-work)por exemplo, usa o conceito de streaming de empregos para o processamento de streaming de dados em tempo real. 

Um trabalho de análise de fluxo, consiste em:

- Entrada de fluxo - Uma entrada stream define as ligações a uma fonte de dados para ler o fluxo de dados a partir de. O Azure SQL Edge suporta atualmente os seguintes tipos de entrada de fluxo:
    - Edge Hub
    - Kafka - Suporte para entradas kafka atualmente está disponível apenas nas versões Intel/AMD64 do Azure SQL Edge.

- Saída de fluxo - Uma saída de Fluxo define as ligações a uma fonte de dados para escrever o fluxo de dados para. O Azure SQL Edge suporta atualmente os seguintes tipos de saída de fluxo
    - Edge Hub
    - SQL - A saída SQL pode ser uma base de dados local dentro da instância SQL Edge ou de um Servidor SQL remoto ou base de dados Azure SQL. 
    - Armazenamento de Blobs do Azure

- Consulta de fluxo - A consulta de fluxo define a transformação, agregações, filtro, triagem e uniões que precisam de ser aplicadas ao fluxo de entrada antes de ser escrita para a saída do fluxo. A consulta de fluxo baseia-se na mesma linguagem de consulta usada pelo Azure Stream Analytics. Para mais informações sobre o idioma de consulta do Azure Stream Analytics, consulte o [Stream Analytics Question Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

> [!IMPORTANT]
> O Streaming T-SQL, ao contrário do Azure Stream Analytics, não suporta atualmente a utilização de dados de [referência para procuras](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data) ou a utilização de [UDF's e UDA's em streaming.](https://docs.microsoft.com/azure/stream-analytics/streaming-technologies#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c)

> [!NOTE]
> O Streaming T-SQL suporta apenas um subconjunto da área de superfície linguística suportada pelo Azure Stream Analytics. Para mais informações sobre o idioma de consulta do Azure Stream Analytics, consulte [o Stream Analytics Question Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

## <a name="limitations-and-restrictions"></a>Limitações e restrições

As seguintes limitações e restrições aplicam-se ao Streaming T-SQL. 

- Apenas um trabalho de streaming pode estar ativo a qualquer momento. Os postos de trabalho que já estão a executar têm de ser interrompidos antes de iniciarem outro trabalho.
- Cada execução de trabalho em streaming é de uma só linha. Se o trabalho de streaming contiver múltiplas consultas, cada consulta será avaliada por ordem de série.

## <a name="next-steps"></a>Passos seguintes

- [Crie um trabalho de Stream Analytics em Azure SQL Edge (Pré-visualização)](create-stream-analytics-job.md)
- [Visualização de metadados associados a trabalhos de streaming em Azure SQL Edge (Pré-visualização)](streaming-catalog-views.md)
- [Criar fluxo externo](create-external-stream-transact-sql.md)