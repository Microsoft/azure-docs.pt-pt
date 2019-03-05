---
title: Criar uma tarefa do Stream Analytics com o Azure PowerShell
description: Este início rápido demonstra como utilizar o módulo Azure PowerShell para implementar e executar uma tarefa do Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 12/20/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
ms.openlocfilehash: 83bcdfa1de3968fbb6fdf427879d84f917610c42
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57339900"
---
# <a name="quickstart-create-a-stream-analytics-job-using-azure-powershell"></a>Início rápido: Criar uma tarefa do Stream Analytics com o Azure PowerShell

O módulo Azure PowerShell é utilizado para criar e gerir recursos do Azure com cmdlets do PowerShell ou scripts. Este início rápido disponibiliza detalhes através do módulo do Azure PowerShell para implementar e executar uma tarefa do Azure Stream Analytics.

A tarefa de exemplo lê os dados de transmissão em fluxo de um dispositivo do IoT Hub. Os dados de entrada são gerados por um simulador online Raspberry Pi. Em seguida, a tarefa do Stream Analytics transforma os dados usando a linguagem de consulta do Stream Analytics para filtrar mensagens com uma temperatura superior a 27°. Por fim, escreve os eventos de saída resultante para um ficheiro no armazenamento de Blobs.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/)

* Este início rápido requer o módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada no computador local. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-Az-ps)(Instalar o módulo do Azure PowerShell).

