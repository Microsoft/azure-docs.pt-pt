---
title: Definir um novo tipo de dispositivo no Azure IoT Central | Microsoft Docs
description: Este tutorial mostra-lhe como definir, enquanto construtor, um novo tipo de dispositivo na aplicação do Azure IoT Central. O utilizador define a telemetria, o estado, as propriedades e as definições para o seu tipo.
author: dominicbetts
ms.author: dobett
ms.date: 10/30/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 0e872509b7971adce871daf6967c0fcd1a87e9bc
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57311040"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application"></a>Tutorial: Definir um novo tipo de dispositivo na sua aplicação do Azure IoT Central

Este tutorial mostra-lhe como utilizar, enquanto construtor, um modelo de dispositivo para definir um novo tipo de dispositivo na aplicação do Azure IoT Central. Um modelo do dispositivo define a telemetria, o estado, as propriedades e as definições para o tipo de dispositivo.

Para poder testar a sua aplicação antes de ligar a um dispositivo real, o IoT Central gera um dispositivo simulado a partir do modelo de dispositivo ao criá-lo.

Neste tutorial, vai criar um modelo de dispositivo de **Ar Condicionado Ligado**. Um dispositivo de ar condicionado ligado:

* Envia telemetria, como temperatura e humidade.
* Comunica o estado, como se está ligado ou desligado.
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

Para concluir este tutorial, precisa de uma aplicação do Azure IoT Central. Se concluiu o início rápido [Criar uma aplicação do Azure IoT Central](quick-deploy-iot-central.md), pode reutilizar a aplicação que criou no início rápido. Caso contrário, execute os seguintes passos para criar uma aplicação do Azure IoT Central vazia:

