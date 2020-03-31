---
title: Processar dados do Event Hubs Azure utilizando o Stream Analytics Microsoft Docs
description: Este artigo mostra-lhe como processar dados do seu centro de eventos Azure usando um trabalho de Azure Stream Analytics.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 07/09/2019
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: 531426656fe833752c9c4685688c00de3894895b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "69991936"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Processe dados do seu centro de eventos usando o Azure Stream Analytics 
O serviço Azure Stream Analytics facilita a ingestão, processo e análise de dados de streaming dos Hubs de Eventos Azure, permitindo insights poderosos para impulsionar ações em tempo real. Esta integração permite-lhe criar rapidamente um pipeline de análise de caminho quente. Pode utilizar o portal Azure para visualizar os dados de entrada e escrever uma consulta de Stream Analytics. Uma vez que a sua consulta esteja pronta, pode movê-la para produção em apenas alguns cliques. 

## <a name="key-benefits"></a>Principais vantagens
Aqui estão os principais benefícios dos Hubs de Eventos Azure e da integração azure Stream Analytics: 
- **Dados de pré-visualização** – Pode pré-visualizar os dados de entrada a partir de um hub de eventos no portal Azure.
- **Teste a sua consulta** – Prepare uma consulta de transformação e teste-a diretamente no portal Azure. Para a sintaxe de linguagem consulta, consulte a documentação da Linguagem consulta da [Stream Analytics.](/stream-analytics-query/built-in-functions-azure-stream-analytics)
- **Implemente a sua consulta para a produção** – Pode implementar a consulta na produção criando e iniciando um trabalho de Azure Stream Analytics.

## <a name="end-to-end-flow"></a>Fluxo de ponta a ponta

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 
1. Navegue para o espaço de nome do Seu **Event Hubs** e navegue depois para o centro de **eventos,** que tem os dados de entrada. 
1. Selecione Dados de **Processo** na página do centro do evento.  

    ![Processar azulejos de dados](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. **Selecione Explore** no **Enable insights em tempo real a partir de azulejos de eventos.** 

    ![Selecione Stream Analytics](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. Você vê uma página de consulta com valores já definidos para os seguintes campos:
    1. O seu centro de **eventos** como entrada para a consulta.
    1. Prove **consulta SQL** com declaração SELECT. 
    1. Um pseudónimo de **saída** para se referir aos resultados dos seus testes de consulta. 

        ![Editor de consultas](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  Quando utiliza esta funcionalidade pela primeira vez, esta página pede a sua permissão para criar um grupo de consumidores e uma política para o seu centro de eventos para visualizar os dados de entrada.
1. Selecione **Criar** no painel de **pré-visualização** de entrada, como mostrado na imagem anterior. 
1. Verá imediatamente uma foto dos últimos dados que chegam neste separador.
    - O tipo de serialização dos seus dados é automaticamente detetado (JSON/CSV). Pode mudá-lo manualmente também para JSON/CSV/AVRO.
    - Pode pré-visualizar os dados de entrada no formato de tabela ou no formato bruto. 
    - Se os seus dados mostrados não estiverem atuais, selecione **Refresh** para ver os eventos mais recentes. 

        Aqui está um exemplo de dados ![no **formato de tabela**: Resultados no formato de tabela](./media/process-data-azure-stream-analytics/snapshot-results.png)

        Aqui está um exemplo de dados no **formato bruto:** 

        ![Resultados no formato bruto](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. Selecione **a consulta** de teste para ver a imagem dos resultados do teste da sua consulta no separador de resultados do **Teste.** Também pode descarregar os resultados.

    ![Resultados da consulta de teste](./media/process-data-azure-stream-analytics/test-results.png)
1. Escreva a sua própria consulta para transformar os dados. Consulte a referência da [linguagem de consulta de análise do stream analytics](/stream-analytics-query/stream-analytics-query-language-reference).
1. Depois de testar a consulta e pretender movê-la para a produção, selecione A consulta de **Implementação**. Para implementar a consulta, crie um trabalho de Azure Stream Analytics onde possa definir uma saída para o seu trabalho e iniciar o trabalho. Para criar um trabalho de Stream Analytics, especifique um nome para o trabalho e selecione **Criar**.

      ![Criar uma tarefa do Azure Stream Analytics](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  Recomendamos que crie um grupo de consumidores e uma política para cada novo trabalho do Azure Stream Analytics que cria a partir da página do Event Hubs. Os grupos de consumidores permitem apenas cinco leitores simultâneos, pelo que a disponibilização de um grupo de consumidores dedicado para cada trabalho evitará quaisquer erros que possam resultar de exceder esse limite. Uma política dedicada permite-lhe rodar a sua chave ou revogar permissões sem afetar outros recursos. 
1. O seu trabalho no Stream Analytics é agora criado onde a sua consulta é a mesma que testou, e a entrada é o seu centro de eventos. 

9.  Para completar o gasoduto, detete a **saída** da consulta e selecione **Iniciar** o trabalho.

    > [!NOTE]
    > Antes de iniciar o trabalho, não se esqueça de substituir as outputalias pelo nome de saída que criou no Azure Stream Analytics.

      ![Definir a saída e iniciar o trabalho](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>Limitações conhecidas
Enquanto testa a sua consulta, os resultados dos testes demoram aproximadamente 6 segundos a carregar. Estamos a trabalhar para melhorar o desempenho dos testes. No entanto, quando implantado em produção, o Azure Stream Analytics terá latência sub-segundo.

## <a name="streaming-units"></a>Unidades de transmissão em fluxo
O seu trabalho no Azure Stream Analytics não se aplica a três unidades de streaming (UsS). Para ajustar esta definição, selecione **Escala** no menu esquerdo na página **de trabalho stream Analytics** no portal Azure. Para saber mais sobre unidades de streaming, consulte [Compreender e ajustar unidades](../stream-analytics/stream-analytics-streaming-unit-consumption.md)de streaming.

![Unidades de streaming de escala](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre consultas de Stream Analytics, consulte [stream analytics consulta language](/stream-analytics-query/built-in-functions-azure-stream-analytics)
