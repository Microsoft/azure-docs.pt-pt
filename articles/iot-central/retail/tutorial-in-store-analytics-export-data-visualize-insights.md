---
title: Tutorial - Dados de exportação e visualização de insights no Azure IoT Central
description: Neste tutorial, aprenda a exportar dados da IoT Central e visualize insights num dashboard Power BI.
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
ms.openlocfilehash: 6062e8a74af4bb0a19d02ccf9a4c50da0cc4a7c5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81000106"
---
# <a name="tutorial-export-data-from-azure-iot-central-and-visualize-insights-in-power-bi"></a>Tutorial: Dados de exportação da Azure IoT Central e visualizam insights no Power BI



Nos dois tutoriais anteriores, criou e personalizou uma aplicação IoT Central utilizando o modelo **de aplicação in-store - checkout.** Neste tutorial, configura a sua aplicação IoT Central para exportar telemetria recolhida dos dispositivos. Em seguida, utilize o Power BI para criar um dashboard personalizado para o gestor da loja visualizar os insights derivados da telemetria.

Neste tutorial, vai aprender a:
> [!div class="checklist"]
> * Configure uma aplicação IoT Central para exportar telemetria para um centro de eventos.
> * Utilize aplicações lógicas para enviar dados de um centro de eventos para um conjunto de dados de streaming power BI.
> * Crie um dashboard Power BI para visualizar dados no conjunto de dados de streaming.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Para completar os dois tutoriais anteriores, [Crie uma aplicação de análise na loja na Azure IoT Central](./tutorial-in-store-analytics-create-app.md) e [personalize o painel de instrumentos do operador e gereos dispositivos em Azure IoT Central.](./tutorial-in-store-analytics-customize-dashboard.md)
* Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Uma conta do Power BI. Se não tiver uma conta Power BI, inscreva-se num [teste free Power BI Pro](https://app.powerbi.com/signupredirect?pbi_source=web) antes de começar.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de criar o seu hub de eventos e app lógica, precisa de criar um grupo de recursos para os gerir. O grupo de recursos deve estar no mesmo local que a sua **aplicação In-store - checkout** IoT Central. Para criar um grupo de recursos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Na navegação à esquerda, selecione **Grupos de Recursos**. Em seguida, selecione **Adicionar**.
1. Para **Subscrição**, selecione o nome da subscrição Azure que usou para criar a sua aplicação IoT Central.
1. Para o nome do **grupo Resource,** insira a _análise da loja de retalho_*.
1. Para a **Região,** selecione a mesma região que escolheu para a aplicação IoT Central.
1. Selecione **Rever + Criar**.
1. Na página **Review + Criar,** selecione **Criar**.

Tem agora um grupo de recursos chamado **retail store-analysis** na sua subscrição.

## <a name="create-an-event-hub"></a>Criar um hub de eventos

Antes de configurar o pedido de monitorização de retalho para exportar telemetria, precisa de criar um centro de eventos para receber os dados exportados. Os seguintes passos mostram-lhe como criar o seu centro de eventos:

1. No portal Azure, selecione **Criar um recurso** na parte superior esquerda do ecrã.
1. Em **Search the Marketplace,** introduza Centros de _Eventos,_ e, em seguida, prima **Enter**.
1. Na página Hubs do **Evento,** selecione **Criar**.
1. Na página **Create Namespace,** tome os seguintes passos:
    * Introduza um nome único para o espaço de nome, como _o seu nome-retail-store-store-analysis_. O sistema verifica se este nome está disponível.
    * Escolha o nível de preços **básicos.**
    * Selecione a mesma **Subscrição** que usou para criar a sua aplicação IoT Central.
    * Selecione o grupo de recursos **de análise de lojas de retalho.**
    * Selecione o mesmo local que utilizou para a sua aplicação IoT Central.
    * Selecione **Criar**. Talvez tenha de esperar alguns minutos para que o sistema forme os recursos.
1. No portal, navegue para o grupo de recursos **de análise de lojas de retalho.** Aguarde pela conclusão da implementação. Poderá ser necessário selecionar **Refresh** para atualizar o estado de implementação. Também pode verificar o estado da criação de espaço de nome do centro do evento nas **Notificações.**
1. No grupo de recursos **de análise de lojas de retalho,** selecione o Espaço nome dos Centros de **Eventos**. Você vê a página inicial do seu espaço de **nome sem nome** do Event Hubs no portal.

Agora tem um Espaço de Nomes de Hubs de **Eventos,** pode criar um Hub de **Eventos** para usar com a sua aplicação IoT Central:

1. Na página inicial do seu Espaço nome **Hubs de Eventos** no portal, selecione **+ Event Hub**.
1. Na página **Create Event Hub,** introduza a _telemetria da loja_ como nome e, em seguida, selecione **Criar**.

Tem agora um hub de eventos que pode utilizar quando configura a exportação de dados a partir da sua aplicação IoT Central:

![Hub de eventos](./media/tutorial-in-store-analytics-visualize-insights/event-hub.png)

## <a name="configure-data-export"></a>Configurar a exportação de dados

Agora tem um hub de eventos, pode configurar a sua análise na loja - aplicação de **check-out** para exportar telemetria a partir dos dispositivos conectados. Os seguintes passos mostram-lhe como configurar a exportação:

1. Inscreva-se na sua **aplicação In-store - checkout** IoT Central.
1. Selecione **A exportação** de Dados no painel esquerdo.
1. Selecione **New > Azure Event Hubs**.
1. Insira _a exportação de telemetria_ como o nome do **visor**.
1. Selecione o espaço de nome do Seu Centro de **Eventos**.
1. Selecione o centro de eventos **de telemetria da loja.**
1. Desligue **os modelos** de dispositivos e **dispositivos** na secção **Dados para exportação.**
1. Selecione **Guardar**.

A exportação de dados pode demorar alguns minutos a começar a enviar telemetria para o seu centro de eventos. Pode ver o estado da exportação na página de exportações de **Dados:**

![Configuração contínua da exportação de dados](./media/tutorial-in-store-analytics-visualize-insights/export-configuration.png)

## <a name="create-the-power-bi-datasets"></a>Criar os conjuntos de dados Power BI

O seu painel power bi apresentará dados da sua aplicação de monitorização de retalho. Nesta solução, utiliza conjuntos de dados de streaming Power BI como fonte de dados para o painel de instrumentos Power BI. Nesta secção, define o esquema dos conjuntos de dados de streaming para que a aplicação lógica possa reencaminhar dados do centro de eventos. Os seguintes passos mostram como criar dois conjuntos de dados de streaming para os sensores ambientais e um conjunto de dados de streaming para o sensor de ocupação:

1. Inicie sessão na sua conta do **Power BI**.
1. Selecione **Espaços de Trabalho**e, em seguida, selecione Criar um espaço de **trabalho**.
1. Na página **Criar um espaço de trabalho,** introduza _analítica na loja - check-out_ como o **nome Workspace**.
1. Percorra a parte inferior do **Welcome to the In-store analytics - checkout workspace** page, e selecione **Skip**.
1. Na página do espaço de trabalho, selecione **Criar > conjunto**de dados streaming .
1. Na nova página de conjunto de dados de **streaming,** escolha **API**, e, em seguida, selecione **Next**.
1. Introduza o _sensor da Zona 1_ como o nome **dataset**.
1. Introduza os três **Valores do stream** na tabela seguinte:

    | Nome do valor  | Tipo de valor |
    | ----------- | ---------- |
    | Carimbo de data/hora   | DateTime   |
    | Humidade    | Número     |
    | Temperatura | Número     |

1. Altere a análise de **dados históricos.**
1. Selecione **Criar** e, em seguida, **Feito**.
1. Crie outro conjunto de dados de streaming chamado **sensor Zone 2** com o mesmo esquema e configurações que o conjunto de dados de streaming do sensor Zone **1.**

Agora tem dois conjuntos de dados de streaming. A aplicação lógica irá direcionar a telemetria dos dois sensores ambientais ligados à sua análise na loja - aplicação de **check-out** para estes dois conjuntos de dados:

![Conjuntos de dados de zona](./media/tutorial-in-store-analytics-visualize-insights/dataset-1.png)

Esta solução utiliza um conjunto de dados de streaming para cada sensor porque não é possível aplicar filtros a dados de streaming no Power BI.

Também precisa de um conjunto de dados de streaming para a telemetria de ocupação:

1. Na página do espaço de trabalho, selecione **Criar > conjunto**de dados streaming .
1. Na nova página de conjunto de dados de **streaming,** escolha **API**, e, em seguida, selecione **Next**.
1. Introduza o _sensor de ocupação_ como nome **dataset**.
1. Introduza os cinco **Valores do stream** na tabela seguinte:

    | Nome do valor     | Tipo de valor |
    | -------------- | ---------- |
    | Carimbo de data/hora      | DateTime   |
    | Comprimento da fila 1 | Número     |
    | Comprimento da fila 2 | Número     |
    | Tempo de habitação 1   | Número     |
    | Tempo de habitação 2   | Número     |

1. Altere a análise de **dados históricos.**
1. Selecione **Criar** e, em seguida, **Feito**.

Tem agora um terceiro conjunto de dados de streaming que armazena valores a partir do sensor de ocupação simulado. Este sensor relata o comprimento da fila nos dois checkouts da loja, e quanto tempo os clientes esperam nestas filas:

![Conjunto de dados de ocupação](./media/tutorial-in-store-analytics-visualize-insights/dataset-2.png)

## <a name="create-a-logic-app"></a>Criar uma aplicação lógica

Nesta solução, a aplicação lógica lê telemetria a partir do centro de eventos, analisa os dados e envia-os depois para os conjuntos de dados de streaming power BI que criou.

Antes de criar a aplicação lógica, precisa dos IDs do dispositivo dos dois sensores RuuviTag que ligou à sua aplicação IoT Central na aplicação De análise Create in store no tutorial [Central Azure IoT:](./tutorial-in-store-analytics-create-app.md)

1. Inscreva-se na sua **aplicação In-store - checkout** IoT Central.
1. Selecione **Dispositivos** no painel esquerdo. Em seguida, selecione **RuuviTag**.
1. Tome nota das **iDs**do dispositivo . Na seguinte imagem, os IDs são **f5dcf4ac32e8** e **e29ffc8d5326:**

    ![IDs de dispositivo](./media/tutorial-in-store-analytics-visualize-insights/device-ids.png)

Os seguintes passos mostram-lhe como criar a aplicação lógica no portal Azure:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e selecione **Criar um recurso** na parte superior esquerda do ecrã.
1. Em **Search the Marketplace,** entre na _Logic App_, e depois prima **Enter**.
1. Na página da **Aplicação Lógica,** selecione **Criar**.
1. Na **aplicação lógica** criar página:
    * Introduza um nome único para a sua aplicação lógica, como _o seu nome-retail-store-store-analysis_.
    * Selecione a mesma **Subscrição** que usou para criar a sua aplicação IoT Central.
    * Selecione o grupo de recursos **de análise de lojas de retalho.**
    * Selecione o mesmo local que utilizou para a sua aplicação IoT Central.
    * Selecione **Criar**. Talvez tenha de esperar alguns minutos para que o sistema forme os recursos.
1. No portal Azure, navegue para a sua nova aplicação lógica.
1. Na página logic **Apps Designer,** percorra para baixo e selecione **Blank Logic App**.
1. Em **Conectores e gatilhos de pesquisa,** introduza Centros de _Eventos_.
1. Em **Gatilhos,** selecione **Quando os eventos estiverem disponíveis no Event Hub**.
1. Introduza _a telemetria da Loja_ como nome de **ligação**e selecione o seu espaço de nome dos Centros de **Eventos**.
1. Selecione a política **RootManageSharedAccess** e selecione **Criar**.
1. No **When eventos estão disponíveis na** ação Event Hub:
    * No **nome Event Hub,** selecione **store-telemetria**.
    * No **tipo de Conteúdo,** selecione **aplicação/json**.
    * Desloque o **intervalo** para três e a **frequência** em segundos
1. Selecione **Guardar** para salvar a sua aplicação lógica.

Para adicionar a lógica ao design da sua aplicação lógica, selecione **a visão de Código:**

1. Substitua-a `"actions": {},` com o seguinte JSON. Substitua os dois `[YOUR RUUVITAG DEVICE ID 1]` `[YOUR RUUVITAG DEVICE ID 2]` espaços reservados e os IDs que observou dos seus dois dispositivos RuuviTag:

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

1. Selecione **Guardar** e, em seguida, selecione **Designer** para ver a versão visual da lógica que adicionou:

    ![Design de aplicativológico](./media/tutorial-in-store-analytics-visualize-insights/logic-app.png)

1. Selecione **Switch by DeviceID** para expandir a ação. Em seguida, selecione **ambiente da Zona 1**, e selecione Adicionar uma **ação**.
1. Em **Search conectores e ações,** introduza **Power BI,** e, em seguida, prima **Enter**.
1. Selecione as linhas Adicionar para uma ação **de conjunto de dados (pré-visualização).**
1. Selecione **Iniciar sessão** e siga as instruções para iniciar sessão na sua conta Power BI.
1. Após o processo de início de sessão estar concluído, nas linhas Add para uma ação de **conjunto de dados:**
    * Selecione **Análise na loja - check-out** como o espaço de trabalho.
    * Selecione o **sensor da Zona 1** como conjunto de dados.
    * Selecione **RealTimeData** como a tabela.
    * **Selecione Adicionar novo parâmetro** e, em seguida, selecione os campos **timestamp,** **Humidade**e **Temperatura.**
    * Selecione o campo **timestamp** e, em seguida, selecione **x-opt-enqueuedtime** da lista de **conteúdos Dinâmicos.**
    * Selecione o campo **humidade** e, em seguida, selecione **Ver mais** ao lado da **Telemetria Parse**. Em seguida, selecione **humidade**.
    * Selecione o campo **Temperatura** e, em seguida, selecione **Ver mais** ao lado da **Telemetria Parse**. Em seguida, selecione **a temperatura**.
    * Selecione **Guardar** para guardar as alterações. A ação **ambiental da Zona** 1 ![parece a seguinte imagem: Ambiente da Zona 1](./media/tutorial-in-store-analytics-visualize-insights/zone-1-action.png)
1. Selecione a ação **ambiental da Zona 2** e selecione Adicionar uma **ação**.
1. Em **Search conectores e ações,** introduza **Power BI,** e, em seguida, prima **Enter**.
1. Selecione as linhas Adicionar para uma ação **de conjunto de dados (pré-visualização).**
1. Nas linhas Add a uma ação **dataset 2:**
    * Selecione **Análise na loja - check-out** como o espaço de trabalho.
    * Selecione o **sensor zona 2** como conjunto de dados.
    * Selecione **RealTimeData** como a tabela.
    * **Selecione Adicionar novo parâmetro** e, em seguida, selecione os campos **timestamp,** **Humidade**e **Temperatura.**
    * Selecione o campo **timestamp** e, em seguida, selecione **x-opt-enqueuedtime** da lista de **conteúdos Dinâmicos.**
    * Selecione o campo **humidade** e, em seguida, selecione **Ver mais** ao lado da **Telemetria Parse**. Em seguida, selecione **humidade**.
    * Selecione o campo **Temperatura** e, em seguida, selecione **Ver mais** ao lado da **Telemetria Parse**. Em seguida, selecione **a temperatura**.
    Selecione **Guardar** para guardar as alterações.  A ação **ambiental da Zona** 2 ![parece a seguinte imagem: Ambiente da Zona 2](./media/tutorial-in-store-analytics-visualize-insights/zone-2-action.png)
1. Selecione a ação **De ocupação** e, em seguida, selecione a **ação Switch by Interface ID.**
1. Selecione a ação **de interface do tempo dwell** e selecione Adicionar uma **ação**.
1. Em **Search conectores e ações,** introduza **Power BI,** e, em seguida, prima **Enter**.
1. Selecione as linhas Adicionar para uma ação **de conjunto de dados (pré-visualização).**
1. Nas linhas Add a uma ação de **conjunto de dados:**
    * Selecione **Análise na loja - check-out** como o espaço de trabalho.
    * **Selecione o Sensor de Ocupação** como conjunto de dados.
    * Selecione **RealTimeData** como a tabela.
    * **Selecione Adicionar novo parâmetro** e, em seguida, selecione os campos **Timestamp,** **Dwell Time 1**e **Dwell Time 2.**
    * Selecione o campo **timestamp** e, em seguida, selecione **x-opt-enqueuedtime** da lista de **conteúdos Dinâmicos.**
    * Selecione o campo **Dwell Time 1** e, em seguida, selecione **Ver mais** ao lado da **Telemetria Parse**. Em seguida, selecione **DwellTime1**.
    * Selecione o campo **Dwell Time 2** e, em seguida, selecione **Ver mais** ao lado da **Telemetria Parse**. Em seguida, selecione **DwellTime2**.
    * Selecione **Guardar** para guardar as alterações. A ação da interface do Tempo ![ **Dwell** parece a seguinte imagem: Ação de ocupação](./media/tutorial-in-store-analytics-visualize-insights/occupancy-action-1.png)
1. Selecione a ação de **interface People Count** e selecione Adicionar uma **ação**.
1. Em **Search conectores e ações,** introduza **Power BI,** e, em seguida, prima **Enter**.
1. Selecione as linhas Adicionar para uma ação **de conjunto de dados (pré-visualização).**
1. Nas linhas Add a uma ação de **conjunto de dados:**
    * Selecione **Análise na loja - check-out** como o espaço de trabalho.
    * **Selecione o Sensor de Ocupação** como conjunto de dados.
    * Selecione **RealTimeData** como a tabela.
    * **Selecione Adicionar novo parâmetro** e, em seguida, selecione os campos **timestamp,** **Queue Length 1**, e Queue **Length 2.**
    * Selecione o campo **timestamp** e, em seguida, selecione **x-opt-enqueuedtime** da lista de **conteúdos Dinâmicos.**
    * Selecione o campo **Queue Length 1** e, em seguida, selecione Ver **mais** ao lado da **Telemetria Parse**. Em seguida, selecione **contagem1**.
    * Selecione o campo **Queue Length 2** e, em seguida, selecione Ver **mais** ao lado da **Telemetria Parse**. Em seguida, selecione **contagem2**.
    * Selecione **Guardar** para guardar as alterações. A ação **de interface People** Count ![parece a seguinte imagem: Ação de ocupação](./media/tutorial-in-store-analytics-visualize-insights/occupancy-action-2.png)

A aplicação lógica funciona automaticamente. Para ver o estado de cada execução, navegue para a página **de visão geral** para a aplicação lógica no portal Azure:

## <a name="create-a-power-bi-dashboard"></a>Criar um dashboard Power BI

Agora tem telemetria a fluir da sua aplicação IoT Central através do seu centro de eventos. Em seguida, a sua aplicação lógica analisa as mensagens do hub do evento e adiciona-as a um conjunto de dados de streaming Power BI. Agora, pode criar um dashboard Power BI para visualizar a telemetria:

1. Inicie sessão na sua conta do **Power BI**.
1. Selecione **Workspaces > Análise na loja - checkout**.
1. Selecione **Criar > Dashboard**.
1. Introduza a análise da **Loja** como nome do painel de instrumentos e selecione **Criar**.

### <a name="add-line-charts"></a>Adicionar gráficos de linha

Adicione quatro azulejos de gráfico de linha para mostrar a temperatura e humidade dos dois sensores ambientais. Utilize a informação na tabela seguinte para criar os azulejos. Para adicionar cada azulejo, comece por **selecionar... (Mais opções) > Adicionar Azulejos**. Selecione Dados de **Streaming personalizados**e, em seguida, selecione **A seguir:**

| Definição | Gráfico #1 | Gráfico #2 | Gráfico #3 | Gráfico #4 |
| ------- | -------- | -------- | -------- | -------- |
| Conjunto de dados | Sensor da Zona 1 | Sensor da Zona 1 | Sensor da Zona 2 | Sensor da Zona 2 |
| Tipo de visualização | Gráfico de linhas | Gráfico de linhas | Gráfico de linhas | Gráfico de linhas |
| Eixo | Carimbo de data/hora | Carimbo de data/hora | Carimbo de data/hora | Carimbo de data/hora |
| Valores | Temperatura | Humidade | Temperatura | Humidade |
| Janela do tempo | 60 minutos | 60 minutos | 60 minutos | 60 minutos |
| Título | Temperatura (1 hora) | Humidade (1 hora) | Temperatura (1 hora) | Humidade (1 hora) |
| Subtítulo | Zona 1 | Zona 1 | Zona 2 | Zona 2 |

A imagem a seguir mostra as definições para o primeiro gráfico:

![Definições do gráfico de linhas](./media/tutorial-in-store-analytics-visualize-insights/line-chart.png)

### <a name="add-cards-to-show-environmental-data"></a>Adicione cartões para mostrar dados ambientais

Adicione quatro telhas de cartão para mostrar os valores de temperatura e humidade mais recentes dos dois sensores ambientais. Utilize a informação na tabela seguinte para criar os azulejos. Para adicionar cada azulejo, comece por **selecionar... (Mais opções) > Adicionar Azulejos**. Selecione Dados de **Streaming personalizados**e, em seguida, selecione **A seguir:**

| Definição | #1 de cartão | #2 de cartão | #3 de cartão | #4 de cartão |
| ------- | ------- | ------- | ------- | ------- |
| Conjunto de dados | Sensor da Zona 1 | Sensor da Zona 1 | Sensor da Zona 2 | Sensor da Zona 2 |
| Tipo de visualização | Cartão | Cartão | Cartão | Cartão |
| Campos | Temperatura | Humidade | Temperatura | Humidade |
| Título | Temperatura (F) | Humidade (%) | Temperatura (F) | Humidade (%) |
| Subtítulo | Zona 1 | Zona 1 | Zona 2 | Zona 2 |

A seguinte imagem mostra as definições para a primeira carta:

![Definições do cartão](./media/tutorial-in-store-analytics-visualize-insights/card-settings.png)

### <a name="add-tiles-to-show-checkout-occupancy-data"></a>Adicione azulejos para mostrar dados de ocupação de check-out

Adicione quatro azulejos de cartão para mostrar o comprimento da fila e tempo de permanência para os dois checkouts na loja. Utilize a informação na tabela seguinte para criar os azulejos. Para adicionar cada azulejo, comece por **selecionar... (Mais opções) > Adicionar Azulejos**. Selecione Dados de **Streaming personalizados**e, em seguida, selecione **A seguir:**

| Definição | #1 de cartão | #2 de cartão | #3 de cartão | #4 de cartão |
| ------- | ------- | ------- | ------- | ------- |
| Conjunto de dados | Sensor de ocupação | Sensor de ocupação | Sensor de ocupação | Sensor de ocupação |
| Tipo de visualização | Gráfico de colunas agrupadas | Gráfico de colunas agrupadas | Medidor | Medidor |
| Eixo    | Carimbo de data/hora | Carimbo de data/hora | N/D | N/D |
| Valor | Tempo de habitação 1 | Tempo de habitação 2 | Comprimento da fila 1 | Comprimento da fila 2 |
| Janela do tempo | 60 minutos | 60 minutos |  N/D | N/D |
| Título | Tempo de habitação | Tempo de habitação | Comprimento da Fila | Comprimento da Fila |
| Subtítulo | Checkout 1 | Checkout 2 | Checkout 1 | Checkout 2 |

Redimensione e reorganize os azulejos no seu painel de instrumentos para parecer a seguinte imagem:

![Dashboard do Power BI](./media/tutorial-in-store-analytics-visualize-insights/pbi-dashboard.png)

Pode adicionar alguns recursos gráficos adicionais para personalizar ainda mais o painel de instrumentos:

![Dashboard do Power BI](./media/tutorial-in-store-analytics-visualize-insights/pbi-dashboard-graphics.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se tiver terminado a sua aplicação IoT Central, pode eliminá-la insindo na aplicação e navegando na página de Definições de **Aplicações** na secção **Administração.**

Se quiser manter a aplicação mas reduzir os custos associados, desative a exportação de dados que está a enviar telemetria para o seu centro de eventos.

Pode eliminar o hub de eventos e a aplicação lógica no portal Azure, eliminando o grupo de recursos chamado **retail store-analysis**.

Pode eliminar os conjuntos de dados e o painel de instrumentos power BI, eliminando o espaço de trabalho da página de definições do Power BI para o espaço de trabalho.

## <a name="next-steps"></a>Passos Seguintes

Estes três tutoriais mostraram-lhe uma solução de ponta a ponta que utiliza a análise da loja - modelo de aplicação IoT Central de **checkout.** Ligou dispositivos à aplicação, usou o IoT Central para monitorizar os dispositivos e utilizou o Power BI para construir um dashboard para visualizar insights da telemetria do dispositivo. Um próximo passo recomendado é explorar um dos outros modelos de aplicação Da IoT Central:

> [!div class="nextstepaction"]
> * [Criar soluções para o setor energético com o IoT Central](../energy/overview-iot-central-energy.md)
> * [Criar soluções para a administração pública com o IoT Central](../government/overview-iot-central-government.md)
> * [Criar soluções de cuidados de saúde com o IoT Central](../healthcare/overview-iot-central-healthcare.md)
