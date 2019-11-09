---
title: Definir um novo tipo de dispositivo Azure IoT Edge no Azure IoT Central | Microsoft Docs
description: Este tutorial mostra, como um construtor, como criar um novo dispositivo de Azure IoT Edge em seu aplicativo de IoT Central do Azure. Você define a telemetria, o estado, as propriedades e os comandos para seu tipo.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: c205b4dd4871ed53e32dce72f12cc2dcfb3baf41
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73893054"
---
# <a name="tutorial-define-a-new-azure-iot-edge-device-type-in-your-azure-iot-central-application-preview-features"></a>Tutorial: definir um novo tipo de dispositivo Azure IoT Edge em seu aplicativo de IoT Central do Azure (recursos de visualização)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este tutorial mostra, como um construtor, como usar um modelo de dispositivo para definir um novo tipo de dispositivo de Azure IoT Edge em seu aplicativo de IoT Central do Azure. 

Para obter uma visão geral do Azure IoT Edge, [consulte este artigo](overview-iot-central.md). 

O Azure IoT Edge é composto por três componentes:
* Os **módulos de IOT Edge** são contêineres que executam serviços do Azure, serviços de terceiros ou seu próprio código. Os módulos são implantados em dispositivos IoT Edge e executados localmente nesses dispositivos.
* O **tempo de execução de IOT Edge** é executado em cada dispositivo IOT Edge e gerencia os módulos implantados em cada dispositivo.
* Uma **interface baseada em nuvem** permite que você monitore e gerencie dispositivos IOT Edge remotamente. IoT Central será a interface de nuvem.

Um dispositivo **Azure IOT Edge** pode ser um dispositivo de gateway com dispositivos downstream conectados ao dispositivo Azure IOT Edge. Os padrões de conectividade do dispositivo downstream serão discutidos neste tutorial.

Um **modelo de dispositivo** define os recursos do seu dispositivo & módulos IOT Edge. Os recursos incluem telemetria que o módulo envia, propriedades do módulo e os comandos que um módulo responde.

Neste tutorial, você criará um modelo de dispositivo de **sensor de ambiente** . Um dispositivo de sensor ambiental:

* Envia telemetria como temperatura.
* Responde às propriedades graváveis quando atualizado na nuvem, como o intervalo de envio de telemetria.
* Responde a comandos como a redefinição da temperatura.

Além disso, neste tutorial, você cria um modelo de dispositivo de **Gateway de ambiente** . Um dispositivo de gateway ambiental:

* Envia telemetria como temperatura.
* Responde às propriedades graváveis quando atualizado na nuvem, como o intervalo de envio de telemetria.
* Responde a comandos como a redefinição da temperatura.
* Permite relações com outros modelos de capacidade de dispositivo


Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie um novo modelo de dispositivo de dispositivo Azure IoT Edge.
> * Carregar um manifesto de implantação.
> * Criar recursos, incluindo telemetria, propriedades e comandos para cada módulo
> * Defina uma visualização para a telemetria do módulo.
> * Adicionar relações aos modelos de dispositivo downstream
> * Publique seu modelo de dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma aplicação do Azure IoT Central. Siga este guia de início rápido para [criar um aplicativo de IOT central do Azure](quick-deploy-iot-central.md).


## <a name="downstream-device-relationships-with-gateway--modules"></a>Relações de dispositivo downstream com módulos de & de gateway

Os dispositivos downstream podem se conectar a Azure IoT Edge dispositivo de gateway por meio do módulo $edgeHub. Esse Azure IoT Edge dispositivo se torna um gateway transparente neste cenário

![Página do aplicativo Central](./media/tutorial-define-edge-device-type/gateway-transparent.png)

Os dispositivos downstream podem se conectar a Azure IoT Edge dispositivo de gateway por meio de um módulo personalizado. No cenário abaixo, os dispositivos downstream estão se conectando por meio de um módulo personalizado Modbus e os dispositivos downstream podem se conectar a Azure IoT Edge dispositivo de gateway por meio do módulo $edgeHub.

![Página do aplicativo Central](./media/tutorial-define-edge-device-type/gateway-module.png)

Os dispositivos downstream podem se conectar a Azure IoT Edge dispositivo de gateway por meio de um módulo personalizado. No cenário abaixo, os dispositivos downstream estão se conectando por meio de um módulo personalizado Modbus. 

![Página do aplicativo Central](./media/tutorial-define-edge-device-type/gateway-module-transparent.png)

Os dispositivos downstream podem se conectar a Azure IoT Edge dispositivo de gateway por meio de vários módulos personalizados. No cenário abaixo, os dispositivos downstream estão se conectando por meio de um módulo personalizado MODBUS, BLE módulo personalizado e dispositivos downstream podem se conectar a Azure IoT Edge dispositivo de gateway por meio do módulo $edgeHub. 

