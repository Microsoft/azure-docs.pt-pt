---
title: 'Tutorial: Analisar eventos a partir da configuração do Azure Digital Twins | Microsoft Docs'
description: Saiba como Visualizar e analisar eventos de seus espaços de gêmeos digitais do Azure, com Azure Time Series Insights, usando as etapas neste tutorial.
services: digital-twins
author: alinamstanciu
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 09/23/2019
ms.author: alinast
ms.openlocfilehash: d155d3566ef87e8a21cdc3e701892144c613f694
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219282"
---
# <a name="tutorial-visualize-and-analyze-events-from-your-azure-digital-twins-spaces-by-using-time-series-insights"></a>Tutorial: Visualize e analise eventos de seus espaços de gêmeos digitais do Azure usando Time Series Insights

Depois de implantar sua instância do gêmeos digital do Azure, provisionar seus espaços e implementar uma função personalizada para monitorar condições específicas, você poderá visualizar os eventos e os dados provenientes de seus espaços para procurar tendências e anomalias.

No [primeiro tutorial](tutorial-facilities-setup.md), você configurou o grafo espacial de uma compilação imaginária, com uma sala que contém sensores de movimento, dióxido de carbono e temperatura. No [segundo tutorial](tutorial-facilities-udf.md), aprovisionou o gráfico e uma função definida pelo utilizador. A função monitora esses valores de sensor e dispara notificações para as condições corretas. Ou seja, a sala está vazia e os níveis de temperatura e dióxido de carbono são normais.

Este tutorial mostra como você pode integrar as notificações e os dados provenientes de sua configuração do gêmeos digital do Azure com o Azure Time Series Insights. Em seguida, você pode visualizar os valores do sensor ao longo do tempo. Você pode procurar tendências como, por exemplo, qual espaço está obtendo mais uso e quais são os horários mais ocupados do dia. Você também pode detectar anomalias como, por exemplo, quais salas sentem-se stuffier e mais quente, ou se uma área em sua construção está enviando valores de alta temperatura consistentemente altos, indicando falhas de ar condicionado.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Transmita dados usando os hubs de eventos do Azure.
> * Analise com Time Series Insights.

## <a name="prerequisites"></a>Pré-requisitos

Neste tutorial, parte-se do princípio de que [configurou](tutorial-facilities-setup.md) e [aprovisionou](tutorial-facilities-udf.md) a sua configuração do Azure Digital Twins. Antes de avançar, confirme que tem:

- Uma [conta do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uma instância do Digital Twins em execução.
- Os [exemplos do Digital Twins em C#](https://github.com/Azure-Samples/digital-twins-samples-csharp) transferidos e extraídos para o computador de trabalho.
- [SDK do .NET Core versão 2.1.403 ou posterior](https://www.microsoft.com/net/download) em seu computador de desenvolvimento para executar o exemplo. Execute `dotnet --version` para verificar se a versão correta está instalada.

> [!TIP]
> Use um nome de instância de gêmeos digital exclusivo se você estiver Provisionando uma nova instância.

## <a name="stream-data-by-using-event-hubs"></a>Transmitir dados usando hubs de eventos

Você pode usar o serviço de [hubs de eventos](../event-hubs/event-hubs-about.md) para criar um pipeline para transmitir seus dados. Esta seção mostra como criar seu hub de eventos como o conector entre suas instâncias do gêmeos e do Time Series Insights digital do Azure.

### <a name="create-an-event-hub"></a>Criar um hub de eventos

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **criar um recurso**.

1. Procure e selecione **Hubs de Eventos**. Selecione **Criar**.

1. Insira um **nome** para seu namespace de hubs de eventos. Escolha **Standard** para **tipo de preço**, sua **assinatura**, o **grupo de recursos** que você usou para sua instância de gêmeos digital e o **local**. Selecione **Criar**.

1. Na implantação do namespace de hubs de eventos, selecione o painel **visão geral** e, em seguida, selecione **ir para recurso**.

    ![Namespace de hubs de eventos após a implantação](./media/tutorial-facilities-analyze/open-event-hub-ns.png)

1. No painel **visão geral** do namespace de hubs de eventos, selecione o botão **Hub de eventos** na parte superior.
    ![Botão Hub de eventos](./media/tutorial-facilities-analyze/create-event-hub.png)

1. Insira um **nome** para o Hub de eventos e selecione **criar**.

   Depois que o Hub de eventos é implantado, ele aparece no painel **hubs de eventos** do namespace de hubs de eventos com um status **ativo** . Selecione este Hub de eventos para abrir seu painel **visão geral** .

1. Selecione o botão **grupo de consumidores** na parte superior e insira um nome como **tsievents** para o grupo de consumidores. Selecione **Criar**.

    ![Grupo de consumidores do Hub de Eventos](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)

   Depois que o grupo de consumidores é criado, ele aparece na lista na parte inferior do painel **visão geral** do hub de eventos.

1. Abra o painel **políticas de acesso compartilhado** para seu hub de eventos e selecione o botão **Adicionar** . Insira **ManageSend** como o nome da política, verifique se todas as caixas de seleção estão marcadas e selecione **criar**.

    ![Cadeias de ligação do Hub de Eventos](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)

1. Abra a política ManageSend que você criou e copie os valores para **cadeia de conexão--chave primária** e **cadeia de conexão--chave secundária** para um arquivo temporário. Você precisará desses valores para criar um ponto de extremidade para o Hub de eventos na próxima seção.

### <a name="create-an-endpoint-for-the-event-hub"></a>Criar um ponto de extremidade para o Hub de eventos

1. Na janela de comando, verifique se você está na pasta **occupancy-quickstart\src** do exemplo gêmeos digital do Azure.

1. Abra o arquivo **actions\createEndpoints.YAML** no editor. Substitua os conteúdos pelo seguinte:

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

1. Substitua os espaços reservados `Primary_connection_string_for_your_event_hub` pelo valor da **cadeia de conexão--chave primária** para o Hub de eventos. Verifique se o formato dessa cadeia de conexão é o seguinte:

   ```plaintext
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
   ```

1. Substitua os espaços reservados `Secondary_connection_string_for_your_event_hub` pelo valor da **cadeia de conexão--chave secundária** para o Hub de eventos. Verifique se o formato dessa cadeia de conexão é o seguinte: 

   ```plaintext
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
   ```

1. Substitua os espaços reservados `Name_of_your_Event_Hub` pelo nome do hub de eventos.

    > [!IMPORTANT]
    > Introduza todos os valores, sem aspas. Verifique se há pelo menos um caractere de espaço após os dois-pontos no arquivo YAML. Você também pode validar o conteúdo do arquivo YAML usando qualquer validador YAML online, como [essa ferramenta](https://onlineyamltools.com/validate-yaml).

1. Guarde e feche o ficheiro. Na janela de comandos, execute o comando seguinte e inicie sessão com a sua conta do Azure quando lhe for pedido.

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Ele cria dois pontos de extremidade para o Hub de eventos.

   ![Pontos finais dos Hubs de Eventos](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)

## <a name="analyze-with-time-series-insights"></a>Analisar com o Time Series Insights

1. No painel esquerdo da [portal do Azure](https://portal.azure.com), selecione **criar um recurso**. 

1. Pesquise e selecione um recurso de GA (disponibilidade geral) **Time Series insights** . Selecione **Criar**.

1. Introduza um **Nome** para a instância do Time Series Insights e selecione a sua **Subscrição**. Selecione o **grupo de recursos** que você usou para sua instância de gêmeos digital e seu **local**. Selecione **avançar: Botão origem** do evento ou a guia **origem do evento** .

    ![Seleções para criar uma instância de Time Series Insights](./media/tutorial-facilities-analyze/create-tsi.png)

1. Na guia **origem do evento** , insira um **nome**, selecione **Hub de eventos** como o **tipo de origem**e verifique se os outros valores foram selecionados corretamente. Selecione **ManageSend** para **nome da política de acesso do hub de eventos**e, em seguida, selecione o grupo de consumidores que você criou na seção anterior para o grupo de consumidores do hub de **eventos**. Selecione **Rever + criar**.

    ![Seleções para criar uma origem de evento](./media/tutorial-facilities-analyze/tsi-event-source.png)

1. No painel **revisar + criar** , examine as informações inseridas e selecione **criar**.

1. No painel implantação, selecione o Time Series Insights recurso que você acabou de criar. Ele abre o painel **visão geral** do seu ambiente de time Series insights.

1. Selecione o botão **ir para ambiente** na parte superior. Se você receber um aviso de acesso a dados, abra o painel **políticas de acesso a dados** para sua instância do time Series insights, selecione **Adicionar**, **colaborador** como a função e selecione o usuário apropriado.

1. O botão **ir para ambiente** abre o [Time Series insights Explorer](../time-series-insights/time-series-insights-explorer.md). Se não mostrar nenhum evento, simule eventos de dispositivo navegando até o projeto de **conectividade de dispositivo** de seu exemplo de gêmeos digital e `dotnet run`executando.

1. Depois que alguns eventos simulados forem gerados, volte para o Time Series Insights Explorer e selecione o botão atualizar na parte superior. Você deve ver os gráficos analíticos sendo criados para os dados do sensor simulado. 

    ![Gráfico no Time Series Insights Explorer](./media/tutorial-facilities-analyze/tsi-explorer.png)

1. No Time Series Insights Explorer, você pode gerar gráficos e calor para diferentes eventos e dados de suas salas, sensores e outros recursos. No lado esquerdo, use as caixas suspensa **medida** e **divisão por** para criar suas próprias visualizações. 

   Por exemplo, selecione **eventos** para **Measure** e **DIGITALTWINS-SensorHardwareId** para **divisão por**, para gerar um calor para cada um dos sensores. O calor será semelhante à imagem a seguir:

   ![Calor no Time Series Insights Explorer](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser interromper a exploração do Azure digital gêmeos além desse ponto, sinta-se à vontade para excluir os recursos criados neste tutorial:

1. No menu à esquerda na [portal do Azure](https://portal.azure.com), selecione **todos os recursos**, selecione o grupo de recursos digital gêmeos e, em seguida, selecione **excluir**.

    > [!TIP]
    > Se teve problemas ao eliminar a instância de duplos Digital, uma atualização de serviço capacidade foi implementada com a correção. Volte a tentar eliminar a instância.

2. Se necessário, exclua os aplicativos de exemplo em seu computador de trabalho.

## <a name="next-steps"></a>Passos seguintes

Vá para o próximo artigo para saber mais sobre gráficos de inteligência espacial e modelos de objeto no gêmeos digital do Azure.

> [!div class="nextstepaction"]
> [Understanding Digital Twins object models and spatial intelligence graph](concepts-objectmodel-spatialgraph.md) (Compreender os modelos de objetos e o gráfico de inteligência espacial do Digital Twins)
