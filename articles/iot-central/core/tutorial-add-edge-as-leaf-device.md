---
title: Tutorial - Adicione um dispositivo Azure IoT Edge ao Azure IoT Central | Microsoft Docs
description: Tutorial - Como operador, adicione um dispositivo Azure IoT Edge à sua aplicação Azure IoT Central
author: rangv
ms.author: rangv
ms.date: 05/29/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
- iot-edge
ms.openlocfilehash: 373d144b4df818a075f0088e9cbf31cb5027e747
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101724885"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Tutorial: Adicione um dispositivo Azure IoT Edge à sua aplicação Azure IoT Central

*Este artigo aplica-se a operadores, construtores de soluções e desenvolvedores de dispositivos.*

Este tutorial mostra-lhe como configurar e adicionar um dispositivo Azure IoT Edge à sua aplicação Azure IoT Central. O tutorial utiliza uma máquina virtual Linux (VM) ativada pelo IoT Edge para simular um dispositivo IoT Edge. O dispositivo IoT Edge utiliza um módulo que gera telemetria ambiental simulada. Você vê a telemetria num dashboard na sua aplicação IoT Central.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie um modelo de dispositivo para um dispositivo IoT Edge
> * Criar um dispositivo IoT Edge em IoT Central
> * Implementar um dispositivo IoT Edge simulado para um Linux VM

## <a name="prerequisites"></a>Pré-requisitos

Complete o quickstart [da aplicação Create azure IoT Central](./quick-deploy-iot-central.md) para criar uma aplicação IoT Central utilizando o modelo **de aplicação Custom > Custom.**

