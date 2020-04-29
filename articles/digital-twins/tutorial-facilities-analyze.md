---
title: 'Tutorial: Analise eventos em Time Series Insights - Azure Digital Twins Microsoft Docs'
description: Saiba como visualizar e analisar eventos a partir dos seus espaços Azure Digital Twins, com Azure Time Series Insights, utilizando os passos deste tutorial.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 01/10/2020
ms.openlocfilehash: 38bd1755ed87050cf8b91a0a82f6e5f1d2af9db5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75933793"
---
# <a name="tutorial-visualize-and-analyze-events-from-azure-digital-twins-by-using-time-series-insights"></a>Tutorial: Visualizar e analisar eventos de Gémeos Digitais Azure usando insights da Time Series

Depois de implementar a sua instância De Gémeos Digitais Azure, fornecer os seus espaços e implementar uma função personalizada para monitorizar condições específicas, pode visualizar os eventos e dados provenientes dos seus espaços para procurar tendências e anomalias.

No [primeiro tutorial,](tutorial-facilities-setup.md)configurao o gráfico espacial de um edifício imaginário, com uma sala que contém sensores para movimento, dióxido de carbono e temperatura. No [segundo tutorial](tutorial-facilities-udf.md), aprovisionou o gráfico e uma função definida pelo utilizador. A função monitoriza estes valores de sensor e despoleta notificações para as condições certas. Ou seja, a sala está vazia, e os níveis de temperatura e dióxido de carbono estão normais.

Este tutorial mostra-lhe como pode integrar as notificações e dados provenientes da sua configuração azure Digital Twins com a Azure Time Series Insights. Em seguida, pode visualizar os valores do sensor ao longo do tempo. Você pode procurar tendências como qual o quarto que está recebendo mais uso e quais são as horas mais movimentadas do dia. Também pode detetar anomalias como quais os quartos que se sentem mais abafados e quentes, ou se uma área do seu edifício está enviando valores de temperatura consistentemente elevados, indicando ar condicionado defeituoso.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Transmita dados utilizando hubs de eventos Azure.
> * Analise com insights da Série Temporal.

## <a name="prerequisites"></a>Pré-requisitos

Neste tutorial, parte-se do princípio de que [configurou](tutorial-facilities-setup.md) e [aprovisionou](tutorial-facilities-udf.md) a sua configuração do Azure Digital Twins. Antes de avançar, confirme que tem:

- Uma [conta Azure.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Uma instância do Digital Twins em execução.
- As [amostras Digital Twins C#](https://github.com/Azure-Samples/digital-twins-samples-csharp) foram descarregadas e extraídas na sua máquina de trabalho.
- [.NET Core SDK versão 2.1.403 ou mais tarde](https://www.microsoft.com/net/download) na sua máquina de desenvolvimento para executar a amostra. Corra `dotnet --version` para verificar se a versão certa está instalada.

> [!TIP]
> Use um nome único de gémeos digitais se estiver a fornecer uma nova instância.

## <a name="stream-data-by-using-event-hubs"></a>Transmitir dados utilizando hubs de eventos

Pode utilizar o serviço [Event Hubs](../event-hubs/event-hubs-about.md) para criar um pipeline para transmitir os seus dados. Esta secção mostra-lhe como criar o seu centro de eventos como o conector entre as suas gémeas digitais Azure e os casos de Insights da Série Time.

### <a name="create-an-event-hub"></a>Criar um hub de eventos

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **Criar um recurso**.

1. Procure e selecione **Hubs de Eventos**. Selecione **Criar**.

    [![Criar um espaço de nome sinuoso de hubs de eventos](./media/tutorial-facilities-analyze/tutorial-create-event-hubs.png)](./media/tutorial-facilities-analyze/tutorial-create-event-hubs.png#lightbox)

1. Insira um **nome** para o seu espaço de nome sinuoso do Event Hubs. Escolha **o Standard** para o nível de **Preços,** a sua **Subscrição,** o **grupo Derecursos** que utilizou para a sua instância de Gémeos Digitais e a **Localização.** Selecione **Criar**.

1. No espaço de nome sinos do Evento Hubs, selecione o painel de **visão geral** e, em seguida, selecione Ir para **o recurso**.

    [![Espaço de nome do Event Hubs após implantação](./media/tutorial-facilities-analyze/tutorial-event-hub-ns.png)](./media/tutorial-facilities-analyze/tutorial-event-hub-ns.png#lightbox)

1. No painel de visão **geral** do espaço de evento seleção de Hubs, selecione o botão **Event Hub** na parte superior.
    [![Botão Hub do Evento](./media/tutorial-facilities-analyze/tutorial-create-event-hub.png)](./media/tutorial-facilities-analyze/tutorial-create-event-hub.png#lightbox)

1. Introduza um **Nome** para o seu centro de eventos e selecione **Criar**.

   Após a implantação do hub do evento, aparece no painel de Hubs de **Eventos** do espaço de nome do Event Hubs com um estado **Ativo.** Selecione este centro de eventos para abrir o seu painel **de visão geral.**

1. Selecione o botão do **grupo Consumidor** na parte superior e introduza um nome como **tsievents** para o grupo de consumidores. Selecione **Criar**.

    [![Grupo de consumidores Do Hub de Eventos](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)](./media/tutorial-facilities-analyze/event-hub-consumer-group.png#lightbox)

   Após a criação do grupo de consumidores, aparece na lista na parte inferior do painel de **visão geral** do centro de eventos.

1. Abra o painel de políticas de **acesso partilhado** para o seu centro de eventos e selecione o botão **Adicionar.** Introduza **o ManageEnviar** como nome de política, certifique-se de que todas as caixas de verificação estão selecionadas e selecione **Criar**.

    [![Cadeias de ligação do Hub de Eventos](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)](./media/tutorial-facilities-analyze/event-hub-connection-strings.png#lightbox)

    > [!TIP]
    > Verifique se está a criar uma Política SAS para o seu caso de hub de eventos em vez do seu espaço de nome.

1. Abra a política **ManageSend** que criou e copie os valores para **a linha de ligação - chave primária** e fio de **ligação - chave secundária** para um ficheiro temporário. Você precisará destes valores para criar um ponto final para o centro de eventos na próxima secção.

### <a name="create-an-endpoint-for-the-event-hub"></a>Criar um ponto final para o centro de eventos

1. Na janela de comando, certifique-se de que está na pasta de **ocupação-quickstart\src** da amostra Das Gémeas Digitais Azure.

1. Abra o ficheiro **actions\createEndpoints.yaml** no editor. Substitua os conteúdos pelo seguinte:

    ```yaml
    - type: EventHub
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hub
    - type: EventHub
      eventTypes:
      - DeviceMessage
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hub
    ```

1. Substitua os `Primary_connection_string_for_your_event_hub` espaços reservados pelo valor da **corda de ligação - chave primária** para o centro do evento. Certifique-se de que o formato desta cadeia de ligação é o seguinte:

   ```ConnectionString
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
   ```

1. Substitua os `Secondary_connection_string_for_your_event_hub` espaços reservados pelo valor da **função de ligação - chave secundária** para o centro do evento. Certifique-se de que o formato desta cadeia de ligação é o seguinte: 

   ```ConnectionString
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
   ```

1. Substitua os `Name_of_your_Event_Hub` espaços reservados com o nome do seu Centro de Eventos.

    > [!IMPORTANT]
    > Introduza todos os valores, sem aspas. Certifique-se de que há pelo menos um personagem espacial depois dos cólons no ficheiro YAML. Também pode validar o conteúdo do seu ficheiro YAML utilizando qualquer validador YAML online, como [esta ferramenta](https://onlineyamltools.com/validate-yaml).

1. Guarde e feche o ficheiro. Na janela de comandos, execute o comando seguinte e inicie sessão com a sua conta do Azure quando lhe for pedido.

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Cria dois pontos finais para o seu centro de eventos.

   [![Pontos finais dos Hubs de Eventos](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png#lightbox)

## <a name="analyze-with-time-series-insights"></a>Analisar com o Time Series Insights

1. No painel esquerdo do [portal Azure,](https://portal.azure.com)selecione **Criar um recurso**. 

1. Procure e selecione um recurso de Disponibilidade Geral (GA) da **Time Series Insights.** Selecione **Criar**.

1. Introduza um **Nome** para a instância do Time Series Insights e selecione a sua **Subscrição**. Selecione o **grupo Derecursos** que utilizou para a sua instância De Gémeos Digitais e a sua **Localização**. Selecione **Seguinte:** Botão Fonte de Evento ou o separador **Fonte de Eventos.**

    [![Seleções para criar um exemplo de Insights da Série De Tempo](./media/tutorial-facilities-analyze/tutorial-create-tsi-environment.png)](./media/tutorial-facilities-analyze/tutorial-create-tsi-environment.png#lightbox)

1. No separador Fonte de **Eventos,** introduza um **Nome**, selecione **Event Hub** como **tipo Fonte,** e certifique-se de que os outros valores são selecionados corretamente para fazer referência ao centro de eventos que criou. Selecione **ManageSend** para nome de política de acesso ao **Event Hub**, e, em seguida, selecione o grupo de consumidores que criou na secção anterior para o grupo de **consumidores Event Hub**. Selecione **Rever + criar**.

    [![Seleções para criar uma fonte de evento](./media/tutorial-facilities-analyze/tutorial-tsi-event-source.png)](./media/tutorial-facilities-analyze/tutorial-tsi-event-source.png#lightbox)

1. No **painel Review + Create,** reveja a informação que inseriu e selecione **Criar**.

1. No painel de implantação, selecione o recurso Time Series Insights que criou. Abre o painel **de visão geral** para o seu ambiente Time Series Insights.

1. Selecione o botão **Ir para** o Ambiente na parte superior. Se receber um aviso de acesso a dados, abra o painel **de Políticas** de Acesso a Dados para a sua instância DeSpissio série de tempo, selecione Adicionar , selecione **Colaborador** como função e selecione o utilizador apropriado. **Add**

1. O botão **Go to Environment** abre o explorador time series [Insights](../time-series-insights/time-series-insights-explorer.md). Se não mostrar quaisquer eventos, simule eventos de dispositivos navegando no `dotnet run`projeto de conectividade de **dispositivos** da sua amostra De Gémeos Digitais, e executando .

1. Depois de gerar alguns eventos simulados, volte ao explorador time series Insights e selecione o botão de atualização na parte superior. Os seus gráficos analíticos que estão a ser criados para os seus dados de sensores simulados aparecerão. 

    [![Gráfico no explorador de Insights da Série De Tempo](./media/tutorial-facilities-analyze/tsi-explorer-with-adt-telemetry.png)](./media/tutorial-facilities-analyze/tsi-explorer-with-adt-telemetry.png#lightbox)

1. No explorador time series Insights, você pode então gerar gráficos e mapas de calor para diferentes eventos e dados a partir de seus quartos, sensores e outros recursos. Do lado esquerdo, use a **MEDIDA** e **SPLIT BY** drop-down boxes para criar as suas próprias visualizações. 

   Por exemplo, selecione **Eventos** para **MEDIDA** e **DigitalTwins-SensorHardwareId** para **SPLIT BY,** para gerar um mapa de calor para cada um dos seus sensores. O mapa de calor será semelhante à seguinte imagem:

   [![Mapa de calor no explorador de Insights da Série De Tempo](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png#lightbox)

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser parar de explorar as Gémeas Digitais Azure para além deste ponto, sinta-se à vontade para apagar os recursos criados neste tutorial:

1. A partir do menu esquerdo no [portal Azure,](https://portal.azure.com)selecione **Todos os recursos,** selecione o seu grupo de recursos Digital Twins e, em seguida, selecione **Delete**.

    > [!TIP]
    > Se teve problemas em apagar a sua instância De Gémeos Digitais, foi lançada uma atualização de serviço com a correção. Por favor, tente apagar o seu caso.

2. Se necessário, elimine as aplicações da amostra na sua máquina de trabalho.

## <a name="next-steps"></a>Passos seguintes

Vá ao próximo artigo para saber mais sobre gráficos de inteligência espacial e modelos de objetos em Azure Digital Twins.

> [!div class="nextstepaction"]
> [Understanding Digital Twins object models and spatial intelligence graph](concepts-objectmodel-spatialgraph.md) (Compreender os modelos de objetos e o gráfico de inteligência espacial do Digital Twins)
