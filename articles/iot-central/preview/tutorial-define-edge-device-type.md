---
title: Tutorial – definir um novo tipo de dispositivo Azure IoT Edge no Azure IoT Central
description: Este tutorial mostra, como um construtor, como criar um novo dispositivo de Azure IoT Edge em seu aplicativo de IoT Central do Azure. Você define a telemetria, o estado, as propriedades e os comandos para seu tipo.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 97bfd2b1e8b571f44c0b782459567f5677dd36a7
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74702797"
---
# <a name="tutorial-define-a-new-azure-iot-edge-device-type-in-your-azure-iot-central-application-preview-features"></a>Tutorial: definir um novo tipo de dispositivo Azure IoT Edge em seu aplicativo de IoT Central do Azure (recursos de visualização)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este tutorial mostra, como um construtor, como usar um modelo de dispositivo para definir um novo tipo de dispositivo de Azure IoT Edge em seu aplicativo de IoT Central do Azure. 

Para obter uma visão geral, consulte [o que é o Azure IOT central (recursos de visualização)?](overview-iot-central.md). 

IoT Edge é composta por três componentes:
* Os **módulos de IOT Edge** são contêineres que executam serviços do Azure, serviços de parceiros ou seu próprio código. Os módulos são implantados em dispositivos IoT Edge e executados localmente nesses dispositivos.
* O **tempo de execução do IOT Edge** é executado em cada dispositivo IOT Edge e gerencia os módulos implantados em cada dispositivo.
* Uma **interface baseada em nuvem** permite que você monitore e gerencie dispositivos IOT Edge remotamente. IoT Central é a interface de nuvem.

Um dispositivo **Azure IOT Edge** pode ser um dispositivo de gateway, com dispositivos downstream conectando-se ao dispositivo IOT Edge. Este tutorial compartilha mais informações sobre os padrões de conectividade do dispositivo downstream.

Um **modelo de dispositivo** define os recursos do seu dispositivo e módulos de IOT Edge. Os recursos incluem telemetria que o módulo envia, propriedades do módulo e os comandos que um módulo responde.

Neste tutorial, você criará um modelo de dispositivo de sensor de ambiente. Um dispositivo de sensor ambiental:

* Envia telemetria, como temperatura.
* Responde às propriedades graváveis quando atualizado na nuvem, como o intervalo de envio de telemetria.
* Responde a comandos, como redefinição de temperatura.

Além disso, neste tutorial, você cria um modelo de dispositivo de gateway de ambiente. Um dispositivo de gateway ambiental:

* Envia telemetria, como temperatura.
* Responde às propriedades graváveis quando atualizado na nuvem, como o intervalo de envio de telemetria.
* Responde a comandos, como redefinição de temperatura.
* Permite relações com outros modelos de capacidade de dispositivo.


Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie um novo modelo de dispositivo de dispositivo Azure IoT Edge.
> * Carregar um manifesto de implantação.
> * Crie recursos, incluindo telemetria, propriedades e comandos para cada módulo.
> * Defina uma visualização para a telemetria do módulo.
> * Adicionar relações aos modelos de dispositivo downstream.
> * Publique seu modelo de dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa [criar um aplicativo de IOT central do Azure](quick-deploy-iot-central.md).


## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Relações de dispositivo downstream com um gateway e módulos

Os dispositivos downstream podem se conectar a um dispositivo de gateway IoT Edge por meio do módulo `$edgeHub`. Esse IoT Edge dispositivo torna-se um gateway transparente nesse cenário.

![Diagrama de gateway transparente](./media/tutorial-define-edge-device-type/gateway-transparent.png)

Os dispositivos downstream também podem se conectar a um dispositivo de gateway IoT Edge por meio de um módulo personalizado. No cenário a seguir, os dispositivos downstream se conectam por meio de um módulo personalizado Modbus.

![Diagrama da conexão de módulo personalizada](./media/tutorial-define-edge-device-type/gateway-module.png)

O diagrama a seguir mostra a conexão a um dispositivo de gateway IoT Edge por meio de ambos os tipos de módulos (personalizado e `$edgeHub`).  

![Diagrama de conexão por meio de ambos os módulos de conexão](./media/tutorial-define-edge-device-type/gateway-module-transparent.png)

