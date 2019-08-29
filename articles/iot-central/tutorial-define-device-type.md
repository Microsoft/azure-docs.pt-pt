---
title: Definir um novo tipo de dispositivo no Azure IoT Central | Microsoft Docs
description: Este tutorial mostra-lhe como definir, enquanto construtor, um novo tipo de dispositivo na aplicação do Azure IoT Central. O utilizador define a telemetria, o estado, as propriedades e as definições para o seu tipo.
author: dominicbetts
ms.author: dobett
ms.date: 06/07/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: db9f7e75af01ed83c39ef3a37ab2612426ef6ea4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099611"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application"></a>Tutorial: Definir um novo tipo de dispositivo em seu aplicativo de IoT Central do Azure

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Este tutorial mostra-lhe como utilizar, enquanto construtor, um modelo de dispositivo para definir um novo tipo de dispositivo na aplicação do Azure IoT Central. Um modelo do dispositivo define a telemetria, o estado, as propriedades e as definições para o tipo de dispositivo.

Para poder testar a sua aplicação antes de ligar a um dispositivo real, o IoT Central gera um dispositivo simulado a partir do modelo de dispositivo ao criá-lo.

Neste tutorial, vai criar um modelo de dispositivo de **Ar Condicionado Ligado**. Um dispositivo de ar condicionado ligado:

* Envia telemetria, como temperatura e humidade.
* Relata o estado, como se ele está ativado ou desativado.
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

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma aplicação do Azure IoT Central. Se concluiu o início rápido [Criar uma aplicação do Azure IoT Central](quick-deploy-iot-central.md), pode reutilizar a aplicação que criou no início rápido. Caso contrário, execute os seguintes passos para criar uma aplicação do Azure IoT Central vazia:

