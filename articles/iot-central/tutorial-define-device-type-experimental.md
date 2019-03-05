---
title: Definir um novo tipo de dispositivo no Azure IoT Central | Microsoft Docs
description: Este tutorial mostra-lhe como definir, enquanto construtor, um novo tipo de dispositivo na aplicação do Azure IoT Central. O utilizador define a telemetria, o estado, as propriedades e as definições para o seu tipo.
author: dominicbetts
ms.author: dobett
ms.date: 01/28/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 894e4c3fb47a8d936c4e27e2c8c164cc43f3d20e
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57317242"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application-new-ui-design"></a>Tutorial: Definir um novo tipo de dispositivo na sua aplicação do Azure IoT Central (design de nova interface do Usuário)

Este tutorial mostra-lhe como utilizar, enquanto construtor, um modelo de dispositivo para definir um novo tipo de dispositivo na aplicação do Azure IoT Central. Um modelo do dispositivo define a telemetria, o estado, as propriedades e as definições para o tipo de dispositivo.

Para poder testar a sua aplicação antes de ligar a um dispositivo real, o IoT Central gera um dispositivo simulado a partir do modelo de dispositivo ao criá-lo.

Neste tutorial, vai criar um modelo de dispositivo de **Ar Condicionado Ligado**. Um dispositivo de ar condicionado ligado:

* Envia telemetria, como temperatura e humidade.
* Estado de relatórios, como está ativada ou desativada.
* Tem propriedades do dispositivo como a versão de firmware e o número de série.
* Tem definições como a temperatura ideal.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um novo modelo de dispositivo
> * Adicionar telemetria ao seu dispositivo
> * Ver telemetria simulada
> * Definir medição de eventos
> * Ver eventos simulados
> * Definir medição de estado
> * Ver estado simulado
> * Definições e propriedades de utilização
> * Utilizar comandos
> * Ver o seu dispositivo simulado no dashboard

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma aplicação do Azure IoT Central. Se concluiu o início rápido [Criar uma aplicação do Azure IoT Central](quick-deploy-iot-central-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json), pode reutilizar a aplicação que criou no início rápido. Caso contrário, execute os seguintes passos para criar uma aplicação do Azure IoT Central vazia:

