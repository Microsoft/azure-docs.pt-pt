---
title: Início rápido-criar um trabalho de Stream Analytics usando Azure PowerShell
description: Este guia de início rápido demonstra como usar o módulo Azure PowerShell para implantar e executar um trabalho de Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.date: 12/20/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
ms.openlocfilehash: 8fb4d50a4ba56efd9087a829c7d54c3010fc671e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431515"
---
# <a name="quickstart-create-a-stream-analytics-job-using-azure-powershell"></a>Início rápido: criar um trabalho de Stream Analytics usando Azure PowerShell

O módulo Azure PowerShell é usado para criar e gerenciar recursos do Azure usando cmdlets ou scripts do PowerShell. Este início rápido disponibiliza detalhes através do módulo do Azure PowerShell para implementar e executar uma tarefa do Azure Stream Analytics.

O trabalho de exemplo lê os dados de streaming de um dispositivo de Hub IoT. Os dados de entrada são gerados por um simulador online do Raspberry Pi. Em seguida, o trabalho de Stream Analytics transforma os dados usando a linguagem de consulta Stream Analytics para filtrar mensagens com uma temperatura maior que 27 °. Por fim, ele grava os eventos de saída resultantes em um arquivo no armazenamento de BLOBs.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/)

* Este início rápido requer o módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada no computador local. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-Az-ps)(Instalar o módulo do Azure PowerShell).

