---
title: Integração de dashboard do Power BI com o Azure Stream Analytics
description: Este artigo descreve como utilizar o dashboard do Power BI em tempo real para visualizar dados fora de uma tarefa do Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 487c142400dc2bfa6f44e17963535051af017196
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58116186"
---
# <a name="tutorial-stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Tutorial: Stream Analytics e o Power BI: Um dashboard de análise em tempo real para dados de transmissão em fluxo
O Azure Stream Analytics permite-lhe tirar partido de um das principais ferramentas de business intelligence, [Microsoft Power BI](https://powerbi.com/). Neste artigo, ficará a saber como criar ferramentas de business intelligence com o Power BI como uma saída para as tarefas do Azure Stream Analytics. Também irá aprender a criar e utilizar um dashboard em tempo real.

Este artigo continua a partir do Stream Analytics [deteção de fraudes em tempo real](stream-analytics-real-time-fraud-detection.md) tutorial. Ele se baseia no fluxo de trabalho criado nesse tutorial e adiciona um Power BI, de modo a que é possível visualizar as chamadas de telefone fraudulentas que são detetadas por um trabalho de análise de transmissão em fluxo de saída. 

Pode assistir [um vídeo](https://www.youtube.com/watch?v=SGUpT-a99MA) que ilustra esse cenário.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem o seguinte:

* Uma conta do Azure.
* Uma conta para o Power BI. Pode utilizar uma conta profissional ou uma conta da instituição de ensino.
* Uma versão concluída dos [deteção de fraudes em tempo real](stream-analytics-real-time-fraud-detection.md) tutorial. O tutorial inclui uma aplicação que gera os metadados de chamada telefónica fictícios. O tutorial, cria um hub de eventos e enviar os dados de chamada telefónica de transmissão em fluxo para o hub de eventos. Escrever uma consulta que Deteta chamadas fraudulentas (chamadas do mesmo número ao mesmo tempo em diferentes locais). 


## <a name="add-power-bi-output"></a>Adicionar a saída do Power BI
O tutorial de deteção de fraudes em tempo real, a saída é enviada para o armazenamento de Blobs do Azure. Nesta secção, vai adicionar uma saída que envia informações para o Power BI.

1. No portal do Azure, abra a tarefa de análise de transmissão em fluxo que criou anteriormente. Se tiver utilizado o nome sugerido, a tarefa com o nome `sa_frauddetection_job_demo`.

2. Selecione o **saídas** caixa no meio do dashboard de tarefa e, em seguida, selecione **+ adicionar**.

3. Para **Alias de saída**, introduza `CallStream-PowerBI`. Pode utilizar um nome diferente. Se o fizer, tome nota do mesmo, porque é necessário o nome mais tarde. 

4. Sob **Sink**, selecione **Power BI**.

   ![Criar uma saída para o Power BI](./media/stream-analytics-power-bi-dashboard/create-power-bi-ouptut.png)

5. Clique em **autorizar**.

    Uma é apresentada a janela onde pode fornecer as credenciais do Azure para uma conta escolar ou profissional. 

    ![Introduza as credenciais de acesso para o Power BI](./media/stream-analytics-power-bi-dashboard/power-bi-authorization-credentials.png)

6. Introduza as credenciais. Tenha em atenção, em seguida, ao introduzir as suas credenciais, também está a dar permissão para a tarefa de análise de transmissão em fluxo para acessar sua área de Power BI.

7. Quando forem retornados para o **nova saída** painel, introduza as seguintes informações:

   * **Área de trabalho de grupo**: Selecione uma área de trabalho no seu inquilino do Power BI em que pretende criar o conjunto de dados.
   * **Nome do conjunto de dados**:  Introduza `sa-dataset`. Pode utilizar um nome diferente. Se o fizer, tome nota do mesmo para utilizar mais tarde.
   * **Nome da tabela**: Introduza `fraudulent-calls`. Atualmente, a saída do Power BI a partir de tarefas do Stream Analytics pode ter apenas uma tabela num conjunto de dados.

     ![O conjunto de dados do Power BI área de trabalho e de tabela](./media/stream-analytics-power-bi-dashboard/create-pbi-ouptut-with-dataset-table.png)

     > [!WARNING]
     > Se o Power BI tem um conjunto de dados e a tabela que tem os mesmos nomes que aqueles que especificou na tarefa do Stream Analytics, os existentes serão substituídos.
     > Recomendamos que não explicitamente criar este conjunto de dados e a tabela na sua conta do Power BI. Elas são criadas automaticamente quando iniciar a tarefa de Stream Analytics e a tarefa é iniciada a saída de bombagem para o Power BI. Se a sua consulta da tarefa não devolve nenhum resultado, o conjunto de dados e a tabela não são criados.
     >

8. Clique em **Criar**.

O conjunto de dados é criado com as seguintes definições:

* **defaultRetentionPolicy: BasicFIFO**: Os dados são FIFO, com um máximo de 200 000 linhas.
* **defaultMode: pushStreaming**: O conjunto de dados suporta a transmissão em fluxo de mosaicos e elementos visuais com base no relatório tradicionais (também conhecido como Push).

Atualmente, não é possível criar conjuntos de dados com outros sinalizadores.

Para obter mais informações sobre conjuntos de dados do Power BI, consulte a [API REST do Power BI](https://msdn.microsoft.com/library/mt203562.aspx) referência.


## <a name="write-the-query"></a>Escrever a consulta

1. Fechar o **saídas** painel e regresse ao painel de tarefas.

2. Clique nas **consulta** caixa. 

3. Introduza a seguinte consulta. Esta consulta é semelhante à consulta de associação automática de mensagens em fila que criou no tutorial de deteção de fraudes. A diferença é que esta consulta envia resultados para a nova saída que criou (`CallStream-PowerBI`). 

    >[!NOTE]
    >Se não nome de entrada `CallStream` do tutorial de deteção de fraudes, substitua o nome de `CallStream` no **FROM** e **associar** cláusulas na consulta.

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


## <a name="test-the-query"></a>Testar a consulta
Esta secção é opcional mas recomendado. 

1. Se a aplicação de TelcoStreaming não estiver em execução, inicie-o através dos seguintes passos:

    * Abra uma janela de comando.
    * Vá para a pasta onde estão os ficheiros de telcodatagen.exe.config modificado e telcogenerator.exe.
    * Execute o seguinte comando:

       `telcodatagen.exe 1000 .2 2`

2. No **consulta** painel, clique nos pontos junto a `CallStream` de entrada e, em seguida, selecione **dados de exemplo da entrada**.

3. Especifique que pretende que o valor de três minutos de dados e clique em **OK**. Aguarde até receber uma notificação a indicar que foi criada uma amostra dos dados.

4. Clique em **Testar** e certifique-se de que está a obter os resultados.


## <a name="run-the-job"></a>Executar a tarefa

1. Certifique-se de que a aplicação de TelcoStreaming está em execução.

2. Fechar o **consulta** painel.

3. No painel de tarefas, clique em **iniciar**.

    ![Iniciar a tarefa de Stream Analytics](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

Sua tarefa do Stream Analytics começa a procurar chamadas fraudulentas num fluxo de entrada. A tarefa também cria o conjunto de dados e a tabela no Power BI e começa a enviar dados sobre as chamadas fraudulentas a eles.


## <a name="create-the-dashboard-in-power-bi"></a>Criar o dashboard no Power BI

1. Aceda a [Powerbi.com](https://powerbi.com) e inicie sessão com a sua conta escolar ou profissional. Se a consulta de tarefa do Stream Analytics produzir resultados, verá que o seu conjunto de dados já foi criado:

    ![Localização do conjunto de dados no Power BI de transmissão em fluxo](./media/stream-analytics-power-bi-dashboard/stream-analytics-streaming-dataset.png)

2. Na área de trabalho, clique em  **+ &nbsp;criar**.

    ![O botão Criar na área de trabalho do Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Crie um novo dashboard e nomeie- `Fraudulent Calls`.

    ![Criar um dashboard e dar a ele um nome na área de trabalho do Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. Na parte superior da janela, clique em **adicionar mosaico**, selecione **dados de transmissão em fluxo personalizados**e, em seguida, clique em **seguinte**.

    ![Mosaico de conjunto de dados no Power BI de transmissão em fluxo personalizados](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. Sob **YOUR DATSETS**, selecione o conjunto de dados e, em seguida, clique em **próxima**.

    ![O conjunto de dados de transmissão em fluxo no Power BI](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. Sob **tipo de visualização**, selecione **cartão**e, em seguida, no **campos** lista, selecione **fraudulentcalls**.

    ![Detalhes de visualização para o novo mosaico](./media/stream-analytics-power-bi-dashboard/add-fraudulent-calls-tile.png)

7. Clique em **Seguinte**.

8. Preencha os detalhes do mosaico, como um título e subtítulo.

    ![Título e subtítulo do novo mosaico](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Clique em **Aplicar**.

    Agora tem um contador de fraude!

    ![Contador de fraude no dashboard do Power BI](./media/stream-analytics-power-bi-dashboard/power-bi-fraud-counter-tile.png)

8. Siga os passos novamente para adicionar um mosaico (começando com o passo 4). Desta vez, faça o seguinte:

    * Ao chegar na **tipo de visualização**, selecione **gráfico de linhas**. 
    * Adicione um eixo e selecione **windowend**. 
    * Adicione um valor e selecione **fraudulentcalls**.
    * Para **Janela de tempo a apresentar**, selecione os últimos 10 minutos.

      ![Criar o mosaico de gráfico de linhas no Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Clique em **próxima**, adicione um título e subtítulo e clique em **aplicar**.

     O dashboard do Power BI agora oferece a dois modos de exibição de dados sobre chamadas fraudulentas como detetado nos dados de transmissão em fluxo.

     ![Concluído o dashboard do Power BI que mostra dois mosaicos para chamadas fraudulentas](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)


## <a name="learn-more-about-power-bi"></a>Mais informações sobre o Power BI

Este tutorial demonstra como criar apenas alguns tipos de visualizações para um conjunto de dados. Power BI pode ajudá-lo a criar outras ferramentas de cliente business intelligence para a sua organização. Para obter mais ideias, consulte os seguintes recursos:

* Para obter outro exemplo de dashboard do Power BI, veja a [introdução ao Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) vídeo.
* Para obter mais informações sobre como configurar a análise de transmissão em fluxo o resultado para o Power BI da tarefa e utilizar grupos do Power BI, reveja os [Power BI](stream-analytics-define-outputs.md#power-bi) secção a [saídas do Stream Analytics](stream-analytics-define-outputs.md) artigo. 
* Para obter informações sobre como utilizar o Power BI em geral, consulte [Dashboards no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).


## <a name="learn-about-limitations-and-best-practices"></a>Saiba mais sobre as limitações e melhores práticas
Atualmente, Power BI pode ser chamado aproximadamente uma vez por segundo. Elementos visuais de transmissão em fluxo suportam pacotes de 15 KB. Além disso, a falha de elementos visuais de transmissão em fluxo (mas push continua a funcionar). Devido a essas limitações, Power BI presta-se mais naturalmente para casos em que o Azure Stream Analytics faz uma redução de carregamento de dados significativos. Recomendamos que utilize uma janela em cascata ou janela Hopping para garantir que o push de dados é, no máximo, um push por segundo e que a consulta que chegam dentro os requisitos de débito.

Pode usar a seguinte equação para calcular o valor para dar a janela em segundos:

![Equação para computar o valor para dar a janela em segundos](./media/stream-analytics-power-bi-dashboard/compute-window-seconds-equation.png)  

Por exemplo:

* Tem 1000 dispositivos de envio de dados em intervalos de um segundo.
* Está a utilizar o Power BI Pro SKU que suporte 1 000 000 linhas por hora.
* Que pretende publicar a quantidade de dados de média por dispositivo para o Power BI.

Como resultado, torna-se a equação:

![Equação com base nos critérios de exemplo](./media/stream-analytics-power-bi-dashboard/power-bi-example-equation.png)  

Tendo em conta esta configuração, pode alterar a consulta original para o seguinte:

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
Se a palavra-passe tiver sido alterado, uma vez que a tarefa foi criada ou pela última vez autenticada, terá de autenticar a sua conta do Power BI. Se o Azure multi-factor Authentication está configurado no seu inquilino do Azure Active Directory (Azure AD), terá também de renovar a autorização do Power BI duas em duas semanas. Se não o renovar, pode ver os sintomas, como a falta de saída da tarefa ou um `Authenticate user error` nos registos de operação.

Da mesma forma, se uma tarefa é iniciada após a expiração do token, ocorre um erro e a tarefa falhar. Para resolver este problema, parar a tarefa que está a executar e vá para a saída do Power BI. Para evitar a perda de dados, selecione o **renovar a autorização** associar e, em seguida, reinicie a tarefa a partir do **data/hora da última parado**.

Após a autorização foi atualizada com o Power BI, um alerta de verde é apresentada na área de autorização para refletir que o problema foi resolvido.

## <a name="get-help"></a>Obter ajuda
Para obter assistência, tente nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos Seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API de REST de gestão do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
