---
title: Visualizar a telemetria de simulação de dispositivo com o Time Series Insights - Azure | Documentos da Microsoft
description: Saiba como configurar o ambiente de Time Series Insights, explorar e analisar a telemetria gerada pelo solution accelerator a simulação do dispositivo.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 5d20adc11e0d679e12fd060e719593a50180db8e
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65834918"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Utilizar o Time Series Insights para ver a telemetria enviada a partir do solution accelerator de simulação do dispositivo

O acelerador de solução de simulação de dispositivo permite-lhe gerar a telemetria de dispositivos simulados para testar as suas soluções de IoT. Este guia de procedimentos mostra como visualizar e analisar a telemetria simulada usando um ambiente do Time Series Insights.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos neste guia de procedimentos, precisa de uma subscrição do Azure Active Directory. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Os passos neste guia de procedimentos partem do princípio de que implementar o acelerador de solução de simulação do dispositivo para a sua subscrição do Azure. Se ainda não implementou o solution accelerator, siga os passos a [implementar e executar uma solução de simulação de dispositivo com base na cloud](quickstart-device-simulation-deploy.md) início rápido.

Este artigo pressupõe que é o nome do seu solution accelerator **contoso-simulação**. Substitua **contoso-simulação** com o nome do seu solution accelerator à medida que conclua os seguintes passos.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>Criar um grupo de consumidores

Terá de criar um grupo de consumidores dedicado no seu hub IoT para a telemetria de fluxo para o Time Series Insights. Uma origem de evento no Time Series Insights deve ter a utilização exclusiva de um grupo de consumidores do IoT Hub.

Os passos seguintes utilizam a CLI do Azure no Azure Cloud Shell para criar o grupo de consumidores:

1. O hub IoT é um dos vários recursos que criou quando implementou o acelerador de solução de simulação do dispositivo. Executar a encontrar o seguinte comando o nome do IoT hub - não se esqueça de utilizar o nome do seu acelerador de solução:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    O hub IoT é o tipo de recurso **iothubs**.

1. Adicionar um grupo de consumidores chamado **devicesimulationtsi** para o hub. No comando seguinte utiliza o nome do seu acelerador de solução e hub:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    Agora pode fechar o Azure Cloud Shell.

## <a name="create-a-new-time-series-insights-environment"></a>Criar um novo ambiente do Time Series Insights