* Algumas ações do Hub IoT não são suportadas pelo Azure PowerShell e devem ser concluídas usando CLI do Azure versão 2.0.24 ou posterior e a extensão de IoT para CLI do Azure. [Instale o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e use `az extension add --name azure-cli-iot-ext` para instalar a extensão de IOT.


## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Entre em sua assinatura do Azure com o comando `Connect-AzAccount` e insira suas credenciais do Azure no navegador pop-up:

```powershell
# Connect to your Azure account
Connect-AzAccount
```

Se você tiver mais de uma assinatura, selecione a assinatura que deseja usar para este guia de início rápido executando os cmdlets a seguir. Certifique-se de que substitui `<your subscription name>` pelo nome da sua subscrição:

```powershell
# List all available subscriptions.
Get-AzSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzSubscription -SubscriptionName "<your subscription name>" | Select-AzSubscription
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

```powershell
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzResourceGroup `
    -Name $resourceGroup `
    -Location $location
```

## <a name="prepare-the-input-data"></a>Preparar os dados de entrada

Antes de definir o trabalho do Stream Analytics, prepare os dados configurados como entrada do mesmo.

O bloco de código CLI do Azure a seguir faz muitos comandos para preparar os dados de entrada exigidos pelo trabalho. Reveja as secções para compreender o código.

1. Na janela do PowerShell, execute o comando [AZ login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) para entrar em sua conta do Azure.

    Quando você entra com êxito, CLI do Azure retorna uma lista de suas assinaturas. Copie a assinatura que você está usando para este guia de início rápido e execute o comando [AZ Account Set](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription) para selecionar essa assinatura. Escolha a mesma assinatura que você selecionou na seção anterior com o PowerShell. Certifique-se de substituir `<your subscription name>` pelo nome da sua assinatura.

    ```azurecli
    az login

    az account set --subscription "<your subscription>"
    ```

2. Crie um hub IoT usando o comando [AZ IOT Hub Create](../iot-hub/iot-hub-create-using-cli.md#create-an-iot-hub) . Este exemplo cria um hub IoT chamado **MyASAIoTHub**. Como os nomes de Hub IoT são exclusivos, você precisa criar seu próprio nome de Hub IoT. Defina a SKU como F1 para usar a camada gratuita se ela estiver disponível com sua assinatura. Caso contrário, escolha a próxima camada mais baixa.

    ```azurecli
    az iot hub create --name "<your IoT Hub name>" --resource-group $resourceGroup --sku S1
    ```

    Depois que o Hub IoT tiver sido criado, obtenha a cadeia de conexão do Hub IoT usando o comando [AZ IOT Hub show-Connection-String](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest) . Copie toda a cadeia de conexão e salve-a para quando você adicionar o Hub IoT como entrada para seu trabalho de Stream Analytics.

    ```azurecli
    az iot hub show-connection-string --hub-name "MyASAIoTHub"
    ```

3. Adicione um dispositivo ao Hub IoT usando o comando [AZ iothub Device-identidade Create](../iot-hub/quickstart-send-telemetry-c.md#register-a-device) . Este exemplo cria um dispositivo chamado **MyASAIoTDevice**.

    ```azurecli
    az iot hub device-identity create --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice"
    ```

4. Obtenha a cadeia de conexão do dispositivo usando o comando [AZ IOT Hub Device-Identity show-Connection-String](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) . Copie toda a cadeia de conexão e salve-a para quando criar o simulador do Raspberry Pi.

    ```azurecli
    az iot hub device-identity show-connection-string --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice" --output table
    ```

    **Exemplo de saída:**

    ```azurecli
    HostName=MyASAIoTHub.azure-devices.net;DeviceId=MyASAIoTDevice;SharedAccessKey=a2mnUsg52+NIgYudxYYUNXI67r0JmNubmfVafojG8=
    ```

## <a name="create-blob-storage"></a>Criar armazenamento de BLOBs

O bloco de código Azure PowerShell a seguir usa comandos para criar o armazenamento de BLOBs que é usado para a saída do trabalho. Reveja as secções para compreender o código.

1. Crie uma conta de armazenamento de uso geral padrão usando o cmdlet [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount) .  Este exemplo cria uma conta de armazenamento chamada **myasaquickstartstorage** com LRS (armazenamento com redundância local) e criptografia de BLOB (habilitada por padrão).

2. Obtenha o contexto da conta de armazenamento `$storageAccount.Context` que define a conta de armazenamento a ser utilizada. Ao trabalhar com contas de armazenamento, referencia o contexto em vez de fornecer repetidamente as credenciais.

3. Crie um contêiner de armazenamento usando [New-AzStorageContainer](https://docs.microsoft.com/powershell/module/az.storage/new-azstoragecontainer).

4. Copie a chave de armazenamento que é emitida pelo código e salve essa chave para criar a saída do trabalho de streaming posteriormente.

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

Crie um trabalho de Stream Analytics com o cmdlet [New-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/new-azstreamanalyticsjob) . Esse cmdlet usa o nome do trabalho, o nome do grupo de recursos e a definição de trabalho como parâmetros. O nome do trabalho pode ser qualquer nome amigável que o identifique. Ele pode ter caracteres alfanuméricos, hifens e sublinhados somente e deve ter entre 3 e 63 caracteres de comprimento. A definição de trabalho é um ficheiro JSON que contém as propriedades necessárias para criar um trabalho. No computador local, crie um ficheiro denominado `JobDefinition.json` e adicione os seguintes dados JSON:

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

Em seguida, execute o cmdlet `New-AzStreamAnalyticsJob`. Substitua o valor de `jobDefinitionFile` variável pelo caminho em que você armazenou o arquivo JSON de definição de trabalho.

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

Adicione uma entrada ao seu trabalho usando o cmdlet [New-AzStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/az.streamanalytics/new-azstreamanalyticsinput) . Este cmdlet utiliza o nome da tarefa, o nome da entrada da tarefa, o nome do grupo de recursos e a definição de entrada da tarefa como parâmetros. A definição da entrada do trabalho é um ficheiro JSON que contém as propriedades necessárias para configurar essa entrada. Neste exemplo, você criará um armazenamento de BLOBs como uma entrada.

No computador local, crie um ficheiro denominado `JobInputDefinition.json` e adicione os seguintes dados JSON ao mesmo. Certifique-se de substituir o valor de `accesspolicykey` pela parte `SharedAccessKey` da cadeia de conexão do Hub IoT que você salvou em uma seção anterior.

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

Em seguida, execute o cmdlet `New-AzStreamAnalyticsInput`, substitua o valor da variável `jobDefinitionFile` pelo caminho em que você armazenou o arquivo JSON de definição de entrada do trabalho.

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

Adicione uma saída ao seu trabalho usando o cmdlet [New-AzStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/az.streamanalytics/new-azstreamanalyticsoutput) . Este cmdlet utiliza o nome da tarefa, o nome da saída da tarefa, o nome do grupo de recursos e a definição de saída da tarefa como parâmetros. A definição da saída do trabalho é um ficheiro JSON que contém as propriedades necessárias para configurar essa saída. Este exemplo utiliza o armazenamento de blobs como saída.

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

Adicione uma transformação seu trabalho usando o cmdlet [New-AzStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/az.streamanalytics/new-azstreamanalyticstransformation) . Este cmdlet utiliza o nome da tarefa, o nome da transformação da tarefa, o nome do grupo de recursos e a definição de transformação da tarefa como parâmetros. No computador local, crie um ficheiro denominado `JobTransformationDefinition.json` e adicione os seguintes dados JSON ao mesmo. O ficheiro JSON contém um parâmetro de consulta que define a consulta de transformação:

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

Execute novamente o cmdlet `New-AzStreamAnalyticsTransformation`. Certifique-se de substituir o valor de `jobTransformationDefinitionFile` variável pelo caminho em que você armazenou o arquivo JSON de definição de transformação do trabalho.

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobTransformationDefinitionFile `
  -Name $jobTransformationName -Force
```
## <a name="run-the-iot-simulator"></a>Executar o simulador de IoT

1. Abra o [simulador online do Azure IOT do Raspberry Pi](https://azure-samples.github.io/raspberry-pi-web-simulator/).

2. Substitua o espaço reservado na linha 15 pela cadeia de conexão do dispositivo do Hub IoT do Azure inteira que você salvou em uma seção anterior.

3. Clique em **Executar**. A saída deve mostrar os dados do sensor e as mensagens que estão sendo enviadas para o Hub IoT.

    ![Simulador Online do Azure IoT Raspberry Pi](./media/stream-analytics-quick-create-powershell/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Iniciar a tarefa do Stream Analytics e verificar a saída

Inicie o trabalho usando o cmdlet [Start-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/start-azstreamanalyticsjob) . Este cmdlet utiliza o nome da tarefa, o nome do grupo de recursos, o modo de início da saída e a hora de início como parâmetros. `OutputStartMode` aceita os valores `JobStartTime``CustomTime` ou `LastOutputEventTime`. Para obter mais informações sobre ao que cada um destes valores se refere, veja a secção [parameters](https://docs.microsoft.com/powershell/module/az.streamanalytics/start-azstreamanalyticsjob) (parâmetros) na documentação do PowerShell.

Depois de executar o cmdlet seguinte, devolve `True` como a saída, se o trabalho for iniciado. No contentor de armazenamento, é criada uma pasta de saída com os dados transformados.

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, a tarefa de transmissão em fluxo e todos os recursos relacionados. A eliminação da tarefa evita a faturação das unidades de transmissão em fluxo consumidas pela tarefa. Se estiver a planear utilizar o trabalho no futuro, pode ignorar a eliminação e parar o trabalho por agora. Se você não for continuar a usar esse trabalho, exclua todos os recursos criados por este guia de início rápido executando o seguinte cmdlet:

```powershell
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, implementou uma tarefa simples do Stream Analytics com o PowerShell. Também pode implementar tarefas do Stream Analytics com o [portal do Azure](stream-analytics-quick-create-portal.md) e o [Visual Studio](stream-analytics-quick-create-vs.md).

Para saber mais sobre como configurar outras origens de entrada e efetuar a deteção em tempo real, avance para o seguinte artigo:

> [!div class="nextstepaction"]
> [Deteção de fraudes em tempo real com o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
