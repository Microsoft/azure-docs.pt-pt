---
title: Tutorial - Criar um painel de triagem de dados de saúde com | Central Azure IoT Microsoft Docs
description: Tutorial - Aprenda a construir um painel de triagem de dados de saúde usando modelos de aplicação Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: d227d934eedd31342ce419576fffe7cea17efb1d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101748155"
---
# <a name="tutorial-build-a-power-bi-provider-dashboard"></a>Tutorial: Construir um painel de fornecedores de Power BI

Ao construir a sua solução de monitorização contínua do paciente, também pode criar um dashboard para uma equipa de cuidados hospitalares para visualizar os dados do paciente. Neste tutorial, você aprenderá a criar um painel de streaming Power BI em tempo real a partir do seu modelo de aplicação de monitorização contínua do paciente IoT Central. Se o seu caso de utilização não necessitar de acesso a dados em tempo real, pode utilizar o [painel IoT Central Power BI](../core/howto-connect-powerbi.md), que tem um processo de implementação simplificado. 

:::image type="content" source="media/dashboard-gif-3.gif" alt-text="Painel GIF":::

A arquitetura básica seguirá esta estrutura:

:::image type="content" source="media/dashboard-architecture.png" alt-text="Painel de Triagem fornecedor":::

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Dados de exportação da Azure IoT Central para os Hubs de Eventos Azure
> * Configurar um conjunto de dados de streaming Power BI
> * Ligue a sua App Lógica aos Hubs de Eventos Azure
> * Transmita os dados para Power BI a partir da sua App Lógica
> * Construir um dashboard em tempo real para os sinais vitais do paciente


## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Um modelo de aplicação contínua de monitorização contínua do paciente Azure IoT Central. Se ainda não tiver um, pode seguir os passos para [implementar um modelo de aplicação.](overview-iot-central-healthcare.md)

* Um espaço de [nome azure Event Hubs e Centro de Eventos.](../../event-hubs/event-hubs-create.md)

