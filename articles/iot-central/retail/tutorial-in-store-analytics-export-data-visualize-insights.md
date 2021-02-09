---
title: Tutorial - Dados de exportação e visualização de insights no Azure IoT Central
description: Neste tutorial, aprenda a exportar dados da IoT Central e visualize insights num painel power BI.
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
ms.openlocfilehash: 2695b34745ff02d55f18cebbe87a468f807ca77a
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831693"
---
# <a name="tutorial-export-data-from-azure-iot-central-and-visualize-insights-in-power-bi"></a>Tutorial: Dados de exportação da Azure IoT Central e visualizar insights no Power BI


Nos dois tutoriais anteriores, criou e personalizou uma aplicação IoT Central utilizando o modelo **de aplicação de check-out in-store.** Neste tutorial, configura a sua aplicação IoT Central para exportar telemetria recolhida dos dispositivos. Em seguida, utilize o Power BI para criar um dashboard personalizado para o gestor da loja visualizar as perceções derivadas da telemetria.

Neste tutorial, vai aprender a:
> [!div class="checklist"]
> * Configure uma aplicação IoT Central para exportar telemetria para um centro de eventos.
> * Utilize as Aplicações Lógicas para enviar dados de um centro de eventos para um conjunto de dados de streaming Power BI.
> * Crie um painel Power BI para visualizar dados no conjunto de dados de streaming.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Para completar os dois tutoriais anteriores, [crie uma aplicação de análise na loja no Azure IoT Central](./tutorial-in-store-analytics-create-app.md) e [personalize o painel de instrumentos do operador e gere os dispositivos no Azure IoT Central](./tutorial-in-store-analytics-customize-dashboard.md).
* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Uma conta do Power BI. Se não tiver uma conta Power BI, inscreva-se para um [teste Power BI Pro gratuito](https://app.powerbi.com/signupredirect?pbi_source=web) antes de começar.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de criar o seu centro de eventos e aplicação lógica, precisa de criar um grupo de recursos para os gerir. O grupo de recursos deve estar no mesmo local que a sua **aplicação In-Store Analytics - checkout** IoT Central. Para criar um grupo de recursos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Na navegação à esquerda, selecione **grupos de recursos.** Em seguida, selecione **Adicionar**.
1. Para **subscrição**, selecione o nome da subscrição Azure que usou para criar a sua aplicação IoT Central.
1. Para o nome do **grupo Recursos,** insira _a análise da loja de retalho_*.
1. Para a **Região**, selecione a mesma região que escolheu para a aplicação IoT Central.
1. Selecione **Rever + Criar**.
1. Na página **'Rever + Criar',** selecione **Criar**.

Tem agora um grupo de recursos chamado **retail-store-analysis** na sua subscrição.

## <a name="create-an-event-hub"></a>Criar um hub de eventos

Antes de configurar o pedido de monitorização do retalho para exportar telemetria, precisa de criar um centro de eventos para receber os dados exportados. Os seguintes passos mostram-lhe como criar o seu centro de eventos:

1. No portal Azure, **selecione Criar um recurso** na parte superior esquerda do ecrã.
1. Em **Search the Marketplace**, insira Os Centros de _Eventos_ e, em seguida, prima **Enter**.
1. Na página **'Centros de Eventos',** selecione **Criar**.
1. Na página **Create Namespace,** tome os seguintes passos:
    * Insira um nome único para o espaço de nome, como _o seu nome-retail-store-analysis_. O sistema verifica se este nome está disponível.
    * Escolha **o** nível básico de preços.
    * Selecione a mesma **Subscrição** que usou para criar a sua aplicação IoT Central.
    * Selecione o grupo de recursos **de análise de lojas de retalho.**
    * Selecione o mesmo local que usou para a sua aplicação IoT Central.
    * Selecione **Criar**. Talvez tenha de esperar alguns minutos para que o sistema ad proteja os recursos.
1. No portal, navegue para o grupo de recursos **de análise de lojas de retalho.** Aguarde pela conclusão da implementação. Pode ter de selecionar **Refresh** para atualizar o estado de implementação. Também pode verificar o estado da criação do espaço de nome do centro de eventos nas **Notificações.**
1. No grupo de recursos **de análise de lojas de retalho,** selecione o **Event Hubs Namespace**. Você vê a página inicial para o seu **espaço de nomes de centros de eventos** no portal.

Agora tem um **Espaço de Nomes de Centros de Eventos,** pode criar um **Centro de Eventos** para utilizar com a sua aplicação IoT Central:

1. Na página inicial do seu **Espaço de Nomes de Centros de Eventos** no portal, selecione **+ Centro de Eventos**.
1. Na página **'Criar Centro de** Eventos', _insira a telemetria da loja_ como nome e, em seguida, selecione **Criar**.

Você agora tem um centro de eventos que você pode usar quando configurar a exportação de dados a partir da sua aplicação IoT Central:

![Hub de eventos](./media/tutorial-in-store-analytics-visualize-insights/event-hub.png)

## <a name="configure-data-export"></a>Configure exportação de dados

Agora que tem um centro de eventos, pode configurar a sua **analítica in-store -** aplicação de check-out para exportar telemetria a partir dos dispositivos conectados. Os seguintes passos mostram-lhe como configurar a exportação:

1. Inscreva-se na sua **analítica in-store - aplicação** IoT Central de check-out.
1. Selecione **Exportação de dados** no painel esquerdo.
1. Selecione **New > Azure Event Hubs**.
1. Introduza _a exportação de telemetria_ como **o nome de exibição**.
1. Selecione o seu **espaço de nomes 'Hubs' de eventos**.
1. Selecione o centro de eventos **de telemetria da loja.**
1. Desligue **os modelos de dispositivos** e **dispositivos** na secção **data para exportação.**
1. Selecione **Guardar**.

A exportação de dados pode demorar alguns minutos a começar a enviar telemetria para o seu centro de eventos. Pode ver o estado da exportação na página **de exportação de dados:**

![Configuração contínua da exportação de dados](./media/tutorial-in-store-analytics-visualize-insights/export-configuration.png)

## <a name="create-the-power-bi-datasets"></a>Criar os conjuntos de dados Power BI

O seu painel Power BI apresentará dados da sua aplicação de monitorização de retalho. Nesta solução, utiliza conjuntos de dados de streaming Power BI como fonte de dados para o painel Power BI. Nesta secção, define-se o esquema dos conjuntos de dados de streaming para que a aplicação lógica possa encaminhar dados do centro de eventos. Os seguintes passos mostram como criar dois conjuntos de dados de streaming para os sensores ambientais e um conjunto de dados de streaming para o sensor de ocupação:

1. Inicie sessão na sua conta do **Power BI**.
1. Selecione **espaços de trabalho** e, em seguida, selecione Criar um espaço de **trabalho**.
1. Na página **Criar um espaço de trabalho,** _insira a análise na loja - check-out_ como o **nome workspace**.
1. Percorra a parte inferior do **Welcome para a página de check-out - check-out workspace** e selecione **Skip**.
1. Na página do espaço de trabalho, **selecione Criar > conjunto de dados de streaming**.
1. Na nova página **de conjunto de dados de streaming,** escolha **API** e, em seguida, selecione **Seguinte**.
1. Introduza _o sensor zona 1_ como **o nome dataset**.
1. Introduza os três **Valores a partir do fluxo** na tabela seguinte:

    | Nome do valor  | Tipo de valor |
    | ----------- | ---------- |
    | CarimboDeDataEHora   | DateTime   |
    | Humidade    | Número     |
    | Temperatura | Número     |

1. Altere **a análise de dados históricos.**
1. Selecione **Criar** e, em seguida, **Feito**.
1. Crie outro conjunto de dados de streaming chamado **sensor Zone 2** com o mesmo esquema e definições que o conjunto de dados de streaming de **sensores da Zona 1.**

Tem agora dois conjuntos de dados de streaming. A aplicação lógica irá direcionar a telemetria dos dois sensores ambientais ligados à sua análise na loja - aplicação **de check-out** para estes dois conjuntos de dados:

![Conjuntos de dados de zona](./media/tutorial-in-store-analytics-visualize-insights/dataset-1.png)

Esta solução utiliza um conjunto de dados de streaming para cada sensor porque não é possível aplicar filtros em dados de streaming no Power BI.

Também precisa de um conjunto de dados de streaming para a telemetria de ocupação:

1. Na página do espaço de trabalho, **selecione Criar > conjunto de dados de streaming**.
1. Na nova página **de conjunto de dados de streaming,** escolha **API** e, em seguida, selecione **Seguinte**.
1. Introduza _o sensor de ocupação_ como o nome **dataset**.
1. Introduza os cinco **Valores do stream** na tabela seguinte:

    | Nome do valor     | Tipo de valor |
    | -------------- | ---------- |
    | CarimboDeDataEHora      | DateTime   |
    | Comprimento da fila 1 | Número     |
    | Comprimento da fila 2 | Número     |
    | Tempo de Morador 1   | Número     |
    | Tempo de Morador 2   | Número     |

1. Altere **a análise de dados históricos.**
1. Selecione **Criar** e, em seguida, **Feito**.

Tem agora um terceiro conjunto de dados de streaming que armazena valores a partir do sensor de ocupação simulado. Este sensor relata o comprimento da fila nos dois checkouts da loja, e quanto tempo os clientes esperam nestas filas:

![Conjunto de dados de ocupação](./media/tutorial-in-store-analytics-visualize-insights/dataset-2.png)

## <a name="create-a-logic-app"></a>Criar uma aplicação lógica

Nesta solução, a aplicação lógica lê a telemetria do centro de eventos, analisa os dados e envia-os para os conjuntos de dados de streaming Power BI que criou.

Antes de criar a aplicação lógica, precisa dos IDs do dispositivo dos dois sensores RuuviTag que ligou à sua aplicação IoT Central [na aplicação Create in-store analytics no tutorial Azure IoT Central:](./tutorial-in-store-analytics-create-app.md)

1. Inscreva-se na sua **analítica in-store - aplicação** IoT Central de check-out.
1. Selecione **Dispositivos** no painel esquerdo. Em seguida, **selecione RuuviTag**.
1. Tome nota dos **IDs do dispositivo.** Na imagem seguinte, os IDs são **f5dcf4ac32e8** e **e29ffc8d5326:**

    ![IDs do dispositivo](./media/tutorial-in-store-analytics-visualize-insights/device-ids.png)

Os seguintes passos mostram-lhe como criar a aplicação lógica no portal Azure:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e selecione **Criar um recurso** na parte superior esquerda do ecrã.
1. Em **Search the Marketplace**, insira a Logic _App_ e, em seguida, prima **Enter**.
1. Na página **'Aplicação Lógica',** selecione **Criar**.
1. Na página de criação da **Aplicação Lógica:**
    * Insira um nome único para a sua aplicação lógica, como _o seu nome-retail-store-analysis_.
    * Selecione a mesma **Subscrição** que usou para criar a sua aplicação IoT Central.
    * Selecione o grupo de recursos **de análise de lojas de retalho.**
    * Selecione o mesmo local que usou para a sua aplicação IoT Central.
    * Selecione **Criar**. Talvez tenha de esperar alguns minutos para que o sistema ad proteja os recursos.
1. No portal Azure, navegue para a sua nova aplicação lógica.
1. Na página **de Design de Aplicações Lógicas,** desloque-se para baixo e selecione Blank Logic **App**.
1. Em **Conectores e gatilhos** de pesquisa, insira _os Centros de Eventos_.
1. Em **Triggers**, selecione **Quando os eventos estiverem disponíveis no Event Hub**.
1. Introduza _a telemetria da Loja_ como o nome De **Ligação** e selecione o **seu Espaço de Nomes de Centros de Eventos**.
1. Selecione a política **RootManageSharedAccess** e selecione **Criar**.
1. No **When os eventos estão disponíveis na** ação Event Hub:
    * No **nome Event Hub**, selecione **loja-telemetria**.
    * No **tipo de conteúdo,** selecione **aplicação/json**.
    * Definir o **Intervalo** para três e a **Frequência** para segundos
1. Selecione **Guardar** para guardar a sua aplicação lógica.

Para adicionar a lógica ao design de aplicações lógicas, selecione **Code view**:

1. `"actions": {},`Substitua-o pelo seguinte JSON. Substitua os dois espaços reservados `[YOUR RUUVITAG DEVICE ID 1]` e `[YOUR RUUVITAG DEVICE ID 2]` pelos IDs que notou dos seus dois dispositivos RuuviTag:

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

1. **Selecione Save** e, em seguida, selecione **Designer** para ver a versão visual da lógica que acrescentou:

    ![Design de aplicativos lógico](./media/tutorial-in-store-analytics-visualize-insights/logic-app.png)

1. Selecione **Switch by DeviceID** para expandir a ação. Em seguida, selecione **ambiente zona 1** e selecione **Adicione uma ação**.
1. Em **Pesquisar conectores e ações**, insira **Power BI** e, em seguida, prima **Enter**.
1. Selecione as **linhas Adicionar a uma ação de conjunto de dados (pré-visualização).**
1. Selecione **Iniciar sôm e** siga as instruções para iniciar scontabilidade na sua conta Power BI.
1. Após o processo de início de sposição estar concluído, nas **linhas Add a uma ação de conjunto de dados:**
    * Selecione **In-store analytics - check-out** como o espaço de trabalho.
    * Selecione **o sensor Zona 1** como conjunto de dados.
    * Selecione **RealTimeData** como tabela.
    * **Selecione Adicione novo parâmetro** e, em seguida, selecione os campos **Timestamp,** **Humidade** e **Temperatura.**
    * Selecione o campo **Timestamp** e, em seguida, **selecione x-opt-enqueuedtime** da lista de **conteúdos Dynamic.**
    * Selecione o campo **de humidade** e, em seguida, selecione **Ver mais** ao lado da **Parse Telemetria**. Em seguida, selecione **a humidade**.
    * Selecione o campo **Temperatura** e, em seguida, selecione **Ver mais** ao lado da **Telemetria Parse**. Em seguida, selecione **a temperatura**.
    * Selecione **Guardar** para guardar as alterações. A ação **ambiental da Zona 1** parece a seguinte imagem: ![ Ambiente zona 1](./media/tutorial-in-store-analytics-visualize-insights/zone-1-action.png)
1. Selecione a ação **ambiental zona 2** e selecione **Adicione uma ação**.
1. Em **Pesquisar conectores e ações**, insira **Power BI** e, em seguida, prima **Enter**.
1. Selecione as **linhas Adicionar a uma ação de conjunto de dados (pré-visualização).**
1. Nas **linhas Adicionar a uma ação do conjunto de dados 2:**
    * Selecione **In-store analytics - check-out** como o espaço de trabalho.
    * Selecione **o sensor Zona 2** como conjunto de dados.
    * Selecione **RealTimeData** como tabela.
    * **Selecione Adicione novo parâmetro** e, em seguida, selecione os campos **Timestamp,** **Humidade** e **Temperatura.**
    * Selecione o campo **Timestamp** e, em seguida, **selecione x-opt-enqueuedtime** da lista de **conteúdos Dynamic.**
    * Selecione o campo **de humidade** e, em seguida, selecione **Ver mais** ao lado da **Parse Telemetria**. Em seguida, selecione **a humidade**.
    * Selecione o campo **Temperatura** e, em seguida, selecione **Ver mais** ao lado da **Telemetria Parse**. Em seguida, selecione **a temperatura**.
    Selecione **Guardar** para guardar as alterações.  A ação **ambiental da Zona 2** parece a seguinte imagem: ![ Ambiente zona 2](./media/tutorial-in-store-analytics-visualize-insights/zone-2-action.png)
1. Selecione a ação **de Ocupação** e, em seguida, selecione a ação **Switch by Interface ID.**
1. Selecione a ação **da interface Dwell Time** e selecione Adicione uma **ação**.
1. Em **Pesquisar conectores e ações**, insira **Power BI** e, em seguida, prima **Enter**.
1. Selecione as **linhas Adicionar a uma ação de conjunto de dados (pré-visualização).**
1. Nas **linhas Adicionar a uma ação de conjunto de dados:**
    * Selecione **In-store analytics - check-out** como o espaço de trabalho.
    * Selecione **o Sensor de Ocupação** como conjunto de dados.
    * Selecione **RealTimeData** como tabela.
    * **Selecione Adicione novo parâmetro** e, em seguida, selecione os campos **Timestamp**, **Dwell Time 1** e **Dwell Time 2.**
    * Selecione o campo **Timestamp** e, em seguida, **selecione x-opt-enqueuedtime** da lista de **conteúdos Dynamic.**
    * Selecione o campo **Dwell Time 1** e, em seguida, selecione **Ver mais** ao lado **da Telemetria Parse**. Em seguida, selecione **DwellTime1**.
    * Selecione o campo **Dwell Time 2** e, em seguida, selecione **Ver mais** ao lado **da Telemetria Parse**. Em seguida, selecione **DwellTime2**.
    * Selecione **Guardar** para guardar as alterações. A ação **da interface Dwell Time** parece a seguinte imagem: Screenshot que mostra a ação ![ "Dwell Time interface".](./media/tutorial-in-store-analytics-visualize-insights/occupancy-action-1.png)
1. Selecione a ação **de interface People Count** e selecione Adicione uma **ação**.
1. Em **Pesquisar conectores e ações**, insira **Power BI** e, em seguida, prima **Enter**.
1. Selecione as **linhas Adicionar a uma ação de conjunto de dados (pré-visualização).**
1. Nas **linhas Adicionar a uma ação de conjunto de dados:**
    * Selecione **In-store analytics - check-out** como o espaço de trabalho.
    * Selecione **o Sensor de Ocupação** como conjunto de dados.
    * Selecione **RealTimeData** como tabela.
    * **Selecione Adicione novo parâmetro** e, em seguida, selecione os campos **Timestamp,** **Queue Length 1** e **Queue Length 2.**
    * Selecione o campo **Timestamp** e, em seguida, **selecione x-opt-enqueuedtime** da lista de **conteúdos Dynamic.**
    * Selecione o campo **Comprimento da Fila 1** e, em seguida, selecione **Ver mais** ao lado da **Telemetria Parse**. Em seguida, selecione **a contagem1**.
    * Selecione o campo **Comprimento da Fila 2** e, em seguida, selecione **Ver mais** ao lado **da Telemetria Parse**. Em seguida, selecione **count2**.
    * Selecione **Guardar** para guardar as alterações. A ação **da interface People Count** parece a seguinte imagem: ![ ação de ocupação](./media/tutorial-in-store-analytics-visualize-insights/occupancy-action-2.png)

A aplicação lógica é executado automaticamente. Para ver o estado de cada execução, navegue para a página **geral** para a aplicação lógica no portal Azure:

## <a name="create-a-power-bi-dashboard"></a>Criar um dashboard do Power BI

Agora tem telemetria fluindo da sua aplicação IoT Central através do seu centro de eventos. Em seguida, a sua aplicação lógica analisa as mensagens do centro do evento e adiciona-as a um conjunto de dados de streaming Power BI. Agora, pode criar um painel Power BI para visualizar a telemetria:

1. Inicie sessão na sua conta do **Power BI**.
1. Selecione **Workspaces > Análises in-store - check-out**.
1. Selecione **Criar > Dashboard**.
1. **Insira a análise da Loja** como o nome do painel de instrumentos e selecione **Criar**.

### <a name="add-line-charts"></a>Adicionar gráficos de linha

Adicione quatro telhas de gráfico de linha para mostrar a temperatura e a humidade dos dois sensores ambientais. Utilize as informações na tabela seguinte para criar os azulejos. Para adicionar cada azulejo, comece por selecionar **... (Mais opções) > Adicionar Azulejo.** Selecione **dados de streaming personalizados** e, em seguida, selecione **Seguinte**:

| Definição | #1 gráfico | #2 gráfico | #3 gráfico | #4 gráfico |
| ------- | -------- | -------- | -------- | -------- |
| Conjunto de dados | Sensor zona 1 | Sensor zona 1 | Sensor zona 2 | Sensor zona 2 |
| Tipo de visualização | Gráfico de linhas | Gráfico de linhas | Gráfico de linhas | Gráfico de linhas |
| Eixo | CarimboDeDataEHora | CarimboDeDataEHora | CarimboDeDataEHora | CarimboDeDataEHora |
| Valores | Temperatura | Humidade | Temperatura | Humidade |
| Janela do tempo | 60 minutos | 60 minutos | 60 minutos | 60 minutos |
| Título | Temperatura (1 hora) | Humidade (1 hora) | Temperatura (1 hora) | Humidade (1 hora) |
| Subtítulo | Zona 1 | Zona 1 | Zona 2 | Zona 2 |

A imagem a seguir mostra as definições para o primeiro gráfico:

![Definições do gráfico de linhas](./media/tutorial-in-store-analytics-visualize-insights/line-chart.png)

### <a name="add-cards-to-show-environmental-data"></a>Adicionar cartões para mostrar dados ambientais

Adicione quatro telhas de cartão para mostrar os valores mais recentes de temperatura e humidade dos dois sensores ambientais. Utilize as informações na tabela seguinte para criar os azulejos. Para adicionar cada azulejo, comece por selecionar **... (Mais opções) > Adicionar Azulejo.** Selecione **dados de streaming personalizados** e, em seguida, selecione **Seguinte**:

| Definição | #1 de cartão | #2 de cartões | #3 de cartões | #4 de cartões |
| ------- | ------- | ------- | ------- | ------- |
| Conjunto de dados | Sensor zona 1 | Sensor zona 1 | Sensor zona 2 | Sensor zona 2 |
| Tipo de visualização | Cartão | Cartão | Cartão | Cartão |
| Campos | Temperatura | Humidade | Temperatura | Humidade |
| Título | Temperatura (F) | Humidade (%) | Temperatura (F) | Humidade (%) |
| Subtítulo | Zona 1 | Zona 1 | Zona 2 | Zona 2 |

A imagem que se segue mostra as definições da primeira carta:

![Configurações de cartões](./media/tutorial-in-store-analytics-visualize-insights/card-settings.png)

### <a name="add-tiles-to-show-checkout-occupancy-data"></a>Adicione azulejos para mostrar dados de ocupação de check-out

Adicione quatro telhas de cartão para mostrar o comprimento da fila e tempo de permanência para os dois checkouts na loja. Utilize as informações na tabela seguinte para criar os azulejos. Para adicionar cada azulejo, comece por selecionar **... (Mais opções) > Adicionar Azulejo.** Selecione **dados de streaming personalizados** e, em seguida, selecione **Seguinte**:

| Definição | #1 de cartão | #2 de cartões | #3 de cartões | #4 de cartões |
| ------- | ------- | ------- | ------- | ------- |
| Conjunto de dados | Sensor de ocupação | Sensor de ocupação | Sensor de ocupação | Sensor de ocupação |
| Tipo de visualização | Gráfico de colunas agrupadas | Gráfico de colunas agrupadas | Medidor | Medidor |
| Eixo    | CarimboDeDataEHora | CarimboDeDataEHora | N/D | N/D |
| Valor | Tempo de Morador 1 | Tempo de Morador 2 | Comprimento da fila 1 | Comprimento da fila 2 |
| Janela do tempo | 60 minutos | 60 minutos |  N/D | N/D |
| Título | Tempo de Mora | Tempo de Mora | Comprimento da Fila | Comprimento da Fila |
| Subtítulo | Check-out 1 | Check-out 2 | Check-out 1 | Check-out 2 |

Redimensione e reorganize os azulejos do seu painel para parecer a seguinte imagem:

![Screenshot que mostra o painel Power B I com azulejos redimensionados e reorganizados.](./media/tutorial-in-store-analytics-visualize-insights/pbi-dashboard.png)

Pode adicionar alguns recursos gráficos adicionais para personalizar ainda mais o painel de instrumentos:

![Dashboard do Power BI](./media/tutorial-in-store-analytics-visualize-insights/pbi-dashboard-graphics.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se terminou com a sua aplicação IoT Central, pode eliminá-la insinando-a na aplicação e navegando na página Definições de **Aplicação** na secção **Administração.**

Se quiser manter a aplicação mas reduzir os custos associados, desative a exportação de dados que está a enviar telemetria para o seu centro de eventos.

Pode eliminar o centro de eventos e a aplicação lógica no portal Azure, eliminando o grupo de recursos chamado **retail-store-analysis**.

Pode eliminar os conjuntos de dados power BI e o dashboard eliminando o espaço de trabalho da página de definições Power BI para o espaço de trabalho.

## <a name="next-steps"></a>Passos Seguintes

Estes três tutoriais mostraram-lhe uma solução de ponta a ponta que utiliza o modelo de aplicação IoT Central **check-out** . Ligou os dispositivos à aplicação, utilizou o IoT Central para monitorizar os dispositivos e utilizou o Power BI para construir um dashboard para visualizar insights da telemetria do dispositivo. Um próximo passo recomendado é explorar um dos outros modelos de aplicação da IoT Central:

> [!div class="nextstepaction"]
> [Criar soluções para o setor energético com o IoT Central](../energy/overview-iot-central-energy.md)
