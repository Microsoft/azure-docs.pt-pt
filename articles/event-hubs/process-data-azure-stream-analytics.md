---
title: Processar dados do Event Hubs Azure utilizando o Stream Analytics | Microsoft Docs
description: Este artigo mostra-lhe como processar dados do seu centro de eventos Azure usando um trabalho Azure Stream Analytics.
ms.date: 06/23/2020
ms.topic: article
ms.openlocfilehash: 2db4fba59a1a06a24ee2939c51ecdf65aa06cef3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85312894"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Processar dados do seu centro de eventos usando a Azure Stream Analytics 
O serviço Azure Stream Analytics facilita a ingestão, processamento e análise de dados de streaming a partir de Azure Event Hubs, permitindo insights poderosos para impulsionar ações em tempo real. Esta integração permite-lhe criar rapidamente um oleoduto de análise de caminhos quentes. Pode utilizar o portal Azure para visualizar os dados de entrada e escrever uma consulta stream Analytics. Uma vez que a sua consulta esteja pronta, pode movê-la para a produção em apenas alguns cliques. 

## <a name="key-benefits"></a>Principais vantagens
Aqui estão os principais benefícios da integração Azure Event Hubs e Azure Stream Analytics: 
- **Dados de pré-visualização** – Pode visualizar dados de entrada a partir de um centro de eventos no portal Azure.
- **Teste a sua consulta** – Prepare uma consulta de transformação e teste-a diretamente no portal Azure. Para a sintaxe da linguagem de consulta, consulte a documentação [da linguagem de consulta stream Analytics.](/stream-analytics-query/built-in-functions-azure-stream-analytics)
- **Implemente a sua consulta para a produção** – Pode implementar a consulta na produção, criando e iniciando um trabalho Azure Stream Analytics.

## <a name="end-to-end-flow"></a>Fluxo de ponta a ponta

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
1. Navegue para o seu **espaço de nomes Event Hubs** e, em seguida, navegue para o centro de **eventos,** que tem os dados de entrada. 
1. Selecione **dados de processo** na página do centro do evento.  

    ![Azulejo de dados do processo](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. **Selecione Explore** no **Enable introspeções em tempo real de eventos** em azulejo. 

    ![Selecione Stream Analytics](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. Você vê uma página de consulta com valores já definidos para os seguintes campos:
    1. O seu **centro de eventos** como entrada para a consulta.
    1. Experimente **a consulta SQL** com a declaração SELECT. 
    1. Um pseudónimo de **saída** para consultar os resultados dos seus testes de consulta. 

        ![Editor de consultas](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  Quando utiliza esta funcionalidade pela primeira vez, esta página pede a sua permissão para criar um grupo de consumidores e uma política para o seu centro de eventos para pré-visualizar os dados recebidos.
1. **Selecione Criar** no painel **de pré-visualização de entrada,** como mostrado na imagem anterior. 
1. Verá imediatamente uma imagem instantânea dos dados mais recentes neste separador.
    - O tipo de serialização dos seus dados é detetado automaticamente (JSON/CSV). Pode alterá-lo manualmente também para JSON/CSV/AVRO.
    - Pode pré-visualizar dados de entrada no formato de tabela ou em formato bruto. 
    - Se os seus dados mostrados não estiverem atuais, **selecione Refresh** para ver os eventos mais recentes. 

        Aqui está um exemplo de dados no **formato** tabela :   ![ Resultados no formato tabela](./media/process-data-azure-stream-analytics/snapshot-results.png)

        Aqui está um exemplo de dados no **formato bruto:** 

        ![Resultados no formato bruto](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. Selecione **a consulta de teste** para ver a imagem dos resultados do teste da sua consulta no separador resultados do **Teste.** Também pode baixar os resultados.

    ![Resultados da consulta de teste](./media/process-data-azure-stream-analytics/test-results.png)
1. Escreva a sua própria consulta para transformar os dados. Consulte [a referência de linguagem de consulta stream analytics](/stream-analytics-query/stream-analytics-query-language-reference).
1. Depois de testar a consulta e pretende movê-la para a produção, selecione **Implementar consulta**. Para implementar a consulta, crie um trabalho Azure Stream Analytics onde pode definir uma saída para o seu trabalho e iniciar o trabalho. Para criar um trabalho stream Analytics, especifique um nome para o trabalho e selecione **Criar**.

      ![Criar uma tarefa do Azure Stream Analytics](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  Recomendamos que crie um grupo de consumidores e uma política para cada novo trabalho Azure Stream Analytics que crie a partir da página de Centros de Eventos. Os grupos de consumidores permitem apenas cinco leitores simultâneos, pelo que o fornecimento de um grupo de consumidores dedicado para cada trabalho evitará quaisquer erros que possam surgir de exceder esse limite. Uma política dedicada permite-lhe rodar a sua chave ou revogar permissões sem afetar outros recursos. 
1. O seu trabalho stream Analytics é agora criado onde a sua consulta é a mesma que testou, e a entrada é o seu centro de eventos. 

9.  Para completar o pipeline, desacione a **saída** da consulta e selecione **Comece** a iniciar o trabalho.

    > [!NOTE]
    > Antes de iniciar o trabalho, não se esqueça de substituir as outputalias pelo nome de saída que criou no Azure Stream Analytics.

      ![Definir saída e iniciar o trabalho](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>Limitações conhecidas
Ao testar a sua consulta, os resultados dos testes demoram aproximadamente 6 segundos a carregar. Estamos a trabalhar para melhorar o desempenho dos testes. No entanto, quando implementado na produção, a Azure Stream Analytics terá latência de subsegundo.

## <a name="streaming-units"></a>Unidades de transmissão em fluxo
O seu trabalho Azure Stream Analytics falha em três unidades de streaming (SUs). Para ajustar esta definição, selecione **Escala** no menu esquerdo na página de **trabalho Stream Analytics** no portal Azure. Para saber mais sobre as unidades de streaming, consulte [Compreender e ajustar Unidades de Streaming.](../stream-analytics/stream-analytics-streaming-unit-consumption.md)

![Unidades de streaming de escala](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre consultas stream analytics, consulte [Stream Analytics Query Language](/stream-analytics-query/built-in-functions-azure-stream-analytics)