1. Navegue até o site [do Azure IOT central Application Manager](https://aka.ms/iotcentral) .

2. Introduza o endereço de e-mail e a palavra-passe que utiliza para aceder à sua subscrição do Azure:

    ![Introduza a sua conta da organização](./media/tutorial-define-device-type/sign-in.png)

3. Para começar a criar um novo aplicativo de IoT Central do Azure, selecione **novo aplicativo**:

    ![Página do Gerenciador de aplicativos do IoT Central do Azure](./media/tutorial-define-device-type/iotcentralhome.png)

4. Para criar uma nova aplicação do Azure IoT Central:
    
   * Escolha **Avaliação**. Não precisa de uma subscrição do Azure para criar uma aplicação de Avaliação.
    
      Para obter mais informações sobre as subscrições e diretórios, veja [criar uma aplicação de início rápido](quick-deploy-iot-central.md).
    
   * Selecione **Aplicação Personalizada**.
    
   * Opcionalmente, pode escolher um nome de aplicação amigável, como **Ares Condicionados Contoso**. O Azure IoT Central gera um prefixo de URL exclusivo para si. Pode alterar este prefixo de URL para algo mais memorável.
    
   * Selecione **Criar**.

     ![Página da Aplicação do Azure IoT Central](./media/tutorial-define-device-type/iotcentralcreate.png)

     Para obter mais informações, veja [criar um início rápido da aplicação](quick-deploy-iot-central.md).

## <a name="create-a-device-template"></a>Criar um modelo de dispositivo

Como construtor, pode criar e editar os modelos de dispositivos na sua aplicação. Quando cria um modelo de dispositivo, o Azure IoT Central gera um dispositivo simulado a partir do modelo. O dispositivo simulado gera telemetria que permite testar o comportamento do seu aplicativo antes de conectar um dispositivo real.

Para adicionar um novo modelo de dispositivo ao seu aplicativo, você precisa ir para a página **modelos de dispositivo** . Para fazer isso, selecione os **modelos de dispositivo** no menu de navegação à esquerda.

![Página modelos de dispositivo](./media/tutorial-define-device-type/devicetemplates.png)

## <a name="add-a-device-template"></a>Adicionar um modelo de dispositivo

Os passos seguintes mostram como criar um novo modelo de dispositivo de **Ar Condicionado Ligado** para dispositivos que enviam telemetria de temperatura para a sua aplicação:

1. Na página **modelos de dispositivo** , selecione **+ novo**:

    ![Página modelos de dispositivo, criar modelo de dispositivo](./media/tutorial-define-device-type/newtemplate.png)

2. A página mostra os modelos dos quais você pode escolher.

    ![Biblioteca de modelos de dispositivo](./media/tutorial-define-device-type/devicetemplatelibrary.png)

3. Selecione **personalizado**, insira o **condicionador de ar conectado** como o nome do seu modelo de dispositivo e, em seguida, selecione **criar**. Também pode carregar uma imagem do seu dispositivo que está visível para os operadores no explorador do dispositivo:

    ![Personalizar Dispositivo](./media/tutorial-define-device-type/createcustomdevice.png)

4. No modelo de dispositivo de **ar-condicionado conectado** , verifique se você está na guia **medidas** em que você define a telemetria. Cada modelo de dispositivo que você define tem guias separadas para você:

   * Especifique as _medidas_, como telemetria, evento e estado, enviadas pelo dispositivo.

   * Defina as _configurações_ usadas para controlar o dispositivo.

   * Defina as _Propriedades_ que são os metadados do dispositivo.

   * Defina os _comandos_ a serem executados diretamente no dispositivo.

   * Defina as _regras_ associadas ao dispositivo.

   * Personalize o _painel_ do dispositivo para seus operadores.

     ![Medidas do ar condicionado](./media/tutorial-define-device-type/airconmeasurements.png)

     > [!NOTE]
     > Para alterar o nome do modelo de dispositivo, selecione o nome do modelo na parte superior da página.

5. Para adicionar a medição de telemetria de temperatura, selecione **+ nova medida**. Em seguida, escolha **Telemetria** como o tipo de medida:

    ![Medidas do dispositivo de ar condicionado ligado](./media/tutorial-define-device-type/airconmeasurementsnew.png)

6. Cada tipo de telemetria que define para um modelo de dispositivo inclui [opções de configuração](howto-set-up-template.md), como:

   * Opções de apresentação.

   * Detalhes da telemetria.

   * Parâmetros de simulação.

     Para configurar a sua telemetria de **Temperatura**, utilize as informações na tabela seguinte:

     | Definição              | Value         |
     | -------------------- | -----------   |
     | Nome a Apresentar         | Temperatura   |
     | Nome do Campo           | temperatura   |
     | Unidades                | S             |
     | Mín.                  | 60            |
     | Máx.                  | 110           |
     | Casas decimais       | 0             |

     Também pode escolher uma cor para a apresentação de telemetria. Para salvar a definição de telemetria, selecione **salvar**:

     ![Configurar a simulação de Temperatura](./media/tutorial-define-device-type/temperaturesimulation.png)

7. Após alguns instantes, a guia **medidas** mostra um gráfico da telemetria de temperatura do seu dispositivo de ar-condicionado conectado simulado. Utilize os controlos para gerir a visibilidade, a agregação ou para editar a definição de telemetria:
 
    > [!NOTE]
    > Para telemetria, a **média** é definida como a agregação padrão. 

    ![Ver simulação de temperatura](./media/tutorial-define-device-type/viewsimulation.png)

8. Também pode personalizar o gráfico com os controlos **Linha**, **Empilhado** e **Editar Intervalo de Tempo**:

    ![Personalizar o gráfico](./media/tutorial-define-device-type/customizechart.png)

## <a name="add-an-event-measurement"></a>Adicionar uma medição de evento

Use eventos para definir dados pontuais que o dispositivo envia quando há um evento, como um erro ou uma falha de componente. O Azure IoT Central pode simular eventos de dispositivo para permitir que você teste o comportamento do seu aplicativo antes de conectar um dispositivo real. Defina as medidas de evento para o modelo de dispositivo na exibição **medidas** .

1. Para adicionar a medição de evento de **erro do motor do ventilador** , selecione **+ nova medida**. Em seguida, escolha **Evento** como o tipo de medição:

    ![Medidas do dispositivo de ar condicionado ligado](./media/tutorial-define-device-type/eventnew.png)

2. Cada tipo de Evento que define para um modelo de dispositivo inclui [opções de configuração](howto-set-up-template.md), como:

   * Nome a Apresentar.

   * Nome do Campo.

   * Gravidade.

     Para configurar o seu evento **Erro do Motor da Ventoinha**, utilize as informações na tabela seguinte:

     | Definição              | Value             |
     | -------------------- | -----------       |
     | Nome a Apresentar         | Erro de Motor da Ventoinha   |
     | Nome do Campo           | fanmotorerr       |
     | Severity             | Erro             |

     Para salvar a definição de evento, selecione **salvar**:

     ![Configurar medição de Eventos](./media/tutorial-define-device-type/eventconfiguration.png)

3. Após alguns instantes, a guia **medidas** mostra um gráfico dos eventos gerados aleatoriamente de seu dispositivo de ar-condicionado conectado simulado. Utilize os controlos para gerir a visibilidade ou para editar a definição do evento:

    ![Ver simulação do evento](./media/tutorial-define-device-type/eventview.png)

1. Para ver detalhes adicionais sobre o evento, selecione o evento no gráfico:

    ![Ver Detalhes do Evento](./media/tutorial-define-device-type/eventviewdetail.png)

## <a name="define-a-state-measurement"></a>Definir uma medida de estado

Você pode usar o estado para definir e visualizar o estado do dispositivo ou seu componente em um período de tempo. O Azure IoT Central pode simular o estado do dispositivo para permitir que você teste o comportamento do seu aplicativo antes de conectar um dispositivo real. O utilizador define as medições do estado para o tipo de dispositivo na vista **Medidas**.

1. Para adicionar uma medida de estado **do modo de ventilador** , selecione **+ nova medida**. Em seguida, escolha **Estado** como o tipo de medição:

    ![Medidas do estado do dispositivo de ar condicionado ligado](./media/tutorial-define-device-type/statenew.png)

2. Cada tipo de estado que você define para um modelo de dispositivo inclui [Opções de configuração](howto-set-up-template.md) , como:

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

     Para salvar a definição de medida de estado, selecione **salvar**:

     ![Configurar medição de Estado](./media/tutorial-define-device-type/stateconfiguration.png)

3. Após alguns instantes, a guia **medidas** mostra um gráfico dos Estados gerados aleatoriamente de seu dispositivo de ar-condicionado conectado simulado. Utilize os controlos para gerir a visibilidade ou para editar a definição do estado:

    ![Ver simulação do estado](./media/tutorial-define-device-type/stateview.png)

4. Se houver muitos pontos de dados enviados pelo dispositivo em uma pequena duração, a medida de estado será mostrada com um visual diferente. Selecione o gráfico para ver todos os pontos de dados dentro desse período de tempo são exibidos em ordem cronológica. Também pode reduzir o intervalo de tempo para ver as medições mais detalhadamente.

## <a name="settings-properties-and-commands"></a>Definições, propriedades e comandos

As definições, as propriedades e os comandos são valores diferentes definidos num modelo de dispositivo e associados a cada dispositivo individual:

* Utilize _definições_ para enviar dados de configuração para um dispositivo da sua aplicação. Por exemplo, um operador pode utilizar uma definição para alterar o intervalo de telemetria do dispositivo de dois segundos para cinco segundos. Quando um operador altera uma configuração, a configuração é marcada como pendente na interface do usuário até que o dispositivo responda com uma confirmação.

* Pode utilizar _propriedades_ para definir os metadados associados ao seu dispositivo. Existem duas categorias de propriedades:
    
  * Utilize _propriedades da aplicação_ para registar informações sobre o dispositivo na sua aplicação. Por exemplo, pode utilizar propriedades da aplicação para registar a localização de um dispositivo e a data da última assistência. Essas propriedades são armazenadas no aplicativo e não são sincronizadas com o dispositivo. Um operador pode atribuir valores às propriedades.

  * Utilize _propriedades do dispositivo_ para ativar um dispositivo para enviar os valores de propriedade para a aplicação. Estas propriedades só podem ser alteradas pelo dispositivo. Para um operador, as propriedades do dispositivo são só de leitura. Neste cenário de um ar condicionado ligado, a versão de firmware e o número de série do dispositivo são propriedades comunicadas pelo dispositivo.
    
    Para obter mais informações, consulte [Propriedades](howto-set-up-template.md#properties) no guia de instruções sobre como configurar um modelo de dispositivo.

* Pode utilizar _comandos_ para gerir remotamente o dispositivo a partir da sua aplicação. Pode executar diretamente comandos no dispositivo a partir da cloud para controlar os dispositivos. Por exemplo, um operador pode executar comandos, como reiniciar, para reiniciar instantaneamente o dispositivo.

## <a name="use-settings"></a>Utilizar as definições

Utilize *definições* para permitir que um operador envie dados de configuração para um dispositivo. Nesta secção, irá adicionar uma definição ao seu modelo de dispositivo **Ar Condicionado Ligado** que permite a um operador definir a temperatura de destino do ar condicionado ligado.

1. Navegue até a guia **configurações** do seu modelo de dispositivo de **ar condicionado conectado** .

2. Pode criar definições de diferentes tipos, como texto ou números. Selecione **número** para adicionar uma configuração de número ao seu dispositivo.

3. Para configurar a definição de **Definir Temperatura**, utilize as informações na tabela seguinte:

    | Campo                | Value           |
    | -------------------- | -----------     |
    | Nome a Apresentar         | Definir Temperatura |
    | Nome do Campo           | setTemperature  |
    | Unidade de Medida      | S               |
    | Casas Decimais       | 1               |
    | Valor Mínimo        | 20              |
    | Valor Máximo        | 200             |
    | Valor Inicial        | 80              |
    | Descrição          | Defina a temperatura de destino para o ar condicionado |

    Em seguida, selecione **salvar**:

    ![Configure a definição Definir Temperatura](./media/tutorial-define-device-type/configuresetting.png)

    > [!NOTE]
    > Quando o dispositivo reconhece uma alteração da definição, o estado da definição muda para **sincronizado**.

4. Você pode personalizar o layout da guia **configurações** movendo e redimensionando blocos de configurações:

    ![Personalizar esquema de definições](./media/tutorial-define-device-type/settingslayout.png)

## <a name="use-properties"></a>Utilizar propriedades

Utilize *propriedades da aplicação* para armazenar informações sobre o seu dispositivo na aplicação. Nesta secção, irá adicionar as propriedades da aplicação ao modelo de dispositivo **Ar Condicionado Ligado** para armazenar a localização do dispositivo e a data da última assistência. Essas propriedades são editáveis no aplicativo. O dispositivo também relata propriedades como o número de série e a versão do firmware que são somente leitura no aplicativo.

1. Navegue até a guia **Propriedades** do seu modelo de dispositivo do **ar condicionado conectado** .

1. Pode criar propriedades do dispositivo de diferentes tipos, como texto ou números. Para adicionar uma propriedade de localização ao modelo de dispositivo, escolha **Localização**. Para configurar a propriedade da localização, utilize as informações na tabela seguinte:

    | Campo                | Value                |
    | -------------------- | -------------------- |
    | Nome a Apresentar         | Location             |
    | Nome do Campo           | location             |
    | Valor Inicial        | Seattle, WA          |
    | Descrição          | Localização do dispositivo      |

    Deixe os outros campos com os respetivos valores predefinidos.

    ![Configurar as propriedades do dispositivo](./media/tutorial-define-device-type/configureproperties.png)

    Selecione **Guardar**.

1. Para adicionar uma propriedade da data da última assistência ao modelo de dispositivo, escolha **Data**.

1. Para configurar a propriedade da data da última assistência, utilize as informações na tabela seguinte:

    | Campo                | Value                   |
    | -------------------- | ----------------------- |
    | Nome a Apresentar         | Data da Última Assistência       |
    | Nome do Campo           | serviceDate             |
    | Valor Inicial        | 1/1/2019                |
    | Descrição          | Última assistência           |

    ![Configurar as propriedades do dispositivo](./media/tutorial-define-device-type/configureproperties2.png)

    Selecione **Guardar**.

1. Você pode personalizar o layout da guia **Propriedades** movendo e redimensionando os blocos de propriedade.

1. Para adicionar uma propriedade de dispositivo, como a versão de firmware, ao modelo de dispositivo, selecione **Propriedade do Dispositivo**.

1. Para configurar a versão de firmware, utilize as informações na tabela seguinte:

    | Campo                | Value                   |
    | -------------------- | ----------------------- |
    | Nome a Apresentar         | Versão do firmware        |
    | Nome do Campo           | firmwareVersion         |
    | Tipo de Dados            | text                    |
    | Descrição          | A versão de firmware do ar condicionado |

    ![Configurar a versão de firmware](./media/tutorial-define-device-type/configureproperties3.png)

    Selecione **Guardar**.

1. Para adicionar uma propriedade de dispositivo, como o número de série, ao modelo de dispositivo, selecione **Propriedade do Dispositivo**.

1. Para configurar o número de série, utilize as informações na tabela seguinte:

    | Campo                | Value                   |
    | -------------------- | ----------------------- |
    | Nome a Apresentar         | Número de série           |
    | Nome do Campo           | serialNumber            |
    | Tipo de Dados            | text                    |
    | Descrição          | O número de série do ar condicionado  |

    ![Configurar o número de série](./media/tutorial-define-device-type/configureproperties4.png)

    Selecione **Guardar**.

    > [!NOTE]
    > A Propriedade do Dispositivo é enviada do dispositivo para a aplicação. Os valores da versão de firmware e do número de série serão atualizados quando o dispositivo real ligar ao IoT Central.

## <a name="use-commands"></a>Utilizar comandos

Pode utilizar _comandos_ para ativar um operador para executar comandos diretamente no dispositivo. Nesta secção, vai adicionar um comando ao seu modelo de dispositivo **Ar Condicionado Ligado** que permite a um operador apresentar uma determinada mensagem no ecrã do ar condicionado ligado.

1. Navegue até a guia **comandos** do seu modelo de dispositivo do **ar-condicionado conectado** para editar o modelo.

1. Selecione **+ novo comando** para adicionar um comando ao seu dispositivo e começar a configurar o novo comando.

1. Para configurar o novo comando, utilize as informações na tabela seguinte:

    | Campo                | Value           |
    | -------------------- | -----------     |
    | Nome a Apresentar         | Comando Echo    |
    | Nome do Campo           | echo            |
    | Tempo Limite Predefinido      | 30              |
    | Tipo a Apresentar         | text            |
    | Descrição          | Comando do Dispositivo  |  

    Você pode adicionar entradas adicionais ao comando selecionando **+** campos de **entrada**.

    ![Preparar para adicionar uma definição](./media/tutorial-define-device-type/commandsecho1.png)

     Selecione **Guardar**.

1. Você pode personalizar o layout da guia **comandos** movendo e redimensionando os blocos de comando.

## <a name="view-your-simulated-device"></a>Veja o seu dispositivo simulado

Agora que você definiu seu modelo de dispositivo de **ar-condicionado conectado** , você pode personalizar seu **painel** para incluir as medidas, as configurações e as propriedades definidas. Em seguida, pode pré-visualizar o dashboard como um operador:

1. Escolha a guia **painel** do seu modelo de dispositivo de **ar condicionado conectado** .

1. Selecione **gráfico de linhas** para adicionar o componente no **painel**.

1. Configure o componente **Gráfico de Linhas** com as informações na tabela seguinte:

    | Definição      | Value       |
    | ------------ | ----------- |
    | Cargo        | Temperatura |
    | Intervalo de Tempo   | Últimos 30 minutos |
    | Medidas     | Temperatura (selecione a **visibilidade** ao lado da **temperatura**) |

    ![Definições do gráfico de linhas](./media/tutorial-define-device-type/linechartsettings.png)

    Em seguida, selecione **Guardar**.

1. Selecione o componente **histórico de eventos** usando as informações na tabela a seguir:

    | Definição      | Value       |
    | ------------ | ----------- |
    | Cargo        | Eventos do motor do ventilador |
    | Intervalo de Tempo   | Últimos 30 minutos |
    | Medidas     | Erro do motor do ventilador (selecione a **visibilidade** ao lado **do erro do motor do ventilador**) |

    ![Configurações de gráfico de eventos](./media/tutorial-define-device-type/dashboardeventchartsetting.png)

    Em seguida, selecione **Guardar**.

1. Configure o componente **Histórico de Estado** com as informações na tabela seguinte:

    | Definição      | Value       |
    | ------------ | ----------- |
    | Cargo        | Modo da Ventoinha |
    | Intervalo de Tempo   | Últimos 30 minutos |
    | Medidas | Modo de ventilador (selecione a **visibilidade** ao lado **do modo de ventilador**) |

    ![Definições do gráfico de linhas](./media/tutorial-define-device-type/dashboardstatechartsetting.png)

    Em seguida, selecione **Guardar**.

1. Para adicionar as configurações e as propriedades do dispositivo ao painel, escolha **configurações e propriedades**. Selecione **Adicionar/remover** para adicionar as configurações ou propriedades que você gostaria de ver no painel.

1. Configure o componente **Definições e Propriedades** com as informações na tabela seguinte:

    | Definição                 | Value         |
    | ----------------------- | ------------- |
    | Cargo                   | Propriedades do dispositivo |
    | Definições e Propriedades | Definir Temperatura<br/>N.º de série<br/>Versão do firmware |

    As configurações e propriedades definidas anteriormente nas páginas **configurações e propriedades** são mostradas em **colunas disponíveis**.

    ![Definir as propriedades de temperatura](./media/tutorial-define-device-type/propertysettings4.png)

    Em seguida, selecione **Guardar**.

1. Agora você pode ver os dados simulados de seu ar-condicionado conectado no painel. Você pode editar os blocos e o layout do painel:

    ![Ver dashboard](./media/tutorial-define-device-type/dashboard.png)

## <a name="next-steps"></a>Passos seguintes

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

Agora que você definiu um modelo de dispositivo em seu aplicativo IoT Central do Azure, aqui estão as próximas etapas sugeridas:

* [Configurar regras e ações para o seu dispositivo](tutorial-configure-rules.md)
* [Personalizar as vistas do operador](tutorial-customize-operator.md)