![Página do aplicativo Central](./media/tutorial-define-edge-device-type/gateway-module2-transparent.png)


## <a name="create-a-template"></a>Criar um modelo

Como um construtor, você pode criar e editar Azure IoT Edge modelos de dispositivo em seu aplicativo. Depois de publicar um modelo de dispositivo, você pode conectar dispositivos reais que implementam o modelo de dispositivo.

### <a name="select-device-template-type"></a>Selecionar tipo de modelo de dispositivo 

Para adicionar um novo modelo de dispositivo ao seu aplicativo, vá para a página **modelos de dispositivo** . Para fazer isso, selecione a guia **modelos de dispositivo** no painel esquerdo.

![Página do aplicativo Central](./media/tutorial-define-edge-device-type/edgedevicetemplate.png)

Clique em **+ novo** para começar a criar um novo modelo de dispositivo.

![Modelos de dispositivo – novo](./media/tutorial-define-edge-device-type/edgedevicetemplatenew.png)

Você vai para a página seleção de tipo de modelo de dispositivo. Selecione **Azure IOT Edge** bloco e clique em **Avançar: botão Personalizar** na parte inferior

![Seleção de modelos de dispositivo-Azure IoT Edge](./media/tutorial-define-edge-device-type/selectiotedge.png)

### <a name="customize-device-template"></a>Personalizar modelo de dispositivo

Azure IoT Edge permite implantar e gerenciar a lógica de negócios na forma de módulos. Os **módulos de Azure IOT Edge** são a menor unidade de computação gerenciada pelo IOT Edge e podem conter serviços do Azure (como Azure Stream Analytics) ou seu próprio código específico da solução. Para entender como os módulos são desenvolvidos, implantados e mantidos [IOT Edge módulos](../../iot-edge/iot-edge-modules.md)de leitura.

Em um alto nível, um manifesto de implantação é uma lista de gêmeos de módulo configurados com suas propriedades desejadas. Um manifesto de implantação informa a um dispositivo IoT Edge (ou um grupo de dispositivos) quais módulos instalar e como configurá-los. Os manifestos de implantação incluem as propriedades desejadas para cada módulo. IoT Edge dispositivos reportam as propriedades relatadas para cada módulo.

