---
title: Ligue uma porta transparente IoT Edge a uma aplicação Azure IoT Central
description: Como ligar dispositivos através de uma porta de entrada transparente IoT Edge para uma aplicação IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 03/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 4e88ad58c7baba1c66c30df3f4effdbf11371c18
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045336"
---
# <a name="how-to-connect-devices-through-an-iot-edge-transparent-gateway"></a>Como conectar dispositivos através de um gateway transparente IoT Edge

Um dispositivo IoT Edge pode funcionar como um gateway que fornece uma ligação entre outros dispositivos numa rede local e a sua aplicação IoT Central. Usa um gateway quando o dispositivo não consegue aceder diretamente à sua aplicação IoT Central.

O IoT Edge suporta os [padrões *transparentes* e de gateway de *tradução.*](../../iot-edge/iot-edge-as-gateway.md) Este artigo resume como implementar o padrão transparente de gateway. Neste padrão, o gateway transmite mensagens do dispositivo a jusante até ao ponto final do IoT Hub na sua aplicação IoT Central.

Este artigo utiliza máquinas virtuais para hospedar o dispositivo a jusante e o gateway. Num cenário real, o dispositivo a jusante e o gateway funcionariam em dispositivos físicos na sua rede local.

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos neste tutorial, precisa de uma subscrição ativa do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Complete o quickstart [da aplicação Create azure IoT Central](./quick-deploy-iot-central.md) para criar uma aplicação IoT Central utilizando o modelo **de aplicação Custom > Custom.**

Para seguir os passos deste artigo, faça o download dos seguintes ficheiros para o seu computador:

- [Modelo do dispositivo do termóstato](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-1.json)
- [Manifesto transparente de gateway](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/EdgeTransparentGatewayManifest.json)

## <a name="add-device-templates"></a>Adicionar modelos de dispositivo

Tanto os dispositivos a jusante como o dispositivo gateway requerem modelos de dispositivo na IoT Central. A IoT Central permite-lhe modelar a relação entre os seus dispositivos a jusante e o seu gateway para que possa vê-los e geri-los depois de conectados.

Para criar um modelo de dispositivo para um dispositivo a jusante, crie um modelo de dispositivo padrão que modele as capacidades do seu dispositivo. O exemplo mostrado neste artigo utiliza o modelo do dispositivo termóstato.

Para criar um modelo de dispositivo para um dispositivo a jusante:

1. Crie um modelo de dispositivo e escolha **o dispositivo IoT** como o tipo de modelo.

1. Na página **De Personalização** do assistente, introduza um nome como *Termóstato* para o modelo do dispositivo.

1. Depois de criar o modelo do dispositivo, **selecione Import a model**. Selecione um modelo como o *thermostat-1.jsno* ficheiro que descarregou anteriormente.

1. Para gerar algumas vistas predefinições para o termóstato, selecione vistas e, em seguida, escolha **Gerar vistas padrão**.

1. Publique o modelo do dispositivo.

Para criar um modelo de dispositivo para um gateway IoT Edge transparente:

1. Crie um modelo de dispositivo e escolha **Azure IoT Edge** como o tipo de modelo.

1. Na página **De Personalização** do assistente, insira um nome como *Edge Gateway* para o modelo do dispositivo.

1. Na página **De Personalização** do assistente, verifique o **dispositivo Gateway com dispositivos a jusante**.

1. Na página **Personalizar** o assistente, **selecione Procurar**. Faça o upload do *EdgeTransparentGatewayManifest.jsno* ficheiro que descarregou anteriormente.

1. Adicione uma entrada em **Relacionamentos** ao modelo do dispositivo a jusante.

A imagem a seguir mostra a página **Relationships** para um dispositivo de gateway IoT Edge que tem dispositivos a jusante que usam o modelo do dispositivo **termóstato:**

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/device-template-relationship.png" alt-text="Screenshot mostrando a relação do modelo do dispositivo de gateway IoT Edge com um modelo de dispositivo a jusante do termóstato.":::

A imagem anterior mostra um modelo de dispositivo de gateway IoT Edge sem módulos definidos. Um gateway transparente não requer nenhum módulo porque o tempo de execução IoT Edge encaminha mensagens dos dispositivos a jusante para a IoT Central. Se o gateway em si precisar de enviar telemetria, sincronizar propriedades ou manusear comandos, pode definir essas capacidades no componente predefinido ou num módulo.

Adicione todas as propriedades e vistas necessárias para a nuvem antes de publicar os modelos de gateway e dispositivo a jusante.

## <a name="add-the-devices"></a>Adicione os dispositivos

Quando adicionar os dispositivos à sua aplicação IoT Central, pode definir a relação entre os dispositivos a jusante e o gateway transparente.

Para adicionar os dispositivos:

1. Navegue para a página de dispositivos na sua aplicação IoT Central.

1. Adicione uma instância do dispositivo transparente gateway IoT Edge. Neste artigo, o ID do dispositivo gateway é `edgegateway` .

1. Adicione uma ou mais instâncias do dispositivo a jusante. Neste artigo, os dispositivos a jusante são termostatos com IDs `thermostat1` e `thermostat2` .

