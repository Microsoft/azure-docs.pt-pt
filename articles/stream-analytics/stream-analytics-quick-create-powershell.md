---
title: Quickstart - Criar um trabalho de Stream Analytics usando o Azure PowerShell
description: Este quickstart demonstra como usar o módulo Azure PowerShell para implementar e executar um trabalho de Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.date: 12/20/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
ms.openlocfilehash: 8fb4d50a4ba56efd9087a829c7d54c3010fc671e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78382030"
---
# <a name="quickstart-create-a-stream-analytics-job-using-azure-powershell"></a>Quickstart: Criar um trabalho de Stream Analytics usando o Azure PowerShell

O módulo Azure PowerShell é utilizado para criar e gerir recursos Azure utilizando cmdlets ou scripts PowerShell. Este início rápido disponibiliza detalhes através do módulo do Azure PowerShell para implementar e executar uma tarefa do Azure Stream Analytics.

O trabalho de exemplo lê dados de streaming de um dispositivo IoT Hub. Os dados de entrada são gerados por um simulador online Raspberry Pi. Em seguida, o trabalho stream analytics transforma os dados usando o idioma de consulta Stream Analytics para filtrar mensagens com uma temperatura superior a 27°. Finalmente, escreve os eventos de saída resultantes num ficheiro em armazenamento de bolhas.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/)

* Este arranque rápido requer o módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada no computador local. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-Az-ps)(Instalar o módulo do Azure PowerShell).

