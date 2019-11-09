---
title: Visualizar telemetria simulada com o Time Series Insights-Azure | Microsoft Docs
description: Saiba como configurar seu ambiente de Time Series Insights para explorar e analisar a telemetria gerada pelo acelerador de solução de simulação de dispositivo.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 2bbd7911a40d6a256d478e2533ad2469b8fd6973
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889334"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Usar Time Series Insights para visualizar a telemetria enviada do acelerador de solução de simulação de dispositivo

O acelerador de solução de simulação de dispositivo permite gerar telemetria de dispositivos simulados para testar suas soluções de IoT. Este guia de instruções mostra como Visualizar e analisar a telemetria simulada usando um ambiente de Time Series Insights.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir as etapas neste guia de instruções, você precisa de uma assinatura ativa do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

As etapas neste guia de instruções pressupõem que você implantou o acelerador de solução de simulação de dispositivo em sua assinatura do Azure. Se você ainda não tiver implantado o Solution Accelerator, siga as etapas no guia de início rápido [implantar e executar solução de simulação de dispositivo baseado em nuvem](quickstart-device-simulation-deploy.md) .

Este artigo pressupõe que o nome do Solution Accelerator é **contoso-Simulation**. Substitua **contoso-Simulation** pelo nome do Solution Accelerator conforme você concluir as etapas a seguir.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>Criar um grupo de consumidores

Você precisa criar um grupo de consumidores dedicado em seu hub IoT para transmitir a telemetria para o Time Series Insights. Uma origem do evento no Time Series Insights deve ter o uso exclusivo de um grupo de consumidores do Hub IoT.

As etapas a seguir usam o CLI do Azure no Azure Cloud Shell para criar o grupo de consumidores:

1. O Hub IoT é um dos vários recursos criados quando você implantou o acelerador de solução de simulação de dispositivo. Execute o seguinte comando para encontrar o nome do seu hub IoT-Lembre-se de usar o nome do acelerador de solução:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    O Hub IoT é o recurso do tipo **Microsoft. Devices/IotHubs**.

1. Adicione um grupo de consumidores chamado **devicesimulationtsi** ao Hub. No comando a seguir, use o nome do seu hub e acelerador de solução:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    Agora você pode fechar o Azure Cloud Shell.

## <a name="create-a-new-time-series-insights-environment"></a>Criar um novo ambiente de Time Series Insights

[Azure Time Series insights](../../articles/time-series-insights/time-series-insights-overview.md) é um serviço de análise, armazenamento e visualização totalmente gerenciado para gerenciar dados de série temporal de escala IOT na nuvem. Para criar um novo ambiente de Time Series Insights:

1. Iniciar sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione **criar um recurso** > **Internet das coisas** > **Time Series insights**:

    ![Novo Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. Para criar seu ambiente de Time Series Insights no mesmo grupo de recursos que o Solution Accelerator, use os valores na tabela a seguir:

    | Definição | Valor |
    | ------- | ----- |
    | Nome do ambiente | A captura de tela a seguir usa o nome **contoso-TSI**. Escolha seu próprio nome exclusivo ao concluir esta etapa. |
    | Subscrição | Selecione a sua subscrição do Azure na lista pendente. |
    | Grupo de recursos | **contoso – simulação**. Use o nome do Solution Accelerator. |
    | Localização | Este exemplo usa o **leste dos EUA**. Crie seu ambiente na mesma região que o acelerador de simulação de dispositivo. |
    | SKU |**S1** |
    | Capacidade | **1** |

    ![Criar Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > Adicionar o ambiente de Time Series Insights ao mesmo grupo de recursos que o Solution Accelerator significa que ele é excluído quando você exclui o Solution Accelerator.

1. Clique em **Criar**. Pode levar alguns minutos para que o ambiente seja criado.

## <a name="create-event-source"></a>Crie a origem de eventos

Crie uma nova origem de evento para se conectar ao Hub IoT. Use o grupo de consumidores criado nas etapas anteriores. Uma fonte de eventos Time Series Insights requer que um grupo de consumidores dedicado não esteja em uso por outro serviço.

1. No portal do Azure, navegue até o novo ambiente de série temporal.

1. À esquerda, clique em **origens do evento**:

    ![Exibir origens do evento](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. Clique em **Adicionar**:

    ![Adicionar origem do evento](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. Para configurar o Hub IoT como uma nova origem de evento, use os valores na tabela a seguir:

    | Definição | Valor |
    | ------- | ----- |
    | Nome da origem do evento | A captura de tela a seguir usa o nome **contoso-IOT-Hub**. Use seu próprio nome exclusivo ao concluir esta etapa. |
    | Origem | **Hub IoT** |
    | Opção de Importar | **Usar o Hub IoT de assinaturas disponíveis** |
    | ID da subscrição | Selecione a sua subscrição do Azure na lista pendente. |
    | Nome do Hub IOT | **contoso-simulation7d894**. Use o nome do seu hub IoT do acelerador de solução de simulação de dispositivo. |
    | Nome da política do Hub IOT | **iothubowner** |
    | Chave de política do Hub IOT | Esse campo é preenchido automaticamente. |
    | Grupo de consumidores do Hub IOT | **devicesimulationtsi** |
    | Formato de serialização de evento | **JSON** |
    | Nome da propriedade Timestamp | Deixar em branco |

    ![Criar origem do evento](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. Clique em **Criar**.

> [!NOTE]
> Você pode [conceder a usuários adicionais acesso](../../articles/time-series-insights/time-series-insights-data-access.md#grant-data-access) ao time Series insights Explorer.

## <a name="start-a-simulation"></a>Iniciar uma simulação

Antes de usar o Time Series Insights Explorer, configure o acelerador de solução de simulação de dispositivo para gerar uma telemetria. A captura de tela a seguir mostra uma simulação em execução com 10 dispositivos resfriadores:

![Simulação de dispositivo em execução](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Explorador do Time Series Insights

O Time Series Insights Explorer é um aplicativo Web que você pode usar para visualizar sua telemetria.

1. Na portal do Azure, selecione a guia **visão geral** Time Series insights.

1. Para abrir o aplicativo Web Time Series Insights Explorer, clique em **ir para o ambiente**:

    ![Explorador do Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. No painel seleção de tempo, selecione **últimos 30 minutos** no menu horários rápidos e clique em **Pesquisar**:

    ![Pesquisa do Time Series Insights Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. No painel termos à esquerda, selecione **temperatura** como a **medida** e **iothub-Connection-Device-ID** como a **divisão por** valor:

    ![Consulta do Time Series Insights Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Clique com o botão direito do mouse no gráfico e selecione **explorar eventos**:

    ![Eventos do Time Series Insights Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. Os dados de evento são mostrados em uma grade:

    ![Tabela do Time Series Insights Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Clique no botão exibição de perspectiva:

    ![Perspectiva do Time Series Insights Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Clique em **+** para adicionar uma nova consulta à perspectiva:

    ![Adicionar consulta do Time Series Insights Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Selecione **últimos 30 minutos** como o período de tempo, **umidade** como a **medida**e **iothub-Connection-Device-ID** como a **divisão por** valor:

    ![Consulta do Time Series Insights Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. Clique no botão exibição de perspectiva para exibir o painel de telemetria do dispositivo:

    ![Painel do Time Series Insights Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja explorar ainda mais, deixe o Solution Accelerator implantado.

Se você não precisar mais do Solution Accelerator, exclua-o da página [soluções provisionadas](https://www.azureiotsolutions.com/Accelerators#dashboard) , selecionando-o e, em seguida, clicando em **excluir solução**.

Se você adicionou o ambiente de Time Series Insights ao grupo de recursos do Solution Accelerator, ele será excluído automaticamente quando você excluir o Solution Accelerator. Caso contrário, você deve remover manualmente o ambiente de Time Series Insights da portal do Azure.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como explorar e consultar dados no Time Series Insights Explorer, consulte [Azure Time Series insights Explorer](../time-series-insights/time-series-insights-explorer.md).
