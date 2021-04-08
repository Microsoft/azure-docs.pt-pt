---
title: Visualizar telemetria simulada com Insights de Séries Tempotárias - Azure | Microsoft Docs
description: Aprenda a configurar o seu ambiente de Insights de Séries Temporais para explorar e analisar a telemetria gerada pelo acelerador de solução de simulação de dispositivos.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: e1409b43f0ce1fc0d8c622dda79e857ac6abdd33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96854568"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Use Insights de Séries temporais para visualizar a telemetria enviada do acelerador de solução de simulação do dispositivo

O acelerador de solução de simulação de dispositivo permite-lhe gerar telemetria a partir de dispositivos simulados para testar as suas soluções IoT. Este guia de como visualizar e analisar a telemetria simulada utilizando um ambiente de Insights de Séries Tempotárias.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos neste guia de como fazer, precisa de uma subscrição ativa do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Os passos neste guia assumem que implementou o acelerador de solução de Simulação de Dispositivos para a sua subscrição Azure. Se ainda não implementou a Simulação do Dispositivo, consulte a [implementação da simulação do dispositivo](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) no GitHub.

Este artigo assume que o nome do seu acelerador de solução é **contoso-simulação.** Substitua **a simulação de contoso** pelo nome do seu acelerador de solução à medida que completa os seguintes passos.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>Criar um grupo de consumidores

Você precisa criar um grupo de consumidores dedicado no seu hub IoT para transmitir telemetria para Time Series Insights. Uma fonte de eventos na Time Series Insights deve ter o uso exclusivo de um grupo de consumidores IoT Hub.

Os seguintes passos utilizam o Azure CLI na Azure Cloud Shell para criar o grupo de consumidores:

1. O hub IoT é um dos vários recursos criados quando implementou o acelerador de solução de simulação de dispositivos. Execute o seguinte comando encontre o nome do seu hub IoT - lembre-se de usar o nome do seu acelerador de solução:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    O hub IoT é o recurso do tipo **Microsoft.Devices/IotHubs**.

1. Adicione um grupo de consumidores chamado **devicesimulationtsi** ao centro. No seguinte comando utilize o nome do seu hub e acelerador de solução:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    Agora pode fechar a Azure Cloud Shell.

## <a name="create-a-new-time-series-insights-environment"></a>Criar um novo ambiente de Insights de Séries Temporências

