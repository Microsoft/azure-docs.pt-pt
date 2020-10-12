---
title: Teste dados ao vivo com Azure Stream Analytics para Estúdio Visual
description: Saiba como testar o seu trabalho Azure Stream Analytics localmente usando dados de streaming ao vivo.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: fe1caca11ec0a3d89ddae140d00cd3556b9d3d89
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86044469"
---
# <a name="test-live-data-locally-using-azure-stream-analytics-tools-for-visual-studio-preview"></a>Teste dados ao vivo localmente usando ferramentas Azure Stream Analytics para Estúdio Visual (Pré-visualização)

As ferramentas Azure Stream Analytics para o Visual Studio permitem-lhe testar trabalhos localmente a partir do IDE usando streams de eventos ao vivo do Azure Event Hub, IoT Hub e Blob Storage. Os testes locais de dados ao vivo não podem substituir o [desempenho e testes de escala](stream-analytics-streaming-unit-consumption.md) que pode realizar na nuvem, mas pode economizar tempo durante os testes funcionais, não tendo de se submeter à nuvem sempre que quiser testar o seu trabalho stream Analytics. Esta funcionalidade está em pré-visualização e não deve ser utilizada para cargas de trabalho de produção.

## <a name="testing-options"></a>Opções de teste

São suportadas as seguintes opções locais de teste:

|**Input**  |**Saída**  |**Tipo de trabalho**  |
|---------|---------|---------|
|Dados estáticos locais   |  Dados estáticos locais   |   Nuvem/Borda |
|Dados de entrada ao vivo   |  Dados estáticos locais   |   Cloud |
|Dados de entrada ao vivo   |  Dados de saída ao vivo   |   Cloud |

## <a name="local-testing-with-live-data"></a>Testes locais com dados ao vivo

1. Depois de ter criado um [projeto em nuvem Azure Stream Analytics no Visual Studio,](stream-analytics-quick-create-vs.md)abra **script.asaql**. Os testes locais utilizam a entrada local e a saída local por defeito.

   ![Entrada local do Azure Stream Analytics Visual Studio e saída local](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. Para testar dados ao vivo, escolha Utilizar a **Entrada cloud** da caixa de entrega.

   ![Entrada em nuvem ao vivo do Azure Stream Analytics Visual Studio](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)

3. Defina a **hora de início** para definir quando o trabalho começará a processar dados de entrada. O trabalho pode precisar de ler os dados de entrada com antecedência para garantir resultados precisos. O tempo predefinido é definido para 30 minutos antes da hora atual.

   ![Azure Stream Analytics Visual Studio ao vivo hora de início](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. Clique **em Executar Localmente.** Aparecerá uma janela de consola com o progresso em funcionamento e as métricas de trabalho. Se quiser interromper o processo, pode fazê-lo manualmente. 

   ![Janela de processo de dados ao vivo do Azure Stream Analytics Visual Studio](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   Os resultados de saída são atualizados a cada três segundos com as primeiras 500 linhas de saída na janela de resultados de execução local, e os ficheiros de saída são colocados na pasta **ASALocalRun** do seu percurso de projeto. Também pode abrir os ficheiros de saída clicando no botão **Desembaraçar resultados** abertos na janela de resultados de execução local.

   ![Azure Stream Analytics Visual Studio imagem de dados abertos resultados](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. Se quiser obter os resultados para os lavatórios de saída da nuvem, escolha **a Saída para Cloud** a partir da segunda caixa de entrega. O armazenamento de power BI e Azure Data Lake Não são lavatórios de saída suportados.

   ![Azure Stream Analytics Visual Studio live data output to cloud](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>Limitações

* O Armazenamento do Lago de Dados de Energia e Azure não são afundados suportados devido a limitações do modelo de autenticação.

* Apenas as opções de entrada na nuvem têm suporte às políticas de [tempo,](stream-analytics-out-of-order-and-late-events.md) enquanto as opções de entrada local não.

## <a name="next-steps"></a>Passos seguintes

* [Crie um trabalho stream analytics utilizando as ferramentas Azure Stream Analytics para o Visual Studio](stream-analytics-quick-create-vs.md)
* [Teste Stream Analytics consultas localmente com Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Use o Estúdio Visual para ver os trabalhos do Azure Stream Analytics](stream-analytics-vs-tools.md)
