---
title: Verificar a conectividade do dispositivo com o Hub IoT do Azure
description: Utilize as ferramentas do Hub IoT para, durante o desenvolvimento, resolver problemas de conectividade do dispositivo com o seu hub do IoT.
services: iot-hub
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.custom: mvc
ms.date: 02/22/2019
ms.topic: tutorial
ms.service: iot-hub
ms.openlocfilehash: cd60129e2da0b0c2130b300159953bd81c4aeb82
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58077569"
---
# <a name="tutorial-use-a-simulated-device-to-test-connectivity-with-your-iot-hub"></a>Tutorial: Utilizar um dispositivo simulado para testar a conectividade com o seu hub IoT

Neste tutorial, irá utilizar ferramentas do portal do Hub IoT do Azure e comandos da CLI do Azure para testar a conectividade do dispositivo. Este tutorial também utiliza um simulador de dispositivos simples que executa no seu computador.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Verificar a autenticação do dispositivo
> * Verificar a conectividade do dispositivo para a cloud
> * Verificar a conectividade da cloud para o dispositivo
> * Verificar a sincronização de dispositivos duplos

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Os scripts da CLI que executa neste tutorial utilizam a [Extensão do Microsoft Azure IoT para a CLI do Azure](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md). Para instalar esta extensão, execute o seguinte comando da CLI:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

A aplicação de simulador de dispositivos que irá executar neste tutorial é escrita com Node.js. Precisa do Node.js. v4.X.X ou posterior no seu computador de desenvolvimento.