[Azure Time Series Insights](../../articles/time-series-insights/time-series-insights-overview.md) é um serviço de análise, armazenamento e visualização totalmente gerido para gerir dados de séries de tempo à escala IoT na nuvem. Para criar um novo ambiente de Insights de Séries Tempos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione **Criar um recurso** Internet of  >  **Things** Time  >  **Series Insights**:

    ![Novos Insights da Série Temporal](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. Para criar o seu ambiente Time Series Insights no mesmo grupo de recursos que o seu acelerador de solução, utilize os valores no quadro seguinte:

    | Definição | Valor |
    | ------- | ----- |
    | Nome do Ambiente | A imagem que se segue utiliza o nome **Contoso-TSI**. Escolha o seu próprio nome único quando completar este passo. |
    | Subscrição | Selecione a sua subscrição do Azure na lista pendente. |
    | Grupo de recursos | **contoso-simulação.** Utilize o nome do seu acelerador de solução. |
    | Localização | Este exemplo utiliza **o Leste dos EUA.** Crie o seu ambiente na mesma região que o seu acelerador de simulação de dispositivo. |
    | Sku |**S1** |
    | Capacidade | **1** |

    ![Criar o Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > Adicionar o ambiente Time Series Insights ao mesmo grupo de recursos que o acelerador de solução significa que é eliminado quando elimina o acelerador de solução.

1. Clique em **Criar**. Pode levar alguns minutos para que o ambiente seja criado.

## <a name="create-event-source"></a>Crie a origem de eventos

Crie uma nova fonte de eventos para ligar ao seu hub IoT. Utilize o grupo de consumidores que criou nos passos anteriores. Uma fonte de eventos time Series Insights requer um grupo de consumidores dedicado que não seja utilizado por outro serviço.

1. No portal Azure, navegue para o seu novo Ambiente série de tempo.

1. À esquerda, clique em **Fontes de Evento:**

    ![Ver Fontes de Evento](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. Clique **em Adicionar:**

    ![Adicionar Fonte de Evento](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. Para configurar o seu hub IoT como uma nova fonte de eventos, utilize os valores na tabela seguinte:

    | Definição | Valor |
    | ------- | ----- |
    | Nome da fonte do evento | A imagem que se segue utiliza o nome **contoso-iot-hub**. Use o seu próprio nome único quando completar este passo. |
    | Origem | **Hub IoT** |
    | Opção de Importar | **Use o IoT Hub a partir de subscrições disponíveis** |
    | ID da subscrição | Selecione a sua subscrição do Azure na lista pendente. |
    | Nome do hub IoT | **contoso-simulação7d894**. Utilize o nome do seu hub IoT a partir do acelerador de solução de simulação do dispositivo. |
    | Nome da política do hub IoT | **iothubowner** |
    | Chave política do hub Iot | Este campo é povoado automaticamente. |
    | Grupo de consumidores do hut IoT | **dispositivosimulação** |
    | Formato de serialização de eventos | **JSON** |
    | Nome da propriedade Carimbo de data/hora | Deixar em branco |

    ![Criar Fonte de Eventos](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. Clique em **Criar**.

> [!NOTE]
> Pode [conceder aos utilizadores acesso adicional](../time-series-insights/concepts-access-policies.md#grant-data-access) ao explorador time series Insights.

## <a name="start-a-simulation"></a>Iniciar uma simulação

Antes de utilizar o explorador Time Series Insights, configuure o acelerador de solução de simulação do dispositivo para gerar alguma telemetria. A imagem a seguir mostra uma simulação em execução com 10 dispositivos de refrigeração:

![Simulação de dispositivo de execução](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Explorador do Time Series Insights

O explorador time series Insights é uma aplicação web que pode usar para visualizar a sua telemetria.

1. No portal Azure, selecione o separador Time Series Insights **Overview.**

1. Para abrir a aplicação do explorador time series Insights, clique em **Ir ao Ambiente:**

    ![Explorador do Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. No painel de seleção de tempo, selecione **Last 30 minutes** do menu de tempos rápidos e clique em **Procurar**:

    ![Pesquisa de explorador de Séries De Tempo Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. No painel de termos à esquerda, selecione a **temperatura** como **medida** e **id de ligação iothub** como o **split por** valor:

    ![Screenshot que mostra o painel "termos" da Série De Tempo Insights, com os valores "Measure" e "Split by" em destaque.](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Clique com o botão direito na tabela e **selecione Explore eventos:**

    ![Eventos de exploradores de Séries De Tempo Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. Os dados do evento mostram numa grelha:

    ![Tabela de exploradores de Séries de Tempo Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Clique no botão visualização de perspetiva:

    ![Perspetiva do explorador de insights de séries de tempo](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Clique **+** para adicionar uma nova consulta à perspetiva:

    ![Explorador de insights de séries de tempo adicionar consulta](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Selecione **Last 30 minutes** as the time span, **Humidity** as the **Measure**, and **iothub-connection-device-id** as the **Split By** value:

    ![Consulta de explorador de insights de séries de tempo](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. Clique no botão de visualização de perspetiva para ver o painel de telemetria do dispositivo:

    ![Painel de explorador de insights de séries de tempo](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretende explorar mais, deixe o acelerador de solução implantado.

Se já não precisar do acelerador de solução, elimine-o da página [de soluções Provisionadas,](https://www.azureiotsolutions.com/Accelerators#dashboard) selecionando-a e clicando em **Eliminar Solution**.

Se adicionar o ambiente Time Series Insights ao grupo de recursos do acelerador de solução, este é automaticamente eliminado quando elimina o acelerador de solução. Caso contrário, deve remover manualmente o ambiente de Insights de Séries De Tempo do portal Azure.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como explorar e consultar dados no explorador de Insights da Série Temporal, consulte o [explorador Azure Time Series Insights](../time-series-insights/time-series-insights-explorer.md).