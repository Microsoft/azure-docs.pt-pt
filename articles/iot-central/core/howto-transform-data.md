---
title: Transforme os dados para a | Central Azure IoT Microsoft Docs
description: Os dispositivos IoT enviam dados em vários formatos que poderá necessitar de transformar. Este artigo descreve como transformar dados tanto no caminho para a IoT Central como na saída. Os cenários descritos usam funções IoT Edge e Azure.
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 7d9575bedbdce96ef59e9b1d77b9034162bc16bf
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730486"
---
# <a name="transform-data-for-iot-central"></a>Transformar dados para ioT Central

*Este tópico aplica-se aos construtores de soluções.*

Os dispositivos IoT enviam dados em vários formatos. Para utilizar os dados do dispositivo com a sua aplicação IoT Central, poderá ter de utilizar uma transformação para:

- Torne o formato dos dados compatível com a sua aplicação IoT Central.
- Converter unidades.
- Calcular novas métricas.
- Enriqueça os dados de outras fontes.

Este artigo mostra-lhe como transformar dados de dispositivos fora da IoT Central, quer em entrada ou saída.

O diagrama a seguir mostra três rotas para os dados que incluem transformações:

:::image type="content" source="media/howto-transform-data/transform-data.png" alt-text="Resumo das rotas de transformação de dados tanto ingresss como saídas" border="false":::

A tabela a seguir mostra três tipos de transformação de exemplo:

| Transformação | Descrição | Exemplo  | Notas |
|------------------------|-------------|----------|-------|
| Formato de mensagem         | Converter para ou manipular mensagens JSON. | CSV para JSON  | Na entrada. A IoT Central só aceita mensagens JSON de valor. Para saber mais, consulte [telemetria, propriedade e cargas de comando.](concepts-telemetry-properties-commands.md) |
| Computação           | Funções matemáticas que [as Funções Azure](../../azure-functions/index.yml) podem executar. | Conversão unitária de Fahrenheit para Celsius.  | Transforme usando o padrão de saída para tirar partido da entrada de dispositivos escaláveis através da ligação direta à IoT Central. A transformação dos dados permite-lhe utilizar funcionalidades da IoT Central, tais como visualizações e empregos. |
| Enriquecimento de Mensagens     | Enriquecimentos de fontes de dados externas não encontradas em propriedades do dispositivo ou telemetria. Para saber mais sobre enriquecimentos internos, consulte [dados de IoT de exportação para destinos em nuvem usando a exportação de dados](howto-export-data.md) | Adicione informações meteorológicas a mensagens utilizando dados de localização dos dispositivos. | Transforme usando o padrão de saída para tirar partido da entrada de dispositivos escaláveis através da ligação direta à IoT Central. |

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos deste artigo, precisa de uma subscrição ativa do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Para configurar a solução, precisa de uma aplicação IoT Central. Para aprender a criar uma aplicação IoT Central, consulte [Criar uma aplicação Azure IoT Central](quick-deploy-iot-central.md).

## <a name="data-transformation-at-ingress"></a>Transformação de dados em entrada

Para transformar os dados do dispositivo na entrada, existem duas opções:

- **IoT Edge**: Utilize um módulo IoT Edge para transformar dados de dispositivos a jusante antes de enviar os dados para a sua aplicação IoT Central.

