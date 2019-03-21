---
title: Utilizar Protocol Buffers com simulação do dispositivo - Azure | Documentos da Microsoft
description: Este guia de procedimentos, irá aprender a usar Protocol Buffers para serializar a telemetria enviada a partir do solution accelerator de simulação do dispositivo.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: 74bb2d181533f802e1428eaa8a855f60fb855193
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258837"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>Serializar telemetria com Buffers de protocolo

Buffers de protocolo (Protobuf) é um formato de serialização binária para dados estruturados. Protobuf é projetado para enfatizar a simplicidade e o desempenho com o objetivo de ser menor e mais rápido do que XML.

Simulação do dispositivo suporta o **proto3** versão do protocolo coloca na memória intermédia idioma.

Como o Protobuf exige código compilado para serializar os dados, tem de criar uma versão personalizada de simulação do dispositivo.

Os passos neste procedimentos-to-guia mostram como para:

1. Preparar um ambiente de desenvolvimento
1. Especificar usando o formato de Protobuf num modelo de dispositivo
1. Definir o seu formato de Protobuf
1. Gerar classes de Protobuf
1. Testar localmente

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos neste guia de procedimentos, tem de:

* Visual Studio Code. Pode baixar [Visual Studio Code para Mac, Linux e Windows](https://code.visualstudio.com/download).
* .NET Core. Pode baixar [.NET Core para Mac, Linux e Windows](https://www.microsoft.com/net/download).
* Postman. Pode baixar [Postman para Mac, windows ou Linux](https://www.getpostman.com/apps).
* Uma [hub IoT implementado na sua subscrição do Azure](../iot-hub/iot-hub-create-through-portal.md). Terá de cadeia de ligação do hub IoT para concluir os passos neste guia. Pode obter a cadeia de ligação do portal do Azure.
* R [base de dados do Cosmos DB implementado na sua subscrição do Azure](../cosmos-db/create-sql-api-dotnet.md#create-account) que utiliza a API de SQL e que está configurada para [consistência forte](../cosmos-db/manage-account.md). Terá de cadeia de ligação da base de dados do Cosmos DB para concluir os passos neste guia. Pode obter a cadeia de ligação do portal do Azure.
* Uma [conta de armazenamento do Azure implementada na sua subscrição do Azure](../storage/common/storage-quickstart-create-account.md). Terá de cadeia de ligação da conta de armazenamento para concluir os passos neste guia. Pode obter a cadeia de ligação do portal do Azure.

## <a name="prepare-your-development-environment"></a>Preparar o ambiente de desenvolvimento

Conclua as seguintes tarefas para preparar o ambiente de desenvolvimento:

* Transfira a origem para os microsserviços de simulação do dispositivo.
* Transfira a origem para os microsserviços de placa de armazenamento.
* Execute localmente o microsserviços de placa de armazenamento.

As instruções neste artigo partem do princípio de que está a utilizar o Windows. Se estiver a utilizar outro sistema operacional, poderá ter de ajustar alguns dos caminhos de ficheiro e comandos de acordo com seu ambiente.

### <a name="download-the-microservices"></a>Transferir os microsserviços

Transfira e deszipe o [Microsserviços de monitorização remota](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) do GitHub para um local adequado no seu computador local. Este repositório inclui os microsserviços de placa de armazenamento que precisa para este procedimentos.

Transfira e deszipe o [microsserviços de simulação de dispositivo](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) do GitHub para um local adequado no seu computador local.

### <a name="run-the-storage-adapter-microservice"></a>Execute os microsserviços de placa de armazenamento

No Visual Studio Code, abra a **remote-monitoring-services-dotnet-master\storage-adapter** pasta. Clique em qualquer **restaurar** botões para corrigir as dependências não resolvidas.

Abra o **.vscode/launch.json** do ficheiro e atribuir a cadeia de ligação do Cosmos DB para o **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** ambiente variável.

> [!NOTE]
> Quando executa os microsserviços localmente no seu computador, ele ainda exige que uma instância de Cosmos DB no Azure para funcionar corretamente.

Para executar os microsserviços de placa de armazenamento localmente, clique em **depurar \> iniciar depuração**.

O **Terminal** janela no Visual Studio Code mostra a saída dos microsserviços em execução, incluindo um URL para a verificação de estado de funcionamento do serviço web: <http://127.0.0.1:9022/v1/status>. Ao navegar para este endereço, o estado deve ser "OK: Ativo e bem".

Deixe os microsserviços de placa de armazenamento em execução nesta instância do Visual Studio Code enquanto efetua os passos seguintes.

## <a name="define-your-device-model"></a>Definir o seu modelo de dispositivo

Abra o **dispositivo-simulação-dotnet-mestre** pasta transferiu a partir do GitHub numa nova instância do Visual Studio Code. Clique em qualquer **restaurar** botões para corrigir quaisquer não resolvido dependências.

Este procedimentos-to-guia, vai criar um novo modelo de dispositivo para um controlador de recurso:

1. Crie um novo ficheiro de modelo de dispositivo chamado **assettracker 01.json** no **Services\data\devicemodels** pasta.

1. Definir a funcionalidade de dispositivos no modelo do dispositivo **assettracker 01.json** ficheiro. A secção de telemetria de um modelo de dispositivo Protobuf tem:

   * Inclua o nome da classe Protobuf que gerar para o seu dispositivo. A seção a seguir mostra como gerar a classe.
   * Especifica Protobuf como o formato de mensagem.

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

Escreva o script de comportamento que define como o seu dispositivo se comporta. Para obter mais informações, consulte [criar um dispositivo simulado avançado](iot-accelerators-device-simulation-advanced-device.md).

## <a name="define-your-protobuf-format"></a>Definir o seu formato de Protobuf

Quando tem um modelo de dispositivo e determinar seu formato de mensagem, pode criar uma **proto** ficheiro. Na **proto** de ficheiros, adicionar:

* R `csharp_namespace` que corresponde a **ClassName** propriedade no modelo do dispositivo.
* Uma mensagem para cada estrutura de dados para a serialização.
* Um nome e tipo para cada campo na mensagem.

1. Crie um novo ficheiro chamado **assettracker.proto** no **Services\Models\Protobuf\proto** pasta.

1. Definir a sintaxe, o espaço de nomes e o esquema de mensagens no **proto** ficheiros da seguinte forma:

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

O `=1`, `=2` marcadores em cada elemento especificam uma marca exclusiva utiliza o campo na codificação binária. Números de 1 a 15 requerem um menos byte para codificar que números mais elevados.

## <a name="generate-the-protobuf-class"></a>Gerar a classe Protobuf

Quando tem um **proto** ficheiro, a próxima etapa é gerar as classes necessárias para ler e escrever mensagens. Para concluir este passo, terá do **Protoc** Protobuf compilador.

1. [Transferir o compilador Protobuf a partir do GitHub](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. Executar o compilador, especificando o diretório de origem, o diretório de destino e o nome da sua **proto** ficheiro. Por exemplo:

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    Este comando gera um **Assettracker.cs** de ficheiros a **Services\Models\Protobuf** pasta.

## <a name="test-protobuf-locally"></a>Protobuf de teste localmente

Nesta secção, vai testar o dispositivo de controlador de recurso que criou nas secções anteriores localmente.

### <a name="run-the-device-simulation-microservice"></a>Execute os microsserviços de simulação do dispositivo

Abra o **.vscode/launch.json** de ficheiros e atribuir sua:

* Cadeia de ligação do IoT Hub para o **PCS\_IOTHUB\_CONNSTRING** variável de ambiente.
* A cadeia de ligação de conta de armazenamento para o **PCS\_AZURE\_armazenamento\_conta** variável de ambiente.
* Cadeia de ligação do cosmos DB para o **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** variável de ambiente.

Abra o **WebService/Properties/launchSettings.json** de ficheiros e atribuir sua:

* Cadeia de ligação do IoT Hub para o **PCS\_IOTHUB\_CONNSTRING** variável de ambiente.
* A cadeia de ligação de conta de armazenamento para o **PCS\_AZURE\_armazenamento\_conta** variável de ambiente.
* Cadeia de ligação do cosmos DB para o **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** variável de ambiente.

Abra o **WebService\appsettings.ini** de ficheiros e modifique as definições da seguinte forma:

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>Configurar a solução incluir seus novos arquivos de modelo do dispositivo

Por predefinição, o novo modelo de dispositivo JSON e JS não serão copiados para a solução criada. Tem de incluir explicitamente.

Adicionar uma entrada para o **services\services.csproj** ficheiro para cada ficheiro que pretende que o incluído. Por exemplo:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

Para executar os microsserviços localmente, clique em **depurar \> iniciar depuração**.

O **Terminal** janela no Visual Studio Code mostra a saída dos microsserviços em execução.

Deixe os microsserviços de simulação de dispositivo em execução nesta instância do Visual Studio Code enquanto efetua os passos seguintes.

### <a name="set-up-a-monitor-for-device-events"></a>Configurar um monitor de eventos do dispositivo

Nesta secção, vai utilizar a CLI do Azure para configurar um monitor de eventos para ver a telemetria enviada a partir dos dispositivos ligados ao seu hub IoT.

O seguinte script parte do princípio de que é o nome do IoT hub **testes de simulação de dispositivo**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-cli-iot-ext

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Deixe o monitor de eventos em execução enquanto testa os dispositivos simulados.

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>Criar uma simulação com o tipo de dispositivo do controlador ativo

Nesta secção, vai utilizar a ferramenta de Postman para pedir os microsserviços de simulação de dispositivo para executar uma simulação usando o tipo de dispositivo do controlador ativo. Postman é uma ferramenta que permite que envie pedidos REST para um serviço web.

Para configurar o Postman:

1. Abra o Postman no seu computador local.

1. Clique em **arquivo \> importação**. Em seguida, clique em **escolher ficheiros**.

1. Selecione **accelerator.postman de solução de simulação de dispositivo do Azure IoT\_coleção** e **accelerator.postman de solução de simulação de dispositivo do IoT do Azure\_ambiente** e Clique em **aberto**.

1. Expanda a **acelerador de solução de simulação de dispositivo do IoT do Azure** para ver os pedidos que pode enviar.

1. Clique em **ambiente sem** e selecione **acelerador de solução de simulação de dispositivo do IoT do Azure**.

Tem agora uma coleção e carregado na sua área de trabalho do Postman que pode utilizar para interagir com os microsserviços de simulação de dispositivo de ambiente.

Para configurar e executar a simulação:

1. Na coleção do Postman, selecione **criar a simulação de controlador asset** e clique em **enviar**. Este pedido cria quatro instâncias do tipo de dispositivo do controlador ativo simulado.

1. O resultado de monitor de eventos na janela da CLI do Azure mostra a telemetria dos dispositivos simulados.

Para parar a simulação, selecione o **parar simulação** pedido no Postman e clique em **enviar**.

### <a name="clean-up-resources"></a>Limpar recursos

Pode parar os dois microsserviços localmente em execução nas suas instâncias do Visual Studio Code (**depurar \> parar depuração**).

Se já não necessitar de instâncias do IoT Hub e do Cosmos DB, eliminá-los a partir da sua subscrição do Azure para evitar eventuais encargos desnecessários.

## <a name="iot-hub-support"></a>Suporte do IoT Hub

Muitos recursos do IoT Hub não suportam nativamente Protobuf ou outros formatos binários. Por exemplo, não é possível encaminhar com base no payload da mensagem porque o IoT Hub será não é possível processar a carga da mensagem. No entanto, pode, encaminhar com base nos cabeçalhos de mensagens.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como personalizar a simulação do dispositivo utilizar Protobuf para enviar telemetria, a próxima etapa é saber agora para [implantar uma imagem personalizada para a cloud](iot-accelerators-device-simulation-deploy-image.md).
