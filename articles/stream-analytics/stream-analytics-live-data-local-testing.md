---
title: Dados em direto de teste localmente, utilizando ferramentas do Azure Stream Analytics para Visual Studio (pré-visualização)
description: Saiba como testar a tarefa do Azure Stream Analytics localmente, utilizando dados de transmissão em fluxo em direto.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: f0a8978a9c2e0538a2e7bc4eab202604913e700b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984165"
---
# <a name="test-live-data-locally-using-azure-stream-analytics-tools-for-visual-studio-preview"></a>Dados em direto de teste localmente, utilizando ferramentas do Azure Stream Analytics para Visual Studio (pré-visualização)

Ferramentas do Stream Analytics do Azure para Visual Studio permite-lhe testar tarefas localmente a partir do IDE com fluxos de eventos em direto do Hub de eventos do Azure, o IoT Hub e o armazenamento de Blobs. Dados não é possível substituir a realização de testes locais dinâmicos a [escalabilidade testar o desempenho e](stream-analytics-streaming-unit-consumption.md) pode realizar na cloud, mas pode poupar tempo durante o teste funcional ao não ter de enviar para a cloud sempre que quiser testar seu Stream Tarefa de análise. Esta funcionalidade está em pré-visualização e não deve ser usada para cargas de trabalho de produção.

## <a name="testing-options"></a>Opções de teste

São suportadas as seguintes opções de testes locais:

|**Input (Entrada)**  |**Saída**  |**Tipo de tarefa**  |
|---------|---------|---------|
|Dados estáticos locais   |  Dados estáticos locais   |   Edge/na cloud |
|Dados de entrada em direto   |  Dados estáticos locais   |   Nuvem |
|Dados de entrada em direto   |  Dados de saída em direto   |   Nuvem |

## <a name="local-testing-with-live-data"></a>Realização de testes locais com dados dinâmicos

1. Depois de criar uma [projeto de cloud do Azure Stream Analytics no Visual Studio](stream-analytics-quick-create-vs.md), abra **script.asaql**. A realização de testes locais usa entrada local e de saída local por padrão.

   ![O Azure Stream Analytics para Visual Studio local de teste com o local de entrada e saída local](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. Para testar dados em direto, escolha **entrada de Cloud de utilização** na caixa pendente.

   ![O Azure Stream Analytics para Visual Studio local de testes com entrada na cloud em direto](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)


3. Definir o **Start Time** para definir quando a tarefa começar a processar dados de entrada. A tarefa poderá ter de ler dados de entrada antes do tempo para garantir resultados precisos. O tempo predefinido está definido como 30 minutos antes da hora atual.

   ![O Azure Stream Analytics para Visual Studio local de teste com a hora de início de dados em direto](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. Clique em **executar localmente**. Será apresentada uma janela de consola com as métricas de progresso e o trabalho em execução. Se desejar interromper o processo, pode fazer isso manualmente. 

   ![O Azure Stream Analytics para Visual Studio realização de testes locais com a janela do processo de dados em direto](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   Os resultados de saída são atualizados a cada três segundos com as primeiras linhas de 500 saída na janela do resultado de execução local e os ficheiros de saída são colocados no caminho do projeto **ASALocalRun** pasta. Também pode abrir os ficheiros de saída ao clicar **Abrir pasta de resultados** botão da janela de resultados de execução local.

   ![Pasta de resultados do Azure Stream Analytics para Visual Studio abra realização de testes locais com dados dinâmicos](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. Se pretender enviar os resultados para os sinks de saída na cloud, escolha **de saída para a Cloud** na segunda caixa pendente. O Power BI e o armazenamento do Azure Data Lake não são sinks de saída suportados.

   ![O Azure Stream Analytics para Visual Studio local de testes com dados em direto de saída para a cloud](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>Limitações

* O Power BI e o armazenamento do Azure Data Lake não são sinks de saída suportadas devido a limitações de modelo de autenticação.

* Apenas as opções de entrada nuvem têm [políticas de tempo](stream-analytics-out-of-order-and-late-events.md) suportar, ao contrário opções de entrada locais.

## <a name="next-steps"></a>Passos Seguintes

* [Criar uma tarefa de Stream Analytics, utilizando as ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-quick-create-vs.md)
* [Instalar as ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-tools-for-visual-studio-install.md)
* [Testar consultas do Stream Analytics localmente com o Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Use o Visual Studio para ver tarefas do Azure Stream Analytics](stream-analytics-vs-tools.md)