1. Navegue para a página do [Gestor de Aplicações](https://aka.ms/iotcentral) do Azure IoT Central.

2. Introduza o endereço de e-mail e a palavra-passe que utiliza para aceder à sua subscrição do Azure:

   ![Introduza a sua conta da organização](./media/tutorial-define-device-type/sign-in.png)

3. Para começar a criar uma nova aplicação do Azure IoT Central, escolha **Nova Aplicação**:

    ![Página do Gestor de Aplicações do Azure IoT Central](./media/tutorial-define-device-type/iotcentralhome.png)

4. Para criar uma nova aplicação do Azure IoT Central:
    
    * Escolha **Avaliação**. Não precisa de uma subscrição do Azure para criar uma aplicação de Avaliação.
    
       Para obter mais informações sobre as subscrições e diretórios, veja [criar uma aplicação de início rápido](quick-deploy-iot-central.md).
    
    * Selecione **Aplicação Personalizada**.
    
    * Opcionalmente, pode escolher um nome de aplicação amigável, como **Ares Condicionados Contoso**. O Azure IoT Central gera um prefixo de URL exclusivo para si. Pode alterar este prefixo de URL para algo mais memorável.
    
    * Escolha **Criar**.

    ![Página da Aplicação do Azure IoT Central](./media/tutorial-define-device-type/iotcentralcreate.png)

    Para obter mais informações, veja [criar um início rápido da aplicação](quick-deploy-iot-central.md).

## <a name="create-a-new-custom-device-template"></a>Criar um novo modelo de dispositivo personalizado

Como construtor, pode criar e editar os modelos de dispositivos na sua aplicação. Quando cria um modelo de dispositivo, o Azure IoT Central gera um dispositivo simulado a partir do modelo. O dispositivo simulado gera telemetria, que permite que teste o comportamento do seu aplicativo antes de ligar um dispositivo real.

Para adicionar um novo modelo de dispositivo à sua aplicação, visite a página **Construtor da Aplicação**. Para tal, selecione o **Construtor da aplicação** no menu de navegação à esquerda.

![Página Application Builder](./media/tutorial-define-device-type/builderhome.png)

## <a name="add-a-device-and-define-telemetry"></a>Adicionar um dispositivo e definir a telemetria

Os passos seguintes mostram como criar um novo modelo de dispositivo de **Ar Condicionado Ligado** para dispositivos que enviam telemetria de temperatura para a sua aplicação:

1. Na página **Construtor da Aplicação**, escolha **Criar Modelo do Dispositivo**:

    ![Página Construtor da Aplicação, Criar Modelo do Dispositivo](./media/tutorial-define-device-type/builderhomedevices.png)

2. Na página **Modelos de Dispositivo**, escolha **Personalizar**. O modelo de dispositivo **Personalizar** permite-lhe definir todas as características e comportamentos do seu ar condicionado ligado:

    ![Dispositivos](./media/tutorial-define-device-type/builderhomedevicescustom.png)

3. Na página **Novo Modelo de Dispositivo**, introduza **Ar Condicionado Ligado** como o nome do seu dispositivo e, em seguida, escolha **Criar**. Também pode carregar uma imagem do seu dispositivo que está visível para os operadores no explorador do dispositivo:

    ![Personalizar Dispositivo](./media/tutorial-define-device-type/createcustomdevice.png)

4. No modelo do dispositivo **Ar Condicionado Ligado**, certifique-se de está na página **Medidas**, onde define a telemetria. Cada modelo de dispositivo que define tem páginas separadas para que possa:

    * Especificar os valores, como telemetria, eventos e estado, enviados pelo dispositivo.
    
    * Definir as definições utilizadas para controlar o dispositivo.
    
    * Defina as propriedades que são os metadados do dispositivo.

    * Defina os comandos a executar diretamente no dispositivo.
    
    * Definir as regras associadas ao dispositivo.
    
    * Personalizar o dashboard do dispositivo para os operadores.

    Sempre que estiver a definir o modelo de dispositivo, selecione primeiro **Editar Modelo** para editar o modelo. Quando tiver terminado, selecione **Concluído**. 

    ![Medidas do ar condicionado](./media/tutorial-define-device-type/airconmeasurements.png)

    > [!NOTE]
    > Para alterar o nome do dispositivo ou modelo de dispositivo, selecione o texto na parte superior da página.

5. Para adicionar a medição de telemetria de temperatura, escolha **Nova Medida**. Em seguida, escolha **Telemetria** como o tipo de medida:

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
    | Unidades                | F             |
    | Mín.                  | 60            |
    | Máx.                  | 110           |
    | Casas decimais       | 0             |

    Também pode escolher uma cor para a apresentação de telemetria. Para guardar a definição de telemetria, escolha **Guardar**:

    ![Configurar a simulação de Temperatura](./media/tutorial-define-device-type/temperaturesimulation.png)

7. Após um curto período, a página **Medidas** mostra um gráfico da telemetria de temperatura a partir do seu dispositivo simulado de ar condicionado ligado. Utilize os controlos para gerir a visibilidade, a agregação ou para editar a definição de telemetria:

    ![Ver simulação de temperatura](./media/tutorial-define-device-type/viewsimulation.png)

8. Também pode personalizar o gráfico com os controlos **Linha**, **Empilhado** e **Editar Intervalo de Tempo**:

    ![Personalizar o gráfico](./media/tutorial-define-device-type/customizechart.png)

## <a name="define-event-measurement"></a>Definir medição de Eventos

Pode utilizar o Evento para definir os dados de ponto no tempo que são enviados pelo dispositivo para indicar algo importante, como um erro ou uma falha de componente. Medições de telemetria, como o Azure IoT Central pode simular eventos de dispositivos para permitir que teste o comportamento do seu aplicativo antes de ligar um dispositivo real. O utilizador define as medições dos eventos para o tipo de dispositivo na vista **Medidas**.

1. Para adicionar a medição do evento **Erro de Motor da Ventoinha**, escolha **Nova Medição**. Em seguida, escolha **Evento** como o tipo de medição:

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
    | Gravidade             | Erro             |

    Para guardar a definição do evento, escolha **Guardar**:

    ![Configurar medição de Eventos](./media/tutorial-define-device-type/eventconfiguration.png)

3. Após um curto período, a página **Medidas** mostra um gráfico dos eventos gerados de forma aleatória a partir do seu dispositivo simulado de ar condicionado ligado. Utilize os controlos para gerir a visibilidade ou para editar a definição do evento:

    ![Ver simulação do evento](./media/tutorial-define-device-type/eventview.png)

1. Para ver detalhes adicionais sobre o evento, selecione o evento no gráfico:

    ![Ver Detalhes do Evento](./media/tutorial-define-device-type/eventviewdetail.png)

## <a name="define-state-measurement"></a>Definir medição de Estado

Pode utilizar o Estado para definir e visualizar o estado do dispositivo ou o respetivo componente durante um período de tempo. Medições de telemetria, como o Azure IoT Central pode simular o estado do dispositivo para permitir que teste o comportamento do seu aplicativo antes de ligar um dispositivo real. O utilizador define as medições do estado para o tipo de dispositivo na vista **Medidas**.

1. Para adicionar a medição **Modo de Ventoinha**, escolha **Nova Medição**. Em seguida, escolha **Estado** como o tipo de medição:

    ![Medidas do estado do dispositivo de ar condicionado ligado](./media/tutorial-define-device-type/statenew.png)

2. Cada tipo de Estado que define para um modelo de dispositivo inclui [opções de configuração](howto-set-up-template.md), como:

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

    Para guardar a definição da medição do estado, escolha **Guardar**:

    ![Configurar medição de Estado](./media/tutorial-define-device-type/stateconfiguration.png)

3. Após um curto período, a página **Medições** mostra um gráfico dos estados gerados de forma aleatória a partir do seu dispositivo simulado de ar condicionado ligado. Utilize os controlos para gerir a visibilidade ou para editar a definição do estado:

    ![Ver simulação do estado](./media/tutorial-define-device-type/stateview.png)

4. No caso, existem demasiados pontos de dados enviados pelo dispositivo num curto período, a medição de estado é apresentada com um visual diferente, conforme mostrado abaixo. Se selecionar o gráfico, em seguida, todos os pontos de dados dentro desse período de tempo são apresentados numa ordem cronológica. Também pode reduzir o intervalo de tempo para ver as medições mais detalhadamente.

    ![Ver Detalhes de estado](./media/tutorial-define-device-type/stateviewdetail.png)

## <a name="settings-properties-and-commands"></a>Definições, propriedades e comandos

As definições, as propriedades e os comandos são valores diferentes definidos num modelo de dispositivo e associados a cada dispositivo individual:

* Utilize _definições_ para enviar dados de configuração para um dispositivo da sua aplicação. Por exemplo, um operador pode utilizar uma definição para alterar o intervalo de telemetria do dispositivo de dois segundos para cinco segundos. Quando um operador altera uma definição, a definição é marcada como pendente na IU até que o dispositivo reconheça que acionou a alteração da definição.

* Pode utilizar _propriedades_ para definir os metadados associados ao seu dispositivo. Existem duas categorias de propriedades:
    
    * Utilize _propriedades da aplicação_ para registar informações sobre o dispositivo na sua aplicação. Por exemplo, pode utilizar propriedades da aplicação para registar a localização de um dispositivo e a data da última assistência. Estas propriedades estão armazenadas na aplicação e não são sincronizadas com o dispositivo. Um operador pode atribuir valores às propriedades.

    * Utilize _propriedades do dispositivo_ para ativar um dispositivo para enviar os valores de propriedade para a aplicação. Estas propriedades só podem ser alteradas pelo dispositivo. Para um operador, as propriedades do dispositivo são só de leitura. Neste cenário de um ar condicionado ligado, a versão de firmware e o número de série do dispositivo são propriedades comunicadas pelo dispositivo. 
    
    Para obter mais informações, veja [Propriedades][lnk-define-template] no guia de procedimentos sobre como configurar um modelo de dispositivo.

* Pode utilizar _comandos_ para gerir remotamente o dispositivo a partir da sua aplicação. Pode executar diretamente comandos no dispositivo a partir da cloud para controlar os dispositivos. Por exemplo, um operador pode executar comandos, como reiniciar, para reiniciar instantaneamente o dispositivo.

## <a name="use-settings"></a>Utilizar as definições

Utilize *definições* para permitir que um operador envie dados de configuração para um dispositivo. Nesta secção, irá adicionar uma definição ao seu modelo de dispositivo **Ar Condicionado Ligado** que permite a um operador definir a temperatura de destino do ar condicionado ligado.

1. Navegue para a página **Definições** para o seu modelo de dispositivo **Ar Condicionado Ligado**:

    ![Preparar para adicionar uma definição](./media/tutorial-define-device-type/deviceaddsetting.png)

    Pode criar definições de diferentes tipos, como texto ou números.

2. Escolha **Número** para adicionar uma definição de número ao seu dispositivo.

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

    Em seguida, escolha **Guardar**:

    ![Configure a definição Definir Temperatura](./media/tutorial-define-device-type/configuresetting.png)

    > [!NOTE]
    > Quando o dispositivo reconhece uma alteração da definição, o estado da definição muda para **sincronizado**.

4. Pode personalizar o esquema da página **Definições** ao mover e redimensionar mosaicos de definições:

    ![Personalizar esquema de definições](./media/tutorial-define-device-type/settingslayout.png)

## <a name="use-properties"></a>Utilizar propriedades 

Utilize *propriedades da aplicação* para armazenar informações sobre o seu dispositivo na aplicação. Nesta secção, irá adicionar as propriedades da aplicação ao modelo de dispositivo **Ar Condicionado Ligado** para armazenar a localização do dispositivo e a data da última assistência. Tenha em atenção que ambas as propriedades do dispositivo são editáveis. Existem também propriedades do dispositivo só de leitura comunicadas pelo dispositivo que não podem ser alteradas, como o número de série e a versão de firmware.
 
1. Navegue para a página **Propriedades** para o seu modelo de dispositivo **Ar Condicionado Ligado**:

    ![Preparar para adicionar uma propriedade](./media/tutorial-define-device-type/deviceaddproperty.png)

    Pode criar propriedades do dispositivo de diferentes tipos, como texto ou números. Para adicionar uma propriedade de localização ao modelo de dispositivo, escolha **Localização**.

1. Para configurar a propriedade da localização, utilize as informações na tabela seguinte:

    | Campo                | Value                |
    | -------------------- | -------------------- |
    | Nome a Apresentar         | Localização             |
    | Nome do Campo           | localização             |
    | Valor Inicial        | Seattle, WA          |
    | Descrição          | Localização do dispositivo      |

    Deixe os outros campos com os respetivos valores predefinidos.

    ![Configurar as propriedades do dispositivo](./media/tutorial-define-device-type/configureproperties.png)

    Escolha **Guardar**.

1. Para adicionar uma propriedade da data da última assistência ao modelo de dispositivo, escolha **Data**.

1. Para configurar a propriedade da data da última assistência, utilize as informações na tabela seguinte:

    | Campo                | Value                   |
    | -------------------- | ----------------------- |
    | Nome a Apresentar         | Data da Última Assistência       |
    | Nome do Campo           | serviceDate             |
    | Valor Inicial        | 01/01/2018                |
    | Descrição          | Última assistência           |

    ![Configurar as propriedades do dispositivo](./media/tutorial-define-device-type/configureproperties2.png)

    Escolha **Guardar**.

5. Pode personalizar o esquema da página **Propriedades** ao mover e redimensionar mosaicos de propriedades:

    ![Personalizar esquema de propriedades](./media/tutorial-define-device-type/propertieslayout.png)

1. Para adicionar uma propriedade de dispositivo, como a versão de firmware, ao modelo de dispositivo, selecione **Propriedade do Dispositivo**.

1.  Para configurar a versão de firmware, utilize as informações na tabela seguinte:

    | Campo                | Value                   |
    | -------------------- | ----------------------- |
    | Nome a Apresentar         | Versão do firmware        |
    | Nome do Campo           | firmwareVersion         |
    | Tipo de Dados            | texto                    |
    | Descrição          | A versão de firmware do ar condicionado |

    ![Configurar a versão de firmware](./media/tutorial-define-device-type/configureproperties3.png)
    
    Escolha **Guardar**.

1. Para adicionar uma propriedade de dispositivo, como o número de série, ao modelo de dispositivo, selecione **Propriedade do Dispositivo**.

1. Para configurar o número de série, utilize as informações na tabela seguinte:

    | Campo                | Value                   |
    | -------------------- | ----------------------- |
    | Nome a Apresentar         | Número de série           |
    | Nome do Campo           | serialNumber            |
    | Tipo de Dados            | texto                    |
    | Descrição          | O número de série do ar condicionado  |

    ![Configurar o número de série](./media/tutorial-define-device-type/configureproperties4.png)
    
    Escolha **Guardar**.
    
    > [!NOTE]
    > A Propriedade do Dispositivo é enviada do dispositivo para a aplicação. Os valores da versão de firmware e do número de série serão atualizados quando o dispositivo real ligar ao IoT Central.

## <a name="use-commands"></a>Utilizar comandos

Pode utilizar _comandos_ para ativar um operador para executar comandos diretamente no dispositivo. Nesta secção, vai adicionar um comando ao seu modelo de dispositivo **Ar Condicionado Ligado** que permite a um operador apresentar uma determinada mensagem no ecrã do ar condicionado ligado.

1. Navegue para a página **Comandos** para o seu modelo de dispositivo **Ar Condicionado Ligado** para editar o modelo. 

1. Selecione **novo comando** para adicionar um comando para o seu dispositivo e começar a configurar o seu novo comando.

   Pode criar comandos de tipos diferentes com base nos seus requisitos. 

1. Para configurar o novo comando, utilize as informações na tabela seguinte:

    | Campo                | Value           |
    | -------------------- | -----------     |
    | Nome a Apresentar         | Comando Echo    |
    | Nome do Campo           | echo            |
    | Tempo Limite Predefinido      | 30              |
    | Tipo a Apresentar         | texto            |
    | Descrição          | Comando do Dispositivo  |  

    Pode adicionar entradas adicionais para o comando selecionando **+** para **campos de entrada**.

    ![Preparar para adicionar uma definição](media/tutorial-define-device-type/commandsecho1.png)

     Escolha **Guardar**.

1. Pode personalizar o esquema da página **Comandos** ao mover e redimensionar mosaicos de comandos:

    ![Personalizar esquema de definições](media/tutorial-define-device-type/commandstileresize1.png)

## <a name="view-your-simulated-device"></a>Veja o seu dispositivo simulado

Agora que definiu o seu modelo de dispositivo do **Ar Condicionado Ligado**, pode personalizar o **Dashboard** para incluir as medidas, definições e propriedades que definiu. Em seguida, pode pré-visualizar o dashboard como um operador:

1. Escolha a página **Dashboard** para o seu modelo de dispositivo **Ar Condicionado Ligado**:

    ![Dashboards de ar condicionado ligado](./media/tutorial-define-device-type/aircondashboards.png)

1. Escolha **Gráfico de Linhas** para adicionar o componente ao **Dashboard**:

    ![Componentes do dashboard](./media/tutorial-define-device-type/dashboardcomponents1.png)

1. Configure o componente **Gráfico de Linhas** com as informações na tabela seguinte:

    | Definição      | Value       |
    | ------------ | ----------- |
    | Título        | Temperatura |
    | Intervalo de Tempo   | Últimos 30 minutos |
    | Medidas     | temperatura (escolha **Visibilidade** junto a **temperatura**) |

    ![Definições do gráfico de linhas](./media/tutorial-define-device-type/linechartsettings.png)

    Em seguida, escolha **Guardar**.

1. Configure o componente **Histórico de Eventos** com as informações na tabela seguinte:

    | Definição      | Value       |
    | ------------ | ----------- |
    | Título        | Eventos |
    | Intervalo de Tempo   | Últimos 30 minutos |
    | Medidas     | Erro do Motor da Ventoinha (escolha **Visibilidade** junto a **Erro do Motor da Ventoinha**) |

    ![Definições do gráfico de linhas](./media/tutorial-define-device-type/dashboardeventchartsetting.png)

    Em seguida, escolha **Guardar**.

1. Configure o componente **Histórico de Estado** com as informações na tabela seguinte:

    | Definição      | Value       |
    | ------------ | ----------- |
    | Título        | Modo da Ventoinha |
    | Intervalo de Tempo   | Últimos 30 minutos |
    | Medidas | Modo da Ventoinha (escolha **Visibilidade** junto a **Modo da Ventoinha**) |

    ![Definições do gráfico de linhas](./media/tutorial-define-device-type/dashboardstatechartsetting.png)

    Em seguida, escolha **Guardar**.

1. Para adicionar a opção para definir a temperatura do dashboard, selecione **Definições e Propriedades**. Selecione **Adicionar/remover** para adicionar as definições ou propriedades que gostaria de ver no dashboard. 

    ![Componentes do dashboard](./media/tutorial-define-device-type/dashboardcomponents4.png)

1. Configure o componente **Definições e Propriedades** com as informações na tabela seguinte:

    | Definição                 | Value         |
    | ----------------------- | ------------- |
    | Título                   | Definir temperatura de destino |
    | Definições e Propriedades | Definir Temperatura |

    As definições e propriedades que definiu anteriormente nas páginas Definições e Propriedades são apresentadas nas Colunas Disponíveis. 

    ![Definir as propriedades de temperatura](./media/tutorial-define-device-type/propertysettings4.png)

    Em seguida, selecione **OK**.

1. Para adicionar o número de série do dispositivo ao dashboard, selecione **Definições e Propriedades**:

    ![Componentes do dashboard](./media/tutorial-define-device-type/dashboardcomponents3.png)

1. Configure o componente **Definições e Propriedades** com as informações na tabela seguinte:

    | Definição                 | Value         |
    | ----------------------- | ------------- |
    | Título                   | Número de série |
    | Definições e Propriedades | Número de série |

    ![Definições de propriedade do número de série](./media/tutorial-define-device-type/propertysettings5.png)

    Em seguida, selecione **OK**.

1. Para adicionar a versão de firmware do dispositivo ao dashboard, selecione **Definições e Propriedades**:

    ![Componentes do dashboard](./media/tutorial-define-device-type/dashboardcomponents4.png)

1. Configure o componente **Definições e Propriedades** com as informações na tabela seguinte:

    | Definição                 | Value            |
    | ----------------------- | ---------------- |
    | Título                   | Versão do firmware |
    | Definições e Propriedades | Versão do firmware |

    ![Definições de propriedade do número de série](./media/tutorial-define-device-type/propertysettings6.png)

    Em seguida, selecione **OK**.

1. Para ver o dashboard como operador, desative **Editar Modelo** na parte superior direita da página.

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

Agora que definiu um modelo do dispositivo na sua aplicação do Azure IoT Central, eis os passos seguintes sugeridos:

* [Configurar regras e ações para o seu dispositivo](tutorial-configure-rules.md)
* [Personalizar as vistas do operador](tutorial-customize-operator.md)

[lnk-define-template]: /azure/iot-central/howto-set-up-template#properties