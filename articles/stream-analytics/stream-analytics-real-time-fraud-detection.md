---
title: Detecção de fraudes em tempo real usando Azure Stream Analytics
description: Saiba como criar uma solução de detecção de fraudes em tempo real com Stream Analytics. Use um hub de eventos para processamento de eventos em tempo real.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 0273a0a729d39de27b9e417c23624992d1d55b42
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064407"
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Introdução ao uso de Azure Stream Analytics: detecção de fraudes em tempo real

Este tutorial fornece uma ilustração de ponta a ponta de como usar Azure Stream Analytics. Saiba como: 

* Traga eventos de streaming para uma instância de hubs de eventos do Azure. Neste tutorial, você usará um aplicativo que simula um fluxo de registros de metadados de telefone celular.

* Escrever consultas do tipo SQL Stream Analytics para transformar dados, agregar informações ou procurar padrões. Você verá como usar uma consulta para examinar o fluxo de entrada e procurar por chamadas que possam ser fraudulentas.

* Envie os resultados para um coletor de saída (armazenamento) que você pode analisar para obter informações adicionais. Nesse caso, você enviará os dados de chamada suspeitas para o armazenamento de BLOBs do Azure.

Este tutorial usa o exemplo de detecção de fraudes em tempo real com base em dados de chamada telefônica. A técnica ilustrada também é adequada para outros tipos de detecção de fraudes, como fraude de cartão de crédito ou roubo de identidade. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Cenário: detecção de fraudes de telecomunicações e SIM em tempo real

Uma empresa de telecomunicações tem um grande volume de dados para chamadas de entrada. A empresa deseja detectar chamadas fraudulentas em tempo real para que elas possam notificar os clientes ou desligar o serviço para um número específico. Um tipo de fraude SIM envolve várias chamadas da mesma identidade ao mesmo tempo, mas em locais geograficamente diferentes. Para detectar esse tipo de fraude, a empresa precisa examinar os registros de telefone de entrada e procurar padrões específicos — nesse caso, para chamadas feitas ao mesmo tempo em diferentes países/regiões. Todos os registros de telefone que se enquadram nessa categoria são gravados no armazenamento para análise subsequente.

## <a name="prerequisites"></a>Pré-requisitos

Neste tutorial, você simulará dados de chamada telefônica usando um aplicativo cliente que gera metadados de chamada telefônica de exemplo. Alguns dos registros que o aplicativo produz se parecerem com chamadas fraudulentas. 

Antes de começar, certifique-se de que tem o seguinte:

* Uma conta do Azure.
* A aplicação geradora de eventos de chamada, [TelcoGenerator.zip,](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip)que pode ser descarregada a partir do Microsoft Download Center. Descompacte este pacote em uma pasta no computador. Se quiser ver o código fonte e executar a aplicação num descato, pode obter o código fonte da aplicação do [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator). 

    >[!NOTE]
    >O Windows pode bloquear o arquivo. zip baixado. Se não conseguir desapertar, clique no ficheiro e selecione **Propriedades**. Se vir a mensagem "Este ficheiro veio de outro computador e pode ser bloqueado para ajudar a proteger este computador", selecione a opção **Desbloquear** e, em seguida, clique em **Aplicar**.

