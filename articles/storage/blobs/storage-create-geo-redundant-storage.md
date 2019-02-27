---
title: 'Tutorial: Criar uma aplicação de elevada disponibilidade com armazenamento de BLOBs, armazenamento do Azure'
description: Utilize o armazenamento georredundante de acesso de leitura para tornar os dados das suas aplicações altamente disponíveis
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: tamram
ms.custom: mvc
ms.subservice: blobs
ms.openlocfilehash: b031e7b772389aa81fd214d31365c20018cf48ae
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56874448"
---
# <a name="tutorial-build-a-highly-available-application-with-blob-storage"></a>Tutorial: Criar uma aplicação de elevada disponibilidade com armazenamento de BLOBs

Este tutorial é a primeira parte de uma série. Nela, saber como tornar os dados da sua aplicação altamente disponíveis no Azure.

Quando tiver concluído este tutorial, terá uma aplicação de consola que carrega e obtém um blob de uma [acesso de leitura georredundante](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) conta de armazenamento (RA-GRS).

RA-GRS funciona ao replicar as transações da região primária para uma região secundária. Este processo de replicação garante que os dados na região secundária acabam por ser consistentes. A aplicação utiliza a [disjuntor](/azure/architecture/patterns/circuit-breaker) padrão para determinar qual ponto de extremidade para ligar, alternar automaticamente entre pontos de extremidade como falhas e recuperações são simuladas.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Na primeira parte da série, saiba como:

> [!div class="checklist"]
> * Criar uma conta de armazenamento
> * Definir a cadeia de ligação
> * Executar a aplicação de consola

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

