---
title: Estenda a Azure IoT Central com | de análise personalizada Microsoft Docs
description: Como desenvolvedor de soluções, configurar uma aplicação IoT Central para fazer análises e visualizações personalizadas. Esta solução utiliza a Azure Databricks.
author: TheRealJasonAndrew
ms.author: v-anjaso
ms.date: 03/15/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 3132ec8fb3cb123653887d92a2f33788f40564c0
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105033828"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Estenda a Azure IoT Central com análises personalizadas usando Azure Databricks

Este guia de como fazer mostra-lhe, como um desenvolvedor de soluções, como estender a sua aplicação IoT Central com análises e visualizações personalizadas. O exemplo utiliza um espaço de trabalho [Azure Databricks](/azure/azure-databricks/) para analisar o fluxo de telemetria IoT Central e gerar visualizações como [parcelas de caixa.](https://wikipedia.org/wiki/Box_plot)  

Este guia de como fazer mostra-lhe como estender a IoT Central para além do que já pode fazer com as [ferramentas de análise incorporadas.](./howto-create-custom-analytics.md)

Neste guia de como guiar, aprende-se a:

* Telemetria de fluxo a partir de uma aplicação IoT Central utilizando *a exportação contínua de dados*.
* Crie um ambiente Azure Databricks para analisar e traçar telemetria de dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos neste guia de como fazer, precisa de uma subscrição ativa do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

### <a name="iot-central-application"></a>Aplicação IoT Central

Crie uma aplicação IoT Central no site do [gestor de aplicações Azure IoT Central](https://aka.ms/iotcentral) com as seguintes definições:

| Definição | Valor |
| ------- | ----- |
| Plano de preços | Standard |
| Modelo de aplicação | Análise na loja – monitorização de condições |
| Nome da aplicação | Aceite o padrão ou escolha o seu próprio nome |
| URL | Aceite o prefixo padrão ou escolha o seu próprio prefixo URL único |
| Diretório | Seu inquilino do Diretório Ativo Azure |
| Subscrição do Azure | A sua subscrição do Azure |
| Region | A sua região mais próxima |

Os exemplos e imagens deste artigo utilizam a região **dos Estados Unidos.** Escolha um local próximo de si e certifique-se de criar todos os seus recursos na mesma região.

Este modelo de aplicação inclui dois dispositivos de termóstato simulado que enviam telemetria.

### <a name="resource-group"></a>Grupo de recursos

Utilize o [portal Azure para criar um grupo de recursos](https://portal.azure.com/#create/Microsoft.ResourceGroup) chamado **IoTCentralAnalysis** para conter os outros recursos que cria. Crie os seus recursos Azure no mesmo local que a sua aplicação IoT Central.

### <a name="event-hubs-namespace"></a>Espaço de nomes dos Event Hubs

Utilize o [portal Azure para criar um espaço de nomes de Centros de Eventos](https://portal.azure.com/#create/Microsoft.EventHub) com as seguintes definições:

| Definição | Valor |
| ------- | ----- |
| Nome    | Escolha o seu nome de espaço de nome |
| Escalão de preço | Básico |
| Subscrição | A sua subscrição |
| Grupo de recursos | IoTCentralAnalysis |
| Localização | E.U.A Leste |
| Unidades de Débito | 1 |

### <a name="azure-databricks-workspace"></a>Espaço de trabalho Azure Databricks

Utilize o [portal Azure para criar um Serviço de Dados Azure](https://portal.azure.com/#create/Microsoft.Databricks) com as seguintes definições:

| Definição | Valor |
| ------- | ----- |
| Nome da área de trabalho    | Escolha o nome do seu espaço de trabalho |
| Subscrição | A sua subscrição |
| Grupo de recursos | IoTCentralAnalysis |
| Localização | E.U.A Leste |
| Escalão de Preço | Standard |

Quando cria os recursos necessários, o seu grupo de recursos **IoTCentralAnalysis** parece a seguinte imagem:

:::image type="content" source="media/howto-create-custom-analytics/resource-group.png" alt-text="imagem do grupo de recursos de análise IoT Central.":::

## <a name="create-an-event-hub"></a>Criar um hub de eventos

Você pode configurar uma aplicação IoT Central para exportar continuamente telemetria para um centro de eventos. Nesta secção, você cria um centro de eventos para receber telemetria a partir da sua aplicação IoT Central. O centro de eventos entrega a telemetria ao seu trabalho stream Analytics para processamento.

1. No portal Azure, navegue para o seu espaço de nomes De Event Hubs e selecione **+ Centro de Eventos.**
1. Nomeie o seu centro **de eventos centralexport.**
1. Na lista de centros de eventos no seu espaço de nome, selecione **centralexport**. Em seguida, escolha **políticas de acesso compartilhado.**
1. Selecione **+ Adicionar**. Crie uma política chamada **SendListen** com as reivindicações **enviar** e **ouvir.**
1. Quando a apólice estiver pronta, selecione-a na lista e, em seguida, copie o valor **da chave primária de ligação.**
1. Tome nota desta cadeia de ligação, use-a mais tarde quando configurar o seu caderno Databricks para ler a partir do centro de eventos.

O seu espaço de nomes do Event Hubs parece a seguinte imagem:

:::image type="content" source="media/howto-create-custom-analytics/event-hubs-namespace.png" alt-text="imagem do espaço de nomes de Event Hubs.":::

## <a name="configure-export-in-iot-central"></a>Configure a exportação na IoT Central

Nesta secção, configura a aplicação para transmitir telemetria dos seus dispositivos simulados para o seu centro de eventos.

No site do [gestor de aplicações Azure IoT Central,](https://aka.ms/iotcentral) navegue para a aplicação IoT Central que criou anteriormente. Para configurar a exportação, primeiro crie um destino:

1. Navegue para a página **de exportação de dados** e, em seguida, selecione **Destinos**.
1. Selecione **+ Novo destino.**
1. Utilize os valores na tabela seguinte para criar um destino:

    | Definição | Valor |
    | ----- | ----- |
    | Nome do destino | Centro de eventos de telemetria |
    | Tipo de destino | Azure Event Hubs |
    | Cadeia de ligação | A cadeia de conexão do centro de eventos que fez uma nota de anteriormente |

    O **Event Hub** mostra como **centralexport.**

    :::image type="content" source="media/howto-create-custom-analytics/data-export-1.png" alt-text="Screenshot mostrando destino de exportação de dados.":::

1. Selecione **Guardar**.

Para criar a definição de exportação:

1. Navegue na página **de exportação de dados** e selecione **+ Nova Exportação.**

1. Utilize os valores no quadro a seguir para configurar a exportação:

    | Definição | Valor |
    | ------- | ----- |
    | Nome de exportação | Exportação de Hub de Eventos |
    | Ativado | Ativado |
    | Tipo de dados para exportar | Telemetria |
    | Destinos | Selecione **+ Destino,** em seguida, selecione **o centro de eventos de Telemetria** |

1. Selecione **Guardar**.

    :::image type="content" source="media/howto-create-custom-analytics/data-export-2.png" alt-text="Screenshot mostrando definição de exportação de dados.":::

Aguarde até que o estado de exportação seja **saudável** na página **de exportação de dados** antes de continuar.

## <a name="configure-databricks-workspace"></a>Configurar o espaço de trabalho databricks

No portal Azure, navegue para o seu serviço Azure Databricks e selecione **Launch Workspace**. Um novo separador abre no seu navegador e inscreve-o no seu espaço de trabalho.

### <a name="create-a-cluster"></a>Criar um cluster

Na página **Azure Databricks,** na lista de tarefas comuns, selecione **New Cluster**.

Utilize as informações na tabela seguinte para criar o seu cluster:

| Definição | Valor |
| ------- | ----- |
| Nome do Cluster | centralmente |
| Modo cluster | Standard |
| Versão de runtime databricks | 5.5 LTS (Scala 2.11, Faísca 2.4.5) |
| Versão Python | 3 |
| Ativar a autoscalagem | No |
| Terminar após minutos de inatividade | 30 |
| Tipo de trabalhador | Standard_DS3_v2 |
| Trabalhadores | 1 |
| Tipo de condutor | O mesmo que trabalhador |

Criar um cluster pode demorar vários minutos, esperar que a criação do cluster esteja concluída antes de continuar.

### <a name="install-libraries"></a>Instalar bibliotecas

Na página **Clusters,** aguarde até que o estado do cluster **esteja a funcionar.**

Os seguintes passos mostram-lhe como importar a biblioteca que a sua amostra necessita para o cluster:

1. Na página **clusters,** aguarde até que o estado do cluster interativo **de centralanalysis** esteja **funcionando.**

1. Selecione o cluster e, em seguida, escolha o separador **Bibliotecas.**

1. No separador **Bibliotecas,** escolha **Instalar Novo.**

1. Na página **'Instalar Biblioteca',** escolha **Maven** como fonte da biblioteca.

1. Na caixa de texto **coordenadas,** introduza o seguinte valor: `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Escolha **instalar** para instalar a biblioteca no cluster.

1. O estado da biblioteca está agora **instalado:**

:::image type="content" source="media/howto-create-custom-analytics/cluster-libraries.png" alt-text="Screenshot da Biblioteca instalada.":::

### <a name="import-a-databricks-notebook"></a>Importar um caderno Databricks

Utilize os seguintes passos para importar um caderno Databricks que contenha o código Python para analisar e visualizar a sua telemetria IoT Central:

1. Navegue para a página **Workspace** no seu ambiente Databricks. Selecione o dropdown ao lado do nome da sua conta e, em seguida, escolha **Importar**.

1. Opte por importar a partir de um URL e introduza o seguinte endereço: [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Para importar o caderno, escolha **Import.**

1. Selecione o **Espaço de Trabalho** para ver o caderno importado:

:::image type="content" source="media/howto-create-custom-analytics/import-notebook.png" alt-text="Screenshot do caderno importado.":::

5. Edite o código na primeira célula Python para adicionar a cadeia de ligação 'Event Hubs' que guardou anteriormente:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Análise de execução

Para fazer a análise, deve anexar o caderno ao cluster:

1. **Selecione Detached** e, em seguida, selecione o cluster **de análise central.**
1. Se o aglomerado não estiver a funcionar, começa-o.
1. Para iniciar o portátil, selecione o botão de funcionação.

Pode ver um erro na última célula. Em caso afirmativo, verifique se as células anteriores estão em funcionamento, aguarde um minuto para que alguns dados sejam escritos para armazenamento e, em seguida, executar a última célula novamente.

### <a name="view-smoothed-data"></a>Ver dados suavizados

No caderno, desça até à cela 14 para ver um enredo da humidade média de rolamento por tipo de dispositivo. Este enredo atualiza continuamente à medida que a telemetria de streaming chega:

:::image type="content" source="media/howto-create-custom-analytics/telemetry-plot.png" alt-text="Screenshot do enredo de telemetria suavizado.":::

Pode redimensionar o gráfico no caderno.

### <a name="view-box-plots"></a>Ver parcelas de caixa

No caderno, desça até à cela 20 para ver os enredos da [caixa.](https://en.wikipedia.org/wiki/Box_plot) Os enredos da caixa são baseados em dados estáticos, por isso, para os atualizar, tem de voltar a atualizar a célula:

:::image type="content" source="media/howto-create-custom-analytics/box-plots.png" alt-text="Screenshot de enredos de caixa.":::

Pode redimensionar os terrenos do caderno.

## <a name="tidy-up"></a>Arrumar tudo

Para arrumar as coisas depois deste modo e evitar custos desnecessários, elimine o grupo de recursos **IoTCentralAnalysis** no portal Azure.

Pode eliminar a aplicação IoT Central da página **Gestão** dentro da aplicação.

## <a name="next-steps"></a>Passos seguintes

Neste guia de como guiar, aprendeu a:

* Telemetria de fluxo a partir de uma aplicação IoT Central utilizando *a exportação contínua de dados*.
* Crie um ambiente Azure Databricks para analisar e traçar dados de telemetria.

Agora que sabe como criar análises personalizadas, o próximo passo sugerido é aprender a [visualizar e analisar os seus dados Azure IoT Central num dashboard power BI](howto-connect-powerbi.md).