* Algumas ações do IoT Hub não são suportadas pelo Azure PowerShell e devem ser concluídas utilizando a versão Azure CLI 2.0.24 ou posterior e a extensão IoT para o Azure CLI. [Instale o AZURI CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e utilize `az extension add --name azure-cli-iot-ext` para instalar a extensão IoT.


## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se na sua subscrição Azure com o comando `Connect-AzAccount` e introduza as suas credenciais Azure no navegador pop-up:

```powershell
# Connect to your Azure account
Connect-AzAccount
```

Se tiver mais de uma subscrição, selecione a subscrição que gostaria de utilizar para este arranque rápido executando os seguintes cmdlets. Certifique-se de que substitui `<your subscription name>` pelo nome da sua subscrição:

```powershell
# List all available subscriptions.
Get-AzSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzSubscription -SubscriptionName "<your subscription name>" | Select-AzSubscription
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos:

Crie um grupo de recursos Azure com [o New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

```powershell
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzResourceGroup `
    -Name $resourceGroup `
    -Location $location
```

## <a name="prepare-the-input-data"></a>Preparar os dados de entrada

Antes de definir o trabalho do Stream Analytics, prepare os dados configurados como entrada do mesmo.

O seguinte bloco de código SLI Azure faz muitos comandos para preparar os dados de entrada exigidos pelo trabalho. Reveja as secções para compreender o código.

1. Na janela PowerShell, execute o comando [de login az](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) para iniciar sessão na sua conta Azure.

    Quando iniciar sessão com sucesso, o Azure CLI devolve uma lista das suas subscrições. Copie a subscrição que está a utilizar para este arranque rápido e execute o comando de conjunto de [contas az](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription) para selecionar essa subscrição. Escolha a mesma subscrição selecionada na secção anterior com a PowerShell. Certifique-se de que substitui `<your subscription name>` pelo nome da sua subscrição.

    ```azurecli
    az login

    az account set --subscription "<your subscription>"
    ```

2. Criar um Hub IoT usando o [hub az iot criar](../iot-hub/iot-hub-create-using-cli.md#create-an-iot-hub) comando. Este exemplo cria um Hub IoT chamado **MyASAIoTHub**. Como os nomes do IoT Hub são únicos, tens de inventar o teu próprio nome IoT Hub. Detete o SKU para F1 para utilizar o nível gratuito se estiver disponível com a sua subscrição. Caso contrário, escolha o próximo nível mais baixo.

    ```azurecli
    az iot hub create --name "<your IoT Hub name>" --resource-group $resourceGroup --sku S1
    ```

    Uma vez criado o hub IoT, obtenha a cadeia de ligação IoT Hub utilizando o comando de cadeia de conexão de [eixo az iot.](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest) Copie toda a cadeia de ligação e guarde-a para quando adicionar o IoT Hub como entrada para o seu trabalho stream analytics.

    ```azurecli
    az iot hub show-connection-string --hub-name "MyASAIoTHub"
    ```

3. Adicione um dispositivo ao IoT Hub utilizando o comando de criação de identidade de [dispositivo az iothub.](../iot-hub/quickstart-send-telemetry-c.md#register-a-device) Este exemplo cria um dispositivo chamado **MyASAIoTDevice**.

    ```azurecli
    az iot hub device-identity create --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice"
    ```

4. Obtenha a cadeia de ligação do dispositivo utilizando o comando de cadeia de ligação de dispositivo-ligação de eixo [az iot.](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) Copie toda a cadeia de ligação e guarde-a para quando criar o simulador Raspberry Pi.

    ```azurecli
    az iot hub device-identity show-connection-string --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice" --output table
    ```

    **Exemplo de saída:**

    ```azurecli
    HostName=MyASAIoTHub.azure-devices.net;DeviceId=MyASAIoTDevice;SharedAccessKey=a2mnUsg52+NIgYudxYYUNXI67r0JmNubmfVafojG8=
    ```

## <a name="create-blob-storage"></a>Criar armazenamento de bolhas

O seguinte bloco de código Sonéris PowerShell utiliza comandos para criar armazenamento de bolhas que é usado para a saída de trabalho. Reveja as secções para compreender o código.

1. Crie uma conta de armazenamento padrão para fins gerais utilizando o [cmdlet New-AzStorageAccount.](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount)  Este exemplo cria uma conta de armazenamento chamada **myasaquickstartstorage** com armazenamento localmente redundante (LRS) e encriptação blob (ativada por padrão).

2. Obtenha o contexto da conta de armazenamento `$storageAccount.Context` que define a conta de armazenamento a ser utilizada. Ao trabalhar com contas de armazenamento, referencia o contexto em vez de fornecer repetidamente as credenciais.

3. Crie um recipiente de armazenamento utilizando o [New-AzStorageContainer](https://docs.microsoft.com/powershell/module/az.storage/new-azstoragecontainer).

4. Copie a chave de armazenamento que é saída pelo código e guarde essa chave para criar a saída do trabalho de streaming mais tarde.

    ```powershell
    $storageAccountName = "myasaquickstartstorage"
    $storageAccount = New-AzStorageAccount `
      -ResourceGroupName $resourceGroup `
      -Name $storageAccountName `
      -Location $location `
      -SkuName Standard_LRS `
      -Kind Storage

    $ctx = $storageAccount.Context
    $containerName = "container1"

    New-AzStorageContainer `
      -Name $containerName `
      -Context $ctx

    $storageAccountKey = (Get-AzStorageAccountKey `
      -ResourceGroupName $resourceGroup `
      -Name $storageAccountName).Value[0]

    Write-Host "The <storage account key> placeholder needs to be replaced in your output json files with this key value:"
    Write-Host $storageAccountKey -ForegroundColor Cyan
    ```

## <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics

Crie um trabalho de Stream Analytics com [o cmdlet New-AzStreamAnalyticsJob.](https://docs.microsoft.com/powershell/module/az.streamanalytics/new-azstreamanalyticsjob) Este cmdlet tem o nome de trabalho, nome do grupo de recursos e definição de trabalho como parâmetros. O nome do trabalho pode ser qualquer nome amigável que o identifique. Pode ter caracteres alfanuméricos, hífenes, e sublinha apenas e deve ter entre 3 e 63 caracteres de comprimento. A definição de trabalho é um ficheiro JSON que contém as propriedades necessárias para criar um trabalho. No computador local, crie um ficheiro denominado `JobDefinition.json` e adicione os seguintes dados JSON:

```json
{
  "location":"WestUS2",
  "properties":{
    "sku":{
      "name":"standard"
    },
    "eventsOutOfOrderPolicy":"adjust",
    "eventsOutOfOrderMaxDelayInSeconds":10,
    "compatibilityLevel": 1.1
  }
}
```

Em seguida, execute o cmdlet `New-AzStreamAnalyticsJob`. Substitua o valor da variável `jobDefinitionFile` pelo caminho onde armazenou o ficheiro JSON de definição de trabalho.

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -File $jobDefinitionFile `
  -Name $jobName `
  -Force
```

## <a name="configure-input-to-the-job"></a>Configurar a entrada da tarefa

Adicione uma entrada ao seu trabalho utilizando o cmdlet [New-AzStreamAnalyticsInputput.](https://docs.microsoft.com/powershell/module/az.streamanalytics/new-azstreamanalyticsinput) Este cmdlet utiliza o nome da tarefa, o nome da entrada da tarefa, o nome do grupo de recursos e a definição de entrada da tarefa como parâmetros. A definição da entrada do trabalho é um ficheiro JSON que contém as propriedades necessárias para configurar essa entrada. Neste exemplo, você vai criar um armazenamento de bolhas como uma entrada.

No computador local, crie um ficheiro denominado `JobInputDefinition.json` e adicione os seguintes dados JSON ao mesmo. Certifique-se de que substitui o valor por `accesspolicykey` pela `SharedAccessKey` porção da cadeia de ligação IoT Hub que guardou numa secção anterior.

```json
{
    "properties": {
        "type": "Stream",
        "datasource": {
            "type": "Microsoft.Devices/IotHubs",
            "properties": {
                "iotHubNamespace": "MyASAIoTHub",
                "sharedAccessPolicyName": "iothubowner",
                "sharedAccessPolicyKey": "accesspolicykey",
                "endpoint": "messages/events",
                "consumerGroupName": "$Default"
                }
        },
        "compression": {
            "type": "None"
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8"
            }
        }
    },
    "name": "IoTHubInput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/inputs"
}
```

Em seguida, faça o `New-AzStreamAnalyticsInput` cmdlet, certifique-se de substituir o valor da variável `jobDefinitionFile` pelo caminho onde armazenou o ficheiro JSON de definição de entrada de trabalho.

```powershell
$jobInputName = "IoTHubInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName
```

## <a name="configure-output-to-the-job"></a>Configurar a saída da tarefa

Adicione uma saída ao seu trabalho utilizando o cmdlet [New-AzStreamAnalyticsOutput.](https://docs.microsoft.com/powershell/module/az.streamanalytics/new-azstreamanalyticsoutput) Este cmdlet utiliza o nome da tarefa, o nome da saída da tarefa, o nome do grupo de recursos e a definição de saída da tarefa como parâmetros. A definição da saída do trabalho é um ficheiro JSON que contém as propriedades necessárias para configurar essa saída. Este exemplo utiliza o armazenamento de blobs como saída.

No computador local, crie um ficheiro denominado `JobOutputDefinition.json` e adicione os seguintes dados JSON ao mesmo. Certifique-se de que substitui o valor de `accountKey` pela chave de acesso da conta de armazenamento, que é o valor armazenado em $storageAccountKey.

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "asaquickstartstorage",
                      "accountKey": "<storage account key>"
                    }
                ],
                "container": "container1",
                "pathPattern": "output/",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8",
                "format": "LineSeparated"
            }
        }
    },
    "name": "BlobOutput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/outputs"
}
```

Em seguida, execute o cmdlet `New-AzStreamAnalyticsOutput`. Confirme que substitui o valor da variável `jobOutputDefinitionFile` pelo caminho em que armazenou o ficheiro JSON da definição da saída do trabalho.

```powershell
$jobOutputName = "BlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobOutputDefinitionFile `
  -Name $jobOutputName -Force
```

## <a name="define-the-transformation-query"></a>Definir a consulta de transformação

Adicione uma transformação ao usar o [cmdlet New-AzStreamAnalyticsTransformation.](https://docs.microsoft.com/powershell/module/az.streamanalytics/new-azstreamanalyticstransformation) Este cmdlet utiliza o nome da tarefa, o nome da transformação da tarefa, o nome do grupo de recursos e a definição de transformação da tarefa como parâmetros. No computador local, crie um ficheiro denominado `JobTransformationDefinition.json` e adicione os seguintes dados JSON ao mesmo. O ficheiro JSON contém um parâmetro de consulta que define a consulta de transformação:

```json
{
    "name":"MyTransformation",
    "type":"Microsoft.StreamAnalytics/streamingjobs/transformations",
    "properties":{
        "streamingUnits":1,
        "script":null,
        "query":" SELECT * INTO BlobOutput FROM IoTHubInput HAVING Temperature > 27"
    }
}
```

Execute novamente o cmdlet `New-AzStreamAnalyticsTransformation`. Certifique-se de substituir o valor da variável `jobTransformationDefinitionFile` pelo caminho onde armazenou o ficheiro JSON de transformação de emprego.

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobTransformationDefinitionFile `
  -Name $jobTransformationName -Force
```
## <a name="run-the-iot-simulator"></a>Executar o simulador IoT

1. Abra o [Simulador Online Raspberry Pi Azure IoT](https://azure-samples.github.io/raspberry-pi-web-simulator/).

2. Substitua o espaço reservado na Linha 15 por toda a cadeia de ligação do Dispositivo Hub Azure IoT que guardou numa secção anterior.

3. Clique em **Executar**. A saída deve mostrar os dados do sensor e as mensagens que estão a ser enviadas para o seu Hub IoT.

    ![Simulador online Raspberry Pi Azure IoT](./media/stream-analytics-quick-create-powershell/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Iniciar a tarefa do Stream Analytics e verificar a saída

Inicie o trabalho utilizando o [cmdlet Start-AzStreamAnalyticsJobJob.](https://docs.microsoft.com/powershell/module/az.streamanalytics/start-azstreamanalyticsjob) Este cmdlet utiliza o nome da tarefa, o nome do grupo de recursos, o modo de início da saída e a hora de início como parâmetros. `OutputStartMode` aceita os valores `JobStartTime``CustomTime` ou `LastOutputEventTime`. Para obter mais informações sobre ao que cada um destes valores se refere, veja a secção [parameters](https://docs.microsoft.com/powershell/module/az.streamanalytics/start-azstreamanalyticsjob) (parâmetros) na documentação do PowerShell.

Depois de executar o cmdlet seguinte, devolve `True` como a saída, se o trabalho for iniciado. No contentor de armazenamento, é criada uma pasta de saída com os dados transformados.

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, a tarefa de transmissão em fluxo e todos os recursos relacionados. A eliminação da tarefa evita a faturação das unidades de transmissão em fluxo consumidas pela tarefa. Se estiver a planear utilizar o trabalho no futuro, pode ignorar a eliminação e parar o trabalho por agora. Se não vai continuar a usar este trabalho, elimine todos os recursos criados por este quickstart executando o seguinte cmdlet:

```powershell
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, implementou uma tarefa simples do Stream Analytics com o PowerShell. Também pode implementar tarefas do Stream Analytics com o [portal do Azure](stream-analytics-quick-create-portal.md) e o [Visual Studio](stream-analytics-quick-create-vs.md).

Para saber mais sobre como configurar outras origens de entrada e efetuar a deteção em tempo real, avance para o seguinte artigo:

> [!div class="nextstepaction"]
> [Deteção de fraudes em tempo real com o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
