---
title: Quickstart - Crie um trabalho stream analytics usando Azure PowerShell
description: Este quickstart demonstra como usar o módulo Azure PowerShell para implementar e executar um trabalho Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.date: 12/20/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 193c8b1f47960b9b42c61a9b0e394e5ebedf7bf7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867605"
---
# <a name="quickstart-create-a-stream-analytics-job-using-azure-powershell"></a>Quickstart: Criar um trabalho stream analytics usando Azure PowerShell

O módulo Azure PowerShell é utilizado para criar e gerir recursos Azure utilizando cmdlets ou scripts PowerShell. Este início rápido disponibiliza detalhes através do módulo do Azure PowerShell para implementar e executar uma tarefa do Azure Stream Analytics.

O trabalho de exemplo lê dados de streaming de um dispositivo IoT Hub. Os dados de entrada são gerados por um simulador online Raspberry Pi. Em seguida, o trabalho Stream Analytics transforma os dados usando a linguagem de consulta Stream Analytics para filtrar mensagens com uma temperatura superior a 27°. Finalmente, escreve os eventos de saída resultantes num ficheiro no armazenamento de bolhas.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita.](https://azure.microsoft.com/free/)

* Este arranque rápido requer o módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada no computador local. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-Az-ps)(Instalar o módulo do Azure PowerShell).

* Algumas ações do IoT Hub não são apoiadas pela Azure PowerShell e devem ser concluídas utilizando a versão 2.0.70 do Azure CLI ou posterior e a extensão IoT para Azure CLI. [Instale o Azure CLI](/cli/azure/install-azure-cli) e `az extension add --name azure-iot` utilize-o para instalar a extensão IoT.


## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se na sua subscrição Azure com o `Connect-AzAccount` comando e insira as suas credenciais Azure no navegador pop-up:

```powershell
# Connect to your Azure account
Connect-AzAccount
```

Se tiver mais de uma subscrição, selecione a subscrição que pretende utilizar para este arranque rápido executando os seguintes cmdlets. Certifique-se de que substitui `<your subscription name>` pelo nome da sua subscrição:

```powershell
# List all available subscriptions.
Get-AzSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzSubscription -SubscriptionName "<your subscription name>" | Select-AzSubscription
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar um grupo de recursos Azure com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

```powershell
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzResourceGroup `
    -Name $resourceGroup `
    -Location $location
