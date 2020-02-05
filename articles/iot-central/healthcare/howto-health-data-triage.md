---
title: Crie um painel de triagem de dados de saúde com a Central Azure IoT  Microsoft Docs
description: Aprenda a construir um painel de triagem de dados de saúde utilizando modelos de aplicação Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 99b27ec53d955079b5f73986408e698955c0969b
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77021649"
---
# <a name="tutorial-build-a-power-bi-provider-dashboard"></a>Tutorial: Construa um dashboard de fornecedor power BI



Ao construir a sua solução contínua de monitorização do paciente, também pode criar um painel de instrumentos para uma equipa de cuidados hospitalares visualizar os dados do paciente. Neste tutorial, você aprenderá a criar um painel de streaming power BI em tempo real a partir do seu modelo de aplicação de monitorização contínua do paciente IoT Central.

>[!div class="mx-imgBorder"]
>![dashboard GIF](media/dashboard-gif-3.gif)

A arquitetura básica seguirá esta estrutura:

>[!div class="mx-imgBorder"] 
>![Provedor de Triagem](media/dashboard-architecture.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Dados de exportação do Azure IoT Central para os Hubs de Eventos Azure
> * Configurar um conjunto de dados de streaming Power BI
> * Ligue a sua App Lógica aos Hubs de Eventos Azure
> * Transmita dados para Power BI a partir da sua Aplicação Lógica
> * Construa um dashboard em tempo real para os sinais vitais do paciente

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Um modelo de aplicação contínua de monitorização contínua do paciente Azure IoT Central. Se ainda não tiver um, pode seguir passos para implementar um modelo de [aplicação](overview-iot-central-healthcare.md).

* Um espaço de nome azure [Event Hubs e Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)de Eventos.

* A App Lógica que pretende aceder ao seu Hub de Eventos. Para iniciar a sua Aplicação Lógica com um gatilho de Hubs de Eventos Azure, você precisa de uma [App Lógica em branco](https://docs.microsoft.com/azure/logic-apps/quickstart-create-first-logic-app-workflow).

* Uma conta de serviço Power BI. Se ainda não tiver uma, pode [criar uma conta de teste gratuita para o serviço Power BI](https://app.powerbi.com/). Se nunca usou o Power BI antes, pode ser útil passar por [Get started with Power BI](https://docs.microsoft.com/power-bi/service-get-started).

## <a name="set-up-a-continuous-data-export-to-azure-event-hubs"></a>Criar uma exportação contínua de dados para o Azure Event Hubs
Em primeiro lugar, terá de configurar uma exportação contínua de dados do seu modelo de aplicação Azure IoT Central para o Azure Event Hub na sua subscrição. Pode fazê-lo seguindo os passos deste tutorial Central Azure IoT para exportação para centros de [eventos.](https://docs.microsoft.com/azure/iot-central/core/howto-export-data) Só terá de exportar para a telemetria para efeitos deste tutorial.

## <a name="create-a-power-bi-streaming-dataset"></a>Criar um conjunto de dados de streaming Power BI

1. Inscreva-se na sua conta Power BI.

2. No seu espaço de trabalho preferido, crie um novo conjunto de dados de streaming selecionando o botão **+ Criar** no canto superior direito da barra de ferramentas. Terá de criar um conjunto de dados separado para cada paciente que gostaria de ter no seu painel de instrumentos.

    >[!div class="mx-imgBorder"] 
    >![Criar](media/create-streaming-dataset.png) de conjunto de dados de streaming

3. Escolha **API** para a fonte do seu conjunto de dados.

4. Introduza um **nome** (por exemplo, o nome de um paciente) para o seu conjunto de dados e, em seguida, preencha os valores do seu fluxo. Pode ver um exemplo abaixo com base nos valores provenientes dos dispositivos simulados no modelo de aplicação de monitorização contínua do paciente. O exemplo tem dois pacientes:

    * Teddy Silvers, que tem dados da Smart Knee Brace
    * Yesenia Sanford, que tem dados do Smart Vitals Patch

    >[!div class="mx-imgBorder"] 
    >![Insira os valores do conjunto de dados](media/enter-dataset-values.png)

Para saber mais sobre o streaming de conjuntos de dados no Power BI, pode ler este documento em [streaming em tempo real no Power BI](https://docs.microsoft.com/power-bi/service-real-time-streaming).

## <a name="connect-your-logic-app-to-azure-event-hubs"></a>Ligue a sua App Lógica aos Hubs de Eventos Azure
Para ligar a sua Aplicação Lógica aos Hubs de Eventos Azure, pode seguir as instruções descritas neste documento sobre o envio de [eventos com Hubs de Eventos Azure e Aplicações Lógicas Azure](https://docs.microsoft.com/azure/connectors/connectors-create-api-azure-event-hubs#add-event-hubs-action). Aqui estão alguns parâmetros sugeridos:

|Parâmetro|Valor|
|---|---|
|Tipo de conteúdo|application/json|
|Intervalo|3|
|Frequência|Segundo|

No final deste passo, o seu Logic App Designer deve ser assim:

>[!div class="mx-imgBorder"] 
>![Apps Lógicas conecta-se a Hubs de Eventos](media/eh-logic-app.png)

## <a name="stream-data-to-power-bi-from-your-logic-app"></a>Transmita dados para Power BI a partir da sua Aplicação Lógica
O próximo passo será analisar os dados provenientes do seu Hub de Eventos para os transmitir para os conjuntos de dados power BI que criou anteriormente.

1. Antes de o fazer, terá de compreender a carga útil JSON que está a ser enviada do seu dispositivo para o seu Hub de Eventos. Pode fazê-lo olhando para este esquema de [amostra](https://docs.microsoft.com/azure/iot-central/core/howto-export-data#telemetry) e modificando-o para combinar com o seu esquema ou utilizando o explorador de [ônibus de serviço](https://github.com/paolosalvatori/ServiceBusExplorer) para inspecionar as mensagens. Se estiver a utilizar as aplicações contínuas de monitorização do paciente, as suas mensagens serão assim:

**Telemetria Smart Vitals Patch**

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

**Telemetria inteligente do joelho**

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

**Propriedades**

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

2. Agora que inspecionou as suas cargas jSON, volte ao seu Logic App Designer e selecione **+ New Step**. Procure e adicione a **variável Inicializar** como próximo passo e introduza os seguintes parâmetros:

    |Parâmetro|Valor|
    |---|---|
    |Nome|Nome da interface|
    |Tipo|Cadeia|

    Prima **Guardar**. 

3. Adicione outra variável chamada **Corpo** com Tipo como **Corda**. A sua Aplicação Lógica terá estas ações adicionadas:

    >[!div class="mx-imgBorder"]
    >![Inicializar variáveis](media/initialize-string-variables.png)
    
4. Selecione **+ Novo Passo** e adicione uma ação **Parse JSON.** Mude o nome para **Parse Properties**. Para o Conteúdo, escolha **Propriedades** provenientes do Hub de Eventos. Selecione Utilize a carga útil da **amostra para gerar esquema** na parte inferior e reexa a carga útil da amostra da secção Propriedades acima.

5. Em seguida, escolha a ação **variável set** e atualize a variável **'Nome interface'** com o **nome iothub-interface** a partir das propriedades parsed JSON.

6. Adicione um Controlo **dividido** como a sua próxima ação e escolha a variável Nome da **Interface** como o parâmetro On. Irá usá-lo para canalizar os dados para o conjunto de dados correto.

7. Na sua aplicação Azure IoT Central, encontre o Nome da Interface para os dados de saúde smart Vitals Patch e os dados de saúde smart Knee Brace da vista Modelos de **Dispositivo.** Crie dois casos diferentes para o **Switch** Control para cada nome da interface e mude o nome do controlo adequadamente. Pode definir o caso Predefinido para utilizar o Controlo **de Terminações** e escolher o estado que gostaria de mostrar.

    >[!div class="mx-imgBorder"] 
    >![](media/split-by-interface.png) de controlo split

8. Para o caso **Smart Vitals Patch,** adicione uma ação **Parse JSON.** Para o Conteúdo, escolha **Conteúdo** proveniente do Centro de Eventos. Copie e cole as cargas da amostra para o Patch Smart Vitals acima para gerar o esquema.

9. Adicione uma ação **variável set** e atualize a variável **Body** com o **Corpo** a partir do JSON analisado no passo 7.

10. Adicione um Controlo de **Condição** como próxima ação e defino a condição para **body,** **contém**, **HeartRate**. Isto irá certificar-se de que tem o conjunto certo de dados provenientes do Patch Smart Vitals antes de povoar o conjunto de dados power BI. Os passos 7-9 serão assim:

    >[!div class="mx-imgBorder"] 
    >![Smart Vitals adicionam condição](media/smart-vitals-pbi.png)

11. Para o **verdadeiro** caso da Condição, adicione uma ação que chama as linhas Add a uma funcionalidade power BI de **conjunto de dados.** Terá que assinar no Power BI para isto. O seu caso **falso** pode voltar a utilizar o controlo **'Terminar'.**

12. Escolha o espaço de **trabalho**adequado, **dataset**e **tabela**. Mapeie os parâmetros que especificou ao criar o seu conjunto de dados de streaming no Power BI para os valores JSON analisados que estão vindo do seu Hub de Eventos. As tuas ações preenchidas devem ser assim:

    >[!div class="mx-imgBorder"] 
    >![Adicionar linhas ao Power BI](media/add-rows-yesenia.png)

13. Para o estojo de interruptor **de joelheira inteligente,** adicione uma ação **Parse JSON** para analisar o conteúdo, semelhante ao Passo 7. Em seguida, **adicione linhas a um conjunto de dados** para atualizar o conjunto de dados teddy Silvers no Power BI.

    >[!div class="mx-imgBorder"] 
    >![Smart Vitals adicionam condição](media/knee-brace-pbi.png)

14. Pressione **Guardar** e, em seguida, executar a sua Aplicação Lógica.

## <a name="build-a-real-time-dashboard-for-patient-vitals"></a>Construa um dashboard em tempo real para os sinais vitais do paciente
Agora volte ao Power BI e selecione **+ Criar** para criar um novo **Dashboard**. Dê um nome ao seu painel de instrumentos e bata **no Create**.

Selecione os três pontos na barra de navegação superior e, em seguida, selecione **+ Adicione o azulejo**.

>[!div class="mx-imgBorder"] 
>![Adicione azulejo ao painel de instrumentos](media/add-tile.png)

Escolha o tipo de azulejo que gostaria de adicionar e personalizar a sua app como quiser.

## <a name="clean-up-resources"></a>Limpar recursos

Se não vai continuar a utilizar esta aplicação, elimine os seus recursos com os seguintes passos:

1. A partir do portal Azure, pode eliminar os recursos do Hub de Eventos e das Aplicações Lógicas que criou.

2. Para a sua aplicação IoT Central, vá ao separador Administração e selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

* Reveja a orientação contínua de [monitorização da arquitetura do paciente.](concept-continuous-patient-monitoring-architecture.md)
