---
title: Tutorial - Criar e gerir um trabalho de Stream Analytics utilizando o portal Azure
description: Este tutorial fornece uma demonstração ponto a ponto de como utilizar o Azure Stream Analytics para analisar chamadas fraudulentas num fluxo de chamadas telefónica.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/30/2020
ms.openlocfilehash: fef949e9285264ef46fbaed05a4385a15b27e65e
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94354473"
---
# <a name="tutorial-analyze-phone-call-data-with-stream-analytics-and-visualize-results-in-power-bi-dashboard"></a>Tutorial: Analise os dados da chamada telefónica com o Stream Analytics e visualize os resultados no painel Power BI

Este tutorial ensina como analisar dados de chamadas telefónicas com o Azure Stream Analytics. Os dados da chamada telefónica, gerados por uma aplicação do cliente, contêm algumas chamadas fraudulentas, que serão filtradas pelo trabalho stream Analytics.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Gerar dados de uma chamada telefónica de exemplo e enviá-los para os Hubs de Eventos do Azure
> * Criar uma tarefa do Stream Analytics
> * Configurar a entrada e a saída da tarefa
> * Definir uma consulta para filtrar chamadas fraudulentas
> * Testar e iniciar a tarefa
> * Visualizar os resultados no Power BI

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, faça as seguintes ações:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).
* Inicie sessão no [Portal do Azure](https://portal.azure.com/).
* Transfira a aplicação geradora de eventos de chamadas telefónicas [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) no Centro de Transferências da Microsoft ou obtenha o código fonte no [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator).
* Irá precisar de uma conta do Power BI.

## <a name="create-an-azure-event-hub"></a>Criar um Hub de Eventos do Azure

Para que o Stream Analytics possa analisar o fluxo de dados de chamadas fraudulentas, os dados têm de ser enviados para o Azure. Neste tutorial, vai enviar os dados para o Azure através dos [Hubs de Eventos do Azure](../event-hubs/event-hubs-about.md).

Utilize os passos seguintes para criar um Hub de Eventos e enviar dados de chamadas para esse Hub de Eventos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Criar um recurso** Internet of  >  **Things**  >  **Event Hubs**.

   ![Criar um Azure Event Hub no portal](media/stream-analytics-manage-job/find-event-hub-resource.png)
3. Preencha o painel **Create Namespace** com os seguintes valores:

   |**Definição**  |**Valor sugerido** |**Descrição**  |
   |---------|---------|---------|
   |Name     | asaTutorialEventHub        |  Um nome exclusivo para identificar o espaço de nomes do hub de eventos.       |
   |Subscrição     |   \<Your subscription\>      |   Selecione uma subscrição do Azure onde pretende criar o hub de eventos.      |
   |Grupo de recursos     |   MyASADemoRG      |  Selecione **Criar Novo** e introduza um novo nome de grupo de recursos para a sua conta.       |
   |Localização     |   E.U.A. Oeste 2      |    Localização onde o espaço de nomes do hub de eventos pode ser implementado.     |

4. Utilize opções predefinidos nas definições restantes e selecione **Review + create**. Em seguida, **selecione Criar** para iniciar a implementação.

   ![Criar espaço de nome de hub de eventos no portal Azure](media/stream-analytics-manage-job/create-event-hub-namespace.png)

5. Quando o espaço de nome terminar de implantar, vá a **Todos os recursos** e encontre o *asaTutorialEventHub* na lista de recursos da Azure. *Selecione asaTutorialEventHub* para abri-lo.

6. Selecione **+Event Hub** e introduza um **Nome** para o Centro de Eventos. Definir o **Conde de Partição** para *2*.  Utilize as opções predefinidos nas definições restantes e selecione **Criar**. Em seguida, aguarde até que a implementação seja executada com êxito.

   ![Configuração do Hub de Eventos no portal Azure](media/stream-analytics-manage-job/create-event-hub-portal.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Conceder acesso ao hub de eventos e obter uma cadeia de ligação

Para que uma aplicação possa enviar dados para os Hubs de Eventos do Azure, o hub de eventos tem de ter uma política que permita o acesso adequado. A política de acesso produz uma cadeia de ligação que inclui as informações de autorização.

1. Navegue até ao centro de eventos que criou no passo anterior, *MyEventHub*. Selecione **Políticas de acesso partilhado** em **Definições** e, em seguida, selecione **+ Adicionar**.

2. Atribua o nome **MyPolicy** à política e certifique-se de que a opção **Gerir** está marcada. Em seguida, selecione **Criar**.

   ![Criar a política de acesso partilhado do hub de eventos](media/stream-analytics-manage-job/create-event-hub-access-policy.png)

3. Uma vez criada a política, clique no nome da política para abrir a política. Encontre a **tecla primária da ligação**. Selecione o botão **de cópia** ao lado da cadeia de ligação.

   ![Guardar a cadeia de ligação da política de acesso partilhado](media/stream-analytics-manage-job/save-connection-string.png)

4. Cole a cadeia de ligação num editor de texto. Vai precisar desta cadeia de ligação na secção seguinte.

   A cadeia de ligação é semelhante à seguinte:

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>`

   Repare que a cadeia de ligação contém vários pares chave-valor, separados por ponto e vírgula: **Endpoint** , **SharedAccessKeyName** , **SharedAccessKey** e **EntityPath**.

## <a name="start-the-event-generator-application"></a>Iniciar a aplicação geradora de eventos

Antes de iniciar a aplicação TelcoGenerator, deve configurá-la para enviar dados para os Hubs de Eventos do Azure que criou anteriormente.

1. Extraia o conteúdo do ficheiro [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip).
2. Abra o `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` ficheiro num editor de texto à sua escolha (Existe mais de um ficheiro .config, por isso certifique-se de que abre o correto.)

3. Atualize o elemento `<appSettings>` no ficheiro de configuração com os seguintes detalhes:

   * Defina o valor da chave *EventHubName* como o valor de EntityPath na cadeia de ligação.
   * Desaponte o valor da tecla *Microsoft.ServiceBus.ConnectionString* para a cadeia de ligação sem o valor EntityPath (não se esqueça de remover o ponto de e vírgula que o precede).

4. Guarde o ficheiro.
5. Em seguida, abra uma janela de comando e mude para a pasta onde deszipou a aplicação TelcoGenerator. Em seguida, introduza o seguinte comando:

   ```cmd
   telcodatagen.exe 1000 0.2 2
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

   ![Criar uma tarefa do Azure Stream Analytics](media/stream-analytics-manage-job/create-stream-analytics-job.png)

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

   ![Configure Azure Stream Analytics](media/stream-analytics-manage-job/configure-stream-analytics-input.png)

## <a name="configure-job-output"></a>Configurar a saída da tarefa

O último passo é definir um lavatório de saída onde o trabalho pode escrever os dados transformados. Neste tutorial, vai produzir e visualizar dados com o Power BI.

1. A partir do portal Azure, abra **todos os recursos** e selecione o trabalho *ASATutorial* Stream Analytics.

2. Na secção **de Topologia** de Trabalho do trabalho Stream Analytics, selecione a opção **Saídas.**

3. Selecione **+ Adicionar**  >  **Power BI**. Em seguida, **selecione Authorize** e siga as instruções para autenticar o Power BI.

:::image type="content" source="media/stream-analytics-manage-job/authorize-power-bi.png" alt-text="botão de autorização para Power BI":::

4. Preencha o formulário de saída com os seguintes detalhes e **selecione Guardar:**

   |**Definição**  |**Valor sugerido**  |
   |---------|---------|
   |Alias de saída  |  MyPBIoutput  |
   |Espaço de trabalho em grupo| A minha área de trabalho |
   |Nome do conjunto de dados  |   ASAdataset  |
   |Nome da tabela |  ASATable  |
   | Modo de autenticação | Ficha de utilizador |

   ![Configure Stream Analytics saída](media/stream-analytics-manage-job/configure-stream-analytics-output.png)

   Este tutorial utiliza o modo de autenticação *do símbolo do Utilizador.* Para utilizar identidade gerida, consulte [a utilização da identidade gerida para autenticar o seu trabalho Azure Stream Analytics para Power BI](powerbi-output-managed-identity.md).

## <a name="define-a-query-to-analyze-input-data"></a>Definir uma consulta para analisar os dados de entrada

O próximo passo consiste em criar uma transformação que analisa os dados em tempo real. Para definir a consulta de transformação, vai utilizar a [Linguagem de Consulta do Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference). A consulta utilizada neste tutorial deteta chamadas fraudulentas a partir dos dados do telefone.

Neste exemplo, as chamadas fraudulentas são feitas pelo mesmo utilizador num intervalo de cinco segundos, mas em localizações diferentes. Por exemplo, o mesmo utilizador não pode legitimamente fazer uma chamada do E.U.A. e da Austrália ao mesmo tempo. Para definir a consulta de transformação para a tarefa de Stream Analytics:

1. A partir do portal Azure, abra o painel **de todos os recursos** e navegue até ao trabalho **asATutorial** Stream Analytics que criou anteriormente.

2. Na secção **de Topologia** de Trabalho do trabalho Stream Analytics, selecione a **opção 'Consulta'.** A janela de consulta lista as entradas e saídas configuradas para o trabalho e permite criar uma consulta para transformar o fluxo de entrada.

3. Substitua a consulta existente no editor pela seguinte consulta, que executa uma associação automática num intervalo de 5 segundos de dados de chamada:

   ```sql
   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "MyPBIoutput"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
   ON CS1.CallingIMSI = CS2.CallingIMSI
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

   Para verificar a existência de chamadas fraudulentas, pode fazer a associação automática dos dados de transmissão em fluxo com base no valor `CallRecTime`. Em seguida, pode procurar registos de chamadas onde o `CallingIMSI` valor (o número de origem) é o mesmo, mas o `SwitchNum` valor (país/região de origem) é diferente. Quando utiliza uma operação JOIN com dados de transmissão em fluxo, a associação tem de fornecer alguns limites relativamente à distância de separação no tempo das linhas correspondentes. Uma vez que os dados de transmissão em fluxo são infinitos, os limites de tempo para a relação são especificados na cláusula **ON** da associação, com a função [DATEDIFF](/stream-analytics-query/datediff-azure-stream-analytics).

   Esta consulta é semelhante a uma associação normal de SQL, exceto a função **DATEDIFF**. A função **DATEDIFF** utilizada nesta consulta é específica do Stream Analytics e tem de aparecer dentro da cláusula `ON...BETWEEN`.

4. **Guarde** a consulta.

   ![Defina a consulta stream analytics no portal](media/stream-analytics-manage-job/define-stream-analytics-query.png)

## <a name="test-your-query"></a>Testar a consulta

Pode testar uma consulta do editor de consultas. Execute os seguintes passos para testar a consulta:

1. Certifique-se de que a aplicação TelcoGenerator está em execução e a produzir registos de chamadas telefónicas.

2. Selecione **Testar** para testar a consulta. Deverá ver os seguintes resultados:

   ![Saída do teste de consulta stream analytics](media/stream-analytics-manage-job/sample-test-output-restuls.png)

## <a name="start-the-job-and-visualize-output"></a>Iniciar a tarefa e visualizar a saída

1. Para iniciar o trabalho, navegue para o **visão geral** do trabalho e selecione **Start**.

2. Selecione **Agora** para a hora de início da saída da tarefa e selecione **Iniciar**. Pode ver o estado da tarefa na barra de notificação.

3. Depois de a tarefa ser executada com êxito, navegue até ao [Power BI](https://powerbi.com/) e inicie sessão com a sua conta escolar ou profissional. Se a consulta da tarefa do Stream Analytics estiver a produzir resultados, significa que o conjunto de dados *ASAdataset* que criou existe no separador **Conjuntos de dados**.

4. Na sua área de trabalho do Power BI, selecione **+ Criar** para criar um novo dashboard com o nome *Chamadas Fraudulentas*.

5. Na parte superior da janela, **selecione Editar** e **Adicione azulejos.** Em seguida, selecione **Dados de Transmissão em Fluxo Personalizados** e **Seguinte**. Escolha o conjunto de dados **ASAdataset** em **Conjuntos de dados**. Selecione **o Cartão** a partir do **dropdown do tipo visualização** e adicione **chamadas fraudulentas** a **Fields**. Selecione **Seguinte** para introduzir um nome para o mosaico e, em seguida, selecione **Aplicar** para criar o mosaico.

   ![Criar azulejos de painel power BI](media/stream-analytics-manage-job/create-power-bi-dashboard-tiles.png)

6. Execute o passo 5 novamente, com as seguintes opções:
   * Quando chegar a Tipo de Visualização, selecione Gráfico de linhas.
   * Adicione um eixo e selecione **windowend**.
   * Adicione um valor e selecione **fraudulentcalls**.
   * Para **Janela de tempo a apresentar** , selecione os últimos 10 minutos.

7. O dashboard deve ter um aspeto semelhante ao seguinte depois de ambos os mosaicos serem adicionados. Note que, se a aplicação deendernerte do hub de eventos e a aplicação streaming Analytics estiverem em execução, o seu painel power BI atualiza periodicamente à medida que novos dados chegam.

   ![Ver resultados no painel Power BI](media/stream-analytics-manage-job/power-bi-results-dashboard.png)

## <a name="embedding-your-power-bi-dashboard-in-a-web-application"></a>Incorporando o seu Painel de Poder BI numa Aplicação Web

Para esta parte do tutorial, você usará uma amostra [ASP.NET](https://asp.net/) aplicação web criada pela equipa Power BI para incorporar o seu dashboard. Para obter mais informações sobre a incorporação de dashboards, veja o artigo [Incorporação com o Power BI](/power-bi/developer/embedding).

Para configurar a aplicação, vá ao repositório [PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) GitHub e siga as instruções na secção **De Dados do Utilizador** (utilize os URLs de redirecionamento e página inicial sob a subsecção de **aplicações integrativas** da web). Uma vez que estamos a usar o exemplo do Dashboard, utilize o código de amostra **integrado-web-app** localizado no [repositório GitHub](https://github.com/microsoft/PowerBI-Developer-Samples/tree/master/.NET%20Framework/Embed%20for%20your%20organization/).
Quando a aplicação estiver em execução no browser, siga estes passos para incorporar o dashboard que criou anteriormente na página Web:

1. Selecione **Iniciar sômposições no Power BI,** que concede à aplicação acesso aos dashboards na sua conta Power BI.

2. Selecione o botão **Obter Dashboards** , que apresenta os Dashboards da sua conta numa tabela. Localize o nome do dashboard que criou anteriormente **powerbi-embedded-dashboard** e copie o **EmbedUrl** correspondente.

3. Por fim, cole o **EmbedUrl** no campo de texto correspondente e selecione **Incorporar Dashboard**. Agora, pode ver o mesmo dashboard incorporado numa aplicação Web.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma tarefa simples do Stream Analytics, analisou os dados de entrada e apresentou os resultados num dashboard do Power BI. Para saber mais sobre tarefas do Stream Analytics, avance para o próximo tutorial:

> [!div class="nextstepaction"]
> [Executar as Funções do Azure nas tarefas do Stream Analytics](stream-analytics-with-azure-functions.md)