* A Aplicação Lógica a que pretende aceder ao seu Centro de Eventos. Para iniciar a sua App Lógica com um gatilho Azure Event Hubs, precisa de uma [Aplicação Lógica em branco.](../../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Uma conta de serviço de POWER BI. Se ainda não tiver um, pode [criar uma conta de teste gratuita para o serviço Power BI](https://app.powerbi.com/). Se ainda não usou o Power BI antes, pode ser útil passar por [Get start with Power BI](/power-bi/service-get-started).


## <a name="set-up-a-continuous-data-export-to-azure-event-hubs"></a>Criar uma exportação contínua de dados para a Azure Event Hubs
Primeiro terá de configurar uma exportação contínua de dados do seu modelo de aplicação Azure IoT Central para o Azure Event Hub na sua subscrição. Pode fazê-lo seguindo os passos deste tutorial Azure IoT Central para [exportação para centros de eventos.](../core/howto-export-data.md) Só terá de exportar para a telemetria para efeitos deste tutorial.


## <a name="create-a-power-bi-streaming-dataset"></a>Criar um conjunto de dados de streaming Power BI

1. Inicie sessão na sua conta do Power BI.

1. No seu espaço de trabalho preferido, crie um novo conjunto de dados de streaming selecionando o botão **+ Criar** no canto superior direito da barra de ferramentas. Terá de criar um conjunto de dados separado para cada paciente que gostaria de ter no seu painel de instrumentos.

   :::image type="content" source="media/create-streaming-dataset.png" alt-text="Criar conjunto de dados de streaming":::


1. Escolha **a API** para a origem do seu conjunto de dados.

1. Introduza um **nome** (por exemplo, o nome de um paciente) para o seu conjunto de dados e, em seguida, preencha os valores do seu fluxo. Pode ver um exemplo abaixo baseado em valores provenientes dos dispositivos simulados no modelo de aplicação de monitorização contínua do paciente. O exemplo tem dois pacientes:

   * Teddy Silvers, que tem dados da Cinta inteligente do joelho.
   * Yesenia Sanford, que tem dados do Smart Vitals Patch.

   :::image type="content" source="media/enter-dataset-values.png" alt-text="Introduza os valores do conjunto de dados":::

Para saber mais sobre conjuntos de dados de streaming no Power BI, pode ler este documento em [streaming em tempo real no Power BI](/power-bi/service-real-time-streaming).


## <a name="connect-your-logic-app-to-azure-event-hubs"></a>Ligue a sua App Lógica aos Hubs de Eventos Azure

Para ligar a sua App Lógica aos Hubs de Eventos Azure, pode seguir as instruções descritas neste documento sobre [o envio de eventos com a Azure Event Hubs e Azure Logic Apps.](../../connectors/connectors-create-api-azure-event-hubs.md#add-event-hubs-action) Aqui estão alguns parâmetros sugeridos:

|Parâmetro|Valor|
|---|---|
|Tipo do conteúdo|application/json|
|Intervalo|3|
|Frequência|Second|

No final deste passo, o seu Designer de Aplicações Lógicas deve ser assim:

>[!div class="mx-imgBorder"] 
>![As Aplicações Lógicas ligam-se aos Centros de Eventos](media/eh-logic-app.png)

:::image type="content" source="media/enter-dataset-values.png" alt-text="Introduza os valores do conjunto de dados":::


## <a name="stream-data-to-power-bi-from-your-logic-app"></a>Transmita os dados para Power BI a partir da sua App Lógica

O próximo passo será analisar os dados provenientes do seu Centro de Eventos para os transmitir para os conjuntos de dados Power BI que criou anteriormente.

Antes de o poder fazer, terá de compreender a carga útil JSON que está a ser enviada do seu dispositivo para o seu Centro de Eventos. Pode fazê-lo olhando para este [esquema de amostra](../core/howto-export-data.md#telemetry-format) e modificando-o para combinar com o seu esquema ou usando o explorador de Service [Bus](https://github.com/paolosalvatori/ServiceBusExplorer) para inspecionar as mensagens. Se estiver a utilizar as aplicações de monitorização contínua do paciente, as suas mensagens serão assim:

**Smart Vitals Patch telemetria**

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

**Telemetria inteligente da joelheira**

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

1. Agora que inspecionou as suas cargas JSON, volte ao seu Designer de Aplicações Lógica e selecione **+ Novo Passo**. Pesse e adicione **a variável Initialize** como o seu próximo passo e introduza os seguintes parâmetros:

   |Parâmetro|Valor|
   |---|---|
   |Nome|Nome da interface|
   |Tipo|String|

   Selecione **Guardar**. 

1. Adicione outra variável chamada **Corpo** com Tipo de **Corda**. A sua Aplicação Lógica terá estas ações adicionadas:

   :::image type="content" source="media/initialize-string-variables.png" alt-text="Inicializar variáveis":::
    
1. Selecione **+ Novo Passo** e adicione uma ação **Parse JSON.** Mude o nome para **Parse Properties.** Para o Conteúdo, escolha **Propriedades** provenientes do Centro de Eventos. Selecione Utilize a carga útil da **amostra para gerar esquema** na parte inferior e cole a carga útil da amostra a partir da secção Propriedades acima.

1. Em seguida, escolha a ação **variável set** e atualize a variável **Nome de Interface** com o nome **iothub-interface-name** a partir das propriedades JSON analisadas.

1. Adicione um **Controlo Dividido** como a sua próxima ação e escolha a variável Nome de **Interface** como parâmetro On. Utilizará isto para canalizar os dados para o conjunto de dados correto.

1. Na sua aplicação Azure IoT Central, encontre o Nome de Interface para os dados de saúde Smart Vitals Patch e os dados de saúde smart knee brace da vista **Modelos do Dispositivo.** Crie dois casos diferentes para o Controlo da **Switch** para cada Nome de Interface e mude o nome do controlo adequadamente. Pode definir a caixa predefinida para utilizar o Controlo **de Terminação** e escolher o estado que pretende mostrar.

   :::image type="content" source="media/split-by-interface.png" alt-text="Controlo dividido":::

1. Para o caso **Smart Vitals Patch,** adicione uma ação **Parse JSON.** Para o Conteúdo, escolha **Conteúdo** vindo do Centro de Eventos. Copie e cole as cargas de amostra para o Smart Vitals Patch acima para gerar o esquema.

1. Adicione uma ação **variável definida** e atualize a variável **Body** com o **Corpo** a partir do JSON analisado no passo 7.

1. Adicione um **Controlo de Condições** como a sua próxima ação e desembra a condição para **o Corpo,** **contém,** **HeartRate**. Isto irá certificar-se de que tem o conjunto certo de dados provenientes do Smart Vitals Patch antes de povoar o conjunto de dados Power BI. Passos 7-9 será assim:

   :::image type="content" source="media/smart-vitals-pbi.png" alt-text="Smart Vitals adiciona condição":::

1. Para o **caso verdadeiro** da Condição, adicione uma ação que chame as linhas Add a uma funcionalidade power bi de conjunto **de dados.** Terá de assinar no Power BI para isto. A sua **mala falsa** pode voltar a utilizar o controlo **"Fim".**

1. Escolha o **espaço de trabalho** apropriado, conjunto de **dados** e **tabela**. Mapear os parâmetros especificados ao criar o seu conjunto de dados de streaming no Power BI para os valores JSON analisados que estão vindo do seu Centro de Eventos. As tuas ações preenchidas devem ser assim:
 
   :::image type="content" source="media/add-rows-yesenia.png" alt-text="Adicione linhas ao Power BI":::

1. Para o estojo de interruptor **de joelheira Smart Knee,** adicione uma ação **Parse JSON** para analisar o conteúdo, semelhante ao Passo 7. Em **seguida, adicione linhas a um conjunto de dados** para atualizar o conjunto de dados teddy Silvers no Power BI.

   :::image type="content" source="media/knee-brace-pbi.png" alt-text="Screenshot que mostra como adicionar linhas a um conjunto de dados":::

1. Pressione **Guardar** e, em seguida, executar a sua App Lógica.


## <a name="build-a-real-time-dashboard-for-patient-vitals"></a>Construir um dashboard em tempo real para os sinais vitais do paciente

Agora volte ao Power BI e selecione **+ Criar** para criar um novo **Dashboard**. Dê ao seu painel um nome e bata **Create**.

Selecione os três pontos na barra de navegação superior e, em seguida, selecione **+ Adicionar azulejos**.

:::image type="content" source="media/add-tile.png" alt-text="Adicione azulejo ao tablier":::

Escolha o tipo de azulejo que gostaria de adicionar e personalize a sua aplicação como quiser.


## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, elimine os seus recursos com os seguintes passos:

1. A partir do portal Azure, pode eliminar os recursos do Event Hub e das Aplicações Lógicas que criou.

1. Para a sua aplicação IoT Central, vá ao separador Administração e selecione **Delete**.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Orientação contínua da arquitetura de monitorização do paciente](concept-continuous-patient-monitoring-architecture.md)