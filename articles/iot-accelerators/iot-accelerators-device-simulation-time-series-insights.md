---
title: Visualizar telemetria simulada com Time Series Insights - Azure Microsoft Docs
description: Aprenda a configurar o seu ambiente Time Series Insights para explorar e analisar a telemetria gerada pelo acelerador de solução de simulação de dispositivo.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 2bbd7911a40d6a256d478e2533ad2469b8fd6973
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889334"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Use insights da Série de Tempo para visualizar a telemetria enviada do acelerador de solução de simulação de dispositivo

O acelerador de solução de simulação de dispositivo permite-lhe gerar telemetria a partir de dispositivos simulados para testar as suas soluções IoT. Este guia mostra-lhe como visualizar e analisar a telemetria simulada usando um ambiente time series Insights.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos neste guia de como orientar, precisa de uma subscrição azure ativa. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Os passos neste guia de como orientar assumem que implementou o acelerador de solução de simulação de dispositivo para a sua subscrição Azure. Se ainda não tiver implantado o acelerador de solução, siga os passos no [Deploy e execute uma solução de simulação de dispositivo baseada na nuvem.](quickstart-device-simulation-deploy.md)

Este artigo assume que o nome do seu acelerador de soluções é **contoso-simulação**. Substitua **a simulação de contos** com o nome do acelerador da sua solução à medida que completa os seguintes passos.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>Criar um grupo de consumidores

Você precisa criar um grupo de consumidores dedicado no seu hub IoT para transmitir telemetria para Time Series Insights. Uma fonte de evento sintetizadora da Time Series Insights deve ter o uso exclusivo de um grupo de consumidores IoT Hub.

Os seguintes passos utilizam o Azure CLI na Casca de Nuvem Azure para criar o grupo de consumidores:

1. O hub IoT é um dos vários recursos criados quando implementou o acelerador de solução de simulação de dispositivo. Execute o seguinte comando encontre o nome do seu hub IoT - lembre-se de usar o nome do seu acelerador de solução:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    O hub IoT é o recurso do tipo **Microsoft.Devices/IotHubs**.

1. Adicione um grupo de consumidores chamado **devicesimulationtsi** ao centro. No comando seguinte utilize o nome do seu hub e acelerador de soluções:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    Agora pode fechar a Casca de Nuvem Azure.

## <a name="create-a-new-time-series-insights-environment"></a>Criar um novo ambiente time series Insights

[Azure Time Series Insights](../../articles/time-series-insights/time-series-insights-overview.md) é um serviço de análise, armazenamento e visualização totalmente gerido para gerir dados da série de tempo em escala IoT na nuvem. Para criar um novo ambiente time series Insights:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