Para completar os passos neste tutorial, precisa de uma subscrição ativa do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Descarregue o ficheiro manifesto IoT Edge do GitHub. Clique com o botão direito no link seguinte e, em seguida, **selecione Guardar link como**:EnvironmentalSensorManifest.js [ em](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>Criar modelo de dispositivo

Nesta secção, você cria um modelo de dispositivo IoT Central para um dispositivo IoT Edge. Importa um manifesto IoT Edge para começar e, em seguida, modifica o modelo para adicionar definições e vistas de telemetria:

### <a name="import-manifest-to-create-template"></a>Manifesto de importação para criar modelo

Para criar um modelo de dispositivo a partir de um manifesto IoT Edge:

1. Na sua aplicação IoT Central, navegue para **modelos de dispositivo e** selecione **+ Novo**.

1. Na página **de tipo de modelo Select,** selecione o azulejo **Azure IoT Edge.** Em seguida, **selecione Seguinte: Personalize**.

1. Na página manifesto de **implementação do Azure IoT Edge,** insira o *Dispositivo de Borda do Sensor Ambiental* como o nome do modelo do dispositivo. Em seguida, **selecione Browse** para carregar o **EnvironmentalSensorManifest.jsem** que descarregou anteriormente. Em seguida, selecione **Seguinte: Revisão**.

1. Na página **'Revisão',** **selecione Criar**.

1. Selecione a interface **Manage** no módulo **SimulaçãoSteperatureSensor** para ver as duas propriedades definidas no manifesto:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/imported-manifest.png" alt-text="Modelo de dispositivo criado a partir do manifesto IoT Edge":::

> [!TIP]
> Este manifesto de implantação retira imagens do módulo de um repositório de registo de contentores Azure que não requer credenciais para se ligar. Se pretender utilizar imagens de módulos a partir de um repositório privado, deite as credenciais de registo do contentor no manifesto.

### <a name="add-telemetry-to-manifest"></a>Adicione telemetria para manifestar

Um manifesto IoT Edge não define a telemetria que um módulo envia. Adicione as definições de telemetria ao modelo do dispositivo na IoT Central. O módulo **SimulaçãoSteperatureSensor** envia mensagens de telemetria que se parecem com as seguintes JSON:

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

1. Selecione a interface **Manage** no modelo **do dispositivo de borda do sensor ambiental.**

1. Selecione **+ Adicionar capacidade**. Introduza a *máquina* como **o nome do Visor** e certifique-se de que o **tipo de Capacidade** é **Telemetria**.

1. Selecione **o Objeto** como o tipo de esquema e, em seguida, selecione **Definir**. Na página de definição do objeto, adicione *a temperatura* e *a pressão* como atributos do tipo **Duplo** e, em seguida, selecione **Aplicar**.

1. Selecione **+ Adicionar capacidade**. Introduza *o ambiente* como o **nome do Visor** e certifique-se de que o **tipo de Capacidade** é **Telemetria**.

1. Selecione **o Objeto** como o tipo de esquema e, em seguida, selecione **Definir**. Na página de definição do objeto, adicione *temperatura* e *humidade* como atributos do tipo **Duplo** e, em seguida, selecione **Aplicar**.

1. Selecione **+ Adicionar capacidade**. Introduza o *tempoCretado* como o **nome do Visor** e certifique-se de que o **tipo de Capacidade** é **Telemetria**.

1. Selecione **DateTime** como o tipo de esquema.

1. **Selecione Guardar** para atualizar o modelo.

A interface **Manage** inclui agora a **máquina,** **o ambiente** e os tipos de telemetria **cronometrado:**

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/manage-interface.png" alt-text="Interface com máquina e tipos de telemetria ambiente":::

### <a name="add-views-to-template"></a>Adicionar vistas ao modelo

O modelo do dispositivo ainda não tem uma visão que permita a um operador ver a telemetria a partir do dispositivo IoT Edge. Para adicionar uma vista ao modelo do dispositivo:

1. Selecione **vistas** no modelo do **dispositivo de borda do sensor ambiental.**

1. No **Select para adicionar uma nova** página de visualização, selecione **o azulejo do dispositivo visualizando.**

1. Altere o nome de visualização para *ver telemetria do dispositivo IoT Edge*.

1. Selecione os tipos de telemetria **ambiente** e **máquina.** Em seguida, **selecione Adicionar azulejo**.

1. **Selecione Guardar** para guardar a vista **de telemetria do dispositivo View IoT Edge.**

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png" alt-text="Modelo de dispositivo com vista de telemetria":::

### <a name="publish-the-template"></a>Publicar o modelo

Antes de poder adicionar um dispositivo que utilize o modelo do **Dispositivo de Borda do Sensor Ambiental,** tem de publicar o modelo.

Navegue para o modelo do **dispositivo de borda do sensor ambiental** e selecione **Publicar**. No modelo de publicação deste dispositivo para o painel **de aplicações,** selecione **Publicar** para publicar o modelo:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/publish-template.png" alt-text="Publicar o modelo de dispositivo":::

## <a name="add-iot-edge-device"></a>Adicionar dispositivo IoT Edge

Agora que publicou o modelo do **Dispositivo de Borda de Sensor Ambiental,** pode adicionar um dispositivo à sua aplicação IoT Central:

1. Na sua aplicação IoT Central, navegue na página **dispositivos** e selecione o **Dispositivo de Borda do Sensor Ambiental** na lista de modelos disponíveis.

1. Selecione **+ Novo** para adicionar um novo dispositivo do modelo. Na página **criar novo dispositivo,** selecione **Criar**.

Tem agora um novo dispositivo com o estado **registado:**

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/new-device.png" alt-text="Dispositivo novo e registado":::

### <a name="get-the-device-credentials"></a>Obtenha as credenciais do dispositivo

Quando implementar o dispositivo IoT Edge mais tarde neste tutorial, precisa das credenciais que permitem que o dispositivo se conecte à sua aplicação IoT Central. Obter as credenciais do dispositivo:

1. Na página **Dispositivo,** selecione o dispositivo que criou.

1. Selecione **Ligar**.

1. Na página de ligação do **dispositivo,** tome nota do **ID Scope,** do **ID do dispositivo** e da chave **primária**. Usa estes valores mais tarde.

1. Selecione **Fechar**.

Já terminou de configurar a sua aplicação IoT Central para permitir a ligação de um dispositivo IoT Edge.

## <a name="deploy-an-iot-edge-device"></a>Implementar um dispositivo IoT Edge

Neste tutorial, você usa um Linux VM ativado por Azure IoT Edge, criado no Azure para simular um dispositivo IoT Edge. Para criar o VM ativado pelo IoT Edge na sua subscrição Azure, clique em:

[![Botão Implementar no Azure iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

Na página **de implementação personalizada:**

1. Selecione a sua subscrição do Azure.

1. **Selecione Criar novo** para criar um novo grupo de recursos chamado *central-edge-rg*.

1. Escolha uma região próxima de si.

1. Adicione um **prefixo exclusivo da etiqueta DNS,** como *contoso-central-edge*.

1. Escolha um nome de utilizador administrativo para a máquina virtual.

1. Introduza *a temperatura* como a cadeia de ligação. Mais tarde, configurar o dispositivo para ligar utilizando DPS.

1. Aceite os valores predefinidos para o tamanho VM, versão Ubuntu e localização.

1. Selecione **a palavra-passe** como o tipo de autenticação.

1. Introduza uma senha para o VM.

1. Em seguida, selecione **Review + Create**.

1. Reveja as suas escolhas e, em seguida, **selecione Criar**:

    :::image type="content" source="media/tutorial-add-edge-as-leaf-device/vm-deployment.png" alt-text="Criar um IoT Edge VM":::

A implantação leva alguns minutos para ser concluída. Quando a implantação estiver concluída, navegue para o grupo de recursos **de ponta central** no portal Azure.

### <a name="configure-the-iot-edge-vm"></a>Configure o IoT Edge VM

Para configurar ioT Edge no VM para utilizar DPS para registar e ligar à sua aplicação IoT Central:

1. No grupo de recursos **contoso-edge-rg,** selecione a instância da máquina virtual.

1. Na secção **Suporte + resolução de problemas,** selecione **consola série**. Se for solicitado para configurar diagnósticos de arranque, siga as instruções no portal.

1. Pressione **Entrar** para ver o `login:` pedido. Introduza o seu nome de utilizador e senha para iniciar sôm.

1. Executar o seguinte comando para verificar a versão de tempo de execução IoT Edge. No momento da escrita, a versão é 1.0.9.1:

    ```bash
    sudo iotedge --version
    ```

1. Utilize o `nano` editor para abrir o ficheiro IoT Edge config.yaml:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Desça até `# Manual provisioning configuration` ver. Comente as próximas três linhas, como mostrado no seguinte corte:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "temp"
    ```

1. Desça até `# DPS symmetric key provisioning configuration` ver. Descomprometar as próximas oito linhas, como mostrado no seguinte corte:

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

    > [!TIP]
    > Certifique-se de que não há mais espaço na frente de `provisioning:`

1. `{scope_id}`Substitua-o pelo **ID Scope** que fez uma nota anterior.

1. `{registration_id}`Substitua-o **pelo ID** do dispositivo que fez anteriormente.

1. `{symmetric_key}`Substitua-a **pela chave primária** que fez anteriormente.

1. Guarde as alterações **(Ctrl-O)** e saia **(Ctrl-X)** o `nano` editor.

1. Executar o seguinte comando para reiniciar o daemon IoT Edge:

    ```bash
    sudo systemctl restart iotedge
    ```

1. Para verificar o estado dos módulos IoT Edge, execute o seguinte comando:

    ```bash
    iotedge list
    ```

    A seguinte saída da amostra mostra os módulos de funcionamento:

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

    > [!TIP]
    > Pode ter de esperar que todos os módulos comecem a funcionar.

## <a name="view-the-telemetry"></a>Ver a telemetria

O dispositivo IoT Edge simulado está agora em funcionamento no VM. Na sua aplicação IoT Central, o estado do dispositivo está agora **previsto** na página **dispositivos:**

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/provisioned-device.png" alt-text="Dispositivo IoT Edge provisionado":::

Pode ver a telemetria do dispositivo na página de **telemetria do dispositivo View IoT Edge:**

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png" alt-text="Telemetria do dispositivo":::

A página **módulos** mostra o estado dos módulos IoT Edge no dispositivo:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/edge-module-status.png" alt-text="Estado do módulo do dispositivo":::

## <a name="clean-up-resources"></a>Limpar os recursos

Se planeia continuar a trabalhar com o IoT Edge VM, pode conservar e reutilizar os recursos utilizados neste tutorial. Caso contrário, pode eliminar os recursos que criou neste tutorial para evitar custos adicionais:

* Para eliminar o IoT Edge VM e os seus recursos associados, elimine o grupo de recursos **contoso-edge-rg** no portal Azure.
* Para eliminar a aplicação IoT Central, navegue na página **de aplicação** da sua aplicação na secção **Administração** da aplicação e selecione **Delete**.

Como desenvolvedor de soluções ou operador, agora que aprendeu a trabalhar e gerir dispositivos IoT Edge na IoT Central, um próximo passo sugerido é:

> [!div class="nextstepaction"]
> [Utilize grupos de dispositivos para analisar a telemetria do dispositivo](./tutorial-use-device-groups.md)

## <a name="next-steps"></a>Passos seguintes

Como desenvolvedor de dispositivos, agora que aprendeu a trabalhar e gerir dispositivos IoT Edge na IoT Central, um passo sugerido é ler:

> [!div class="nextstepaction"]
> [Desenvolver módulos IoT Edge](../../iot-edge/tutorial-develop-for-linux.md)