Por fim, os dispositivos downstream podem se conectar a um dispositivo de gateway IoT Edge por meio de vários módulos personalizados. O diagrama a seguir mostra os dispositivos downstream que se conectam por meio de um módulo personalizado MODBUS, um módulo BLE personalizado e o módulo `$edgeHub`. 

![Diagrama de conexão por meio de vários módulos personalizados](./media/tutorial-define-edge-device-type/gateway-module2-transparent.png)


## <a name="create-a-template"></a>Criar um modelo

Como um construtor, você pode criar e editar IoT Edge modelos de dispositivo em seu aplicativo. Depois de publicar um modelo de dispositivo, você pode conectar dispositivos reais que implementam o modelo de dispositivo.

### <a name="select-device-template-type"></a>Selecionar tipo de modelo de dispositivo 

Para adicionar um novo modelo de dispositivo ao seu aplicativo, selecione **modelos de dispositivo** no painel esquerdo.

![Captura de tela do aplicativo de visualização, com modelos de dispositivo realçado](./media/tutorial-define-edge-device-type/edgedevicetemplate.png)

Selecione **+ novo** para começar a criar um novo modelo de dispositivo.

![Captura de tela da página modelos de dispositivo, com novo realçado](./media/tutorial-define-edge-device-type/edgedevicetemplatenew.png)

Na página **Selecionar tipo de modelo** , selecione **Azure IOT Edge**e selecione **Avançar: Personalizar**.

![Captura de tela da página Selecionar tipo de modelo](./media/tutorial-define-edge-device-type/selectiotedge.png)

### <a name="customize-device-template"></a>Personalizar modelo de dispositivo

No IoT Edge, você pode implantar e gerenciar a lógica de negócios na forma de módulos. Os módulos de IoT Edge são a menor unidade de computação gerenciada pelo IoT Edge e podem conter serviços do Azure (como Azure Stream Analytics) ou seu próprio código específico da solução. Para entender como os módulos são desenvolvidos, implantados e mantidos, consulte [módulos IOT Edge](../../iot-edge/iot-edge-modules.md).

Em um alto nível, um manifesto de implantação é uma lista de gêmeos de módulo configurados com suas propriedades desejadas. Um manifesto de implantação informa um dispositivo IoT Edge (ou um grupo de dispositivos) quais módulos instalar e como configurá-los. Os manifestos de implantação incluem as propriedades desejadas para cada módulo. IoT Edge dispositivos reportam as propriedades relatadas para cada módulo.

