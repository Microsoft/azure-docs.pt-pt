---
title: Tutorial - Analise dados fraudulentos de chamadas com a Azure Stream Analytics e visualize os resultados no painel Power BI
description: Este tutorial fornece uma demonstração ponto a ponto de como utilizar o Azure Stream Analytics para analisar chamadas fraudulentas num fluxo de chamadas telefónica.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: contperf-fy21q2
ms.date: 12/17/2020
ms.openlocfilehash: 1e26159c07ca551a78ee2f83a0ca64779c60f7b4
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018875"
---
# <a name="tutorial-analyze-fraudulent-call-data-with-stream-analytics-and-visualize-results-in-power-bi-dashboard"></a>Tutorial: Analise dados fraudulentos de chamadas com Stream Analytics e visualize os resultados no painel Power BI

Este tutorial mostra-lhe como analisar dados de chamadas telefónicas usando a Azure Stream Analytics. Os dados da chamada telefónica, gerados por uma aplicação do cliente, contêm chamadas fraudulentas, que são filtradas pelo trabalho stream Analytics. Você pode usar as técnicas deste tutorial para outros tipos de deteção de fraude, como fraude de cartão de crédito ou roubo de identidade.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Gerei dados de chamadas telefónicas de amostra e envia-os para os Hubs de Eventos Azure.
> * Criar uma tarefa do Stream Analytics.
> * Configurar entrada e saída de trabalho.
> * Defina consultas para filtrar chamadas fraudulentas.
> * Teste e comece o trabalho.
> * Visualizar os resultados no Power BI.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de ter completado os seguintes passos:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).
* Transfira a aplicação geradora de eventos de chamadas telefónicas [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) no Centro de Transferências da Microsoft ou obtenha o código fonte no [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator).
* Irá precisar de uma conta do Power BI.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-an-azure-event-hub"></a>Criar um Hub de Eventos do Azure

Para que o Stream Analytics possa analisar o fluxo de dados de chamadas fraudulentas, os dados têm de ser enviados para o Azure. Neste tutorial, vai enviar os dados para o Azure através dos [Hubs de Eventos do Azure](../event-hubs/event-hubs-about.md).

Utilize os passos seguintes para criar um Hub de Eventos e enviar dados de chamadas para esse Hub de Eventos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **Criar um recurso** Internet of  >  **Things**  >  **Event Hubs**.

   ![Criar um Azure Event Hub no portal](media/stream-analytics-real-time-fraud-detection/find-event-hub-resource.png)
3. Preencha o painel **Create Namespace** com os seguintes valores:

   |**Definição**  |**Valor sugerido** |**Descrição**  |
   |---------|---------|---------|
   |Name     | asaTutorialEventHub        |  Um nome exclusivo para identificar o espaço de nomes do hub de eventos.       |
   |Subscrição     |   \<Your subscription\>      |   Selecione uma subscrição do Azure onde pretende criar o hub de eventos.      |
   |Grupo de recursos     |   MyASADemoRG      |  Selecione **Criar Novo** e introduza um novo nome de grupo de recursos para a sua conta.       |
   |Localização     |   E.U.A. Oeste 2      |    Localização onde o espaço de nomes do hub de eventos pode ser implementado.     |

4. Utilize opções predefinidos nas definições restantes e selecione **Review + create**. Em seguida, **selecione Criar** para iniciar a implementação.

   ![Criar espaço de nome de hub de eventos no portal Azure](media/stream-analytics-real-time-fraud-detection/create-event-hub-namespace.png)

5. Quando o espaço de nome terminar de implantar, vá a **Todos os recursos** e encontre o *asaTutorialEventHub* na lista de recursos da Azure. *Selecione asaTutorialEventHub* para abri-lo.

