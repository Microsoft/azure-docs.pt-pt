---
title: Adicione um dispositivo Azure IoT Edge à Central Azure IoT [ Central De IoT ] Microsoft Docs
description: Como operador, adicione um dispositivo Azure IoT Edge à sua aplicação Azure IoT Central
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: c60cf4b90b089d271c0ccd91031420efe9017b1e
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758172"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Tutorial: Adicione um dispositivo Azure IoT Edge à sua aplicação Central Azure IoT

*Este artigo aplica-se a construtores de soluções e desenvolvedores de dispositivos.*

Este tutorial mostra-lhe como configurar e adicionar um dispositivo Azure IoT Edge à sua aplicação Azure IoT Central. O tutorial utiliza uma máquina virtual Linux (VM) ativada por IoT Edge do Azure Marketplace para simular um dispositivo IoT Edge. O dispositivo IoT Edge utiliza um módulo que gera telemetria ambiental simulada. Você vê a telemetria num painel de instrumentos na sua aplicação IoT Central.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie um modelo de dispositivo para um dispositivo IoT Edge
> * Crie um dispositivo IoT Edge em IoT Central
> * Implemente um dispositivo IoT Edge simulado para um VM Linux

## <a name="prerequisites"></a>Pré-requisitos

Complete a [aplicação Create a Azure IoT Central](./quick-deploy-iot-central.md) rapidamente para criar uma aplicação IoT Central utilizando a aplicação Custom > modelo de **aplicação Personalizada.**