* Algumas ações não são suportadas pelo Azure PowerShell e tem de ser concluído de utilizar a CLI do Azure versão 2.0.24 ou posterior o IoT Hub e a extensão de IoT para a CLI do Azure. [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e utilize `az extension add --name azure-cli-iot-ext` para instalar a extensão de IoT.


## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o `Connect-AzAccount` de comandos e introduza as credenciais do Azure no browser pop-up:

```powershell
# Connect to your Azure account
Connect-AzAccount
```

Se tiver mais de uma subscrição, selecione a subscrição que pretende utilizar para este início rápido ao executar os seguintes cmdlets. Certifique-se de que substitui `<your subscription name>` pelo nome da sua subscrição:

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

O seguinte bloco de código da CLI do Azure faz muitos comandos para preparar os dados de entrada necessários para a tarefa. Reveja as secções para compreender o código.

1. Na janela do PowerShell, execute o [início de sessão az](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) comando para iniciar sessão na sua conta do Azure.

    Quando inicia sessão com êxito no, a CLI do Azure devolve uma lista das suas subscrições. Copiar a subscrição está a utilizar para este início rápido e execute o [conjunto de conta de az](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription) comando para selecionar essa subscrição. Escolha a mesma subscrição que selecionou na secção anterior com o PowerShell. Certifique-se substituir `<your subscription name>` com o nome da sua subscrição.

    ```azurecli
    az login

    az account set --subscription "<your subscription>"
    ```

2. Criar um IoT Hub com o [criar hub de iot de az](../iot-hub/iot-hub-create-using-cli.md#create-an-iot-hub) comando. Este exemplo cria um Hub IoT chamado **MyASAIoTHub**. Como os nomes do IoT Hub são exclusivos, tem de surgir com o nome do seu IoT Hub. Defina o SKU como F1 para utilizar o escalão gratuito, se estiver disponível com a sua subscrição. Caso contrário, escolha o escalão mais baixo próxima.

    ```azurecli
    az iot hub create --name "<your IoT Hub name>" --resource-group $resourceGroup --sku S1
    ```

    Quando o hub IoT tiver sido criado, obter o IoT Hub cadeia de ligação utilizando o [az iot hub show-connection-string](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest) comando. Copie a cadeia de ligação completa e guarde-o para quando adicionar o IoT Hub como entrada para a tarefa de Stream Analytics.

    ```azurecli
    az iot hub show-connection-string --hub-name "MyASAIoTHub"
    ```

3. Adicionar um dispositivo ao IoT Hub com o [criar a identidade de dispositivo do iothub az](../iot-hub/quickstart-send-telemetry-c.md#register-a-device) comando. Este exemplo cria um dispositivo designado **MyASAIoTDevice**.

    ```azurecli
    az iot hub device-identity create --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice"
    ```

4. Obter o dispositivo cadeia de ligação utilizando o [identidade de dispositivo de hub iot com az-show-connection-string](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) comando. Copie a cadeia de ligação completa e guarde-o para criar o simulador de Raspberry Pi.

    ```azurecli
    az iot hub device-identity show-connection-string --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice" --output table
    ```

    **Exemplo de saída:**

    ```azurecli
    HostName=MyASAIoTHub.azure-devices.net;DeviceId=MyASAIoTDevice;SharedAccessKey=a2mnUsg52+NIgYudxYYUNXI67r0JmNubmfVafojG8=
    ```

## <a name="create-blob-storage"></a>Criar armazenamento de BLOBs

O seguinte bloco de código do Azure PowerShell utiliza comandos para criar o armazenamento de BLOBs que é utilizado para a saída da tarefa. Reveja as secções para compreender o código.

1. Criar uma conta de armazenamento para fins gerais com [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount) cmdlet.  Este exemplo cria uma conta de armazenamento denominada **myasaquickstartstorage** com localmente redundante (lrs) e encriptação de BLOBs (ativada por predefinição).

2. Obtenha o contexto da conta de armazenamento `$storageAccount.Context` que define a conta de armazenamento a ser utilizada. Ao trabalhar com contas de armazenamento, referencia o contexto em vez de fornecer repetidamente as credenciais.

3. Crie um contentor de armazenamento com [New-AzStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-AzStoragecontainer).

4. Copie a chave de armazenamento que é debitada pelo código e guarde essa chave para criar a saída da tarefa de transmissão em fluxo mais tarde.

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

Criar uma tarefa do Stream Analytics com o [New-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/new-azstreamanalyticsjob?view=azurermps-5.4.0) cmdlet. Este cmdlet utiliza o nome da tarefa, o nome do grupo de recursos e a definição de tarefa como parâmetros. O nome do trabalho pode ser qualquer nome amigável que o identifique. Ele pode ter carateres alfanuméricos, hífenes, e apenas carateres de sublinhado e tem de ter entre 3 e 63 carateres de comprimento. A definição de trabalho é um ficheiro JSON que contém as propriedades necessárias para criar um trabalho. No computador local, crie um ficheiro denominado `JobDefinition.json` e adicione os seguintes dados JSON:

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

Em seguida, execute o cmdlet `New-AzStreamAnalyticsJob`. Substitua o valor do `jobDefinitionFile` variável com o caminho onde armazenou o ficheiro JSON da definição de tarefa.

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

Adicionar uma entrada para a tarefa com o [New-AzStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/az.streamanalytics/new-azstreamanalyticsinput?view=azurermps-5.4.0) cmdlet. Este cmdlet utiliza o nome da tarefa, o nome da entrada da tarefa, o nome do grupo de recursos e a definição de entrada da tarefa como parâmetros. A definição da entrada do trabalho é um ficheiro JSON que contém as propriedades necessárias para configurar essa entrada. Neste exemplo, criará um armazenamento de BLOBs como entrada.

No computador local, crie um ficheiro denominado `JobInputDefinition.json` e adicione os seguintes dados JSON ao mesmo. Lembre-se de que substitui o valor `accesspolicykey` com o `SharedAccessKey` parte a cadeia de ligação do IoT Hub que guardou na secção anterior.

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

Em seguida, execute o `New-AzStreamAnalyticsInput` cmdlet, lembre-se de que substitua o valor do `jobDefinitionFile` variável com o caminho onde armazenou o ficheiro JSON de entrada de definição de tarefa.

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

Adicionar uma saída para a tarefa com o [New-AzStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/az.streamanalytics/new-azstreamanalyticsoutput?view=azurermps-5.4.0) cmdlet. Este cmdlet utiliza o nome da tarefa, o nome da saída da tarefa, o nome do grupo de recursos e a definição de saída da tarefa como parâmetros. A definição da saída do trabalho é um ficheiro JSON que contém as propriedades necessárias para configurar essa saída. Este exemplo utiliza o armazenamento de blobs como saída.

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

Adicione uma transformação seu trabalho, utilizando o [New-AzStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/az.streamanalytics/new-azstreamanalyticstransformation?view=azurermps-5.4.0) cmdlet. Este cmdlet utiliza o nome da tarefa, o nome da transformação da tarefa, o nome do grupo de recursos e a definição de transformação da tarefa como parâmetros. No computador local, crie um ficheiro denominado `JobTransformationDefinition.json` e adicione os seguintes dados JSON ao mesmo. O ficheiro JSON contém um parâmetro de consulta que define a consulta de transformação:

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

Execute novamente o cmdlet `New-AzStreamAnalyticsTransformation`. Lembre-se de que substitua o valor do `jobTransformationDefinitionFile` variável com o caminho onde armazenou o ficheiro JSON da definição de transformação de tarefa.

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

1. Abra o [Raspberry Pi simulador Online do Azure IoT](https://azure-samples.github.io/raspberry-pi-web-simulator/).

2. Substitua o marcador de posição na linha 15 a cadeia de ligação de dispositivos no Hub IoT do Azure completa que guardou na secção anterior.

3. Clique em **Executar**. A saída deve mostrar os dados de sensor e as mensagens que estão a ser enviadas ao seu IoT Hub.

    ![Simulador Online do Azure IoT raspberry Pi](./media/stream-analytics-quick-create-powershell/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Iniciar a tarefa do Stream Analytics e verificar a saída

Iniciar a tarefa com o [Start-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/start-azstreamanalyticsjob?view=azurermps-5.4.0) cmdlet. Este cmdlet utiliza o nome da tarefa, o nome do grupo de recursos, o modo de início da saída e a hora de início como parâmetros. `OutputStartMode` aceita os valores `JobStartTime` `CustomTime` ou `LastOutputEventTime`. Para obter mais informações sobre ao que cada um destes valores se refere, veja a secção [parameters](https://docs.microsoft.com/powershell/module/az.streamanalytics/start-azstreamanalyticsjob?view=azurermps-5.4.0) (parâmetros) na documentação do PowerShell.

Depois de executar o cmdlet seguinte, devolve `True` como a saída, se o trabalho for iniciado. No contentor de armazenamento, é criada uma pasta de saída com os dados transformados.

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, a tarefa de transmissão em fluxo e todos os recursos relacionados. A eliminação da tarefa evita a faturação das unidades de transmissão em fluxo consumidas pela tarefa. Se estiver a planear utilizar o trabalho no futuro, pode ignorar a eliminação e parar o trabalho por agora. Se não pretender continuar a utilizar esta tarefa, elimine todos os recursos criados neste início rápido, execute o seguinte cmdlet:

```powershell
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, implementou uma tarefa simples do Stream Analytics com o PowerShell. Também pode implementar tarefas do Stream Analytics com o [portal do Azure](stream-analytics-quick-create-portal.md) e o [Visual Studio](stream-analytics-quick-create-vs.md).

Para saber mais sobre como configurar outras origens de entrada e efetuar a deteção em tempo real, avance para o seguinte artigo:

> [!div class="nextstepaction"]
> [Deteção de fraudes em tempo real com o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
