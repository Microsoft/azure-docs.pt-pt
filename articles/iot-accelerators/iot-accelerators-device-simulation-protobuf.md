---
title: Utilizar tampão de protocolo com simulação de dispositivo - Azure/ Microsoft Docs
description: Neste guia de como orientar, aprende-se a utilizar buffers protocolar para serializar a telemetria enviada do acelerador de soluções de simulação de dispositivo.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: bc08cd5183bcaac6cb77ccb0938b07893f082862
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250217"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>Serialize a telemetria usando buffers protocolar

Buffers protocole (Protobuf) é um formato de serialização binário para dados estruturados. Protobuf é projetado para enfatizar a simplicidade e desempenho com o objetivo de ser menor e mais rápido que XML.

A Simulação do Dispositivo suporta a versão **proto3** do idioma dos tampões protocolar.

Como o Protobuf requer código compilado para serializar os dados, tem de construir uma versão personalizada da Simulação do Dispositivo.

Os passos neste como-guia mostram-lhe como:

1. Preparar um ambiente de desenvolvimento
1. Especifique a utilização do formato Protobuf num modelo de dispositivo
1. Defina o seu formato Protobuf
1. Gerar aulas de Protobuf
1. Teste local

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos neste guia de como orientar, precisa de:

* Visual Studio Code. Você pode baixar [Visual Studio Code para Mac, Linux e Windows](https://code.visualstudio.com/download).
* .NET Core. Pode baixar [.NET Core para Mac, Linux e Windows](https://www.microsoft.com/net/download).
* Carteiro. Você pode baixar [O Carteiro para Mac, janelas ou Linux](https://www.getpostman.com/apps).
* Um [hub IoT implantado na sua subscrição Azure.](../iot-hub/iot-hub-create-through-portal.md) Você precisa da cadeia de ligação do centro IoT para completar os passos neste guia. Pode obter a corda de ligação do portal Azure.
* Uma base de [dados Cosmos DB implantada para a sua subscrição Azure](../cosmos-db/create-sql-api-dotnet.md#create-account) que utiliza a API SQL e que está configurada para [uma forte consistência](../cosmos-db/manage-account.md). Precisa da cadeia de ligação da base de dados Cosmos DB para completar os passos deste guia. Pode obter a corda de ligação do portal Azure.
* Uma conta de [armazenamento Azure implantada para a sua subscrição Azure.](../storage/common/storage-account-create.md) Precisa da cadeia de ligação da conta de armazenamento para completar os passos deste guia. Pode obter a corda de ligação do portal Azure.

## <a name="prepare-your-development-environment"></a>Preparar o ambiente de desenvolvimento

Complete as seguintes tarefas para preparar o seu ambiente de desenvolvimento:

* Descarregue a fonte para o microserviço de simulação do dispositivo.
* Descarregue a fonte para o microserviço do adaptador de armazenamento.
* Executar o microserviço adaptador de armazenamento localmente.

As instruções deste artigo assumem que está a usar o Windows. Se estiver a utilizar outro sistema operativo, poderá ter de ajustar alguns dos caminhos e comandos de ficheiros para se adequar ao seu ambiente.

### <a name="download-the-microservices"></a>Descarregue os microserviços

Descarregue e descarregue os [Microserviços](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) de Monitorização Remota do GitHub para um local adequado na sua máquina local. Este repositório inclui o microserviço adaptador de armazenamento que você precisa para este como.

Descarregue e desaperte o microserviço de [simulação](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) do dispositivo do GitHub para um local adequado na sua máquina local.

### <a name="run-the-storage-adapter-microservice"></a>Executar o microserviço adaptador de armazenamento

No Visual Studio Code, abra a pasta de **monitorização remota-serviços-dotnet-master\storage-adapter.** Clique em botões **restaurar** para corrigir dependências não resolvidas.

Abra o ficheiro **.vscode/launch.json** e atribua a sua cadeia de ligação Cosmos DB à variável ambiente **DOCUMENTDB\_\_CONNSTRING do PCS STORAGEADAPTER.\_**

> [!NOTE]
> Quando executa o microserviço localmente na sua máquina, ainda requer uma instância Cosmos DB em Azure para funcionar corretamente.

Para executar o microserviço do adaptador de armazenamento localmente, clique em **Debug \> Start Debugging**.

A janela **Terminal** em Código de Estúdio Visual mostra saída do microserviço <http://127.0.0.1:9022/v1/status>em execução, incluindo um URL para a verificação de saúde do serviço web: . Quando navegar para este endereço, o estado deve ser "OK: Vivo e bem".

Deixe o microserviço adaptador de armazenamento em funcionamento neste caso de Visual Studio Code enquanto completa os seguintes passos.

## <a name="define-your-device-model"></a>Defina o seu modelo de dispositivo

Abra a pasta de **simulação-dotnet-master** de dispositivo que descarregou do GitHub numa nova instância de Visual Studio Code. Clique em botões **restaurar** para corrigir quaisquer dependências não resolvidas.

Neste how-to-guide, cria-se um novo modelo de dispositivo para um rastreador de ativos:

1. Crie um novo ficheiro de modelo de dispositivo chamado **assettracker-01.json** na pasta **Services\data\devicemodels.**

1. Defina a funcionalidade do dispositivo no ficheiro **assettracker-01.json** do modelo do dispositivo. A secção de telemetria de um modelo de dispositivo Protobuf deve:

   * Inclua o nome da classe Protobuf que gera para o seu dispositivo. A secção seguinte mostra-lhe como gerar esta classe.
   * Especifique protobuf como formato de mensagem.

     ```json
     {
     "SchemaVersion": "1.0.0",
     "Id": "assettracker-01",
     "Version": "0.0.1",
     "Name": "Asset Tracker",
     "Description": "An asset tracker with location, temperature, and humidity",
     "Protocol": "AMQP",
     "Simulation": {
       "InitialState": {
         "online": true,
         "latitude": 47.445301,
         "longitude": -122.296307,
         "temperature": 38.0,
         "humidity": 62.0
       },
       "Interval": "00:01:00",
       "Scripts": [
         {
           "Type": "javascript",
           "Path": "assettracker-01-state.js"
         }
       ]
     },
     "Properties": {
       "Type": "AssetTracker",
       "Location": "Field",
       "Latitude": 47.445301,
       "Longitude": -122.296307
     },
     "Telemetry": [
       {
         "Interval": "00:00:10",
         "MessageTemplate": "{\"latitude\":${latitude},\"longitude\":${longitude},\"temperature\":${temperature},\"humidity\":${humidity}}",
         "MessageSchema": {
           "Name": "assettracker-sensors;v1",
           "ClassName": "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf.AssetTracker",
           "Format": "Protobuf",
           "Fields": {
             "latitude": "double",
             "longitude": "double",
             "temperature": "double",
             "humidity": "double"
           }
         }
       }
     ]
     }
     ```

### <a name="create-device-behaviors-script"></a>Criar o script de comportamentos do dispositivo

Escreva o guião de comportamento que define como o seu dispositivo se comporta. Para mais informações, consulte [Criar um dispositivo simulado avançado](iot-accelerators-device-simulation-advanced-device.md).

## <a name="define-your-protobuf-format"></a>Defina o seu formato Protobuf

Quando tiver um modelo de dispositivo e tiver determinado o formato de mensagem, pode criar um ficheiro **proto.** No ficheiro **proto,** adiciona:

* A `csharp_namespace` que corresponda à propriedade **ClassName** no seu modelo de dispositivo.
* Uma mensagem para cada estrutura de dados para serializar.
* Um nome e um tipo para cada campo na mensagem.

1. Crie um novo ficheiro chamado **assettracker.proto** na pasta **Services\Models\Protobuf\proto.**

1. Defina a sintaxe, o espaço de nome e o esquema de mensagem no ficheiro **proto** da seguinte forma:

    ```proto
    syntax = "proto3";

    option csharp_namespace = "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf";

    message AssetTracker {
      double latitude=1;
      double longitude=2;
      double temperature=5;
      double humidity=6;
    }
    ```

Os `=1` `=2` marcadores de cada elemento especificam uma etiqueta única que o campo utiliza na codificação binária. Os números 1-15 exigem um byte a menos para codificar do que números mais altos.

## <a name="generate-the-protobuf-class"></a>Gerar a classe Protobuf

quando se tem um ficheiro **proto,** o próximo passo é gerar as classes necessárias para ler e escrever mensagens. Para completar este passo, precisa do compilador **Protoc** Protobuf.

1. [Descarregue o compilador Protobuf do GitHub](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. Execute o compilador, especificando o diretório de origem, o diretório de destino e o nome do seu ficheiro **proto.** Por exemplo:

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    Este comando gera um ficheiro **Assettracker.cs** na pasta **Services\Models\Protobuf.**

## <a name="test-protobuf-locally"></a>Teste Protobuf localmente

Nesta secção, testa o dispositivo de rastreador de ativos que criou nas secções anteriores localmente.

### <a name="run-the-device-simulation-microservice"></a>Executar o microserviço de simulação do dispositivo

Abra o ficheiro **.vscode/launch.json** e atribua o seu:

* Cadeia de ligação IoT Hub à variável ambiente **PCS\_\_IOTHUB CONNSTRING.**
* Cadeia de ligação à conta de **armazenamento\_\_do PCS AZURE\_**
* Cadeia de ligação Cosmos DB à variável ambiente **DOCUMENTDB\_\_\_CONNSTRING do PCS STORAGEADAPT.**

Abra o ficheiro **WebService/Properties/launchSettings.json** e atribua o seu:

* Cadeia de ligação IoT Hub à variável ambiente **PCS\_\_IOTHUB CONNSTRING.**
* Cadeia de ligação à conta de **armazenamento\_\_do PCS AZURE\_**
* Cadeia de ligação Cosmos DB à variável ambiente **DOCUMENTDB\_\_\_CONNSTRING do PCS STORAGEADAPT.**

Abra o ficheiro **WebService\appsettings.ini** e modifique as definições da seguinte forma:

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>Configure a solução para incluir os seus novos ficheiros de modelos de dispositivos

Por predefinição, os seus novos ficheiros JSON e JS do modelo de dispositivo não serão copiados para a solução incorporada. Precisa incluí-los explicitamente.

Adicione uma entrada no ficheiro **services\services.csproj** para cada ficheiro que desejar incluído. Por exemplo:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

Para executar o microserviço localmente, clique em **Debug \> Start Debugging**.

A janela **Terminal** em Código de Estúdio Visual mostra saída do microserviço em execução.

Deixe o microserviço de simulação do dispositivo em funcionamento nesta instância de Código de Estúdio Visual enquanto completa os próximos passos.

### <a name="set-up-a-monitor-for-device-events"></a>Configurar um monitor para eventos de dispositivos

Nesta secção, utiliza o Azure CLI para configurar um monitor de eventos para visualizar a telemetria enviada dos dispositivos ligados ao seu hub IoT.

O seguinte script pressupõe que o nome do seu hub IoT é **teste de simulação de dispositivos**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Deixe o monitor de eventos em funcionamento enquanto testa os dispositivos simulados.

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>Criar uma simulação com o tipo de dispositivo de rastreador de ativos

Nesta secção, utiliza a ferramenta Postman para solicitar o microserviço de simulação do dispositivo para executar uma simulação utilizando o tipo de dispositivo de rastreador de ativos. O carteiro é uma ferramenta que permite enviar pedidos REST para um serviço web.

Para criar o Carteiro:

1. Abre o Carteiro na tua máquina local.

1. Clique na **importação de ficheiros \> **. Em seguida, clique em **Escolher Ficheiros**.

1. Selecione **Azure IoT Device Simulation\_Solution accelerator.postman collection** and **Azure\_IoT Device Simulation accelerator.postman environment** and click **Open**.

1. Expanda o acelerador de simulação de **dispositivo Solução Azure IoT** para visualizar os pedidos que pode enviar.

1. Clique **em No Environment** e selecione Acelerador de simulação de dispositivo **Azure IoT**.

Tem agora uma recolha e ambiente carregados no seu espaço de trabalho do Carteiro que pode usar para interagir com o microserviço de simulação do dispositivo.

Para configurar e executar a simulação:

1. Na coleção Postman, selecione **Criar simulação** de rastreador de ativos e clique **em Enviar**. Este pedido cria quatro instâncias do tipo de dispositivo de rastreador de ativos simulado.

1. A saída do monitor de eventos na janela Azure CLI mostra a telemetria dos dispositivos simulados.

Para parar a simulação, selecione o pedido de **simulação Stop** no Carteiro e clique em **Enviar**.

### <a name="clean-up-resources"></a>Limpar recursos

Pode parar os dois microserviços de funcionamento local nas suas instâncias de Código de Estúdio Visual **(Debug \> Stop Debugging).**

Se já não necessitar dos casos IoT Hub e Cosmos DB, elimine-os da subscrição do Azure para evitar quaisquer encargos desnecessários.

## <a name="iot-hub-support"></a>Suporte do Hub IoT

Muitas funcionalidades do IoT Hub não suportam nativamente protobuf ou outros formatos binários. Por exemplo, não é possível encaminhar com base na carga útil da mensagem porque o IoT Hub não poderá processar a carga útil da mensagem. Pode, no entanto, encaminhar-se com base em cabeçalhos de mensagem.

## <a name="next-steps"></a>Passos seguintes

Agora aprendeu a personalizar a Simulação do Dispositivo para usar protobuf para enviar telemetria, o próximo passo é aprender agora a [implementar uma imagem personalizada para a nuvem](iot-accelerators-device-simulation-deploy-image.md).
