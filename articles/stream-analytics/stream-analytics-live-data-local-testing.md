---
title: Teste dados ao vivo com Azure Stream Analytics para Estúdio Visual
description: Saiba como testar o seu trabalho de Azure Stream Analytics localmente utilizando dados de streaming ao vivo.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: f2876ea32bdcd900a454ae6b7ac58c11b8ec67c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76840490"
---
# <a name="test-live-data-locally-using-azure-stream-analytics-tools-for-visual-studio-preview"></a>Teste dados ao vivo localmente utilizando ferramentas Azure Stream Analytics para Estúdio Visual (Pré-visualização)

As ferramentas Azure Stream Analytics para O Estúdio Visual permitem-lhe testar trabalhos localmente a partir do IDE utilizando streams de eventos ao vivo do Azure Event Hub, IoT Hub e Blob Storage. Os testes locais de dados ao vivo não podem substituir o desempenho e os testes de [escalabilidade](stream-analytics-streaming-unit-consumption.md) que pode realizar na nuvem, mas pode economizar tempo durante os testes funcionais por não ter de se submeter à nuvem sempre que quiser testar o seu trabalho de Streaming Analytics. Esta funcionalidade encontra-se em pré-visualização e não deve ser utilizada para cargas de trabalho de produção.

## <a name="testing-options"></a>Opções de teste

São apoiadas as seguintes opções de teste locais:

|**Input**  |**Saída**  |**Tipo de trabalho**  |
|---------|---------|---------|
|Dados estáticos locais   |  Dados estáticos locais   |   Nuvem/Borda |
|Dados de entrada ao vivo   |  Dados estáticos locais   |   Nuvem |
|Dados de entrada ao vivo   |  Dados de saída ao vivo   |   Nuvem |

## <a name="local-testing-with-live-data"></a>Testes locais com dados ao vivo

1. Depois de ter criado um [projeto de nuvem Azure Stream Analytics no Estúdio Visual,](stream-analytics-quick-create-vs.md)open **script.asaql**. Os testes locais utilizam a entrada local e a saída local por padrão.

   ![Entrada local do Azure Stream Analytics Studio e saída local](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. Para testar dados ao vivo, escolha a Utilização de **Cloud Input** a partir da caixa de dropdown.

   ![Entrada em nuvem ao vivo do Azure Stream Analytics Studio](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)

3. Defina o **Tempo de Início** para definir quando o trabalho começará a processar os dados de entrada. O trabalho pode precisar de ler os dados de entrada com antecedência para garantir resultados precisos. O tempo de predefinição é definido para 30 minutos antes do tempo atual.

   ![Azure Stream Analytics Visual Studio data live start time](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. Clique em **Executar Localmente**. Uma janela de consola aparecerá com o progresso em execução e métricas de trabalho. Se quiser parar o processo, pode fazê-lo manualmente. 

   ![Janela de processo de processo de dados ao vivo do Azure Stream Analytics Studio](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   Os resultados de saída são atualizados a cada três segundos com as primeiras 500 linhas de saída na janela de resultados de execução local, e os ficheiros de saída são colocados na sua pasta **ASALocalRun.** Também pode abrir os ficheiros de saída clicando no botão **da pasta de resultados abertos** na janela de resultados de execução local.

   ![Azure Stream Analytics Visual Studio live data open results folder](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. Se pretender obter os resultados para os seus asinks de saída em nuvem, escolha **Output to Cloud** a partir da segunda caixa de dropdown. O Power BI e o Azure Data Lake Storage não são sumidouros de saída suportados.

   ![Saída de dados ao vivo do Azure Stream Analytics Studio para a nuvem](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>Limitações

* O Power BI e o Azure Data Lake Storage não são sumidouros de saída suportados devido a limitações do modelo de autenticação.

* Apenas as opções de entrada em nuvem têm apoio de políticas de [tempo,](stream-analytics-out-of-order-and-late-events.md) enquanto as opções locais de entrada não.

## <a name="next-steps"></a>Passos seguintes

* [Crie um trabalho de Stream Analytics utilizando as ferramentas Azure Stream Analytics para Estúdio Visual](stream-analytics-quick-create-vs.md)
* [Test Stream Analytics consultas localmente com Estúdio Visual](stream-analytics-vs-tools-local-run.md)
* [Use o Estúdio Visual para ver empregos da Azure Stream Analytics](stream-analytics-vs-tools.md)