Para completar os passos neste tutorial, precisa de uma subscrição azure ativa.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Descarregue o ficheiro manifesto IoT Edge do GitHub. Clique à direita no link seguinte e, em seguida, selecione **'Salvar link as:** [EnvironmentalSensorManifest.json'](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>Criar modelo de dispositivo

Nesta secção, cria-se um modelo de dispositivo para um dispositivo IoT Edge que se liga à sua aplicação IoT Central. Importa-se um manifesto IoT Edge para começar e, em seguida, modificar o modelo para adicionar definições e vistas de telemetria:

### <a name="import-manifest-to-create-template"></a>Manifesto de importação para criar modelo

Para criar um modelo de dispositivo a partir de um manifesto IoT Edge:

1. Na sua aplicação IoT Central, navegue para **os modelos do Dispositivo** e selecione + **Novo**.

1. Na página do **modelo Select,** selecione o azulejo **Azure IoT Edge.** Em seguida, selecione **Seguinte: Personalizar**.

1. No upload de uma página de manifesto de **implementação Azure IoT Edge,** selecione **Browse** para carregar o **EnvironmentalSensorManifest.json** que descarregou anteriormente. Em seguida, selecione **Seguinte: Rever**.

1. Na página **'Rever',** selecione **Criar**.

1. Quando o modelo tiver sido criado, mude o seu nome para *Dispositivo de Borda*do Sensor Ambiental .

1. Selecione a interface **Gerir** no módulo **SimuladoTemperatureSensor** para ver as duas propriedades definidas no manifesto:

![Modelo de dispositivo criado a partir do manifesto IoT Edge](./media/tutorial-add-edge-as-leaf-device/imported-manifest.png)

### <a name="add-telemetry-to-manifest"></a>Adicione telemetria para manifestar

Um manifesto IoT Edge não define a telemetria que um módulo envia. Deve adicionar as definições de telemetria ao modelo do dispositivo. O módulo **SimuladoTemperatureSensor** envia mensagens de telemetria que se parecem com o seguinte JSON:

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

Para adicionar as definições de telemetria ao modelo do dispositivo:

1. Selecione a interface **Gerir** no modelo de dispositivo de borda do **sensor ambiental.**

1. Selecione **+ Adicionar capacidade**. Introduza a *máquina* como o **nome display** e certifique-se de que o **tipo Capability** é **Telemetria**.

1. Selecione **O Objeto** como o tipo de esquema e, em seguida, selecione **Definir**. Na página de definição de objeto, adicione *temperatura* e *pressão* como atributos do tipo **Duplo** e, em seguida, selecione **Aplicar**.

1. Selecione **+ Adicionar capacidade**. Introduza ambiente como o **nome display** e certifique-se de que o **tipo Capability** é **Telemetria**. *ambient*

1. Selecione **O Objeto** como o tipo de esquema e, em seguida, selecione **Definir**. Na página de definição de objeto, adicione *temperatura* e *humidade* como atributos do tipo **Duplo** e, em seguida, selecione **Aplicar**.

1. Selecione **+ Adicionar capacidade**. Introduza o *tempoCriado* como nome **display** e certifique-se de que o **tipo Capability** é **Telemetria**.

1. Selecione **DateTime** como o tipo de esquema.

1. Selecione **Guardar** para atualizar o modelo.

A interface **Manage** agora inclui a **máquina,** **ambiente,** e **tempoCriadotipos** de telemetria:

![Interface com máquina e tipos de telemetria ambiente](./media/tutorial-add-edge-as-leaf-device/manage-interface.png)

### <a name="add-views-to-template"></a>Adicione vistas ao modelo

O modelo do dispositivo ainda não tem uma vista que permita a um operador ver a telemetria do dispositivo IoT Edge. Para adicionar uma vista ao modelo do dispositivo:

1. Selecione **vistas** no modelo do dispositivo de borda do **sensor ambiental.**

1. No Select para adicionar uma nova página de **visualização,** selecione o azulejo **visualizado do dispositivo.**

1. Mude o nome da vista para *ver telemetria do dispositivo IoT Edge*.

1. **Selecione** os tipos de telemetria ambiente e **máquina.** Em seguida, selecione **Adicionar azulejo**.

1. Selecione **Guardar** para guardar a vista de **telemetria do dispositivo View IoT Edge.**

![Modelo de dispositivo com vista para a telemetria](./media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png)

### <a name="publish-the-template"></a>Publicar o modelo

Antes de poder adicionar um dispositivo que utilize o modelo de dispositivo de borda de **sensor ambiental,** tem de publicar o modelo.

Navegue para o modelo de dispositivo de borda do **sensor ambiental** e selecione **Publicar**. Em seguida, **selecione Publicar** para publicar o modelo:

![Publique o modelo do dispositivo](./media/tutorial-add-edge-as-leaf-device/publish-template.png)

## <a name="add-iot-edge-device"></a>Adicione o dispositivo IoT Edge

Agora que publicou o modelo de Dispositivo de Borda de **Sensor Ambiental,** pode adicionar um dispositivo à sua aplicação IoT Central:

1. Na sua aplicação IoT Central, navegue para a página **dispositivos** e selecione **O Dispositivo de Borda** do Sensor Ambiental na lista de modelos disponíveis.

1. Selecione **+** adicionar um novo dispositivo a partir do modelo. Na página **Criar novo dispositivo,** selecione **Criar**.

Tem agora um novo dispositivo com o estado **Registado:**

![Publique o modelo do dispositivo](./media/tutorial-add-edge-as-leaf-device/new-device.png)

### <a name="get-the-device-credentials"></a>Obtenha as credenciais do dispositivo

Quando implementar o dispositivo IoT Edge mais tarde neste tutorial, precisa das credenciais que permitem que o dispositivo se conectem à sua aplicação IoT Central. O obter as credenciais do dispositivo:

1. Na página **do Dispositivo,** selecione o dispositivo que criou.

1. Selecione **Ligar**.

1. Na página de ligação do **Dispositivo,** tome nota do âmbito de **identificação,** do ID do **dispositivo**e da **chave primária**. Usa estes valores mais tarde.

1. Selecione **Fechar**.

Terminou agora de configurar a sua aplicação IoT Central para permitir a ligação de um dispositivo IoT Edge.

## <a name="deploy-an-iot-edge-device"></a>Implementar um dispositivo IoT Edge

Neste tutorial, você usa um Linux VM com borda seletiva Azure IoT, criado no Azure para simular um dispositivo IoT Edge. Para criar o VM iot edge-enabled:

1. Navegue até [Azure IoT Edge em Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) no Azure Marketplace. Em seguida, selecione **-lo agora**.

1. Na **página Create this app in Azure,** selecione **Continue**. Este link leva-o ao portal Azure, onde poderá ter de iniciar sessão na sua subscrição Azure.

1. Na **borda Azure IoT na página Ubuntu** no portal Azure, selecione **Create**.

1. Na página **Criar uma máquina virtual > Basics:**

    - Selecione a sua subscrição do Azure.
    - Crie um novo grupo de recursos chamado **iot-edge-devices**.
    - Utilize o nome da máquina virtual: **iotedgevm**.
    - Escolha a região mais próxima de si.
    - Desloque o tipo de autenticação para **Password**.
    - Escolha um nome de utilizador e uma senha.
    - Pode deixar as outras opções nos seus valores padrão.
    - Selecione **Rever + criar**.

1. Quando a validação estiver concluída, selecione **Criar**.

Após alguns minutos, quando a implementação estiver concluída, selecione **Ir para o recurso**.

### <a name="provision-vm-as-an-iot-edge-device"></a>Provision VM como um dispositivo IoT Edge 

Para fornecer VM como um dispositivo IoT Edge:

1. Na secção **Suporte + resolução de problemas,** selecione **consola série**.

1. Pressione **Entrar** `login:` para ver o pedido. Insira o seu nome de utilizador e senha para iniciar sessão.

1. Execute o seguinte comando para verificar a versão de tempo de execução IoT Edge. No momento da escrita, a versão é 1.0.8:

    ```bash
    sudo iotedge --version
    ```

1. Utilize `nano` o editor para abrir o ficheiro IoT Edge config.yaml:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Desça até ver. `# Manual provisioning configuration` Comente as próximas três linhas, como mostra o seguinte corte:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
    ```

1. Desça até ver. `# DPS symmetric key provisioning configuration` Descodere as próximas oito linhas, como mostra o seguinte corte:

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

1. Substitua-a `{scope_id}` pelo Âmbito de **Identificação** por si que tomou nota anteriormente.

1. Substitua-a `{registration_id}` pelo ID do **dispositivo** que fez uma nota anteriormente.

1. Substitua-a `{symmetric_key}` pela **chave primária** de que tomou nota anteriormente.

1. Guarde as alterações **(CTRL-O)** e saia `nano` **(Ctrl-X**) o editor.

1. Executar o seguinte comando para reiniciar o daemon IoT Edge:

    ```bash
    sudo systemctl restart iotedge
    ```

1. Para verificar o estado dos módulos IoT Edge, execute o seguinte comando:

    ```bash
    iotedge list
    ```

    A saída parece ser a seguinte:

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

## <a name="view-the-telemetry"></a>Ver a telemetria

O dispositivo IoT Edge simulado está agora a funcionar no VM. Na sua aplicação IoT Central, o estado do dispositivo está agora **provisionado** na página **dispositivos:**

![Dispositivo provisionado](./media/tutorial-add-edge-as-leaf-device/provisioned-device.png)

Pode ver a telemetria na página de **telemetria do dispositivo View IoT Edge:**

![Telemetria do dispositivo](./media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png)

A página **Módulos** mostra o estado dos módulos IoT Edge:

![Telemetria do dispositivo](./media/tutorial-add-edge-as-leaf-device/edge-module-status.png)

## <a name="next-steps"></a>Passos seguintes

Como desenvolvedor de dispositivos, agora que aprendeu a trabalhar e gerir dispositivos IoT Edge na IoT Central, um próximo passo sugerido é ler:

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [Fique ligado à Central Azure IoT](./concepts-get-connected.md)
