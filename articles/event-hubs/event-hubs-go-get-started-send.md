---
title: 'Início rápido: enviar e receber eventos usando o go-hubs de eventos do Azure'
description: 'Início rápido: Este artigo fornece uma explicação para a criação de um aplicativo go que envia eventos dos hubs de eventos do Azure.'
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: e5f52d0ddbf9a66d974732d6d98ca8a5b09cc2d0
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720584"
---
# <a name="quickstart-send-events-to-or-receive-events-from-event-hubs-using-go"></a>Início rápido: enviar eventos para ou receber eventos de hubs de eventos usando go
Os Hubs de Eventos do Azure são uma plataforma de fluxo de Macrodados e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos por dispositivos e software distribuído. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análise em tempo real ou adaptadores de armazenamento/criação de batches. Para uma descrição geral detalhada dos Hubs de Eventos, veja [Descrição geral dos Hubs de Eventos](event-hubs-about.md) e [Funcionalidades dos Hubs de Eventos](event-hubs-features.md).

Este tutorial descreve como escrever aplicativos Go para enviar eventos ou receber eventos de um hub de eventos. 

> [!NOTE]
> Pode transferir este início rápido como uma amostra a partir do [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), substituir as cadeias de carateres `EventHubConnectionString` e `EventHubName` pelos seus valores de hub de eventos e executá-la. Em alternativa, pode seguir os passos neste tutorial para criar a sua própria.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa dos seguintes pré-requisitos:

- Vá instalado localmente. Siga [estas instruções](https://golang.org/doc/install) se necessário.
- Uma conta ativa do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita][] antes de começar.
- **Crie um namespace de hubs de eventos e um hub de eventos**. Use o [portal do Azure](https://portal.azure.com) para criar um namespace do tipo hubs de eventos e obter as credenciais de gerenciamento que seu aplicativo precisa para se comunicar com o Hub de eventos. Para criar um espaço de nomes e um hub de eventos, siga o procedimento [este artigo](event-hubs-create.md).

## <a name="send-events"></a>Enviar eventos
Esta seção mostra como criar um aplicativo Go para enviar eventos para um hub de eventos. 

### <a name="install-go-package"></a>Instalar o pacote do Go

Obter o pacote do Go para os Hubs de eventos com `go get` ou `dep`. Por exemplo:

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

### <a name="import-packages-in-your-code-file"></a>Importar pacotes em seu arquivo de código

Para importar os pacotes de Go, utilize o seguinte exemplo de código:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

### <a name="create-service-principal"></a>Criar um principal de serviço

Crie um novo principal de serviço ao seguir as instruções em [criar um Azure principal de serviço com a CLI 2.0 do Azure](/cli/azure/create-an-azure-service-principal-azure-cli). Guarde as credenciais fornecidas no seu ambiente com os seguintes nomes. O Azure SDK para Go e os pacotes de Hubs de eventos estão pré-configuradas para procurar por estes nomes de variáveis:

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Agora, crie um fornecedor de autorização para o seu cliente de Hubs de eventos que utiliza estas credenciais:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="create-event-hubs-client"></a>Crie os Hubs de eventos do cliente

O código a seguir cria um cliente dos Hubs de eventos:

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

### <a name="write-code-to-send-messages"></a>Escrever código para enviar mensagens

No seguinte fragmento, utilize (1) para enviar mensagens interativamente a partir de um terminal ou (2) para enviar mensagens dentro de seu programa:

```go
// 1. send messages at the terminal
ctx = context.Background()
reader := bufio.NewReader(os.Stdin)
for {
    fmt.Printf("Input a message to send: ")
    text, _ := reader.ReadString('\n')
    hub.Send(ctx, eventhubs.NewEventFromString(text))
}

// 2. send messages within program
ctx = context.Background()
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!"))
```

### <a name="extras"></a>Extras

Obter os IDs das partições no seu hub de eventos:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n", info.PartitionIDs)
```

Execute a aplicação para enviar eventos para o hub de eventos. 

Parabéns! Enviou agora mensagens para um hub de eventos.

## <a name="receive-events"></a>Receber eventos

### <a name="create-a-storage-account-and-container"></a>Criar uma conta de armazenamento e um contentor

Estado, como de concessões em partições e os pontos de verificação de evento stream são partilhados entre recetores com um contentor de armazenamento do Azure. Pode criar uma conta de armazenamento e um contentor com o SDK Go, mas também pode criar uma ao seguir as instruções em [sobre as contas de armazenamento](../storage/common/storage-create-storage-account.md).

Exemplos para criar artefactos de armazenamento com o SDK Go estão disponíveis no [repositório de amostras do Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) e no exemplo correspondente a este tutorial.

### <a name="go-packages"></a>Pacotes go

Para receber as mensagens, obter os pacotes de ir para os Hubs de eventos com `go get` ou `dep`:

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

### <a name="import-packages-in-your-code-file"></a>Importar pacotes em seu arquivo de código

Para importar os pacotes de Go, utilize o seguinte exemplo de código:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
    eph "github.com/Azure/azure-event-hubs-go/eph"
    storageLeaser "github.com/Azure/azure-event-hubs-go/storage"
    azure "github.com/Azure/go-autorest/autorest/azure"
)
```

### <a name="create-service-principal"></a>Criar um principal de serviço

Crie um novo principal de serviço ao seguir as instruções em [criar um Azure principal de serviço com a CLI 2.0 do Azure](/cli/azure/create-an-azure-service-principal-azure-cli). Guardar as credenciais fornecidas no seu ambiente com os seguintes nomes: O Azure SDK para Go e os Hubs de eventos de pacote estão pré-configuradas para procurar por estes nomes de variáveis.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Em seguida, crie um fornecedor de autorização para o seu cliente de Hubs de eventos que utiliza estas credenciais:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="get-metadata-struct"></a>Obter a estrutura de metadados

Obtenha um struct com metadados sobre o seu ambiente do Azure com o SDK do Go do Azure. Operações posteriores usam este struct para localizar pontos de extremidade corretos.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

### <a name="create-credential-helper"></a>Criar programa auxiliar de credencial 

Crie um credencial do programa auxiliar que utiliza as credenciais do Azure Active Directory (AAD) anterior para criar uma credencial de assinatura de acesso partilhado (SAS) para armazenamento. O último parâmetro indica este construtor para utilizar as variáveis de ambiente mesmo como utilizado anteriormente:

```go
cred, err := storageLeaser.NewAADSASCredential(
    subscriptionID,
    resourceGroupName,
    storageAccountName,
    storageContainerName,
    storageLeaser.AADSASCredentialWithEnvironmentVars())
if err != nil {
    log.Fatalf("could not prepare a storage credential: %s\n", err)
}
```

### <a name="create-a-check-pointer-and-a-leaser"></a>Criar um ponteiro de verificação e um leasing 

Crie um **leasing**, responsável por conceder uma partição a um receptor específico e um **ponteiro de verificação**, responsável por escrever pontos de verificação para o fluxo de mensagens para que outros receptores possam começar a ler a partir do deslocamento correto.

Atualmente, uma única **StorageLeaserCheckpointer** está disponível que utiliza o mesmo contentor de armazenamento para gerir as concessões e pontos de verificação. Além dos nomes de conta e contentor de armazenamento, o **StorageLeaserCheckpointer** precisa a credencial que criou no passo anterior e a estrutura de ambiente do Azure para aceder corretamente ao contentor.

```go
leaserCheckpointer, err := storageLeaser.NewStorageLeaserCheckpointer(
    cred,
    storageAccountName,
    storageContainerName,
    azureEnv)
if err != nil {
    log.Fatalf("could not prepare a storage leaserCheckpointer: %s\n", err)
}
```

### <a name="construct-event-processor-host"></a>Construir o anfitrião do processador de eventos

Tem agora as partes necessárias para construir um EventProcessorHost, da seguinte forma. O mesmo **StorageLeaserCheckpointer** é usado como um arrendador e um ponteiro de verificação, conforme descrito anteriormente:

```go
ctx := context.Background()
p, err := eph.New(
    ctx,
    nsName,
    hubName,
    tokenProvider,
    leaserCheckpointer,
    leaserCheckpointer)
if err != nil {
    log.Fatalf("failed to create EPH: %s\n", err)
}
defer p.Close(context.Background())
```

### <a name="create-handler"></a>Criar o manipulador 

Agora, crie um manipulador e registá-lo com o anfitrião do processador de eventos. Quando o anfitrião é iniciado, ele aplica-se este e outros manipuladores especificados a mensagens de entrada:

```go
handler := func(ctx context.Context, event *eventhubs.Event) error {
    fmt.Printf("received: %s\n", string(event.Data))
    return nil
}

// register the handler with the EPH
_, err := p.RegisterHandler(ctx, handler)
if err != nil {
    log.Fatalf("failed to register handler: %s\n", err)
}
```

### <a name="write-code-to-receive-messages"></a>Escrever código para receber mensagens

Com tudo configurado, pode iniciar o anfitrião do processador de eventos com `Start(context)` mantê-lo permanentemente em execução ou com `StartNonBlocking(context)` apenas executar, desde que as mensagens estão disponíveis.

Este tutorial inicia e executa os seguintes; Veja o exemplo do GitHub para um exemplo usando `StartNonBlocking`:

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="next-steps"></a>Passos seguintes
Leia os seguintes artigos:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funcionalidades e terminologia nos Hubs de Eventos do Azure](event-hubs-features.md)
- [FAQ dos Hubs de Eventos](event-hubs-faq.md)


<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