Pode transferir o Node.js para múltiplas plataformas em [nodejs.org](https://nodejs.org).

Pode verificar qual a versão atual do Node.js no seu computador de desenvolvimento através do seguinte comando:

```cmd/sh
node --version
```

Transfira o projeto Node.js de simulador de dispositivos de exemplo em https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip e extraia o arquivo ZIP.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Se tiver criado um hub do IoT gratuito ou padrão no tutorial ou guia de início rápido anterior, pode ignorar este passo.

[!INCLUDE [iot-hub-tutorials-create-free-hub](../../includes/iot-hub-tutorials-create-free-hub.md)]

## <a name="check-device-authentication"></a>Verificar a autenticação do dispositivo

Um dispositivo tem de ser autenticado com o seu hub antes de poder trocar dados com o mesmo. Pode utilizar a ferramenta **Dispositivos IoT** na secção **Gestão de Dispositivos** do portal para gerir os seus dispositivos e verificar as chaves de autenticação que estão a ser utilizadas. Nesta secção do tutorial, irá adicionar um novo dispositivo de teste, obter a chave e verificar se o dispositivo de teste consegue estabelecer ligação ao hub. Posteriormente, irá repor a chave de autenticação para observar o que acontece quando um dispositivo tenta utilizar uma chave desatualizada. Esta secção do tutorial utiliza o portal do Azure para criar, gerir e monitorizar um dispositivo e o simulador de dispositivos Node.js de exemplo.

Inicie sessão no portal e navegue para o seu hub do IoT. Em seguida, navegue para a ferramenta **Dispositivos IoT**:

![Ferramenta Dispositivos IoT](media/tutorial-connectivity/iot-devices-tool.png)

Para registar um novo dispositivo, clique em **+ Adicionar**, defina o **ID do Dispositivo** como **MyTestDevice** e clique em **Guardar**:

![Adicionar um novo dispositivo](media/tutorial-connectivity/add-device.png)

Para obter a cadeia de ligação de **MyTestDevice**, clique na mesma na lista de dispositivos e copie o valor **Cadeia de ligação - chave primária**. A cadeia de ligação inclui a *chave de acesso partilhado* do dispositivo.

![Obter a cadeia de ligação do dispositivo](media/tutorial-connectivity/copy-connection-string.png)

Para simular o **MyTestDevice** a enviar dados telemétricos para o hub do IoT, execute a aplicação de dispositivo simulado Node.js que transferiu anteriormente.

Numa janela de terminal no computador de desenvolvimento, navegue para a pasta raiz do projeto Node.js de exemplo que transferiu. Em seguida, navegue para o **iot hub\Tutorials\ConnectivityTests** pasta.

Na janela de terminal, execute os seguintes comandos para instalar as bibliotecas necessárias e execute a aplicação de dispositivo simulado. Utilize a cadeia de ligação do dispositivo tomou nota do quando adicionou o dispositivo no portal.

```cmd/sh
npm install
node SimulatedDevice-1.js "{your device connection string}"
```

A janela de terminal apresenta informações à medida que tenta estabelecer ligação ao seu hub:

![Ligar um dispositivo simulado](media/tutorial-connectivity/sim-1-connected.png)

Efetuou a autenticação com êxito a partir de um dispositivo através de uma chave de dispositivo gerada pelo seu hub do IoT.

### <a name="reset-keys"></a>Repor chaves

Nesta secção, irá repor a chave do dispositivo e observar o erro quando o dispositivo simulado tenta estabelecer ligação.

Para repor a chave primária do dispositivo **MyTestDevice**, execute os seguintes comandos:

```azurecli-interactive
# Generate a new Base64 encoded key using the current date
read key < <(date +%s | sha256sum | base64 | head -c 32)

# Requires the IoT Extension for Azure CLI
# az extension add --name azure-cli-iot-ext

# Reset the primary device key for MyTestDevice
az iot hub device-identity update --device-id MyTestDevice --set authentication.symmetricKey.primaryKey=$key --hub-name {YourIoTHubName}
```

Na janela de terminal no computador de desenvolvimento, execute a aplicação de dispositivo simulado novamente:

```cmd/sh
npm install
node SimulatedDevice-1.js "{your device connection string}"
```

Desta vez, verá um erro de autenticação quando a aplicação tenta estabelecer ligação:

![Erro de ligação](media/tutorial-connectivity/sim-1-fail.png)

### <a name="generate-shared-access-signature-sas-token"></a>Obter token de assinatura de acesso partilhado (SAS)

Se o seu dispositivo utilizar um dos SDKs de dispositivo do Hub IoT, o código da biblioteca do SDK irá gerar o token SAS utilizado para autenticar com o hub. É gerado um token SAS a partir do nome do hub, do nome do dispositivo e da chave do dispositivo.

Em alguns cenários, tal como num gateway de protocolo de cloud ou como parte de um esquema de autenticação personalizado, poderá ter de gerar o token SAS manualmente. Para resolver problemas com o seu código de geração de SAS, é útil gerar um token SAS de boa conhecida para utilizar durante o teste.

> [!NOTE]
> A amostra SimulatedDevice-2.js inclui exemplos de geração de um token SAS com e sem o SDK.

Para gerar um token SAS válido conhecido com a CLI, execute o seguinte comando:

```azurecli-interactive
az iot hub generate-sas-token --device-id MyTestDevice --hub-name {YourIoTHubName}
```

Tome nota do texto completo do token SAS gerado. Um token SAS tem o seguinte aspeto: `SharedAccessSignature sr=tutorials-iot-hub.azure-devices.net%2Fdevices%2FMyTestDevice&sig=....&se=1524155307`

Numa janela de terminal no computador de desenvolvimento, navegue para a pasta raiz do projeto Node.js de exemplo que transferiu. Em seguida, navegue para o **iot hub\Tutorials\ConnectivityTests** pasta.

Na janela de terminal, execute os seguintes comandos para instalar as bibliotecas necessárias e executar a aplicação de dispositivo simulado:

```cmd/sh
npm install
node SimulatedDevice-2.js "{Your SAS token}"
```

A janela de terminal apresenta informações à medida que tenta estabelecer ligação ao seu hub através do token SAS:

![Ligar a um dispositivo simulado com um token](media/tutorial-connectivity/sim-2-connected.png)

Efetuou a autenticação com êxito a partir de um dispositivo através de um token SAS de teste gerado por um comando da CLI. O ficheiro **SimulatedDevice-2.js** inclui um código de exemplo que mostra como gerar um token SAS no código.

### <a name="protocols"></a>Protocolos

Um dispositivo pode utilizar qualquer um dos seguintes protocolos para estabelecer ligação ao hub do IoT:

| Protocolo | Porta de saída |
| --- | --- |
| MQTT |8883 |
| MQTT através de WebSockets |443 |
| AMQP |5671 |
| AMQP através de WebSockets |443 |
| HTTPS |443 |

Se a porta de saída estiver bloqueada por uma firewall, o dispositivo não conseguirá estabelecer ligação:

![Porta bloqueada](media/tutorial-connectivity/port-blocked.png)

## <a name="check-device-to-cloud-connectivity"></a>Verificar a conectividade do dispositivo para a cloud

Quando um dispositivo estabelece ligação, normalmente tenta enviar dados telemétricos para o seu hub do IoT. Esta secção mostra como verificar se os dados telemétricos enviados pelo dispositivo são recebidos pelo hub.

Em primeiro lugar, obtenha a cadeia de ligação atual do dispositivo simulado através do seguinte comando:

```azurecli-interactive
az iot hub device-identity show-connection-string --device-id MyTestDevice --output table --hub-name {YourIoTHubName}
```

Para executar um dispositivo simulado que envia mensagens, navegue para o **iot hub\Tutorials\ConnectivityTests** pasta no código que transferiu.

Na janela de terminal, execute os seguintes comandos para instalar as bibliotecas necessárias e executar a aplicação de dispositivo simulado:

```cmd/sh
npm install
node SimulatedDevice-3.js "{your device connection string}"
```

A janela de terminal mostra informações à medida que envia dados telemétricos para o seu hub:

![Dispositivo simulado a enviar mensagens](media/tutorial-connectivity/sim-3-sending.png)

Pode usar **métricas** no portal para verificar que as mensagens de telemetria são atingir o seu hub IoT. Selecione o seu hub do IoT no menu pendente **Recurso**, selecione **Mensagens de telemetria enviadas** como a métrica e defina o intervalo de tempo para **Última hora**. O gráfico mostra a contagem agregada de mensagens enviadas pelo dispositivo simulado:

![Mostrar métricas do Hub IoT](media/tutorial-connectivity/metrics-portal.png)

As métricas demoram alguns minuto a ficar disponíveis após iniciar o dispositivo simulado.

## <a name="check-cloud-to-device-connectivity"></a>Verificar a conectividade da cloud para o dispositivo

Esta secção mostra como pode fazer uma chamada de método direto de teste para um dispositivo, para verificar a conectividade da cloud para o dispositivo. Execute um dispositivo simulado no seu computador de desenvolvimento para escutar chamadas de método direto do seu hub.

Numa janela de terminal, utilize o seguinte comando para executar a aplicação de dispositivo simulado:

```cmd/sh
node SimulatedDevice-3.js "{your device connection string}"
```

Utilize um comando da CLI para chamar um método direto no dispositivo:

```azurecli-interactive
az iot hub invoke-device-method --device-id MyTestDevice --method-name TestMethod --timeout 10 --method-payload '{"key":"value"}' --hub-name {YourIoTHubName}
```

O dispositivo simulado imprime uma mensagem na consola quando recebe uma chamada de método direto:

![O dispositivo simulado recebe uma chamada de método direto](media/tutorial-connectivity/receive-method-call.png)

Quando o dispositivo simulado recebe com êxito a chamada de método direto, envia uma confirmação para o hub:

![Receber confirmação de método direto](media/tutorial-connectivity/method-acknowledgement.png)

## <a name="check-twin-synchronization"></a>Verificar a sincronização de dispositivos duplos

Os dispositivos utilizam duplos para sincronizar o estado entre o dispositivo e o hub. Nesta secção, irá utilizar comandos da CLI para enviar as _propriedades pretendidas_ para um dispositivo e ler as _propriedades reportadas_ enviadas pelo dispositivo.

O dispositivo simulado que utiliza nesta secção envia propriedades reportadas para o hub sempre que é iniciado e imprime as propriedades pretendidas na consola sempre que as recebe.

Numa janela de terminal, utilize o seguinte comando para executar a aplicação de dispositivo simulado:

```cmd/sh
node SimulatedDevice-3.js "{your device connection string}"
```

Para verificar se o hub recebeu as propriedades reportadas do dispositivo, utilize o seguinte comando da CLI:

```azurecli-interactive
az iot hub device-twin show --device-id MyTestDevice --hub-name {YourIoTHubName}
```

No resultado do comando, pode ver a propriedade **devicelaststarted** na secção de propriedades reportadas. Esta propriedade mostra a data e hora em que iniciou o dispositivo simulado pela última vez.

![Ver propriedades reportadas](media/tutorial-connectivity/reported-properties.png)

Para verificar se o hub consegue enviar os valores de propriedades pretendidas para o dispositivo, utilize o seguinte comando da CLI:

```azurecli-interactive
az iot hub device-twin update --set properties.desired='{"mydesiredproperty":"propertyvalue"}' --device-id MyTestDevice --hub-name {YourIoTHubName}
```

O dispositivo simulado imprime uma mensagem quando recebe uma atualização de propriedades pretendidas do hub:

![Receber propriedades pretendidas](media/tutorial-connectivity/desired-properties.png)

Além de receber as alterações de propriedades pretendidas à medida que são feitas, o dispositivo simulado verifica automaticamente a existência de propriedades pretendidas quando é iniciado.

## <a name="clean-up-resources"></a>Limpar recursos

Se já não precisar do Hub IoT, elimine-o, bem como ao grupo de recursos, no portal. Para tal, selecione o grupo de recursos **tutorial-iot-hub-rg** que contém o seu hub do IoT e clique em **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a verificar as chaves do dispositivo, a verificar a conectividade do dispositivo para a cloud, a verificar a conectividade da cloud para o dispositivo e a verificar a sincronização de dispositivos duplos. Para saber mais sobre como monitorizar o seu hub do IoT, aceda ao artigo de procedimentos de monitorização do Hub IoT.

> [!div class="nextstepaction"]
> [Monitorizar com diagnósticos](iot-hub-monitor-resource-health.md)
