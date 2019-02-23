---
title: Deteção de fraudes em tempo real com o Azure Stream Analytics
description: Saiba como criar uma solução de deteção de fraudes em tempo real com o Stream Analytics. Utilize um hub de eventos para processamento de eventos em tempo real.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 6433d130da108ce7bd0b6f8b4093161043c32645
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56733151"
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Comece a utilizar o Azure Stream Analytics: Deteção de fraudes em tempo real

Este tutorial fornece uma ilustração ponto-a-ponto de como utilizar o Azure Stream Analytics. Saiba como: 

* Traga a transmissão em fluxo eventos numa instância de Hubs de eventos do Azure. Neste tutorial, vai utilizar uma aplicação que simula um fluxo de registos de metadados de telefones celulares.

* Escreva consultas de análise de Stream de tipo SQL para transformar dados, Agregar informações ou à procura de padrões. Verá como utilizar uma consulta para examinar o fluxo de entrada e procurar chamadas que podem ser fraudulentas.

* Envie os resultados para um sink de saída (armazenamento) que pode analisar informações adicionais. Neste caso, irá enviar os dados das chamadas suspeita para o armazenamento de Blobs do Azure.

Este tutorial utiliza o exemplo de deteção de fraudes em tempo real com base nos dados de chamada telefónica. A técnica ilustrada também é adequada para outros tipos de deteção de fraudes, como o roubo de identidade de fraude ou cartão de crédito. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Cenário: Telecomunicações e SIM a deteção de fraudes em tempo real

Uma empresa de telecomunicações tem um grande volume de dados, chamadas de entrada. A empresa quer detetar chamadas fraudulentas em tempo real, para que eles possam notifique os clientes ou encerrar o serviço para um número específico. Um tipo de fraude SIM envolve várias chamadas da mesma identidade quase ao mesmo tempo, mas em locais geograficamente diferentes. Para detetar este tipo de fraude, a empresa precisa examinar registos de telefone de entrada e procurar padrões específicos — neste caso, para as chamadas feitas quase ao mesmo tempo em diferentes países. Quaisquer registos de telefone que se enquadram nessa categoria são escritos para o armazenamento para análise posterior.

## <a name="prerequisites"></a>Pré-requisitos

Neste tutorial, irá simular dados das chamadas telefónicas utilizando uma aplicação de cliente que gera os metadados de chamada telefónica de exemplo. Alguns dos registos que produz a aplicação a aparência chamadas fraudulentas. 

Antes de começar, certifique-se de que tem o seguinte:

* Uma conta do Azure.
* A aplicação de gerador de evento de chamada [Telcogenerator](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip), que pode ser transferido a partir do Microsoft Download Center. Deszipe o pacote para uma pasta no seu computador. Se quiser ver a origem de código e executar a aplicação num depurador, pode obter o código de origem da aplicação [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator). 

    >[!NOTE]
    >Windows poderão bloquear o ficheiro. zip transferido. Se não é possível descomprimi-lo, o ficheiro com o botão direito e selecione **propriedades**. Se vir a mensagem "este arquivo veio de outro computador e poderá ser bloqueado para ajudar a proteger este computador", selecione o **desbloquear** opção e, em seguida, clique em **aplicar**.