* Instale o [2017 do Visual Studio](https://www.visualstudio.com/downloads/) com as seguintes cargas de trabalho:
  - **Desenvolvimento do Azure**

  ![Desenvolvimento do Azure (na Web e na Cloud)](media/storage-create-geo-redundant-storage/workloads.png)

# <a name="pythontabpython"></a>[Python](#tab/python)

* Instalar o [Python](https://www.python.org/downloads/)
* Transfira e instale o [SDK de Armazenamento do Azure para Python](https://github.com/Azure/azure-storage-python).

# <a name="java-v7-sdk-tabjava-v7"></a>[V7 do Java SDK ](#tab/java-v7)

* Instale e configure o [Maven](http://maven.apache.org/download.cgi) para funcionar a partir da linha de comandos
* instalar e configurar um [JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

# <a name="java-v10-sdktabjava-v10"></a>[Java V10 SDK](#tab/java-v10)

* Instale e configure o [Maven](http://maven.apache.org/download.cgi) para funcionar a partir da linha de comandos
* instalar e configurar um [JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

---

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Uma conta de armazenamento fornece um espaço de nomes exclusivo para armazenar e aceder aos seus objetos de dados do armazenamento do Azure.

Siga estes passos para criar uma conta de armazenamento georredundante com acesso de leitura:

1. Selecione o botão **Criar um recurso**, no canto superior esquerdo do portal do Azure.
2. Selecione **armazenamento** partir do **New** página.
3. Selecione **conta de armazenamento - blob, ficheiro, tabela, fila** sob **em destaque**.
4. Preencha o formulário da conta de armazenamento com as seguintes informações, conforme mostrado na imagem abaixo e selecione **Criar**:

   | Definição       | Valor sugerido | Descrição |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nome** | mystorageaccount | Um valor exclusivo para a conta de armazenamento |
   | **Deployment model** (Modelo de implementação) | Resource Manager  | O Resource Manager contém as funcionalidades mais recentes.|
   | **Account kind** (Tipo de conta) | StorageV2 | Para obter detalhes sobre os tipos de contas, veja [Tipos de contas de armazenamento](../common/storage-introduction.md#types-of-storage-accounts) |
   | **Performance** (Desempenho) | Standard | O desempenho standard é suficiente para este cenário de exemplo. |
   | **Replicação**| Armazenamento georredundante com acesso de leitura (RA-GRS) | É necessário para o exemplo funcionar. |
   |**Subscrição** | A sua subscrição |Para obter detalhes sobre as suas subscrições, veja [Subscriptions](https://account.windowsazure.com/Subscriptions) (Subscrições). |
   |**ResourceGroup** | myResourceGroup |Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Atribuição de nomes de regras e restrições). |
   |**Localização** | EUA Leste | Escolher uma localização. |

![criar conta de armazenamento](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>Transferir o exemplo

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

[Transfira o projeto de exemplo](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) e extraia (deszipe) o ficheiro storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. Também pode utilizar o [git](https://git-scm.com/) para transferir uma cópia da aplicação para o seu ambiente de desenvolvimento. O projeto de exemplo contém uma aplicação de consola.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git 
```

# <a name="python-tabpython"></a>[Python] (#tab/python)

[Transfira o projeto de exemplo](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) e extraia (deszipe) o ficheiro storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. Também pode utilizar o [git](https://git-scm.com/) para transferir uma cópia da aplicação para o seu ambiente de desenvolvimento. O projeto de exemplo contém uma aplicação Python básica.

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="java-v7-sdk--tabjava-v7"></a>[V7 do Java SDK] (# separador/java-v7)

[Transferir o projeto de exemplo](https://github.com/Azure-Samples/storage-java-ha-ra-grs) e extrair o ficheiro storage-java-ragrs.zip. Também pode utilizar o [git](https://git-scm.com/) para transferir uma cópia da aplicação para o seu ambiente de desenvolvimento. O projeto de exemplo contém uma aplicação Java básica.

```bash
git clone https://github.com/Azure-Samples/storage-java-ha-ra-grs.git
```

# <a name="java-v10-sdk-tabjava-v10"></a>[Java V10 SDK] (# separador/java-v10)

[Transferir o projeto de exemplo](https://github.com/Azure-Samples/storage-java-V10-ha-ra-grs) e extrair o ficheiro storage-java-ragrs.zip. Também pode utilizar o [git](https://git-scm.com/) para transferir uma cópia da aplicação para o seu ambiente de desenvolvimento. O projeto de exemplo contém uma aplicação Java básica.

```bash
git clone https://github.com/Azure-Samples/storage-java-V10-ha-ra-grs
```

---

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

Na aplicação, tem de indicar a cadeia de ligação da sua conta de armazenamento. Recomenda-se que armazenar esta cadeia de ligação numa variável de ambiente no computador local executando o aplicativo. Siga um dos exemplos abaixo, consoante o Sistema Operativo para criar a variável de ambiente.

No portal do Azure, navegue para a sua conta de armazenamento. Selecione **Chaves de acesso**, em **Definições**, na conta de armazenamento. Copie a **cadeia de ligação** da chave primária ou secundária. Substitua \<yourconnectionstring\> pela cadeia de ligação real ao executar um dos comandos seguintes, consoante o seu Sistema Operativo. Este comando guarda uma variável de ambiente no computador local. No Windows, a variável de ambiente não está disponível enquanto não recarregar a **linha de comandos** ou shell estiver a utilizar. Substitua **\<storageConnectionString\>** no exemplo seguinte:

### <a name="linux"></a>Linux

```
export storageconnectionstring=\<yourconnectionstring\> 
```
### <a name="windows"></a>Windows

```PowerShell
setx storageconnectionstring "\<yourconnectionstring\>"
```

# <a name="python-tabpython"></a>[Python] (#tab/python)

Na aplicação, tem de indicar a cadeia de ligação da sua conta de armazenamento. Recomenda-se que armazenar esta cadeia de ligação numa variável de ambiente no computador local executando o aplicativo. Siga um dos exemplos abaixo, consoante o Sistema Operativo para criar a variável de ambiente.

No portal do Azure, navegue para a sua conta de armazenamento. Selecione **Chaves de acesso**, em **Definições**, na conta de armazenamento. Copie a **cadeia de ligação** da chave primária ou secundária. Substitua \<yourconnectionstring\> pela cadeia de ligação real ao executar um dos comandos seguintes, consoante o seu Sistema Operativo. Este comando guarda uma variável de ambiente no computador local. No Windows, a variável de ambiente não está disponível enquanto não recarregar a **linha de comandos** ou shell estiver a utilizar. Substitua **\<storageConnectionString\>** no exemplo seguinte:

### <a name="linux"></a>Linux

```
export storageconnectionstring=\<yourconnectionstring\> 
```
### <a name="windows"></a>Windows

```PowerShell
setx storageconnectionstring "\<yourconnectionstring\>"
```

# <a name="java-v7-sdk--tabjava-v7"></a>[V7 do Java SDK] (# separador/java-v7)

Na aplicação, tem de indicar a cadeia de ligação da sua conta de armazenamento. Recomenda-se que armazenar esta cadeia de ligação numa variável de ambiente no computador local executando o aplicativo. Siga um dos exemplos abaixo, consoante o Sistema Operativo para criar a variável de ambiente.

No portal do Azure, navegue para a sua conta de armazenamento. Selecione **Chaves de acesso**, em **Definições**, na conta de armazenamento. Copie a **cadeia de ligação** da chave primária ou secundária. Substitua \<yourconnectionstring\> pela cadeia de ligação real ao executar um dos comandos seguintes, consoante o seu Sistema Operativo. Este comando guarda uma variável de ambiente no computador local. No Windows, a variável de ambiente não está disponível enquanto não recarregar a **linha de comandos** ou shell estiver a utilizar. Substitua **\<storageConnectionString\>** no exemplo seguinte:

### <a name="linux"></a>Linux

```
export storageconnectionstring=\<yourconnectionstring\> 
```
### <a name="windows"></a>Windows

```PowerShell
setx storageconnectionstring "\<yourconnectionstring\>"
```

# <a name="java-v10-sdk-tabjava-v10"></a>[Java V10 SDK] (# separador/java-v10)

Este exemplo requer que armazene em segurança o nome e chave da conta de armazenamento. Store-las nas variáveis de ambiente locais do computador que irá executar o exemplo. Utilize a Linux ou o exemplo do Windows, consoante o sistema operativo, para criar as variáveis de ambiente. No Windows, a variável de ambiente não está disponível enquanto não recarregar a **linha de comandos** ou shell estiver a utilizar.

### <a name="linux-example"></a>Exemplo do Linux

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

### <a name="windows-example"></a>Exemplo do Windows

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

---

## <a name="run-the-console-application"></a>Executar a aplicação de consola

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

No Visual Studio, prima **F5** ou selecione **iniciar** para iniciar a depuração da aplicação. Visual studio automaticamente restauros em falta pacotes NuGet, se configurado, visite [instalar e reinstalar pacotes com o restauro do pacote](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview) para saber mais.

É iniciada uma janela de consola e a aplicação começa a ser executada. A aplicação carrega a imagem **HelloWorld.png** da solução para a conta de armazenamento. A aplicação verifica para garantir que a imagem foi replicada para o ponto final de RA-GRS secundário. Em seguida, começa a transferir a imagem até 999 vezes. Cada leitura é representada por um **P** ou uma **S**. em que **P** representa o ponto final primário e **S** o secundário.

![Aplicação de consola em execução](media/storage-create-geo-redundant-storage/figure3.png)

No código de exemplo, a tarefa `RunCircuitBreakerAsync` no ficheiro `Program.cs` é utilizada para transferir uma imagem da conta de armazenamento através do método [DownloadToFileAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_DownloadToFileAsync_System_String_System_IO_FileMode_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_). Antes da transferência, é definido um [OperationContext](/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet). O contexto da operação define os processadores de eventos que são acionados se uma transferência for concluída com êxito ou se falhar e estiver a repetir a operação.

# <a name="python-tabpython"></a>[Python] (#tab/python)

Para executar a aplicação num terminal ou numa linha de comandos, aceda ao diretório **circuitbreaker.py** e introduza `python circuitbreaker.py`. A aplicação carrega a imagem **HelloWorld.png** da solução para a conta de armazenamento. A aplicação verifica para garantir que a imagem foi replicada para o ponto final de RA-GRS secundário. Em seguida, começa a transferir a imagem até 999 vezes. Cada leitura é representada por um **P** ou uma **S**. em que **P** representa o ponto final primário e **S** o secundário.

![Aplicação de consola em execução](media/storage-create-geo-redundant-storage/figure3.png)

No código de exemplo, o método `run_circuit_breaker` no ficheiro `circuitbreaker.py` é utilizado para transferir uma imagem da conta de armazenamento através do método [get_blob_to_path](https://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html). 

A função de repetição do objeto de armazenamento está definida como uma política de repetição linear. A função de repetição determina se deve repetir um pedido e especifica o número de segundos a aguardar antes da repetição. Defina o valor **retry\_to\_secondary** como verdadeiro se pretender repetir o pedido para o ponto final secundário, caso o pedido inicial para o primário falhe. A aplicação de exemplo, é definida uma política de repetição personalizada na função `retry_callback` do objeto de armazenamento.

Antes da transferência, são definidas as funções [retry_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) e [response_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) do objeto Serviço. Estas funções definem os processadores de eventos que são acionados se uma transferência for concluída com êxito ou se falhar e estiver a repetir a operação.  

# <a name="java-v7-sdk-tabjava-v7"></a>[V7 do Java SDK] (# separador/java-v7)

Pode executar a aplicação abrindo um terminal ou âmbito de linha de comandos para a pasta de aplicação transferida. A partir daí, introduza `mvn compile exec:java` para executar a aplicação. A aplicação, em seguida, carrega a imagem **HelloWorld.png** do diretório para a conta de armazenamento e efetua uma verificação para assegurar-se de que a imagem foi replicada para o ponto final RA-GRS secundário. Assim que a verificação estiver concluída, a aplicação irá iniciar a transferência da imagem repetidamente, reportando para o ponto final do qual está a transferir.

A função de repetição do objeto de armazenamento está definida para utilizar uma política de repetição linear. A função de repetição determina se deve repetir um pedido e especifica o número de segundos a aguardar entre cada tentativa. A propriedade **LocationMode** da sua **BlobRequestOptions** está definida como **PRIMÁRIA\_ SEGUIDA DE \_SECUNDÁRIA**. Isto permite que a aplicação mude automaticamente para a localização secundária se não conseguir alcançar a localização primária ao tentar transferir **HelloWorld.png**.

# <a name="java-v10-sdk-tabjava-v10"></a>[Java V10 SDK] (# separador/java-v10)

Para executar o exemplo, use o Maven na linha de comandos.

1. Abra uma shell e navegue até **storage-blobs-java-v10-quickstart** dentro do diretório clonado.
2. Introduza `mvn compile exec:java`.

Este exemplo cria um ficheiro de teste no seu diretório predefinido, para usuários do windows é este diretório **AppData\Local\Temp**. O exemplo, em seguida, apresenta as seguintes opções de comandos que pode introduzir:

- Introduza **P** para executar uma operação de BLOBs colocado, isso carrega um ficheiro temporário à sua conta de armazenamento.
- Introduza **L** para efetuar uma operação de blob de lista, isso listar os blobs atualmente no seu contentor.
- Introduza **G** para efetuar uma operação de obtenção de blob, esta ação transfere um ficheiro da conta de armazenamento no seu computador local.
- Introduza **1!d** para executar uma operação de eliminação do blob, esta ação elimina o blob da sua conta de armazenamento.
- Introduza **i** para fechar o exemplo, isto também elimina todos os recursos de exemplo criado.

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

Tem controlo do exemplo, por isso, introduza comandos para o código ser executado. Entradas diferenciam maiúsculas de minúsculas.

---

## <a name="understand-the-sample-code"></a>Compreender o código de exemplo

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

### <a name="retry-event-handler"></a>Processador de eventos de repetição

O processador de eventos `OperationContextRetrying` é chamado quando a transferência da imagem falha e está definida para repetir. Se for atingido o número máximo de repetições definidas na aplicação, [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) do pedido é alterado para `SecondaryOnly`. Esta definição força a aplicação a tentar transferir a imagem do ponto final secundário. Esta configuração reduz o tempo que demora a pedir a imagem, porque o ponto final primário não é repetido indefinidamente.
 
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

O processador de eventos `OperationContextRequestCompleted` é chamado quando a transferência da imagem é bem-sucedida. Se a aplicação estiver a utilizar o ponto final secundário, continua a utilizar este ponto final até 20 vezes. Ao fim dessas 20 vezes, a aplicação define [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) novamente como `PrimaryThenSecondary` e repete o ponto final primário. Se um pedido for bem-sucedido, a aplicação continua a ler a partir do ponto final primário.

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

# <a name="python-tabpython"></a>[Python] (#tab/python) 

### <a name="retry-event-handler"></a>Processador de eventos de repetição

O processador de eventos `retry_callback` é chamado quando a transferência da imagem falha e está definida para repetir. Se for atingido o número máximo de repetições definidas na aplicação, [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) do pedido é alterado para `SECONDARY`. Esta definição força a aplicação a tentar transferir a imagem do ponto final secundário. Esta configuração reduz o tempo que demora a pedir a imagem, porque o ponto final primário não é repetido indefinidamente.  

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write("\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
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

# <a name="java-v7-sdk--tabjava-v7"></a>[V7 do Java SDK] (# separador/java-v7)

Com o Java, a definição de processadores de chamada de retorno é desnecessária se a propriedade **LocationMode** das **BlobRequestOptions** estiver definida como **PRIMARY\_THEN\_SECONDARY**. Isto permite que a aplicação mude automaticamente para a localização secundária se não conseguir alcançar a localização primária ao tentar transferir **HelloWorld.png**.

```java
    BlobRequestOptions myReqOptions = new BlobRequestOptions();
    myReqOptions.setRetryPolicyFactory(new RetryLinearRetry(deltaBackOff, maxAttempts));
    myReqOptions.setLocationMode(LocationMode.PRIMARY_THEN_SECONDARY);
    blobClient.setDefaultRequestOptions(myReqOptions);

    blob.downloadToFile(downloadedFile.getAbsolutePath(),null,blobClient.getDefaultRequestOptions(),opContext);
```

# <a name="java-v10-sdk-tabjava-v10"></a>[Java V10 SDK] (# separador/java-v10)

Com V10 o Java SDK, definir os manipuladores de retorno de chamada é ainda necessário e o SDK agora tem algumas diferenças fundamentais do V7 SDK. Em vez de LocationMode, temos uma secundária **Pipeline**. Pode definir um pipeline secundário através da **RequestRetryOptions** e, se definido, irá permitir que a aplicação mude automaticamente para o pipeline secundário se não conseguir aceder aos dados através do pipeline principal.

```java
// We create pipeline options here so that they can be easily used between different pipelines
PipelineOptions myOptions = new PipelineOptions();
myOptions.withRequestRetryOptions(new RequestRetryOptions(RetryPolicyType.EXPONENTIAL, 3, 10, 500L, 1000L, accountName + "-secondary.blob.core.windows.net"));
// We are using a default pipeline here, you can learn more about it at https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview
final ServiceURL serviceURL = new ServiceURL(new URL("https://" + accountName + ".blob.core.windows.net"), StorageURL.createPipeline(creds, myOptions));
```
---

## <a name="next-steps"></a>Passos Seguintes

Na primeira parte da série, aprendeu a tornar uma aplicação altamente disponível com contas de armazenamento RA-GRS.

Avance para a parte dois da série para saber como simular uma falha e forçar a aplicação a utilizar o ponto final RA-GRS secundário.

> [!div class="nextstepaction"]
> [Simulate a failure in connection to your primary storage endpoint](storage-simulate-failure-ragrs-account-app.md) (Simular uma falha associada ao ponto final de armazenamento primário)
