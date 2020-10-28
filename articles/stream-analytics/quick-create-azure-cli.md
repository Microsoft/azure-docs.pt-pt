---
title: Quickstart - Crie um trabalho Azure Stream Analytics utilizando o Azure CLI
description: Este quickstart mostra como usar o CLI Azure para criar um trabalho Azure Stream Analytics.
services: stream-analytics
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 07/01/2020
ms.openlocfilehash: 6ef4d63e30aeceec9cba3ae97f69afa1c299ec65
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92742738"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-using-the-azure-cli"></a>Quickstart: Criar um trabalho Azure Stream Analytics utilizando o Azure CLI

Neste arranque rápido, utiliza-se o CLI Azure para definir um trabalho stream Analytics que filtra mensagens de sensor em tempo real com uma leitura de temperatura superior a 27. O seu trabalho stream Analytics irá ler dados do IoT Hub, transformar os dados e escrever os dados de volta para um recipiente no armazenamento de bolhas. Os dados de entrada utilizados neste quickstart são gerados por um simulador online Raspberry Pi.

## <a name="before-you-begin"></a>Antes de começar

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-your-environment"></a>Preparar o ambiente

1. Inicie sessão.

   Inicie sessão com o comando [az login](/cli/azure/reference-index#az-login) se estiver a utilizar uma instalação local da CLI.

    ```azurecli
    az login
    ```

    Siga os passos apresentados no seu terminal para concluir o processo de autenticação.

2. Instale a extensão da CLI do Azure.

   Para trabalhar com referências de extensão da CLI do Azure, terá primeiro de instalar a extensão.  As extensões da CLI do Azure dão-lhe acesso a comandos experimentais e de pré-lançamento que ainda não foram enviados como parte do núcleo da CLI.  Para saber mais sobre as extensões, incluindo a atualização e a desinstalação, veja [Utilizar extensões com a CLI do Azure](/cli/azure/azure-cli-extensions-overview).

   Instale a [extensão para Stream Analytics](/cli/azure/ext/stream-analytics/stream-analytics) executando o seguinte comando:

    ```azurecli
    az extension add --name stream-analytics
    ```

   Instale a [extensão para Azure IoT](/cli/azure/ext/azure-iot) executando o seguinte comando:

    ```azurecli
    az extension add --name azure-iot
    ```

3. Crie um grupo de recursos.

   Todos os recursos Azure devem ser implantados num grupo de recursos. Os grupos de recursos permitem organizar e gerir recursos relacionados do Azure.

   Para este arranque rápido, crie um grupo de recursos chamado *streamanalyticsrg* na localização *este* com o seguinte [grupo az criar](/cli/azure/group#az-group-create) comando:

   ```azurecli
   az group create --name streamanalyticsrg --location eastus
   ```

## <a name="prepare-the-input-data"></a>Preparar os dados de entrada

Antes de definir o trabalho stream Analytics, prepare os dados utilizados para a entrada do trabalho.

Os seguintes blocos de código Azure CLI são comandos que preparam os dados de entrada exigidos pelo trabalho. Reveja as secções para compreender o código.

1. Crie um Hub IoT utilizando o [hub az iot criar](../iot-hub/iot-hub-create-using-cli.md#create-an-iot-hub) comando. Este exemplo cria um hub IoT chamado **MyASAIoTHub** . Como os nomes do IoT Hub são únicos, tens de inventar o teu próprio nome IoT Hub. Desa estama o SKU para F1 para utilizar o nível livre se estiver disponível com a sua subscrição. Caso contrário, escolha o próximo nível mais baixo.

    ```azurecli
    az iot hub create --name "MyASAIoTHub" --resource-group streamanalyticsrg --sku S1
    ```

    Uma vez criado o hub IoT, obtenha a cadeia de ligação IoT Hub utilizando o comando [az iot hub show-connection-string.](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest) Copie toda a cadeia de ligação e guarde-a para quando adicionar o Hub IoT como entrada no seu trabalho stream Analytics.

    ```azurecli
    az iot hub show-connection-string --hub-name "MyASAIoTHub"
    ```

2. Adicione um dispositivo ao IoT Hub utilizando o comando [de criação de identidade de dispositivo az iothub.](../iot-hub/quickstart-send-telemetry-c.md#register-a-device) Este exemplo cria um dispositivo chamado **MyASAIoTDevice** .

    ```azurecli
    az iot hub device-identity create --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice"
    ```

3. Obtenha a cadeia de ligação do dispositivo utilizando o comando [az iot hub-identidade de programa-ligação de ligação.](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-show-connection-string) Copie toda a cadeia de ligação e guarde-a para quando criar o simulador Raspberry Pi.

    ```azurecli
    az iot hub device-identity show-connection-string --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice" --output table
    ```

    **Exemplo de saída:**

    ```output
    HostName=MyASAIoTHub.azure-devices.net;DeviceId=MyASAIoTDevice;SharedAccessKey=a2mnUsg52+NIgYudxYYUNXI67r0JmNubmfVafojG8=
    ```

## <a name="create-a-blob-storage-account"></a>Criar uma conta de armazenamento blob

Os blocos de código Azure CLI seguintes criam uma conta de armazenamento de bolhas que é usada para a saída de trabalho. Reveja as secções para compreender o código.

1. Crie uma conta de armazenamento para fins gerais com o comando [az storage account create](/cli/azure/storage/account). A conta de armazenamento para fins gerais pode ser utilizada para os quatro serviços: blobs, ficheiros, tabelas e filas.

   Lembre-se de substituir os valores do espaço reservado nos suportes angulares com os seus próprios valores:

   ```azurecli
   az storage account create \
       --name <storage-account> \
       --resource-group streamanalyticsrg \
       --location eastus \
       --sku Standard_ZRS \
       --encryption-services blob
   ```

2. Obtenha a chave para a sua conta de armazenamento executando o comando da [lista de chaves de armazenamento az.](/cli/azure/storage/account/keys) Guarde esta chave para usar no próximo passo.

   ```azurecli
   az storage account keys list -g streamanalyticsrg -n <storage-account>
   ```

3. Crie um contentor para armazenar blobs com o comando [az storage container create](/cli/azure/storage/container). Utilize a chave da conta de armazenamento para autorizar a operação para criar o recipiente. Para obter mais informações sobre a autorização de operações de dados com o Azure CLI, consulte [Autorizar o acesso aos dados de blob ou fila com o Azure CLI](/azure/storage/common/authorize-data-operations-cli).

   ```azurecli
   az storage container create \
       --account-name <storage-account> \
       --name sample-container \
       --account-key <key>
       --auth-mode key
   ```

## <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics

Os seguintes blocos de código Azure CLI criam um trabalho stream Analytics. Reveja as secções para entender o código

1. Crie um trabalho stream Analytics com o [trabalho de análise de stream-analytics az criar](/cli/azure/ext/stream-analytics/stream-analytics/job?view=azure-cli-latest#ext-stream-analytics-az-stream-analytics-job-create) comando.

```azurecli
az stream-analytics job create \
    --resource-group streamanalyticsrg 
    --name streamanalyticsjob \
    --location eastus \
    --output-error-policy "Drop" \
    --events-outoforder-policy "Drop" \
    --events-outoforder-max-delay 5 \
    --events-late-arrival-max-delay 16 \     
    --data-locale "en-US"
```

## <a name="configure-input-to-the-job"></a>Configurar a entrada da tarefa

Adicione uma entrada ao seu trabalho utilizando o cmdlet [de entrada az stream-analytics.](/cli/azure/ext/stream-analytics/stream-analytics/input?view=azure-cli-latest#ext-stream-analytics-az-stream-analytics-input-create) Este cmdlet utiliza o nome da tarefa, o nome da entrada da tarefa, o nome do grupo de recursos e a definição de entrada da tarefa como parâmetros. A definição de entrada de trabalho é um ficheiro JSON que contém as propriedades necessárias para configurar a entrada do trabalho. Neste exemplo, você vai criar um Hub IoT como uma entrada.

No computador local, crie um ficheiro denominado `datasource.json` e adicione os seguintes dados JSON ao mesmo. Certifique-se de que substitui o valor pela `sharedAccessPolicyKey` parte da cadeia de `SharedAccessKey` ligação IoT Hub que guardou numa secção anterior.

```json
{
    "type": "Microsoft.Devices/IotHubs",
    "properties": {
        "iotHubNamespace": "iothub",
        "sharedAccessPolicyName": "iothubowner",
        "sharedAccessPolicyKey": "sharedAccessPolicyKey=",
        "consumerGroupName": "sdkconsumergroup",
        "endpoint": "messages/events"
    }
}
```

No computador local, crie um ficheiro denominado `serialization.json` e adicione os seguintes dados JSON ao mesmo.

```json
{
     "type": "Json",
     "properties": {
         "encoding": "UTF8"
     }
}
```

Em seguida, execute o cmdlet `az stream-analytics input create`. Certifique-se de substituir o valor da `datasource` variável pelo caminho onde guardou a definição de entrada de trabalho ficheiro JSON, e o valor da `serialization` variável pelo caminho onde guardou o ficheiro JSON de serialização.

```azurecli
az stream-analytics input create 
    --resource-group streamanalyticsrg 
    --job-name streamanalyticsjob \
    --name asaiotinput \
    --type Stream \
    --datasource datasource.json \
    --serialization serialization.json
```

## <a name="configure-output-to-the-job"></a>Configurar a saída da tarefa

Adicione uma saída ao seu trabalho utilizando a [saída az stream-analytics criar](/cli/azure/ext/stream-analytics/stream-analytics/output?view=azure-cli-latest#ext-stream-analytics-az-stream-analytics-output-create) cmdlet. Este cmdlet utiliza o nome da tarefa, o nome da saída da tarefa, o nome do grupo de recursos e a definição de saída da tarefa como parâmetros. A definição de saída de trabalho é um ficheiro JSON que contém as propriedades necessárias para configurar a produção do trabalho. Este exemplo utiliza o armazenamento de blobs como saída.

No computador local, crie um ficheiro denominado `datasink.json` e adicione os seguintes dados JSON ao mesmo. Certifique-se de que substitui o valor pela `accountKey` chave de acesso da sua conta de armazenamento que é o valor armazenado em $storageAccountKey valor.

```json
{
    "type": "Microsoft.Storage/Blob",
    "properties": {
        "storageAccounts": [
            {
                "accountName": "<storage-account>",
                "accountKey": "accountKey=="
            }
        ],
        "container": "state",
        "pathPattern": "{date}/{time}",
        "dateFormat": "yyyy/MM/dd",
        "timeFormat": "HH"
    }
}
```

Em seguida, execute o cmdlet `az stream-analytics output`. Certifique-se de substituir o valor da `datasource` variável pelo caminho onde guardou o ficheiro JSON de definição de saída de trabalho e o valor da `serialization` variável pelo caminho onde guardou o ficheiro JSON de serialização.

```azurecli
az stream-analytics output create 
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name asabloboutput \
    --datasource datasink.json \
    --serialization serialization.json
```

## <a name="define-the-transformation-query"></a>Definir a consulta de transformação

Adicione uma transformação ao seu trabalho usando a [transformação az stream-analytics criar](/cli/azure/ext/stream-analytics/stream-analytics/transformation?view=azure-cli-latest#ext-stream-analytics-az-stream-analytics-transformation-create) cmdlet. Este cmdlet utiliza o nome da tarefa, o nome da transformação da tarefa, o nome do grupo de recursos e a definição de transformação da tarefa como parâmetros. 

Executar o `az stream-analytics transformation create` cmdlet.

```azurecli
az stream-analytics transformation create 
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name Transformation \
    --streaming-units "6" \
    --transformation-query "SELECT * INTO asabloboutput FROM asaiotinput HAVING Temperature > 27"
```
## <a name="run-the-iot-simulator"></a>Executar o simulador IoT

1. Abra o [Raspberry Pi Azure IoT Online Simulator](https://azure-samples.github.io/raspberry-pi-web-simulator/).

2. Substitua o espaço reservado na Linha 15 por toda a cadeia de ligação Azure IoT Hub Device que guardou numa secção anterior.

3. Clique em **Run** (Executar). A saída deve mostrar os dados do sensor e as mensagens que estão a ser enviadas para o seu Hub IoT.

    ![Simulador Online do Azure IoT Raspberry Pi](./media/stream-analytics-quick-create-powershell/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Iniciar a tarefa do Stream Analytics e verificar a saída

Inicie o trabalho utilizando o [trabalho de arranque de az stream-analytics](/cli/azure/ext/stream-analytics/stream-analytics/job?view=azure-cli-latest#ext-stream-analytics-az-stream-analytics-job-start) cmdlet. Este cmdlet utiliza o nome da tarefa, o nome do grupo de recursos, o modo de início da saída e a hora de início como parâmetros. `OutputStartMode` aceita os valores `JobStartTime``CustomTime` ou `LastOutputEventTime`.

Depois de executar o cmdlet seguinte, devolve `True` como a saída, se o trabalho for iniciado. No contentor de armazenamento, é criada uma pasta de saída com os dados transformados.

```azurecli
az stream-analytics job start 
    --resource-group streamanalyticsrg \
    --name streamanalyticsjob \
    --output-start-mode JobStartTime
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos, a tarefa de transmissão em fluxo e todos os recursos relacionados. A eliminação da tarefa evita a faturação das unidades de transmissão em fluxo consumidas pela tarefa. Se estiver a planear utilizar o trabalho no futuro, pode ignorar a eliminação e parar o trabalho por agora. Se não continuar a utilizar este trabalho, elimine todos os recursos criados por este arranque rápido executando o seguinte cmdlet:

```powershell
az group delete \
    --name streamanalyticsrg \
    --no-wait
```

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você implementou um simples trabalho stream Analytics usando Azure CLI. Também pode implementar tarefas do Stream Analytics com o [portal do Azure](stream-analytics-quick-create-portal.md) e o [Visual Studio](stream-analytics-quick-create-vs.md).

Para saber mais sobre como configurar outras origens de entrada e efetuar a deteção em tempo real, avance para o seguinte artigo:

> [!div class="nextstepaction"]
> [Deteção de fraudes em tempo real com o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
