---
title: Expandir o Azure IoT Central com a análise personalizada | Documentos da Microsoft
description: Como um desenvolvedor de soluções, configure uma aplicação de centro de IoT para fazer análises personalizados e visualizações. Esta solução utiliza o Azure Databricks.
author: dominicbetts
ms.author: dobett
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: e039e2b8d9c183b5bfee1bee47e4addc4c873bf7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66743444"
---
# <a name="extend-azure-iot-central-with-custom-analytics"></a>Expandir o Azure IoT Central com a análise personalizada

Este guia de procedimentos mostra-lhe, como um desenvolvedor de soluções, como expandir a sua aplicação do Centro de IoT com análises personalizados e visualizações. O exemplo utiliza um [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) área de trabalho para analisar o fluxo de telemetria de IoT Central e para gerar visualizações, tal como [caixa gráficos](https://wikipedia.org/wiki/Box_plot).

Este guia de procedimentos mostra como estender o Centro de IoT para além de que ele pode já fazer com o [ferramentas de análise incorporadas](howto-create-analytics.md).

Este guia de procedimentos, ficará a saber como:

* Stream telemetria a partir de uma aplicação do Centro de IoT com *exportação de dados contínua*.
* Crie um ambiente do Azure Databricks para analisar e representar a telemetria do dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste guia de procedimentos, precisa de uma subscrição do Azure Active Directory.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

### <a name="iot-central-application"></a>Aplicação IoT Central

Criar uma aplicação do Centro de IoT desde o [do Azure IoT Central - meus aplicativos](https://aka.ms/iotcentral) página com as seguintes definições:

| Definição | Value |
| ------- | ----- |
| Plano de pagamento | "Pay As You Go" |
| Modelo de aplicação | Exemplo da Contoso |
| Nome da aplicação | Aceite a predefinição ou escolha seu próprio nome de |
| do IdP | Aceite a predefinição ou escolha seu próprio prefixo de URL exclusivo |
| Diretório | Inquilino do Azure Active Directory |
| Subscrição do Azure | A sua subscrição do Azure |
| Região | EUA Leste |

Os exemplos e capturas de ecrã neste artigo utilizam o **E.U.A. Leste** região. Escolha uma localização perto de si e certifique-se de que criar todos os seus recursos na mesma região.

### <a name="resource-group"></a>Grupo de recursos

Utilize o [portal do Azure para criar um grupo de recursos](https://portal.azure.com/#create/Microsoft.ResourceGroup) chamado **IoTCentralAnalysis** para conter os outros recursos que criou. Crie os seus recursos do Azure na mesma localização que a aplicação do Centro de IoT.

### <a name="event-hubs-namespace"></a>Espaço de nomes dos Event Hubs

Utilize o [portal do Azure para criar um espaço de nomes de Hubs de eventos](https://portal.azure.com/#create/Microsoft.EventHub) com as seguintes definições:

| Definição | Valor |
| ------- | ----- |
| Name    | Escolha o seu espaço de nomes |
| Escalão de preço | Básica |
| Subscrição | A sua subscrição |
| Grupo de recursos | IoTCentralAnalysis |
| Location | EUA Leste |
| Unidades de Débito | 1 |

### <a name="azure-databricks-workspace"></a>Área de trabalho do Azure Databricks

Utilize o [portal do Azure para criar um serviço do Azure Databricks](https://portal.azure.com/#create/Microsoft.Databricks) com as seguintes definições:

| Definição | Value |
| ------- | ----- |
| Nome de área de trabalho    | Escolha o nome da sua área de trabalho |
| Subscrição | A sua subscrição |
| Grupo de recursos | IoTCentralAnalysis |
| Location | EUA Leste |
| Escalão de Preço | Standard |

Depois de criar os recursos necessários, sua **IoTCentralAnalysis** grupo de recursos é semelhante a captura de ecrã seguinte:

![Grupo de recursos de análise de IoT Central](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>Criar um hub de eventos

Pode configurar uma aplicação IoT Central continuamente exportar telemetria para um hub de eventos. Nesta secção, vai criar um hub de eventos a receber dados telemétricos a partir da sua aplicação IoT Central. O hub de eventos fornece a telemetria para a sua tarefa do Stream Analytics para processamento.

1. No portal do Azure, navegue para o espaço de nomes de Hubs de eventos e selecione **+ Hub de eventos**.
1. Nome do seu hub de eventos **centralexport**e selecione **criar**.
1. Na lista de hubs de eventos no espaço de nomes, selecione **centralexport**. Em seguida, escolha **políticas de acesso partilhado**.
1. Selecione **+ Adicionar**. Criar uma política com o nome **escutar** com o **escutar** de afirmação.
1. Quando a política estiver pronta, selecione-o na lista e, em seguida, copie os **ligação chave primária da cadeia de caracteres** valor.
1. Tome nota desta cadeia de ligação, usá-lo mais tarde quando configurar o seu bloco de notas do Databricks para ler a partir do hub de eventos.

O espaço de nomes de Hubs de eventos é semelhante a captura de ecrã seguinte:

![Espaço de nomes dos Event Hubs](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>Configurar a exportação no Centro de IoT

Navegue para o [aplicação IoT Central](https://aka.ms/iotcentral) criada a partir do modelo de Contoso. Nesta secção, irá configurar a aplicação para transmitir a telemetria dos seus dispositivos simulados para o event hub. Para configurar a exportação:

1. Navegue para o **a exportação contínua de dados** página, selecione **+ novo**e, em seguida **Hubs de eventos**.
1. Utilize as seguintes definições para configurar a exportação, em seguida, selecione **guardar**:

    | Definição | Value |
    | ------- | ----- |
    | Nome a apresentar | Exportar para os Hubs de eventos |
    | Enabled | Ativado |
    | Espaço de nomes dos Event Hubs | O nome do espaço de nomes de Hubs de eventos |
    | Hub de eventos | centralexport |
    | Medições | Ativado |
    | Dispositivos | Desativado |
    | Modelos de dispositivos | Desativado |

![Configuração da exportação contínua de dados](media/howto-create-custom-analytics/cde-configuration.png)

Aguarde até que seja o estado de exportação **em execução** antes de continuar.

## <a name="configure-databricks-workspace"></a>Configurar área de trabalho do Databricks

No portal do Azure, navegue para o serviço do Azure Databricks e selecione **iniciar área de trabalho**. Uma nova guia é aberta no seu browser e iniciada para a área de trabalho.

### <a name="create-a-cluster"></a>Criar um cluster

Sobre o **Azure Databricks** página, em que a lista de tarefas comuns, selecionadas **Novo Cluster**.

Utilize as informações na tabela seguinte para criar o cluster:

| Definição | Valor |
| ------- | ----- |
| Nome do Cluster | centralanalysis |
| Modo de cluster | Standard |
| Versão de Runtime do Databricks | 5.3 (Scala 2.11, Spark 2.4.0) |
| Versão do Python | 3 |
| Ativar o Dimensionamento Automático | Não |
| Terminar depois de minutos de inatividade | 30 |
| Tipo de função de trabalho | Standard_DS3_v2 |
| Funções de trabalho | 1 |
| Tipo de controlador | Mesmo que o trabalhador |

Criar um cluster pode demorar vários minutos, aguarde pela criação do cluster concluir antes de continuar.

### <a name="install-libraries"></a>Instalar bibliotecas

Sobre o **Clusters** página, aguarde até que o estado do cluster seja **em execução**.

Os passos seguintes mostram como importar a biblioteca tem seu exemplo para o cluster:

1. Na **Clusters** página, aguardar até o estado do **centralanalysis** cluster interativo é **executar**.

1. Selecione o cluster e, em seguida, escolha o **bibliotecas** separador.

1. Sobre o **bibliotecas** separador, escolha **instalar novo**.

1. Sobre o **instalar biblioteca** página, selecione **Maven** como a fonte da biblioteca.

1. Na **coordena** caixa de texto, introduza o seguinte valor: `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Escolher **instalar** para instalar a biblioteca no cluster.

1. O estado da biblioteca é agora **instalada**:

    ![Biblioteca instalada](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>Importar um bloco de notas do Databricks

Utilize os seguintes passos para importar um bloco de notas do Databricks que contém o código de Python para analisar e visualizar a telemetria de IoT Central:

1. Navegue para o **área de trabalho** página no seu ambiente do Databricks. Selecione o menu pendente junto ao nome da sua conta e, em seguida, escolha **importação**.

1. Opte por importar a partir de uma URL e introduza o seguinte endereço: [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Para importar o bloco de notas, escolha **importar**.

1. Selecione o **área de trabalho** para ver o bloco de notas importado:

    ![Bloco de notas importado](media/howto-create-custom-analytics/import-notebook.png)

1. Edite o código na primeira célula do Python para adicionar a cadeia de ligação de Hubs de eventos que guardou anteriormente:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Executar a análise

Para executar a análise, terá de anexar o bloco de notas para o cluster:

1. Selecione **Detached** e, em seguida, selecione a **centralanalysis** cluster.
1. Se o cluster não está em execução, inicie-o.
1. Para iniciar o bloco de notas, selecione o botão de execução.

Poderá ver um erro na última célula. Se assim for, verifique as células anteriores estão em execução, espere um minuto para alguns dados de escrita para armazenamento e, em seguida, execute novamente a última célula.

### <a name="view-smoothed-data"></a>Ver dados suavizados

No bloco de notas, desloque para baixo para a célula 14 para ver um desenho da humidade média sem interrupção por tipo de dispositivo. Essa plotagem atualiza continuamente à medida que chega de transmissão em fluxo de telemetria:

![Suavizados plotagem de telemetria](media/howto-create-custom-analytics/telemetry-plot.png)

Pode redimensionar o gráfico no bloco de notas.

### <a name="view-box-plots"></a>Representa a caixa de vista

No bloco de notas, desloque para baixo para a célula 20 para ver os [caixa gráficos](https://en.wikipedia.org/wiki/Box_plot). Os gráficos de caixa baseiam-se em dados estáticos, para que atualizá-los, deve voltar a executar a célula:

![Representa a caixa](media/howto-create-custom-analytics/box-plots.png)

Pode redimensionar os gráficos no bloco de notas.

## <a name="tidy-up"></a>Arrumá

Para organizar após este procedimentos e evitar custos desnecessários, elimine o **IoTCentralAnalysis** grupo de recursos no portal do Azure.

Pode eliminar a aplicação do Centro de IoT do **gestão** página na aplicação.

## <a name="next-steps"></a>Passos Seguintes

Este guia de procedimentos, ficou a saber como:

* Stream telemetria a partir de uma aplicação do Centro de IoT com *exportação de dados contínua*.
* Crie um ambiente do Azure Databricks para analisar e dados de telemetria de desenho.

Agora que sabe como criar análises personalizados, a próxima etapa sugerida é saber como [Visualize e analise os dados do Azure IoT Central no dashboard do Power BI](howto-connect-powerbi.md).
