---
title: Utilize tampão de protocolo com simulação de dispositivo - Azure| Microsoft Docs
description: Neste guia de como fazer, aprende a utilizar tampões de protocolo para serializar a telemetria enviada a partir do acelerador de solução de simulação do dispositivo.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc, amqp, devx-track-csharp
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: 77a795b8f704084b612a2dcbd364b8d5be0a3333
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96852155"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>Serialize telemetria usando tampão de protocolo

Protocol Buffers (Protobuf) é um formato binário de serialização para dados estruturados. O Protobuf é projetado para enfatizar a simplicidade e desempenho com o objetivo de ser menor e mais rápido que O XML.

A Simulação do Dispositivo suporta a versão **proto3** da linguagem de tampão de protocolo.

Como o Protobuf requer código compilado para serializar os dados, tens de construir uma versão personalizada da Simulação do Dispositivo.

Os passos deste guia mostram-lhe como:

1. Preparar um ambiente de desenvolvimento
1. Especificar usando o formato Protobuf num modelo de dispositivo
1. Defina o seu formato Protobuf
1. Gerar aulas de Protobuf
1. Teste local

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos neste guia de como guiar, você precisa:

* Visual Studio Code. Você pode baixar [Visual Studio Code para Mac, Linux e Windows](https://code.visualstudio.com/download).
* .NET Core. Pode baixar [.NET Core para Mac, Linux e Windows.](https://www.microsoft.com/net/download)
* Carteiro. Você pode baixar [Carteiro para Mac, janelas ou Linux.](https://www.getpostman.com/apps)
* Um [hub IoT implantado na sua subscrição Azure](../iot-hub/iot-hub-create-through-portal.md). Você precisa da cadeia de ligação do hub IoT para completar os passos neste guia. Pode obter a cadeia de ligação do portal Azure.
* Uma [base de dados de Coss DB implementada na sua subscrição Azure](../cosmos-db/create-sql-api-dotnet.md#create-account) que utiliza a API SQL e que está configurada para [uma forte consistência.](../cosmos-db/how-to-manage-database-account.md) Precisa da cadeia de ligação da base de dados Cosmos DB para completar os passos deste guia. Pode obter a cadeia de ligação do portal Azure.
* Uma [conta de armazenamento Azure implantada na sua subscrição Azure](../storage/common/storage-account-create.md). Precisa do fio de ligação da conta de armazenamento para completar os passos neste guia. Pode obter a cadeia de ligação do portal Azure.

## <a name="prepare-your-development-environment"></a>Preparar o ambiente de desenvolvimento

Complete as seguintes tarefas para preparar o seu ambiente de desenvolvimento:

* Descarregue a fonte para o microserviço de simulação do dispositivo.
* Descarregue a fonte para o microserviço do adaptador de armazenamento.
* Executar o microserviço do adaptador de armazenamento localmente.

As instruções deste artigo pressupõem que está a usar o Windows. Se estiver a utilizar outro sistema operativo, poderá ter de ajustar alguns dos caminhos e comandos de ficheiros para se adequar ao seu ambiente.

### <a name="download-the-microservices"></a>Descarregue os microserviços

Faça o download e desaperte os [Microserviços](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) de Monitorização Remota do GitHub para uma localização adequada na sua máquina local. Este repositório inclui o microserviço do adaptador de armazenamento que necessita para este como fazê-lo.

Faça o download e desaperte o [microserviço](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) de simulação do dispositivo do GitHub para uma localização adequada na sua máquina local.

### <a name="run-the-storage-adapter-microservice"></a>Executar o microserviço do adaptador de armazenamento

No Código do Estúdio Visual, abra a pasta **adaptador de dispositivos de monitorização remota-dotnet-master\storage-adapter.** Clique em quaisquer botões **De restauro** para corrigir dependências não resolvidas.

Abra a variável **ambiente de .vscode/launch.jsno** ficheiro e atribua a sua cadeia de ligação Cosmos DB à variável ambiente de **\_ \_ connstring DOCUMENTDB DO PCS STORAGEADAPTER. \_**

> [!NOTE]
> Quando você executar o micro-serviço localmente na sua máquina, ele ainda requer uma instância Cosmos DB em Azure para funcionar corretamente.

Para executar o microserviço do adaptador de armazenamento localmente, clique em **Debug \> Start Debugging**.

A janela **terminal** em Visual Studio Code mostra a saída do microserviço em execução, incluindo um URL para a verificação de saúde do serviço web: <http://127.0.0.1:9022/v1/status> . Quando navegar para este endereço, o estado deve ser "OK: Vivo e bem".

Deixe o microserviço do adaptador de armazenamento em funcionamento neste caso de Visual Studio Code enquanto completa os seguintes passos.

## <a name="define-your-device-model"></a>Defina o seu modelo de dispositivo

Abra a pasta **de simulação-dotnet-master** que descarregou do GitHub num novo exemplo do Visual Studio Code. Clique em quaisquer botões **De restauro** para corrigir quaisquer dependências não resolvidas.

Neste como guiar, cria-se um novo modelo de dispositivo para um rastreador de ativos:

1. Crie um novo ficheiro modelo de dispositivo chamado **assettracker-01.jsna** pasta **Serviço\data\devicemodels.**

1. Defina a funcionalidade do dispositivo no modelo do dispositivo **assettracker-01.jsem** ficheiro. A secção de telemetria de um modelo de dispositivo Protobuf deve:

   * Inclua o nome da classe Protobuf que gera para o seu dispositivo. A seguinte secção mostra-lhe como gerar esta classe.
   * Especifique o Protobuf como o formato de mensagem.

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

### <a name="create-device-behaviors-script"></a>Criar script de comportamentos de dispositivo

Escreva o script de comportamento que define como o seu dispositivo se comporta. Para obter mais informações, consulte [Criar um dispositivo simulado avançado.](iot-accelerators-device-simulation-advanced-device.md)

## <a name="define-your-protobuf-format"></a>Defina o seu formato Protobuf

Quando tiver um modelo de dispositivo e tiver determinado o seu formato de mensagem, pode criar um **ficheiro proto.** No ficheiro **proto,** acrescenta::

* A `csharp_namespace` que corresponde à propriedade **ClassName** no seu modelo de dispositivo.
* Uma mensagem para cada estrutura de dados para serializar.
* Um nome e um tipo para cada campo na mensagem.

1. Crie um novo ficheiro chamado **assettracker.proto** na pasta **Serviços\Modelos\Protobuf\proto.**

1. Defina a sintaxe, o espaço de nome e o esquema de mensagens no ficheiro **proto** da seguinte forma:

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

Os `=1` `=2` marcadores , em cada elemento, especificam uma etiqueta única que o campo utiliza na codificação binária. Os números 1-15 requerem menos um byte para codificar do que os números mais altos.

## <a name="generate-the-protobuf-class"></a>Gerar a classe Protobuf

quando se tem um ficheiro **proto,** o próximo passo é gerar as classes necessárias para ler e escrever mensagens. Para completar este passo, precisa do **compilador Protoc** Protobuf.

1. [Descarregue o compilador Protobuf do GitHub](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. Execute o compilador, especificando o diretório de origem, o diretório de destino e o nome do seu ficheiro **proto.** Por exemplo:

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    Este comando gera um ficheiro **.cs Ativo** na pasta **Serviços\Modelos\Protobuf.**

## <a name="test-protobuf-locally"></a>Teste Protobuf localmente

Nesta secção, testa o dispositivo de rastreador de ativos que criou nas secções anteriores localmente.

### <a name="run-the-device-simulation-microservice"></a>Executar o microserviço de simulação do dispositivo

Abra o **ficheiro .vscode/launch.jse** atribua o seu:

* Cadeia de ligação IoT Hub à variável ambiente **\_ de \_ connstring PCS IOTHUB.**
* Cadeia de ligação da conta de armazenamento para a variável ambiente **de conta de armazenamento PCS \_ \_ \_ AZURE.**
* Cadeia de ligação Cosmos DB à variável ambiente ambiente **\_ DE \_ CONNSTRING DOCUMENTDB \_ DOCUMENTDB.**

Abra o **WebService/Properties/launchSettings.jsno** ficheiro e atribua o seu:

* Cadeia de ligação IoT Hub à variável ambiente **\_ de \_ connstring PCS IOTHUB.**
* Cadeia de ligação da conta de armazenamento para a variável ambiente **de conta de armazenamento PCS \_ \_ \_ AZURE.**
* Cadeia de ligação Cosmos DB à variável ambiente ambiente **\_ DE \_ CONNSTRING DOCUMENTDB \_ DOCUMENTDB.**

Abra o ficheiro **WebService\appsettings.ini** e modifique as definições da seguinte forma:

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>Configure a solução para incluir os seus novos ficheiros de modelos de dispositivos

Por predefinição, os ficheiros JSON e JS do seu novo dispositivo não serão copiados para a solução incorporada. Precisa incluí-los explicitamente.

Adicione uma entrada no ficheiro **services\services\services.csproj** para cada ficheiro que pretenda incluído. Por exemplo:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

Para executar o microserviço localmente, clique em **Debug \> Start Debugging**.

A janela **Terminal** em Visual Studio Code mostra a saída do microserviço em execução.

Deixe o microserviço de simulação do dispositivo em funcionamento neste caso de Visual Studio Code enquanto completa os próximos passos.

### <a name="set-up-a-monitor-for-device-events"></a>Criar um monitor para eventos de dispositivos

Nesta secção, utiliza o CLI Azure para configurar um monitor de eventos para visualizar a telemetria enviada dos dispositivos ligados ao seu hub IoT.

O seguinte script pressupõe que o nome do seu hub IoT é **teste de simulação de dispositivo**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Deixe o monitor de eventos em funcionamento enquanto testa os dispositivos simulados.

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>Criar uma simulação com o tipo de dispositivo de rastreador de ativos

Nesta secção, utiliza-se a ferramenta Postman para solicitar o microserviço de simulação do dispositivo para executar uma simulação utilizando o tipo de dispositivo de rastreador de ativos. O Carteiro é uma ferramenta que permite enviar pedidos DE REST para um serviço web.

Para configurar o Carteiro:

1. Abra o Carteiro na sua máquina local.

1. Clique **\> em Importar Ficheiros.** Em seguida, clique em **Escolher Ficheiros**.

1. Selecione **Azure IoT Device Simulation accelerator.postman \_ collection** and **Azure IoT Device Simulation accelerator.postman \_ environment** and click **Open**.

1. Expanda o acelerador de **simulação de dispositivoS Azure IoT** para visualizar os pedidos que pode enviar.

1. Clique **em No Environment** e selecione o acelerador de **simulação de dispositivoS Azure IoT**.

Tem agora uma coleção e ambiente carregados no seu espaço de trabalho do Carteiro que pode usar para interagir com o microserviço de simulação do dispositivo.

Para configurar e executar a simulação:

1. Na coleção Postman, selecione **Criar simulação de rastreador de ativos** e clique em **Enviar**. Este pedido cria quatro instâncias do tipo de dispositivo de rastreador de ativos simulado.

1. A saída do monitor do evento na janela Azure CLI mostra a telemetria dos dispositivos simulados.

Para interromper a simulação, selecione o pedido **de simulação Stop** no Carteiro e clique em **Enviar**.

### <a name="clean-up-resources"></a>Limpar os recursos

Pode parar os dois microserviços de funcionamento local nas suas instâncias do Código do Estúdio Visual **(Debug \> Stop Debugging).**

Se já não necessitar das instâncias IoT Hub e Cosmos DB, elimine-as da sua assinatura Azure para evitar eventuais encargos desnecessários.

## <a name="iot-hub-support"></a>Suporte ioT hub

Muitas funcionalidades do IoT Hub não suportam de forma nativa o Protobuf ou outros formatos binários. Por exemplo, não é possível fazer uma rota com base na carga útil da mensagem porque o IoT Hub não poderá processar a carga útil da mensagem. Pode, no entanto, fazer percurso com base em cabeçalhos de mensagens.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a personalizar a Simulação do Dispositivo para usar o Protobuf para enviar telemetria, o próximo passo é visitar o repositório do GitHub para aprender mais [simulação de dispositivo.](https://github.com/Azure/device-simulation-dotnet)