Se quiser examinar os resultados da tarefa do Stream Analytics, terá também uma ferramenta para visualizar o conteúdo de um contentor de armazenamento de Blobs do Azure. Se utilizar o Visual Studio, pode utilizar [ferramentas do Azure para Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) ou [Explorador de nuvem do Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer). Em alternativa, pode instalar ferramentas autônomas, como [Explorador de armazenamento do Azure](https://storageexplorer.com/) ou [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>Criar um Hubs de eventos do Azure para ingestão de eventos

Para analisar um fluxo de dados, *ingerir* -lo para o Azure. Uma forma típica de ingestão de dados é usar [os Hubs de eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md), que lhe permite ingerir milhões de eventos por segundo e, em seguida, processar e armazenar as informações do evento. Para este tutorial, irá criar um hub de eventos e, em seguida, tem a aplicação de gerador de evento de chamada enviar dados de chamada para esse hub de eventos. Para obter mais informações sobre os event hubs, consulte a [documentação do Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).

>[!NOTE]
>Para obter uma versão mais detalhada deste procedimento, consulte [criar um espaço de nomes de Hubs de eventos e um hub de eventos com o portal do Azure](../event-hubs/event-hubs-create.md). 

### <a name="create-a-namespace-and-event-hub"></a>Criar um hub de eventos e de espaço de nomes
Neste procedimento, primeiro tem de criar um espaço de nomes do hub de eventos e, em seguida, adicione um hub de eventos para esse espaço de nomes. Espaços de nomes de hub de eventos são utilizados para agrupar logicamente as instâncias de barramento de eventos relacionados. 

1. Inicie sessão no portal do Azure e clique em **criar um recurso** > **Internet das coisas** > **Hub de eventos**. 

2. Na **criar espaço de nomes** painel, introduza um nome de espaço de nomes como `<yourname>-eh-ns-demo`. Pode utilizar qualquer nome para o espaço de nomes, mas o nome tem de ser válido para um URL e tem de ser exclusivo em todo o Azure. 
    
3. Selecione uma subscrição e criar ou escolha um grupo de recursos e clique em **criar**.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png" alt="Create event hub namespace in Azure portal" width="300px"/>

4. Quando o espaço de nomes tiver concluído a implementação, encontre o espaço de nomes do hub de eventos na sua lista de recursos do Azure. 

5. Clique em novo espaço de nomes e, no painel de espaço de nomes, clique em **Hub de eventos**.

   ![O botão Adicionar Hub de eventos para a criação de um novo hub de eventos ](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
6. Nomeie o novo hub de eventos `asa-eh-frauddetection-demo`. Pode utilizar um nome diferente. Se o fizer, tome nota do mesmo, porque é necessário o nome mais tarde. Não precisa definir quaisquer outras opções para o hub de eventos neste momento.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png" alt="Name event hub in Azure portal" width="400px"/>
    
 
7. Clique em **Criar**.

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Conceder acesso ao hub de eventos e obter uma cadeia de ligação

Antes de um processo pode enviar dados para um hub de eventos, o hub de eventos tem de ter uma política que permita o acesso adequado. A política de acesso produz uma cadeia de ligação que inclui as informações de autorização.

1.  No painel de espaço de nomes de eventos, clique em **os Hubs de eventos** e, em seguida, clique no nome do seu novo hub de eventos.

2.  No painel do hub de eventos, clique em **políticas de acesso partilhado** e, em seguida, clique em  **+ &nbsp;Add**.

    >[!NOTE]
    >Certifique-se de que está a trabalhar com o hub de eventos, não o namespace do hub de eventos.

3.  Adicionar uma política com o nome `sa-policy-manage-demo` e para **afirmação**, selecione **gerir**.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png" alt="Create shared access policy for Stream Analytics" width="300px"/>
 
4.  Clique em **Criar**.

5.  Depois de implementar a política, clique no mesmo na lista de políticas de acesso partilhado.

6.  Localize a caixa rotulada **ligação chave primária da cadeia de caracteres** e clique no botão Copiar junto a cadeia de ligação. 

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png" alt="Stream Analytics shared access policy" width="300px"/>
 
7.  Cole a cadeia de ligação num editor de texto. Precisa desta cadeia de ligação para a secção seguinte, depois de fazer algumas pequenas edições ao mesmo.

    A cadeia de ligação tem o seguinte aspeto:

        Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=asa-eh-frauddetection-demo

    Tenha em atenção que a cadeia de ligação contém vários pares de chave-valor, separados por ponto e vírgula: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`, e `EntityPath`.  

## <a name="configure-and-start-the-event-generator-application"></a>Configurar e iniciar a aplicação geradora de eventos

Antes de iniciar a aplicação TelcoGenerator, deve configurá-lo para que ele irá enviar registos de chamadas para o hub de eventos que criou.

### <a name="configure-the-telcogenerator-app"></a>Configurar a aplicação TelcoGenerator

1.  No editor de onde copiou a cadeia de ligação, anote o `EntityPath` valor e, em seguida, remova o `EntityPath` par (não se esqueça de remover o ponto e vírgula que o precede). 

2.  Na pasta onde descompactei o ficheiro de Telcogenerator, abra o ficheiro de telcodatagen.exe.config num editor. (Há mais de um arquivo. config, por isso, certifique-se de que abre o correto.)

3.  Na `<appSettings>` elemento:

    * Defina o valor do `EventHubName` chave para o nome do hub de eventos (ou seja, para o valor do caminho de entidade).
    * Defina o valor do `Microsoft.ServiceBus.ConnectionString` chave para a cadeia de ligação. 

    O `<appSettings>` secção terá um aspeto semelhante ao seguinte exemplo. (Para maior clareza, as linhas são compactadas e alguns caracteres foram removidos do token de autorização).

   ![Ficheiro de configuração de TelcoGenerator mostra a cadeia de ligação e o nome do hub de eventos](./media/stream-analytics-real-time-fraud-detection/stream-analytics-telcogenerator-config-file-app-settings.png)
 
4.  Guarde o ficheiro. 

### <a name="start-the-app"></a>Iniciar a aplicação
1.  Abra uma janela de comando e altere para a pasta em que a aplicação TelcoGenerator é descompactada.
2.  Introduza o seguinte comando:

        ```cmd
        telcodatagen.exe 1000 0.2 2
        ```

    Os parâmetros são: 

    * Número de CDRs por hora. 
    * Probabilidade de fraude de cartão SIM: Como muitas vezes, como uma percentagem de todas as chamadas, que a aplicação deve simular uma chamada fraudulenta. O valor 0.2 significa que cerca de 20% dos registos de chamada parecerão fraudulentos.
    * Duração em horas. O número de horas que a aplicação deve ser executada. Também pode parar a aplicação qualquer altura ao premir Ctrl + C na linha de comandos.

    Após alguns segundos, a aplicação começa a apresentar registos de chamadas telefónicas no ecrã, à medida que os envia para o hub de eventos.

Alguns dos campos de chave, que irá utilizar neste aplicativo de deteção de fraudes em tempo real são os seguintes:

|**Registo**|**Definição**|
|----------|--------------|
|`CallrecTime`|O carimbo de data/hora da hora de início da chamada. |
|`SwitchNum`|O comutador de telefone utilizado para estabelecer a chamada. Neste exemplo, os comutadores são cadeias que representam o país de origem (E.U.A., China, Reino Unido, Alemanha ou Austrália). |
|`CallingNum`|O número de telefone do chamador. |
|`CallingIMSI`|A Identidade Internacional de Assinante Móvel (IMSI). Este é o identificador exclusivo do chamador. |
|`CalledNum`|O número de telefone do destinatário da chamada. |
|`CalledIMSI`|Identidade Internacional de Assinante Móvel (IMSI). Este é o identificador exclusivo do destinatário da chamada. |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>Criar uma tarefa do Stream Analytics para gerir os dados de transmissão em fluxo

Agora que tem um fluxo de eventos de chamada, pode configurar uma tarefa do Stream Analytics. A tarefa será ler dados do hub de eventos que configurou. 

### <a name="create-the-job"></a>Criar o trabalho 

1. No portal do Azure, clique em **criar um recurso** > **Internet das coisas** > **tarefa do Stream Analytics**.

2. Nome da tarefa `asa_frauddetection_job_demo`, especifique uma subscrição, grupo de recursos e localização.

    É uma boa idéia colocar a tarefa e o hub de eventos na mesma região para um melhor desempenho e para que não pague a transferência de dados entre regiões.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png" alt="Create Stream Analytics job in portal" width="300px"/>

3. Clique em **Criar**.

    A tarefa é criada e o portal apresenta os detalhes da tarefa. Nada é ainda em execução, no entanto, tem de configurar a tarefa antes que possa ser iniciado.

### <a name="configure-job-input"></a>Configurar a entrada da tarefa

1. No dashboard ou o **todos os recursos** painel, localize e selecione o `asa_frauddetection_job_demo` tarefa do Stream Analytics. 
2. Na **descrição geral** seção do painel de tarefas do Stream Analytics, clique no **entrada** caixa.

   ![Caixa de entrada na topologia no painel de trabalho de análise de transmissão em fluxo](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. Clique em **Adicionar entrada de fluxo** e selecione **Hub de eventos**. Em seguida, preencha a página de entrada novo com as seguintes informações:

   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Alias de entrada  |  CallStream   |  Introduza um nome para identificar a entrada da tarefa.   |
   |Subscrição   |  \<A sua subscrição\> |  Selecione a subscrição do Azure com o Hub de eventos que criou.   |
   |Espaço de nomes do hub de eventos  |  asa-eh-ns-demo |  Introduza o nome do espaço de nomes do Hub de eventos.   |
   |O nome do hub de eventos  | asa-eh-frauddetection-demo | Selecione o nome do seu Hub de eventos.   |
   |Nome da política do Hub de Eventos  | asa-policy-manage-demo | Selecione a política de acesso que criou anteriormente.   |
    </br>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png" alt="Create Stream Analytics input in portal" width="300px"/>


4. Clique em **Criar**.

## <a name="create-queries-to-transform-real-time-data"></a>Criar consultas para transformar dados em tempo real

Neste ponto, tem uma tarefa de Stream Analytics que configurar para ler um fluxo de dados de entrada. A próxima etapa é criar uma consulta que analisa os dados em tempo real. Stream Analytics suporta um modelo de consulta simples e declarativo que descreve as transformações para processamento em tempo real. As consultas de usar uma linguagem de tipo SQL que tem algumas extensões específicas para o Stream Analytics. 

Uma consulta simples apenas poderá ler todos os dados de entrada. No entanto, muitas vezes cria consultas que procuram de dados específicos ou de relações nos dados. Nesta secção do tutorial, pode criar e testar várias consultas para obter algumas formas em que pode transformar um fluxo de entrada para análise. 

As consultas que criar aqui apenas irão apresentar os dados transformados na tela. Numa seção posterior, irá configurar um sink de saída e uma consulta que escreve os dados transformados para esse sink.

Para saber mais sobre a linguagem, veja a [referência de linguagem de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/dn834998.aspx).

### <a name="get-sample-data-for-testing-queries"></a>Obter dados de exemplo para o teste de consultas

A aplicação TelcoGenerator está a enviar registos de chamadas para o hub de eventos e a tarefa de Stream Analytics é configurada para ler a partir do hub de eventos. Pode usar uma consulta para testar a tarefa para se certificar de que está a ler corretamente. Para testar uma consulta na consola do Azure, terá de dados de exemplo. Neste passo a passo, vai extrair dados de exemplo do fluxo que está a entrar no hub de eventos.

1. Certifique-se de que a aplicação TelcoGenerator está em execução e produzir registos de chamadas.
2. No portal, regresse ao painel de tarefas do Stream Analytics. (Se tiver fechado o painel, procure `asa_frauddetection_job_demo` no **todos os recursos** painel.)
3. Clique nas **consulta** caixa. O Azure apresenta uma lista as entradas e saídas que estão configuradas para a tarefa e permite-lhe criar uma consulta que lhe permite transformar o fluxo de entrada, como ele é enviado para a saída.
4. No **consulta** painel, clique nos pontos junto a `CallStream` de entrada e, em seguida, selecione **dados de exemplo da entrada**.

   ![Opções de menu para utilizar dados de exemplo para a entrada de tarefa do Stream Analytics, com "Dados de exemplo da entrada" selecionados](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)


5. Definir **minutos** 3 e, em seguida, clique em **OK**. 
    
   ![Opções de fluxo de entrada de amostragem com 3 minutos selecionados](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    O Azure amostras de valor de 3 minutos de dados do fluxo de entrada e notifica-o quando os dados de exemplo estão prontos. (Isto demora pouco tempo.) 

Os dados de exemplo são temporariamente armazenados e estão disponíveis enquanto a janela de consulta estiver aberta. Se fechar a janela de consulta, os dados de exemplo são eliminados e terá de criar um novo conjunto de dados de exemplo. 

Como alternativa, pode obter um ficheiro. JSON que tem dados de exemplo na mesma [do GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json)e, em seguida, carregue esse ficheiro. JSON para utilizar como dados de exemplo para o `CallStream` entrada. 

### <a name="test-using-a-pass-through-query"></a>Testar usando uma consulta pass-through

Se pretende arquivar todos os eventos, pode utilizar uma consulta pass-through para ler todos os campos no payload do evento.

1. Na janela da consulta, introduza esta consulta:
        
   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >Tal como acontece com SQL, palavras-chave não diferenciam maiúsculas de minúsculas e espaço em branco não é significativo.

    Nesta consulta, `CallStream` é o alias que especificou quando criou a entrada. Se utilizou um alias diferente, use esse nome.

2. Clique em **teste**.

    A tarefa do Stream Analytics executa a consulta os dados de exemplo e apresenta o resultado na parte inferior da janela. Os resultados indicam que o Hub de eventos e a tarefa do Stream Analytics estão configuradas corretamente. (Conforme indicado, mais tarde criará um sink de saída que a consulta pode escrever dados para.)

   ![Stream Analytics o resultado da tarefa, que mostra 73 registos gerados](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    O número exato de registos, ver dependerá de quantos registros tiverem sido capturados num seu exemplo de 3 minutos.
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Reduzir o número de campos com uma projeção de coluna

Em muitos casos, a análise não precisa de todas as colunas do fluxo de entrada. Pode usar uma consulta para projetar um conjunto menor de campos retornados do que na consulta pass-through.

1. Altere a consulta no editor de código para o seguinte:

   ```SQL
   SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
   FROM 
       CallStream
   ```

2. Clique em **teste** novamente. 

   ![Saída de tarefa do Stream Analytics para projeção mostra 25 registros](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Contagem de chamadas recebidas por região: Janela em cascata com agregação

Suponha que queira contar o número de chamadas recebidas por região. Em dados de transmissão em fluxo, quando deseja realizar as funções de agregação, como a contagem, precisa de dividir o fluxo em unidades temporais (uma vez que o fluxo de dados em si é efetivamente infinito). Faz isso usando uma análise de transmissão em fluxo [função de janela](stream-analytics-window-functions.md). Em seguida, pode trabalhar com os dados dentro dela como uma unidade.

Para essa transformação, pretende uma seqüência de temporais windows que não se sobreponham, cada janela terá um conjunto discreto de dados que pode agrupar e agregado. Este tipo de janela é referido como um *janela em cascata*. Dentro da janela em cascata, pode obter uma contagem de chamadas recebidas, agrupados por `SwitchNum`, que representa o país onde a chamada foi originada. 

1. Altere a consulta no editor de código para o seguinte:

        ```SQL
        SELECT 
            System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
        FROM
            CallStream TIMESTAMP BY CallRecTime 
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
        ```

    Esta consulta utiliza a `Timestamp By` palavra-chave no `FROM` cláusula para especificar que campo timestamp no fluxo de entrada para utilizar para definir a janela em cascata. Neste caso, a janela divide os dados em segmentos pelo `CallRecTime` campo em cada registo. (Não se for especificado nenhum campo, a operação de windowing utiliza a hora em que cada evento chegar ao hub de eventos. Consulte o "Tempo da aplicação do Vs de tempo de chegada" na [Stream referência de linguagem de consulta do Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx). 

    Inclui a projeção `System.Timestamp`, que retorna um carimbo para o final de cada janela. 

    Para especificar que pretende utilizar uma janela em cascata, utilize o [TUMBLINGWINDOW](https://msdn.microsoft.com/library/dn835055.aspx) funcionar o `GROUP BY `cláusula. Na função, especifique uma unidade de tempo (em qualquer lugar a partir de um microssegundo para um dia) e um tamanho de janela (quantas unidades). Neste exemplo, a janela em cascata consiste em intervalos de 5 segundos, então obterá uma contagem por país para o valor de cada 5 segundos de chamadas.

2. Clique em **teste** novamente. Nos resultados, tenha em atenção que os carimbos de data / sob **WindowEnd** são em incrementos de 5 segundos.

   ![Saída para agregação a mostrar 13 registos de tarefa do Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>Detetar fraudes SIM com uma associação automática

Neste exemplo, considere a utilização fraudulenta ser chamadas que têm origem no mesmo utilizador, mas em diferentes locais dentro de 5 segundos entre si. Por exemplo, o mesmo utilizador não pode legitimamente fazer uma chamada do E.U.A. e da Austrália ao mesmo tempo. 

Para verificar esses casos, pode utilizar uma associação automática dos dados de transmissão em fluxo para associar o fluxo ao se baseia no `CallRecTime` valor. Pode procurar a chamada, em seguida, os registos em que o `CallingIMSI` valor (o número de origem) é o mesmo, mas o `SwitchNum` valor (país de origem) não é o mesmo.

Quando utiliza uma junção com dados de transmissão em fluxo, a associação tem de fornecer que alguns limites relativamente a distância linhas correspondentes podem ser separados em tempo. (Conforme observado anteriormente, os dados de transmissão em fluxo são efetivamente infinitas.) Os limites de tempo para a relação são especificados no interior da `ON` cláusula de associação, usando o `DATEDIFF` função. Neste caso, a associação se baseia num intervalo de 5 segundos de dados de chamada.

1. Altere a consulta no editor de código para o seguinte: 

        ```SQL
        SELECT  System.Timestamp as Time, 
            CS1.CallingIMSI, 
            CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, 
            CS1.SwitchNum as Switch1, 
            CS2.SwitchNum as Switch2 
        FROM CallStream CS1 TIMESTAMP BY CallRecTime 
            JOIN CallStream CS2 TIMESTAMP BY CallRecTime 
            ON CS1.CallingIMSI = CS2.CallingIMSI 
            AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5 
        WHERE CS1.SwitchNum != CS2.SwitchNum
        ```

    Esta consulta é como qualquer associação SQL, exceto para o `DATEDIFF` função na associação. Esta versão do `DATEDIFF` é específico para análise de transmissão em fluxo de mensagens em fila, e tem de aparecer no `ON...BETWEEN` cláusula. Os parâmetros são uma unidade de tempo (segundos neste exemplo) e os aliases de duas origens de associação. Isso é diferente do SQL standard `DATEDIFF` função.

    O `WHERE` cláusula inclui a condição que sinaliza a chamada fraudulenta: os comutadores de origem não são iguais. 

2. Clique em **teste** novamente. 

   ![Saída de tarefa do Stream Analytics para associação automática, que mostra 6 registos gerados](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. Clique em **guardar** para guardar a consulta de associação automática como parte da tarefa do Stream Analytics. (Ele não salva os dados de exemplo.)

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png" alt="Save Stream Analytics query in portal" width="300px"/>

## <a name="create-an-output-sink-to-store-transformed-data"></a>Criar um sink de saída para armazenar os dados transformados

Definir um fluxo de eventos, um hub de eventos para ingerir eventos e uma consulta para realizar uma transformação durante o fluxo de entrada. A última etapa é definir um sink de saída para a tarefa — ou seja, um local para escrever o fluxo transformado a. 

Pode usar muitos recursos como sinks de saída — uma base de dados do SQL Server, o armazenamento de tabelas, o armazenamento do Data Lake, o Power BI e o hub de eventos até mesmo outro. Para este tutorial, irá escrever o fluxo para o armazenamento de Blobs do Azure, que é uma opção típica para recolher informações de eventos para análise posterior, uma vez que ele permite a dados não estruturados.

Se tiver uma conta de armazenamento de BLOBs existentes, pode utilizá-lo. Para este tutorial, aprenderá como criar uma nova conta de armazenamento.

### <a name="create-an-azure-blob-storage-account"></a>Criar uma conta de armazenamento de Blobs do Azure

1. No canto superior esquerdo do portal do Azure, selecione **Criar um recurso** > **Armazenamento** > **Conta de armazenamento**. Preencha a página de tarefa de conta de armazenamento com **Name** definido como "asaehstorage", **localização** definido como "Leste E.u.a.", **grupo de recursos** definido como "asa-,-ns-rg" (anfitrião da conta de armazenamento na o mesmo grupo de recursos como a tarefa de transmissão em fluxo para aumentar o desempenho). As restantes definições podem ser mantidas nos respetivos valores predefinidos.  

   ![Criar conta de armazenamento no portal do Azure](./media/stream-analytics-real-time-fraud-detection/stream-analytics-storage-account-create.png)

2. No portal do Azure, regresse ao painel de tarefas do Stream Analytics. (Se tiver fechado o painel, procure `asa_frauddetection_job_demo` no **todos os recursos** painel.)

3. Na **topologia da tarefa** secção, clique nas **saída** caixa.

4. Na **saídas** painel, clique em **Add** e selecione **armazenamento de BLOBs**. Em seguida, preencha a nova página de saída com as seguintes informações:

   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Alias de saída  |  CallStream-FraudulentCalls   |  Introduza um nome para identificar a saída da tarefa.   |
   |Subscrição   |  \<A sua subscrição\> |  Selecione a subscrição do Azure que tem a conta de armazenamento que criou. A conta de armazenamento pode estar na mesma subscrição ou numa diferente. Este exemplo assume que criou a conta de armazenamento na mesma subscrição. |
   |Conta de armazenamento  |  asaehstorage |  Introduza o nome da conta de armazenamento que criou. |
   |Contentor  | asa-fraudulentcalls-demo | Selecione criar novo e introduza um nome de contentor. |
    <br/>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png" alt="Create blob output for Stream Analytics job" width="300px"/>
    
5. Clique em **Guardar**. 


## <a name="start-the-streaming-analytics-job"></a>Iniciar a tarefa do Stream Analytics

A tarefa está agora configurada. Tiver especificado uma entrada (hub de eventos), uma transformação (a consulta para procurar chamadas fraudulentas) e uma saída (armazenamento de BLOBs). Agora pode iniciar a tarefa. 

1. Certificar-se de que a aplicação TelcoGenerator está em execução.

2. No painel de tarefas, clique em **iniciar**. Na **tarefa de início** painel, para a tarefa saída hora de início, selecione **agora**. 

   ![Iniciar a tarefa de Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start.png)



## <a name="examine-the-transformed-data"></a>Examinar os dados transformados

Agora tem uma tarefa de análise de transmissão em fluxo concluída. A tarefa é examinar um fluxo de metadados de chamada telefónica, à procura de chamadas fraudulentas de telefone em tempo real e escrever as informações sobre essas chamadas fraudulentas para o armazenamento. 

Para concluir este tutorial, pode querer examinar os dados capturados pela tarefa do Stream Analytics. Os dados são a ser escritos para o armazenamento de blogue do Azure em segmentos (ficheiros). Pode utilizar qualquer ferramenta que lê o armazenamento de Blobs do Azure. Conforme indicado na secção pré-requisitos, pode utilizar extensões do Azure no Visual Studio ou pode utilizar uma ferramenta como o [Explorador de armazenamento do Azure](https://storageexplorer.com/) ou [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

Quando examinar o conteúdo de um ficheiro no armazenamento de BLOBs, verá algo semelhante ao seguinte:

   ![Armazenamento de Blobs do Azure com a saída de análise de transmissão em fluxo](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>Limpar recursos

Existem artigos adicionais que continuam com o cenário de deteção de fraudes e utilizam os recursos que criou neste tutorial. Se quiser continuar, veja as sugestões em **próximos passos**.

No entanto, se já está e não precisa de recursos que criou, pode eliminá-los para que não a incorrer em encargos desnecessários do Azure. Nesse caso, sugerimos que faça o seguinte:

1. Pare a tarefa de análise de transmissão em fluxo. Na **trabalhos** painel, clique em **parar** na parte superior.
2. Parar o Telco gerador de aplicação. Na janela de comando onde começou a aplicação, prima Ctrl + C.
3. Se tiver criado uma nova conta de armazenamento de blob apenas para este tutorial, elimine-o. 
4. Elimine a tarefa de análise de transmissão em fluxo.
5. Elimine o hub de eventos.
6. Elimine o espaço de nomes do hub de eventos.

## <a name="get-support"></a>Obter suporte

Para obter assistência, tente o [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos Seguintes

Pode continuar este tutorial com o seguinte artigo:

* [Stream Analytics e o Power BI: Um dashboard de análise em tempo real para dados de transmissão em fluxo](stream-analytics-power-bi-dashboard.md). Este artigo mostra-lhe como enviar a saída de telecomunicações tarefa do Stream Analytics para Power BI para visualização em tempo real e análise.

Para obter mais informações sobre o Stream Analytics em geral, veja estes artigos:

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