1. Na lista de dispositivos, selecione cada dispositivo a jusante e **selecione Ligue ao gateway**.

A imagem que se segue mostra que pode ver a lista de dispositivos anexados a um portal na página dispositivos a **jusante:**

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-devices.png" alt-text="Screenshot que mostra a lista de dispositivos a jusante ligados a um gateway transparente.":::

Numa porta de entrada transparente, os dispositivos a jusante ligam-se ao próprio portal, e não a um módulo personalizado alojado pelo gateway.

Antes de implantar os dispositivos, precisa de:

- **ID Âmbito** da sua aplicação IoT Central.
- **Valores de ID** do dispositivo para os dispositivos gateway e a jusante.
- **Valores-chave primários** para os dispositivos gateway e a jusante.

Para encontrar estes valores, navegue em cada dispositivo na lista de dispositivos e selecione **Connect**. Tome nota destes valores antes de continuar.

## <a name="deploy-the-gateway-and-devices"></a>Implementar o gateway e os dispositivos

Para que possa experimentar este cenário, os seguintes passos mostram-lhe como implantar os dispositivos gateway e a jusante para máquinas virtuais Azure. Num cenário real, o dispositivo a jusante e o gateway funcionam em dispositivos físicos na sua rede local.

Para experimentar o cenário de gateway transparente, selecione o seguinte botão para implantar duas máquinas virtuais Linux. Uma máquina virtual é um gateway IoT Edge transparente, o outro é um dispositivo a jusante que simula um termostato:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

Quando as duas máquinas virtuais estiverem implantadas e em funcionamento, verifique se o dispositivo de gateway IoT Edge está a funcionar na `edgegateway` máquina virtual:

1. Aceda à página **Dispositivos** na sua aplicação IoT Central. Se o dispositivo de gateway IoT Edge estiver ligado à IoT Central, o seu estado é **Provisionado**.

1. Abra o dispositivo de gateway IoT Edge e verifique o estado dos módulos na página **Módulos.** Se o tempo de funcionamento do IoT Edge começou com sucesso, o estado dos módulos **$edgeAgent** e **$edgeHub** está **em execução:**

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/iot-edge-runtime.png" alt-text="Screenshot mostrando os módulos $edgeAgent e $edgeHub em execução no gateway IoT Edge.":::

> [!TIP]
> Poderá ter de esperar alguns minutos enquanto a máquina virtual se inicia e o dispositivo está a provisionado na sua aplicação IoT Central.

## <a name="configure-the-gateway"></a>Configurar o gateway

Para que o seu dispositivo IoT Edge funcione como um gateway transparente, necessita de alguns certificados para provar a sua identidade em qualquer dispositivo a jusante. Este artigo utiliza certificados de demonstração. Em ambiente de produção, utilize certificados da sua autoridade de certificados.

Para gerar os certificados de demonstração e instalá-los no seu dispositivo gateway:

1. Utilize o SSH para ligar e iniciar sinstrução na máquina virtual do seu dispositivo gateway.

1. Executar os seguintes comandos para clonar o repositório IoT Edge e gerar os seus certificados de demonstração:

    ```bash
    # Clone the repo
    cd ~
    git clone https://github.com/Azure/iotedge.git

    # Generate the demo certificates
    mkdir certs
    cd certs
    cp ~/iotedge/tools/CACertificates/*.cnf .
    cp ~/iotedge/tools/CACertificates/certGen.sh .
    ./certGen.sh create_root_and_intermediate
    ./certGen.sh create_edge_device_ca_certificate "mycacert"
    ```

    Depois de executar os comandos anteriores, os seguintes ficheiros estão prontos a ser utilizados nos próximos passos:

    - *~/certs/certs/azure-iot-test-only.root.ca.cert.pem* - O certificado de CA raiz utilizado para fazer todos os outros certificados de demonstração para testar um cenário IoT Edge.
    - *~/certs/certs/iot-edge-device-mycacert-full-chain.cert.pem* - Um certificado de CA de dispositivo que é referenciado a partir do ficheiro *config.yaml.* Num cenário de gateway, este certificado de CA é como o dispositivo IoT Edge verifica a sua identidade para dispositivos a jusante.
    - *~/certs/private/iot-edge-device-mycacert.key.pem* - A chave privada associada ao certificado ca do dispositivo.

    Para saber mais sobre estes certificados de demonstração, consulte [criar certificados de demonstração para testar as funcionalidades do dispositivo IoT Edge](../../iot-edge/how-to-create-test-certificates.md).

1. Abra o ficheiro *config.yaml* num editor de texto. Por exemplo:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Localize as `Certificate settings` definições. Descomprimir e modificar as definições do certificado da seguinte forma:

    ```text
    certificates:
      device_ca_cert: "file:///home/AzureUser/certs/certs/iot-edge-device-ca-mycacert-full-chain.cert.pem"
      device_ca_pk: "file:///home/AzureUser/certs/private/iot-edge-device-ca-mycacert.key.pem"
      trusted_ca_certs: "file:///home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem"
    ```

    O exemplo acima mostrado pressupõe que está assinado como **AzureUser** e criou um dispositivo certificado ca chamado "mycacert".

