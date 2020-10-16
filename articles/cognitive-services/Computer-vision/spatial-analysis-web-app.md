---
title: Implementar uma aplicação web de Análise Espacial
titleSuffix: Azure Cognitive Services
description: Aprenda a usar a Análise Espacial numa aplicação web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: aahi
ms.openlocfilehash: 8032c3607dd74cddbaa5fd6690a95ebdf218809a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91628199"
---
# <a name="how-to-deploy-a-people-counting-web-application"></a>Como: Implementar uma aplicação web people Counting

Use este artigo para aprender a integrar a análise espacial numa aplicação web que compreende o movimento das pessoas e monitoriza o número de pessoas que ocupam um espaço físico. 

Neste tutorial, vai aprender a:

* Implantar o recipiente de análise espacial
* Configure a operação e a câmara
* Configure a ligação IoT Hub na Aplicação Web
* Implementar e testar a Aplicação Web

## <a name="prerequisites"></a>Pré-requisitos

* Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Compreensão básica das configurações de implementação do Azure IoT Edge e um [Hub IoT Azure](https://docs.microsoft.com/azure/iot-hub/)
* Um [computador de anfitrião](spatial-analysis-container.md)configurado.

## <a name="deploy-the-spatial-analysis-container"></a>Implantar o recipiente de análise espacial

Preencha o [pedido de pedido](https://aka.ms/csgate) para ter acesso ao funcionamento do contentor. 

Siga [a configuração do computador anfitrião](./spatial-analysis-container.md) para configurar o computador anfitrião e ligue um dispositivo IoT Edge ao Azure IoT Hub. 

### <a name="deploy-an-azure-iot-hub-service-in-your-subscription"></a>Implementar um serviço Azure IoT Hub na sua subscrição

Em primeiro lugar, crie um serviço Azure IoT Hub com o Standard Pricing Tier (S1) ou Free Tier (S0). Siga estas instruções para criar este caso utilizando o Azure CLI.

Preencha os parâmetros necessários:
* Subscrição: O nome ou ID da sua Subscrição Azure
* Grupo de recursos: Crie um nome para o seu grupo de recursos
* Iot Hub Name: Crie um nome para o seu IoT Hub
* Nome IoTHub: O nome do IoT Hub que criou 
* Nome do dispositivo de borda: Crie um nome para o seu dispositivo edge

```azurecli
az login
az account set --subscription <name or ID of Azure Subscription>
az group create --name "<Resource Group Name>" --location "WestUS"

az iot hub create --name "<IoT Hub Name>" --sku S1 --resource-group "test-resource-group"

az iot hub device-identity create --hub-name "<IoT Hub Name>" --device-id "<Edge Device Name>" --edge-enabled
```

### <a name="deploy-the-container-on-azure-iot-edge-on-the-host-computer"></a>Desloque o recipiente no Azure IoT Edge no computador anfitrião

Desloque o recipiente de análise espacial como módulo IoT no computador anfitrião, utilizando o CLI Azure. O processo de implantação requer um ficheiro manifesto de implantação que delineie os recipientes, variáveis e configurações necessários para a sua implantação. Pode encontrar um [manifesto de implantação de](https://github.com/Azure-Samples/cognitive-services-rest-api-samples/) amostras no GitHub, que inclui uma configuração básica de implantação para o recipiente de análise *espacial.* 

> [!NOTE] 
> Os *recipientes* *de diagnóstico de análise espacial-telegraf* e de análise espacial são opcionais. Pode decidir removê-los do *DeploymentManifest.jsarquivado.* Para mais informações consulte o artigo [de telemetria e resolução de problemas.](./spatial-analysis-logging.md) Pode encontrar uma amostra *DeploymentManifest.jsno* ficheiro [em Github](https://go.microsoft.com/fwlink/?linkid=2142179) 

### <a name="set-environment-variables"></a>Definir variáveis de ambiente

A maioria das **Variáveis Ambientais** para o Módulo de Borda IoT já estão definidas na amostra *DeploymentManifest.jsno* ficheiro ligado acima. No ficheiro, procure as `BILLING_ENDPOINT` variáveis e `API_KEY` ambientais, mostradas abaixo. Substitua os valores pela Endpoint URI e pela Chave API que criou anteriormente. Certifique-se de que o valor EULA está definido para "aceitar". 

```json
"EULA": { 
    "value": "accept"
},

"BILLING_ENDPOINT":{ 
    "value": "<Use a key from your Computer Vision resource>"
},
"API_KEY":{
    "value": "<Use the endpoint from your Computer Vision resource>"
}
```

### <a name="configure-the-operation-parameters"></a>Configurar os parâmetros de operação

Agora que a configuração inicial do recipiente *de análise espacial* está completa, o próximo passo é configurar os parâmetros de operações e adicioná-los à implantação. 

O primeiro passo é atualizar o [manifesto de implantação](https://go.microsoft.com/fwlink/?linkid=2142179) da amostra e configurar a operaçãoId para `cognitiveservices.vision.spatialanalysis-personcount` os indicados abaixo:


```json
"personcount": {
    "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "<Replace RTSP URL here>",
        "VIDEO_SOURCE_ID": "<Replace with friendly name>",
        "VIDEO_IS_LIVE":true,
        "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0 }",
        "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[<Replace with your values>], \"events\": [{\"type\":\"count\"}], \"threshold\":<use 0 for no threshold.}]}"
    }
},
```

Depois de atualizado o [manifesto de implantação,](https://go.microsoft.com/fwlink/?linkid=2142179) siga as instruções do fabricante da câmara para instalar a câmara, configurar o url da câmara e configurar o nome de utilizador e a palavra-passe. 

Em seguida, coloque `VIDEO_URL` a url RTSP da câmara e as credenciais para ligar à câmara.

Se o dispositivo de borda tiver mais de uma GPU, selecione a GPU para executar esta operação. Certifique-se de que carrega o equilíbrio das operações onde não há mais de 8 operações em execução numa única GPU de cada vez.  

Em seguida, configure a zona em que quer contar as pessoas. Para configurar o polígono da zona, siga primeiro as instruções do fabricante para recuperar uma moldura da câmara. Para determinar cada vértice do polígono, selecione um ponto no quadro, pegue nas coordenadas x,y pixel do ponto relativo à esquerda, canto superior do quadro, e divida pelas dimensões do quadro correspondentes. Desa parte dos resultados como x,y coordenadas do vértice. Pode definir a configuração do polígono de zona no `SPACEANALYTICS_CONFIG` campo.

Esta é uma moldura de vídeo de amostra que mostra como as coordenadas do vértice estão a ser calculadas para um quadro de tamanho 1920/1080.
![Quadro de vídeo de amostra](./media/spatial-analysis/sample-video-frame.jpg)

Também pode selecionar um limiar de confiança para quando as pessoas detetadas são contadas e os eventos são gerados. Desa um limite para 0, se quiser que todos os eventos sejam de saída.

### <a name="execute-the-deployment"></a>Executar a implantação

Agora que o [manifesto de implantação](https://go.microsoft.com/fwlink/?linkid=2142179) está completo, utilize este comando no CLI Azure para implantar o recipiente no computador anfitrião como módulo IoT Edge.

```azurecli
az login
az extension add --name azure-iot
az iot edge set-modules --hub-name "<IoT Hub name>" --device-id "<IoT Edge device name>" --content DeploymentManifest.json -–subscription "<subscriptionId>"
```

Preencha os parâmetros necessários:

* IoT Hub Name: Your Azure IoT Hub name
* DeploymentManifest.jsem: O nome do seu ficheiro de implantação
* Nome do dispositivo IoT Edge: O nome do dispositivo IoT Edge do seu computador anfitrião
* Subscrição: O seu ID de subscrição ou nome

Este comando iniciará a implantação e poderá visualizar o estado de implantação no seu exemplo Azure IoT Hub no portal Azure. O estado pode mostrar como *417 – A configuração de implementação do dispositivo não é definida* até que o dispositivo termine de descarregar as imagens do contentor e comece a funcionar.

### <a name="validate-that-the-deployment-was-successful"></a>Validar que a implantação foi bem sucedida

Localizar o Estado de *execução* nas Definições do Módulo de Borda IoT para o módulo de análise espacial no seu exemplo IoT Hub no portal Azure. O **Valor Desejado** e o Valor **Reportado** para o Estado *de Execução* devem dizer `Running` . Veja abaixo como será este no portal Azure.

![Verificação de implementação de exemplo](./media/spatial-analysis/deployment-verification.png)

Neste momento, o contentor de análise espacial está a executar a operação. Emite insights de IA para a `cognitiveservices.vision.spatialanalysis-personcount` operação e encaminha estas insights como telemetria para o seu exemplo Azure IoT Hub. Para configurar câmaras adicionais, pode atualizar o ficheiro [manifesto de implementação](https://go.microsoft.com/fwlink/?linkid=2142179) e executar novamente a implementação.

## <a name="person-counting-web-application"></a>Aplicação web de contagem de pessoas

Esta aplicação web contando com esta pessoa permite-lhe configurar rapidamente uma aplicação web de amostra e acolhê-la no seu ambiente Azure.

### <a name="get-the-person-counting-app-container"></a>Obtenha o recipiente de aplicação de contagem de pessoas

Uma forma de recipiente desta aplicação disponível no Registo do Contentor Azure. Use o seguinte comando de puxar o estivador para o descarregar. Contacte a Microsoft projectarchon@microsoft.com para obter o token de acesso.

```bash
docker login rtvsofficial.azurecr.io -u <token name> -p <password>
docker pull rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0
```

Empurre o recipiente para o registo do seu contentor Azure (ACR).

```bash
az acr login --name <your ACR name>

docker tag rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0 [desired local image name]

docker push [desired local image name]
```

Para instalar o recipiente, crie uma nova App Web Azure para contentores e preencha os parâmetros necessários. Em seguida, vá ao **separador Docker** e selecione **Single Container**, em seguida, Registo do **Contentor Azure**. Use o seu exemplo de Registo de Contentores Azure onde empurrou a imagem acima.

![Insira detalhes de imagem](./media/spatial-analysis/solution-app-create-screen.png)

Depois de introduzir os parâmetros acima, clique em **Review+Create** e crie a app.

### <a name="configure-the-app"></a>Configurar a aplicação 

Aguarde a conclusão da configuração e navegue para o seu recurso no portal Azure. Aceda à secção **de configuração** e adicione as **duas seguintes definições de aplicação**.

* `EventHubConsumerGroup` – O nome de corda do grupo de consumidores do seu Azure IoT Hub, pode criar um novo grupo de consumidores no seu IoT Hub ou utilizar o grupo padrão. 
* `IotHubConnectionString` – O fio de ligação ao seu Azure IoT Hub, este pode ser recuperado a partir da secção de teclas do seu recurso Azure IoT Hub ![ Configure Parâmetros de configuração](./media/spatial-analysis/solution-app-config-page.png)

Assim que estas 2 definições forem adicionadas, clique em **Guardar**. Em seguida, clique em **Autenticação/Autorização** no menu de navegação à esquerda e atualize-o com o nível de autenticação pretendido. Recomendamos o expresso do Azure Ative Diretor (Azure AD). 

### <a name="test-the-app"></a>Testar a aplicação

Vá à App Azure Web e verifique se a implementação foi bem sucedida, e a aplicação web está em execução. Navegue para o url configurado: `<yourapp>.azurewebsites.net` para ver a aplicação de execução.

![Testar a implementação](./media/spatial-analysis/solution-app-output.png)

## <a name="next-steps"></a>Passos seguintes

* [Configure operações de análise espacial](./spatial-analysis-operations.md)
* [Registo e resolução de problemas](spatial-analysis-logging.md)
* [Guia de colocação de câmera](spatial-analysis-camera-placement.md)
* [Guia de colocação de zona e linha](spatial-analysis-zone-line-placement.md)
