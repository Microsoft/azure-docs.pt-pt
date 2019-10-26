---
title: Criar um painel de triagem de dados de integridade com o Azure IoT Central | Microsoft Docs
description: Aprenda a criar um painel de triagem de dados de integridade usando os modelos de aplicativo IoT Central do Azure.
author: philmea
ms.author: philmea
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: c30f939aa353ae5809a8ba6e9c2b2d7f376ddc0d
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72956664"
---
# <a name="tutorial-build-a-power-bi-provider-dashboard"></a>Tutorial: criar um painel do provedor de Power BI

Ao criar sua solução de monitoramento de pacientes contínua, convém também criar um painel para uma equipe de atendimento ao hospital para visualizar dados de pacientes. Este tutorial orientará você pelas etapas para criar um Power BI painel de streaming em tempo real do seu IoT Central modelo de aplicativo de monitoramento de pacientes contínuo.

>[!div class="mx-imgBorder"]
>](media/dashboard-gif-3.gif) do ![do painel GIF

A arquitetura básica seguirá esta estrutura:

>[!div class="mx-imgBorder"] 
>Painel de triagem do provedor de ![](media/dashboard-architecture.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Exportar dados do Azure IoT Central para os hubs de eventos do Azure
> * Configurar um conjunto de Power BI de streaming
> * Conectar seu aplicativo lógico aos hubs de eventos do Azure
> * Transmitir dados para Power BI de seu aplicativo lógico
> * Crie um painel em tempo real para os vitais dos pacientes

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Um modelo de aplicativo de monitoramento contínuo do paciente do Azure IoT Central. Se você ainda não tiver um, poderá seguir as etapas para [implantar um modelo de aplicativo](overview-iot-central-healthcare.md).

* Um [namespace dos hubs de eventos do Azure e o Hub de eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

* O aplicativo lógico que você deseja acessar o Hub de eventos. Para iniciar seu aplicativo lógico com um gatilho de hubs de eventos do Azure, você precisa de um [aplicativo lógico em branco](https://docs.microsoft.com/azure/logic-apps/quickstart-create-first-logic-app-workflow).

* Uma conta de serviço do Power BI. Se você ainda não tiver um, poderá [criar uma conta de avaliação gratuita para serviço do Power bi](https://app.powerbi.com/). Se você ainda não usou Power BI antes, pode ser útil passar por introdução [ao Power bi](https://docs.microsoft.com/power-bi/service-get-started).

## <a name="set-up-a-continuous-data-export-to-azure-event-hubs"></a>Configurar uma exportação de dados contínua para os hubs de eventos do Azure
Primeiro, você precisará configurar uma exportação de dados contínua de seu modelo de aplicativo IoT Central do Azure para o Hub de eventos do Azure em sua assinatura. Você pode fazer isso seguindo as etapas neste tutorial de IoT Central do Azure para [exportar para os hubs de eventos](https://docs.microsoft.com/azure/iot-central/howto-export-data-event-hubs-service-bus-pnp). Você só precisará exportar para a telemetria para os fins deste tutorial.

## <a name="create-a-power-bi-streaming-dataset"></a>Criar um conjunto de Power BI de streaming

1. Entre em sua conta do Power BI.

2. Em seu espaço de trabalho preferido, crie um novo conjunto de fluxos de transmissão selecionando o botão **+ criar** no canto superior direito da barra de ferramentas. Você precisará criar um conjunto de um DataSet separado para cada paciente que você gostaria de ter em seu painel.

    >[!div class="mx-imgBorder"] 
    >![criar conjunto de](media/create-streaming-dataset.png) de fluxo de mídia

3. Escolha a **API** para a origem do conjunto de seus conjuntos de seus.

4. Insira um **nome** (por exemplo, o nome de um paciente) para seu conjunto de e, em seguida, preencha os valores de seu fluxo. Você pode ver um exemplo abaixo com base nos valores provenientes dos dispositivos simulados no modelo de aplicativo de monitoramento contínuo do paciente. O exemplo tem dois pacientes:

    * Teddy Silvers, que têm dados da chave do Smart joelho
    * Yesenia Sanford, que tem dados do patch do Smart vitals

    >[!div class="mx-imgBorder"] 
    >![inserir valores de conjunto de](media/enter-dataset-values.png)

Para saber mais sobre os conjuntos de informações de streaming no Power BI, você pode ler este documento em [streaming em tempo real em Power bi](https://docs.microsoft.com/power-bi/service-real-time-streaming).

## <a name="connect-your-logic-app-to-azure-event-hubs"></a>Conectar seu aplicativo lógico aos hubs de eventos do Azure
Para conectar seu aplicativo lógico aos hubs de eventos do Azure, você pode seguir as instruções descritas neste documento sobre como [enviar eventos com hubs de eventos do Azure e aplicativos lógicos do Azure](https://docs.microsoft.com/azure/connectors/connectors-create-api-azure-event-hubs#add-event-hubs-action). Aqui estão alguns parâmetros sugeridos:

|Parâmetro|Valor|
|---|---|
|Tipo de conteúdo|application/json|
|Intervalo|3|
|Frequência|Segundo|

No final desta etapa, o designer do aplicativo lógico deve ter esta aparência:

>[!div class="mx-imgBorder"] 
>![aplicativos lógicos se conectam aos hubs de eventos](media/eh-logic-app.png)

## <a name="stream-data-to-power-bi-from-your-logic-app"></a>Transmitir dados para Power BI de seu aplicativo lógico
A próxima etapa será analisar os dados provenientes do seu hub de eventos para transmiti-los para os conjuntos Power BI dados que você criou anteriormente.

1. Antes de fazer isso, você precisará entender a carga JSON que está sendo enviada do seu dispositivo para o Hub de eventos. Você pode fazer isso examinando este [esquema de exemplo](https://docs.microsoft.com/azure/iot-central/howto-export-data-event-hubs-service-bus-pnp#telemetry) e modificando-o para corresponder ao seu esquema ou usando o Gerenciador do [barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer) para inspecionar as mensagens. Se você estiver usando os aplicativos de monitoramento contínuo do paciente, suas mensagens terão a seguinte aparência:

**Telemetria de patches do Smart vitals**

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```

**Telemetria de chave joelho inteligente**

```json
{
  "Acceleration": {
    "x": 72.73510947763711,
    "y": 72.73510947763711,
    "z": 72.73510947763711
  },
  "RangeOfMotion": 123,
  "KneeBend": 3
}
```

**Properties**

```json
{
  "iothub-connection-device-id": "1qsi9p8t5l2",
  "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",  \"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
  "iothub-connection-auth-generation-id": "637063718586331040",
  "iothub-enqueuedtime": 1571681440990,
  "iothub-message-source": "Telemetry",
  "iothub-interface-name": "Patient_health_data_3bk",
  "x-opt-sequence-number": 7,
  "x-opt-offset": "3672",
  "x-opt-enqueued-time": 1571681441317
}
```

2. Agora que você inspecionou suas cargas JSON, volte para o designer do aplicativo lógico e selecione **+ nova etapa**. Pesquise e adicione a **variável Initialize** como a próxima etapa e insira os seguintes parâmetros:

    |Parâmetro|Valor|
    |---|---|
    |Nome|Nome da interface|
    |Tipo|String|

    Clique em **salvar**. 

3. Adicione outra variável chamada **Body** com o tipo como **cadeia de caracteres**. Seu aplicativo lógico terá essas ações adicionadas:

    >[!div class="mx-imgBorder"]
    >![inicializar variáveis](media/initialize-string-variables.png)
    
4. Selecione **+ nova etapa** e adicione uma ação **analisar JSON** . Renomeie-o para **analisar as propriedades**. Para o conteúdo, escolha **as propriedades** provenientes do hub de eventos. Selecione **usar conteúdo de exemplo para gerar o esquema** na parte inferior e cole o conteúdo de exemplo da seção Propriedades acima.

5. Em seguida, escolha a ação **definir variável** e atualize a variável **nome da interface** com o **iothub-interface-Name** das propriedades JSON analisadas.

6. Adicione um controle **Split** como sua próxima ação e escolha a variável **nome da interface** como o parâmetro on. Você usará isso para direcionar os dados para o conjunto de dado correto.

7. Em seu aplicativo de IoT Central do Azure, localize o nome da interface dos dados de integridade do patch dos vitais inteligentes e os dados de integridade da chave do Smart joelho do modo de exibição **modelos de dispositivo** . Crie dois casos diferentes para o controle de **alternância** para cada nome de interface e renomeie o controle adequadamente. Você pode definir o caso padrão para usar o controle **Terminate** e escolher qual status deseja mostrar.

    >[!div class="mx-imgBorder"] 
    >![controle de divisão](media/split-by-interface.png)

8. Para o caso de **patch dos vitals inteligentes** , adicione uma ação de **analisar JSON** . Para o conteúdo, escolha o **conteúdo** proveniente do hub de eventos. Copie e cole os conteúdos de exemplo para o patch vitals inteligentes acima para gerar o esquema.

9. Adicione uma ação **Set Variable** e atualize a variável **Body** com o **corpo** do JSON analisado na etapa 7.

10. Adicione um controle de **condição** como sua próxima ação e defina a condição como **corpo**, **Contains**, **frequência cardíaca**. Isso garantirá que você tenha o conjunto correto de dados provenientes do patch inteligente dos vitals antes de preencher o conjunto de Power BI DataSet. As etapas 7-9 terão a seguinte aparência:

    >[!div class="mx-imgBorder"] 
    >![Adicionar condição de vital inteligentes](media/smart-vitals-pbi.png)

11. Para o **verdadeiro** caso da condição, adicione uma ação que chame **adicionar linhas a um conjunto** de Power bi funcionalidade. Você precisará entrar Power BI para isso. O caso **falso** pode usar novamente o controle **Terminate** .

12. Escolha o **espaço de trabalho**, o **conjunto**de tabelas e a **tabela**apropriados. Mapeie os parâmetros que você especificou ao criar seu conjunto de fluxos de transmissão em Power BI aos valores JSON analisados que são provenientes do seu hub de eventos. Suas ações preenchidas devem ter a seguinte aparência:

    >[!div class="mx-imgBorder"] 
    >![adicionar linhas a Power BI](media/add-rows-yesenia.png)

13. Para o caso do comutador de **chaves do Smart joelho** , adicione uma ação de **analisar JSON** para analisar o conteúdo, semelhante à etapa 7. Em seguida, **adicione linhas a um conjunto de registros** para atualizar seu conjunto de Teddy Silvers no Power bi.

    >[!div class="mx-imgBorder"] 
    >![Adicionar condição de vital inteligentes](media/knee-brace-pbi.png)

14. Pressione **salvar** e, em seguida, execute seu aplicativo lógico.

## <a name="build-a-real-time-dashboard-for-patient-vitals"></a>Crie um painel em tempo real para os vitais dos pacientes
Agora, volte para Power BI e selecione **+ criar** para criar um novo **painel**. Dê um nome ao seu painel e clique em **criar**.

Selecione os três pontos na barra de navegação superior e, em seguida, selecione **+ Adicionar bloco**.

>[!div class="mx-imgBorder"] 
>![Adicionar bloco ao painel](media/add-tile.png)

Escolha o tipo de bloco que você deseja adicionar e personalize seu aplicativo, no entanto, você gostaria de.

## <a name="clean-up-resources"></a>Limpar recursos

Se você não pretende usar este aplicativo, exclua seus recursos com as seguintes etapas:

1. No portal do Azure, você pode excluir os recursos do hub de eventos e dos aplicativos lógicos que você criou.

2. Para seu aplicativo IoT Central, vá para a guia Administração e selecione **excluir**.

## <a name="next-steps"></a>Passos seguintes

* Examine as [diretrizes de arquitetura de monitoramento de pacientes contínuas](concept-continuous-patient-monitoring-architecture.md).
