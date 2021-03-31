---
title: Integração do painel power BI com Azure Stream Analytics
description: Este artigo descreve como usar um dashboard Power BI em tempo real para visualizar dados de um trabalho do Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 3bd35df91e836245de52d8959dff0671582ebc3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012449"
---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics e Power BI: Um dashboard de análise em tempo real para dados de streaming

O Azure Stream Analytics permite-lhe tirar partido de uma das principais ferramentas de inteligência empresarial, o [Microsoft Power BI](https://powerbi.com/). Neste artigo, aprende-se a criar ferramentas de inteligência empresarial usando o Power BI como uma produção para os seus trabalhos Azure Stream Analytics. Também aprende a criar e a utilizar um dashboard em tempo real que é continuamente atualizado pelo trabalho stream Analytics.

Este artigo continua a partir do tutorial [de deteção de fraude em tempo real](stream-analytics-real-time-fraud-detection.md) do Stream Analytics. Baseia-se no fluxo de trabalho criado nesse tutorial e adiciona uma saída de Power BI para que possa visualizar chamadas telefónicas fraudulentas que são detetadas por um trabalho de Streaming Analytics. 

Pode ver [um vídeo](https://www.youtube.com/watch?v=SGUpT-a99MA)  que ilustra este cenário.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem o seguinte:

* Uma conta do Azure.
* Uma conta para o Power BI Pro. Pode usar uma conta de trabalho ou uma conta escolar.
* Uma versão completa do tutorial [de deteção de fraude em tempo real.](stream-analytics-real-time-fraud-detection.md) O tutorial inclui uma aplicação que gera metadados fictícios de chamadas telefónicas. No tutorial, você cria um centro de eventos e envia os dados de chamadas telefónicas de streaming para o centro do evento. Escreve uma consulta que deteta chamadas fraudulentas (chamadas do mesmo número ao mesmo tempo em locais diferentes). 


## <a name="add-power-bi-output"></a>Adicionar potência de saída BI
No tutorial de deteção de fraudes em tempo real, a saída é enviada para o armazenamento da Azure Blob. Nesta secção, adicione uma saída que envia informações ao Power BI.

1. No portal Azure, abra o trabalho streaming Analytics que criou anteriormente. Se usou o nome sugerido, o trabalho `sa_frauddetection_job_demo` chama-se.

2. No menu esquerdo, selecione **Outputs** em **topologia de Trabalho**. Em seguida, **selecione + Adicione** e escolha Power **BI** no menu suspenso.

3. Selecione **+ Adicionar**  >  **Power BI**. Em seguida, preencha o formulário com os seguintes detalhes e **selecione Autorizado** a usar a sua própria identidade de utilizador para ligar ao Power BI (o token é válido por 90 dias). 

>[!NOTE]
>Para trabalhos de produção, recomendamos ligar-se à [utilização da Identidade Gerida para autenticar o seu trabalho Azure Stream Analytics para o Power BI](./powerbi-output-managed-identity.md).

   |**Definição**  |**Valor sugerido**  |
   |---------|---------|
   |Alias de saída  |  CallStream-PowerBI  |
   |Nome do conjunto de dados  |   sa-dataset  |
   |Nome da tabela |  chamadas fraudulentas  |

   ![Configure Stream Analytics saída](media/stream-analytics-power-bi-dashboard/configure-stream-analytics-output.png)

   > [!WARNING]
   > Se o Power BI tiver um conjunto de dados e uma tabela com os mesmos nomes que especifica no trabalho stream Analytics, os existentes são substituídos.
   > Recomendamos que não crie explicitamente este conjunto de dados e tabela na sua conta Power BI. São criadas automaticamente quando inicia o seu trabalho stream Analytics e o trabalho começa a bombear a saída para o Power BI. Se a sua consulta de trabalho não devolver quaisquer resultados, o conjunto de dados e a tabela não são criados.
   >

4. Quando seleciona **Autorizar**, uma janela de pop-up abre e é-lhe pedido que forneça credenciais para fazer a autenticação na sua conta do Power BI. Assim que a autorização for bem sucedida, **guarde** as definições.

8. Clique em **Criar**.

O conjunto de dados é criado com as seguintes definições:

* **defaultRetentionPolicy: BasicFIFO** - Data is FIFO, com um máximo de 200.000 linhas.
* **defaultMode: híbrido** - O conjunto de dados suporta tanto os azulejos de streaming (também conhecidos como push) como os visuais tradicionais baseados em relatórios. Para o conteúdo push, os dados são continuamente atualizados a partir do trabalho de análise de fluxo neste caso, sem necessidade de agendar atualização do lado do Power BI.

Atualmente, não é possível criar conjuntos de dados com outras bandeiras.

Para obter mais informações sobre conjuntos de dados Power BI, consulte a referência [API power BI REST.](/rest/api/power-bi/)


## <a name="write-the-query"></a>Escreva a consulta

1. Feche a lâmina **outputs** e volte à lâmina de trabalho.

2. Clique na **caixa de consulta.** 

3. Insira a seguinte consulta. Esta consulta é semelhante à consulta de auto-junção que criou no tutorial de deteção de fraudes. A diferença é que esta consulta envia resultados para a nova saída que criou ( `CallStream-PowerBI` ). 

    >[!NOTE]
    >Se não nomeou a entrada `CallStream` no tutorial de deteção de fraudes, substitua o seu nome `CallStream` nas cláusulas **FROM** e **JOIN** na consulta.

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

1. Se a aplicação TelcoStreaming não estiver atualmente em funcionamento, inicie-a seguindo estes passos:

    * Abrir o pedido de comando.
    * Vá à pasta onde estão os ficheiros telcogenerator.exe e telcodatagen.exe.config modificados.
    * Execute o seguinte comando:

       `telcodatagen.exe 1000 .2 2`

2. Na página **de consulta** para o seu trabalho stream Analytics, clique nos pontos ao lado da `CallStream` entrada e, em seguida, selecione **dados de amostra a partir da entrada**.

3. Especifique que deseja três minutos de dados e clique **em OK**. Aguarde até receber uma notificação a indicar que foi criada uma amostra dos dados.

4. Clique **em Teste** e reveja os resultados.

## <a name="run-the-job"></a>Executar a tarefa

1. Certifique-se de que a aplicação TelcoStreaming está em funcionamento.

2. Navegue na página **'Vista Geral'** para o seu trabalho stream Analytics e selecione **Start**.

    ![Inicie o trabalho stream analytics](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

O seu trabalho de Streaming Analytics começa a procurar chamadas fraudulentas no stream de entrada. O trabalho também cria o conjunto de dados e tabela no Power BI e começa a enviar dados sobre as chamadas fraudulentas para os mesmos.


## <a name="create-the-dashboard-in-power-bi"></a>Criar o dashboard no Power BI

1. Vá a [Powerbi.com](https://powerbi.com) e inscreva-se com o seu trabalho ou conta escolar. Se o resultado da consulta de trabalho stream Analytics, verá que o seu conjunto de dados já foi criado:

    ![Localização do conjunto de dados de streaming no Power BI](./media/stream-analytics-power-bi-dashboard/stream-analytics-streaming-dataset.png)

2. No seu espaço de trabalho, clique em **+ &nbsp; Criar**.

    ![O botão Criar no espaço de trabalho Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Crie um novo painel de instrumentos e nomeie-o. `Fraudulent Calls`

    ![Crie um dashboard e dê-lhe um nome no espaço de trabalho Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. Na parte superior da janela, clique em **Adicionar azulejo,** selecione **DADOS DE STREAMING PERSONALIZADOs** e, em seguida, clique em **Seguinte**.

    ![Azulejo de conjunto de dados de streaming personalizado no Power BI](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. No **ÂMBITO DOS SEUS DATSETS,** selecione o conjunto de dados e, em seguida, clique em **Seguinte**.

    ![O seu conjunto de dados de streaming no Power BI](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. Sob **o Tipo de Visualização**, selecione **Card**, e, em seguida, na lista **Fields,** selecione **chamadas fraudulentas**.

    ![Detalhes de visualização para novo azulejo](./media/stream-analytics-power-bi-dashboard/add-fraudulent-calls-tile.png)

7. Clique em **Seguinte**.

8. Preencha detalhes de azulejos como um título e legenda.

    ![Título e legenda para novo azulejo](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Clique em **Aplicar**.

    Agora tem um contador de fraudes!

    ![Contador de fraude no painel power BI](./media/stream-analytics-power-bi-dashboard/power-bi-fraud-counter-tile.png)

8. Siga novamente os passos para adicionar um azulejo (começando com o passo 4). Desta vez, faça o seguinte:

    * Quando chegar ao **Tipo de Visualização,** selecione **gráfico de linha**. 
    * Adicione um eixo e selecione **windowend**. 
    * Adicione um valor e selecione **fraudulentcalls**.
    * Para **Janela de tempo a apresentar**, selecione os últimos 10 minutos.

      ![Criar azulejos para gráfico de linha em Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Clique **em seguida,** adicione um título e legenda, e clique em **Aplicar**.

     O painel Power BI dá-lhe agora duas visões de dados sobre chamadas fraudulentas, tal como detetado nos dados de streaming.

     ![Painel de bi de potência acabado mostrando dois azulejos para chamadas fraudulentas](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)

## <a name="learn-about-limitations-and-best-practices"></a>Conheça as limitações e as melhores práticas
Atualmente, o Power BI pode ser chamado aproximadamente uma vez por segundo. Streaming de suporte de visuais pacotes de 15 KB. Além disso, os visuais de streaming falham (mas o impulso continua a funcionar). Devido a estas limitações, o Power BI presta-se mais naturalmente aos casos em que o Azure Stream Analytics faz uma redução significativa da carga de dados. Recomendamos a utilização de uma janela de Tumbling ou janela Hopping para garantir que o impulso de dados é no máximo um empurrão por segundo, e que a sua consulta cai dentro dos requisitos de produção.

Pode utilizar a seguinte equação para calcular o valor para dar a sua janela em segundos:

![Equação para calcular valor para dar janela em segundos](./media/stream-analytics-power-bi-dashboard/compute-window-seconds-equation.png)  

Por exemplo:

* Tem 1.000 dispositivos enviando dados em intervalos de um segundo.
* Está a usar o Power BI Pro SKU que suporta 1.000.000 linhas por hora.
* Pretende publicar a quantidade de dados médios por dispositivo para o Power BI.

Como resultado, a equação torna-se:

![Equação baseada em critérios de exemplo](./media/stream-analytics-power-bi-dashboard/power-bi-example-equation.png)  

Dada esta configuração, pode alterar a consulta original para o seguinte:

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
Se a palavra-passe tiver sido alterada desde que o seu trabalho foi criado ou autenticado pela última vez, tem de reautorá-la à sua conta Power BI. Se a autenticação multi-factor Azure AD estiver configurada no seu inquilino Azure Ative Directory (Azure AD), também precisa de renovar a autorização do Power BI de duas em duas semanas. Se não renovar, poderá ver sintomas como a falta de saída de emprego ou `Authenticate user error` um nos registos de operação.

Da mesma forma, se um trabalho começar após o token expirar, ocorre um erro e o trabalho falha. Para resolver este problema, pare o trabalho que está em execução e vá para a sua saída de Power BI. Para evitar a perda de dados, selecione o link **de autorização Renovar** e, em seguida, reinicie o seu trabalho a partir da última hora **parada**.

Depois de a autorização ter sido atualizada com o Power BI, aparece um alerta verde na área de autorização para refletir que a questão foi resolvida.

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics saídas](stream-analytics-define-outputs.md)
* [Referência linguística de consulta Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência Azure Stream Analytics API](/rest/api/streamanalytics/)
* [Utilize identidade gerida para autenticar o seu trabalho Azure Stream Analytics para Power BI](./powerbi-output-managed-identity.md)