1. Selecione **Criar um recurso** > **Internet of Things** > **Time Series Insights:**

    ![Insights da série new time](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. Para criar o seu ambiente Time Series Insights no mesmo grupo de recursos que o seu acelerador de soluções, utilize os valores na tabela seguinte:

    | Definição | Valor |
    | ------- | ----- |
    | Nome do Ambiente | A imagem seguinte usa o nome **Contoso-TSI**. Escolha o seu nome único quando completar este passo. |
    | Subscrição | Selecione a sua subscrição do Azure na lista pendente. |
    | Grupo de recursos | **Contoso-simulação.** Utilize o nome do seu acelerador de solução. |
    | Localização | Este exemplo utiliza **os EUA Orientais.** Crie o seu ambiente na mesma região que o seu acelerador de simulação do Dispositivo. |
    | Sku |**S1** |
    | Capacidade | **1** |

    ![Criar insights da série de tempo](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > Adicionar o ambiente Time Series Insights ao mesmo grupo de recursos que o acelerador de solução significa que é eliminado quando elimina o acelerador de soluções.

1. Clique em **Criar**. Pode levar alguns minutos para o ambiente ser criado.

## <a name="create-event-source"></a>Crie a origem de eventos

Crie uma nova fonte de evento para se conectar ao seu hub IoT. Utilize o grupo de consumidores que criou nos passos anteriores. Uma fonte de evento time series Insights requer um grupo de consumidores dedicado que não seja utilizado por outro serviço.

1. No portal Azure, navegue para o seu novo Ambiente time series.

1. À esquerda, clique em **Fontes de Evento:**

    ![Ver Fontes de Eventos](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. Clique em **Adicionar**:

    ![Adicionar Fonte de Evento](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. Para configurar o seu hub IoT como uma nova fonte de evento, utilize os valores na tabela seguinte:

    | Definição | Valor |
    | ------- | ----- |
    | Nome da fonte do evento | A imagem seguinte usa o nome **contoso-iot-hub**. Use o seu próprio nome único quando completar este passo. |
    | Origem | **IoT Hub** |
    | Opção de Importar | **Use o IoT Hub a partir de subscrições disponíveis** |
    | ID da subscrição | Selecione a sua subscrição do Azure na lista pendente. |
    | Nome do hub IoT | **contos-simulação7d894**. Utilize o nome do seu hub IoT do acelerador de solução de simulação de dispositivo. |
    | Nome da política do hub IoT | **iothubowner** |
    | Chave política do hub de iot | Este campo é povoado automaticamente. |
    | Grupo de consumidores do hut IoT | **dispositivosimulationtsi** |
    | Formato de serialização de eventos | **Rio JSON** |
    | Nome da propriedade Carimbo de data/hora | Deixar em branco |

    ![Criar fonte de evento](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. Clique em **Criar**.

> [!NOTE]
> Pode [conceder aos utilizadores acesso adicional](../../articles/time-series-insights/time-series-insights-data-access.md#grant-data-access) ao explorador de Insights da Série Time.

## <a name="start-a-simulation"></a>Iniciar uma simulação

Antes de utilizar o explorador time series Insights, configure o acelerador de solução de simulação de dispositivo para gerar alguma telemetria. A seguinte imagem mostra uma simulação de execução com 10 dispositivos de refrigeração:

![Simulação de dispositivo de execução](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Explorador do Time Series Insights

O explorador time series Insights é uma aplicação web que pode usar para visualizar a sua telemetria.

1. No portal Azure, selecione o separador De **Visão Geral** da Série Time Insights.

1. Para abrir a aplicação web do explorador Time Series Insights, clique em **Ir para o Ambiente:**

    ![Explorador do Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. No painel de seleção de tempo, selecione **Last 30 minutos** a partir do menu de tempos rápidos e clique **em Procurar:**

    ![Pesquisa de exploradores de Insights da Série De Tempo](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. No painel à esquerda, selecione **a temperatura** como **medida** e **iothub-ligação-dispositivo-id** como **o Split Por** valor:

    ![Consulta de explorador de Insights da Série De Tempo](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Clique à direita na tabela e **selecione Explore eventos:**

    ![Eventos exploradores de Insights da Série De Tempo](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. Os dados do evento mostram numa grelha:

    ![Tabela de exploradores de Insights da Série De Tempo](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Clique no botão de visão de perspetiva:

    ![Perspetiva do explorador de Insights da Série Do Tempo](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Clique **+** para adicionar uma nova consulta à perspetiva:

    ![Explorador de Insights da Série De Tempo Adicionar Consulta](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Selecione **Last 30 minutos** como o tempo, **humidade** como **medida**, e **iothub-ligação-dispositivo-id** como **o Split Por** valor:

    ![Consulta de explorador de Insights da Série De Tempo](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. Clique no botão de visão de perspetiva para ver o painel de telemetria do seu dispositivo:

    ![Painel de instrumentos de explorador de Insights da Série De Tempo](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se planeia explorar mais, deixe o acelerador de solução implantado.

Se já não necessitar do acelerador de solução, elimine-o da página de [soluções Provisionadas,](https://www.azureiotsolutions.com/Accelerators#dashboard) selecionando-a e clicando em **Eliminar a Solução**.

Se adicionou o ambiente Time Series Insights ao grupo de recursos do acelerador de soluções, é automaticamente eliminado quando elimina o acelerador de soluções. Caso contrário, deve remover manualmente o ambiente Time Series Insights do portal Azure.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como explorar e consultar dados no explorador de Insights da Série Time, consulte [o explorador de Insights da Série Time.](../time-series-insights/time-series-insights-explorer.md)