```

## <a name="prepare-the-input-data"></a>Preparar os dados de entrada

Antes de definir o trabalho do Stream Analytics, prepare os dados configurados como entrada do mesmo.

O bloco de código Azure CLI seguinte faz muitos comandos para preparar os dados de entrada exigidos pelo trabalho. Reveja as secções para compreender o código.

1. Na sua janela PowerShell, execute o comando [de login az](/cli/azure/authenticate-azure-cli) para iniciar sessão na sua conta Azure.

    Quando iniciar sedições com sucesso, o Azure CLI devolve uma lista das suas subscrições. Copie a subscrição que está a usar para este arranque rápido e execute o comando [conjunto de conta az](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription) para selecionar essa subscrição. Escolha a mesma subscrição selecionada na secção anterior com o PowerShell. Certifique-se de que substitui `<your subscription name>` pelo nome da sua subscrição.

    ```azurecli
    az login

    az account set --subscription "<your subscription>"
    ```

2. Crie um Hub IoT utilizando o [hub az iot criar](../iot-hub/iot-hub-create-using-cli.md#create-an-iot-hub) comando. Este exemplo cria um hub IoT chamado **MyASAIoTHub**. Como os nomes do IoT Hub são únicos, tens de inventar o teu próprio nome IoT Hub. Desa estama o SKU para F1 para utilizar o nível livre se estiver disponível com a sua subscrição. Caso contrário, escolha o próximo nível mais baixo.

    ```azurecli
    az iot hub create --name "<your IoT Hub name>" --resource-group $resourceGroup --sku S1
    ```

    Uma vez criado o hub IoT, obtenha a cadeia de ligação IoT Hub utilizando o comando [az iot hub show-connection-string.](/cli/azure/iot/hub) Copie toda a cadeia de ligação e guarde-a para quando adicionar o Hub IoT como entrada no seu trabalho stream Analytics.

    ```azurecli
    az iot hub show-connection-string --hub-name "MyASAIoTHub"
    ```

3. Adicione um dispositivo ao IoT Hub utilizando o comando [de criação de identidade de dispositivo az iothub.](../iot-hub/quickstart-send-telemetry-c.md#register-a-device) Este exemplo cria um dispositivo chamado **MyASAIoTDevice**.

    ```azurecli
    az iot hub device-identity create --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice"
    ```

4. Obtenha a cadeia de ligação do dispositivo utilizando o comando [az iot hub-identidade de programa-ligação de ligação.](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_show_connection_string) Copie toda a cadeia de ligação e guarde-a para quando criar o simulador Raspberry Pi.

    ```azurecli
    az iot hub device-identity show-connection-string --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice" --output table
    ```

    **Exemplo de saída:**

    ```output
    HostName=MyASAIoTHub.azure-devices.net;DeviceId=MyASAIoTDevice;SharedAccessKey=a2mnUsg52+NIgYudxYYUNXI67r0JmNubmfVafojG8=
    ```

## <a name="create-blob-storage"></a>Criar armazenamento de bolhas

O bloco de código Azure PowerShell que se segue utiliza comandos para criar armazenamento de bolhas que é usado para a saída de trabalho. Reveja as secções para compreender o código.

1. Crie uma conta de armazenamento de uso de [new-AzStorageAccount.](/powershell/module/az.storage/New-azStorageAccount)  Este exemplo cria uma conta de armazenamento chamada **myasaquickstartstorage** com armazenamento localmente redundante (LRS) e encriptação blob (ativada por padrão).

2. Obtenha o contexto da conta de armazenamento `$storageAccount.Context` que define a conta de armazenamento a ser utilizada. Ao trabalhar com contas de armazenamento, referencia o contexto em vez de fornecer repetidamente as credenciais.

3. Criar um recipiente de armazenamento utilizando [o New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer).

4. Copie a chave de armazenamento que é outputada pelo código e guarde essa chave para criar a saída do trabalho de streaming mais tarde.

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

Crie um trabalho stream analytics com [o cmdlet New-AzStreamAnalyticsJob.](/powershell/module/az.streamanalytics/new-azstreamanalyticsjob) Este cmdlet tem o nome de trabalho, nome do grupo de recursos e definição de trabalho como parâmetros. O nome do trabalho pode ser qualquer nome amigável que o identifique. Pode ter caracteres alfanuméricos, hífens e sublinha apenas e deve ter entre 3 e 63 caracteres de comprimento. A definição de trabalho é um ficheiro JSON que contém as propriedades necessárias para criar um trabalho. No computador local, crie um ficheiro denominado `JobDefinition.json` e adicione os seguintes dados JSON:

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

Em seguida, execute o cmdlet `New-AzStreamAnalyticsJob`. Substitua o valor da `jobDefinitionFile` variável pelo caminho onde guardou o ficheiro JSON de definição de trabalho.

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

Adicione uma entrada ao seu trabalho utilizando o [cmdlet New-AzStreamAnalyticsInput.](/powershell/module/az.streamanalytics/new-azstreamanalyticsinput) Este cmdlet utiliza o nome da tarefa, o nome da entrada da tarefa, o nome do grupo de recursos e a definição de entrada da tarefa como parâmetros. A definição da entrada do trabalho é um ficheiro JSON que contém as propriedades necessárias para configurar essa entrada. Neste exemplo, irá criar um armazenamento de bolhas como entrada.

No computador local, crie um ficheiro denominado `JobInputDefinition.json` e adicione os seguintes dados JSON ao mesmo. Certifique-se de que substitui o valor pela `accesspolicykey` parte da cadeia de `SharedAccessKey` ligação IoT Hub que guardou numa secção anterior.

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

Em seguida, executar o `New-AzStreamAnalyticsInput` cmdlet, certifique-se de substituir o valor da `jobDefinitionFile` variável pelo caminho onde guardou o ficheiro JSON de definição de entrada de trabalho.

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

Adicione uma saída ao seu trabalho utilizando o [cmdlet New-AzStreamAnalyticsOutput.](/powershell/module/az.streamanalytics/new-azstreamanalyticsoutput) Este cmdlet utiliza o nome da tarefa, o nome da saída da tarefa, o nome do grupo de recursos e a definição de saída da tarefa como parâmetros. A definição da saída do trabalho é um ficheiro JSON que contém as propriedades necessárias para configurar essa saída. Este exemplo utiliza o armazenamento de blobs como saída.

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

Adicione uma transformação ao seu trabalho utilizando o [cmdlet New-AzStreamAnalyticsTransformation.](/powershell/module/az.streamanalytics/new-azstreamanalyticstransformation) Este cmdlet utiliza o nome da tarefa, o nome da transformação da tarefa, o nome do grupo de recursos e a definição de transformação da tarefa como parâmetros. No computador local, crie um ficheiro denominado `JobTransformationDefinition.json` e adicione os seguintes dados JSON ao mesmo. O ficheiro JSON contém um parâmetro de consulta que define a consulta de transformação:

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

Execute novamente o cmdlet `New-AzStreamAnalyticsTransformation`. Certifique-se de substituir o valor da `jobTransformationDefinitionFile` variável pelo caminho onde guardou o ficheiro JSON de definição de transformação de emprego.

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

1. Abra o [Raspberry Pi Azure IoT Online Simulator](https://azure-samples.github.io/raspberry-pi-web-simulator/).

2. Substitua o espaço reservado na Linha 15 por toda a cadeia de ligação Azure IoT Hub Device que guardou numa secção anterior.

3. Clique em **Run** (Executar). A saída deve mostrar os dados do sensor e as mensagens que estão a ser enviadas para o seu Hub IoT.

    ![Simulador Online do Azure IoT Raspberry Pi](./media/stream-analytics-quick-create-powershell/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Iniciar a tarefa do Stream Analytics e verificar a saída

Inicie o trabalho utilizando o [cmdlet Start-AzStreamAnalyticsJob.](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob) Este cmdlet utiliza o nome da tarefa, o nome do grupo de recursos, o modo de início da saída e a hora de início como parâmetros. `OutputStartMode` aceita os valores `JobStartTime``CustomTime` ou `LastOutputEventTime`. Para obter mais informações sobre ao que cada um destes valores se refere, veja a secção [parameters](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob) (parâmetros) na documentação do PowerShell.

Depois de executar o cmdlet seguinte, devolve `True` como a saída, se o trabalho for iniciado. No contentor de armazenamento, é criada uma pasta de saída com os dados transformados.

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos, a tarefa de transmissão em fluxo e todos os recursos relacionados. A eliminação da tarefa evita a faturação das unidades de transmissão em fluxo consumidas pela tarefa. Se estiver a planear utilizar o trabalho no futuro, pode ignorar a eliminação e parar o trabalho por agora. Se não continuar a utilizar este trabalho, elimine todos os recursos criados por este arranque rápido executando o seguinte cmdlet:

```powershell
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, implementou uma tarefa simples do Stream Analytics com o PowerShell. Também pode implementar tarefas do Stream Analytics com o [portal do Azure](stream-analytics-quick-create-portal.md) e o [Visual Studio](stream-analytics-quick-create-vs.md).

Para saber mais sobre como configurar outras origens de entrada e efetuar a deteção em tempo real, avance para o seguinte artigo:

> [!div class="nextstepaction"]
> [Deteção de fraudes em tempo real com o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)