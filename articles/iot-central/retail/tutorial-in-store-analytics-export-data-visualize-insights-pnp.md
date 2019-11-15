---
title: 'Tutorial: exportar dados e visualizar informações no Azure IoT Central'
description: Neste tutorial, saiba como exportar dados do IoT Central e visualizar informações em um painel de Power BI.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: dobett
author: dominicbetts
ms.date: 11/12/2019
ms.openlocfilehash: ce775f207eaa5df05900ea2e5b6d9fbeab4dc878
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112886"
---
# <a name="tutorial-export-data-from-azure-iot-central-and-visualize-insights-in-power-bi"></a>Tutorial: exportar dados do Azure IoT Central e visualizar informações no Power BI

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Nos dois tutoriais anteriores, você criou e personalizou um aplicativo IoT Central usando o modelo **de aplicativo análise de check-out no repositório** . Neste tutorial, você configura seu aplicativo IoT Central para exportar a telemetria coletada dos dispositivos. Em seguida, você usa Power BI para criar um painel personalizado para o Gerenciador de loja Visualizar as informações derivadas da telemetria.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Configure um aplicativo IoT Central para exportar telemetria para um hub de eventos.
> * Use os aplicativos lógicos para enviar dados de um hub de eventos para um conjunto Power BI DataSet.
> * Crie um painel de Power BI para visualizar os dados no DataSet de streaming.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Para concluir os dois tutoriais anteriores, [crie um aplicativo de análise no repositório no azure IOT central](./tutorial-in-store-analytics-create-app-pnp.md) e [Personalize o painel do operador e gerencie os dispositivos na IOT central do Azure](./tutorial-in-store-analytics-customize-dashboard-pnp.md).
* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Uma conta do Power BI. Se você não tiver uma conta de Power BI, Inscreva-se para uma [avaliação gratuita Power bi pro](https://app.powerbi.com/signupredirect?pbi_source=web) antes de começar.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de criar o Hub de eventos e o aplicativo lógico, você precisa criar um grupo de recursos para gerenciá-los. O grupo de recursos deve estar no mesmo local que o aplicativo **Analytics-check-out in-store** IOT central. Para criar um grupo de recursos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No painel de navegação esquerdo, selecione **grupos de recursos**. Em seguida, selecione **Adicionar**.
1. Para **assinatura**, selecione o nome da assinatura do Azure que você usou para criar seu aplicativo IOT central.
1. Para o nome do **grupo de recursos** , insira _Retail-Store-Analysis_*.
1. Para a **região**, selecione a mesma região que você escolheu para o aplicativo IOT central.
1. Selecione **revisão + criar**.
1. Na página **revisar + criar** , selecione **criar**.

Agora você tem um grupo de recursos chamado **Retail-Store-Analysis** na sua assinatura.

## <a name="create-an-event-hub"></a>Criar um hub de eventos

Antes de configurar o aplicativo de monitoramento de varejo para exportar a telemetria, você precisa criar um hub de eventos para receber os dados exportados. As etapas a seguir mostram como criar seu hub de eventos:

1. Na portal do Azure, selecione **criar um recurso** na parte superior esquerda da tela.
1. Em **Pesquisar no Marketplace**, insira os _hubs de eventos_e pressione **Enter**.
1. Sobre o **os Hubs de eventos** página, selecione **criar**.
1. Na página **criar namespace** , execute as seguintes etapas:
    * Insira um nome exclusivo para o namespace, como _o namename-Retail-Store-Analysis_. O sistema verifica se esse nome está disponível.
    * Escolha o tipo de preço **básico** .
    * Selecione a mesma **assinatura** que você usou para criar seu aplicativo IOT central.
    * Selecione o grupo de recursos **Retail-Store-Analysis** .
    * Selecione o mesmo local usado para seu aplicativo IoT Central.
    * Selecione **Criar**. Talvez seja necessário aguardar alguns minutos para que o sistema Provisione os recursos.
1. No portal, navegue até o grupo de recursos **Retail-Store-Analysis** . Aguarde pela conclusão da implementação. Talvez seja necessário selecionar **Atualizar** para atualizar o status da implantação. Você também pode verificar o status da criação do namespace do hub de eventos nas **notificações**.
1. No grupo de recursos **Retail-Store-Analysis** , selecione o **namespace de hubs de eventos**. Você verá o home page para seu **namespace de hubs de eventos** no Portal.

Agora você tem um **namespace de hubs de eventos**, você pode criar um **Hub de eventos** para usar com seu aplicativo IOT central:

1. Na home page do seu **namespace de hubs de eventos** no portal, selecione **+ Hub de eventos**.
1. Na página **criar Hub de eventos** , digite _telemetria de repositório_ como o nome e, em seguida, selecione **criar**.

Agora você tem um hub de eventos que pode ser usado ao configurar a exportação de dados do seu aplicativo IoT Central:

![Hub de eventos](./media/tutorial-in-store-analytics-visualize-insights-pnp/event-hub.png)

## <a name="configure-data-export"></a>Configurar exportação de dados

Agora você tem um hub de eventos, é possível configurar seu aplicativo **Analytics-checkout no repositório** para exportar a telemetria dos dispositivos conectados. As etapas a seguir mostram como configurar a exportação:

1. Entre em sua **análise na loja-check-out** IOT central aplicativo.
1. Selecione **exportação de dados** no painel esquerdo.
1. Selecione **novo > hubs de eventos do Azure**.
1. Insira _exportação de telemetria_ como o **nome de exibição**.
1. Selecione seu **namespace de hubs de eventos**.
1. Selecione o Hub de eventos **Store-telemetria** .
1. Desative **dispositivos** e **modelos de dispositivo** na seção **dados a serem exportados** .
1. Selecione **Guardar**.

A exportação de dados pode levar alguns minutos para começar a enviar telemetria para o Hub de eventos. Você pode ver o status da exportação na página **exportações de dados** :

![Configuração de exportação de dados contínuas](./media/tutorial-in-store-analytics-visualize-insights-pnp/export-configuration.png)

## <a name="create-the-power-bi-datasets"></a>Criar os conjuntos de Power BI de os

Seu painel de Power BI exibirá dados do seu aplicativo de monitoramento de varejo. Nesta solução, você usa Power BI conjuntos de dados de streaming como a fonte de dado para o painel de Power BI. Nesta seção, você define o esquema dos conjuntos de dados de streaming para que o aplicativo lógico possa encaminhar dados do hub de eventos. As etapas a seguir mostram como criar dois conjuntos de Datastream para os sensores ambientais e um conjunto de fluxo de transmissão para o sensor de ocupação:

1. Inicie sessão na sua conta do **Power BI**.
1. Selecione **espaços de trabalho**e, em seguida, selecione **criar um espaço de trabalho**.
1. Na página **criar um espaço de trabalho** , insira _Analytics no repositório-check-out_ como o **nome do espaço de trabalho**.
1. Role até a parte inferior da página **Bem-vindo ao espaço de trabalho análise-check-out no armazenamento** e selecione **ignorar**.
1. Na página do espaço de trabalho, selecione **criar > conjunto de fluxos de mídia**.
1. Na página **novo conjunto de mídias de streaming** , escolha **API**e, em seguida, selecione **Avançar**.
1. Insira _zona 1 sensor_ como o **nome do conjunto**de um.
1. Insira os três **valores do fluxo** na tabela a seguir:

    | Nome do valor  | Tipo de valor |
    | ----------- | ---------- |
    | Carimbo de data/hora   | DateTime   |
    | Humidade    | Número     |
    | Temperatura | Número     |

1. Alterne a **análise de dados históricos** em.
1. Selecione **criar** e, em seguida, **concluído**.
1. Crie outro conjunto de fluxo de transmissão chamado **zona 2 sensor** com o mesmo esquema e configurações do conjunto de zona 1 streaming do **sensor** .

Agora você tem dois conjuntos de espelhos de transmissão. O aplicativo lógico roteará a telemetria dos dois sensores ambientais conectados ao seu aplicativo **Analytics-checkout na loja** para esses dois conjuntos de valores:

![Conjuntos de zonas de os](./media/tutorial-in-store-analytics-visualize-insights-pnp/dataset-1.png)

Essa solução usa um conjunto de dados de streaming para cada sensor porque não é possível aplicar filtros para transmitir dados em Power BI.

Você também precisa de um conjunto de fluxo de transmissão para a telemetria de ocupação:

1. Na página do espaço de trabalho, selecione **criar > conjunto de fluxos de mídia**.
1. Na página **novo conjunto de mídias de streaming** , escolha **API**e, em seguida, selecione **Avançar**.
1. Insira o _sensor de ocupação_ como o nome do **conjunto**de um.
1. Insira os cinco **valores do fluxo** na tabela a seguir:

    | Nome do valor     | Tipo de valor |
    | -------------- | ---------- |
    | Carimbo de data/hora      | DateTime   |
    | Comprimento da fila 1 | Número     |
    | Comprimento da fila 2 | Número     |
    | Tempo de pesquisa 1   | Número     |
    | Tempo de duração 2   | Número     |

1. Alterne a **análise de dados históricos** em.
1. Selecione **criar** e, em seguida, **concluído**.

Agora você tem um terceiro conjunto de fluxos de transmissão que armazena valores do sensor de ocupação simulado. Esse sensor informa o comprimento da fila nos dois check-outs no repositório e por quanto tempo os clientes estão aguardando nessas filas:

![Conjunto de uma ocupação](./media/tutorial-in-store-analytics-visualize-insights-pnp/dataset-2.png)

## <a name="create-a-logic-app"></a>Criar uma aplicação lógica

Nessa solução, o aplicativo lógico lê a telemetria do hub de eventos, analisa os dados e, em seguida, envia-os para os Power BI conjuntos de dados de streaming que você criou.

Antes de criar o aplicativo lógico, você precisa das IDs de dispositivo dos dois sensores RuuviTag conectados ao seu aplicativo IoT Central no tutorial [criar um aplicativo de análise na loja no Azure IOT central](./tutorial-in-store-analytics-create-app-pnp.md) :

1. Entre em sua **análise na loja-check-out** IOT central aplicativo.
1. Selecione **dispositivos** no painel esquerdo. Em seguida, selecione **RuuviTag**.
1. Anote as **IDs do dispositivo**. Na captura de tela a seguir, as IDs são **f5dcf4ac32e8** e **e29ffc8d5326**:

    ![IDs de dispositivo](./media/tutorial-in-store-analytics-visualize-insights-pnp/device-ids.png)

As etapas a seguir mostram como criar o aplicativo lógico no portal do Azure:

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **criar um recurso** na parte superior esquerda da tela.
1. Em **Pesquisar no Marketplace**, insira _aplicativo lógico_e pressione **Enter**.
1. Na página **aplicativo lógico** , selecione **criar**.
1. Na página Criar do **aplicativo lógico** :
    * Insira um nome exclusivo para seu aplicativo lógico, como _o seu.-Retail-Store-Analysis_.
    * Selecione a mesma **assinatura** que você usou para criar seu aplicativo IOT central.
    * Selecione o grupo de recursos **Retail-Store-Analysis** .
    * Selecione o mesmo local usado para seu aplicativo IoT Central.
    * Selecione **Criar**. Talvez seja necessário aguardar alguns minutos para que o sistema Provisione os recursos.
1. No portal do Azure, navegue até seu novo aplicativo lógico.
1. Na página **Designer de aplicativos lógicos** , role para baixo e selecione **aplicativo lógico em branco**.
1. Em **conectores de pesquisa e gatilhos**, insira os _hubs de eventos_.
1. Em **gatilhos**, selecione **quando os eventos estão disponíveis no Hub de eventos**.
1. Insira _telemetria de repositório_ como o **nome da conexão**e selecione seu namespace de **hubs de eventos**.
1. Selecione a política **RootManageSharedAccess** e selecione **criar**.
1. Na ação **quando os eventos estão disponíveis na Event Hub** :
    * Em **nome do hub de eventos**, selecione **loja-telemetria**.
    * Em **tipo de conteúdo**, selecione **Application/JSON**.
    * Defina o **intervalo** como três e a **frequência** como segundos
1. Selecione **salvar** para salvar seu aplicativo lógico.

Para adicionar a lógica ao design do aplicativo lógico, selecione **exibição de código**:

1. Substitua `"actions": {},` pelo JSON a seguir. Substitua os dois espaços reservados `[YOUR RUUVITAG DEVICE ID 1]` e `[YOUR RUUVITAG DEVICE ID 2]` com as IDs que você anotou de seus dois dispositivos RuuviTag:

    ```json
    "actions": {
        "Initialize_Device_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "DeviceID",
                        "type": "String"
                    }
                ]
            },
            "runAfter": {},
            "type": "InitializeVariable"
        },
        "Initialize_Interface_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "InterfaceID",
                        "type": "String",
                        "value": "Other"
                    }
                ]
            },
            "runAfter": {
                "Initialize_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "InitializeVariable"
        },
        "Parse_Properties": {
            "inputs": {
                "content": "@triggerBody()?['Properties']",
                "schema": {
                    "properties": {
                        "iothub-connection-auth-generation-id": {
                            "type": "string"
                        },
                        "iothub-connection-auth-method": {
                            "type": "string"
                        },
                        "iothub-connection-device-id": {
                            "type": "string"
                        },
                        "iothub-enqueuedtime": {
                            "type": "string"
                        },
                        "iothub-interface-name": {
                            "type": "string"
                        },
                        "iothub-message-source": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "integer"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Parse_Telemetry": {
            "inputs": {
                "content": "@triggerBody()?['ContentData']",
                "schema": {
                    "properties": {
                        "DwellTime1": {
                            "type": "number"
                        },
                        "DwellTime2": {
                            "type": "number"
                        },
                        "count1": {
                            "type": "number"
                        },
                        "count2": {
                            "type": "number"
                        },
                        "humidity": {
                            "type": "number"
                        },
                        "temperature": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Set_Device_ID_variable": {
            "inputs": {
                "name": "DeviceID",
                "value": "@body('Parse_Properties')?['iothub-connection-device-id']"
            },
            "runAfter": {
                "Parse_Properties": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Set_Interface_ID_variable": {
            "inputs": {
                "name": "InterfaceID",
                "value": "@body('Parse_Properties')?['iothub-interface-name']"
            },
            "runAfter": {
                "Set_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Switch_by_DeviceID": {
            "cases": {
                "Occupancy": {
                    "actions": {
                        "Switch_by_InterfaceID": {
                            "cases": {
                                "Dwell_Time_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_v2_1l0"
                                },
                                "People_Count_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_iv"
                                }
                            },
                            "default": {
                                "actions": {}
                            },
                            "expression": "@variables('InterfaceID')",
                            "runAfter": {},
                            "type": "Switch"
                        }
                    },
                    "case": "Occupancy"
                },
                "Zone 2 environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 2]"
                },
                "Zone_1_environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 1]"
                }
            },
            "default": {
                "actions": {}
            },
            "expression": "@variables('DeviceID')",
            "runAfter": {
                "Parse_Telemetry": [
                    "Succeeded"
                ],
                "Set_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "Switch"
        }
    },
    ```

1. Selecione **salvar** e, em seguida, selecione **Designer** para ver a versão Visual da lógica que você adicionou:

    ![Design de aplicativo lógico](./media/tutorial-in-store-analytics-visualize-insights-pnp/logic-app.png)

1. Selecione **alternar por DeviceID** para expandir a ação. Em seguida, selecione **zona 1 ambiente**e selecione **Adicionar uma ação**.
1. Em **Pesquisar conectores e ações**, digite **Power bi**e pressione **Enter**.
1. Selecione a ação **adicionar linhas a um conjunto de uma (visualização)** .
1. Selecione **entrar** e siga os prompts para entrar em sua conta do Power bi.
1. Após a conclusão do processo de entrada, na ação **adicionar linhas a um conjunto de registros** :
    * Selecione **in-Store Analytics-checkout** como o espaço de trabalho.
    * Selecione **zona 1 sensor** como o conjunto de os.
    * Selecione **RealTimeData** como a tabela.
    * Selecione **Adicionar novo parâmetro** e, em seguida, selecione os campos **carimbo de data/hora**, **umidade**e **temperatura** .
    * Selecione o campo **timestamp** e, em seguida, selecione **x-opt-enqueuedtime** na lista de **conteúdo dinâmico** .
    * Selecione o campo **umidade** e, em seguida, selecione **Ver mais** ao lado de **analisar telemetria**. Em seguida, selecione **umidade**.
    * Selecione o campo **temperatura** e, em seguida, selecione **Ver mais** ao lado de **analisar telemetria**. Em seguida, selecione **temperatura**.
    * Selecione **guardar** para guardar as alterações. A ação de **ambiente zona 1** é semelhante à captura de tela a seguir: ![ambiente de zona 1](./media/tutorial-in-store-analytics-visualize-insights-pnp/zone-1-action.png)
1. Selecione a ação **zona 2 ambiente** e selecione **Adicionar uma ação**.
1. Em **Pesquisar conectores e ações**, digite **Power bi**e pressione **Enter**.
1. Selecione a ação **adicionar linhas a um conjunto de uma (visualização)** .
1. Na ação **adicionar linhas a um conjunto de registros 2** :
    * Selecione **in-Store Analytics-checkout** como o espaço de trabalho.
    * Selecione **zona 2 sensor** como o conjunto de os.
    * Selecione **RealTimeData** como a tabela.
    * Selecione **Adicionar novo parâmetro** e, em seguida, selecione os campos **carimbo de data/hora**, **umidade**e **temperatura** .
    * Selecione o campo **timestamp** e, em seguida, selecione **x-opt-enqueuedtime** na lista de **conteúdo dinâmico** .
    * Selecione o campo **umidade** e, em seguida, selecione **Ver mais** ao lado de **analisar telemetria**. Em seguida, selecione **umidade**.
    * Selecione o campo **temperatura** e, em seguida, selecione **Ver mais** ao lado de **analisar telemetria**. Em seguida, selecione **temperatura**.
    Selecione **guardar** para guardar as alterações.  A ação de **ambiente zona 2** é semelhante à captura de tela a seguir: ![ambiente de zona 2](./media/tutorial-in-store-analytics-visualize-insights-pnp/zone-2-action.png)
1. Selecione a ação de **ocupação** e, em seguida, selecione a ação **alternar por ID de interface** .
1. Selecione a ação de **interface de tempo de duração de pesquisa** e selecione **Adicionar uma ação**.
1. Em **Pesquisar conectores e ações**, digite **Power bi**e pressione **Enter**.
1. Selecione a ação **adicionar linhas a um conjunto de uma (visualização)** .
1. Na ação **adicionar linhas a um conjunto de registros** :
    * Selecione **in-Store Analytics-checkout** como o espaço de trabalho.
    * Selecione **sensor de ocupação** como o conjunto de os.
    * Selecione **RealTimeData** como a tabela.
    * Selecione **Adicionar novo parâmetro** e, em seguida, selecione os campos **carimbo de data/** hora, **tempo de duração 1**e **tempo de duração 2** .
    * Selecione o campo **timestamp** e, em seguida, selecione **x-opt-enqueuedtime** na lista de **conteúdo dinâmico** .
    * Selecione o campo **tempo de duração 1** e, em seguida, selecione **Ver mais** ao lado de **analisar telemetria**. Em seguida, selecione **DwellTime1**.
    * Selecione o campo **tempo de duração 2** e, em seguida, selecione **Ver mais** ao lado de **analisar telemetria**. Em seguida, selecione **DwellTime2**.
    * Selecione **guardar** para guardar as alterações. A ação de **interface de tempo de duração da pesquisa** é semelhante à seguinte captura de tela: ![ação de ocupação](./media/tutorial-in-store-analytics-visualize-insights-pnp/occupancy-action-1.png)
1. Selecione a ação **interface de contagem de pessoas** e selecione **Adicionar uma ação**.
1. Em **Pesquisar conectores e ações**, digite **Power bi**e pressione **Enter**.
1. Selecione a ação **adicionar linhas a um conjunto de uma (visualização)** .
1. Na ação **adicionar linhas a um conjunto de registros** :
    * Selecione **in-Store Analytics-checkout** como o espaço de trabalho.
    * Selecione **sensor de ocupação** como o conjunto de os.
    * Selecione **RealTimeData** como a tabela.
    * Selecione **Adicionar novo parâmetro** e, em seguida, selecione os campos **carimbo de data/hora**, **comprimento da fila 1**e **comprimento da fila 2** .
    * Selecione o campo **timestamp** e, em seguida, selecione **x-opt-enqueuedtime** na lista de **conteúdo dinâmico** .
    * Selecione o campo **comprimento da fila 1** e, em seguida, selecione **Ver mais** ao lado de **analisar telemetria**. Em seguida, selecione **count1**.
    * Selecione o campo **comprimento da fila 2** e, em seguida, selecione **Ver mais** ao lado de **analisar telemetria**. Em seguida, selecione **Count2**.
    * Selecione **guardar** para guardar as alterações. A ação da **interface de contagem de pessoas** é semelhante à seguinte captura de tela: ação de ![de ocupação](./media/tutorial-in-store-analytics-visualize-insights-pnp/occupancy-action-2.png)

O aplicativo lógico é executado automaticamente. Para ver o status de cada execução, navegue até a página **visão geral** do aplicativo lógico no portal do Azure:

## <a name="create-a-power-bi-dashboard"></a>Criar um painel de Power BI

Agora você tem telemetria fluindo do seu aplicativo IoT Central por meio de seu hub de eventos. Em seguida, seu aplicativo lógico analisa as mensagens do hub de eventos e as adiciona a um conjunto de Power BI fluxo de os. Agora, você pode criar um painel de Power BI para visualizar a telemetria:

1. Inicie sessão na sua conta do **Power BI**.
1. Selecione **espaços de trabalho > análise na loja-check-out**.
1. Selecione **criar > painel**.
1. Insira o **Store Analytics** como o nome do painel e selecione **criar**.

### <a name="add-line-charts"></a>Adicionar gráficos de linhas

Adicione quatro blocos de gráfico de linhas para mostrar a temperatura e a umidade dos dois sensores ambientais. Use as informações na tabela a seguir para criar os blocos. Para adicionar cada bloco, comece selecionando **... (Mais opções) > Adicionar bloco**. Selecione **dados de streaming personalizados**e, em seguida, selecione **Avançar**:

| Definição | #1 do gráfico | #2 do gráfico | #3 do gráfico | #4 do gráfico |
| ------- | -------- | -------- | -------- | -------- |
| Conjunto de dados | Sensor de Zona 1 | Sensor de Zona 1 | Sensor de Zona 2 | Sensor de Zona 2 |
| Tipo de visualização | Gráfico de linhas | Gráfico de linhas | Gráfico de linhas | Gráfico de linhas |
| Valores | Carimbo de data/hora | Carimbo de data/hora | Carimbo de data/hora | Carimbo de data/hora |
| Valores | Temperatura | Humidade | Temperatura | Humidade |
| Janela de tempo | 60 minutos | 60 minutos | 60 minutos | 60 minutos |
| Título | Temperatura (1 hora) | Umidade (1 hora) | Temperatura (1 hora) | Umidade (1 hora) |
| Subtítulo | Zona 1 | Zona 1 | Zona 2 | Zona 2 |

A captura de tela a seguir mostra as configurações do primeiro gráfico:

![Definições do gráfico de linhas](./media/tutorial-in-store-analytics-visualize-insights-pnp/line-chart.png)

### <a name="add-cards-to-show-environmental-data"></a>Adicionar cartões para mostrar dados ambientais

Adicione quatro blocos de cartão para mostrar os valores de temperatura e umidade mais recentes dos dois sensores ambientais. Use as informações na tabela a seguir para criar os blocos. Para adicionar cada bloco, comece selecionando **... (Mais opções) > Adicionar bloco**. Selecione **dados de streaming personalizados**e, em seguida, selecione **Avançar**:

| Definição | #1 do cartão | #2 do cartão | #3 do cartão | #4 do cartão |
| ------- | ------- | ------- | ------- | ------- |
| Conjunto de dados | Sensor de Zona 1 | Sensor de Zona 1 | Sensor de Zona 2 | Sensor de Zona 2 |
| Tipo de visualização | Descartado | Descartado | Descartado | Descartado |
| Campos | Temperatura | Humidade | Temperatura | Humidade |
| Título | Temperatura (F) | Umidade (%) | Temperatura (F) | Umidade (%) |
| Subtítulo | Zona 1 | Zona 1 | Zona 2 | Zona 2 |

A captura de tela a seguir mostra as configurações para o primeiro cartão:

![Configurações do cartão](./media/tutorial-in-store-analytics-visualize-insights-pnp/card-settings.png)

### <a name="add-tiles-to-show-checkout-occupancy-data"></a>Adicionar blocos para mostrar dados de ocupação de check-out

Adicione quatro blocos de cartão para mostrar o comprimento da fila e o tempo de duração da pesquisa para os dois check-outs no repositório. Use as informações na tabela a seguir para criar os blocos. Para adicionar cada bloco, comece selecionando **... (Mais opções) > Adicionar bloco**. Selecione **dados de streaming personalizados**e, em seguida, selecione **Avançar**:

| Definição | #1 do cartão | #2 do cartão | #3 do cartão | #4 do cartão |
| ------- | ------- | ------- | ------- | ------- |
| Conjunto de dados | Sensor de ocupação | Sensor de ocupação | Sensor de ocupação | Sensor de ocupação |
| Tipo de visualização | Gráfico de colunas clusterizado | Gráfico de colunas clusterizado | Medidor | Medidor |
| Valores    | Carimbo de data/hora | Carimbo de data/hora | N/D | N/D |
| Valor | Tempo de pesquisa 1 | Tempo de duração 2 | Comprimento da fila 1 | Comprimento da fila 2 |
| Janela de tempo | 60 minutos | 60 minutos |  N/D | N/D |
| Título | Tempo de duração da pesquisa | Tempo de duração da pesquisa | Comprimento da fila | Comprimento da fila |
| Subtítulo | Check-out 1 | Check-out 2 | Check-out 1 | Check-out 2 |

Redimensione e reorganize os blocos no painel para se parecer com a captura de tela a seguir:

![Dashboard do Power BI](./media/tutorial-in-store-analytics-visualize-insights-pnp/pbi-dashboard.png)

Você pode adicionar alguns recursos gráficos adicionais para personalizar ainda mais o painel:

![Dashboard do Power BI](./media/tutorial-in-store-analytics-visualize-insights-pnp/pbi-dashboard-graphics.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se tiver concluído o aplicativo IoT Central, você poderá excluí-lo entrando no aplicativo e navegando até a página Configurações do **aplicativo** na seção **Administração** .

Se você quiser manter o aplicativo, mas reduzir os custos associados a ele, desabilite a exportação de dados que está enviando telemetria para o Hub de eventos.

Você pode excluir o Hub de eventos e o aplicativo lógico no portal do Azure excluindo o grupo de recursos chamado **Retail-Store-Analysis**.

Você pode excluir seus conjuntos de Power BI de trabalho e o painel excluindo o espaço de trabalho da página Configurações de Power BI do espaço de trabalho.

## <a name="next-steps"></a>Passos Seguintes

Esses três tutoriais mostraram uma solução de ponta a ponta que usa o modelo **de aplicativo análise na loja-check-out** IOT central. Você conectou dispositivos ao aplicativo, usou IoT Central para monitorar os dispositivos e usou Power BI para criar um painel para exibir informações da telemetria do dispositivo. Uma próxima etapa recomendada é explorar um dos outros modelos de aplicativo IoT Central:

> [!div class="nextstepaction"]
> * [Crie soluções de energia com IoT Central](../energy/overview-iot-central-energy.md)
> * [Crie soluções governamentais com o IoT Central](../government/overview-iot-central-government.md)
> * [Crie soluções de saúde com IoT Central](../healthcare/overview-iot-central-healthcare.md)
