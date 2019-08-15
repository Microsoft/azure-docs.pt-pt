---
title: Detecção de fraudes em tempo real usando Azure Stream Analytics
description: Saiba como criar uma solução de detecção de fraudes em tempo real com Stream Analytics. Use um hub de eventos para processamento de eventos em tempo real.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 19c9448b6a743302eb81bb208444336d6435f114
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947052"
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Comece a usar o Azure Stream Analytics: Deteção de fraudes em tempo real

Este tutorial fornece uma ilustração de ponta a ponta de como usar Azure Stream Analytics. Saiba como: 

* Traga eventos de streaming para uma instância de hubs de eventos do Azure. Neste tutorial, você usará um aplicativo que simula um fluxo de registros de metadados de telefone celular.

* Escrever consultas do tipo SQL Stream Analytics para transformar dados, agregar informações ou procurar padrões. Você verá como usar uma consulta para examinar o fluxo de entrada e procurar por chamadas que possam ser fraudulentas.

* Envie os resultados para um coletor de saída (armazenamento) que você pode analisar para obter informações adicionais. Nesse caso, você enviará os dados de chamada suspeitas para o armazenamento de BLOBs do Azure.

Este tutorial usa o exemplo de detecção de fraudes em tempo real com base em dados de chamada telefônica. A técnica ilustrada também é adequada para outros tipos de detecção de fraudes, como fraude de cartão de crédito ou roubo de identidade. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Cenário: Detecção de fraudes de telecomunicações e SIM em tempo real

Uma empresa de telecomunicações tem um grande volume de dados para chamadas de entrada. A empresa deseja detectar chamadas fraudulentas em tempo real para que elas possam notificar os clientes ou desligar o serviço para um número específico. Um tipo de fraude SIM envolve várias chamadas da mesma identidade ao mesmo tempo, mas em locais geograficamente diferentes. Para detectar esse tipo de fraude, a empresa precisa examinar os registros de telefone de entrada e procurar padrões específicos — nesse caso, para chamadas feitas ao mesmo tempo em diferentes países/regiões. Todos os registros de telefone que se enquadram nessa categoria são gravados no armazenamento para análise subsequente.

## <a name="prerequisites"></a>Pré-requisitos

Neste tutorial, você simulará dados de chamada telefônica usando um aplicativo cliente que gera metadados de chamada telefônica de exemplo. Alguns dos registros que o aplicativo produz se parecerem com chamadas fraudulentas. 

Antes de começar, certifique-se de que tem o seguinte:

* Uma conta do Azure.
* O aplicativo do gerador de eventos de chamada, [TelcoGenerator. zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip), que pode ser baixado do centro de download da Microsoft. Descompacte este pacote em uma pasta no computador. Se você quiser ver o código-fonte e executar o aplicativo em um depurador, poderá obter o código-fonte do aplicativo do [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator). 

    >[!NOTE]
    >O Windows pode bloquear o arquivo. zip baixado. Se você não puder descompactá-lo, clique com o botão direito do mouse no arquivo e selecione **Propriedades**. Se você vir a mensagem "este arquivo veio de outro computador e pode ser bloqueado para ajudar a proteger este computador", selecione a opção **desbloquear** e, em seguida, clique em **aplicar**.