6. Selecione **+Event Hub** e introduza um **Nome** para o Centro de Eventos. Desajei a **contagem de divisórias** a 2.  Utilize as opções predefinidos nas definições restantes e selecione **Criar**. Em seguida, aguarde até que a implementação seja executada com êxito.

   ![Configuração do Hub de Eventos no portal Azure](media/stream-analytics-real-time-fraud-detection/create-event-hub-portal.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Conceder acesso ao hub de eventos e obter uma cadeia de ligação

Antes de uma aplicação poder enviar dados para a Azure Event Hubs, o centro de eventos deve ter uma política que permita o acesso. A política de acesso produz uma cadeia de ligação que inclui as informações de autorização.

1. Navegue até ao centro de eventos que criou no passo anterior, *MyEventHub*. Selecione **Políticas de acesso partilhado** em **Definições** e, em seguida, selecione **+ Adicionar**.

2. Atribua o nome **MyPolicy** à política e certifique-se de que a opção **Gerir** está marcada. Em seguida, selecione **Create** (Criar).

   ![Criar a política de acesso partilhado do hub de eventos](media/stream-analytics-real-time-fraud-detection/create-event-hub-access-policy.png)

3. Uma vez criada a política, selecione o nome da política para abrir a política. Encontre a **tecla primária da ligação**. Selecione o botão **de cópia** ao lado da cadeia de ligação.

   ![Guardar a cadeia de ligação da política de acesso partilhado](media/stream-analytics-real-time-fraud-detection/save-connection-string.png)

4. Cole a cadeia de ligação num editor de texto. Vai precisar desta cadeia de ligação na secção seguinte.

   A cadeia de ligação é semelhante à seguinte:

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>`

   Repare que a cadeia de ligação contém vários pares chave-valor, separados por ponto e vírgula: **Endpoint**, **SharedAccessKeyName**, **SharedAccessKey** e **EntityPath**.

## <a name="start-the-event-generator-application"></a>Iniciar a aplicação geradora de eventos

Antes de iniciar a aplicação TelcoGenerator, deve configurá-la para enviar dados para os Hubs de Eventos do Azure que criou anteriormente.

1. Extraia o conteúdo do ficheiro [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip).
2. Abra o `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` ficheiro num editor de texto à sua escolha Há mais de um `.config` ficheiro, por isso certifique-se de que abre o correto.

3. Atualize o elemento `<appSettings>` no ficheiro de configuração com os seguintes detalhes:

   * Defina o valor da chave *EventHubName* como o valor de EntityPath na cadeia de ligação.
   * Defina o valor da chave *Microsoft.ServiceBus.ConnectionString* como a cadeia de ligação sem o valor de EntityPath. Não se esqueça de remover o ponto e vírgula que precede o valor EntityPath.

4. Guarde o ficheiro.

5. Em seguida, abra uma janela de comando e mude para a pasta onde deszipou a aplicação TelcoGenerator. Em seguida, introduza o seguinte comando:

   ```cmd
   .\telcodatagen.exe 1000 0.2 2
   ```

   Este comando recebe os seguintes parâmetros:
   * Número de registos de dados de chamada por hora.
   * Percentagem de probabilidade de fraude, que corresponde à frequência com que a aplicação deve simular uma chamada fraudulenta. O valor 0.2 significa que cerca de 20% dos registos de chamada parecerão fraudulentos.
   * Duração em horas, que corresponde ao número de horas que a aplicação deve ser executada. Também pode parar a aplicação a qualquer momento, terminando o processo **(Ctrl+C)** na linha de comando.

   Após alguns segundos, a aplicação começa a apresentar registos de chamadas telefónicas no ecrã, à medida que os envia para o hub de eventos. Os dados das chamadas telefónicas contêm os seguintes campos:

   |**Registo**  |**Definição**  |
   |---------|---------|
   |CallrecTime    |  O carimbo de data/hora da hora de início da chamada.       |
   |SwitchNum     |  O comutador de telefone utilizado para estabelecer a chamada. Por exemplo, os interruptores são cordas que representam o país/região de origem (EUA, China, Reino Unido, Alemanha ou Austrália).       |
   |CallingNum     |  O número de telefone do chamador.       |
   |CallingIMSI     |  A Identidade Internacional de Assinante Móvel (IMSI). É um identificador exclusivo do chamador.       |
   |CalledNum     |   O número de telefone do destinatário da chamada.      |
   |CalledIMSI     |  Identidade Internacional de Assinante Móvel (IMSI). É um identificador exclusivo do destinatário da chama.       |

## <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics

Agora que tem um fluxo de eventos de chamada, pode criar uma tarefa do Stream Analytics que lê os dados do hub de eventos.

1. Para criar uma tarefa do Stream Analytics, navegue até ao [portal do Azure](https://portal.azure.com/).

2. Selecione **Criar um recurso** e procurar o trabalho stream **Analytics**. Selecione o **azulejo de trabalho Stream Analytics** e selecione *Create**.

3. Preencha o formulário **de trabalho New Stream Analytics** com os seguintes valores:

   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Nome da tarefa     |  ASATutorial       |   Um nome exclusivo para identificar o espaço de nomes do hub de eventos.      |
   |Subscrição    |  \<Your subscription\>   |   Selecione uma subscrição do Azure onde pretende criar a tarefa.       |
   |Grupo de recursos   |   MyASADemoRG      |   Selecione **Utilizar existente** e introduza um novo nome de grupo de recursos para a sua conta.      |
   |Localização   |    E.U.A. Oeste 2     |      Localização onde a tarefa pode ser implementada. É recomendado colocar a tarefa e o hub de eventos na mesma região para obter o melhor desempenho e para que não pague a transferência de dados entre regiões.      |
   |Ambiente de alojamento    | Cloud        |     As tarefas do Stream Analytics podem ser implementadas na cloud ou no Edge. A Cloud permite-lhe implantar para a Azure Cloud, e o Edge permite-lhe implementar para um dispositivo IoT Edge.    |
   |Unidades de transmissão em fluxo     |    1       |      As unidades de transmissão em fluxo representam os recursos informáticos que são necessários para executar uma tarefa. Por predefinição, este valor está definido como 1. Para saber mais sobre o dimensionamento de unidades de transmissão em fluxo, veja o artigo [Compreender e ajustar as unidades de transmissão em fluxo](stream-analytics-streaming-unit-consumption.md).      |

4. Utilize opções predefinidos nas definições restantes, **selecione Criar** e aguarde que a implementação tenha sucesso.

   ![Criar uma tarefa do Azure Stream Analytics](media/stream-analytics-real-time-fraud-detection/create-stream-analytics-job.png)

## <a name="configure-job-input"></a>Configurar a entrada da tarefa

A próxima etapa consiste em definir uma origem de entrada para a tarefa ler dados com o hub de eventos que criou na secção anterior.

1. A partir do portal Azure, abra a página **de Todos os recursos** e encontre o trabalho *asATutorial* Stream Analytics.

2. Na secção **de Topologia** de Trabalho do trabalho Stream Analytics, selecione **Inputs**.

3. Selecione **+ Adicionar entrada de fluxo** e **Hub de eventos**. Preencha o formulário de entrada com os seguintes valores:

   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Alias de entrada     |  CallStream       |  Indique um nome amigável para identificar a entrada. O alias de entrada só pode conter carateres alfanuméricos, hífenes e carateres de sublinhado, e tem de ter entre 3 e 63 carateres.       |
   |Subscrição    |   \<Your subscription\>      |   Selecione a subscrição do Azure onde criou o hub de eventos. O hub de eventos pode estar na mesma subscrição ou numa subscrição diferente da tarefa do Stream Analytics.       |
   |Espaço de nomes do hub de eventos    |  asaTutorialEventHub       |  Selecione o espaço de nomes do hub de eventos que criou na secção anterior. Todos os espaços de nomes dos hubs de eventos disponíveis na sua subscrição atual são apresentados na lista pendente.       |
   |Nome do Hub de Eventos    |   MyEventHub      |  Selecione o hub de eventos que criou na secção anterior. Todos os hubs de eventos disponíveis na sua subscrição atual são apresentados na lista pendente.       |
   |Nome da política do Hub de Eventos   |  MyPolicy       |  Selecione a política de acesso partilhado do hub de eventos que criou na secção anterior. Todas as políticas dos hubs de eventos disponíveis na sua subscrição atual são apresentados na lista pendente.       |

4. Utilize as opções predefinidas nas restantes definições e selecione **Guardar**.

   ![Configure Azure Stream Analytics](media/stream-analytics-real-time-fraud-detection/configure-stream-analytics-input.png)

## <a name="configure-job-output"></a>Configurar a saída da tarefa

O último passo é definir um lavatório de saída onde o trabalho pode escrever os dados transformados. Neste tutorial, vai produzir e visualizar dados com o Power BI.

1. A partir do portal Azure, abra **todos os recursos** e selecione o trabalho *ASATutorial* Stream Analytics.

2. Na secção **de Topologia** de Trabalho do trabalho Stream Analytics, selecione a opção **Saídas.**

3. Selecione **+ Adicionar**  >  **Power BI**. Em seguida, **selecione Authorize** e siga as instruções para autenticar o Power BI.

:::image type="content" source="media/stream-analytics-real-time-fraud-detection/authorize-power-bi.png" alt-text="botão de autorização para Power BI":::

4. Preencha o formulário de saída com os seguintes detalhes e **selecione Guardar:**

   |**Definição**  |**Valor sugerido**  |
   |---------|---------|
   |Alias de saída  |  MyPBIoutput  |
   |Espaço de trabalho em grupo| A minha área de trabalho |
   |Nome do conjunto de dados  |   ASAdataset  |
   |Nome da tabela |  ASATable  |
   | Modo de autenticação | Ficha de utilizador |

   ![Configure Stream Analytics saída](media/stream-analytics-real-time-fraud-detection/configure-stream-analytics-output.png)

   Este tutorial utiliza o modo de autenticação *do símbolo do Utilizador.* Para utilizar identidade gerida, consulte [a utilização da identidade gerida para autenticar o seu trabalho Azure Stream Analytics para Power BI](powerbi-output-managed-identity.md).

## <a name="create-queries-to-transform-real-time-data"></a>Criar consultas para transformar dados em tempo real

Neste momento, tem um trabalho de Stream Analytics configurado para ler um fluxo de dados que está a chegar. O próximo passo é criar uma consulta que analise os dados em tempo real. As consultas usam uma linguagem semelhante ao SQL que tem algumas extensões específicas do Stream Analytics.

Nesta secção do tutorial, você cria e testa várias consultas para aprender algumas maneiras em que você pode transformar um fluxo de entrada para análise. 

As consultas que cria aqui apenas mostrarão os dados transformados para o ecrã. Numa secção posterior, escreverá os dados transformados para Power BI.

Para saber mais sobre o idioma, consulte a [Referência de Idioma Azure Stream Analytics.](/stream-analytics-query/stream-analytics-query-language-reference)

### <a name="test-using-a-pass-through-query"></a>Teste utilizando uma consulta de passagem

Se quiser arquivar todos os eventos, pode utilizar uma consulta de passagem para ler todos os campos na carga útil do evento.

1. Navegue para o seu trabalho stream Analytics no portal Azure e **selecione Consulta** em *Topologia de Trabalho*. 

2. Na janela de consulta, insira esta consulta:

   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >Tal como acontece com o SQL, as palavras-chave não são sensíveis a casos, e o espaço em branco não é significativo.

    Nesta consulta, `CallStream` é o pseudónimo que especificou quando criou a entrada. Se usou um pseudónimo diferente, use esse nome.

3. Selecione **consulta de teste**.

    O trabalho Stream Analytics executa a consulta com os dados da amostra a partir da entrada e exibe a saída na parte inferior da janela. Os resultados indicam que o Centro de Eventos e o trabalho de Streaming Analytics estão configurados corretamente.

    :::image type="content" source="media/stream-analytics-real-time-fraud-detection/sample-output-passthrough.png" alt-text="Saída de amostra da consulta de teste":::

    O número exato de registos que vês dependerá de quantos registos foram capturados na amostra.

### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Reduzir o número de campos usando uma projeção de coluna

Em muitos casos, a sua análise não precisa de todas as colunas do fluxo de entrada. Você pode usar uma consulta para projetar um conjunto menor de campos devolvidos do que na consulta de passagem.

Executar a seguinte consulta e notar a saída.

```SQL
SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNumCalledNum 
FROM 
    CallStream
```

### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Contagem de chamadas por região: Janela de tropeçar com agregação

Suponha que queira contar o número de chamadas recebidas por região. Nos dados de streaming, quando se pretende executar funções agregadas como a contagem, é necessário segmentar o fluxo em unidades temporais, uma vez que o fluxo de dados em si é efetivamente infinito. Fá-lo utilizando uma [função de janela](stream-analytics-window-functions.md)Streaming Analytics. Em seguida, pode trabalhar com os dados dentro dessa janela como uma unidade.

Para esta transformação, você quer uma sequência de janelas temporais que não se sobrepõem - cada janela terá um conjunto discreto de dados que você pode agrupar e agregar. Este tipo de janela é referido como uma *janela de tumbling*. Dentro da janela Tumbling, você pode obter uma contagem das chamadas recebidas agrupadas por `SwitchNum` , que representa o país/região de onde a chamada originou.

1. Cole a seguinte consulta no editor de consulta:

    ```SQL
    SELECT 
        System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
    FROM
        CallStream TIMESTAMP BY CallRecTime 
    GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
    ```

    Esta consulta utiliza a `Timestamp By` palavra-chave na `FROM` cláusula para especificar qual o campo de tempos no fluxo de entrada para definir a janela Tumbling. Neste caso, a janela divide os dados em segmentos pelo `CallRecTime` campo em cada registo. (Se não for especificado nenhum campo, a operação de janela utiliza o tempo que cada evento chega ao centro do evento. Consulte "Tempo de chegada vs tempo de aplicação" em [Stream Analytics Consulta de Idioma .](/stream-analytics-query/stream-analytics-query-language-reference) 

    A projeção inclui `System.Timestamp` , que devolve um tempotando para o final de cada janela. 

    Para especificar que pretende utilizar uma janela de Tumbling, utilize a função [TUMBLINGWINDOW](/stream-analytics-query/tumbling-window-azure-stream-analytics) na `GROUP BY` cláusula. Na função, especifique uma unidade de tempo (em qualquer lugar de um microsegundo a um dia) e um tamanho de janela (quantas unidades). Neste exemplo, a janela Tumbling consiste em intervalos de 5 segundos, para que você obtenha uma contagem por país/região por cada 5 segundos de chamadas.

2. Selecione **consulta de teste**. Nos resultados, note que os intervalos de tempo em **WindowEnd** estão em incrementos de 5 segundos.

### <a name="detect-sim-fraud-using-a-self-join"></a>Detete a fraude SIM usando uma união automática

Para este exemplo, considere o uso fraudulento como chamadas originárias do mesmo utilizador, mas em locais diferentes dentro de 5 segundos um do outro. Por exemplo, o mesmo utilizador não pode legitimamente fazer uma chamada do E.U.A. e da Austrália ao mesmo tempo.

Para verificar estes casos, pode utilizar uma união auto-junção dos dados de streaming para se juntar ao fluxo para si mesmo com base no `CallRecTime` valor. Em seguida, pode procurar registos de chamadas onde o `CallingIMSI` valor (o número de origem) é o mesmo, mas o `SwitchNum` valor (país/região de origem) não é o mesmo.

Quando utilizar uma junção com dados de streaming, a junção deve fornecer alguns limites para a distância em que as linhas correspondentes podem ser separadas a tempo. Como já foi notado anteriormente, os dados de streaming são efetivamente infinitos. Os limites de tempo para a relação são especificados dentro `ON` da cláusula da junção, utilizando a `DATEDIFF` função. Neste caso, a junção baseia-se num intervalo de 5 segundos de dados de chamada.

1. Cole a seguinte consulta no editor de consulta:

    ```SQL
    SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
    INTO "MyPBIoutput"
    FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
    JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
    ON CS1.CallingIMSI = CS2.CallingIMSI
    AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
    WHERE CS1.SwitchNum != CS2.SwitchNum
    GROUP BY TumblingWindow(Duration(second, 1))
    ```

    Esta consulta é como qualquer unção SQL, exceto para a `DATEDIFF` função na junção. Esta versão `DATEDIFF` é específica para streaming Analytics, e deve aparecer na `ON...BETWEEN` cláusula. Os parâmetros são uma unidade de tempo (segundos neste exemplo) e os pseudónimos das duas fontes para a junção. Isto é diferente da função SQL `DATEDIFF` padrão.

    A `WHERE` cláusula inclui a condição que sinaliza a chamada fraudulenta: os interruptores de origem não são os mesmos.

2. Selecione **consulta de teste**. Reveja a saída e, em seguida, **selecione Guardar consulta**.

## <a name="start-the-job-and-visualize-output"></a>Iniciar a tarefa e visualizar a saída

1. Para iniciar o trabalho, navegue para o **visão geral** do trabalho e selecione **Start**.

2. Selecione **Agora** para a hora de início da saída da tarefa e selecione **Iniciar**. Pode ver o estado da tarefa na barra de notificação.

3. Depois de a tarefa ser executada com êxito, navegue até ao [Power BI](https://powerbi.com/) e inicie sessão com a sua conta escolar ou profissional. Se a consulta da tarefa do Stream Analytics estiver a produzir resultados, significa que o conjunto de dados *ASAdataset* que criou existe no separador **Conjuntos de dados**.

4. Na sua área de trabalho do Power BI, selecione **+ Criar** para criar um novo dashboard com o nome *Chamadas Fraudulentas*.

5. Na parte superior da janela, **selecione Editar** e **Adicione azulejos.** Em seguida, selecione **Dados de Transmissão em Fluxo Personalizados** e **Seguinte**. Escolha o conjunto de dados **ASAdataset** em **Conjuntos de dados**. Selecione **o Cartão** a partir do **dropdown do tipo visualização** e adicione **chamadas fraudulentas** a **Fields**. Selecione **Seguinte** para introduzir um nome para o mosaico e, em seguida, selecione **Aplicar** para criar o mosaico.

   ![Criar azulejos de painel power BI](media/stream-analytics-real-time-fraud-detection/create-power-bi-dashboard-tiles.png)

6. Execute o passo 5 novamente, com as seguintes opções:
   * Quando chegar a Tipo de Visualização, selecione Gráfico de linhas.
   * Adicione um eixo e selecione **windowend**.
   * Adicione um valor e selecione **fraudulentcalls**.
   * Para **Janela de tempo a apresentar**, selecione os últimos 10 minutos.

7. O dashboard deve ter um aspeto semelhante ao seguinte depois de ambos os mosaicos serem adicionados. Note que, se a aplicação deendernerte do hub de eventos e a aplicação streaming Analytics estiverem em execução, o seu painel power BI atualiza periodicamente à medida que novos dados chegam.

   ![Ver resultados no painel Power BI](media/stream-analytics-real-time-fraud-detection/power-bi-results-dashboard.png)

## <a name="embedding-your-power-bi-dashboard-in-a-web-application"></a>Incorporando o seu Painel de Poder BI numa Aplicação Web

Para esta parte do tutorial, você usará uma amostra [ASP.NET](https://asp.net/) aplicação web criada pela equipa Power BI para incorporar o seu dashboard. Para obter mais informações sobre a incorporação de dashboards, veja o artigo [Incorporação com o Power BI](/power-bi/developer/embedding).

Para configurar a aplicação, vá ao repositório [PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) GitHub e siga as instruções na secção **De Dados do Utilizador** (utilize os URLs de redirecionamento e página inicial sob a subsecção de **aplicações integrativas** da web). Uma vez que estamos a usar o exemplo do Dashboard, utilize o código de amostra **integrado-web-app** localizado no [repositório GitHub](https://github.com/microsoft/PowerBI-Developer-Samples/tree/master/.NET%20Framework/Embed%20for%20your%20organization/).
Quando a aplicação estiver em execução no browser, siga estes passos para incorporar o dashboard que criou anteriormente na página Web:

1. Selecione **Iniciar sômposições no Power BI,** que concede à aplicação acesso aos dashboards na sua conta Power BI.

2. Selecione o botão **Obter Dashboards**, que apresenta os Dashboards da sua conta numa tabela. Localize o nome do dashboard que criou anteriormente **powerbi-embedded-dashboard** e copie o **EmbedUrl** correspondente.

3. Por fim, cole o **EmbedUrl** no campo de texto correspondente e selecione **Incorporar Dashboard**. Agora, pode ver o mesmo dashboard incorporado numa aplicação Web.

## <a name="next-steps"></a>Próximos passos

Neste tutorial, criou uma tarefa simples do Stream Analytics, analisou os dados de entrada e apresentou os resultados num dashboard do Power BI. Para saber mais sobre tarefas do Stream Analytics, avance para o próximo tutorial:

> [!div class="nextstepaction"]
> [Executar as Funções do Azure nas tarefas do Stream Analytics](stream-analytics-with-azure-functions.md)