1. Navegue para a página do [Gestor de Aplicações](https://aka.ms/iotcentral) do Azure IoT Central.

2. Introduza o endereço de e-mail e a palavra-passe que utiliza para aceder à sua subscrição do Azure:

    ![Introduza a sua conta da organização](./media/tutorial-define-device-type-experimental/sign-in.png)

3. Para começar a criar uma nova aplicação do Azure IoT Central, selecione **nova aplicação**:

    ![Página do Gestor de Aplicações do Azure IoT Central](./media/tutorial-define-device-type-experimental/iotcentralhome.png)

4. Para criar uma nova aplicação do Azure IoT Central:
    
    * Escolha **Avaliação**. Não precisa de uma subscrição do Azure para criar uma aplicação de Avaliação.
    
       Para obter mais informações sobre as subscrições e diretórios, veja [criar uma aplicação de início rápido](quick-deploy-iot-central-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
    
    * Selecione **Aplicação Personalizada**.
    
    * Opcionalmente, pode escolher um nome de aplicação amigável, como **Ares Condicionados Contoso**. O Azure IoT Central gera um prefixo de URL exclusivo para si. Pode alterar este prefixo de URL para algo mais memorável.
    
    * Selecione **Criar**.

    ![Página da Aplicação do Azure IoT Central](./media/tutorial-define-device-type-experimental/iotcentralcreate.png)

    Para obter mais informações, veja [criar um início rápido da aplicação](quick-deploy-iot-central-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="create-a-new-custom-device-template"></a>Criar um novo modelo de dispositivo personalizado

Como construtor, pode criar e editar os modelos de dispositivos na sua aplicação. Quando cria um modelo de dispositivo, o Azure IoT Central gera um dispositivo simulado a partir do modelo. O dispositivo simulado gera telemetria, que permite que teste o comportamento do seu aplicativo antes de ligar um dispositivo real.

Para adicionar um novo modelo de dispositivo para a sua aplicação, terá de ir para o **modelos de dispositivos** página. Para o fazer por isso, selecione o **modelos de dispositivos** no menu de navegação esquerdo.

![Página de modelos de dispositivo](./media/tutorial-define-device-type-experimental/devicetemplates.png)

## <a name="add-a-device-template"></a>Adicionar um modelo de dispositivo

Os passos seguintes mostram como criar um novo modelo de dispositivo de **Ar Condicionado Ligado** para dispositivos que enviam telemetria de temperatura para a sua aplicação:

1. Sobre o **modelos de dispositivos** página, selecione **+**:

    ![Página de modelos de dispositivo, criar modelo de dispositivo](./media/tutorial-define-device-type-experimental/newtemplate.png)

3. Sobre o **modelo de dispositivo personalizado** página, introduza **ligado ar-condicionado** como o nome do seu dispositivo e, em seguida, selecione **criar**. Também pode carregar uma imagem do seu dispositivo que está visível para os operadores no explorador do dispositivo:

    ![Personalizar Dispositivo](./media/tutorial-define-device-type-experimental/createcustomdevice.png)

4. Na **ligado ar-condicionado** modelo de dispositivo, certifique-se de que está no **medidas** separador onde define a telemetria. Cada modelo de dispositivo, que define tem separadores separados para que possa:

    * Especifique a _medidas_, como telemetria, o evento e o estado, enviados pelo dispositivo.

    * Definir o _definições_ utilizadas para controlar o dispositivo.

    * Definir o _propriedades_ que são os metadados do dispositivo.

    * Definir o _comandos_ para ser executado diretamente no dispositivo.

    * Definir o _regras_ associados ao dispositivo.

    * Personalizar o dispositivo _dashboard_ para os operadores.

    ![Medidas do ar condicionado](./media/tutorial-define-device-type-experimental/airconmeasurements.png)

    > [!NOTE]
    > Para alterar o nome do modelo de dispositivo, selecione o nome do modelo na parte superior da página.

5. Para adicionar a medida de telemetria de temperatura, selecione **+ nova medida**. Em seguida, escolha **Telemetria** como o tipo de medida:

    ![Medidas do dispositivo de ar condicionado ligado](./media/tutorial-define-device-type-experimental/airconmeasurementsnew.png)

6. Cada tipo de telemetria que define para um modelo de dispositivo inclui [opções de configuração](howto-set-up-template-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json), como:

    * Opções de apresentação.

    * Detalhes da telemetria.

    * Parâmetros de simulação.

    Para configurar a sua telemetria de **Temperatura**, utilize as informações na tabela seguinte:

    | Definição              | Value         |
    | -------------------- | -----------   |
    | Nome a Apresentar         | Temperatura   |
    | Nome do Campo           | temperatura   |
    | Unidades                | F             |
    | Mín.                  | 60            |
    | Máx.                  | 110           |
    | Casas decimais       | 0             |

    Também pode escolher uma cor para a apresentação de telemetria. Para guardar a definição de telemetria, selecione **guardar**:

    ![Configurar a simulação de Temperatura](./media/tutorial-define-device-type-experimental/temperaturesimulation.png)

7. Depois de pouco tempo, o **medidas** separador mostra um gráfico de telemetria de temperatura do seu dispositivo simulado ligados ar-condicionado. Utilize os controlos para gerir a visibilidade, a agregação ou para editar a definição de telemetria:

    ![Ver simulação de temperatura](./media/tutorial-define-device-type-experimental/viewsimulation.png)

8. Também pode personalizar o gráfico com os controlos **Linha**, **Empilhado** e **Editar Intervalo de Tempo**:

    ![Personalizar o gráfico](./media/tutorial-define-device-type-experimental/customizechart.png)

## <a name="add-an-event-measurement"></a>Adicionar uma medida de eventos

Use eventos para definir os dados de ponto no tempo que o dispositivo envia quando existe um evento, como um erro ou de uma falha de componente. O Azure IoT Central pode simular eventos de dispositivos para permitir que teste o comportamento do seu aplicativo antes de ligar um dispositivo real. Definir medidas de eventos para o modelo de dispositivo na **medidas** vista.

1. Para adicionar o **erro de Motor de ventoinha** medição de evento, selecione **+ nova medida**. Em seguida, escolha **Evento** como o tipo de medição:

    ![Medidas do dispositivo de ar condicionado ligado](./media/tutorial-define-device-type-experimental/eventnew.png)

2. Cada tipo de Evento que define para um modelo de dispositivo inclui [opções de configuração](howto-set-up-template-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json), como:

   * Nome a Apresentar.

   * Nome do Campo.

   * Gravidade.

    Para configurar o seu evento **Erro do Motor da Ventoinha**, utilize as informações na tabela seguinte:

    | Definição              | Value             |
    | -------------------- | -----------       |
    | Nome a Apresentar         | Erro de Motor da Ventoinha   |
    | Nome do Campo           | fanmotorerr       |
    | Gravidade             | Erro             |

    Para guardar a definição de eventos, selecione **guardar**:

    ![Configurar medição de Eventos](./media/tutorial-define-device-type-experimental/eventconfiguration.png)

3. Depois de pouco tempo, o **medidas** separador mostra um gráfico dos eventos gerados aleatoriamente a partir do seu dispositivo simulado ligados ar-condicionado. Utilize os controlos para gerir a visibilidade ou para editar a definição do evento:

    ![Ver simulação do evento](./media/tutorial-define-device-type-experimental/eventview.png)

1. Para ver detalhes adicionais sobre o evento, selecione o evento no gráfico:

    ![Ver Detalhes do Evento](./media/tutorial-define-device-type-experimental/eventviewdetail.png)

## <a name="define-a-state-measurement"></a>Definir uma medida de estado

Pode usar o estado para definir e visualizar o estado do dispositivo ou o seu componente durante um período de tempo. O Azure IoT Central pode simular o estado do dispositivo para permitir que teste o comportamento do seu aplicativo antes de ligar um dispositivo real. O utilizador define as medições do estado para o tipo de dispositivo na vista **Medidas**.

1. Para adicionar um **ventoinha modo** medição do Estado, selecione **+ nova medida**. Em seguida, escolha **Estado** como o tipo de medição:

    ![Medidas do estado do dispositivo de ar condicionado ligado](./media/tutorial-define-device-type-experimental/statenew.png)

2. Cada tipo de estado que definir para um modelo de dispositivo inclui [opções de configuração](howto-set-up-template-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) , tais como:

   * Nome a Apresentar.

   * Nome do Campo.

   * Valores com etiquetas de apresentação opcionais.

   * Cor para cada valor.

    Para configurar o seu estado do **Modo da Ventoinha**, utilize as informações na tabela seguinte:

    | Definição              | Value             |
    | -------------------- | -----------       |
    | Nome a Apresentar         | Modo da Ventoinha          |
    | Nome do Campo           | fanmode           |
    | Value                | 1                 |
    | Etiqueta de apresentação        | Em funcionamento         |
    | Value                | 0                 |
    | Etiqueta de apresentação        | Parada           |

    Para guardar a definição de medição do Estado, selecione **guardar**:

    ![Configurar medição de Estado](./media/tutorial-define-device-type-experimental/stateconfiguration.png)

3. Depois de pouco tempo, o **medidas** separador mostra um gráfico dos Estados aleatoriamente gerado a partir do seu dispositivo simulado ligados ar-condicionado. Utilize os controlos para gerir a visibilidade ou para editar a definição do estado:

    ![Ver simulação do estado](./media/tutorial-define-device-type-experimental/stateview.png)

4. Se existirem demasiados pontos de dados enviados pelo dispositivo dentro de um pequeno período de tempo, a medição de estado é apresentada com um elemento visual diferente. Selecione o gráfico para ver que todos os pontos de dados dentro desse período de tempo são apresentados por ordem cronológica. Também pode reduzir o intervalo de tempo para ver as medições mais detalhadamente.

## <a name="settings-properties-and-commands"></a>Definições, propriedades e comandos

As definições, as propriedades e os comandos são valores diferentes definidos num modelo de dispositivo e associados a cada dispositivo individual:

* Utilize _definições_ para enviar dados de configuração para um dispositivo da sua aplicação. Por exemplo, um operador pode utilizar uma definição para alterar o intervalo de telemetria do dispositivo de dois segundos para cinco segundos. Quando um operador altera uma definição, a definição é marcada como pendente na interface do Usuário até que o dispositivo responde com uma confirmação.

* Pode utilizar _propriedades_ para definir os metadados associados ao seu dispositivo. Existem duas categorias de propriedades:
    
    * Utilize _propriedades da aplicação_ para registar informações sobre o dispositivo na sua aplicação. Por exemplo, pode utilizar propriedades da aplicação para registar a localização de um dispositivo e a data da última assistência. Essas propriedades são armazenadas no aplicativo e não sincronizar com o dispositivo. Um operador pode atribuir valores às propriedades.

    * Utilize _propriedades do dispositivo_ para ativar um dispositivo para enviar os valores de propriedade para a aplicação. Estas propriedades só podem ser alteradas pelo dispositivo. Para um operador, as propriedades do dispositivo são só de leitura. Neste cenário de um ar condicionado ligado, a versão de firmware e o número de série do dispositivo são propriedades comunicadas pelo dispositivo.
    
    Para obter mais informações, consulte [propriedades](howto-set-up-template-experimental.md#properties) no guia de procedimento sobre como configurar um modelo de dispositivo.

* Pode utilizar _comandos_ para gerir remotamente o dispositivo a partir da sua aplicação. Pode executar diretamente comandos no dispositivo a partir da cloud para controlar os dispositivos. Por exemplo, um operador pode executar comandos, como reiniciar, para reiniciar instantaneamente o dispositivo.

## <a name="use-settings"></a>Utilizar as definições

Utilize *definições* para permitir que um operador envie dados de configuração para um dispositivo. Nesta secção, irá adicionar uma definição ao seu modelo de dispositivo **Ar Condicionado Ligado** que permite a um operador definir a temperatura de destino do ar condicionado ligado.

1. Navegue para o **configurações** separador para sua **ligado ar-condicionado** modelo do dispositivo.

2. Pode criar definições de diferentes tipos, como texto ou números. Selecione **número** para adicionar uma definição de número para o seu dispositivo.

3. Para configurar a definição de **Definir Temperatura**, utilize as informações na tabela seguinte:

    | Campo                | Value           |
    | -------------------- | -----------     |
    | Nome a Apresentar         | Definir Temperatura |
    | Nome do Campo           | setTemperature  |
    | Unidade de Medida      | F               |
    | Casas Decimais       | 1               |
    | Valor Mínimo        | 20              |
    | Valor Máximo        | 200             |
    | Valor Inicial        | 80              |
    | Descrição          | Defina a temperatura de destino para o ar condicionado |

    Em seguida, selecione **guardar**:

    ![Configure a definição Definir Temperatura](./media/tutorial-define-device-type-experimental/configuresetting.png)

    > [!NOTE]
    > Quando o dispositivo reconhece uma alteração da definição, o estado da definição muda para **sincronizado**.

4. Pode personalizar o layout do **definições** separador ao mover e redimensionar mosaicos de definições:

    ![Personalizar esquema de definições](./media/tutorial-define-device-type-experimental/settingslayout.png)

## <a name="use-properties"></a>Utilizar propriedades

Utilize *propriedades da aplicação* para armazenar informações sobre o seu dispositivo na aplicação. Nesta secção, irá adicionar as propriedades da aplicação ao modelo de dispositivo **Ar Condicionado Ligado** para armazenar a localização do dispositivo e a data da última assistência. Estas propriedades são editáveis na aplicação. O dispositivo também relata propriedades, tais como o número de série e versão de firmware que são só de leitura no aplicativo.

1. Navegue para o **propriedades** separador para sua **ligado ar-condicionado** modelo do dispositivo.

1. Pode criar propriedades do dispositivo de diferentes tipos, como texto ou números. Para adicionar uma propriedade de localização ao modelo de dispositivo, escolha **Localização**. Para configurar a propriedade da localização, utilize as informações na tabela seguinte:

    | Campo                | Value                |
    | -------------------- | -------------------- |
    | Nome a Apresentar         | Localização             |
    | Nome do Campo           | localização             |
    | Valor Inicial        | Seattle, WA          |
    | Descrição          | Localização do dispositivo      |

    Deixe os outros campos com os respetivos valores predefinidos.

    ![Configurar as propriedades do dispositivo](./media/tutorial-define-device-type-experimental/configureproperties.png)

    Selecione **Guardar**.

1. Para adicionar uma propriedade da data da última assistência ao modelo de dispositivo, escolha **Data**.

1. Para configurar a propriedade da data da última assistência, utilize as informações na tabela seguinte:

    | Campo                | Value                   |
    | -------------------- | ----------------------- |
    | Nome a Apresentar         | Data da Última Assistência       |
    | Nome do Campo           | serviceDate             |
    | Valor Inicial        | 1/1/2019                |
    | Descrição          | Última assistência           |

    ![Configurar as propriedades do dispositivo](./media/tutorial-define-device-type-experimental/configureproperties2.png)

    Selecione **Guardar**.

1. Pode personalizar o layout do **propriedades** separador ao mover e redimensionar mosaicos de propriedade.

1. Para adicionar uma propriedade de dispositivo, como a versão de firmware, ao modelo de dispositivo, selecione **Propriedade do Dispositivo**.

1. Para configurar a versão de firmware, utilize as informações na tabela seguinte:

    | Campo                | Value                   |
    | -------------------- | ----------------------- |
    | Nome a Apresentar         | Versão do firmware        |
    | Nome do Campo           | firmwareVersion         |
    | Tipo de Dados            | texto                    |
    | Descrição          | A versão de firmware do ar condicionado |

    ![Configurar a versão de firmware](./media/tutorial-define-device-type-experimental/configureproperties3.png)

    Selecione **Guardar**.

1. Para adicionar uma propriedade de dispositivo, como o número de série, ao modelo de dispositivo, selecione **Propriedade do Dispositivo**.

1. Para configurar o número de série, utilize as informações na tabela seguinte:

    | Campo                | Value                   |
    | -------------------- | ----------------------- |
    | Nome a Apresentar         | Número de série           |
    | Nome do Campo           | serialNumber            |
    | Tipo de Dados            | texto                    |
    | Descrição          | O número de série do ar condicionado  |

    ![Configurar o número de série](./media/tutorial-define-device-type-experimental/configureproperties4.png)

    Selecione **Guardar**.

    > [!NOTE]
    > A Propriedade do Dispositivo é enviada do dispositivo para a aplicação. Os valores da versão de firmware e do número de série serão atualizados quando o dispositivo real ligar ao IoT Central.

## <a name="use-commands"></a>Utilizar comandos

Pode utilizar _comandos_ para ativar um operador para executar comandos diretamente no dispositivo. Nesta secção, vai adicionar um comando ao seu modelo de dispositivo **Ar Condicionado Ligado** que permite a um operador apresentar uma determinada mensagem no ecrã do ar condicionado ligado.

1. Navegue para o **comandos** separador para sua **ligado ar-condicionado** modelo de dispositivo para editar o modelo.

1. Selecione **+ novo comando** para adicionar um comando para o seu dispositivo e começar a configurar o seu novo comando.

1. Para configurar o novo comando, utilize as informações na tabela seguinte:

    | Campo                | Value           |
    | -------------------- | -----------     |
    | Nome a Apresentar         | Comando Echo    |
    | Nome do Campo           | echo            |
    | Tempo Limite Predefinido      | 30              |
    | Tipo a Apresentar         | texto            |
    | Descrição          | Comando do Dispositivo  |  

    Pode adicionar entradas adicionais para o comando selecionando **+** para **campos de entrada**.

    ![Preparar para adicionar uma definição](./media/tutorial-define-device-type-experimental/commandsecho1.png)

     Selecione **Guardar**.

1. Pode personalizar o layout do **comandos** separador ao mover e redimensionar mosaicos de comando.

## <a name="view-your-simulated-device"></a>Veja o seu dispositivo simulado

Agora que definiu sua **ligado ar-condicionado** modelo de dispositivo, pode personalizar sua **Dashboard** para incluir as medidas, definições e propriedades que definiu. Em seguida, pode pré-visualizar o dashboard como um operador:

1. Escolha o **Dashboard** separador para sua **ligado ar-condicionado** modelo do dispositivo.

1. Selecione **gráfico de linhas** para adicionar o componente para o **Dashboard**.

1. Configure o componente **Gráfico de Linhas** com as informações na tabela seguinte:

    | Definição      | Value       |
    | ------------ | ----------- |
    | Título        | Temperatura |
    | Intervalo de Tempo   | Últimos 30 minutos |
    | Medidas     | Temperatura (selecione **visibilidade** junto a **temperatura**) |

    ![Definições do gráfico de linhas](./media/tutorial-define-device-type-experimental/linechartsettings.png)

    Em seguida, selecione **Guardar**.

1. Selecione o **histórico de eventos** componente usando as informações na tabela a seguir:

    | Definição      | Value       |
    | ------------ | ----------- |
    | Título        | Ventoinha eventos de Motor |
    | Intervalo de Tempo   | Últimos 30 minutos |
    | Medidas     | Ventoinha erro entrar (selecione **visibilidade** junto a **erro de Motor de ventoinha**) |

    ![Definições de gráfico do evento](./media/tutorial-define-device-type-experimental/dashboardeventchartsetting.png)

    Em seguida, selecione **Guardar**.

1. Configure o componente **Histórico de Estado** com as informações na tabela seguinte:

    | Definição      | Value       |
    | ------------ | ----------- |
    | Título        | Modo da Ventoinha |
    | Intervalo de Tempo   | Últimos 30 minutos |
    | Medidas | Ventoinha modo (selecione **visibilidade** junto a **modo ventoinha**) |

    ![Definições do gráfico de linhas](./media/tutorial-define-device-type-experimental/dashboardstatechartsetting.png)

    Em seguida, selecione **Guardar**.

1. Para adicionar as definições de dispositivo e propriedades para o dashboard, escolha **definições e propriedades**. Selecione **Adicionar/remover** para adicionar as definições ou propriedades que gostaria de ver no dashboard.

1. Configure o componente **Definições e Propriedades** com as informações na tabela seguinte:

    | Definição                 | Value         |
    | ----------------------- | ------------- |
    | Título                   | Propriedades do dispositivo |
    | Definições e Propriedades | Definir Temperatura<br/>Número de série<br/>Versão do firmware |

    Definições e propriedades que definiu anteriormente sobre o **definições e propriedades** páginas são mostradas na **colunas disponíveis**.

    ![Definir as propriedades de temperatura](./media/tutorial-define-device-type-experimental/propertysettings4.png)

    Em seguida, selecione **Guardar**.

1. Agora, pode ver dados simulados para sua ligado ar-condicionado no dashboard. Pode editar os mosaicos e o esquema do dashboard:

    ![Ver dashboard](./media/tutorial-define-device-type-experimental/dashboard.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Criar um novo modelo de dispositivo
> * Adicionar telemetria ao seu dispositivo
> * Ver telemetria simulada
> * Definir eventos de dispositivo
> * Ver eventos simulados
> * Definir o seu estado
> * Ver estado simulado
> * Definições e propriedades de utilização
> * Utilizar comandos
> * Ver o seu dispositivo simulado no dashboard

Agora que definiu um modelo de dispositivo na sua aplicação do Azure IoT Central, aqui estão os passos sugeridos seguintes:

* [Configurar regras e ações para o seu dispositivo](tutorial-configure-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Personalizar as vistas do operador](tutorial-customize-operator-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)