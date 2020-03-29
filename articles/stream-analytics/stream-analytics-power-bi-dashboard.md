---
title: Integração do painel power BI com o Azure Stream Analytics
description: Este artigo descreve como usar um dashboard Power BI em tempo real para visualizar dados de um trabalho de Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2019
ms.openlocfilehash: 8466fbcb4325dc244551a3b84fc20581366b7071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851161"
---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics e Power BI: Um dashboard de análise em tempo real para dados de streaming

O Azure Stream Analytics permite-lhe tirar partido de uma das principais ferramentas de inteligência empresarial, o [Microsoft Power BI](https://powerbi.com/). Neste artigo, aprende-se a criar ferramentas de inteligência empresarial utilizando o Power BI como uma saída para os seus trabalhos no Azure Stream Analytics. Também aprende saquea do painel de instrumentos em tempo real.

Este artigo continua a partir do tutorial de [deteção de fraude](stream-analytics-real-time-fraud-detection.md) em tempo real stream analytics. Baseia-se no fluxo de trabalho criado nesse tutorial e adiciona uma saída power BI para que possa visualizar chamadas telefónicas fraudulentas que são detetadas por um trabalho de Streaming Analytics. 

Pode ver [um vídeo](https://www.youtube.com/watch?v=SGUpT-a99MA) que ilustra este cenário.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem o seguinte:

* Uma conta do Azure.
* Uma conta para o Power BI Pro. Pode usar uma conta de trabalho ou uma conta escolar.
* Uma versão completa do tutorial [de deteção de fraudeem em tempo real.](stream-analytics-real-time-fraud-detection.md) O tutorial inclui uma aplicação que gera metadados fictícios de chamadas telefónicas. No tutorial, você cria um hub de eventos e envia os dados de chamadas de streaming para o centro do evento. Escreve uma consulta que deteta chamadas fraudulentas (chamadas do mesmo número ao mesmo tempo em diferentes locais). 


## <a name="add-power-bi-output"></a>Adicionar saída power BI
No tutorial de deteção de fraudes em tempo real, a saída é enviada para o armazenamento da Blob Azure. Nesta secção, adicione uma saída que envia informações para o Power BI.

1. No portal Azure, abra o trabalho de Streaming Analytics que criou anteriormente. Se usou o nome sugerido, o `sa_frauddetection_job_demo`trabalho chama-se.

2. No menu esquerdo, selecione **Saídas** sob **topologia de trabalho**. Em seguida, selecione **+ Adicione** e escolha **Power BI** a partir do menu dropdown.

3. Selecione **+ Adicionar** > **Power BI**. Em seguida, preencha o formulário com os seguintes detalhes e selecione **Autorizar**:

   |**Definição**  |**Valor sugerido**  |
   |---------|---------|
   |Alias de saída  |  CallStream-PowerBI  |
   |Nome do conjunto de dados  |   conjunto de dados sa  |
   |Nome da tabela |  chamadas fraudulentas  |

   ![Configure stream analytics saída](media/stream-analytics-power-bi-dashboard/configure-stream-analytics-output.png)

   > [!WARNING]
   > Se o Power BI tiver um conjunto de dados e uma tabela que tenham os mesmos nomes que especifica no trabalho do Stream Analytics, os existentes são substituídos.
   > Recomendamos que não crie explicitamente este conjunto de dados e tabela na sua conta Power BI. São automaticamente criados quando inicias o teu trabalho de Stream Analytics e o trabalho começa a bombear a produção para o Power BI. Se a sua consulta de trabalho não devolver quaisquer resultados, o conjunto de dados e a tabela não são criados.
   >

4. Quando seleciona **Autorizar**, uma janela de pop-up abre e é-lhe pedido que forneça credenciais para fazer a autenticação na sua conta do Power BI. Assim que a autorização for bem sucedida, **guarde** as definições.

8. Clique em **Criar**.

O conjunto de dados é criado com as seguintes definições:

* política de **predefinição: BasicFIFO** - Os dados são FIFO, com um máximo de 200.000 linhas.
* **defaultMode: pushStreaming** - O conjunto de dados suporta tanto os azulejos de streaming como os visuais tradicionais baseados em relatórios (também conhecidos como push).

Atualmente, não é possível criar conjuntos de dados com outras bandeiras.

Para mais informações sobre os conjuntos de dados power BI, consulte a referência [power bi REST API.](https://msdn.microsoft.com/library/mt203562.aspx)


## <a name="write-the-query"></a>Escreva a consulta

1. Feche a lâmina **de saída** e volte à lâmina de trabalho.

2. Clique na caixa **de consulta.** 

3. Introduza a seguinte consulta. Esta consulta é semelhante à consulta de auto-join que criou no tutorial de deteção de fraudes. A diferença é que esta consulta envia resultados`CallStream-PowerBI`para a nova saída que criou ( ). 

    >[!NOTE]
    >Se não nomeou a `CallStream` entrada no tutorial de deteção `CallStream` de fraudes, substitua o seu nome nas cláusulas **FROM** e **JOIN** na consulta.

   ```SQL
   /* Our criteria for fraud:
   Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "CallStream-PowerBI"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime

   /* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
   ON CS1.CallingIMSI = CS2.CallingIMSI

   /* ...and date between CS1 and CS2 is between one and five seconds */
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

   /* Where the switch location is different */
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

4. Clique em **Guardar**.


## <a name="test-the-query"></a>Testar o modelo

Esta secção é opcional, mas recomendada. 

1. Se a aplicação TelcoStreaming não estiver atualmente em execução, inicie-a seguindo estes passos:

    * Aviso de comando aberto.
    * Vá para a pasta onde estão os ficheiros telcogenerator.exe e telcodatagen.exe.config modificados.
    * Execute o seguinte comando:

       `telcodatagen.exe 1000 .2 2`

2. Na página **Consulta** para o seu trabalho de Streaming Analytics, clique nos pontos ao lado da `CallStream` entrada e, em seguida, selecione dados da amostra a partir da **entrada**.

3. Especifique que deseja três minutos de dados e clique em **OK**. Aguarde até receber uma notificação a indicar que foi criada uma amostra dos dados.

4. Clique em **Testar** e reveja os resultados.

## <a name="run-the-job"></a>Executar a tarefa

1. Certifique-se de que a aplicação TelcoStreaming está em execução.

2. Navegue para a página **'Overview'** para o seu trabalho no Stream Analytics e selecione **Iniciar**.

    ![Inicie o trabalho de Stream Analytics](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

O seu trabalho de Streaming Analytics começa a procurar chamadas fraudulentas no fluxo de entrada. O trabalho também cria o conjunto de dados e a tabela no Power BI e começa a enviar dados sobre as chamadas fraudulentas para eles.


## <a name="create-the-dashboard-in-power-bi"></a>Crie o dashboard no Power BI

1. Vá para [Powerbi.com](https://powerbi.com) e inscreva-se com o seu trabalho ou conta escolar. Se os resultados da consulta de trabalho do Stream Analytics resultarem, verá que o seu conjunto de dados já está criado:

    ![Localização do conjunto de dados de streaming em Power BI](./media/stream-analytics-power-bi-dashboard/stream-analytics-streaming-dataset.png)

2. No seu espaço ** + &nbsp;** de trabalho, clique em Criar .

    ![O botão Criar no espaço de trabalho Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Crie um novo `Fraudulent Calls`painel de instrumentos e nomeie-o.

    ![Crie um dashboard e dê-lhe um nome no espaço de trabalho power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. Na parte superior da janela, clique em **adicionar azulejos,** selecione **DADOS DE STREAMING PERSONALIZADOs,** e depois clique **em Seguinte**.

    ![Azulejo de conjunto de dados de streaming personalizado em Power BI](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. Em seu **DATSETS,** selecione o seu conjunto de dados e, em seguida, clique **em Seguinte**.

    ![O seu conjunto de dados de streaming em Power BI](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. Em **Tipo de Visualização,** selecione **Cartão,** e depois na lista **Fields,** selecione **chamadas fraudulentas**.

    ![Detalhes da visualização de novos azulejos](./media/stream-analytics-power-bi-dashboard/add-fraudulent-calls-tile.png)

7. Clique em **Seguinte**.

8. Preencha detalhes de azulejos como um título e legenda.

    ![Título e legenda para novo azulejo](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Clique em **Aplicar**.

    Agora tem um contador de fraudes!

    ![Contador de fraude no painel power bi](./media/stream-analytics-power-bi-dashboard/power-bi-fraud-counter-tile.png)

8. Siga novamente os passos para adicionar um azulejo (começando com o passo 4). Desta vez, faça o seguinte:

    * Quando chegar ao Tipo de **Visualização,** selecione **gráfico de linha**. 
    * Adicione um eixo e selecione **windowend**. 
    * Adicione um valor e selecione **fraudulentcalls**.
    * Para **Janela de tempo a apresentar**, selecione os últimos 10 minutos.

      ![Crie azulejos para gráfico de linha em Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Clique **em Seguinte,** adicione um título e legenda, e clique **em Aplicar**.

     O dashboard Power BI dá-lhe agora duas visualizações de dados sobre chamadas fraudulentas, conforme detetado nos dados de streaming.

     ![Painel de instrumentos do Power BI acabado mostrando dois azulejos para chamadas fraudulentas](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)


## <a name="learn-more-about-power-bi"></a>Saiba mais sobre o Power BI

Este tutorial demonstra como criar apenas alguns tipos de visualizações para um conjunto de dados. O Power BI pode ajudá-lo a criar outras ferramentas de inteligência do negócio do cliente para a sua organização. Para mais ideias, consulte os seguintes recursos:

* Para mais um exemplo de um dashboard Power BI, assista ao vídeo [Getting Started with Power BI.](https://youtu.be/L-Z_6P56aas?t=1m58s)
* Para obter mais informações sobre a configuração da produção de trabalho do Streaming Analytics para o Power BI e a utilização de grupos Power [BI,](stream-analytics-define-outputs.md#power-bi) reveja a secção Power BI do artigo de [saídas stream analytics.](stream-analytics-define-outputs.md) 
* Para obter informações sobre a utilização do Power BI em geral, consulte [dashboards no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).


## <a name="learn-about-limitations-and-best-practices"></a>Conheça limitações e boas práticas
Atualmente, o Power BI pode ser chamado aproximadamente uma vez por segundo. Pacotes de suporte visual de 15 KB. Além disso, os visuais de streaming falham (mas o impulso continua a funcionar). Devido a estas limitações, o Power BI presta-se mais naturalmente a casos em que o Azure Stream Analytics faz uma redução significativa da carga de dados. Recomendamos a utilização de uma janela Tumbling ou de uma janela Hopping para garantir que o impulso de dados é, no máximo, um empurrão por segundo, e que a sua consulta aterra dentro dos requisitos de entrada.

Pode utilizar a seguinte equação para calcular o valor para dar a sua janela em segundos:

![Equação para calcular valor para dar janela em segundos](./media/stream-analytics-power-bi-dashboard/compute-window-seconds-equation.png)  

Por exemplo:

* Tem 1.000 dispositivos a enviar dados em intervalos de um segundo.
* Está a usar o Power BI Pro SKU que suporta 1.000.000 filas por hora.
* Pretende publicar a quantidade de dados médios por dispositivo ao Power BI.

Como resultado, a equação torna-se:

![Equação com base em critérios de exemplo](./media/stream-analytics-power-bi-dashboard/power-bi-example-equation.png)  

Dada esta configuração, pode alterar a consulta original para a seguinte:

```SQL
    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO "CallStream-PowerBI"
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl
```

### <a name="renew-authorization"></a>Renovar a autorização
Se a palavra-passe tiver mudado desde que o seu trabalho foi criado ou autenticado pela última vez, precisa de reautenticar a sua conta Power BI. Se a autenticação de vários fatores azure estiver configurada no seu inquilino azure Ative Directory (Azure AD), também precisa de renovar a autorização do Power BI de duas em duas semanas. Se não renovar, poderá ver sintomas como a falta de `Authenticate user error` saída de emprego ou um registo de operação.

Da mesma forma, se um trabalho começar após o termo do token, ocorre um erro e o trabalho falha. Para resolver este problema, pare o trabalho que está a decorrer e vá para a sua saída de Power BI. Para evitar a perda de dados, selecione o link **de autorização Renovar** e, em seguida, reinicie o seu trabalho a partir do tempo de **paragem.**

Depois de a autorização ter sido renovada com o Power BI, aparece um alerta verde na área de autorização para refletir que o problema foi resolvido.

## <a name="get-help"></a>Obter ajuda
Para mais assistência, experimente o nosso [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência linguística de consulta Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API de Gestão de Análise de Fluxo Soque](https://msdn.microsoft.com/library/azure/dn835031.aspx)
