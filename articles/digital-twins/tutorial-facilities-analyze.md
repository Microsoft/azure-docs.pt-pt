---
title: 'Tutorial: Analisar eventos a partir da configuração do Azure Digital Twins | Microsoft Docs'
description: Saiba como visualizar e analisar eventos a partir de seus espaços duplos Digital do Azure, com o Azure Time Series Insights, utilize os passos neste tutorial.
services: digital-twins
author: dsk-2015
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 12/18/2018
ms.author: dkshir
ms.openlocfilehash: 8f8bdb4d601e35cf489c1c4c1db3574fb8c8f18f
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56730790"
---
# <a name="tutorial-visualize-and-analyze-events-from-your-azure-digital-twins-spaces-by-using-time-series-insights"></a>Tutorial: Visualize e analise os eventos a partir de seus espaços duplos Digital do Azure com o Time Series Insights

Depois de implementar a sua instância de duplos Digital do Azure, aprovisionar os seus espaços e implementar uma função personalizada para monitorizar a condições específicas, pode ver os eventos e os dados provenientes de seus espaços para procurar tendências e anomalias.

Na [o primeiro tutorial](tutorial-facilities-setup.md), configurou o gráfico geográfico de um edifício imaginário, com um espaço que contém os sensores de temperatura, dióxido de carbono e movimento. No [segundo tutorial](tutorial-facilities-udf.md), aprovisionou o gráfico e uma função definida pelo utilizador. A função monitoriza estes valores de sensor e dispara notificações para as condições adequadas. Ou seja, o espaço está vazio e os níveis de temperatura e as emissões de dióxido de carbono são normais.

Este tutorial mostra-lhe como integrar as notificações e dados provenientes da sua configuração de duplos Digital do Azure com o Azure Time Series Insights. Em seguida, é possível visualizar seus valores de sensor ao longo do tempo. Pode procurar tendências, como o espaço é obter o máximo partido e quais são as mais ocupadas horas do dia. Também pode detetar anomalias, como se sentir que os ambientes de stuffier e muita ou se está a enviar valores de temperatura consistentemente alta, uma área em sua criação com a indicação de falha ar-condicionado.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Dados de Stream ao utilizar os Hubs de eventos do Azure.
> * Analise com o Time Series Insights.

## <a name="prerequisites"></a>Pré-requisitos

Neste tutorial, parte-se do princípio de que [configurou](tutorial-facilities-setup.md) e [aprovisionou](tutorial-facilities-udf.md) a sua configuração do Azure Digital Twins. Antes de avançar, confirme que tem:

