---
title: Alargar a Azure IoT Central com análise personalizada / Microsoft Docs
description: Como desenvolvedor de soluções, configure uma aplicação IoT Central para fazer análises e visualizações personalizadas. Esta solução utiliza os Bricks Azure.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 7c2c14a937b4ef55d0e5f71e7b20214428ecd68c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158202"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Estenda a Azure IoT Central com análise personalizada usando tijolos de dados Azure

Este guia mostra-lhe, como desenvolvedor de soluções, como alargar a sua aplicação IoT Central com análises e visualizações personalizadas. O exemplo usa um espaço de trabalho [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) para analisar o fluxo de telemetria IoT Central e para gerar visualizações como enredos de [caixas.](https://wikipedia.org/wiki/Box_plot)

Este guia de como fazer mostra como estender a IoT Central para além do que já pode fazer com as [ferramentas de análise incorporadas.](./howto-create-custom-analytics.md)

Neste guia de como orientar, aprende-se a:

* Telemetria de fluxo de uma aplicação IoT Central utilizando *a exportação contínua de dados*.
* Crie um ambiente Azure Databricks para analisar e traçar telemetria de dispositivos.

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos neste guia de como guiar, precisa de uma subscrição azure ativa.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

### <a name="iot-central-application"></a>Aplicação IoT Central

Criar uma aplicação IoT Central no site do gestor de [aplicações Azure IoT Central](https://aka.ms/iotcentral) com as seguintes definições:

| Definição | Valor |
| ------- | ----- |
| Plano de preços | Standard |
| Modelo de aplicação | Análise na loja – monitorização de condições |
| Nome da aplicação | Aceite o padrão ou escolha o seu próprio nome |
| do IdP | Aceite o padrão ou escolha o seu próprio prefixo URL único |
| Diretório | O seu inquilino de Diretório Ativo Azure |
| Subscrição do Azure | A sua subscrição do Azure |
| Região | A sua região mais próxima |

Os exemplos e imagens deste artigo utilizam a região **dos Estados Unidos.** Escolha um local próximo de si e certifique-se de criar todos os seus recursos na mesma região.

Este modelo de aplicação inclui dois dispositivos termóstatos simulados que enviam telemetria.

### <a name="resource-group"></a>Grupo de recursos

Utilize o [portal Azure para criar um grupo](https://portal.azure.com/#create/Microsoft.ResourceGroup) de recursos chamado **IoTCentralAnalysis** para conter os outros recursos que cria. Crie os seus recursos Azure no mesmo local que a sua aplicação IoT Central.

### <a name="event-hubs-namespace"></a>Espaço de nomes dos Event Hubs

Utilize o [portal Azure para criar um espaço](https://portal.azure.com/#create/Microsoft.EventHub) de nome dos Hubs de Eventos com as seguintes definições:

| Definição | Valor |
| ------- | ----- |
| Nome    | Escolha o seu nome de espaço de nome |
| Escalão de preço | Básico |
| Subscrição | A sua subscrição |
| Grupo de recursos | Análise Central ioT |
| Localização | E.U.A. Leste |
| Unidades de Débito | 1 |

### <a name="azure-databricks-workspace"></a>Espaço de trabalho Azure Databricks

Utilize o [portal Azure para criar um Serviço de Tijolos de Dados Azure](https://portal.azure.com/#create/Microsoft.Databricks) com as seguintes definições:

| Definição | Valor |
| ------- | ----- |
| Nome da área de trabalho    | Escolha o seu nome de espaço de trabalho |
| Subscrição | A sua subscrição |
| Grupo de recursos | Análise Central ioT |
| Localização | E.U.A. Leste |
| Escalão de Preço | Standard |

Quando criou os recursos necessários, o seu grupo de recursos **IoTCentralAnalysis** parece ser a seguinte imagem:

![Grupo de recursos de análise IoT Central](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>Criar um hub de eventos

Pode configurar uma aplicação IoT Central para exportar continuamente telemetria para um centro de eventos. Nesta secção, você cria um hub de eventos para receber telemetria da sua aplicação IoT Central. O centro de eventos entrega a telemetria ao seu trabalho de Stream Analytics para processamento.

1. No portal Azure, navegue para o espaço de nome do Seu Event Hubs e selecione **+ Event Hub**.
1. Nomeie o seu centro de eventos **centralexport,** e selecione **Create**.
1. Na lista de centros de eventos no seu espaço de nome, selecione **centralexport**. Em seguida, escolha políticas de **acesso partilhado**.
1. Selecione **+ Adicionar**. Crie uma política chamada **Ouvir** com **a** reivindicação ouvir.
1. Quando a política estiver pronta, selecione-a na lista e, em seguida, copie o **valor-chave primário da Ligação.**
1. Tome nota desta cadeia de ligação, use-a mais tarde quando configurar o seu caderno Databricks para ler a partir do centro do evento.

O espaço de nome do Seu Event Hubs parece a seguinte imagem:

![Espaço de nomes dos Event Hubs](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>Configure a exportação na IoT Central

No site do gestor de [aplicações Azure IoT Central,](https://aka.ms/iotcentral) navegue para a aplicação IoT Central que criou a partir do modelo Contoso. Nesta secção, configura a aplicação para transmitir a telemetria dos seus dispositivos simulados para o seu centro de eventos. Para configurar a exportação:

1. Navegue na página **de Exportação** de Dados, selecione **+ Novo,** e depois **Azure Event Hubs**.
1. Utilize as seguintes definições para configurar a exportação e, em seguida, **selecione Guardar:**

    | Definição | Valor |
    | ------- | ----- |
    | Nome a Apresentar | Exportação para Centros de Eventos |
    | Ativado | Ativado |
    | Espaço de nomes dos Event Hubs | Nome do nome do nome do seu evento Hubs |
    | Hub de eventos | centralexporte |
    | Medições | Ativado |
    | Dispositivos | Desativado |
    | Modelos de dispositivo | Desativado |

![Configuração de exportação de dados](media/howto-create-custom-analytics/cde-configuration.png)

Aguarde até que o estado de exportação **esteja em funcionamento** antes de continuar.

## <a name="configure-databricks-workspace"></a>Configure espaço de trabalho de Databricks

No portal Azure, navegue para o seu serviço Azure Databricks e selecione **Launch Workspace**. Um novo separador abre no seu navegador e assina-o no seu espaço de trabalho.

### <a name="create-a-cluster"></a>Criar um cluster

Na página **Azure Databricks,** sob a lista de tarefas comuns, selecione **New Cluster**.

Utilize as informações na tabela seguinte para criar o seu cluster:

| Definição | Valor |
| ------- | ----- |
| Nome do Cluster | centralanálise |
| Modo cluster | Standard |
| Versão de tempo de execução de databricks | 5.5 LTS (Scala 2.11, Faísca 2.4.3) |
| Versão Python | 3 |
| Ativar a autoscalcificação | Não |
| Terminar após minutos de inatividade | 30 |
| Tipo de trabalhador | Standard_DS3_v2 |
| Trabalhadores | 1 |
| Tipo de condutor | O mesmo que o trabalhador |

Criar um cluster pode demorar vários minutos, esperar que a criação do cluster termine antes de continuar.

### <a name="install-libraries"></a>Instalar bibliotecas

Na página **Clusters,** aguarde até que o estado de cluster esteja **em execução**.

Os seguintes passos mostram-lhe como importar a biblioteca que a sua amostra precisa para o cluster:

1. Na página **Clusters,** aguarde até que o estado do cluster interativo de **análise central** esteja **em execução**.

1. Selecione o cluster e, em seguida, escolha o separador **Bibliotecas.**

1. No separador **Bibliotecas,** escolha **Instalar Novo**.

1. Na página da **Biblioteca de Instalação,** escolha **Maven** como fonte da biblioteca.

1. Na caixa de texto **Coordenadas,** introduza o seguinte valor:`com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Escolha **instalar** para instalar a biblioteca no cluster.

1. O estado da biblioteca está agora **instalado:**

    ![Biblioteca instalada](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>Importar um caderno databricks

Utilize os seguintes passos para importar um caderno databricks que contenha o código Python para analisar e visualizar a sua telemetria Central IoT:

1. Navegue para a página **Workspace** no seu ambiente Databricks. Selecione a queda ao lado do nome da sua conta e, em seguida, escolha **Importar**.

1. Opte por importar a partir de um URL e insira o seguinte endereço:[https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Para importar o caderno, escolha **Importar**.

1. Selecione o **Espaço de Trabalho** para ver o caderno importado:

    ![Caderno importado](media/howto-create-custom-analytics/import-notebook.png)

1. Editar o código na primeira célula Python para adicionar a cadeia de ligação Event Hubs que guardou anteriormente:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Análise de execução

Para executar a análise, deve anexar o caderno ao cluster:

1. Selecione **Desvinculado** e, em seguida, selecione o cluster **centralde análise.**
1. Se o aglomerado não estiver a funcionar, começa.
1. Para ligar o caderno, selecione o botão de execução.

Pode ver um erro na última célula. Em caso afirmativo, verifique se as células anteriores estão em funcionamento, aguarde um minuto para que alguns dados sejam escritos para armazenamento e, em seguida, verifique novamente a última célula.

### <a name="view-smoothed-data"></a>Ver dados lisos

No caderno, desloque-se até à cela 14 para ver um enredo da humidade média rolante por tipo de dispositivo. Este enredo atualiza continuamente à medida que a telemetria de streaming chega:

![Enredo de telemetria suavizado](media/howto-create-custom-analytics/telemetry-plot.png)

Pode redimensionar a ficha no caderno.

### <a name="view-box-plots"></a>Ver enredos de caixa

No caderno, desloque-se até à cela 20 para ver os enredos da [caixa.](https://en.wikipedia.org/wiki/Box_plot) Os enredos da caixa baseiam-se em dados estáticos, de modo a atualizá-los, deve reproduzir a célula:

![Lotes de caixa](media/howto-create-custom-analytics/box-plots.png)

Podereres redimensionar os enredos do caderno.

## <a name="tidy-up"></a>Arrumação

Para arrumar depois deste como e evitar custos desnecessários, elimine o grupo de recursos **IoTCentralAnalysis** no portal Azure.

Pode eliminar a aplicação IoT Central da página **De Gestão** dentro da aplicação.

## <a name="next-steps"></a>Passos seguintes

Neste guia de como orientar, aprendeu a:

* Telemetria de fluxo de uma aplicação IoT Central utilizando *a exportação contínua de dados*.
* Crie um ambiente Azure Databricks para analisar e traçar dados de telemetria.

Agora que sabe como criar análises personalizadas, o próximo passo sugerido é aprender a gerir a [sua aplicação.](howto-administer.md)