- **Ponte do dispositivo IoT Central**: A [ponte do dispositivo IoT Central](https://github.com/Azure/iotc-device-bridge) liga outras nuvens de dispositivos IoT, como sigfox, particle, e The Things Network, à IoT Central. A ponte do dispositivo utiliza uma função Azure para reencaminhar os dados e pode personalizar a função para transformar os dados do dispositivo.

### <a name="use-iot-edge-to-transform-device-data"></a>Use ioT Edge para transformar dados do dispositivo

:::image type="content" source="media/howto-transform-data/transform-ingress.png" alt-text="Transformação de dados em entrada usando IoT Edge" border="false":::

Neste cenário, um módulo IoT Edge transforma os dados de dispositivos a jusante antes de os encaminhar para a sua aplicação IoT Central. A um nível elevado, os passos para configurar este cenário são:

1. **Configurar um dispositivo IoT Edge**: Instale e proteja um dispositivo IoT Edge como porta de entrada e ligue o gateway à sua aplicação IoT Central.

1. **Ligue o dispositivo a jusante ao dispositivo IoT Edge:** Ligue os dispositivos a jusante ao dispositivo IoT Edge e fornece-os à sua aplicação IoT Central.

1. **Transforme os dados do dispositivo em IoT Edge:** Crie um módulo IoT Edge para transformar os dados. Coloque o módulo no dispositivo de gateway IoT Edge que encaminha os dados do dispositivo transformado para a sua aplicação IoT Central.

1. **Verifique:** Envie dados de um dispositivo a jusante para o gateway e verifique se os dados do dispositivo transformado chegam à sua aplicação IoT Central.

No exemplo descrito nas seguintes secções, o dispositivo a jusante envia dados de CSV no seguinte formato para o dispositivo de gateway IoT Edge:

```csv
"<temperature >, <pressure>, <humidity>"
```

Pretende utilizar um módulo IoT Edge para transformar os dados no seguinte formato JSON antes de ser enviado para a IoT Central:

```json
{
  "device": {
      "deviceId": "<downstream-deviceid>"
  },
  "measurements": {
    "temp": <temperature>,
    "pressure": <pressure>,
    "humidity": <humidity>,
  }
}
```

Os seguintes passos mostram-lhe como configurar e configurar este cenário:

### <a name="build-the-custom-module"></a>Construa o módulo personalizado

Neste cenário, o dispositivo IoT Edge executa um módulo personalizado que transforma os dados a partir do dispositivo a jusante. Antes de implementar e configurar o dispositivo IoT Edge, tem de:

- Construa o módulo personalizado.
- Adicione o módulo personalizado a um registo de contentores.

O tempo de funcionamento IoT Edge descarrega módulos personalizados a partir de um registo de contentores, como um registo de contentores Azure ou Docker Hub. O [Azure Cloud Shell](../../cloud-shell/overview.md) tem todas as ferramentas necessárias para criar um registo de contentores, construir o módulo e enviar o módulo para o registo:

Para criar um registo de contentores:

1. Abra a [Azure Cloud Shell](https://shell.azure.com/) e inscreva-se na sua subscrição Azure.

1. Executar os seguintes comandos para criar um registo de contentores Azure:

    ```azurecli
    REGISTRY_NAME="{your unique container registry name}"
    az group create --name ingress-scenario --location eastus
    az acr create -n $REGISTRY_NAME -g ingress-scenario --sku Standard --admin-enabled true
    az acr credential show -n $REGISTRY_NAME
    ```

    Tome nota dos `username` `password` valores e valores, use-os mais tarde.

Para construir o módulo personalizado na [Azure Cloud Shell:](https://shell.azure.com/)

1. No [Azure Cloud Shell,](https://shell.azure.com/)navegue para uma pasta adequada.
1. Para clonar o repositório GitHub que contém o código fonte do módulo, execute o seguinte comando:

    ```azurecli
    git clone https://github.com/iot-for-all/iot-central-transform-with-iot-edge
    ```

1. Para construir o módulo personalizado, executar os seguintes comandos na Azure Cloud Shell:

    ```azurecli
    cd iot-central-transform-with-iot-edge/custommodule/transformmodule
    az acr build --registry $REGISTRY_NAME --image transformmodule:0.0.1-amd64 -f Dockerfile.amd64 .
    ```

    Os comandos anteriores podem demorar vários minutos a ser executados.

### <a name="set-up-an-iot-edge-device"></a>Configurar um dispositivo IoT Edge

Este cenário utiliza um dispositivo de gateway IoT Edge para transformar os dados de quaisquer dispositivos a jusante. Esta secção descreve como criar modelos de dispositivos IoT Central para os dispositivos gateway e a jusante na sua aplicação IoT Central. Os dispositivos IoT Edge utilizam um manifesto de implantação para configurar os seus módulos.

Para criar um modelo de dispositivo para o dispositivo a jusante, este cenário utiliza um modelo simples de dispositivo termóstato:

1. Descarregue o modelo do dispositivo para o dispositivo [termóstato](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-2.json) para a sua máquina local.

1. Inscreva-se na sua aplicação IoT Central e navegue para a página **de modelos do Dispositivo.**

1. Selecione **+ Novo,** selecione **Dispositivo IoT**, e selecione **Seguinte: Personalize**.

1. Introduza *o termóstato* como o nome do modelo e selecione **Seguinte: Revê**. Em seguida, selecione **Criar**.

1. **Selecione Importar um modelo** e importar othermostat-2.js *no* ficheiro que descarregou anteriormente.

1. **Selecione Publicar** para publicar o novo modelo de dispositivo.

Para criar um modelo de dispositivo para o dispositivo de gateway IoT Edge:

1. Guarde uma cópia do manifesto de implantação para a sua máquina de desenvolvimento local: [moduledeployment.jsligado](https://raw.githubusercontent.com/iot-for-all/iot-central-transform-with-iot-edge/main/edgemodule/moduledeployment.json).

1. Abra a sua cópia local do *moduledeployment.jsem* arquivo manifesto num editor de texto.

1. Encontre a `registryCredentials` secção e substitua os espaços reservados pelos valores de que fez uma nota quando criou o registo do contentor Azure. O `address` valor `<username>.azurecr.io` parece.

1. Encontre a `settings` secção `transformmodule` para. `<acr or docker repo>`Substitua-o pelo mesmo `address` valor que utilizou no passo anterior. Guarde as alterações.

1. Na sua aplicação IoT Central, navegue na página **de modelos do Dispositivo.**

1. Selecione **+ Novo**, selecione **Azure IoT Edge**, e, em seguida, selecione **Seguinte: Personalize**.

1. Introduza *o dispositivo de gateway IoT Edge* como o nome do modelo do dispositivo. Selecione **Este é um dispositivo de gateway**. **Selecione Procurar** para carregar o *moduledeployment.jsno* ficheiro manifesto de implementação que editou anteriormente.

1. Quando o manifesto de implantação for validado, selecione **Seguinte: Reveja** e, em seguida, **selecione Criar**.

1. No **Modelo**, selecione **Relações**. Selecione **+ Adicionar relacionamento**. Introduza *o dispositivo a jusante* como o nome do visor e selecione **o termóstato** como alvo. Selecione **Guardar**.

1. **Selecione Publicar** para publicar o modelo do dispositivo.

Tem agora dois modelos de dispositivo na sua aplicação IoT Central. O modelo **do dispositivo de gateway IoT Edge** e o modelo do **termóstato** como dispositivo a jusante.

Para registar um dispositivo de gateway na IoT Central:

1. Na sua aplicação IoT Central, navegue na página **Dispositivos.**

1. Selecione **o dispositivo de gateway IoT Edge** e selecione Criar um **dispositivo**. Introduza o *dispositivo de gateway IoT Edge* como nome do dispositivo, introduza o *gateway-01* como iD do dispositivo, certifique-se de que o **dispositivo de gateway IoT Edge** é selecionado como modelo do dispositivo. Selecione **Criar**.

1. Na lista de dispositivos, clique no **dispositivo de gateway IoT Edge** e, em seguida, selecione **Connect**.

1. Tome nota do âmbito de **identificação,** **identificação do dispositivo** e valores-chave **primários** para o **dispositivo de gateway IoT Edge**. Usa-os mais tarde.

Para registar um dispositivo a jusante na IoT Central:

1. Na sua aplicação IoT Central, navegue na página **Dispositivos.**

1. Selecione **o termóstato** e selecione **Criar um dispositivo**. Introduza *o termóstato* como nome do dispositivo, *introduza a jusante-01* como iD do dispositivo, certifique-se de que **o termóstato** está selecionado como modelo do dispositivo. Selecione **Criar**.

1. Na lista de dispositivos, selecione **o termóstato** e, em seguida, selecione **Fixe o Gateway**. Selecione o modelo do **dispositivo de gateway IoT Edge** e a instância do dispositivo de gateway **IoT Edge.** **Selecione Anexar**.

1. Na lista de dispositivos, clique no **termóstato** e, em seguida, selecione **Connect**.

1. Tome nota do âmbito de **identificação,** **identificação do dispositivo** e **valores de chave primária** para o dispositivo **termóstato.** Usa-os mais tarde.

### <a name="deploy-the-gateway-and-downstream-devices"></a>Implementar o gateway e os dispositivos a jusante

Por conveniência, este artigo utiliza máquinas virtuais Azure para executar os dispositivos gateway e a jusante. Para criar as duas máquinas virtuais Azure, selecione o botão **Implementar para Azure** abaixo e utilize as informações na tabela seguinte para completar o formulário **de implementação Personalizado:**

| Campo | Valor |
| ----- | ----- |
| Grupo de recursos | `ingress-scenario` |
| Gateway prefixo da etiqueta do DNS | Um nome DNS único para esta máquina, como `<your name>edgegateway` |
| Prefixo da etiqueta do DNS a jusante | Um nome DNS único para esta máquina, como `<your name>downstream` |
| ID de Âmbito | O âmbito de ID que fez uma nota de anteriormente |
| Porta de entrada ID IoT Edge do dispositivo | `gateway-01` |
| Porta de borda IoT chave do dispositivo | O valor principal da chave que fez uma nota de anteriormente |
| Tipo de Autenticação | Palavra-passe |
| Senha ou chave de administrador | A sua escolha de senha para a conta **AzureUser** em ambas as máquinas virtuais. |

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

Selecione **Rever + Criar** e, em seguida, **Criar**. Leva alguns minutos para criar as máquinas virtuais no grupo de recursos **de cenário ingress.**

Para verificar se o dispositivo IoT Edge está a funcionar corretamente:

1. Abra a sua aplicação IoT Central. Em seguida, navegue para o **dispositivo IoT Edge Gateway** na lista de dispositivos na página **dispositivos.**

1. Selecione o **separador Módulos** e verifique o estado dos três módulos. Leva alguns minutos para que o tempo de funcionação do IoT Edge seja iniciado na máquina virtual. Quando é iniciado, o estado dos três módulos está **a funcionar.** Se o tempo de funcionaamento do IoT Edge não começar, consulte [Troubleshoot o seu dispositivo IoT Edge](../../iot-edge/troubleshoot.md).

Para que o seu dispositivo IoT Edge funcione como um gateway, precisa de alguns certificados para provar a sua identidade em qualquer dispositivo a jusante. Este artigo utiliza certificados de demonstração. Em ambiente de produção, utilize certificados da sua autoridade de certificados.

Para gerar os certificados de demonstração e instalá-los no seu dispositivo gateway:

1. Utilize o SSH para ligar e iniciar sinstrução na máquina virtual do seu dispositivo gateway. Pode encontrar o nome DNS desta máquina virtual no portal Azure. Navegue para a máquina virtual **edgegateway** no grupo de recursos **de cenário ingress.**

    > [!TIP]
    > Pode ser necessário abrir a porta 22 para acesso SSH em ambas as suas máquinas virtuais antes de poder utilizar o SSH para ligar a partir da sua máquina local ou da Azure Cloud Shell.

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

Se o tempo de funcionamento do IoT Edge começar com sucesso após as alterações, o estado dos módulos **$edgeAgent** e **$edgeHub** muda para **o Funcionamento**. Pode ver estes valores de estado na página **Módulos** para o seu dispositivo gateway no IoT Central.

Se o tempo de funcionamento não começar, verifique as alterações efetuadas em *config.yaml* e consulte [Troubleshoot o seu dispositivo IoT Edge](../../iot-edge/troubleshoot.md).

### <a name="connect-downstream-device-to-iot-edge-device"></a>Ligue o dispositivo a jusante ao dispositivo IoT Edge

Para ligar um dispositivo a jusante ao dispositivo de gateway IoT Edge:

1. Utilize o SSH para ligar e iniciar sinstrução na máquina virtual do seu dispositivo a jusante. Pode encontrar o nome DNS desta máquina virtual no portal Azure. Navegue para a máquina virtual **dedevice folha** no grupo de recursos **de cenário de entrada.**

    > [!TIP]
    > Pode ser necessário abrir a porta 22 para acesso SSH em ambas as suas máquinas virtuais antes de poder utilizar o SSH para ligar a partir da sua máquina local ou da Azure Cloud Shell.

1. Para clonar o repositório GitHub com o código fonte para o dispositivo a jusante da amostra, executar o seguinte comando:

    ```bash
    cd ~
    git clone https://github.com/iot-for-all/iot-central-transform-with-iot-edge
    ```

1. Para copiar o certificado exigido do dispositivo gateway, execute os `scp` seguintes comandos. Este `scp` comando utiliza o nome de anfitrião para identificar a máquina virtual `edgegateway` gateway. Será solicitado para a sua senha:

    ```bash
    cd ~/iot-central-transform-with-iot-edge
    scp AzureUser@edgegateway:/home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem leafdevice/certs
    ```

1. Navegue na pasta *leafdevice* e instale as embalagens necessárias. Em seguida, executar os `build` scripts e `start` scripts para provisão e ligar o dispositivo ao gateway:

    ```bash
    cd ~/iot-central-transform-with-iot-edge/leafdevice
    sudo apt update
    sudo apt install nodejs npm node-typescript
    npm install
    npm run-script build
    npm run-script start
    ```

1. Introduza o ID do dispositivo, o ID de âmbito e a tecla SAS para o dispositivo a jusante que criou anteriormente. Para o nome de anfitrião, insira `edgegateway` . A saída do comando parece:

    ```output
    Registering device downstream-01 with scope 0ne00284FD9
    Registered device downstream-01.
    Connecting device downstream-01
    Connected device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    ```

### <a name="verify"></a>Verificação

Para verificar se o cenário está em execução, navegue para o seu **dispositivo de gateway IoT Edge** na IoT Central:

:::image type="content" source="media/howto-transform-data/transformed-data.png" alt-text="Screenshot que mostra dados transformados na página de dispositivos.":::

- Selecione **Módulos**. Verifique se os três módulos IoT Edge **$edgeAgent,** **$edgeHub** e **transformmodule** estão em funcionamento.
- Selecione os **Dispositivos a jusante** e verifique se o dispositivo a jusante está a ser adustado.
- Selecione **dados brutos**. Os dados da telemetria na coluna **de dados** não modelo parecem:

    ```json
    {"device":{"deviceId":"downstream-01"},"measurements":{"temperature":85.21208,"pressure":59.97321,"humidity":77.718124,"scale":"farenheit"}}
    ```

Como o dispositivo IoT Edge está a transformar os dados do dispositivo a jusante, a telemetria está associada ao dispositivo de gateway na IoT Central. Para visualizar a telemetria, crie uma nova versão do modelo do **dispositivo de gateway IoT Edge** com definições para os tipos de telemetria.

## <a name="data-transformation-at-egress"></a>Transformação de dados na saída

Pode ligar os seus dispositivos à IoT Central, exportar os dados do dispositivo para um motor de computação para os transformar e, em seguida, enviar os dados transformados de volta para a IoT Central para a gestão e análise do dispositivo. Por exemplo:

- Os seus dispositivos enviam dados de localização para a IoT Central.
- A IoT Central exporta os dados para um motor de computação que melhora os dados de localização com informações meteorológicas.
- O motor computacional envia os dados melhorados de volta para a IoT Central.

Pode utilizar a ponte do [dispositivo IoT Central](https://github.com/Azure/iotc-device-bridge) como motor de computação para transformar os dados exportados da IoT Central.

Uma vantagem de transformar dados em saída é que os seus dispositivos se ligam diretamente à IoT Central, o que facilita o envio de comandos para dispositivos ou atualizar as propriedades do dispositivo. No entanto, com este método, poderá utilizar mais mensagens do que o seu loteamento mensal e aumentar o custo de utilização da Azure IoT Central.

### <a name="use-the-iot-central-device-bridge-to-transform-device-data"></a>Utilize a ponte do dispositivo IoT Central para transformar dados do dispositivo

:::image type="content" source="media/howto-transform-data/transform-egress.png" alt-text="Transformação de dados na saída usando IoT Edge" border="false":::

Neste cenário, um motor computacional transforma os dados do dispositivo exportados da IoT Central antes de enviá-los de volta para a sua aplicação IoT Central. A um nível elevado, os passos para configurar este cenário são:

1. **Configurar o motor computacional:** Crie uma ponte de dispositivos IoT Central para funcionar como um motor de computação para a transformação de dados.

1. **Transforme os dados do dispositivo na ponte do dispositivo:** Transforme os dados na ponte do dispositivo modificando o código de função da ponte do dispositivo para o seu caso de utilização da transformação de dados.

1. **Permitir o fluxo de dados da IoT Central para a ponte do dispositivo:** Exporte os dados da IoT Central para a ponte do dispositivo para transformação. Em seguida, encaminhar os dados transformados de volta para a IoT Central. Quando criar a exportação de dados, utilize filtros de propriedade de mensagens apenas para exportar dados não transformados.

1. **Verifique:** Ligue o seu dispositivo à aplicação IoT Central e verifique se há dados brutos do dispositivo e dados transformados na IoT Central.

<!-- To Do - doesn't the device send JSON data? -->
No exemplo descrito nas seguintes secções, o dispositivo envia dados CSV no seguinte formato para o dispositivo de gateway IoT Edge:

```csv
"<temperature in degrees C>, <humidity>, <latitude>, <longitude>"
```

Utiliza a ponte do dispositivo para transformar os dados do dispositivo por:

- Alterando a unidade de temperatura de centigrado para fahrenheit.
- Enriquecendo os dados do dispositivo com dados meteorológicos retirados do serviço [De Tempo Aberto](https://openweathermap.org/) para os valores de latitude e longitude.

Em seguida, a ponte do dispositivo envia os dados transformados para a IoT Central no seguinte formato:

```json
{
  "temp": <temperature in degrees F>,
  "humidity": <humidity>,
  "lat": <latitude>,
  "lon": <logitude>,
  "weather": {
    "weather_temp": <temperature at lat/lon>,
    "weather_humidity": <humidity at lat/lon>,
    "weather_pressure": <pressure at lat/lon>,
    "weather_windspeed": <wind speed at lat/lon>,
    "weather_clouds": <cloud cover at lat/lon>,
    "weather_uvi": <UVI at lat/lon>
  }
}
```

Os seguintes passos mostram-lhe como configurar e configurar este cenário:

### <a name="retrieve-your-iot-central-connection-settings"></a>Recupere as definições de ligação IoT Central

Antes de configurar este cenário, precisa de obter algumas definições de ligação da sua aplicação IoT Central:

1. Inscreva-se na sua aplicação IoT Central.

1. Navegue para a **ligação do dispositivo > administração**.

1. Tome nota do âmbito de **identificação.** Usa este valor mais tarde.

1. Selecione o grupo de inscrição **SaS-IoT-Devices.** Tome nota da chave primária de assinatura de acesso partilhado. Usa este valor mais tarde.

### <a name="set-up-a-compute-engine"></a>Configurar um motor computacional

Este cenário utiliza a mesma implantação de Funções Azure que a ponte do dispositivo IoT Central. Para implantar a ponte do dispositivo, selecione o botão **Implementar para Azure** abaixo e utilize as informações na tabela seguinte para completar o formulário **de implantação personalizado:**

| Campo | Valor |
| ----- | ----- |
| Grupo de recursos | Criar um novo grupo de recursos chamado `egress-scenario` |
| Região | Selecione a região mais próxima de si. |
| ID de Âmbito | Utilize o âmbito de **identificação** que fez anteriormente. |
| Chave SAS Central IoT | Utilize a chave primária de assinatura de acesso partilhado para o grupo de inscrição **saS-IoT-Devices.** Já fez uma nota deste valor anteriormente. |

[ ![ Desdobre-se para Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fiotc-device-bridge%2Fmaster%2Fazuredeploy.json).

Selecione **Rever + Criar** e, em seguida, **Criar**. Leva alguns minutos para criar a função Azure e recursos relacionados no grupo de recursos **de cenário de saída.**

### <a name="transform-device-data-in-the-device-bridge"></a>Transforme os dados do dispositivo na ponte do dispositivo

Para configurar a ponte do dispositivo para transformar os dados do dispositivo exportado:

1. Obtenha uma chave API de aplicação do serviço Open Weather. Uma conta é gratuita com uso limitado do serviço. Para criar uma chave API de aplicação, crie uma conta no portal de [serviço Open Weather](https://openweathermap.org/) e siga as instruções. Use a sua chave API de tempo aberto mais tarde.

1. No portal Azure, navegue para a App function no grupo de recursos **de cenário de saída.**

1. Na navegação à esquerda, em **Ferramentas de Desenvolvimento,** selecione **App Service Editor (Preview)**.

1. Selecione **Vá &rarr;** para abrir a página **do Editor de Serviço de Aplicações.** Efetue as seguintes alterações:

    1. Abra o ficheiro *wwwroot/IoTCIntegration/index.js.* Substitua todo o código deste ficheiro pelo código [index.js](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/Azure_function/index.js).

    1. No novo *index.js*, atualize o `openWeatherAppId` ficheiro variável com a chave API open weather que obteve anteriormente.

        ```javascript
        const openWeatherAppId = '<Your Open Weather API Key>'
        ```

    1. Adicione uma propriedade de mensagem aos dados enviados pela função à IoT Central. A IoT Central utiliza esta propriedade para evitar a exportação dos dados transformados. Para errar esta alteração, abra o ficheiro *wwwroot/IoTCIntegration/lib/engine.js.* Localize o seguinte código:

        ```javascript
        if (timestamp) {
          message.properties.add('iothub-creation-time-utc', timestamp);
        }
        ```

        Adicione o seguinte código logo após o código no corte anterior:

        ```javascript
        // add a message property that we can look for in data export to not re-compute computed telemetry
        message.properties.add('computed', true);
        ```

        Para referência, pode ver um exemplo completo do ficheiro [engine.js.](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/Azure_function/lib/engine.js)

1. No **Editor de Serviço de Aplicações**, selecione **Consola** na navegação à esquerda. Executar os seguintes comandos para instalar as embalagens necessárias:

    ```bash
    cd IoTCIntegration
    npm install
    ```

    Este comando pode demorar alguns minutos a ser executado.

1. Volte à página geral da **função Azure** e reinicie a função:

    :::image type="content" source="media/howto-transform-data/azure-function.png" alt-text="Reiniciar a função":::

1. Selecione **Funções** na navegação à esquerda. Em seguida, selecione **IoTCIntegration**. Selecione **Código + Teste**.

1. Tome nota do URL de função, precisa deste valor mais tarde:

    :::image type="content" source="media/howto-transform-data/get-function-url.png" alt-text="Obtenha o URL de função":::

### <a name="enable-data-flow-from-iot-central-to-the-device-bridge"></a>Permitir o fluxo de dados da IoT Central para a ponte do dispositivo

Esta secção descreve como configurar a aplicação Azure IoT Central.

Primeiro, guarde o ficheiro [do modelo](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/model.json) do dispositivo para a sua máquina local.

Para adicionar um modelo de dispositivo à sua aplicação IoT Central, navegue para a sua aplicação IoT Central e, em seguida, :

1. Inscreva-se na sua aplicação IoT Central e navegue para a página **de modelos do Dispositivo.**

1. Selecione **+ Novo**, selecione **Dispositivo IoT,** selecione **Seguinte: Personalize,** *introduza o modelo Compute* como o nome do modelo. Selecione **Seguinte: Revisão**. Em seguida, selecione **Criar**.

1. **Selecione Importar um modelo** e navegar para omodel.js *no* ficheiro que descarregou anteriormente.

1. Depois de importar o modelo, **selecione Publicar** para publicar o modelo de dispositivo do **modelo Compute.**

Para configurar a exportação de dados para enviar dados para a sua ponte dispositivo:

1. Na sua aplicação IoT Central, selecione **Data export.**

1. Selecione **+ Novo destino** para criar um destino a utilizar com a ponte do dispositivo. Ligue para a *função de destino Compute*, para **o tipo de destino** selecionar **Webhook**. Para o URL callback, selecione pasta no URL de função que fez uma nota anterior. Deixe a **Autorização** como **No Auth**.

1. Guarde as alterações.

1. Selecione a **+ Nova exportação** e crie uma exportação de dados chamada *exportação computacional.*

1. Adicione um filtro apenas aos dados do dispositivo de exportação para o modelo do dispositivo que está a usar. Selecione **+ Filtro**, selecione **o modelo de dispositivo de** item, selecione o operador **Equals**, e selecione o modelo de dispositivo **do modelo Compute** que acabou de criar.

1. Adicione um filtro de mensagem para diferenciar entre dados transformados e não-transformados. Este filtro evita o envio de valores transformados de volta à ponte do dispositivo. Selecione **+ filtro de propriedade de mensagem** e introduza o valor do nome *calculado,* em seguida, selecione o operador **Não existe**. A cadeia `computed` é utilizada como palavra-chave no código de exemplo da ponte do dispositivo.

1. Para o destino, selecione o destino **de função Compute** que criou anteriormente.

1. Guarde as alterações. Após um minuto ou mais, o **estado de exportação** mostra como **Saudável**.

### <a name="verify"></a>Verificação

O dispositivo de amostra que utiliza para testar o cenário está escrito em Node.js. Certifique-se de que tem Node.js e NPM instalados na sua máquina local. Se não pretender instalar estes pré-requisitos, utilize o[Azure Cloud Shell](https://shell.azure.com/) que os tenha pré-instalados.

Para executar um dispositivo de amostra que testa o cenário:

1. Clone o repositório GitHub que contém o código de amostra, executar o seguinte comando:

    ```bash
    git clone https://github.com/iot-for-all/iot-central-compute
    ```

1. Para ligar o dispositivo de amostra à sua aplicação IoT Central, edite as definições de ligação no ficheiro *iot-central compute/device/device.js.* Substitua a chave de ID de âmbito e de grupo SAS pelos valores que fez anteriormente:

    ```javascript
    // These values need to be filled in from your Azure IoT Central application
    //
    const scopeId = "<IoT Central Scope Id value>";
    const groupSasKey = "<IoT Central Group SAS key>";
    //
    ```

    Guarde as alterações.

1. Utilize os seguintes comandos para instalar as embalagens necessárias e executar o dispositivo:

    ```bash
    cd ~/iot-central-compute/device
    npm install
    node device.js
    ```

1. O resultado deste comando parece a seguinte saída:

    ```output
    registration succeeded
    assigned hub=iotc-2bd611b0....azure-devices.net
    deviceId=computeDevice
    Client connected
    send status: MessageEnqueued [{"data":"33.23, 69.09, 30.7213, -61.1192"}]
    send status: MessageEnqueued [{"data":"2.43, 75.86, -2.6358, 162.935"}]
    send status: MessageEnqueued [{"data":"6.19, 76.55, -14.3538, -82.314"}]
    send status: MessageEnqueued [{"data":"33.26, 48.01, 71.9172, 48.6606"}]
    send status: MessageEnqueued [{"data":"40.5, 36.41, 14.6043, 14.079"}]
    ```

1. Na sua aplicação IoT Central, navegue para o dispositivo chamado **computeDevice**. Na vista **de dados raw** existem dois fluxos de telemetria diferentes que aparecem a cada cinco segundos. O fluxo com dados não modelados é a telemetria original, o fluxo com dados modelados são os dados que a função transformou:

    :::image type="content" source="media/howto-transform-data/egress-telemetry.png" alt-text="Screenshot que mostra dados crus originais e transformados.":::

## <a name="clean-up-resources"></a>Limpar os recursos

Se já não necessitar dos recursos Azure que criou enquanto segue os passos deste guia, elimine os [grupos de recursos no portal Azure](https://portal.azure.com/?r=1#blade/HubsExtension/BrowseResourceGroups).

Os dois grupos de recursos que usou neste guia são **cenário de** entrada e cenário **de saída.**

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre as diferentes opções para transformar dados de dispositivos para a IoT Central, tanto em entrada como em saída. O artigo incluía passos para dois cenários específicos:

- Utilize um módulo IoT Edge para transformar dados de dispositivos a jusante antes de os dados serem enviados para a sua aplicação IoT Central.
- Utilize funções Azure para transformar dados fora da IoT Central. Neste cenário, a IoT Central utiliza uma exportação de dados para enviar dados de entrada para uma função Azure a ser transformado. A função envia os dados transformados de volta para a sua aplicação IoT Central.

Agora que aprendeu a transformar dados de dispositivos fora da sua aplicação Azure IoT Central, pode aprender [a usar analítica para analisar dados do dispositivo no IoT Central.](howto-create-analytics.md)
