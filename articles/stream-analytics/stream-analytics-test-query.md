---
title: Teste um trabalho de Análise de Fluxo Azure com dados de amostra
description: Este artigo descreve como usar o portal Azure para testar um trabalho de Azure Stream Analytics, a entrada de amostras e o upload de dados da amostra.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/6/2020
ms.custom: seodec18
ms.openlocfilehash: de2b99666dbed4eaec3db3e56800e9a7352e10e7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78898392"
---
# <a name="test-an-azure-stream-analytics-job-in-the-portal"></a>Teste um trabalho de Azure Stream Analytics no portal

No Azure Stream Analytics, pode testar a sua consulta sem iniciar ou parar o seu trabalho. Pode testar as consultas sobre os dados que chegam a partir das suas fontes de streaming ou fazer upload de dados de amostras a partir de um ficheiro local no Portal Do Azure. Também pode testar consultas localmente a partir dos dados da sua amostra local ou dados ao vivo no [Visual Studio](stream-analytics-live-data-local-testing.md) e Visual [Studio Code](visual-studio-code-local-run-live-input.md).

## <a name="automatically-sample-incoming-data-from-input"></a>Amostraautomaticamente dos dados de entrada a partir da entrada

O Azure Stream Analytics recolhe automaticamente eventos das suas inputs de streaming. Pode executar consultas na amostra predefinida ou definir um prazo específico para a amostra.

1. Inicie sessão no Portal do Azure.

2. Localize e selecione o seu trabalho existente em Streaming Analytics.

3. Na página de trabalho do Stream Analytics, sob o título **Job Topology,** selecione **Consulta** para abrir a janela do editor da Consulta. 

4. Para ver uma lista de eventos de entrada, selecione a entrada com ícone de ficheiro e os eventos da amostra aparecerão automaticamente na **pré-visualização**de Entrada .

   a. O tipo de serialização dos seus dados é automaticamente detetado se o seu JSON ou CSV. Pode mudá-lo manualmente também para JSON, CSV, AVRO alterando a opção no menu dropdown.
    
   b. Utilize o seletor para visualizar os seus dados em **formato Tabela** ou **Cru.**
    
   c. Se os seus dados mostrados não estiverem atuais, selecione **Refresh** para ver os eventos mais recentes.

   O quadro seguinte é um exemplo de dados no **formato tabela:**

   ![Entrada da amostra Azure Stream Analytics em formato de mesa](./media/stream-analytics-test-query/asa-sample-table.png)

   O quadro seguinte é um exemplo de dados no **formato Raw:**

   ![Entrada da amostra Azure Stream Analytics em formato cru](./media/stream-analytics-test-query/asa-sample-raw.png)

5. Para testar a sua consulta com os dados de entrada, selecione **test consulta**. Os resultados aparecem no separador **de resultados** do Teste. Também pode selecionar **os resultados do Download** para descarregar os resultados.

   ![Resultados da consulta de teste da amostra Azure Stream Analytics](./media/stream-analytics-test-query/asa-test-query.png)

6. Para testar a sua consulta contra uma gama de tempo específica de eventos de entrada, **selecione Select intervalo**de tempo .
   
   ![Gama de tempo do Azure Stream Analytics para eventos de amostras de entrada](./media/stream-analytics-test-query/asa-select-time-range.png)

7. Detete a gama de tempo dos eventos que pretende utilizar para testar a sua consulta e selecionar **Sample**. Dentro desse prazo, pode recuperar até 1000 eventos ou 1 MB, o que vier em primeiro lugar.

   ![Azure Stream Analytics estabelece intervalo de tempo para eventos de amostras de entrada](./media/stream-analytics-test-query/asa-set-time-range.png)

8. Uma vez que os eventos são amostrados para o intervalo de tempo selecionado, eles aparecem no separador **de pré-visualização de entrada.**

   ![Resultados dos testes de visualização do Azure Stream Analytics](./media/stream-analytics-test-query/asa-view-test-results.png)

9. Selecione **Reset** para ver a lista de amostras de eventos que estão a chegar. Se selecionar **Reset,** perder-se-á a seleção do intervalo de tempo. Selecione **a consulta** de teste para testar a sua consulta e reveja os resultados no separador de resultados do **Teste.**

10. Quando fizer alterações na sua consulta, selecione **Guardar consulta** para testar a nova lógica de consulta. Isto permite modificar iterativamente a sua consulta e testá-la novamente para ver como a saída muda.

11. Depois de verificar os resultados mostrados no navegador, está pronto para **iniciar** o trabalho.

## <a name="upload-sample-data-from-a-local-file"></a>Upload de dados da amostra de um ficheiro local

Em vez de utilizar dados ao vivo, pode utilizar dados de amostra saqueados de um ficheiro local para testar a sua consulta azure Stream Analytics.

1. Inicie sessão no Portal do Azure.
   
2. Localize o seu trabalho existente no Stream Analytics e selecione-o.

3. Na página de trabalho do Stream Analytics, sob o título **Job Topology,** selecione **Consulta** para abrir a janela do editor da Consulta.

4. Para testar a sua consulta com um ficheiro local, selecione a entrada da **amostra de upload** no separador de **pré-visualização de entrada.** 

   ![Ficheiro de amostra de upload do Azure Stream Analytics](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. Faça upload do seu ficheiro local para testar a consulta. Só é possível fazer o upload de ficheiros com os formatos JSON, CSV ou AVRO. Selecione **OK**.

   ![Ficheiro de amostra de upload do Azure Stream Analytics](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. Assim que fizer o upload do ficheiro, também pode ver o conteúdo do ficheiro no formulário como uma tabela ou no seu formato bruto. Se selecionar **Reset,** os dados da amostra voltarão aos dados de entrada explicados na secção anterior. Pode fazer o upload de qualquer outro ficheiro para testar a consulta a qualquer momento.

7. Selecione **a consulta** de teste para testar a sua consulta com o ficheiro de amostra seletiva.

8. Os resultados dos testes são mostrados com base na sua consulta. Pode alterar a sua consulta e selecionar **Guardar a consulta** para testar a nova lógica de consulta. Isto permite modificar iterativamente a sua consulta e testá-la novamente para ver como a saída muda.

9. Quando utiliza várias saídas na consulta, os resultados são apresentados com base na saída selecionada. 

   ![Saída selecionada do Azure Stream Analytics](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. Depois de verificar os resultados mostrados no navegador, pode **iniciar** o trabalho.

## <a name="next-steps"></a>Passos seguintes
* [Construa uma solução IoT utilizando](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics)o Stream Analytics : este tutorial irá guiá-lo para construir uma solução de ponta a ponta com um gerador de dados que simulará tráfego numa cabine de portagem.

* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

* [Exemplos de consulta para padrões comuns de utilização do Stream Analytics](stream-analytics-stream-analytics-query-patterns.md)

* [Compreender as inputs para azure stream analytics](stream-analytics-add-inputs.md)

* [Compreender as saídas do Azure Stream Analytics](stream-analytics-define-outputs.md)