Use Visual Studio Code para criar um manifesto de implantação. Siga a documentação sobre como criar um [manifesto de implantação](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

Aqui está um manifesto de implantação básica com um módulo como um exemplo a ser usado neste tutorial. Copie o JSON abaixo e salve-o como arquivo. JSON. 

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

**Carregar um manifesto de implantação de Azure IoT Edge**

Clique no botão **procurar** 

![Modelo de dispositivo-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplateuploadmanifest.png)

Se você planeja criar um modelo de dispositivo de gateway Azure IoT Edge, certifique-se de selecionar o **dispositivo de gateway com dispositivos downstream**

![Modelo de dispositivo-Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-upload.png)

Uma caixa de diálogo de seleção de arquivo será exibida. Selecione o arquivo de manifesto de implantação e clique no botão **abrir** .

O arquivo de manifesto de implantação será validado em relação a um esquema. Após validação bem-sucedida, clique no botão **examinar**

![Modelo de dispositivo-Azure IoT Edge](./media/tutorial-define-edge-device-type/deploymentmanifestvalidate.png)

Abaixo está o fluxo de um ciclo de vida de manifesto de implantação no IoT Central.

![Modelo de dispositivo-Azure IoT Edge](./media/tutorial-define-edge-device-type/dmflow.png)

A página revisão é exibida com detalhes do manifesto de implantação. A lista de módulos do manifesto de implantação será exibida na página revisão. Neste tutorial, você verá o módulo SimulatedTemperatureSensor listado. Clique no botão **criar** .

![Modelo de dispositivo-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplatereview.png)

Se você selecionou dispositivo de gateway, verá esta página de revisão

![Modelo de dispositivo-Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-review.png)


A criação de um novo controle giratório de modelo de dispositivo será exibida em que o modelo de dispositivo está sendo criado no IoT Central.

O modelo de dispositivo é criado com modelos de capacidade de módulo. Neste tutorial, você verá o modelo de funcionalidade de módulo SimulatedTemperatureSensor Create. 

Altere o título do modelo de dispositivo para modelo de dispositivo do sensor de ambiente.

![Modelo de dispositivo-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplatelanding.png)

Azure IoT Edge modelagem do dispositivo plug n play é feita da seguinte maneira
* Cada modelo de dispositivo Azure IoT Edge terá um **modelo de capacidade de dispositivo**
* Para cada módulo personalizado listado no manifesto de implantação, um **modelo de funcionalidade de módulo** será gerado
* Uma **relação** será estabelecida entre cada modelo de funcionalidade de módulo e um modelo de capacidade de dispositivo
* O modelo de funcionalidade do módulo implementa **interfaces de módulo**
* Cada interface de módulo contém
   - Telemetria
   - Propriedades
   - Comandos

![Modelo de dispositivo-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgemodelling.png)

**Adicionar recursos ao modelo de funcionalidade do módulo**

Veja um exemplo de saída do módulo SimulatedTemperatureSensor
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

Adicione recursos ao módulo SimulatedTemperatureSensor, que refletirá o JSON acima. 

* Clique em **gerenciar** uma interface do modelo de funcionalidade do módulo SimulatedTemperatureSensor. Clique em **adicionar funcionalidade**. 

    ![Modelo de dispositivo-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateaddcapability.png)
  
* Adicionar computador como um tipo de objeto, pois ele é um tipo complexo
  
    ![Modelo de dispositivo-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatemachineobject.png)

    Clique em **definir**. Em pop-up, altere o nome do objeto para a máquina e crie propriedades temperatura, pressão e clique em **aplicar**
  
    ![Modelo de dispositivo-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatemachineattributes.png)
  
* Adicionar ambiente como um tipo de objeto, pois ele é um tipo complexo

    Clique em **definir**. No menu popup, altere o nome do objeto para ambiente e crie propriedades temperatura, umidade e clique em **aplicar**
  
    ![Modelo de dispositivo-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateambientattributes.png)

  
* Adicione timecriado como um tipo DateTime e clique em **salvar**
  
    ![Modelo de dispositivo-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateallattributes.png)


### <a name="add-relationships"></a>Adicionar relações

Se você selecionou Azure IoT Edge dispositivo para ser um dispositivo de gateway, poderá adicionar relações de downstream a modelos de capacidade de dispositivo para dispositivos que serão conectados ao dispositivo de gateway.
  
  ![Modelo de dispositivo-Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-add-relationship.png)

A relação pode ser adicionada em um dispositivo ou em um módulo.
  
  ![Modelo de dispositivo-Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-relationship-types.png)


Você pode selecionar um modelo de funcionalidade de dispositivo downstream ou selecionar asterisco. 
  
  ![Modelo de dispositivo-Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-downstream-rel.png)

  Para este tutorial, selecionaremos asterisco, o que significa que qualquer relacionamento downstream será permitido. Clicar em **Guardar**

  ![Modelo de dispositivo-Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-add-relationship-asterix.png)


### <a name="add-cloud-properties"></a>Adicionar propriedades de nuvem

Um modelo de dispositivo pode incluir propriedades de nuvem. As propriedades de nuvem existem somente no aplicativo IoT Central e nunca são enviadas ou recebidas de um dispositivo.

1. Selecione **Propriedades de nuvem** e **+ Adicionar Propriedade de nuvem**. Use as informações na tabela a seguir para adicionar uma propriedade de nuvem ao modelo de dispositivo.

    | Nome a Apresentar      | Tipo semântico | Esquema |
    | ----------------- | ------------- | ------ |
    | Data da Última Assistência | Nenhum          | Date   |
    | Nome do cliente     | Nenhum          | String |

2. Selecione **salvar** para salvar as alterações:

  
    ![Propriedades de nuvem-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatecloudproperties.png)

### <a name="add-customizations"></a>Adicionar personalizações

Use personalizações quando precisar modificar uma interface ou adicionar recursos específicos de IoT Central a uma funcionalidade que não exige a versão do modelo de capacidade do dispositivo. Você pode personalizar os campos quando o modelo de funcionalidade estiver em um rascunho ou em um estado publicado. Você só pode personalizar campos que não interrompem a compatibilidade de interface. Por exemplo, pode:

- Personalize o nome de exibição e as unidades de um recurso.
- Adicione uma cor padrão a ser usada quando o valor for exibido em um gráfico.
- Especifique os valores iniciais, mínimos e máximos para uma propriedade.

Você não pode personalizar o nome da funcionalidade ou o tipo de funcionalidade. Clicar em **Guardar**
  
![Personalizações-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatecustomize.png)


### <a name="create-views"></a>Criar exibições

Como um construtor, você pode personalizar o aplicativo para exibir informações relevantes sobre o dispositivo do sensor ambiental para um operador. Suas personalizações permitem que o operador gerencie os dispositivos do sensor ambiental conectados ao aplicativo. Você pode criar dois tipos de modos de exibição para que um operador Use para interagir com dispositivos:

* Formulários para exibir e editar propriedades de dispositivo e de nuvem.
* Painéis para visualizar dispositivos.

### <a name="configure-a-view-to-visualize-devices"></a>Configurar uma exibição para visualizar dispositivos

Um painel de dispositivos permite que um operador visualize um dispositivo usando gráficos e métricas. Pode definir as informações que são apresentadas num dashboard do dispositivo como construtor. Você pode definir vários painéis para dispositivos. Para criar um painel para visualizar a telemetria do sensor ambiental, selecione **exibições** e, em seguida, **visualizando o dispositivo**:

  
![Exibições-Azure IoT Edge](./media/tutorial-define-edge-device-type/visualizingthedevice.png)


Telemetria ambiente e telemetria de máquina são objetos complexos, para criar gráficos, faça o seguinte

Arraste telemetria de ambiente e selecione gráfico de linhas. 
  
![Exibições-Azure IoT Edge](./media/tutorial-define-edge-device-type/sensorambientchart.png)

Clique no ícone configurar e selecione temperatura e umidade para visualizar os dados e clique no botão **Atualizar configuração** . 
  
![Exibições-Azure IoT Edge](./media/tutorial-define-edge-device-type/sensorambienttelemetrychart.png)

Selecione **salvar** para salvar sua exibição:

Você pode adicionar mais blocos que mostram outras propriedades ou valores de telemetria. Você também pode adicionar texto estático, links e imagens. Para mover ou redimensionar um bloco no painel, mova o ponteiro do mouse sobre o bloco e arraste o bloco para um novo local ou redimensione-o.
  
![Exibições-Azure IoT Edge](./media/tutorial-define-edge-device-type/viewsdashboard.png)

### <a name="add-a-device-form"></a>Adicionar um formulário de dispositivo

Um formulário de dispositivo permite que um operador Edite propriedades de dispositivo gravável e propriedades de nuvem. Como um construtor, você pode definir vários formulários e escolher quais propriedades de dispositivo e de nuvem mostrar em cada formulário. Você também pode exibir propriedades de dispositivo somente leitura em um formulário.

Para criar um formulário para exibir e editar as propriedades do sensor ambiental:

Navegue até **exibições** no modelo de **sensor ambiental** . Selecione o bloco **dispositivo de edição e dados de nuvem** para adicionar uma nova exibição.
  
![Exibições-Azure IoT Edge](./media/tutorial-define-edge-device-type/editingdeviceandclouddata.png)

Insira o nome do formulário **Propriedades do sensor ambiental**.

Arraste as propriedades de nuvem **nome do cliente** e **última data de serviço** até a seção existente no formulário.
  
![Exibições-Azure IoT Edge](./media/tutorial-define-edge-device-type/views-properties.png)

Selecione **salvar** para salvar sua exibição.

### <a name="generate-default-views"></a>Gerar exibições padrão

Não há suporte para a funcionalidade gerar exibições padrão para modelos de Azure IoT Edge 

## <a name="publish-device-template"></a>Publicar modelo de dispositivo

Para poder criar um sensor ambiental simulado ou conectar um sensor ambiental real, você precisa publicar o modelo do dispositivo.

Para publicar um modelo de dispositivo:

1. Vá para o modelo de dispositivo na página **modelos de dispositivo** .

2. Selecione **Publicar**.
  
    ![Exibições-publicar](./media/tutorial-define-edge-device-type/edgetemplatepublish.png)

1. Na caixa de diálogo **publicar um modelo de dispositivo** , escolha **publicar**:
  
    ![Exibições-publicar](./media/tutorial-define-edge-device-type/edgepublishtemplate.png)

Depois que um modelo de dispositivo é publicado, ele fica visível na página **dispositivos** e no operador. Em um modelo de dispositivo publicado, você não pode editar um modelo de capacidade de dispositivo sem criar uma nova versão. No entanto, você pode fazer atualizações nas propriedades de nuvem, personalizações e exibições, em um modelo de dispositivo publicado sem controle de versão. Depois de fazer alterações, selecione **publicar** para enviar por push essas alterações para o operador.
  
![Exibições-publicar](./media/tutorial-define-edge-device-type/publishedtemplate.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

* Criar uma nova borda como um modelo de dispositivo de folha
* Gerar módulos de um manifesto de implantação carregado
* Adicionar propriedades e telemetria de tipo complexo
* Criar propriedades de nuvem.
* Criar personalizações.
* Defina uma visualização para a telemetria do dispositivo.
* Publique o modelo de dispositivo do Edge.

Agora que você criou um modelo de dispositivo em seu aplicativo IoT Central do Azure, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Conectar dispositivo](./tutorial-connect-pnp-device.md)