Se você quiser examinar os resultados do trabalho do Stream Analytics, também precisará de uma ferramenta para exibir o conteúdo de um contêiner de armazenamento de BLOBs do Azure. Se você usar o Visual Studio, poderá usar as [Ferramentas do Azure para Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) ou o [Visual Studio Cloud Explorer](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer). Como alternativa, você pode instalar ferramentas autônomas como [Gerenciador de armazenamento do Azure](https://storageexplorer.com/) ou [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>Criar um hub de eventos do Azure para eventos de ingestão

Para analisar um fluxo de dados, você o ingeri no Azure. Uma maneira comum de ingerir dados é usar os [hubs de eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md), que permite que você ingerir milhões de eventos por segundo e, em seguida, processe e armazene as informações do evento. Para este tutorial, você criará um hub de eventos e, em seguida, fará com que o aplicativo do gerador de eventos de chamada envie dados de chamada para esse Hub de eventos. Para obter mais informações sobre os hubs de eventos, consulte a [documentação do barramento de serviço do Azure](https://docs.microsoft.com/azure/service-bus/).

>[!NOTE]
>Para obter uma versão mais detalhada deste procedimento, consulte [criar um namespace de hubs de eventos e um hub de eventos usando o portal do Azure](../event-hubs/event-hubs-create.md). 

### <a name="create-a-namespace-and-event-hub"></a>Criar um hub de eventos e de espaço de nomes
Neste procedimento, você primeiro cria um namespace de Hub de eventos e, em seguida, adiciona um hub de eventos a esse namespace. Os namespaces do hub de eventos são usados para agrupar logicamente as instâncias do barramento de eventos relacionadas. 

1. Faça logon no portal do Azure e clique em **criar um recurso** > **Internet das coisas** > **Hub de eventos**. 

2. No painel **criar namespace** , insira um nome `<yourname>-eh-ns-demo`de namespace, como. Você pode usar qualquer nome para o namespace, mas o nome deve ser válido para uma URL e deve ser exclusivo no Azure. 
    
3. Selecione uma assinatura e crie ou escolha um grupo de recursos e, em seguida, clique em **criar**.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png" alt="Create event hub namespace in Azure portal" width="300px"/>

4. Quando o namespace concluir a implantação, localize o namespace do hub de eventos na lista de recursos do Azure. 

5. Clique no novo namespace e, no painel namespace, clique em **Hub de eventos**.

   ![O botão Adicionar Hub de eventos para criar um novo hub de eventos](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
6. Nomeie o novo hub `asa-eh-frauddetection-demo`de eventos. Pode utilizar um nome diferente. Se você fizer isso, anote-o, pois você precisará do nome mais tarde. Você não precisa definir nenhuma outra opção para o Hub de eventos no momento.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png" alt="Name event hub in Azure portal" width="400px"/>
    
 
7. Clique em **Criar**.

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Conceder acesso ao hub de eventos e obter uma cadeia de ligação

Antes que um processo possa enviar dados para um hub de eventos, o Hub de eventos deve ter uma política que permita o acesso apropriado. A política de acesso produz uma cadeia de ligação que inclui as informações de autorização.

1.  No painel namespace do evento, clique em **hubs de eventos** e, em seguida, clique no nome do novo hub de eventos.

2.  No painel Hub de eventos, clique em **políticas de acesso compartilhado** e, em seguida, clique em  **+ &nbsp;adicionar**.

    >[!NOTE]
    >Verifique se você está trabalhando com o Hub de eventos, não o namespace do hub de eventos.

3.  Adicione uma política chamada `sa-policy-manage-demo` e para **declaração**, selecione **gerenciar**.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png" alt="Create shared access policy for Stream Analytics" width="300px"/>
 
4.  Clique em **Criar**.

5.  Depois que a política tiver sido implantada, clique nela na lista de políticas de acesso compartilhado.

6.  Localize a caixa rotulada **cadeia de conexão-chave primária** e clique no botão copiar ao lado da cadeia de conexão. 

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png" alt="Stream Analytics shared access policy" width="300px"/>
 
7.  Cole a cadeia de ligação num editor de texto. Você precisa dessa cadeia de conexão para a próxima seção, depois de fazer algumas pequenas edições nela.

    A cadeia de conexão tem esta aparência:

        Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=asa-eh-frauddetection-demo

    Observe que a cadeia de conexão contém vários pares chave-valor, separados por ponto- `Endpoint`e `SharedAccessKeyName`- `SharedAccessKey`vírgula: `EntityPath`,, e.  

## <a name="configure-and-start-the-event-generator-application"></a>Configurar e iniciar o aplicativo gerador de eventos

Antes de iniciar o aplicativo TelcoGenerator, você deve configurá-lo para que ele envie registros de chamada para o Hub de eventos que você criou.

### <a name="configure-the-telcogenerator-app"></a>Configurar o aplicativo TelcoGenerator

1. No editor em que você copiou a cadeia de conexão, anote o `EntityPath` valor e, em seguida, remova o `EntityPath` par (não se esqueça de remover o ponto e vírgula que o precede). 

2. Na pasta em que você descompactou o arquivo TelcoGenerator. zip, abra o arquivo telcodatagen. exe. config em um editor. (Há mais de um arquivo. config, portanto, certifique-se de abrir o correto.)

3. `<appSettings>` No elemento:

   * Defina o valor da `EventHubName` chave para o nome do hub de eventos (ou seja, para o valor do caminho da entidade).
   * Defina o valor da `Microsoft.ServiceBus.ConnectionString` chave para a cadeia de conexão. 

   A `<appSettings>` seção se parecerá com o exemplo a seguir. (Para maior clareza, as linhas são encapsuladas e alguns caracteres foram removidos do token de autorização.)

   ![O arquivo de configuração TelcoGenerator mostra o nome do hub de eventos e a cadeia de conexão](./media/stream-analytics-real-time-fraud-detection/stream-analytics-telcogenerator-config-file-app-settings.png)
 
4. Guarde o ficheiro. 

### <a name="start-the-app"></a>Iniciar o aplicativo
1.  Abra uma janela de comando e altere para a pasta onde o aplicativo TelcoGenerator é descompactado.

2.  Introduza o seguinte comando:

   ```cmd
   telcodatagen.exe 1000 0.2 2
   ```

   Os parâmetros são: 

   * Número de CDRs por hora. 
   * Probabilidade de fraude de cartão SIM: Com que frequência, como uma porcentagem de todas as chamadas, que o aplicativo deve simular uma chamada fraudulenta. O valor 0.2 significa que cerca de 20% dos registos de chamada parecerão fraudulentos.
   * Duração em horas. O número de horas que o aplicativo deve ser executado. Você também pode interromper o aplicativo a qualquer momento pressionando CTRL + C na linha de comando.

   Após alguns segundos, a aplicação começa a apresentar registos de chamadas telefónicas no ecrã, à medida que os envia para o hub de eventos.

Alguns dos campos principais que você usará neste aplicativo de detecção de fraudes em tempo real são os seguintes:

|**Registo**|**Definição**|
|----------|--------------|
|`CallrecTime`|O carimbo de data/hora da hora de início da chamada. |
|`SwitchNum`|O comutador de telefone utilizado para estabelecer a chamada. Para este exemplo, as opções são cadeias de caracteres que representam o país/região de origem (EUA, China, Reino Unido, Alemanha ou Austrália). |
|`CallingNum`|O número de telefone do chamador. |
|`CallingIMSI`|A Identidade Internacional de Assinante Móvel (IMSI). Esse é o identificador exclusivo do chamador. |
|`CalledNum`|O número de telefone do destinatário da chamada. |
|`CalledIMSI`|Identidade Internacional de Assinante Móvel (IMSI). Esse é o identificador exclusivo do destinatário da chamada. |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>Criar um trabalho de Stream Analytics para gerenciar dados de streaming

Agora que você tem um fluxo de eventos de chamada, você pode configurar um trabalho de Stream Analytics. O trabalho lerá os dados do hub de eventos que você configurou. 

### <a name="create-the-job"></a>Criar o trabalho 

1. No portal do Azure, clique em **criar um recurso** > **Internet das coisas** > **trabalho de Stream Analytics**.

2. Nomeie o trabalho `asa_frauddetection_job_demo`, especifique uma assinatura, um grupo de recursos e um local.

    É uma boa ideia posicionar o trabalho e o Hub de eventos na mesma região para obter o melhor desempenho e, assim, você não paga para transferir dados entre regiões.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png" alt="Create Stream Analytics job in portal" width="300px"/>

3. Clique em **Criar**.

    O trabalho é criado e o portal exibe detalhes do trabalho. No entanto, nada está em execução — você precisa configurar o trabalho antes que ele possa ser iniciado.

### <a name="configure-job-input"></a>Configurar a entrada da tarefa

1. No painel ou em **todos os recursos** , localize e selecione o trabalho `asa_frauddetection_job_demo` de Stream Analytics. 
2. Na seção **visão geral** do painel trabalho Stream Analytics, clique na caixa de **entrada** .

   ![Caixa de entrada em topologia no painel de trabalho do Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. Clique em **Adicionar entrada de fluxo** e selecione **Hub de eventos**. Em seguida, preencha a página nova entrada com as seguintes informações:

   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Alias de entrada  |  CallStream   |  Introduza um nome para identificar a entrada da tarefa.   |
   |Subscription   |  \<A sua subscrição\> |  Selecione a assinatura do Azure que tem o Hub de eventos que você criou.   |
   |Espaço de nomes do hub de eventos  |  asa-eh-ns-demo |  Insira o nome do namespace do hub de eventos.   |
   |O nome do hub de eventos  | asa-eh-frauddetection-demo | Selecione o nome do hub de eventos.   |
   |Nome da política do Hub de Eventos  | asa-política-gerenciar-demonstração | Selecione a política de acesso que você criou anteriormente.   |

    </br>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png" alt="Create Stream Analytics input in portal" width="300px"/>


4. Clique em **Criar**.

## <a name="create-queries-to-transform-real-time-data"></a>Criar consultas para transformar dados em tempo real

Neste ponto, você tem um trabalho Stream Analytics configurado para ler um fluxo de dados de entrada. A próxima etapa é criar uma consulta que analise os dados em tempo real. O Stream Analytics dá suporte a um modelo de consulta simples e declarativo que descreve as transformações para o processamento em tempo real. As consultas usam uma linguagem semelhante a SQL que tem algumas extensões específicas para Stream Analytics. 

Uma consulta simples pode apenas ler todos os dados de entrada. No entanto, muitas vezes você cria consultas que procuram dados específicos ou relações nos dados. Nesta seção do tutorial, você cria e testa várias consultas para saber algumas maneiras pelas quais você pode transformar um fluxo de entrada para análise. 

As consultas que você criar aqui só exibirão os dados transformados na tela. Em uma seção posterior, você configurará um coletor de saída e uma consulta que grava os dados transformados nesse coletor.

Para saber mais sobre a linguagem, consulte a [referência de linguagem de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

### <a name="get-sample-data-for-testing-queries"></a>Obter dados de exemplo para testar consultas

O aplicativo TelcoGenerator está enviando registros de chamada para o Hub de eventos e seu trabalho de Stream Analytics está configurado para ler do hub de eventos. Você pode usar uma consulta para testar o trabalho para certificar-se de que ele está sendo lido corretamente. Para testar uma consulta no console do Azure, você precisa de dados de exemplo. Para esta explicação, você extrairá dados de exemplo do fluxo que está chegando ao Hub de eventos.

1. Verifique se o aplicativo TelcoGenerator está em execução e produzindo registros de chamada.
2. No portal, retorne ao painel de trabalho do Stream Analytics. (Se você fechou o painel, procure `asa_frauddetection_job_demo` no painel **todos os recursos** .)
3. Clique na caixa **consulta** . O Azure lista as entradas e saídas configuradas para o trabalho e permite que você crie uma consulta que permite transformar o fluxo de entrada conforme ele é enviado para a saída.
4. No painel de **consulta** , clique nos pontos ao lado da `CallStream` entrada e, em seguida, selecione **dados de exemplo da entrada**.

   ![Opções de menu para usar dados de exemplo para a entrada de trabalho do Stream Analytics, com "dados de exemplo da entrada" selecionados](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)


5. Defina **minutos** como 3 e clique em **OK**. 
    
   ![Opções para amostragem de fluxo de entrada com 3 minutos selecionados](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    O Azure amostras de 3 minutos de dados do fluxo de entrada e o notifica quando os dados de exemplo estão prontos. (Isso demora um pouco.) 

Os dados de exemplo são temporariamente armazenados e estão disponíveis enquanto a janela de consulta estiver aberta. Se fechar a janela de consulta, os dados de exemplo são eliminados e terá de criar um novo conjunto de dados de exemplo. 

Como alternativa, você pode obter um arquivo. JSON que contém dados [de exemplo do GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json)e, em seguida, carregar esse arquivo. JSON para usar como dados de exemplo para `CallStream` a entrada. 

### <a name="test-using-a-pass-through-query"></a>Testar usando uma consulta de passagem

Se você quiser arquivar todos os eventos, poderá usar uma consulta de passagem para ler todos os campos na carga do evento.

1. Na janela de consulta, insira esta consulta:
        
   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >Assim como acontece com o SQL, as palavras-chave não diferenciam maiúsculas de minúsculas e o espaço em branco não é significativo.

    Nessa consulta, `CallStream` é o alias que você especificou quando criou a entrada. Se você usou um alias diferente, use esse nome em vez disso.

2. Clique em **testar**.

    O trabalho de Stream Analytics executa a consulta em relação aos dados de exemplo e exibe a saída na parte inferior da janela. Os resultados indicam que o Hub de eventos e o trabalho do Stream Analytics estão configurados corretamente. (Conforme observado, posteriormente, você criará um coletor de saída no qual a consulta pode gravar dados.)

   ![Stream Analytics saída de trabalho, mostrando os registros 73 gerados](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    O número exato de registros que você vê dependerá de quantos registros foram capturados em seu exemplo de 3 minutos.
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Reduzir o número de campos usando uma projeção de coluna

Em muitos casos, sua análise não precisa de todas as colunas do fluxo de entrada. Você pode usar uma consulta para projetar um conjunto menor de campos retornados do que na consulta de passagem.

1. Altere a consulta no editor de código para o seguinte:

   ```SQL
   SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
   FROM 
       CallStream
   ```

2. Clique em **testar** novamente. 

   ![Stream Analytics saída de trabalho para projeção mostra 25 registros](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Contar chamadas de entrada por região: Janela em cascata com agregação

Suponha que você deseja contar o número de chamadas de entrada por região. No streaming de dados, quando você deseja executar funções de agregação como contagem, você precisa segmentar o fluxo em unidades temporais (já que o fluxo de dados em si é efetivamente infinito). Você faz isso usando uma função de [janela](stream-analytics-window-functions.md)do Stream Analytics. Em seguida, você pode trabalhar com os dados dentro dessa janela como uma unidade.

Para essa transformação, você deseja uma sequência de janelas temporais que não se sobrepõem – cada janela terá um conjunto discreto de dados que você pode agrupar e agregar. Esse tipo de janela é chamado de *janela em cascata*. Na janela em cascata, você pode obter uma contagem das chamadas de entrada agrupadas por `SwitchNum`, que representa o país/região onde a chamada foi originada. 

1. Altere a consulta no editor de código para o seguinte:

        ```SQL
        SELECT 
            System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
        FROM
            CallStream TIMESTAMP BY CallRecTime 
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
        ```

    Essa consulta usa a `Timestamp By` palavra-chave `FROM` na cláusula para especificar qual campo de carimbo de data/hora no fluxo de entrada usará para definir a janela em cascata. Nesse caso, a janela divide os dados em segmentos pelo `CallRecTime` campo em cada registro. (Se nenhum campo for especificado, a operação de janela usará a hora em que cada evento chega no Hub de eventos. Consulte "hora de chegada vs. hora do aplicativo" em [Stream Analytics referência de linguagem de consulta](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). 

    A projeção `System.Timestamp`inclui, que retorna um carimbo de data/hora para o final de cada janela. 

    Para especificar que você deseja usar uma janela em cascata, use a função [TUMBLINGWINDOW](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) na `GROUP BY` cláusula. Na função, você especifica uma unidade de tempo (em qualquer lugar de um segundo a um dia) e um tamanho de janela (quantas unidades). Neste exemplo, a janela em cascata consiste em intervalos de 5 segundos, portanto, você receberá uma contagem por país/região para cada 5 segundos de chamadas.

2. Clique em **testar** novamente. Nos resultados, observe que os carimbos de data/hora em **WindowEnd** estão em incrementos de 5 segundos.

   ![Stream Analytics saída de trabalho para agregação mostrando 13 registros](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>Detectar fraudes SIM usando uma auto-associação

Para este exemplo, considere o uso de fraudulento para chamadas que se originam do mesmo usuário, mas em locais diferentes dentro de 5 segundos entre si. Por exemplo, o mesmo utilizador não pode legitimamente fazer uma chamada do E.U.A. e da Austrália ao mesmo tempo. 

Para verificar esses casos, você pode usar uma autojunção dos dados de streaming para unir o fluxo a si mesmo com base no `CallRecTime` valor. Em seguida, você pode procurar registros de chamada `CallingIMSI` em que o valor (o número de origem) é o mesmo `SwitchNum` , mas o valor (país/região de origem) não é o mesmo.

Quando você usa uma junção com dados de streaming, a junção deve fornecer alguns limites sobre até onde as linhas correspondentes podem ser separadas no tempo. (Conforme observado anteriormente, os dados de streaming são efetivamente intermináveis.) Os limites de tempo para a relação são especificados dentro da `ON` cláusula da junção, usando a `DATEDIFF` função. Nesse caso, a junção é baseada em um intervalo de 5 segundos de dados de chamada.

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

    Essa consulta é como qualquer junção SQL, exceto a `DATEDIFF` função na junção. Esta versão do `DATEDIFF` é específica para o `ON...BETWEEN` streaming Analytics e deve aparecer na cláusula. Os parâmetros são uma unidade de tempo (segundos neste exemplo) e os aliases das duas fontes para a junção. Isso é diferente da função SQL `DATEDIFF` padrão.

    A `WHERE` cláusula inclui a condição que sinaliza a chamada fraudulenta: as opções de origem não são as mesmas. 

2. Clique em **testar** novamente. 

   ![Stream Analytics saída de trabalho para autojunção, mostrando 6 registros gerados](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. Clique em **salvar** para salvar a consulta de auto-associação como parte do trabalho do Stream Analytics. (Ele não salva os dados de exemplo.)

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png" alt="Save Stream Analytics query in portal" width="300px"/>

## <a name="create-an-output-sink-to-store-transformed-data"></a>Criar um coletor de saída para armazenar dados transformados

Você definiu um fluxo de eventos, uma entrada do hub de eventos para eventos de ingestão e uma consulta para executar uma transformação sobre o fluxo. A última etapa é definir um coletor de saída para o trabalho, ou seja, um local para gravar o fluxo transformado. 

Você pode usar muitos recursos como coletores de saída — um banco de dados SQL Server, armazenamento de tabelas, armazenamento de Data Lake, Power BI e até mesmo outro hub de eventos. Para este tutorial, você gravará o fluxo no armazenamento de BLOBs do Azure, que é uma opção típica para coletar informações de evento para análise posterior, pois ela acomoda dados não estruturados.

Se você tiver uma conta de armazenamento de BLOBs existente, poderá usá-la. Para este tutorial, você aprenderá a criar uma nova conta de armazenamento.

### <a name="create-an-azure-blob-storage-account"></a>Criar uma conta de armazenamento de BLOBs do Azure

1. No canto superior esquerdo do portal do Azure, selecione **Criar um recurso** > **Armazenamento** > **Conta de armazenamento**. Preencha a página de trabalho da conta de armazenamento com o **nome** definido como "asaehstorage", **local** definido como "leste dos EUA", **grupo de recursos** definido como "asa-eh-NS-RG" (hospede a conta de armazenamento no mesmo grupo de recursos que o trabalho de streaming para aumentar o desempenho) . As restantes definições podem ser mantidas nos respetivos valores predefinidos.  

   ![Criar conta de armazenamento no portal do Azure](./media/stream-analytics-real-time-fraud-detection/stream-analytics-storage-account-create.png)

2. No portal do Azure, retorne ao painel de trabalho do Stream Analytics. (Se você fechou o painel, procure `asa_frauddetection_job_demo` no painel **todos os recursos** .)

3. Na seção **topologia do trabalho** , clique na caixa **saída** .

4. No painel **saídas** , clique em **Adicionar** e selecione **armazenamento**de BLOBs. Em seguida, preencha a página nova saída com as seguintes informações:

   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Alias de saída  |  CallStream-FraudulentCalls   |  Introduza um nome para identificar a saída da tarefa.   |
   |Subscription   |  \<A sua subscrição\> |  Selecione a subscrição do Azure que tem a conta de armazenamento que criou. A conta de armazenamento pode estar na mesma subscrição ou numa diferente. Este exemplo assume que criou a conta de armazenamento na mesma subscrição. |
   |Conta de armazenamento  |  asaehstorage |  Insira o nome da conta de armazenamento que você criou. |
   |Contentor  | asa-fraudulentcalls-demo | Escolha Criar novo e insira um nome de contêiner. |

    <br/>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png" alt="Create blob output for Stream Analytics job" width="300px"/>
    
5. Clique em **Guardar**. 


## <a name="start-the-streaming-analytics-job"></a>Iniciar o trabalho do Stream Analytics

O trabalho agora está configurado. Você especificou uma entrada (o Hub de eventos), uma transformação (a consulta para procurar por chamadas fraudulentas) e uma saída (armazenamento de BLOBs). Agora você pode iniciar o trabalho. 

1. Verifique se o aplicativo TelcoGenerator está em execução.

2. No painel trabalho, clique em **Iniciar**. No painel **Iniciar trabalho** , para hora de início da saída do trabalho, selecione **agora**. 

   ![Iniciar o trabalho de Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start.png)



## <a name="examine-the-transformed-data"></a>Examinar os dados transformados

Agora você tem um trabalho de streaming Analytics completo. O trabalho está examinando um fluxo de metadados de chamada telefônica, procurando por chamadas telefônicas fraudulentas em tempo real e gravando informações sobre essas chamadas fraudulentas para armazenamento. 

Para concluir este tutorial, talvez você queira examinar os dados que estão sendo capturados pelo trabalho do Stream Analytics. Os dados estão sendo gravados no armazenamento de blog do Azure em partes (arquivos). Você pode usar qualquer ferramenta que leia o armazenamento de BLOBs do Azure. Conforme observado na seção pré-requisitos, você pode usar extensões do Azure no Visual Studio ou pode usar uma ferramenta como [Gerenciador de armazenamento do Azure](https://storageexplorer.com/) ou [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

Ao examinar o conteúdo de um arquivo no armazenamento de BLOBs, você verá algo semelhante ao seguinte:

   ![Armazenamento de BLOBs do Azure com saída do streaming Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>Limpar recursos

Há artigos adicionais que continuam com o cenário de detecção de fraude e usam os recursos que você criou neste tutorial. Se você quiser continuar, consulte as sugestões em **próximas etapas**.

No entanto, se você tiver terminado e não precisar dos recursos que criou, poderá excluí-los para não incorrer em encargos do Azure desnecessários. Nesse caso, sugerimos que você faça o seguinte:

1. Pare o trabalho do Stream Analytics. No painel **trabalhos** , clique em **parar** na parte superior.
2. Pare o aplicativo gerador de telecomunicações. Na janela de comando em que você iniciou o aplicativo, pressione CTRL + C.
3. Se você criou uma nova conta de armazenamento de BLOBs apenas para este tutorial, exclua-a. 
4. Exclua o trabalho do Stream Analytics.
5. Exclua o Hub de eventos.
6. Exclua o namespace do hub de eventos.

## <a name="get-support"></a>Obter suporte

Para obter assistência, tente o [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos Seguintes

Você pode continuar este tutorial com o seguinte artigo:

* [Stream Analytics e Power BI: Um painel de análise em tempo real para dados](stream-analytics-power-bi-dashboard.md)de streaming. Este artigo mostra como enviar a saída de TelCo do trabalho de Stream Analytics para Power BI para análise e visualização em tempo real.

Para obter mais informações sobre Stream Analytics em geral, consulte estes artigos:

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