[O Azure Time Series Insights](../../articles/time-series-insights/time-series-insights-overview.md) é um serviço de análise, armazenamento e visualização totalmente gerido para o gerenciamento de dados de séries de tempo de escala de IoT na cloud. Para criar um novo ambiente do Time Series Insights:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione **criar um recurso** > **Internet das coisas** > **Time Series Insights**:

    ![O Time Series Insights novo](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. Para criar o seu ambiente do Time Series Insights no mesmo grupo de recursos do solution accelerator, utilize os valores na tabela a seguir:

    | Definição | Value |
    | ------- | ----- |
    | Nome do ambiente | Captura de ecrã seguinte utiliza o nome **Contoso-TSI**. Escolha o seu próprio nome exclusivo quando concluir este passo. |
    | Subscrição | Selecione a sua subscrição do Azure na lista pendente. |
    | Grupo de recursos | **simulação de contoso**. Utilize o nome do seu solution accelerator. |
    | Location | Este exemplo utiliza **E.U.A. Leste**. Crie o seu ambiente na mesma região que o seu acelerador de simulação do dispositivo. |
    | SKU |**S1** |
    | Capacidade | **1** |

    ![Criar o Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > Adicionar o Time Series Insights o ambiente para o mesmo grupo de recursos como o solution accelerator significa que este é eliminado quando elimina o solution accelerator.

1. Clique em **Criar**. Pode demorar alguns minutos para o ambiente ser criado.

## <a name="create-event-source"></a>Crie a origem de eventos

Crie uma nova origem de evento para ligar ao seu hub IoT. Utilize o grupo de consumidores que criou nos passos anteriores. Uma fonte de eventos do Time Series Insights requer um grupo de consumidores dedicado não está em utilização por outro serviço.

1. No portal do Azure, navegue para o seu novo ambiente de Time Series.

1. No lado esquerdo, clique em **origens de eventos**:

    ![Visualizar origens de eventos](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. Clique em **adicionar**:

    ![Adicionar origem de eventos](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. Para configurar o seu hub IoT como uma nova origem de evento, utilize os valores na tabela a seguir:

    | Definição | Value |
    | ------- | ----- |
    | Nome da origem de evento | Captura de ecrã seguinte utiliza o nome **contoso-iot-hub**. Utilize o seu próprio nome exclusivo quando concluir este passo. |
    | Source | **Hub IoT** |
    | Importar opção | **Utilize o IoT Hub a partir de subscrições disponíveis** |
    | ID da subscrição | Selecione a sua subscrição do Azure na lista pendente. |
    | Nome do hub IoT | **Contoso-simulation7d894**. Utilize o nome do hub IoT partir do solution accelerator de simulação do dispositivo. |
    | nome de política do hub IoT | **iothubowner** |
    | chave de política do hub IoT | Este campo é preenchido automaticamente. |
    | Grupo de consumidores do hub IoT | **devicesimulationtsi** |
    | Formato de serialização de eventos | **JSON** |
    | Nome da propriedade de carimbo de data/hora | Deixar em branco |

    ![Criar origem de evento](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. Clique em **Criar**.

> [!NOTE]
> Pode [conceder acesso a utilizadores adicionais](../../articles/time-series-insights/time-series-insights-data-access.md#grant-data-access) para o Explorador do Time Series Insights.

## <a name="start-a-simulation"></a>Iniciar uma simulação

Antes de utilizar o Explorador do Time Series Insights, configure o acelerador de solução de simulação do dispositivo para gerar alguma telemetria. Captura de ecrã seguinte mostra uma simulação em execução com 10 dispositivos chiller:

![Execução de simulação do dispositivo](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Explorador do Time Series Insights

O Explorador do Time Series Insights é uma aplicação web que pode utilizar para visualizar a sua telemetria.

1. No portal do Azure, selecione o Time Series Insights **descrição geral** separador.

1. Para abrir a aplicação de web de Explorador do Time Series Insights, clique em **vá para o ambiente**:

    ![Explorador do Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. No painel de seleção de tempo, selecione **últimos 30 minutos** partir rápido vezes menu e clique em **pesquisa**:

    ![Pesquisa de explorer do tempo Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. No painel de termos no lado esquerdo, selecione **temperatura** como o **medida** e **iothub-ligação-dispositivo-id** como o **dividido por** valor:

    ![Consulta de Explorador do Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Faça duplo clique no gráfico e selecione **explorar eventos**:

    ![Eventos de Explorador do Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. Mostram os dados do evento numa grade:

    ![Tabela de Explorador do Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Clique no botão de vista do ponto de vista:

    ![O ponto de vista do Time Series Insights explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Clique em **+** para adicionar uma nova consulta para o ponto de vista:

    ![Explorador do Time Series Insights adicionar consulta](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Selecione **últimos 30 minutos** como o intervalo de tempo **humidade** como o **medida**, e **iothub-ligação-dispositivo-id** como o **Dividido por** valor:

    ![Consulta de Explorador do Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. Clique no botão de vista do ponto de vista para ver o painel de telemetria do dispositivo:

    ![Dashboard de Explorador do Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser explorar ainda mais, deixe o acelerador de solução implementado.

Se já não precisar do solution accelerator, elimine-o a partir da [aprovisionado de soluções](https://www.azureiotsolutions.com/Accelerators#dashboard) página, selecioná-la e, em seguida, clicando em **Eliminar solução**.

Se tiver adicionado o ambiente do Time Series Insights para o grupo de recursos do solution accelerator, é eliminado automaticamente quando eliminar o solution accelerator. Caso contrário, tem de remover manualmente o ambiente do Time Series Insights no portal do Azure.

## <a name="next-steps"></a>Próximos Passos

Para saber mais sobre como explorar e consultar dados no Explorador do Time Series Insights, veja [Explorador do Azure Time Series Insights](../time-series-insights/time-series-insights-explorer.md).
