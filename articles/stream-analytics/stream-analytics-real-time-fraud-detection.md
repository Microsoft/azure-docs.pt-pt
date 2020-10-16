---
title: Deteção de fraudes em tempo real com o Azure Stream Analytics
description: Saiba como criar uma solução de deteção de fraude em tempo real com o Stream Analytics. Utilize um centro de eventos para o processamento de eventos em tempo real.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: 3bfc03dd7a04bea7e69aa1b62cef267a81b650f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86037618"
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Começar a usar Azure Stream Analytics: Deteção de fraude em tempo real

Este tutorial fornece uma ilustração de ponta a ponta de como usar o Azure Stream Analytics. Saiba como: 

* Traga eventos de streaming para um exemplo de Azure Event Hubs. Neste tutorial, você usará uma aplicação que simula um fluxo de registos de metadados de telemóvel.

* Escreva consultas de Stream Analytics semelhantes a SQL para transformar dados, agregar informações ou procurar padrões. Você verá como usar uma consulta para examinar o fluxo de entrada e procurar chamadas que possam ser fraudulentas.

* Envie os resultados para um lavatório de saída (armazenamento) que pode analisar para obter informações adicionais. Neste caso, você enviará os dados de chamadas suspeitas para o armazenamento de Azure Blob.

Este tutorial usa o exemplo da deteção de fraude em tempo real com base em dados de chamadas telefónicas. A técnica ilustrada também é adequada para outros tipos de deteção de fraudes, como fraude com cartão de crédito ou roubo de identidade. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Cenário: Deteção de fraudes em tempo real e telecomunicações e SIM

Uma empresa de telecomunicações tem um grande volume de dados para chamadas recebidas. A empresa quer detetar chamadas fraudulentas em tempo real para que possam notificar os clientes ou encerrar o serviço por um número específico. Um tipo de fraude SIM envolve múltiplas chamadas da mesma identidade ao mesmo tempo, mas em locais geograficamente diferentes. Para detetar este tipo de fraude, a empresa precisa examinar os registos telefónicos recebidos e procurar padrões específicos - neste caso, para chamadas feitas ao mesmo tempo em diferentes países/regiões. Quaisquer registos telefónicos que se instituam nesta categoria são escritos para armazenamento para análise posterior.

## <a name="prerequisites"></a>Pré-requisitos

Neste tutorial, simulará dados de chamadas telefónicas utilizando uma aplicação de cliente que gera metadados de chamadas telefónicas de amostra. Alguns dos registos que a aplicação produz parecem chamadas fraudulentas. 

Antes de começar, certifique-se de que tem o seguinte:

* Uma conta do Azure.
* A aplicação de gerador de call-event, [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip), que pode ser descarregada a partir do Microsoft Download Center. Desaperte este pacote numa pasta no seu computador. Se quiser ver o código fonte e executar a aplicação num depurar, pode obter o código fonte da aplicação do [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator). 

    >[!NOTE]
    >O Windows pode bloquear o ficheiro .zip descarregado. Se não conseguir desapertá-lo, clique com o botão direito no ficheiro e selecione **Propriedades**. Se vir a mensagem "Este ficheiro veio de outro computador e pode estar bloqueado para ajudar a proteger este computador", selecione a opção **Desbloqueio** e clique em **Aplicar**.