- Uma [conta do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uma instância do Digital Twins em execução.
- Os [exemplos do Digital Twins em C#](https://github.com/Azure-Samples/digital-twins-samples-csharp) transferidos e extraídos para o computador de trabalho.
- [.NET core SDK versão 2.1.403 ou posterior](https://www.microsoft.com/net/download) no computador de desenvolvimento para executar o exemplo. Executar `dotnet --version` para verificar se a versão correta está instalada.

## <a name="stream-data-by-using-event-hubs"></a>Dados de Stream ao utilizar os Hubs de eventos

Pode utilizar o [os Hubs de eventos](../event-hubs/event-hubs-about.md) serviço para criar um pipeline para transmitir os seus dados. Esta secção mostra-lhe como criar o seu hub de eventos como conector entre as instâncias de duplos Digital do Azure e o Time Series Insights.

### <a name="create-an-event-hub"></a>Criar um hub de eventos

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **criar um recurso**.

1. Procure e selecione **Hubs de Eventos**. Selecione **Criar**.

1. Introduza um **nome** para o espaço de nomes de Hubs de eventos. Escolher **padrão** para **escalão de preço**, o seu **subscrição**, o **grupo de recursos** que utilizou para a sua instância de duplos Digital, e o **localização**. Selecione **Criar**.

1. Numa implementação de espaço de nomes dos Hubs de eventos, selecione o espaço de nomes em **recursos**.

    ![Espaço de nomes de Hubs de eventos após a implementação](./media/tutorial-facilities-analyze/open-event-hub-ns.png)

1. No espaço de nomes dos Hubs de eventos **descrição geral** painel, selecione a **Hub de eventos** botão na parte superior.
    ![Botão de Hub de eventos](./media/tutorial-facilities-analyze/create-event-hub.png)

1. Introduza um **Name** para o seu hub de eventos e selecione **criar**.

   Depois do hub de eventos é implementado, é apresentado na **os Hubs de eventos** painel do espaço de nomes dos Hubs de eventos com um **Active Directory** estado. Selecione este hub de eventos para abrir o respetivo **descrição geral** painel.

1. Selecione o **grupo de consumidores** botão na parte superior e introduza um nome, tal como **tsievents** para o grupo de consumidores. Selecione **Criar**.

    ![Grupo de consumidor do Hub de Eventos](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)

   Depois de criar o grupo de consumidores, ele aparece na lista na parte inferior do hub de eventos **descrição geral** painel.

1. Abra o **políticas de acesso partilhado** painel para o seu hub de eventos e selecione o **Add** botão. Introduza **ManageSend** como o nome da política, certifique-se de que todas as caixas de verificação estão selecionadas e selecione **criar**.

    ![Cadeias de ligação do Hub de Eventos](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)

1. Abra a política de ManageSend que criou e copie os valores para **cadeia de ligação – chave primária** e **cadeia de ligação – chave secundária** num arquivo temporário. Precisará estes valores para criar um ponto final para o hub de eventos na próxima seção.

### <a name="create-an-endpoint-for-the-event-hub"></a>Criar um ponto final para o hub de eventos

1. Na janela de comando, certifique-se de que está na **ocupação quickstart\src** pasta do exemplo duplos Digital do Azure.

1. Abra o ficheiro **actions\createEndpoints.yaml** no seu editor. Substitua os conteúdos pelo seguinte:

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

1. Substitua os marcadores de posição `Primary_connection_string_for_your_event_hub` com o valor de **cadeia de ligação – chave primária** para o hub de eventos. Certifique-se de que o formato desta cadeia de ligação é o seguinte:

   ```plaintext
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
   ```

1. Substitua os marcadores de posição `Secondary_connection_string_for_your_event_hub` com o valor de **cadeia de ligação – chave secundária** para o hub de eventos. Certifique-se de que o formato desta cadeia de ligação é o seguinte: 

   ```plaintext
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
   ```

1. Substitua os marcadores de posição `Name_of_your_Event_Hub` com o nome do seu Hub de eventos.

    > [!IMPORTANT]
    > Introduza todos os valores, sem aspas. Certificar-se de que existe caráter de espaço, pelo menos, uma após a vírgula no ficheiro YAML. Também pode validar o seu conteúdo do ficheiro YAML, utilizando qualquer validador YAML online, como [essa ferramenta](https://onlineyamltools.com/validate-yaml).

1. Guarde e feche o ficheiro. Na janela de comandos, execute o comando seguinte e inicie sessão com a sua conta do Azure quando lhe for pedido.

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Ele cria dois pontos de extremidade para o seu hub de eventos.

   ![Pontos finais dos Hubs de Eventos](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)

## <a name="analyze-with-time-series-insights"></a>Analisar com o Time Series Insights

1. No painel esquerdo dos [portal do Azure](https://portal.azure.com), selecione **criar um recurso**. 

1. Procure e selecione um recurso novo do **Time Series Insights**. Selecione **Criar**.

1. Introduza um **Nome** para a instância do Time Series Insights e selecione a sua **Subscrição**. Selecione o **grupo de recursos** que utilizou para a sua instância de duplos Digital e seu **localização**. Selecione **Criar**.

    ![Seleções para a criação de uma instância do Time Series Insights](./media/tutorial-facilities-analyze/create-tsi.png)

1. Depois da instância for implementada, abrir o ambiente do Time Series Insights e, em seguida, abra seu **origens de eventos** painel. Selecione o **adicionar** botão na parte superior para adicionar um grupo de consumidores.

1. No painel **New event source** (Nova origem de evento), introduza **Name** (Nome) e confirme que os outros valores estão devidamente selecionados. Selecione **ManageSend** para **nome de política do hub de eventos**e, em seguida, selecione o grupo de consumidores que criou na secção anterior para **grupo de consumidores do hub de eventos**. Selecione **Criar**.

    ![Seleções para a criação de uma origem de evento](./media/tutorial-facilities-analyze/tsi-event-source.png)

1. Abra o **descrição geral** painel para seu ambiente do Time Series Insights e selecione o **vá para o ambiente** botão na parte superior. Se receber um aviso de acesso de dados, abra a **políticas de acesso de dados** painel para a sua instância do Time Series Insights, selecione **Add**, selecione **contribuinte** como a função e selecione o utilizador apropriado.

1. O **vá para o ambiente** botão abre a [Explorador do Time Series Insights](../time-series-insights/time-series-insights-explorer.md). Se não mostra todos os eventos, simular eventos de dispositivo ao navegar para o **conectividade do dispositivo** projeto do exemplo de duplos Digital e a execução `dotnet run`.

1. Depois de alguns eventos simulados são gerados, voltar para o Explorador do Time Series Insights e selecione o botão de atualização na parte superior. Deverá ver gráficos analíticos, que está sendo criados para os seus dados de sensor simulado. 

    ![No Explorador do Time Series Insights do gráfico](./media/tutorial-facilities-analyze/tsi-explorer.png)

1. No Explorador do Time Series Insights, pode gerar, em seguida, gráficos e mapas térmicos para diferentes eventos e dados a partir de seus ambientes, sensores e outros recursos. No lado esquerdo, utilize o **medida** e **DIVIDIR por** caixas suspensas para criar suas próprias visualizações. 

   Por exemplo, seleccione **eventos** para **medida** e **DigitalTwins SensorHardwareId** para **DIVIDIR por**, para gerar um mapa térmico para cada um dos aos sensores. O mapa térmico será semelhante à imagem seguinte:

   ![Mapa térmico no Explorador do Time Series Insights](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender parar a explorar duplos Digital do Azure para além deste ponto, fique à vontade eliminar recursos criados neste tutorial:

1. No menu do lado esquerdo da [portal do Azure](http://portal.azure.com), selecione **todos os recursos**, selecione o grupo de recursos de duplos Digital e, em seguida, selecione **eliminar**.

    > [!TIP]
    > Se teve problemas ao eliminar a instância de duplos Digital, uma atualização de serviço capacidade foi implementada com a correção. Volte a tentar eliminar a instância.

2. Se necessário, elimine os aplicativos de exemplo no seu computador de trabalho.

## <a name="next-steps"></a>Passos Seguintes

Vá para o artigo seguinte para saber mais sobre os gráficos de inteligência geográficos e modelos de objeto no duplos Digital do Azure.

> [!div class="nextstepaction"]
> [Understanding Digital Twins object models and spatial intelligence graph](concepts-objectmodel-spatialgraph.md) (Compreender os modelos de objetos e o gráfico de inteligência espacial do Digital Twins)
