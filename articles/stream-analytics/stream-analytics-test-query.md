---
title: Testar um trabalho de Azure Stream Analytics com dados de exemplo
description: Este artigo descreve como usar o portal do Azure para testar um trabalho de Azure Stream Analytics, entrada de exemplo e carregar dados de exemplo.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/23/2019
ms.custom: seodec18
ms.openlocfilehash: d68c1ee084efc146d69d8404aadb8acbb4cc33d1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73891759"
---
# <a name="test-an-azure-stream-analytics-job-with-sample-data"></a>Testar um trabalho de Azure Stream Analytics com dados de exemplo

No Azure Stream Analytics, você pode testar sua consulta sem iniciar ou parar seu trabalho. Você pode testar consultas em dados de entrada de seu coletor de entrada ou carregar dados de exemplo de um arquivo local no portal do Azure. Você também pode testar as consultas localmente de seus dados de exemplo locais ou dados dinâmicos no [Visual Studio](stream-analytics-live-data-local-testing.md) e [Visual Studio Code](vscode-local-run.md). 

## <a name="sample-incoming-data-from-input"></a>Exemplo de dados de entrada de entrada

Azure Stream Analytics busca automaticamente eventos de sua entrada de streaming. Você pode executar consultas no exemplo padrão ou definir um intervalo de tempo específico para o exemplo.

1. Inicie sessão no Portal do Azure.

2. Localize e selecione seu trabalho de Stream Analytics existente.

3. Na página Stream Analytics trabalho, no cabeçalho **topologia do trabalho** , selecione **consulta** para abrir a janela do editor de consultas. 

4. Para ver uma lista de exemplos de eventos de entrada, selecione o ícone entrada com arquivo e os eventos de exemplo serão exibidos automaticamente na **visualização de entrada**. 

   a. O tipo de serialização para seus dados será detectado automaticamente se seu JSON ou CSV. Você pode alterá-lo manualmente, bem como JSON, CSV, AVRO alterando a opção no menu suspenso.
    
   b. Use o seletor para exibir seus dados em formato de **tabela** ou **bruto** .
    
   c. Se os dados mostrados não estiverem atuais, selecione **Atualizar** para ver os eventos mais recentes.

   A tabela a seguir é um exemplo de dados no **formato de tabela**:

   ![Azure Stream Analytics entrada de exemplo no formato de tabela](./media/stream-analytics-test-query/asa-sample-table.png)

   A tabela a seguir é um exemplo de dados no **formato bruto**:

   ![Azure Stream Analytics entrada de exemplo no formato bruto](./media/stream-analytics-test-query/asa-sample-raw.png)

5. Para testar sua consulta com os dados de entrada, selecione **testar consulta**. Os resultados aparecem na guia **resultados do teste** . Você também pode selecionar **baixar resultados** para baixar os resultados.

   ![Azure Stream Analytics resultados da consulta de teste de exemplo](./media/stream-analytics-test-query/asa-test-query.png)

6. Para testar sua consulta em um intervalo de tempo específico de eventos de entrada, selecione **selecionar intervalo de tempo**.
   
   ![Azure Stream Analytics intervalo de tempo para eventos de exemplo de entrada](./media/stream-analytics-test-query/asa-select-time-range.png)

7. Defina o intervalo de tempo dos eventos que você deseja usar para testar sua consulta e selecione **exemplo**. Dentro desse período, você pode recuperar até 1000 eventos ou 1 MB, o que vier primeiro.

   ![Azure Stream Analytics definir intervalo de tempo para eventos de exemplo de entrada](./media/stream-analytics-test-query/asa-set-time-range.png)

8. Depois que os eventos são amostrados para o intervalo de tempo selecionado, eles aparecem na guia **visualização de entrada** .

   ![Azure Stream Analytics exibir resultados de teste](./media/stream-analytics-test-query/asa-view-test-results.png)

9. Selecione **Redefinir** para ver a lista de exemplos de eventos de entrada. Se você selecionar **Redefinir**, sua seleção de intervalo de tempo será perdida. Selecione **testar consulta** para testar sua consulta e examine os resultados na guia **resultados do teste** .

10. Ao fazer alterações em sua consulta, selecione **Salvar consulta** para testar a nova lógica de consulta. Isso permite que você modifique a consulta iterativamente e teste-a novamente para ver como a saída é alterada.

11. Depois de verificar os resultados mostrados no navegador, você estará pronto para **Iniciar** o trabalho.

## <a name="upload-sample-data-from-a-local-file"></a>Carregar dados de exemplo de um arquivo local

Em vez de usar dados dinâmicos, você pode usar dados de exemplo de um arquivo local para testar sua consulta de Azure Stream Analytics.

1. Inicie sessão no Portal do Azure.
   
2. Localize o trabalho de Stream Analytics existente e selecione-o.

3. Na página Stream Analytics trabalho, no cabeçalho **topologia do trabalho** , selecione **consulta** para abrir a janela do editor de consultas.

4. Para testar sua consulta com um arquivo local, selecione **carregar entrada de exemplo** na guia **visualização de entrada** . 

   ![Azure Stream Analytics carregar arquivo de exemplo](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. Carregue seu arquivo local para testar a consulta. Você só pode carregar arquivos com os formatos JSON, CSV ou AVRO. Selecione **OK**.

   ![Azure Stream Analytics carregar arquivo de exemplo](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. Assim que você carregar o arquivo, também poderá ver o conteúdo do arquivo no formato como uma tabela ou no formato bruto. Se você selecionar **Redefinir**, os dados de exemplo serão retornados para os dados de entrada de entrada explicados na seção anterior. Você pode carregar qualquer outro arquivo para testar a consulta a qualquer momento.

7. Selecione **testar consulta** para testar sua consulta em relação ao arquivo de exemplo carregado.

8. Os resultados de teste são mostrados com base em sua consulta. Você pode alterar a consulta e selecionar **Salvar consulta** para testar a nova lógica de consulta. Isso permite que você modifique a consulta iterativamente e teste-a novamente para ver como a saída é alterada.

9. Quando você usa várias saídas na consulta, os resultados são mostrados com base na saída selecionada. 

   ![Azure Stream Analytics saída selecionada](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. Depois de verificar os resultados mostrados no navegador, você pode **Iniciar** o trabalho.

## <a name="next-steps"></a>Passos seguintes

* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

* [Exemplos de consulta para padrões comuns de uso de Stream Analytics](stream-analytics-stream-analytics-query-patterns.md)

* [Entender as entradas para Azure Stream Analytics](stream-analytics-add-inputs.md)

* [Entender as saídas de Azure Stream Analytics](stream-analytics-define-outputs.md)