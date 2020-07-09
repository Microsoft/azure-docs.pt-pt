---
title: Teste um trabalho Azure Stream Analytics com dados de amostra
description: Este artigo descreve como usar o portal Azure para testar um trabalho do Azure Stream Analytics, entrada de amostra e upload de dados da amostra.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 3/6/2020
ms.custom: seodec18
ms.openlocfilehash: 04d86b0ef8202c1c544524dd2a7331d521245f12
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86043961"
---
# <a name="test-an-azure-stream-analytics-job-in-the-portal"></a>Teste um trabalho de Azure Stream Analytics no portal

No Azure Stream Analytics, pode testar a sua consulta sem iniciar ou parar o seu trabalho. Pode testar consultas sobre dados de entrada a partir das suas fontes de streaming ou enviar dados de amostras a partir de um ficheiro local no Portal Azure. Também pode testar consultas localmente a partir dos dados da amostra local ou dados ao vivo no [Visual Studio](stream-analytics-live-data-local-testing.md) e visual [Studio Code.](visual-studio-code-local-run-live-input.md)

## <a name="automatically-sample-incoming-data-from-input"></a>Recolher automaticamente dados de entrada a partir da entrada

O Azure Stream Analytics recolhe automaticamente eventos a partir das suas entradas de streaming. Pode executar consultas na amostra predefinida ou definir um prazo específico para a amostra.

1. Inicie sessão no Portal do Azure.

2. Localize e selecione o seu trabalho stream analytics existente.

3. Na página de trabalho stream Analytics, sob o título **Job Topology,** selecione **Consulta** para abrir a janela do editor de consulta. 

4. Para ver uma lista de amostras de eventos de entrada, selecione a entrada com o ícone do ficheiro e os eventos da amostra aparecerão automaticamente na **pré-visualização de Entrada**.

   a. O tipo de serialização dos seus dados é automaticamente detetado se o seu JSON ou CSV. Pode alterá-lo manualmente também para JSON, CSV, AVRO alterando a opção no menu suspenso.
    
   b. Utilize o seletor para ver os seus dados no formato **Tabela** ou **Raw.**
    
   c. Se os seus dados mostrados não estiverem atuais, **selecione Refresh** para ver os eventos mais recentes.

   A tabela a seguir é um exemplo de dados no **formato quadro:**

   ![Entrada da amostra Azure Stream Analytics no formato de tabela](./media/stream-analytics-test-query/asa-sample-table.png)

   A tabela a seguir é um exemplo de dados no **formato Raw:**

   ![Entrada da amostra Azure Stream Analytics em formato bruto](./media/stream-analytics-test-query/asa-sample-raw.png)

5. Para testar a sua consulta com os dados de entrada, selecione **a consulta de teste**. Os resultados aparecem no separador resultados do **Teste.** Também pode selecionar **Baixar os resultados** para descarregar os resultados.

   ![Resultados da consulta da amostra Azure Stream Analytics](./media/stream-analytics-test-query/asa-test-query.png)

6. Para testar a sua consulta com um intervalo de tempo específico de eventos de entrada, **selecione Selecione o intervalo de tempo**.
   
   ![Intervalo de tempo Azure Stream Analytics para eventos de amostras de entrada](./media/stream-analytics-test-query/asa-select-time-range.png)

7. Desacione o intervalo de tempo dos eventos que pretende utilizar para testar a sua consulta e selecionar **a Amostra.** Dentro desse prazo, você pode recuperar até 1000 eventos ou 1 MB, o que vier primeiro.

   ![Azure Stream Analytics definiu intervalo de tempo para eventos de amostra de entrada](./media/stream-analytics-test-query/asa-set-time-range.png)

8. Uma vez que os eventos são amostrados para intervalo de tempo selecionado, eles aparecem no **separador de pré-visualização de Entrada.**

   ![Azure Stream Analytics visualizar resultados dos testes](./media/stream-analytics-test-query/asa-view-test-results.png)

9. Selecione **Reset** para ver a lista de amostras de eventos que chegam. Se selecionar **Reset,** a seleção do intervalo de tempo perder-se-á. Selecione **a consulta de teste** para testar a sua consulta e rever os resultados no separador resultados do **Teste.**

10. Quando escoar alterações na sua consulta, **selecione Guardar a consulta** para testar a nova lógica de consulta. Isto permite-lhe modificar iterativamente a sua consulta e testá-la novamente para ver como a saída muda.

11. Depois de verificar os resultados apresentados no navegador, está pronto para **iniciar** o trabalho.

## <a name="upload-sample-data-from-a-local-file"></a>Faça upload de dados de amostras a partir de um ficheiro local

Em vez de utilizar dados ao vivo, pode utilizar dados de amostras de um ficheiro local para testar a sua consulta Azure Stream Analytics.

1. Inicie sessão no Portal do Azure.
   
2. Localize o seu trabalho stream analytics existente e selecione-o.

3. Na página de trabalho stream Analytics, sob o título **Job Topology,** selecione **Consulta** para abrir a janela do editor de consulta.

4. Para testar a sua consulta com um ficheiro local, selecione **Enviar a informação** da amostra no **separador de pré-visualização de Entrada.** 

   ![Ficheiro de amostra de upload Azure Stream Analytics](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. Faça o upload do seu arquivo local para testar a consulta. Só é possível fazer o upload de ficheiros com os formatos JSON, CSV ou AVRO. Selecione **OK**.

   ![Ficheiro de amostra de upload Azure Stream Analytics](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. Assim que fizer o upload do ficheiro, também pode ver o conteúdo do ficheiro no formulário como uma tabela ou no seu formato cru. Se selecionar **Reset,** os dados da amostra voltarão aos dados de entrada de entrada explicados na secção anterior. Pode fazer o upload de qualquer outro ficheiro para testar a consulta a qualquer momento.

7. Selecione **a consulta de teste** para testar a sua consulta com o ficheiro de amostras carregado.

8. Os resultados dos testes são apresentados com base na sua consulta. Pode alterar a sua consulta e selecionar **Guardar a consulta** para testar a nova lógica de consulta. Isto permite-lhe modificar iterativamente a sua consulta e testá-la novamente para ver como a saída muda.

9. Quando utiliza várias saídas na consulta, os resultados são apresentados com base na saída selecionada. 

   ![Azure Stream Analytics produção selecionada](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. Depois de verificar os resultados apresentados no navegador, pode **iniciar** o trabalho.

## <a name="next-steps"></a>Próximos passos
* [Construa uma solução IoT utilizando o Stream Analytics:](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics)este tutorial irá guiá-lo a construir uma solução de ponta a ponta com um gerador de dados que simulará o tráfego numa cabine de portagens.

* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

* [Exemplos de consulta para padrões comuns de utilização do Stream Analytics](stream-analytics-stream-analytics-query-patterns.md)

* [Compreenda as entradas para a Azure Stream Analytics](stream-analytics-add-inputs.md)

* [Compreender as saídas do Azure Stream Analytics](stream-analytics-define-outputs.md)
