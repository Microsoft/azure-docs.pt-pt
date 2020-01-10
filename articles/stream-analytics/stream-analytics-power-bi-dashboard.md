---
title: Integração do painel do Power BI com o Azure Stream Analytics
description: Este artigo descreve como usar um painel de Power BI em tempo real para visualizar dados fora de um trabalho de Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 76f5c1f0cd3186244e9a262358c9c9a652a73fdb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431637"
---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics e Power BI: um painel de análise em tempo real para dados de streaming

Azure Stream Analytics permite que você tire proveito de uma das principais ferramentas de business intelligence, a [Microsoft Power bi](https://powerbi.com/). Neste artigo, você aprenderá como criar ferramentas de business intelligence usando Power BI como uma saída para seus trabalhos de Azure Stream Analytics. Você também aprenderá a criar e usar um painel em tempo real.

Este artigo continua no Stream Analytics tutorial de [detecção de fraudes em tempo real](stream-analytics-real-time-fraud-detection.md) . Ele se baseia no fluxo de trabalho criado nesse tutorial e adiciona uma saída de Power BI para que você possa Visualizar chamadas telefônicas fraudulentas detectadas por um trabalho do Stream Analytics. 

Você pode assistir [a um vídeo](https://www.youtube.com/watch?v=SGUpT-a99MA) que ilustra esse cenário.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem o seguinte:

* Uma conta do Azure.
* Uma conta para Power BI. Você pode usar uma conta corporativa ou uma conta de estudante.
* Uma versão completa do tutorial de [detecção de fraudes em tempo real](stream-analytics-real-time-fraud-detection.md) . O tutorial inclui um aplicativo que gera metadados fictícios de chamada telefônica. No tutorial, você cria um hub de eventos e envia os dados de chamada telefônica de streaming para o Hub de eventos. Você escreve uma consulta que detecta chamadas fraudulentas (chamadas do mesmo número ao mesmo tempo em locais diferentes). 


## <a name="add-power-bi-output"></a>Adicionar Power BI saída
No tutorial de detecção de fraudes em tempo real, a saída é enviada para o armazenamento de BLOBs do Azure. Nesta seção, você adiciona uma saída que envia informações para Power BI.

1. No portal do Azure, abra o trabalho do Stream Analytics que você criou anteriormente. Se você usou o nome sugerido, o trabalho será nomeado `sa_frauddetection_job_demo`.

2. No menu à esquerda, selecione **saídas** em **topologia do trabalho**. Em seguida, selecione **+ Adicionar** e escolha **Power bi** no menu suspenso.

3. Selecione **+ Adicionar** > **Power BI**. Em seguida, preencha o formulário com os seguintes detalhes e selecione **Autorizar**:

   |**Definição**  |**Valor sugerido**  |
   |---------|---------|
   |Alias de saída  |  CallStream-PowerBI  |
   |Nome do conjunto de dados  |   SA-conjunto de um  |
   |Nome da tabela |  chamadas fraudulentas  |

   ![Configurar a saída do Stream Analytics](media/stream-analytics-power-bi-dashboard/configure-stream-analytics-output.png)

   > [!WARNING]
   > Se Power BI tiver um conjunto de registros e uma tabela que tenham os mesmos nomes que os que você especificar no trabalho de Stream Analytics, os existentes serão substituídos.
   > É recomendável que você não crie explicitamente esse conjunto de e tabela em sua conta de Power BI. Eles são criados automaticamente quando você inicia seu trabalho de Stream Analytics e o trabalho começa a bombear a saída no Power BI. Se a consulta de trabalho não retornar nenhum resultado, o conjunto de resultados e a tabela não serão criados.
   >

4. Quando seleciona **Autorizar**, uma janela de pop-up abre e é-lhe pedido que forneça credenciais para fazer a autenticação na sua conta do Power BI. Assim que a autorização for bem sucedida, **guarde** as definições.

8. Clique em **Criar**.

O DataSet é criado com as seguintes configurações:

* **defaultRetentionPolicy: BasicFIFO** -os dados são FIFO, com um máximo de 200.000 linhas.
* **: pushStreaming** -o conjunto de um é compatível com blocos de streaming e visuais baseados em relatórios tradicionais (também conhecido como push).

No momento, você não pode criar conjuntos de os com outros sinalizadores.

Para obter mais informações sobre conjuntos de dados Power BI, consulte a referência da [API REST do Power bi](https://msdn.microsoft.com/library/mt203562.aspx) .


## <a name="write-the-query"></a>Gravar a consulta

1. Feche a folha **saídas** e retorne à folha de trabalho.

2. Clique na caixa **consulta** . 

3. Insira a consulta a seguir. Essa consulta é semelhante à consulta de auto-associação que você criou no tutorial de detecção de fraudes. A diferença é que essa consulta envia resultados para a nova saída que você criou (`CallStream-PowerBI`). 

    >[!NOTE]
    >Se você não nomear a entrada `CallStream` no tutorial de detecção de fraudes, substitua seu nome por `CallStream` nas cláusulas **from** e **Join** na consulta.

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

Esta seção é opcional, mas recomendada. 

1. Se o aplicativo TelcoStreaming não estiver em execução no momento, inicie-o seguindo estas etapas:

    * Abra o prompt de comando.
    * Vá para a pasta em que os arquivos telcogenerator. exe e modificados telcodatagen. exe. config são.
    * Execute o seguinte comando:

       `telcodatagen.exe 1000 .2 2`

2. Na página **consulta** de seu trabalho de Stream Analytics, clique nos pontos ao lado da entrada `CallStream` e, em seguida, selecione **dados de exemplo da entrada**.

3. Especifique que você deseja dados de três minutos e clique em **OK**. Aguarde até receber uma notificação a indicar que foi criada uma amostra dos dados.

4. Clique em **testar** e examine os resultados.

## <a name="run-the-job"></a>Executar a tarefa

1. Verifique se o aplicativo TelcoStreaming está em execução.

2. Navegue até a página de **visão geral** de seu trabalho de Stream Analytics e selecione **Iniciar**.

    ![Iniciar o trabalho de Stream Analytics](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

O trabalho do Stream Analytics começa a procurar por chamadas fraudulentas no fluxo de entrada. O trabalho também cria o conjunto de dados e a tabela em Power BI e inicia o envio de informações sobre as chamadas fraudulentas para eles.


## <a name="create-the-dashboard-in-power-bi"></a>Criar o painel no Power BI

1. Acesse [Powerbi.com](https://powerbi.com) e entre com sua conta corporativa ou de estudante. Se a consulta Stream Analytics trabalho gerar resultados, você verá que o conjunto de seus conjuntos de seus já está criado:

    ![Local do conjunto de fluxo de streaming no Power BI](./media/stream-analytics-power-bi-dashboard/stream-analytics-streaming-dataset.png)

2. No espaço de trabalho, clique em **+&nbsp;criar**.

    ![O botão criar no espaço de trabalho Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Crie um novo painel e nomeie-o `Fraudulent Calls`.

    ![Crie um painel e dê a ele um nome no espaço de trabalho Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. Na parte superior da janela, clique em **Adicionar bloco**, selecione **dados de streaming personalizados**e clique em **Avançar**.

    ![Bloco de conjunto de gráficos de streaming personalizado em Power BI](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. Em **seu conjuntos**, selecione seu conjunto de e clique em **Avançar**.

    ![Seu conjunto de fluxo de transmissão no Power BI](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. Em **tipo de visualização**, selecione **cartão**e, em seguida, na lista **campos** , selecione **fraudulentcalls**.

    ![Detalhes de visualização para o novo bloco](./media/stream-analytics-power-bi-dashboard/add-fraudulent-calls-tile.png)

7. Clique em **Seguinte**.

8. Preencha os detalhes do bloco, como um título e subtítulo.

    ![Título e subtítulo para o novo bloco](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Clique em **Aplicar**.

    Agora você tem um contador de fraudes!

    ![Contador de fraudes no painel Power BI](./media/stream-analytics-power-bi-dashboard/power-bi-fraud-counter-tile.png)

8. Siga as etapas novamente para adicionar um bloco (começando pela etapa 4). Desta vez, faça o seguinte:

    * Quando você chegar ao **tipo de visualização**, selecione **gráfico de linhas**. 
    * Adicione um eixo e selecione **windowend**. 
    * Adicione um valor e selecione **fraudulentcalls**.
    * Para **Janela de tempo a apresentar**, selecione os últimos 10 minutos.

      ![Criar bloco para gráfico de linhas em Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Clique em **Avançar**, adicione um título e um subtítulo e clique em **aplicar**.

     O painel do Power BI agora oferece duas exibições de dados sobre chamadas fraudulentas, conforme detectadas nos dados de streaming.

     ![Concluído Power BI painel mostrando dois blocos para chamadas fraudulentas](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)


## <a name="learn-more-about-power-bi"></a>Saiba mais sobre o Power BI

Este tutorial demonstra como criar apenas alguns tipos de visualizações para um conjunto de uma. Power BI pode ajudá-lo a criar outras ferramentas de business intelligence de cliente para sua organização. Para obter mais ideias, consulte os seguintes recursos:

* Para obter outro exemplo de um painel de Power BI, Assista ao [introdução com Power bi](https://youtu.be/L-Z_6P56aas?t=1m58s) vídeo.
* Para obter mais informações sobre como configurar a saída do trabalho do Stream Analytics para Power BI e usar grupos de Power BI, examine a seção [Power bi](stream-analytics-define-outputs.md#power-bi) do artigo [Stream Analytics saídas](stream-analytics-define-outputs.md) . 
* Para obter informações sobre como usar Power BI geralmente, consulte [painéis em Power bi](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).


## <a name="learn-about-limitations-and-best-practices"></a>Saiba mais sobre limitações e práticas recomendadas
No momento, Power BI pode ser chamado aproximadamente uma vez por segundo. Os visuais de streaming dão suporte a pacotes de 15 KB. Além disso, os visuais de streaming falham (mas o push continua a funcionar). Devido a essas limitações, Power BI se presta mais naturalmente aos casos em que Azure Stream Analytics faz uma redução significativa de carga de dados. É recomendável usar uma janela em cascata ou janela de salto para garantir que o envio de dados seja no máximo um push por segundo e que sua consulta fique dentro dos requisitos de taxa de transferência.

Você pode usar a equação a seguir para calcular o valor para dar sua janela em segundos:

![Equação para calcular o valor para dar janela em segundos](./media/stream-analytics-power-bi-dashboard/compute-window-seconds-equation.png)  

Por exemplo:

* Você tem 1.000 dispositivos enviando dados em intervalos de um segundo.
* Você está usando o Power BI Pro SKU que dá suporte a 1 milhão linhas por hora.
* Você deseja publicar a quantidade de dados médios por dispositivo para Power BI.

Como resultado, a equação se torna:

![Equação com base nos critérios de exemplo](./media/stream-analytics-power-bi-dashboard/power-bi-example-equation.png)  

Dada essa configuração, você pode alterar a consulta original para o seguinte:

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

### <a name="renew-authorization"></a>Renovar autorização
Se a senha tiver sido alterada desde que seu trabalho foi criado ou autenticado pela última vez, você precisará reautenticar sua conta de Power BI. Se a autenticação multifator do Azure estiver configurada no locatário do Azure Active Directory (Azure AD), você também precisará renovar a autorização Power BI a cada duas semanas. Se você não renovar, poderá ver sintomas como uma falta de saída de trabalho ou um `Authenticate user error` nos logs de operação.

Da mesma forma, se um trabalho for iniciado depois que o token tiver expirado, ocorrerá um erro e o trabalho falhará. Para resolver esse problema, pare o trabalho que está em execução e vá para a saída Power BI. Para evitar a perda de dados, selecione o link **renovar autorização** e reinicie o trabalho na **hora da última interrupção**.

Depois que a autorização for atualizada com Power BI, um alerta verde será exibido na área autorização para refletir que o problema foi resolvido.

## <a name="get-help"></a>Obter ajuda
Para obter assistência, tente nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST de gerenciamento de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