Se quiser examinar os resultados do trabalho do Streaming Analytics, também precisa de uma ferramenta para visualizar o conteúdo de um recipiente de armazenamento Azure Blob. Se utilizar o Visual Studio, pode utilizar [ferramentas Azure para Estúdio Visual](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) ou [Visual Studio Cloud Explorer](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer). Em alternativa, pode instalar ferramentas autónomas como [o Azure Storage Explorer](https://storageexplorer.com/) ou o [Cerulean.](https://www.cerebrata.com/products/cerulean/features/azure-storage) 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>Criar um Azure Event Hubs para ingerir eventos

Para analisar um fluxo de dados, *ingeri-lo* em Azure. Uma forma típica de ingerir dados é usar [o Azure Event Hubs,](../event-hubs/event-hubs-what-is-event-hubs.md)que permite ingerir milhões de eventos por segundo e depois processar e armazenar a informação do evento. Para este tutorial, irá criar um centro de eventos e, em seguida, pedir à aplicação do gerador de call-event que envie dados de chamadas para esse centro de eventos.

>[!NOTE]
>Para obter uma versão mais detalhada deste procedimento, consulte Criar um espaço de nomes de Centros de [Eventos e um centro de eventos utilizando o portal Azure](../event-hubs/event-hubs-create.md). 

### <a name="create-a-namespace-and-event-hub"></a>Crie um espaço de nome e centro de eventos
Neste procedimento, você primeiro cria um espaço de nome de centro de eventos, e depois você adiciona um centro de eventos a esse espaço de nome. Os espaços de nome do centro de eventos são usados para grupos logicamente relacionados casos de ônibus de evento. 

1. Faça login no portal Azure e clique em **Criar um recurso** na parte superior esquerda do ecrã.

2. Selecione **todos os serviços** no menu esquerdo e selecione **star `*` ()** ao lado **de Event Hubs** na categoria **Analytics.** Confirme que **os Hubs de Evento são adicionados** aos **FAVORITOS** no menu de navegação à esquerda. 

   ![Pesquisa rumo aos Centros de Eventos](./media/stream-analytics-real-time-fraud-detection/select-event-hubs-menu.png)

3. Selecione **Os Centros de Eventos** em **FAVORITOS** no menu de navegação à esquerda e selecione **Adicione** na barra de ferramentas.

   ![Botão Adicionar](./media/stream-analytics-real-time-fraud-detection/event-hubs-add-toolbar.png)

4. No painel De espaço para **nomes Create,** introduza um nome de espaço de nome como `<yourname>-eh-ns-demo` . Você pode usar qualquer nome para o espaço de nome, mas o nome deve ser válido para um URL e deve ser único em Azure. 
    
5. Selecione uma subscrição e crie ou escolha um grupo de recursos e, em seguida, clique em **Criar**.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png" alt="Create event hub namespace in Azure portal" width="300px"/>

6. Quando o espaço de nome terminar de ser implementado, encontre o espaço de nome do centro de eventos na sua lista de recursos Azure. 

7. Clique no novo espaço de nome, e no painel de espaços com nome, clique no **Centro de Eventos**.

   ![O botão Add Event Hub para criar um novo centro de eventos](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
8. Nomeie o novo centro de `asa-eh-frauddetection-demo` eventos. Pode utilizar um nome diferente. Se o fizer, tome nota, porque precisa do nome mais tarde. Não precisa definir nenhuma outra opção para o centro de eventos agora.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png" alt="Name event hub in Azure portal" width="400px"/>
    
9. Clique em **Criar**.

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Conceder acesso ao hub de eventos e obter uma cadeia de ligação

Antes de um processo poder enviar dados para um centro de eventos, o centro de eventos deve ter uma política que permita o acesso adequado. A política de acesso produz uma cadeia de ligação que inclui as informações de autorização.

1. No painel de nomes do evento, clique em **'Event Hubs'** e, em seguida, clique no nome do seu novo centro de eventos.

2. No painel de hub do evento, clique nas **políticas de acesso compartilhados** e, em seguida, clique em ** + &nbsp; Adicionar**.

    > [!NOTE]
    > Certifique-se de que está a trabalhar com o centro de eventos, não com o espaço de nomes do centro de eventos.

3. Adicione uma política nomeada `asa-policy-manage-demo` e para **Reclamação**, selecione **Gerir**.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png" alt="Create shared access policy for Stream Analytics" width="300px"/>
 
4. Clique em **Criar**.

5. Depois de implementada a política, clique na lista de políticas de acesso partilhado.

6. Encontre a chave **PRIMARária DE LIGAÇÃO** com a etiqueta da caixa e clique no botão de cópia ao lado da cadeia de ligação. 

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png" alt="Stream Analytics shared access policy" width="300px"/>
 
7. Cole a cadeia de ligação num editor de texto. Precisa desta cadeia de ligação para a próxima secção, depois de fazer algumas pequenas edições.

    A cadeia de ligação é assim:

    `Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=asa-eh-frauddetection-demo`

    Note que a cadeia de ligação contém múltiplos pares de valores-chave, separados com pontos de ligação: `Endpoint` `SharedAccessKeyName` , , e `SharedAccessKey` `EntityPath` .  


## <a name="configure-and-start-the-event-generator-application"></a>Configurar e iniciar a aplicação do gerador de eventos

Antes de iniciar a aplicação TelcoGenerator, tem de a configurar para que envie registos de chamadas para o centro de eventos que criou.

### <a name="configure-the-telcogenerator-app"></a>Configure a app TelcoGenerator

1. No editor onde copiou a cadeia de ligação, tome nota do `EntityPath` valor e, em seguida, retire o `EntityPath` par (não se esqueça de remover o ponto e vírgula que o precede). 

2. Na pasta onde desapertou o ficheiro TelcoGenerator.zip, abra o ficheiro telcodatagen.exe.config num editor. (Existe mais de um ficheiro .config, por isso certifique-se de abrir o ficheiro certo.)

3. No `<appSettings>` elemento:

   * Desacorra o valor da `EventHubName` chave para o nome do hub do evento (isto é, ao valor do percurso da entidade).
   * Desa estar o valor da `Microsoft.ServiceBus.ConnectionString` chave para a cadeia de ligação. 

   A `<appSettings>` secção será o seguinte exemplo:

    ```xml
    <appSettings>
     <!-- Service Bus specific app setings for messaging connections -->
     <add key="EventHubName" value="asa-eh-ns-demo"/>
     <add key="Microsoft.ServiceBus.ConnectionString" value="Endpoint=sb://asa-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=GEcnTKf2//1MRn6SN1A2u0O76MP9pj3v0Ccyf1su4Zo="/>
   </appSettings>
    ```
 
4. Guarde o ficheiro. 

### <a name="start-the-app"></a>Inicie a aplicação

1. Abra uma janela de comando e mude para a pasta onde a aplicação TelcoGenerator está desapertado.

2. Introduza o seguinte comando:

    ```console
    telcodatagen.exe 1000 0.2 2
    ```

   Os parâmetros são: 

   * Número de CDRs por hora. 
   * Probabilidade de Fraude de Cartão SIM: Com que frequência, em percentagem de todas as chamadas, a aplicação deve simular uma chamada fraudulenta. O valor 0.2 significa que cerca de 20% dos registos de chamada parecerão fraudulentos.
   * Duração em horas. O número de horas que a aplicação deve executar. Também pode parar a aplicação a qualquer momento premindo Ctrl+C na linha de comando.

   Após alguns segundos, a aplicação começa a apresentar registos de chamadas telefónicas no ecrã, à medida que os envia para o hub de eventos.

Alguns dos campos-chave que utilizará neste pedido de deteção de fraude em tempo real são os seguintes:

|**Registo**|**Definição**|
|----------|--------------|
|`CallrecTime`|O carimbo de data/hora da hora de início da chamada. |
|`SwitchNum`|O comutador de telefone utilizado para estabelecer a chamada. Por exemplo, os interruptores são cordas que representam o país/região de origem (EUA, China, Reino Unido, Alemanha ou Austrália). |
|`CallingNum`|O número de telefone do chamador. |
|`CallingIMSI`|A Identidade Internacional de Assinante Móvel (IMSI). Este é o identificador único do chamador. |
|`CalledNum`|O número de telefone do destinatário da chamada. |
|`CalledIMSI`|Identidade Internacional de Assinante Móvel (IMSI). Este é o identificador único do destinatário da chamada. |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>Criar um trabalho stream Analytics para gerir dados de streaming

Agora que tem uma série de eventos de chamadas, pode configurar um trabalho de Stream Analytics. O trabalho irá ler dados do centro de eventos que criou. 

### <a name="create-the-job"></a>Criar o trabalho 

1. No portal Azure, clique em **Criar um trabalho**de Internet de  >  **Coisas**  >  **Stream Analytics**.

2. Nomeie o trabalho `asa_frauddetection_job_demo` , especifique uma subscrição, grupo de recursos e localização.

    É uma boa ideia colocar o trabalho e o centro de eventos na mesma região para melhor desempenho e para que você não pague para transferir dados entre regiões.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png" alt="Create Stream Analytics job in portal" width="300px"/>

3. Clique em **Criar**.

    O trabalho é criado e o portal apresenta detalhes do trabalho. Nada está a correr ainda — tens de configurar o trabalho antes de poder ser iniciado.

### <a name="configure-job-input"></a>Configurar a entrada da tarefa

1. No painel de instrumentos ou no painel **de todos os recursos,** encontre e selecione o `asa_frauddetection_job_demo` trabalho Stream Analytics. 
2. Na secção **visão geral** do painel de trabalho Stream Analytics, clique na caixa **entrada.**

   ![Caixa de entrada sob Topologia no painel de trabalho streaming Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. Clique **em Adicionar entrada de fluxo** e selecione Event **Hub**. Em seguida, preencha a nova página de entrada com as seguintes informações:

   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Alias de entrada  |  CallStream   |  Insira um nome para identificar a entrada do trabalho.   |
   |Subscrição   |  \<Your subscription\> |  Selecione a subscrição Azure que tem o Centro de Eventos que criou.   |
   |Espaço de nomes do hub de eventos  |  asa-eh-ns-demo |  Insira o nome do espaço de nomes do Event Hub.   |
   |Nome do Hub de Eventos  | asa-eh-fraudedetecção-demonstração | Selecione o nome do seu Centro de Eventos.   |
   |Nome da política do Hub de Eventos  | asa-policy-manage-demo | Selecione a política de acesso que criou anteriormente.   |

    </br>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png" alt="Create Stream Analytics input in portal" width="300px"/>


4. Clique em **Criar**.

## <a name="create-queries-to-transform-real-time-data"></a>Criar consultas para transformar dados em tempo real

Neste momento, tem um trabalho de Stream Analytics configurado para ler um fluxo de dados que está a chegar. O próximo passo é criar uma consulta que analise os dados em tempo real. Stream Analytics suporta um modelo de consulta simples e declarativo que descreve transformações para o processamento em tempo real. As consultas usam uma linguagem semelhante ao SQL que tem algumas extensões específicas do Stream Analytics. 

Uma simples consulta pode apenas ler todos os dados que chegam. No entanto, muitas vezes cria consultas que procuram dados específicos ou para relacionamentos nos dados. Nesta secção do tutorial, você cria e testa várias consultas para aprender algumas maneiras em que você pode transformar um fluxo de entrada para análise. 

As consultas que cria aqui apenas mostrarão os dados transformados para o ecrã. Numa secção posterior, irá configurar um lavatório de saída e uma consulta que escreve os dados transformados para aquele lavatório.

Para saber mais sobre o idioma, consulte a [Referência de Idioma Azure Stream Analytics.](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

### <a name="get-sample-data-for-testing-queries"></a>Obtenha dados de amostra para testes de consultas

A aplicação TelcoGenerator está a enviar registos de chamadas para o centro de eventos, e o seu trabalho stream Analytics está configurado para ler a partir do centro de eventos. Pode usar uma consulta para testar o trabalho para se certificar de que está a ler corretamente. Para testar uma consulta na consola Azure, precisa de dados de amostra. Para esta passagem, você vai extrair dados de amostra do fluxo que está vindo para o centro do evento.

1. Certifique-se de que a aplicação TelcoGenerator está a executar e a produzir registos de chamadas.
2. No portal, volte ao painel de trabalho do Streaming Analytics. (Se fechou o painel, procure `asa_frauddetection_job_demo` no painel de todos os **recursos.)**
3. Clique na **caixa de consulta.** O Azure lista as entradas e saídas configuradas para o trabalho e permite criar uma consulta que lhe permite transformar o fluxo de entrada à medida que é enviado para a saída.
4. No **painel de consultas,** clique nos pontos ao lado da `CallStream` entrada e, em seguida, selecione **os dados da amostra a partir da entrada**.

   ![Opções de menu para utilizar dados de amostra para a entrada de emprego em Streaming Analytics, com "Dados de amostra da entrada" selecionados](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)


5. Desa esta hora a 3 e, em seguida, clique **em OK**. **Minutes** 
    
   ![Opções para fluxo de entrada de amostragem com 3 minutos selecionados](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    Azure recolhe dados no valor de 3 minutos do fluxo de entrada e notifica-o quando os dados da amostra estiverem prontos. (Isto demora um pouco.) 

Os dados de exemplo são temporariamente armazenados e estão disponíveis enquanto a janela de consulta estiver aberta. Se fechar a janela de consulta, os dados de exemplo são eliminados e terá de criar um novo conjunto de dados de exemplo. 

Como alternativa, pode obter um ficheiro .json que tenha dados [de amostras no GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json), e depois carregar esse ficheiro .json para usar como dados de amostra para a `CallStream` entrada. 

### <a name="test-using-a-pass-through-query"></a>Teste utilizando uma consulta de passagem

Se quiser arquivar todos os eventos, pode utilizar uma consulta de passagem para ler todos os campos na carga útil do evento.

1. Na janela de consulta, insira esta consulta:
        
   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >Tal como acontece com o SQL, as palavras-chave não são sensíveis a casos, e o espaço em branco não é significativo.

    Nesta consulta, `CallStream` é o pseudónimo que especificou quando criou a entrada. Se usou um pseudónimo diferente, use esse nome.

2. Clique **em Teste**.

    O trabalho Stream Analytics executa a consulta com os dados da amostra e mostra a saída na parte inferior da janela. Os resultados indicam que o Centro de Eventos e o trabalho de Streaming Analytics estão configurados corretamente. (Como notado, mais tarde irá criar um lavatório de saída para o qual a consulta pode escrever dados.)

   ![Stream Analytics saída de trabalho, mostrando 73 registos gerados](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    O número exato de registos que vê dependerá de quantos registos foram capturados na sua amostra de 3 minutos.
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Reduzir o número de campos usando uma projeção de coluna

Em muitos casos, a sua análise não precisa de todas as colunas do fluxo de entrada. Você pode usar uma consulta para projetar um conjunto menor de campos devolvidos do que na consulta de passagem.

1. Altere a consulta no editor de código para o seguinte:

    ```SQL
    SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
    FROM 
        CallStream
    ```

2. Clique em **Testar** novamente. 

   ![Stream Analytics produção de trabalho para projeção mostra 25 registos](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Contagem de chamadas por região: Janela de tropeçar com agregação

Suponha que queira contar o número de chamadas recebidas por região. Nos dados de streaming, quando pretende executar funções agregadas como a contagem, é necessário segmentar o fluxo em unidades temporais (uma vez que o fluxo de dados em si é efetivamente infinito). Fá-lo utilizando uma [função de janela](stream-analytics-window-functions.md)Streaming Analytics. Em seguida, pode trabalhar com os dados dentro dessa janela como uma unidade.

Para esta transformação, você quer uma sequência de janelas temporais que não se sobrepõem - cada janela terá um conjunto discreto de dados que você pode agrupar e agregar. Este tipo de janela é referido como uma *janela de tumbling*. Dentro da janela Tumbling, você pode obter uma contagem das chamadas recebidas agrupadas por `SwitchNum` , que representa o país/região de onde a chamada originou. 

1. Altere a consulta no editor de código para o seguinte:

    ```SQL
    SELECT 
        System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
    FROM
        CallStream TIMESTAMP BY CallRecTime 
    GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
    ```

    Esta consulta utiliza a `Timestamp By` palavra-chave na `FROM` cláusula para especificar qual o campo de tempos no fluxo de entrada para definir a janela Tumbling. Neste caso, a janela divide os dados em segmentos pelo `CallRecTime` campo em cada registo. (Se não for especificado nenhum campo, a operação de janela utiliza o tempo que cada evento chega ao centro do evento. Consulte "Tempo de chegada vs tempo de aplicação" em [Stream Analytics Consulta de Idioma .](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) 

    A projeção inclui `System.Timestamp` , que devolve um tempotando para o final de cada janela. 

    Para especificar que pretende utilizar uma janela de Tumbling, utilize a função [TUMBLINGWINDOW](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) na `GROUP BY` cláusula. Na função, especifique uma unidade de tempo (em qualquer lugar de um microsegundo a um dia) e um tamanho de janela (quantas unidades). Neste exemplo, a janela Tumbling consiste em intervalos de 5 segundos, para que você obtenha uma contagem por país/região por cada 5 segundos de chamadas.

2. Clique em **Testar** novamente. Nos resultados, note que os intervalos de tempo em **WindowEnd** estão em incrementos de 5 segundos.

   ![Stream Analytics produção de trabalho para agregação mostrando 13 registos](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>Detete a fraude SIM usando uma união automática

Para este exemplo, considere o uso fraudulento como chamadas originárias do mesmo utilizador, mas em locais diferentes dentro de 5 segundos um do outro. Por exemplo, o mesmo utilizador não pode legitimamente fazer uma chamada do E.U.A. e da Austrália ao mesmo tempo. 

Para verificar estes casos, pode utilizar uma união auto-junção dos dados de streaming para se juntar ao fluxo para si mesmo com base no `CallRecTime` valor. Em seguida, pode procurar registos de chamadas onde o `CallingIMSI` valor (o número de origem) é o mesmo, mas o `SwitchNum` valor (país/região de origem) não é o mesmo.

Quando utilizar uma junção com dados de streaming, a junção deve fornecer alguns limites para a distância em que as linhas correspondentes podem ser separadas a tempo. (Como já foi notado anteriormente, os dados de streaming são efetivamente infinitos.) Os limites de tempo para a relação são especificados dentro `ON` da cláusula da junção, utilizando a `DATEDIFF` função. Neste caso, a junção baseia-se num intervalo de 5 segundos de dados de chamada.

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

    Esta consulta é como qualquer unção SQL, exceto para a `DATEDIFF` função na junção. Esta versão `DATEDIFF` é específica para streaming Analytics, e deve aparecer na `ON...BETWEEN` cláusula. Os parâmetros são uma unidade de tempo (segundos neste exemplo) e os pseudónimos das duas fontes para a junção. Isto é diferente da função SQL `DATEDIFF` padrão.

    A `WHERE` cláusula inclui a condição que sinaliza a chamada fraudulenta: os interruptores de origem não são os mesmos. 

2. Clique em **Testar** novamente. 

   ![Stream Analytics produção de trabalho para auto-junção, mostrando 6 registos gerados](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. Clique em **Guardar** para salvar a consulta de auto-junção como parte do trabalho de Streaming Analytics. (Não guarda os dados da amostra.)

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png" alt="Save Stream Analytics query in portal" width="300px"/>

## <a name="create-an-output-sink-to-store-transformed-data"></a>Criar um lavatório de saída para armazenar dados transformados

Você definiu um fluxo de eventos, uma entrada de centro de eventos para ingerir eventos, e uma consulta para realizar uma transformação sobre o fluxo. O último passo é definir um lavatório de saída para o trabalho , isto é, um lugar para escrever o fluxo transformado para. 

Você pode usar muitos recursos como pias de saída - uma base de dados SQL Server, armazenamento de mesa, armazenamento de Data Lake, Power BI, e até mesmo outro centro de eventos. Para este tutorial, você escreverá o stream para Azure Blob Storage, que é uma escolha típica para recolher informações de eventos para análise posterior, uma vez que acomoda dados não estruturados.

Se tiver uma conta de armazenamento de bolhas existente, pode usá-la. Para este tutorial, você vai aprender como criar uma nova conta de armazenamento.

### <a name="create-an-azure-blob-storage-account"></a>Criar uma conta de armazenamento Azure Blob

1. A partir do canto superior esquerdo do portal Azure, selecione **Criar uma**conta de  >  **armazenamento**  >  **de**recursos . Preencha a página de trabalho da conta de armazenamento com **o nome** definido para "asaehstorage", **localização** definida para "East US", **grupo de recursos** definido para "asa-eh-ns-rg" (hospedar a conta de armazenamento no mesmo grupo de recursos que o trabalho de Streaming para maior desempenho). As restantes definições podem ser mantidas nos respetivos valores predefinidos.  

   ![Criar conta de armazenamento no portal Azure](./media/stream-analytics-real-time-fraud-detection/stream-analytics-storage-account-create.png)

2. No portal Azure, volte ao painel de trabalho streaming Analytics. (Se fechou o painel, procure `asa_frauddetection_job_demo` no painel de todos os **recursos.)**

3. Na secção **De Topologia de Trabalho,** clique na caixa **de saída.**

4. No painel **outputs,** clique em **Adicionar** e selecione **o armazenamento blob**. Em seguida, preencha a nova página de saída com as seguintes informações:

   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Alias de saída  |  CallStream-FraudulentCalls   |  Insira um nome para identificar a saída do trabalho.   |
   |Subscrição   |  \<Your subscription\> |  Selecione a subscrição do Azure que tem a conta de armazenamento que criou. A conta de armazenamento pode estar na mesma subscrição ou numa diferente. Este exemplo assume que criou a conta de armazenamento na mesma subscrição. |
   |Conta de armazenamento  |  asaehstorage |  Insira o nome da conta de armazenamento que criou. |
   |Contentor  | asa-fraudulentos chamadas-demo | Escolha Criar novo e insira um nome de recipiente. |

    <br/>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png" alt="Create blob output for Stream Analytics job" width="300px"/>
    
5. Clique em **Guardar**. 


## <a name="start-the-streaming-analytics-job"></a>Inicie o trabalho de Streaming Analytics

O trabalho está agora configurado. Especificou uma entrada (o centro do evento), uma transformação (a consulta para procurar chamadas fraudulentas) e uma saída (armazenamento de bolhas). Agora pode começar o trabalho. 

1. Certifique-se de que a aplicação TelcoGenerator está em funcionamento.

2. No painel de trabalho, clique em **Iniciar**. No painel **de trabalho Iniciar,** para a hora de início da saída de Trabalho, selecione **Agora**. 

   ![Inicie o trabalho stream analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start.png)



## <a name="examine-the-transformed-data"></a>Examinar os dados transformados

Agora tem um trabalho completo de Streaming Analytics. O trabalho é examinar um fluxo de metadados de chamadas telefónicas, à procura de chamadas telefónicas fraudulentas em tempo real, e escrever informações sobre essas chamadas fraudulentas para o armazenamento. 

Para completar este tutorial, talvez queira ver os dados que estão a ser capturados pelo trabalho do Streaming Analytics. Os dados estão a ser escritos para o Azure Blog Storage em pedaços (ficheiros). Pode utilizar qualquer ferramenta que leia o Azure Blob Storage. Como indicado na secção Pré-Requisitos, pode utilizar extensões Azure no Visual Studio, ou pode utilizar uma ferramenta como [o Azure Storage Explorer](https://storageexplorer.com/) ou o [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

Quando examina o conteúdo de um ficheiro no armazenamento de bolhas, vê algo como o seguinte:

   ![Armazenamento de blob Azure com saída streaming Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>Limpar os recursos

Existem artigos adicionais que continuam com o cenário de deteção de fraudes e usam os recursos que criou neste tutorial. Se quiser continuar, consulte as sugestões nos **próximos passos**.

No entanto, se já acabou e não precisa dos recursos que criou, pode eliminá-los para que não incorre em acusações desnecessárias do Azure. Nesse caso, sugerimos que faça o seguinte:

1. Pare o trabalho de Streaming Analytics. No painel **Jobs,** clique em **Stop** no topo.
2. Pare a aplicação Telco Generator. Na janela de comando onde iniciou a aplicação, prima Ctrl+C.
3. Se criou uma nova conta de armazenamento de bolhas apenas para este tutorial, apague-a. 
4. Apague o trabalho de Streaming Analytics.
5. Apague o centro de eventos.
6. Elimine o espaço de nome do centro de eventos.

## <a name="get-support"></a>Obter suporte

Para obter mais assistência, experimente a [página de perguntas do Microsoft Q&A para o Azure Stream Analytics](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Passos seguintes

Pode continuar este tutorial com o seguinte artigo:

* [Stream Analytics e Power BI: Um dashboard de análise em tempo real para dados de streaming](stream-analytics-power-bi-dashboard.md). Este artigo mostra-lhe como enviar a saída TelCo do trabalho Stream Analytics para Power BI para visualização e análise em tempo real.

Para obter mais informações sobre o Stream Analytics em geral, consulte estes artigos:

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
