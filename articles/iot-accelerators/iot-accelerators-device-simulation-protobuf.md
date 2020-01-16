---
title: Usar buffers de protocolo com simulação de dispositivo – Azure | Microsoft Docs
description: Neste guia de instruções, você aprende a usar buffers de protocolo para serializar a telemetria enviada do acelerador de solução de simulação de dispositivo.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: 79517ffd68c501203ea9c02f3a3276973d4a8a56
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982152"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>Serializar telemetria usando buffers de protocolo

Buffers de protocolo (Protobuf) é um formato de serialização binária para dados estruturados. O Protobuf foi projetado para enfatizar a simplicidade e o desempenho com o objetivo de ser menor e mais rápido que o XML.

A simulação de dispositivo dá suporte à versão **Proto3** do idioma dos buffers de protocolo.

Como o Protobuf requer código compilado para serializar os dados, você deve criar uma versão personalizada da simulação de dispositivo.

As etapas neste guia de instruções mostram como:

1. Preparar um ambiente de desenvolvimento
1. Especificar usando o formato Protobuf em um modelo de dispositivo
1. Definir o formato Protobuf
1. Gerar classes Protobuf
1. Testar localmente

## <a name="prerequisites"></a>Pré-requisitos

Para seguir as etapas neste guia de instruções, você precisará de:

* Visual Studio Code. Você pode baixar [Visual Studio Code para Mac, Linux e Windows](https://code.visualstudio.com/download).
* .NET Core. Você pode baixar o [.NET Core para Mac, Linux e Windows](https://www.microsoft.com/net/download).
* Postman. Você pode baixar o [postmaster para Mac, Windows ou Linux](https://www.getpostman.com/apps).
* Um [Hub IOT implantado em sua assinatura do Azure](../iot-hub/iot-hub-create-through-portal.md). Você precisa da cadeia de conexão do Hub IoT para concluir as etapas neste guia. Você pode obter a cadeia de conexão do portal do Azure.
* Um [banco de dados Cosmos DB implantado em sua assinatura do Azure](../cosmos-db/create-sql-api-dotnet.md#create-account) que usa a API do SQL e que está configurado para [consistência forte](../cosmos-db/manage-account.md). Você precisa da cadeia de conexão do banco de dados Cosmos DB para concluir as etapas neste guia. Você pode obter a cadeia de conexão do portal do Azure.
* Uma [conta de armazenamento do Azure implantada em sua assinatura do Azure](../storage/common/storage-account-create.md). Você precisa da cadeia de conexão da conta de armazenamento para concluir as etapas neste guia. Você pode obter a cadeia de conexão do portal do Azure.

## <a name="prepare-your-development-environment"></a>Preparar o ambiente de desenvolvimento

Conclua as seguintes tarefas para preparar seu ambiente de desenvolvimento:

* Baixe a origem do microserviço de simulação de dispositivo.
* Baixe a origem do microserviço do adaptador de armazenamento.
* Execute o microserviço do adaptador de armazenamento localmente.

As instruções neste artigo pressupõem que você esteja usando o Windows. Se você estiver usando outro sistema operacional, talvez seja necessário ajustar alguns dos caminhos de arquivo e comandos para adequá-los ao seu ambiente.

### <a name="download-the-microservices"></a>Baixar os microserviços

Baixe e descompacte os [microserviços de monitoramento remoto](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) do GitHub para um local adequado no computador local. Esse repositório inclui o microserviço de adaptador de armazenamento que você precisa para este "como".

Baixe e descompacte o [microserviço de simulação de dispositivo](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) do GitHub para um local adequado no computador local.

### <a name="run-the-storage-adapter-microservice"></a>Executar o microserviço do adaptador de armazenamento

No Visual Studio Code, abra a pasta **Remote-Monitoring-Services-dotnet-master\storage-Adapter** . Clique em qualquer botão de **restauração** para corrigir dependências não resolvidas.

Abra o arquivo **. vscode/Launch. JSON** e atribua sua cadeia de conexão Cosmos DB à variável de ambiente **PCS\_STORAGEADAPTER\_DOCUMENTDB\_connString** .

> [!NOTE]
> Quando você executa o microserviço localmente em seu computador, ele ainda requer uma instância de Cosmos DB no Azure para funcionar corretamente.

Para executar o microserviço do adaptador de armazenamento localmente, clique em **depurar \> iniciar a depuração**.

A janela do **terminal** no Visual Studio Code mostra a saída do microserviço em execução, incluindo uma URL para a verificação de integridade do serviço web: <http://127.0.0.1:9022/v1/status>. Quando você navega para esse endereço, o status deve ser "OK: vivo e bem".

Deixe o microserviço do adaptador de armazenamento em execução nesta instância do Visual Studio Code enquanto você conclui as etapas a seguir.

## <a name="define-your-device-model"></a>Definir o modelo do dispositivo

Abra a pasta **Device-Simulation-dotnet-Master** que você baixou do GitHub em uma nova instância do Visual Studio Code. Clique em qualquer botão de **restauração** para corrigir as dependências não resolvidas.

Neste guia de instruções, você cria um novo modelo de dispositivo para um rastreador de ativos:

1. Crie um novo arquivo de modelo de dispositivo chamado **assettracker-01. JSON** na pasta **Services\data\devicemodels**

1. Defina a funcionalidade do dispositivo no arquivo **assettracker-01. JSON** do modelo do dispositivo. A seção telemetria de um modelo de dispositivo Protobuf deve:

   * Inclua o nome da classe Protobuf que você gera para seu dispositivo. A seção a seguir mostra como gerar essa classe.
   * Especifique Protobuf como o formato de mensagem.

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

## <a name="define-your-protobuf-format"></a>Definir o formato Protobuf

Quando você tiver um modelo de dispositivo e tiver determinado seu formato de mensagem, poderá criar um arquivo **proto** . No arquivo **proto** , você adiciona:

* Um `csharp_namespace` que corresponde à propriedade **ClassName** em seu modelo de dispositivo.
* Uma mensagem para cada estrutura de dados a ser serializada.
* Um nome e um tipo para cada campo na mensagem.

1. Crie um novo arquivo chamado **assettracker. proto** na pasta **Services\Models\Protobuf\proto** .

1. Defina a sintaxe, o namespace e o esquema de mensagem no arquivo **proto** da seguinte maneira:

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

A `=1`, `=2` marcadores em cada elemento especificam uma marca exclusiva que o campo usa na codificação binária. Os números 1-15 exigem um menos byte para codificar do que números mais altos.

## <a name="generate-the-protobuf-class"></a>Gerar a classe Protobuf

Quando você tem um arquivo **proto** , a próxima etapa é gerar as classes necessárias para ler e gravar mensagens. Para concluir esta etapa, você precisa do compilador **protoc** Protobuf.

1. [Baixe o compilador Protobuf do GitHub](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. Execute o compilador, especificando o diretório de origem, o diretório de destino e o nome do seu arquivo **proto** . Por exemplo:

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    Esse comando gera um arquivo **Assettracker.cs** na pasta **Services\Models\Protobuf** .

## <a name="test-protobuf-locally"></a>Testar Protobuf localmente

Nesta seção, você testará o dispositivo do rastreador de ativos que você criou nas seções anteriores localmente.

### <a name="run-the-device-simulation-microservice"></a>Executar o microserviço de simulação de dispositivo

Abra o arquivo **. vscode/Launch. JSON** e atribua:

* Cadeia de conexão do Hub IoT para os PCs\_a variável de ambiente **IOTHUB\_connString** .
* Cadeia de conexão da conta de armazenamento para os **PCS\_o AZURE\_armazenamento\_** variável de ambiente de conta.
* Cosmos DB cadeia de conexão para os **PCS\_STORAGEADAPTER\_DOCUMENTDB\_** a variável de ambiente connString.

Abra o arquivo **WebService/Properties/launchSettings. JSON** e atribua:

* Cadeia de conexão do Hub IoT para os PCs\_a variável de ambiente **IOTHUB\_connString** .
* Cadeia de conexão da conta de armazenamento para os **PCS\_o AZURE\_armazenamento\_** variável de ambiente de conta.
* Cosmos DB cadeia de conexão para os **PCS\_STORAGEADAPTER\_DOCUMENTDB\_** a variável de ambiente connString.

Abra o arquivo **WebService\appsettings.ini** e modifique as configurações da seguinte maneira:

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>Configurar a solução para incluir os novos arquivos de modelo de dispositivo

Por padrão, seus novos arquivos JSON e JS de modelo de dispositivo não serão copiados para a solução interna. Você precisa incluí-las explicitamente.

Adicione uma entrada ao arquivo **Services\Services.csproj** para cada arquivo que você deseja incluir. Por exemplo:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

Para executar o Microservice localmente, clique em **depurar \> iniciar a depuração**.

A janela do **terminal** no Visual Studio Code mostra a saída do Microservice em execução.

Deixe o microserviço de simulação de dispositivo em execução nesta instância do Visual Studio Code enquanto você conclui as próximas etapas.

### <a name="set-up-a-monitor-for-device-events"></a>Configurar um monitor para eventos de dispositivo

Nesta seção, você usa o CLI do Azure para configurar um monitor de eventos para exibir a telemetria enviada dos dispositivos conectados ao Hub IoT.

O script a seguir pressupõe que o nome do Hub IoT é o **teste de simulação de dispositivo**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-cli-iot-ext

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Deixe o monitor de eventos em execução enquanto você testa os dispositivos simulados.

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>Criar uma simulação com o tipo de dispositivo do rastreador de ativos

Nesta seção, você usará a ferramenta de postmaster para solicitar que o microserviço de simulação de dispositivo execute uma simulação usando o tipo de dispositivo do rastreador de ativos. O postmaster é uma ferramenta que permite enviar solicitações REST a um serviço Web.

Para configurar o postmaster:

1. Abra o postmaster no computador local.

1. Clique em **arquivo \> importar**. Em seguida, clique em **escolher arquivos**.

1. Selecione **acelerador de solução de simulação de dispositivo IOT do Azure. postmaster\_coleção** e **acelerador de solução de simulação de dispositivo IOT do azure. postmaster\_ambiente** e clique em **abrir**.

1. Expanda o **acelerador de solução de simulação de dispositivo IOT do Azure** para exibir as solicitações que você pode enviar.

1. Clique em **nenhum ambiente** e selecione **acelerador de solução de simulação de dispositivo IOT do Azure**.

Agora você tem uma coleção e um ambiente carregados no seu espaço de trabalho do postmaster que você pode usar para interagir com o microserviço de simulação de dispositivo.

Para configurar e executar a simulação:

1. Na coleção do postmaster, selecione **criar simulação do rastreador de ativos** e clique em **Enviar**. Essa solicitação cria quatro instâncias do tipo de dispositivo do rastreador de ativos simulado.

1. A saída do monitor de eventos na janela CLI do Azure mostra a telemetria dos dispositivos simulados.

Para interromper a simulação, selecione a solicitação **parar simulação** no postmaster e clique em **Enviar**.

### <a name="clean-up-resources"></a>Limpar recursos

Você pode parar os dois microserviços em execução localmente em suas instâncias de Visual Studio Code (**depurar \> interromper a depuração**).

Se você não precisar mais do Hub IoT e Cosmos DB instâncias, exclua-os da sua assinatura do Azure para evitar quaisquer encargos desnecessários.

## <a name="iot-hub-support"></a>Suporte ao Hub IoT

Muitos recursos do Hub IoT não dão suporte nativo a Protobuf ou a outros formatos binários. Por exemplo, você não pode rotear com base na carga da mensagem porque o Hub IoT não poderá processar a carga da mensagem. No entanto, você pode rotear com base em cabeçalhos de mensagens.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu como personalizar a simulação de dispositivo para usar o Protobuf para enviar telemetria, a próxima etapa é aprender agora a [implantar uma imagem personalizada na nuvem](iot-accelerators-device-simulation-deploy-image.md).
