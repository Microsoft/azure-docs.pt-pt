---
title: 'Tutorial: Criar um aplicativo altamente disponível com armazenamento de BLOBs-armazenamento do Azure'
description: Utilize o armazenamento georredundante de acesso de leitura para tornar os dados das suas aplicações altamente disponíveis
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: tamram
ms.reviewer: artek
ms.custom: mvc
ms.subservice: blobs
ms.openlocfilehash: 124b10607f710ddfb76787eac09dea7ec6ffc03c
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173059"
---
# <a name="tutorial-build-a-highly-available-application-with-blob-storage"></a>Tutorial: Crie um aplicativo altamente disponível com o armazenamento de BLOBs

Este tutorial é a primeira parte de uma série. Nele, você aprende a tornar seus dados de aplicativo altamente disponíveis no Azure.

Quando você concluir este tutorial, terá um aplicativo de console que carrega e recupera um blob de uma conta de armazenamento com [redundância geográfica com acesso de leitura](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (ra-grs).

O RA-GRS funciona replicando as transações de uma região primária para uma região secundária. Este processo de replicação garante que os dados na região secundária acabam por ser consistentes. O aplicativo usa o padrão de [disjuntor](/azure/architecture/patterns/circuit-breaker) para determinar a qual ponto de extremidade se conectar, alternando automaticamente entre os pontos de extremidades à medida que as falhas e recuperações são simuladas.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Na primeira parte da série, saiba como:

> [!div class="checklist"]
> * Criar uma conta de armazenamento
> * Definir a cadeia de ligação
> * Executar a aplicação de consola

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

* Instale o [Visual Studio 2019](https://www.visualstudio.com/downloads/) com as seguintes cargas de trabalho:
  - **Desenvolvimento do Azure**

  ![Desenvolvimento do Azure (na Web e na Cloud)](media/storage-create-geo-redundant-storage/workloads.png)

# <a name="pythontabpython"></a>[Python](#tab/python)

* Instalar o [Python](https://www.python.org/downloads/)
* Transfira e instale o [SDK de Armazenamento do Azure para Python](https://github.com/Azure/azure-storage-python).

# <a name="java-v10-sdktabjava-v10"></a>[SDK do Java v10](#tab/java-v10)

* Instale e configure o [Maven](https://maven.apache.org/download.cgi) para funcionar a partir da linha de comandos
* instalar e configurar um [JDK](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

* Instale o [node. js](https://nodejs.org).

---

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Uma conta de armazenamento fornece um namespace exclusivo para armazenar e acessar seus objetos de dados do armazenamento do Azure.

Siga estes passos para criar uma conta de armazenamento georredundante com acesso de leitura:

1. Selecione o botão **Criar um recurso**, no canto superior esquerdo do portal do Azure.
2. Selecione **armazenamento** na página **novo** .
3. Selecione **conta de armazenamento-BLOB, arquivo, tabela, fila** em **destaque**.
4. Preencha o formulário da conta de armazenamento com as seguintes informações, conforme mostrado na imagem abaixo e selecione **Criar**:

   | Definição       | Valor sugerido | Descrição |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Name** | mystorageaccount | Um valor exclusivo para a conta de armazenamento |
   | **Deployment model** (Modelo de implementação) | Resource Manager  | O Resource Manager contém as funcionalidades mais recentes.|
   | **Account kind** (Tipo de conta) | StorageV2 | Para obter detalhes sobre os tipos de contas, veja [Tipos de contas de armazenamento](../common/storage-introduction.md#types-of-storage-accounts) |
   | **Performance** (Desempenho) | Standard | O desempenho standard é suficiente para este cenário de exemplo. |
   | **Replicação**| Armazenamento georredundante com acesso de leitura (RA-GRS) | É necessário para o exemplo funcionar. |
   |**Subscrição** | A sua subscrição |Para obter detalhes sobre as suas subscrições, veja [Subscriptions](https://account.azure.com/Subscriptions) (Subscrições). |
   |**ResourceGroup** | myResourceGroup |Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Atribuição de nomes de regras e restrições). |
   |**Location** | East US | Escolher uma localização. |

![criar conta de armazenamento](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>Transferir o exemplo

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[Transfira o projeto de exemplo](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) e extraia (deszipe) o ficheiro storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. Também pode utilizar o [git](https://git-scm.com/) para transferir uma cópia da aplicação para o seu ambiente de desenvolvimento. O projeto de exemplo contém uma aplicação de consola.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="pythontabpython"></a>[Python](#tab/python)

[Transfira o projeto de exemplo](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) e extraia (deszipe) o ficheiro storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. Também pode utilizar o [git](https://git-scm.com/) para transferir uma cópia da aplicação para o seu ambiente de desenvolvimento. O projeto de exemplo contém uma aplicação Python básica.

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="java-v10-sdktabjava-v10"></a>[SDK do Java v10](#tab/java-v10)

[Transferir o projeto de exemplo](https://github.com/Azure-Samples/storage-java-V10-ha-ra-grs) e extrair o ficheiro storage-java-ragrs.zip. Também pode utilizar o [git](https://git-scm.com/) para transferir uma cópia da aplicação para o seu ambiente de desenvolvimento. O projeto de exemplo contém uma aplicação Java básica.

```bash
git clone https://github.com/Azure-Samples/storage-java-V10-ha-ra-grs
```

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

[Baixe o projeto de exemplo](https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs) e descompacte o arquivo. Também pode utilizar o [git](https://git-scm.com/) para transferir uma cópia da aplicação para o seu ambiente de desenvolvimento. O projeto de exemplo contém um aplicativo node. js básico.

```bash
git clone https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs
```

---

## <a name="configure-the-sample"></a>Configurar o exemplo

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Na aplicação, tem de indicar a cadeia de ligação da sua conta de armazenamento. Pode armazenar esta cadeia de ligação numa variável de ambiente no computador local que executa a aplicação. Siga um dos exemplos abaixo, consoante o Sistema Operativo para criar a variável de ambiente.

No portal do Azure, navegue para a sua conta de armazenamento. Selecione **Chaves de acesso**, em **Definições**, na conta de armazenamento. Copie a **cadeia de ligação** da chave primária ou secundária. Execute um dos comandos a seguir com base em seu sistema operacional, \<substituindo yourconnectionstring\> pela cadeia de conexão real. Este comando guarda uma variável de ambiente no computador local. No Windows, a variável de ambiente não estará disponível até que você recarregue o **prompt de comando** ou o shell que você está usando.

### <a name="linux"></a>Linux

```
export storageconnectionstring=<yourconnectionstring>
```

### <a name="windows"></a>Windows

```powershell
setx storageconnectionstring "<yourconnectionstring>"
```

# <a name="pythontabpython"></a>[Python](#tab/python)

No aplicativo, você deve fornecer suas credenciais de conta de armazenamento. Você pode armazenar essas informações em variáveis de ambiente no computador local que executa o aplicativo. Siga um dos exemplos abaixo, dependendo do seu sistema operacional, para criar as variáveis de ambiente.

No portal do Azure, navegue para a sua conta de armazenamento. Selecione **Chaves de acesso**, em **Definições**, na conta de armazenamento. Cole o **nome da conta de armazenamento** e os valores de **chave** nos comandos a \<seguir\> , substituindo os espaços reservados YourAccountName e \<YourAccountKey\> . Esse comando salva as variáveis de ambiente no computador local. No Windows, a variável de ambiente não estará disponível até que você recarregue o **prompt de comando** ou o shell que você está usando.

### <a name="linux"></a>Linux

```
export accountname=<youraccountname>
export accountkey=<youraccountkey>
```

### <a name="windows"></a>Windows

```powershell
setx accountname "<youraccountname>"
setx accountkey "<youraccountkey>"
```

# <a name="java-v10-sdktabjava-v10"></a>[SDK do Java v10](#tab/java-v10)

Este exemplo requer que você armazene com segurança o nome e a chave da sua conta de armazenamento. Armazene-os em variáveis de ambiente locais no computador que executará o exemplo. Use o exemplo do Linux ou do Windows, dependendo do seu sistema operacional, para criar as variáveis de ambiente. No Windows, a variável de ambiente não está disponível até que você recarregue o **prompt de comando** ou o shell que você está usando.

### <a name="linux-example"></a>Exemplo do Linux

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

### <a name="windows-example"></a>Exemplo do Windows

```powershell
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

Para executar este exemplo, você deve adicionar suas credenciais de conta de armazenamento `.env.example` ao arquivo e, em seguida `.env`, renomeá-lo como.

```
AZURE_STORAGE_ACCOUNT_NAME=<replace with your storage account name>
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=<replace with your storage account access key>
```

Você pode encontrar essas informações no portal do Azure navegando até sua conta de armazenamento e selecionando **chaves de acesso** na seção **configurações** .

Instale as dependências necessárias. Para fazer isso, abra um prompt de comando, navegue até a pasta de exemplo e `npm install`, em seguida, digite.

---

## <a name="run-the-console-application"></a>Executar a aplicação de consola

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

No Visual Studio, pressione **F5** ou selecione **Iniciar** para iniciar a depuração do aplicativo. O Visual Studio restaura automaticamente os pacotes NuGet ausentes, se configurados, visita [instalar e reinstalar pacotes com a restauração do pacote](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview) para saber mais.

É iniciada uma janela de consola e a aplicação começa a ser executada. A aplicação carrega a imagem **HelloWorld.png** da solução para a conta de armazenamento. A aplicação verifica para garantir que a imagem foi replicada para o ponto final de RA-GRS secundário. Em seguida, começa a transferir a imagem até 999 vezes. Cada leitura é representada por um **P** ou um **S**. em que **P** representa o ponto final primário e **S** o secundário.

![Aplicação de consola em execução](media/storage-create-geo-redundant-storage/figure3.png)

No código de exemplo, a tarefa `RunCircuitBreakerAsync` no ficheiro `Program.cs` é utilizada para transferir uma imagem da conta de armazenamento através do método [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync). Antes da transferência, é definido um [OperationContext](/dotnet/api/microsoft.azure.cosmos.table.operationcontext). O contexto da operação define os processadores de eventos que são acionados se uma transferência for concluída com êxito ou se falhar e estiver a repetir a operação.

# <a name="pythontabpython"></a>[Python](#tab/python)

Para executar a aplicação num terminal ou numa linha de comandos, aceda ao diretório **circuitbreaker.py** e introduza `python circuitbreaker.py`. A aplicação carrega a imagem **HelloWorld.png** da solução para a conta de armazenamento. A aplicação verifica para garantir que a imagem foi replicada para o ponto final de RA-GRS secundário. Em seguida, começa a transferir a imagem até 999 vezes. Cada leitura é representada por um **P** ou um **S**. em que **P** representa o ponto final primário e **S** o secundário.

![Aplicação de consola em execução](media/storage-create-geo-redundant-storage/figure3.png)

No código de exemplo, o método `run_circuit_breaker` no ficheiro `circuitbreaker.py` é utilizado para transferir uma imagem da conta de armazenamento através do método [get_blob_to_path](https://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html).

A função de repetição do objeto de armazenamento está definida como uma política de repetição linear. A função de repetição determina se deve repetir um pedido e especifica o número de segundos a aguardar antes da repetição. Defina o valor **retry\_to\_secondary** como verdadeiro se pretender repetir o pedido para o ponto final secundário, caso o pedido inicial para o primário falhe. A aplicação de exemplo, é definida uma política de repetição personalizada na função `retry_callback` do objeto de armazenamento.

Antes do download, o objeto de serviço [retry_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) e a função [response_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) são definidos. Estas funções definem os processadores de eventos que são acionados se uma transferência for concluída com êxito ou se falhar e estiver a repetir a operação.

# <a name="java-v10-sdktabjava-v10"></a>[SDK do Java v10](#tab/java-v10)

Para executar o exemplo, use o Maven na linha de comando.

1. Abra um shell e navegue até **Storage-BLOBs-Java-V10-QuickStart** dentro de seu diretório clonado.
2. Introduza `mvn compile exec:java`.

Este exemplo cria um arquivo de teste no diretório padrão. Para usuários do Windows, esse diretório é **AppData\Local\Temp**. O exemplo apresenta as seguintes opções de comandos que você pode inserir:

- Insira **P** para executar uma operação Put Blob. esse comando carrega um arquivo temporário em sua conta de armazenamento.
- Insira **L** para executar uma operação de lista de BLOBs, este comando lista os BLOBs atualmente em seu contêiner.
- Digite **G** para executar uma operação obter BLOB. esse comando baixa um arquivo de sua conta de armazenamento para o computador local.
- Digite **D** para executar uma operação de exclusão de BLOB, este comando exclui o blob de sua conta de armazenamento.
- Digite **E** para fechar o exemplo. esse comando também exclui todos os recursos criados pelo exemplo.

Este exemplo mostra o resultado se executar a aplicação no Windows.

```
Created quickstart container
Enter a command
(P)utBlob | (L)istBlobs | (G)etBlob | (D)eleteBlobs | (E)xitSample
# Enter a command :
P
Uploading the sample file into the container: https://<storageaccount>.blob.core.windows.net/quickstart
# Enter a command :
L
Listing blobs in the container: https://<storageaccount>.blob.core.windows.net/quickstart
Blob name: SampleBlob.txt
# Enter a command :
G
Get the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
The blob was downloaded to C:\Users\<useraccount>\AppData\Local\Temp\downloadedFile13097087873115855761.txt
# Enter a command :
D
Delete the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt

# Enter a command :
>> Blob deleted: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
E
Cleaning up the sample and exiting!
```

Tem controlo do exemplo, por isso, introduza comandos para o código ser executado. As entradas diferenciam maiúsculas de minúsculas.

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

Para executar o exemplo, abra um prompt de comando, navegue até a pasta de exemplo e `node index.js`, em seguida, digite.

O exemplo cria um contêiner em sua conta de armazenamento de BLOBs, carrega **HelloWorld. png** no contêiner e verifica repetidamente se o contêiner e a imagem foram replicados para a região secundária. Após a replicação, ele solicita que você insira **D** ou **Q** (seguido de Enter) para baixar ou encerrar. A saída deve ser semelhante ao exemplo a seguir:

```
Created container successfully: newcontainer1550799840726
Uploaded blob: HelloWorld.png
Checking to see if container and blob have replicated to secondary region.
[0] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[1] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
...
[31] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[32] Container found, but blob has not replicated to secondary region yet.
...
[67] Container found, but blob has not replicated to secondary region yet.
[68] Blob has replicated to secondary region.
Ready for blob download. Enter (D) to download or (Q) to quit, followed by ENTER.
> D
Attempting to download blob...
Blob downloaded from primary endpoint.
> Q
Exiting...
Deleted container newcontainer1550799840726
```

---

## <a name="understand-the-sample-code"></a>Compreender o código de exemplo

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

### <a name="retry-event-handler"></a>Processador de eventos de repetição

O processador de eventos `OperationContextRetrying` é chamado quando a transferência da imagem falha e está definida para repetir. Se for atingido o número máximo de repetições definidas na aplicação, [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) do pedido é alterado para `SecondaryOnly`. Esta definição força a aplicação a tentar transferir a imagem do ponto final secundário. Esta configuração reduz o tempo que demora a pedir a imagem, porque o ponto final primário não é repetido indefinidamente.

```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>Processador de eventos de pedido concluído

O processador de eventos `OperationContextRequestCompleted` é chamado quando a transferência da imagem é bem-sucedida. Se a aplicação estiver a utilizar o ponto final secundário, continua a utilizar este ponto final até 20 vezes. Ao fim dessas 20 vezes, a aplicação define [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) novamente como `PrimaryThenSecondary` e repete o ponto final primário. Se um pedido for bem-sucedido, a aplicação continua a ler a partir do ponto final primário.

```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

### <a name="retry-event-handler"></a>Processador de eventos de repetição

O processador de eventos `retry_callback` é chamado quando a transferência da imagem falha e está definida para repetir. Se for atingido o número máximo de repetições definidas na aplicação, [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) do pedido é alterado para `SECONDARY`. Esta definição força a aplicação a tentar transferir a imagem do ponto final secundário. Esta configuração reduz o tempo que demora a pedir a imagem, porque o ponto final primário não é repetido indefinidamente.

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write(
        "\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
    sys.stdout.flush()

    # Check if we have more than n-retries in which case switch to secondary
    if retry_count >= retry_threshold:

        # Check to see if we can fail over to secondary.
        if blob_client.location_mode != LocationMode.SECONDARY:
            blob_client.location_mode = LocationMode.SECONDARY
            retry_count = 0
        else:
            raise Exception("Both primary and secondary are unreachable. "
                            "Check your application's network connection.")
```

### <a name="request-completed-event-handler"></a>Processador de eventos de pedido concluído

O processador de eventos `response_callback` é chamado quando a transferência da imagem é bem-sucedida. Se a aplicação estiver a utilizar o ponto final secundário, continua a utilizar este ponto final até 20 vezes. Ao fim dessas 20 vezes, a aplicação define [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) novamente como `PRIMARY` e repete o ponto final primário. Se um pedido for bem-sucedido, a aplicação continua a ler a partir do ponto final primário.

```python
def response_callback(response):
    global secondary_read_count
    if blob_client.location_mode == LocationMode.SECONDARY:

        # You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        # then switch back to the primary and see if it is available now.
        secondary_read_count += 1
        if secondary_read_count >= secondary_threshold:
            blob_client.location_mode = LocationMode.PRIMARY
            secondary_read_count = 0
```

# <a name="java-v10-sdktabjava-v10"></a>[SDK do Java v10](#tab/java-v10)

Com o SDK do Java v10, a definição de manipuladores de retorno de chamada é desnecessária e o SDK agora tem algumas diferenças fundamentais do SDK do v7. Em vez de Locationmode, temos um **pipeline**secundário. Você pode definir um pipeline secundário por meio de **RequestRetryOptions** e, se definido, permitirá que o aplicativo alterne automaticamente para o pipeline secundário se ele não conseguir acessar seus dados por meio do pipeline primário.

```java
// We create pipeline options here so that they can be easily used between different pipelines
PipelineOptions myOptions = new PipelineOptions();
myOptions.withRequestRetryOptions(new RequestRetryOptions(RetryPolicyType.EXPONENTIAL, 3, 10, 500L, 1000L, accountName + "-secondary.blob.core.windows.net"));
// We are using a default pipeline here, you can learn more about it at https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview
final ServiceURL serviceURL = new ServiceURL(new URL("https://" + accountName + ".blob.core.windows.net"), StorageURL.createPipeline(creds, myOptions));
```

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

Com o SDK V10 do node. js, os manipuladores de retorno de chamada são desnecessários. Em vez disso, o exemplo cria um pipeline configurado com opções de repetição e um ponto de extremidade secundário. Isso permite que o aplicativo alterne automaticamente para o pipeline secundário se ele não conseguir acessar seus dados por meio do pipeline primário.

```javascript
const accountName = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const storageAccessKey = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
const sharedKeyCredential = new SharedKeyCredential(accountName, storageAccessKey);

const primaryAccountURL = `https://${accountName}.blob.core.windows.net`;
const secondaryAccountURL = `https://${accountName}-secondary.blob.core.windows.net`;

const pipeline = StorageURL.newPipeline(sharedKeyCredential, {
  retryOptions: {
    maxTries: 3,
    tryTimeoutInMs: 10000,
    retryDelayInMs: 500,
    maxRetryDelayInMs: 1000,
    secondaryHost: secondaryAccountURL
  }
});
```

---

## <a name="next-steps"></a>Passos seguintes

Na parte um da série, você aprendeu a tornar um aplicativo altamente disponível com contas de armazenamento RA-GRS.

Avance para a parte dois da série para saber como simular uma falha e forçar a aplicação a utilizar o ponto final RA-GRS secundário.

> [!div class="nextstepaction"]
> [Simulate a failure in connection to your primary storage endpoint](storage-simulate-failure-ragrs-account-app.md) (Simular uma falha associada ao ponto final de armazenamento primário)