Use Visual Studio Code para criar um manifesto de implantação. Para saber mais, confira [Azure IOT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

Aqui está um manifesto de implantação básica, com um módulo como um exemplo a ser usado neste tutorial. Copie o JSON a seguir e salve-o como um arquivo. JSON. 

   ```JSON
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "SimulatedTemperatureSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {
              "SendData": true,
              "SendInterval": 10
         }
       }
     }
   }
   ```

#### <a name="upload-an-iot-edge-deployment-manifest"></a>Carregar um manifesto de implantação de IoT Edge

Na página **Personalizar dispositivo** , em **carregar um manifesto de implantação do Azure IOT Edge**, selecione **procurar**. 

![Captura de tela da página Personalizar dispositivo, com navegar realçado](./media/tutorial-define-edge-device-type/edgedevicetemplateuploadmanifest.png)

Se você planeja criar um modelo de dispositivo de gateway IoT Edge, certifique-se de selecionar **dispositivo de gateway com dispositivos downstream**.

![Captura de tela da página Personalizar dispositivo, com o dispositivo de gateway com dispositivos downstream realçado](./media/tutorial-define-edge-device-type/gateway-upload.png)

Na caixa de diálogo seleção de arquivo, selecione o arquivo de manifesto de implantação e selecione **abrir**.

IoT Edge valida o arquivo de manifesto de implantação em relação a um esquema. Se a validação for bem-sucedida, selecione **examinar**.

![Captura de tela da página Personalizar dispositivo, com manifesto de implantação e revisão realçada](./media/tutorial-define-edge-device-type/deploymentmanifestvalidate.png)

O fluxograma a seguir mostra um ciclo de vida de manifesto de implantação no IoT Central.

![Fluxograma do ciclo de vida de manifesto de implantação](./media/tutorial-define-edge-device-type/dmflow.png)

Em seguida, você verá uma página de revisão, com detalhes do manifesto de implantação. Esta página mostra uma lista de módulos do manifesto de implantação. Neste tutorial, observe que o módulo `SimulatedTemperatureSensor` está listado. Selecione **Criar**.

![Captura de tela da página de revisão, com módulo e criar realçadas](./media/tutorial-define-edge-device-type/edgedevicetemplatereview.png)

Se você selecionou um dispositivo de gateway, verá a página de revisão a seguir.

![Captura de tela da página de revisão, com Azure IoT Edge gateway realçado](./media/tutorial-define-edge-device-type/gateway-review.png)


Você cria um modelo de dispositivo com modelos de funcionalidade de módulo. Neste tutorial, você cria um modelo de dispositivo com o modelo de funcionalidade do módulo `SimulatedTemperatureSensor`. 

Altere o título do modelo de dispositivo para **modelo de dispositivo do sensor de ambiente**.

![Captura de tela do modelo de dispositivo, com título atualizado realçado](./media/tutorial-define-edge-device-type/edgedevicetemplatelanding.png)

Em IoT Edge dispositivo, modelo de IoT Plug and Play da seguinte maneira:
* Cada modelo de dispositivo IoT Edge tem um modelo de funcionalidade de dispositivo.
* Para cada módulo personalizado listado no manifesto de implantação, um modelo de funcionalidade de módulo é gerado.
* Uma relação é estabelecida entre cada modelo de funcionalidade de módulo e um modelo de capacidade de dispositivo.
* Um modelo de funcionalidade de módulo implementa interfaces de módulo.
* Cada interface de módulo contém telemetria, propriedades e comandos.

![Diagrama de modelagem de IoT Edge](./media/tutorial-define-edge-device-type/edgemodelling.png)

#### <a name="add-capabilities-to-a-module-capability-model"></a>Adicionar recursos a um modelo de funcionalidade de módulo

Aqui está um exemplo de saída do módulo `SimulatedTemperatureSensor`:
```json
{

    "machine": {

        "temperature": 75.0,
        "pressure": 40.2
    },
    "ambient": {
        "temperature": 23.0,
        "humidity": 30.0
    },
    "timeCreated": ""
}
```

Você pode adicionar recursos ao módulo `SimulatedTemperatureSensor`, que refletirá a saída anterior. 

1. Para gerenciar uma interface do modelo de funcionalidade do módulo `SimulatedTemperatureSensor`, selecione **gerenciar** > **recurso Adicionar**. 

    ![Captura de tela do modelo de sensor de ambiente, com adição de funcionalidade realçada](./media/tutorial-define-edge-device-type/edgetemplateaddcapability.png)
  
1. Adicione um computador como um tipo de objeto.
  
    ![Captura de tela da página de recursos do modelo do sensor de ambiente, com esquema realçado](./media/tutorial-define-edge-device-type/edgetemplatemachineobject.png)

1. Selecione **definir**. Na caixa de diálogo exibida, altere o nome do objeto para **computador**. Crie Propriedades de temperatura e pressão e selecione **aplicar**.
  
    ![Captura de tela da caixa de diálogo atributos, com várias opções realçadas](./media/tutorial-define-edge-device-type/edgetemplatemachineattributes.png)
  
1. Adicionar **ambiente** como um tipo de objeto.

1. Selecione **definir**. Na caixa de diálogo que aparece, altere o nome do objeto para **ambiente**. Crie Propriedades de temperatura e umidade e selecione **aplicar**.
  
    ![Captura de tela da caixa de diálogo atributos, com várias opções realçadas](./media/tutorial-define-edge-device-type/edgetemplateambientattributes.png)

  
1. Adicione `timeCreated` como um tipo de `DateTime` e selecione **salvar**.
  
    ![Captura de tela do modelo de sensor de ambiente, com salvar realçado](./media/tutorial-define-edge-device-type/edgetemplateallattributes.png)


### <a name="add-relationships"></a>Adicionar relações

Se você selecionou um dispositivo de IoT Edge para ser um dispositivo de gateway, poderá adicionar relações de downstream a modelos de capacidade de dispositivo para dispositivos que você deseja conectar ao dispositivo de gateway.
  
  ![Captura de tela do modelo de gateway de ambiente, com adicionar relação realçado](./media/tutorial-define-edge-device-type/gateway-add-relationship.png)

Você pode adicionar uma relação em um dispositivo ou em um módulo.
  
  ![Captura de tela do modelo de gateway de ambiente, com relações de nível de dispositivo e módulo realçadas](./media/tutorial-define-edge-device-type/gateway-relationship-types.png)


Você pode selecionar um modelo de funcionalidade de dispositivo downstream ou pode selecionar o símbolo de asterisco. 
  
  ![Captura de tela do modelo de gateway de ambiente, com destino realçado](./media/tutorial-define-edge-device-type/gateway-downstream-rel.png)

  Para este tutorial, selecione o asterisco. Essa opção permite qualquer relação de downstream. Em seguida, selecione **Guardar**.

  ![Captura de tela do modelo de gateway de ambiente, com destino realçado](./media/tutorial-define-edge-device-type/gateway-add-relationship-asterix.png)


### <a name="add-cloud-properties"></a>Adicionar propriedades de nuvem

Um modelo de dispositivo pode incluir propriedades de nuvem. As propriedades de nuvem existem somente no aplicativo IoT Central e nunca são enviadas ou recebidas de um dispositivo.

1. Selecione **Propriedades de nuvem** >  **+ Adicionar Propriedade de nuvem**. Use as informações na tabela a seguir para adicionar uma propriedade de nuvem ao modelo de dispositivo.

    | Nome a apresentar      | Tipo semântico | Esquema |
    | ----------------- | ------------- | ------ |
    | Data da Última Assistência | Nenhuma          | Date   |
    | Nome do cliente     | Nenhuma          | String |

2. Selecione **Guardar**.

  
    ![Captura de tela do modelo de sensor de ambiente, com salvar realçado](./media/tutorial-define-edge-device-type/edgetemplatecloudproperties.png)

### <a name="add-customizations"></a>Adicionar personalizações

Use personalizações para modificar uma interface ou para adicionar recursos específicos do IoT Central a uma funcionalidade que não exige a versão do modelo de capacidade do dispositivo. Você pode personalizar os campos quando o modelo de funcionalidade estiver em um rascunho ou em um estado publicado. Você só pode personalizar campos que não interrompem a compatibilidade de interface. Por exemplo, pode:

- Personalize o nome de exibição e as unidades de um recurso.
- Adicione uma cor padrão a ser usada quando o valor for exibido em um gráfico.
- Especifique os valores iniciais, mínimos e máximos para uma propriedade.

Você não pode personalizar o nome da funcionalidade ou o tipo de funcionalidade.

Quando tiver terminado de personalizar, selecione **salvar**.
  
![Captura de tela da página Personalizar modelo do sensor de ambiente](./media/tutorial-define-edge-device-type/edgetemplatecustomize.png)


### <a name="create-views"></a>Criar exibições

Como um construtor, você pode personalizar o aplicativo para exibir informações relevantes sobre o dispositivo do sensor ambiental para um operador. Suas personalizações permitem que o operador gerencie os dispositivos do sensor ambiental conectados ao aplicativo. Você pode criar dois tipos de modos de exibição para que um operador Use para interagir com dispositivos:

* Formulários para exibir e editar propriedades de dispositivo e de nuvem.
* Painéis para visualizar dispositivos.

### <a name="configure-a-view-to-visualize-devices"></a>Configurar uma exibição para visualizar dispositivos

Um painel de dispositivos permite que um operador visualize um dispositivo usando gráficos e métricas. Como um construtor, você pode definir quais informações são exibidas em um painel do dispositivo. Você pode definir vários painéis para dispositivos. Para criar um painel para visualizar a telemetria do sensor ambiental, selecione **exibições** > **visualização do dispositivo**:

  
![Captura de tela da página exibições do modelo do sensor de ambiente, com a visualização do dispositivo realçado](./media/tutorial-define-edge-device-type/visualizingthedevice.png)


A telemetria de ambiente e a telemetria de máquina são objetos complexos. Para criar gráficos:

1. Arraste **telemetria de ambiente**e selecione **gráfico de linhas**. 
  
   ![Captura de tela do modelo de sensor de ambiente, com telemetria ambiental e gráfico de linhas realçado](./media/tutorial-define-edge-device-type/sensorambientchart.png)

1. Selecione o ícone configurar. Selecione **temperatura** e **umidade** para visualizar os dados e selecione **Atualizar configuração**. 
  
   ![Captura de tela do modelo de sensor de ambiente, com várias opções realçadas](./media/tutorial-define-edge-device-type/sensorambienttelemetrychart.png)

1. Selecione **Guardar**.

Você pode adicionar mais blocos que mostram outras propriedades ou valores de telemetria. Você também pode adicionar texto estático, links e imagens. Para mover ou redimensionar um bloco no painel, mova o ponteiro do mouse sobre o bloco e arraste o bloco para um novo local ou redimensione-o.
  
![Captura de tela da exibição de painel do modelo do sensor de ambiente](./media/tutorial-define-edge-device-type/viewsdashboard.png)

### <a name="add-a-device-form"></a>Adicionar um formulário de dispositivo

Um formulário de dispositivo permite que um operador Edite propriedades de dispositivo gravável e propriedades de nuvem. Como um construtor, você pode definir vários formulários e escolher quais propriedades de dispositivo e de nuvem mostrar em cada formulário. Você também pode exibir propriedades de dispositivo somente leitura em um formulário.

Para criar um formulário para exibir e editar as propriedades do sensor ambiental:

1. No **modelo de sensor ambiental**, vá para **exibições**. Selecione o bloco **dispositivo de edição e dados de nuvem** para adicionar uma nova exibição.
  
   ![Captura de tela da página exibições de modelo de sensor ambiental, com a edição de dispositivo e dados na nuvem](./media/tutorial-define-edge-device-type/editingdeviceandclouddata.png)

1. Insira o nome do formulário **Propriedades do sensor ambiental**.

1. Arraste as propriedades de nuvem **nome do cliente** e **última data de serviço** até a seção existente no formulário.
  
   ![Captura de tela da página exibições do modelo do sensor ambiental, com várias opções realçadas](./media/tutorial-define-edge-device-type/views-properties.png)

1. Selecione **Guardar**.

## <a name="publish-a-device-template"></a>Publicar um modelo de dispositivo

Para poder criar um sensor ambiental simulado ou conectar um sensor ambiental real, você precisa publicar o modelo do dispositivo.

Para publicar um modelo de dispositivo:

1. Vá para o modelo de dispositivo na página **modelos de dispositivo** .

2. Selecione **Publicar**.
  
    ![Captura de tela do modelo de sensor ambiental, com a publicação realçada](./media/tutorial-define-edge-device-type/edgetemplatepublish.png)

1. Na caixa de diálogo **publicar um modelo de dispositivo** , escolha **publicar**.
  
    ![Captura de tela da caixa de diálogo publicar um modelo de dispositivo, com a publicação realçada](./media/tutorial-define-edge-device-type/edgepublishtemplate.png)

Depois que um modelo de dispositivo é publicado, ele fica visível na página **dispositivos** e no operador. Em um modelo de dispositivo publicado, você não pode editar um modelo de capacidade de dispositivo sem criar uma nova versão. No entanto, você pode fazer atualizações nas propriedades de nuvem, personalizações e exibições, em um modelo de dispositivo publicado. Essas atualizações não fazem com que uma nova versão seja criada. Depois de fazer alterações, selecione **publicar** para enviar por push essas alterações para o operador.
  
![Captura de tela de modelos de dispositivo lista de modelos publicados](./media/tutorial-define-edge-device-type/publishedtemplate.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

* Crie uma nova borda como um modelo de dispositivo folha.
* Gere módulos de um manifesto de implantação carregado.
* Adicione telemetria e propriedades de tipo complexo.
* Criar propriedades de nuvem.
* Criar personalizações.
* Defina uma visualização para a telemetria do dispositivo.
* Publique o modelo de dispositivo do Edge.

Agora que você criou um modelo de dispositivo em seu aplicativo de IoT Central do Azure, poderá fazer o seguinte:

> [!div class="nextstepaction"]
> [Conectar dispositivo](./tutorial-connect-pnp-device.md)
