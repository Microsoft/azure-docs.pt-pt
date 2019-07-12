---
title: Processar dados do Azure de Hubs de eventos com o Stream Analytics | Documentos da Microsoft
description: Este artigo mostra-lhe como processar dados do seu hub de eventos do Azure com uma tarefa do Azure Stream Analytics.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 07/09/2019
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: f179687b0983e145244e228a3d3b06b4eabead48
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723418"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Processar dados do seu hub de eventos com o Azure Stream Analytics
O serviço Azure Stream Analytics facilita a ingestão, processo e analisar dados de transmissão em fluxo dos Hubs de eventos do Azure, permitindo que informações importantes para originar ações em tempo real. Esta integração permite-lhe criar rapidamente um pipeline de análise de caminhos recorrentes. Pode utilizar o portal do Azure para visualizar dados de entrada e escrever uma consulta do Stream Analytics. Assim que a consulta estiver pronta, pode movê-lo em produção em apenas alguns cliques. 

## <a name="key-benefits"></a>Principais vantagens
Aqui estão as principais vantagens da integração do Event Hubs do Azure e o Azure Stream Analytics: 
- **Pré-visualizar dados** – pode visualizar os dados recebidos de um hub de eventos no portal do Azure.
- **Testar a sua consulta** – preparar uma consulta de transformação e testá-lo diretamente no portal do Azure. Para a sintaxe de linguagem de consulta, consulte [linguagem de consulta do Stream Analytics](/stream-analytics-query/built-in-functions-azure-stream-analytics) documentação.
- **Implementar a sua consulta na produção** – pode implementar a consulta em produção através da criação e a partir de uma tarefa do Azure Stream Analytics.

## <a name="end-to-end-flow"></a>Fluxo de ponto a ponto

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
1. Navegue até à sua **espaço de nomes de Hubs de eventos** e, em seguida, navegue para o **hub de eventos**, que tem os dados de entrada. 
1. Selecione **processar dados** na página de hub de eventos.  

    ![Mosaico de dados do processo](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. Selecione **explorar** sobre o **ativar informações em tempo real dos eventos** mosaico. 

    ![Selecione o Stream Analytics](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. É apresentada uma página de consulta com valores já está definidos para os seguintes campos:
    1. Sua **hub de eventos** como entrada para a consulta.
    1. Exemplo **consulta SQL** com a instrução SELECT. 
    1. Uma **saída** alias consultar os resultados do teste. 

        ![Editor de consultas](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  Quando usar esse recurso pela primeira vez, esta página pede sua permissão para criar um grupo de consumidores e uma política para o seu hub de eventos para pré-visualizar os dados de entrada.
1. Selecione **Create** no **pré-visualização de entrada** painel, conforme mostrado na imagem anterior. 
1. Verá imediatamente um instantâneo dos dados de entrada mais recente neste separador.
    - O tipo de serialização nos seus dados é automaticamente detetado (JSON/CSV). Manualmente pode alterá-lo também para JSON/CSV/AVRO.
    - Pode visualizar os dados recebidos no formato de tabela ou formato não processado. 
    - Se seus dados não for atuais, selecione **atualizar** para ver os eventos mais recentes. 

        Eis um exemplo de dados do **formato de tabela**:   ![Resultados em formato de tabela](./media/process-data-azure-stream-analytics/snapshot-results.png)

        Eis um exemplo de dados do **formato não processado**: 

        ![Resultados no formato não processado](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. Selecione **consulta de teste** para ver o instantâneo dos resultados de teste da sua consulta na **resultados do teste** separador. Também pode transferir os resultados.

    ![Testar os resultados da consulta](./media/process-data-azure-stream-analytics/test-results.png)
1. Escreva sua própria consulta para transformar os dados. Ver [referência de linguagem de consulta do Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference).
1. Assim que tiver testado a consulta e pretende movê-lo produção, selecione **Deploy consulta**. Para implementar a consulta, crie uma tarefa Azure Stream Analytics, onde pode definir uma saída para a sua tarefa e iniciar a tarefa. Para criar uma tarefa do Stream Analytics, especifique um nome para a tarefa e selecione **criar**.

      ![Criar uma tarefa do Azure Stream Analytics](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  Recomendamos que crie um grupo de consumidores e uma política para cada nova tarefa do Azure Stream Analytics que criou a partir da página de Hubs de eventos. Grupos de consumidores permitem apenas cinco de leitores simultâneos, para que o fornecimento de um grupo de consumidores dedicado para cada tarefa evitará quaisquer erros que possam surgir de exceder esse limite. Uma política de tipo dedicado permite-lhe rodar a chave ou revogar permissões sem afetar outros recursos. 
1. A tarefa de Stream Analytics está agora criada em que a consulta é o mesmo que testado, e a entrada é o seu hub de eventos. 

9.  Para concluir o pipeline, defina o **saída** da consulta e selecione **iniciar** para iniciar a tarefa.

    > [!NOTE]
    > Antes de iniciar a tarefa, não se esqueça de substituir o outputalias com o nome de saída que criou no Azure Stream Analytics.

      ![Definir a saída e iniciar a tarefa](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>Limitações conhecidas
Ao testar a sua consulta, os resultados do teste demoram aproximadamente 6 segundos para carregar. Estamos a melhorar o desempenho do teste. No entanto, quando implementado na produção, o Azure Stream Analytics terão subsecond latência.

## <a name="streaming-units"></a>Unidades de transmissão em fluxo
O Azure Stream Analytics da tarefa a predefinição é três unidades de transmissão em fluxo (SUs). Para ajustar esta definição, selecione **escala** no menu da esquerda no **tarefa do Stream Analytics** página no portal do Azure. Para saber mais sobre unidades de transmissão em fluxo, veja [compreender e ajustar as unidades transmissão em fluxo](../stream-analytics/stream-analytics-streaming-unit-consumption.md).

![Dimensionamento de unidades de transmissão em fluxo](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre as consultas do Stream Analytics, consulte [linguagem de consulta do Stream Analytics](/stream-analytics-query/built-in-functions-azure-stream-analytics)