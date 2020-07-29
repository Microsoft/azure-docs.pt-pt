---
title: Streaming de dados em Azure SQL Edge (Pré-visualização)
description: Saiba mais sobre o streaming de dados em Azure SQL Edge (Preview).
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 866c74fbdfcfcef7cbb7d6cddb360c4265a2f776
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84669618"
---
# <a name="data-streaming-in-azure-sql-edge-preview"></a>Streaming de dados em Azure SQL Edge (Pré-visualização)

O Azure SQL Edge (Preview) fornece as seguintes opções para implementar o streaming de dados: 

- Implantação de postos de trabalho Azure Stream Analytics criados em Azure. Para obter mais informações, consulte [os trabalhos de Deploy Azure Stream Analytics](deploy-dacpac.md).
- Utilização do streaming T-SQL para criar trabalhos de streaming em Azure SQL Edge, sem a necessidade de configurar trabalhos de streaming em Azure. 

Embora seja possível utilizar ambas as opções para implementar o streaming de dados em Azure SQL Edge, deve utilizar apenas uma delas. Quando se está a usar ambos, pode haver condições de corrida que afetam o funcionamento das operações de streaming de dados.

O streaming T-SQL é o foco deste artigo. Fornece streaming de dados em tempo real, análise e processamento de eventos para analisar e processar volumes elevados de dados de streaming rápido de múltiplas fontes, simultaneamente. O streaming T-SQL é construído utilizando o mesmo motor de streaming de alto desempenho que alimenta o [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction) no Microsoft Azure. A funcionalidade suporta um conjunto de capacidades semelhantes oferecidas pelo Azure Stream Analytics a correr no limite.

Tal como acontece com o Stream Analytics, o Streaming T-SQL reconhece padrões e relações na informação extraída de várias fontes de entrada IoT, incluindo dispositivos, sensores e aplicações. Pode utilizar estes padrões para desencadear ações e iniciar fluxos de trabalho. Por exemplo, pode criar alertas, dar informações a uma solução de reporte ou visualização ou armazenar os dados para posterior utilização. 

O streaming T-SQL pode ajudá-lo:

* Analise os fluxos de telemetria em tempo real a partir de dispositivos IoT.
* Utilize análises em tempo real de dados gerados a partir de veículos autónomos e sem condutor.
* Utilize monitorização remota e manutenção preditiva de ativos industriais ou de fabrico de alto valor.
* Utilize a deteção de anomalias e o reconhecimento de padrões de leituras de sensores IoT numa agricultura ou numa exploração energética.

## <a name="how-does-t-sql-streaming-work"></a>Como funciona o streaming T-SQL?

O streaming T-SQL funciona exatamente da mesma forma que o [Azure Stream Analytics.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction#how-does-stream-analytics-work) Por exemplo, utiliza o conceito de *trabalhos* de streaming para o processamento de streaming de dados em tempo real. 

Um trabalho de análise de fluxo consiste em:

- **Entrada de fluxo**: Isto define as ligações a uma fonte de dados para ler o fluxo de dados a partir de. A Azure SQL Edge suporta atualmente os seguintes tipos de entrada de fluxo:
    - Hub de borda
    - Kafka (Suporte para entradas Kafka está atualmente disponível apenas nas versões Intel/AMD64 do Azure SQL Edge.)

- **Saída de fluxo**: Isto define as ligações a uma fonte de dados para escrever o fluxo de dados para. A Azure SQL Edge suporta atualmente os seguintes tipos de saída de fluxo
    - Hub de borda
    - SQL (A saída SQL pode ser uma base de dados local no caso de Azure SQL Edge, ou um servidor SQL remoto ou base de dados Azure SQL.) 
    - Armazenamento de Blobs do Azure

- **Consulta de fluxo**: Isto define a transformação, agregações, filtro, triagem e junções a aplicar no fluxo de entrada, antes de ser escrito para a saída do fluxo. A consulta de fluxo baseia-se na mesma linguagem de consulta que a utilizada pela Stream Analytics. Para obter mais informações, consulte [stream Analytics Consulta idioma](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

> [!IMPORTANT]
> O streaming T-SQL, ao contrário do Stream Analytics, não suporta atualmente [o uso de dados de referência para procuras](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data) ou [a utilização de UDF e UDA's num trabalho de streaming.](https://docs.microsoft.com/azure/stream-analytics/streaming-technologies#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c)

> [!NOTE]
> O streaming T-SQL suporta apenas um subconjunto da área de superfície do idioma suportado pelo Stream Analytics. Para obter mais informações, consulte [stream Analytics Consulta idioma](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

## <a name="limitations-and-restrictions"></a>Limitações e restrições

As seguintes limitações e restrições aplicam-se ao streaming T-SQL. 

- Apenas um trabalho de streaming pode estar ativo a qualquer momento específico. Os postos de trabalho que já estão a decorrer têm de ser interrompidos antes de iniciarem outro trabalho.
- Cada execução de trabalho em streaming é desfasada. Se o trabalho de streaming contiver múltiplas consultas, cada consulta é avaliada por ordem de série.

## <a name="next-steps"></a>Próximos passos

- [Criar um trabalho stream analytics em Azure SQL Edge (Pré-visualização)](create-stream-analytics-job.md)
- [Visualização de metadados associados a trabalhos de streaming em Azure SQL Edge (Pré-visualização)](streaming-catalog-views.md)
- [Criar fluxo externo](create-external-stream-transact-sql.md)