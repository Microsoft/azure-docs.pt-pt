---
title: Enviar e receber eventos com o node. js - Event Hubs do Azure | Documentos da Microsoft
description: Este artigo fornece um passo a passo para criar uma aplicação node. js para enviar eventos de Hubs de eventos do Azure.
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: spelluru
ms.openlocfilehash: f03bfde8f7ea37989756ad47678369e94b831438
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59677906"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Enviar eventos para ou receber eventos dos Hubs de eventos do Azure com node. js

Os Hubs de eventos do Azure é um grandes volumes de dados de transmissão em fluxo plataforma e o serviço de ingestão de eventos que pode receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos por dispositivos e software distribuído. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análise em tempo real ou adaptadores de armazenamento/criação de batches. Para uma descrição geral detalhada dos Hubs de Eventos, veja [Descrição geral dos Hubs de Eventos](event-hubs-about.md) e [Funcionalidades dos Hubs de Eventos](event-hubs-features.md).

Este tutorial descreve como criar aplicações node. js para enviar eventos para ou receber eventos de um hub de eventos.

> [!NOTE]
> Pode transferir este início rápido como uma amostra a partir do [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), substituir as cadeias de carateres `EventHubConnectionString` e `EventHubName` pelos seus valores de hub de eventos e executá-la. Em alternativa, pode seguir os passos neste tutorial para criar a sua própria.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa dos seguintes pré-requisitos:

- Uma conta ativa do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- Versão node. js 8.x e superiores. Transferir a versão mais recente do LTS partir [ https://nodejs.org ](https://nodejs.org).
- Visual Studio Code (recomendado) ou qualquer outro IDE
- **Criar um espaço de nomes de Hubs de eventos e um hub de eventos**. O primeiro passo consiste em utilizar o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes do tipo Hubs de Eventos e obter as credenciais de gestão de que a sua aplicação precisa para comunicar com o hub de eventos. Para criar um espaço de nomes e um hub de eventos, siga o procedimento [este artigo](event-hubs-create.md), em seguida, prossiga com os seguintes passos neste tutorial. Em seguida, obter a cadeia de ligação para o espaço de nomes do hub de eventos ao seguir as instruções do artigo: [Obter cadeia de ligação](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Irá utilizar a cadeia de ligação mais tarde neste tutorial.
- Clone o [repositório do GitHub de exemplo](https://github.com/Azure/azure-event-hubs-node) no seu computador. 


## <a name="send-events"></a>Enviar eventos
Esta secção mostra-lhe como criar uma aplicação node. js para enviar eventos para um hub de eventos. 

### <a name="install-nodejs-package"></a>Instalar o pacote de node. js
Instale o pacote de node. js para os Hubs de eventos do Azure no seu computador. 

```shell
npm install @azure/event-hubs
```

Se ainda não clonou o repositório de Git, como mencionado no pré-requisito, transfira o [exemplo](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples) do GitHub. 

O SDK tem um clone contém vários exemplos que mostram-lhe como enviar eventos para um hub de eventos com node. js. Neste início rápido, vai utilizar o **simpleSender.js** exemplo. Para observar eventos a ser recebidos, abra outra terminal e receber eventos com o [receber exemplo](event-hubs-node-get-started-receive.md).

1. Abra o projeto no Visual Studio Code. 
2. Crie um ficheiro denominado **. env** sob a **cliente** pasta. Copie e cole as variáveis de ambiente de exemplo do **sample.env** na pasta raiz.
3. Configure a sua cadeia de ligação do hub de eventos, o nome do hub de eventos e o ponto final de armazenamento. Para obter instruções sobre como obter uma cadeia de ligação para um hub de eventos [obter cadeia de ligação](event-hubs-create.md#create-an-event-hubs-namespace).
4. Sobre a CLI do Azure, navegue para o **cliente** caminho da pasta. Instalar pacotes de nó e compile o projeto ao executar os comandos seguintes:

    ```shell
    npm i
    npm run build
    ```
5. Inicie o envio de eventos ao executar o seguinte comando: 

    ```shell
    node dist/examples/simpleSender.js
    ```


### <a name="review-the-sample-code"></a>Reveja o código de exemplo 
Rever o código de exemplo no arquivo simpleSender.js para enviar eventos para um hub de eventos.

```javascript
"use strict";
Object.defineProperty(exports, "__esModule", { value: true });
const lib_1 = require("../lib");
const dotenv = require("dotenv");
dotenv.config();
const connectionString = "EVENTHUB_CONNECTION_STRING";
const entityPath = "EVENTHUB_NAME";
const str = process.env[connectionString] || "";
const path = process.env[entityPath] || "";

async function main() {
    const client = lib_1.EventHubClient.createFromConnectionString(str, path);
    const data = {
        body: "Hello World!!"
    };
    const delivery = await client.send(data);
    console.log(">>> Sent the message successfully: ", delivery.tag.toString());
    console.log(delivery);
    console.log("Calling rhea-promise sender close directly. This should result in sender getting reconnected.");
    await Object.values(client._context.senders)[0]._sender.close();
    // await client.close();
}

main().catch((err) => {
    console.log("error: ", err);
});

```

Não se esqueça de definir as variáveis de ambiente antes de executar o script. Pode configurá-las na linha de comandos, conforme mostrado no exemplo seguinte, ou utilizar o [dotenv pacote](https://www.npmjs.com/package/dotenv#dotenv). 

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

## <a name="receive-events"></a>Receber eventos
Este tutorial mostra como receber eventos de um hub de eventos ao utilizar o Azure [EventProcessorHost](event-hubs-event-processor-host.md) numa aplicação node. js. O EventProcessorHost (EPH) ajuda-o com eficiência receber eventos de um hub de eventos através da criação de recetores todas as partições no grupo de consumidor de um hub de eventos. Ele metadados de pontos de verificação nas mensagens recebidas em intervalos regulares num Blob de armazenamento do Azure. Essa abordagem torna mais fácil continuar a receber mensagens a partir de onde parou num momento posterior.

O código para este início rápido está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/processor).

### <a name="clone-the-git-repository"></a>Clonar o repositório de Git
Transfira ou clone a [exemplo](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/) do GitHub. 

### <a name="install-the-eventprocessorhost"></a>Instalar o EventProcessorHost
Instale o EventProcessorHost para o módulo de Hubs de eventos. 

```shell
npm install @azure/event-processor-host
```

### <a name="receive-events-using-eventprocessorhost"></a>Receber eventos com o EventProcessorHost
O SDK tem um clone contém vários exemplos que lhe mostram como receber eventos de um hub de eventos com node. js. Neste início rápido, vai utilizar o **singleEPH.js** exemplo. Para observar eventos a ser recebidos, abra outra terminal e enviar eventos com o [enviar exemplo](event-hubs-node-get-started-send.md).

1. Abra o projeto no Visual Studio Code. 
2. Crie um ficheiro denominado **. env** sob a **processador** pasta. Copie e cole as variáveis de ambiente de exemplo do **sample.env** na pasta raiz.
3. Configure a sua cadeia de ligação do hub de eventos, o nome do hub de eventos e o ponto final de armazenamento. Pode copiar a cadeia de ligação para o seu hub de eventos de **primário de cadeia de ligação** chave sob **RootManageSharedAccessKey** na página do Hub de eventos no portal do Azure. Para obter passos detalhados, consulte [obter cadeia de ligação](event-hubs-create.md#create-an-event-hubs-namespace).
4. Sobre a CLI do Azure, navegue para o **processador** caminho da pasta. Instalar pacotes de nó e compile o projeto ao executar os comandos seguintes:

    ```shell
    npm i
    npm run build
    ```
5. Receba eventos com o anfitrião do processador de eventos ao executar o seguinte comando:

    ```shell
    node dist/examples/singleEph.js
    ```

### <a name="review-the-sample-code"></a>Reveja o código de exemplo 
Aqui está o código de exemplo para receber eventos de um hub de eventos com o node. js. Manualmente pode criar um ficheiro de sampleEph.js e execute-o para receber eventos para um hub de eventos. 

  ```javascript
  const { EventProcessorHost, delay } = require("@azure/event-processor-host");

  const path = process.env.EVENTHUB_NAME;
  const storageCS = process.env.STORAGE_CONNECTION_STRING;
  const ehCS = process.env.EVENTHUB_CONNECTION_STRING;
  const storageContainerName = "test-container";
  
  async function main() {
    // Create the Event Processor Host
    const eph = EventProcessorHost.createFromConnectionString(
      EventProcessorHost.createHostName("my-host"),
      storageCS,
      storageContainerName,
      ehCS,
      {
        eventHubPath: path,
        onEphError: (error) => {
          console.log("This handler will notify you of any internal errors that happen " +
          "during partition and lease management: %O", error);
        }
      }
    );
    let count = 0;
    // Message event handler
    const onMessage = async (context/*PartitionContext*/, data /*EventData*/) => {
      console.log(">>>>> Rx message from '%s': '%s'", context.partitionId, data.body);
      count++;
      // let us checkpoint every 100th message that is received across all the partitions.
      if (count % 100 === 0) {
        return await context.checkpoint();
      }
    };
    // Error event handler
    const onError = (error) => {
      console.log(">>>>> Received Error: %O", error);
    };
    // start the EPH
    await eph.start(onMessage, onError);
    // After some time let' say 2 minutes
    await delay(120000);
    // This will stop the EPH.
    await eph.stop();
  }
  
  main().catch((err) => {
    console.log(err);
  });
      
  ```

Não se esqueça de definir as variáveis de ambiente antes de executar o script. Pode configurar esta opção na linha de comandos, conforme mostrado no exemplo seguinte, ou utilizar o [dotenv pacote](https://www.npmjs.com/package/dotenv#dotenv). 

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

Pode encontrar mais exemplos [aqui](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples).


## <a name="next-steps"></a>Passos Seguintes
Leia os artigos seguintes:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funcionalidades e a terminologia nos Hubs de eventos do Azure](event-hubs-features.md)
- [FAQ dos Hubs de Eventos](event-hubs-faq.md)
- Verifique outras amostras de node. js para os Hubs de eventos no [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/).