1. Guarde as alterações e reinicie o tempo de execução do IoT Edge:

    ```bash
    sudo systemctl restart iotedge
    ```

Se o tempo de funcionamento do IoT Edge começar com sucesso após as alterações, o estado dos módulos **$edgeAgent** e **$edgeHub** altera-se em **Funcionamento** na página **de Módulos** para o seu dispositivo gateway na IoT Central.

Se o tempo de funcionamento não começar, verifique as alterações efetuadas em *config.yaml* e consulte [Troubleshoot o seu dispositivo IoT Edge](../../iot-edge/troubleshoot.md).

O seu gateway transparente está agora configurado e pronto para começar a encaminhar a telemetria a partir de dispositivos a jusante.

## <a name="provision-a-downstream-device"></a>Disposição de um dispositivo a jusante

Atualmente, o IoT Edge não pode automaticamente providenciar um dispositivo a jusante à sua aplicação IoT Central. Os passos seguintes mostram-lhe como providenciar o `thermostat1` dispositivo. Para completar estes passos, você precisa de um ambiente com Python 3.6 (ou superior) instalado e conectividade internet. O [Azure Cloud Shell](https://shell.azure.com/) tem Python 3.7 pré-instalado:

1. Executar o seguinte comando para instalar o `azure.iot.device` módulo:

    ```bash
    pip install azure.iot.device
    ```

1. Executar o seguinte comando para descarregar o script Python que faz o provisionamento:

    ```bash
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/provision_device.py
    ```

1. Para o fornecimento do dispositivo a `thermostat1` jusante na sua aplicação IoT Central, execute os seguintes comandos, substituindo `{your application id scope}` e `{your device primary key}`  :

    ```bash
    export IOTHUB_DEVICE_DPS_DEVICE_ID=thermostat1
    export IOTHUB_DEVICE_DPS_ID_SCOPE={your application id scope}
    export IOTHUB_DEVICE_DPS_DEVICE_KEY={your device primary key}
    python provision_device.py
    ```

Na sua aplicação IoT Central, verifique se o estado do **dispositivo** para o dispositivo termóstato1 está agora **previsto**. 

## <a name="configure-a-downstream-device"></a>Configure um dispositivo a jusante

Na secção anterior, configuraste a `edgegateway` máquina virtual com os certificados de demonstração para que possa funcionar como porta de entrada. A `leafdevice` máquina virtual está pronta para instalar um simulador de termostato que utiliza o gateway para ligar à IoT Central.

A `leafdevice` máquina virtual necessita de uma cópia do certificado de CA raiz que criou na máquina `edgegateway` virtual. Copie o ficheiro */home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem* da `edgegateway` máquina virtual para o seu diretório doméstico na máquina `leafdevice` virtual. Pode utilizar o comando **SCP** para copiar ficheiros de e para uma máquina virtual Linux.

Para saber como verificar a ligação do dispositivo a jusante até ao gateway, consulte [a ligação do gateway](../../iot-edge/how-to-connect-downstream-device.md#test-the-gateway-connection).

Para executar o simulador do termóstato na `leafdevice` máquina virtual:

1. Descarregue a amostra Python para o seu diretório de casa:

    ```bash
    cd ~
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/simple_thermostat.py
    ```

1. Instale o módulo Python do dispositivo Azure IoT:

    ```bash
    sudo apt update
    sudo apt install python3-pip
    pip3 install azure.iot.device
    ```

1. Desafie as variáveis ambientais para configurar a amostra. `{your device shared key}`Substitua-a pela tecla primária da `thermostat1` sua mente anterior. Estas variáveis assumem que o nome da máquina virtual gateway é `edgegateway` e o ID do dispositivo termóstato `thermostat1` é:

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    export IOTHUB_DEVICE_CONNECTION_STRING="HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}"
    export IOTEDGE_ROOT_CA_CERT_PATH=~/azure-iot-test-only.root.ca.cert.pem
    ```

    Note como a cadeia de ligação utiliza o nome do dispositivo gateway e não o nome de um hub IoT.

1. Para executar o código, utilize o seguinte comando:

    ```bash
    python3 simple_thermostat.py
    ```

    A saída deste comando parece:

    ```output
    Connecting using Connection String HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}
    Listening for command requests and property updates
    Press Q to quit
    Sending telemetry for temperature
    Sent message
    Sent message
    Sent message
    ...
    ```

1. Para ver a telemetria em IoT Central, navegue na página **'Vista Geral'** para o dispositivo **termóstato1:**

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-device-telemetry.png" alt-text="Screenshot mostrando telemetria do dispositivo a jusante.":::

    Na página **Sobre** pode ver os valores de propriedade enviados a partir do dispositivo a jusante, e na página **De Comando** pode chamar comandos no dispositivo a jusante.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a configurar uma porta de entrada transparente com a IoT Central, o próximo passo sugerido é aprender mais sobre [o IoT Edge.](../../iot-edge/about-iot-edge.md)