Se você quiser examinar os resultados do trabalho do Stream Analytics, também precisará de uma ferramenta para exibir o conteúdo de um contêiner de armazenamento de BLOBs do Azure. Se utilizar o Estúdio Visual, pode utilizar [ferramentas Azure para estúdio visual](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) ou [visual studio Cloud Explorer.](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) Em alternativa, pode instalar ferramentas autónomas como [o Azure Storage Explorer](https://storageexplorer.com/) ou o [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>Criar um hub de eventos do Azure para eventos de ingestão

Para analisar um fluxo de dados, *ingere-lo* no Azure. Uma forma típica de ingerir dados é usar o [Azure Event Hubs,](../event-hubs/event-hubs-what-is-event-hubs.md)que permite ingerir milhões de eventos por segundo e depois processar e armazenar as informações do evento. Para este tutorial, você criará um hub de eventos e, em seguida, fará com que o aplicativo do gerador de eventos de chamada envie dados de chamada para esse Hub de eventos. Para mais informações sobre centros de eventos, consulte a documentação do [Azure Service Bus.](https://docs.microsoft.com/azure/service-bus/)

>[!NOTE]
>Para uma versão mais detalhada deste procedimento, consulte [Create a Event Hubs namespace e um hub de eventos utilizando o portal Azure](../event-hubs/event-hubs-create.md). 

### <a name="create-a-namespace-and-event-hub"></a>Criar um hub de eventos e de espaço de nomes
Neste procedimento, você primeiro cria um namespace de Hub de eventos e, em seguida, adiciona um hub de eventos a esse namespace. Os namespaces do hub de eventos são usados para agrupar logicamente as instâncias do barramento de eventos relacionadas. 

1. Inicie sessão no portal Azure e clique em **Criar um recurso** na parte superior esquerda do ecrã.

2. Selecione **todos os serviços** no menu esquerdo e selecione **star (`*`)** ao lado dos Centros de **Eventos** na categoria **Analytics.** Confirme que **o Event Hubs** é adicionado aos **FAVORITES** no menu de navegação esquerdo. 

   ![Pesquisar hubs de eventos](./media/stream-analytics-real-time-fraud-detection/select-event-hubs-menu.png)

3. Selecione Centros de **Eventos** em **FAVORITOS** no menu de navegação esquerdo e selecione **Adicionar** na barra de ferramentas.

   ![Botão Adicionar](./media/stream-analytics-real-time-fraud-detection/event-hubs-add-toolbar.png)

4. No painel Criar espaço de **nome,** introduza um nome de espaço de nome, como `<yourname>-eh-ns-demo`. Você pode usar qualquer nome para o namespace, mas o nome deve ser válido para uma URL e deve ser exclusivo no Azure. 
    
5. Selecione uma subscrição e crie ou escolha um grupo de recursos e, em seguida, clique em **Criar**.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png" alt="Create event hub namespace in Azure portal" width="300px"/>

6. Quando o namespace concluir a implantação, localize o namespace do hub de eventos na lista de recursos do Azure. 

7. Clique no novo espaço de nomes e no painel de espaço de nome, clique no **Event Hub**.

   ![O botão Adicionar Hub de eventos para criar um novo hub de eventos](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
8. Nomeie o novo centro de eventos `asa-eh-frauddetection-demo`. Pode utilizar um nome diferente. Se você fizer isso, anote-o, pois você precisará do nome mais tarde. Você não precisa definir nenhuma outra opção para o Hub de eventos no momento.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png" alt="Name event hub in Azure portal" width="400px"/>
    
9. Clique em **Criar**.

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Conceder acesso ao hub de eventos e obter uma cadeia de ligação

Antes que um processo possa enviar dados para um hub de eventos, o Hub de eventos deve ter uma política que permita o acesso apropriado. A política de acesso produz uma cadeia de ligação que inclui as informações de autorização.

1.  No painel de espaço de eventos, clique em Centros de **Eventos** e, em seguida, clique no nome do seu novo hub de eventos.

2.  No painel do hub do evento, clique em políticas de **acesso partilhado** e, em seguida, clique em+ **&nbsp;Adicionar**.

    >[!NOTE]
    >Verifique se você está trabalhando com o Hub de eventos, não o namespace do hub de eventos.

3.  Adicione uma política denominada `sa-policy-manage-demo` e para **Reclamação,** selecione **Gerir**.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png" alt="Create shared access policy for Stream Analytics" width="300px"/>
 
4.  Clique em **Criar**.

5.  Depois que a política tiver sido implantada, clique nela na lista de políticas de acesso compartilhado.

6.  Encontre a chave DE **LIGAÇÃO STRING-PRIMARY** da caixa e clique no botão de cópia ao lado da cadeia de ligação. 

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png" alt="Stream Analytics shared access policy" width="300px"/>
 
7.  Cole a cadeia de ligação num editor de texto. Você precisa dessa cadeia de conexão para a próxima seção, depois de fazer algumas pequenas edições nela.

    A cadeia de conexão tem esta aparência:

        Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=asa-eh-frauddetection-demo

    Note que a cadeia de ligação contém múltiplos pares de valor-chave, separados com pontos evíbis: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`e `EntityPath`.  

## <a name="configure-and-start-the-event-generator-application"></a>Configurar e iniciar o aplicativo gerador de eventos

Antes de iniciar o aplicativo TelcoGenerator, você deve configurá-lo para que ele envie registros de chamada para o Hub de eventos que você criou.

### <a name="configure-the-telcogenerator-app"></a>Configurar o aplicativo TelcoGenerator

1. No editor onde copiou a corda de ligação, tome nota do valor `EntityPath` e, em seguida, remova o par `EntityPath` (não se esqueça de remover o ponto e vírgula que o precede). 

2. Na pasta em que você descompactou o arquivo TelcoGenerator. zip, abra o arquivo telcodatagen. exe. config em um editor. (Há mais de um arquivo. config, portanto, certifique-se de abrir o correto.)

3. No elemento `<appSettings>`:

   * Detete o valor da chave `EventHubName` para o nome do hub do evento (isto é, para o valor do caminho da entidade).
   * Detete o valor da chave `Microsoft.ServiceBus.ConnectionString` para a cadeia de ligação. 

   A secção `<appSettings>` será o seguinte exemplo. (Para maior clareza, as linhas são encapsuladas e alguns caracteres foram removidos do token de autorização.)

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
   * Probabilidade de fraude de cartão SIM: com que frequência, como uma porcentagem de todas as chamadas, o aplicativo deve simular uma chamada fraudulenta. O valor 0.2 significa que cerca de 20% dos registos de chamada parecerão fraudulentos.
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

1. No portal Azure, clique em **Criar um recurso** > internet das **coisas** > trabalho **stream analytics.**

2. Nomeie o trabalho `asa_frauddetection_job_demo`, especifique uma subscrição, grupo de recursos e localização.

    É uma boa ideia posicionar o trabalho e o Hub de eventos na mesma região para obter o melhor desempenho e, assim, você não paga para transferir dados entre regiões.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png" alt="Create Stream Analytics job in portal" width="300px"/>

3. Clique em **Criar**.

    O trabalho é criado e o portal exibe detalhes do trabalho. No entanto, nada está em execução — você precisa configurar o trabalho antes que ele possa ser iniciado.

### <a name="configure-job-input"></a>Configurar a entrada da tarefa

1. No painel de instrumentos ou no painel **de todos os recursos,** encontre e selecione o trabalho `asa_frauddetection_job_demo` Stream Analytics. 
2. Na secção **de visão geral** do painel de trabalho stream analytics, clique na caixa de **entrada.**

   ![Caixa de entrada em topologia no painel de trabalho do Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. Clique em **Adicionar entrada** de fluxo e selecione **Event Hub**. Em seguida, preencha a página nova entrada com as seguintes informações:

   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Alias de entrada  |  CallStream   |  Introduza um nome para identificar a entrada da tarefa.   |
   |Subscrição   |  \<A sua subscrição\> |  Selecione a assinatura do Azure que tem o Hub de eventos que você criou.   |
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

Para saber mais sobre o idioma, consulte a Referência da [Linguagem da Consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

### <a name="get-sample-data-for-testing-queries"></a>Obter dados de exemplo para testar consultas

O aplicativo TelcoGenerator está enviando registros de chamada para o Hub de eventos e seu trabalho de Stream Analytics está configurado para ler do hub de eventos. Você pode usar uma consulta para testar o trabalho para certificar-se de que ele está sendo lido corretamente. Para testar uma consulta no console do Azure, você precisa de dados de exemplo. Para esta explicação, você extrairá dados de exemplo do fluxo que está chegando ao Hub de eventos.

1. Verifique se o aplicativo TelcoGenerator está em execução e produzindo registros de chamada.
2. No portal, retorne ao painel de trabalho do Stream Analytics. (Se fechar o painel, procure `asa_frauddetection_job_demo` no painel **de todos os recursos.)**
3. Clique na caixa **de consulta.** O Azure lista as entradas e saídas configuradas para o trabalho e permite que você crie uma consulta que permite transformar o fluxo de entrada conforme ele é enviado para a saída.
4. No painel **De consulta,** clique nos pontos ao lado da entrada `CallStream` e, em seguida, selecione **dados da amostra a partir da entrada**.

   ![Opções de menu para usar dados de exemplo para a entrada de trabalho do Stream Analytics, com "dados de exemplo da entrada" selecionados](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)


5. Definir **Minutos** para 3 e, em seguida, clicar **EM OK**. 
    
   ![Opções para amostragem de fluxo de entrada com 3 minutos selecionados](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    O Azure amostras de 3 minutos de dados do fluxo de entrada e o notifica quando os dados de exemplo estão prontos. (Isso demora um pouco.) 

Os dados de exemplo são temporariamente armazenados e estão disponíveis enquanto a janela de consulta estiver aberta. Se fechar a janela de consulta, os dados de exemplo são eliminados e terá de criar um novo conjunto de dados de exemplo. 

Como alternativa, pode obter um ficheiro .json que tenha dados de amostra [saindo do GitHub,](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json)e, em seguida, carregar esse ficheiro .json para usar como dados de amostra para a entrada `CallStream`. 

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

    Nesta consulta, `CallStream` é o pseudónimo que especificou quando criou a entrada. Se você usou um alias diferente, use esse nome em vez disso.

2. Clique no **Teste**.

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

2. Clique no **Teste** novamente. 

   ![Stream Analytics saída de trabalho para projeção mostra 25 registros](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Contar chamadas de entrada por região: janela em cascata com agregação

Suponha que você deseja contar o número de chamadas de entrada por região. No streaming de dados, quando você deseja executar funções de agregação como contagem, você precisa segmentar o fluxo em unidades temporais (já que o fluxo de dados em si é efetivamente infinito). Faça isto usando uma [função](stream-analytics-window-functions.md)de janela streaming Analytics . Em seguida, você pode trabalhar com os dados dentro dessa janela como uma unidade.

Para essa transformação, você deseja uma sequência de janelas temporais que não se sobrepõem – cada janela terá um conjunto discreto de dados que você pode agrupar e agregar. Este tipo de janela é referido como uma *janela caindo*. Dentro da janela Tumbling, você pode obter uma contagem das chamadas recebidas agruparadas por `SwitchNum`, que representa o país/região onde a chamada teve origem. 

1. Altere a consulta no editor de código para o seguinte:

        ```SQL
        SELECT 
            System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
        FROM
            CallStream TIMESTAMP BY CallRecTime 
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
        ```

    Esta consulta utiliza a palavra-chave `Timestamp By` na cláusula `FROM` para especificar qual o campo de carimbo sinuoso no fluxo de entrada a utilizar para definir a janela Tumbling. Neste caso, a janela divide os dados em segmentos pelo campo `CallRecTime` em cada registo. (Se nenhum campo for especificado, a operação de janela usará a hora em que cada evento chega no Hub de eventos. Ver "Hora de Chegada Vs Tempo de Aplicação" na Referência da Linguagem da [Consulta de Análise de Fluxo](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). 

    A projeção inclui `System.Timestamp`, que devolve um carimbo temporal para o fim de cada janela. 

    Para especificar que pretende utilizar uma janela Tumbling, utiliza a função [TUMBLINGWINDOW](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) na cláusula `GROUP BY`. Na função, você especifica uma unidade de tempo (em qualquer lugar de um segundo a um dia) e um tamanho de janela (quantas unidades). Neste exemplo, a janela em cascata consiste em intervalos de 5 segundos, portanto, você receberá uma contagem por país/região para cada 5 segundos de chamadas.

2. Clique no **Teste** novamente. Nos resultados, note que os selos de tempo em **WindowEnd** estão em incrementos de 5 segundos.

   ![Stream Analytics saída de trabalho para agregação mostrando 13 registros](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>Detectar fraudes SIM usando uma auto-associação

Para este exemplo, considere o uso de fraudulento para chamadas que se originam do mesmo usuário, mas em locais diferentes dentro de 5 segundos entre si. Por exemplo, o mesmo utilizador não pode legitimamente fazer uma chamada do E.U.A. e da Austrália ao mesmo tempo. 

Para verificar estes casos, pode utilizar uma auto-adesão dos dados de streaming para se juntar ao fluxo para si mesmo com base no valor `CallRecTime`. Pode então procurar registos de chamadas onde o valor `CallingIMSI` (o número de origem) é o mesmo, mas o valor `SwitchNum` (país/região de origem) não é o mesmo.

Quando você usa uma junção com dados de streaming, a junção deve fornecer alguns limites sobre até onde as linhas correspondentes podem ser separadas no tempo. (Como notado anteriormente, os dados de streaming são efetivamente infinitos.) Os limites de tempo para a relação são especificados dentro da cláusula `ON` da união, utilizando a função `DATEDIFF`. Nesse caso, a junção é baseada em um intervalo de 5 segundos de dados de chamada.

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

    Esta consulta é como qualquer sql de adesão, exceto para a função `DATEDIFF` na união. Esta versão do `DATEDIFF` é específica do Streaming Analytics, e deve figurar na cláusula `ON...BETWEEN`. Os parâmetros são uma unidade de tempo (segundos neste exemplo) e os aliases das duas fontes para a junção. Isto é diferente da função padrão do SQL `DATEDIFF`.

    A cláusula `WHERE` inclui a condição de sinalizar a chamada fraudulenta: os interruptores originários não são os mesmos. 

2. Clique no **Teste** novamente. 

   ![Stream Analytics saída de trabalho para autojunção, mostrando 6 registros gerados](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. Clique em **Guardar** para salvar a consulta de auto-join como parte do trabalho de Streaming Analytics. (Ele não salva os dados de exemplo.)

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png" alt="Save Stream Analytics query in portal" width="300px"/>

## <a name="create-an-output-sink-to-store-transformed-data"></a>Criar um coletor de saída para armazenar dados transformados

Você definiu um fluxo de eventos, uma entrada do hub de eventos para eventos de ingestão e uma consulta para executar uma transformação sobre o fluxo. A última etapa é definir um coletor de saída para o trabalho, ou seja, um local para gravar o fluxo transformado. 

Você pode usar muitos recursos como coletores de saída — um banco de dados SQL Server, armazenamento de tabelas, armazenamento de Data Lake, Power BI e até mesmo outro hub de eventos. Para este tutorial, você gravará o fluxo no armazenamento de BLOBs do Azure, que é uma opção típica para coletar informações de evento para análise posterior, pois ela acomoda dados não estruturados.

Se você tiver uma conta de armazenamento de BLOBs existente, poderá usá-la. Para este tutorial, você aprenderá a criar uma nova conta de armazenamento.

### <a name="create-an-azure-blob-storage-account"></a>Criar uma conta de armazenamento de BLOBs do Azure

1. No canto superior esquerdo do portal do Azure, selecione **Criar um recurso** > **Armazenamento** > **Conta de armazenamento**. Preencha a página de trabalho da conta de armazenamento com **nome** definido para "asaehstorage", **Localização** definida para "East US", **grupo de recursos** definido para "asa-eh-ns-rg" (hospedar a conta de armazenamento no mesmo grupo de recursos que o trabalho de Streaming para maior desempenho). As restantes definições podem ser mantidas nos respetivos valores predefinidos.  

   ![Criar conta de armazenamento no portal do Azure](./media/stream-analytics-real-time-fraud-detection/stream-analytics-storage-account-create.png)

2. No portal do Azure, retorne ao painel de trabalho do Stream Analytics. (Se fechar o painel, procure `asa_frauddetection_job_demo` no painel **de todos os recursos.)**

3. Na secção **De Trabalho topologia,** clique na caixa **de saída.**

4. No painel **de saídas,** clique em **Adicionar** e selecione **o armazenamento Blob**. Em seguida, preencha a página nova saída com as seguintes informações:

   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Alias de saída  |  CallStream-FraudulentCalls   |  Introduza um nome para identificar a saída da tarefa.   |
   |Subscrição   |  \<A sua subscrição\> |  Selecione a subscrição do Azure que tem a conta de armazenamento que criou. A conta de armazenamento pode estar na mesma subscrição ou numa diferente. Este exemplo assume que criou a conta de armazenamento na mesma subscrição. |
   |Conta de armazenamento  |  asaehstorage |  Insira o nome da conta de armazenamento que você criou. |
   |Contentor  | asa-fraudulentcalls-demo | Escolha Criar novo e insira um nome de contêiner. |

    <br/>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png" alt="Create blob output for Stream Analytics job" width="300px"/>
    
5. Clique em **Guardar**. 


## <a name="start-the-streaming-analytics-job"></a>Iniciar o trabalho do Stream Analytics

O trabalho agora está configurado. Você especificou uma entrada (o Hub de eventos), uma transformação (a consulta para procurar por chamadas fraudulentas) e uma saída (armazenamento de BLOBs). Agora você pode iniciar o trabalho. 

1. Verifique se o aplicativo TelcoGenerator está em execução.

2. No painel de trabalho, clique **em Iniciar**. No painel **de trabalho inicial,** para a hora de início da saída de trabalho, selecione **Agora**. 

   ![Iniciar o trabalho de Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start.png)



## <a name="examine-the-transformed-data"></a>Examinar os dados transformados

Agora você tem um trabalho de streaming Analytics completo. O trabalho está examinando um fluxo de metadados de chamada telefônica, procurando por chamadas telefônicas fraudulentas em tempo real e gravando informações sobre essas chamadas fraudulentas para armazenamento. 

Para concluir este tutorial, talvez você queira examinar os dados que estão sendo capturados pelo trabalho do Stream Analytics. Os dados estão sendo gravados no armazenamento de blog do Azure em partes (arquivos). Você pode usar qualquer ferramenta que leia o armazenamento de BLOBs do Azure. Como se nota na secção Pré-Requisitos, pode utilizar extensões Azure no Estúdio Visual, ou pode utilizar uma ferramenta como [o Azure Storage Explorer](https://storageexplorer.com/) ou o [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

Ao examinar o conteúdo de um arquivo no armazenamento de BLOBs, você verá algo semelhante ao seguinte:

   ![Armazenamento de BLOBs do Azure com saída do streaming Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>Limpar recursos

Há artigos adicionais que continuam com o cenário de detecção de fraude e usam os recursos que você criou neste tutorial. Se quiser continuar, consulte as sugestões em **passos seguintes.**

No entanto, se você tiver terminado e não precisar dos recursos que criou, poderá excluí-los para não incorrer em encargos do Azure desnecessários. Nesse caso, sugerimos que você faça o seguinte:

1. Pare o trabalho do Stream Analytics. No painel **Jobs,** clique em **Parar** no topo.
2. Pare o aplicativo gerador de telecomunicações. Na janela de comando em que você iniciou o aplicativo, pressione CTRL + C.
3. Se você criou uma nova conta de armazenamento de BLOBs apenas para este tutorial, exclua-a. 
4. Exclua o trabalho do Stream Analytics.
5. Exclua o Hub de eventos.
6. Exclua o namespace do hub de eventos.

## <a name="get-support"></a>Obter suporte

Para mais assistência, experimente o [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes

Você pode continuar este tutorial com o seguinte artigo:

* [Stream Analytics e Power BI: Um dashboard de análise em tempo real para dados de streaming](stream-analytics-power-bi-dashboard.md). Este artigo mostra como enviar a saída de TelCo do trabalho de Stream Analytics para Power BI para análise e visualização em tempo real.

Para obter mais informações sobre Stream Analytics em geral, consulte estes artigos:

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
