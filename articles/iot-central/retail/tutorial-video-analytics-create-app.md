---
title: Tutorial - Criar uma análise de vídeo - aplicação de deteção de objetos e movimentos no Azure IoT Central
description: Este tutorial mostra como criar uma aplicação de análise de vídeo na IoT Central. Cria-se, personaliza-o e liga-se a outros serviços Azure.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 07/31/2020
ms.openlocfilehash: c7ddff70d24fe87380c4bf1439811042418ac76a
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91369574"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central"></a>Tutorial: Criar uma aplicação de vídeo-análise - aplicação de deteção de objetos e movimentos no Azure IoT Central

Como construtor de soluções, aprenda a criar uma aplicação de análise de vídeo com a análise de vídeo IoT Central - modelo de aplicação *de deteção de objetos e movimentos,* dispositivos Azure IoT Edge e Azure Media Services. A solução utiliza uma loja de retalho para mostrar como atender à necessidade de monitorizar as câmaras de segurança. A solução utiliza a deteção automática de objetos num feed de vídeo para identificar e localizar rapidamente eventos interessantes.

A aplicação da amostra inclui dois dispositivos simulados e um gateway IoT Edge. Os seguintes tutoriais mostram duas abordagens para experimentar e compreender as capacidades do portal:

* Crie o gateway IoT Edge num Azure VM e ligue uma câmara simulada.
* Crie o gateway IoT Edge num dispositivo real, como um Intel NUC e conecte uma câmara real.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Utilize o modelo de aplicação de análise de vídeo Azure IoT Central para criar uma aplicação de loja de retalho
> * Personalize as definições de aplicação
> * Crie um modelo de dispositivo para um dispositivo de gateway IoT Edge
> * Adicione um dispositivo de gateway à sua aplicação IoT Central

## <a name="prerequisites"></a>Pré-requisitos

Para completar esta série tutorial, precisa:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode criar uma na página de inscrição do [Azure](https://aka.ms/createazuresubscription).
* Se estiver a utilizar uma câmara real, precisa de conectividade entre o dispositivo IoT Edge e a câmara, e precisa do canal **de Protocolo de Streaming em Tempo Real.**

## <a name="initial-setup"></a>Configuração inicial

Nestes tutoriais, atualiza-se e utiliza vários ficheiros de configuração. As versões iniciais destes ficheiros estão disponíveis no repositório [GitHub de gateway LVA.](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway) O repositório também inclui um ficheiro de texto de scratchpad para que possa descarregar e utilizar para registar valores de configuração a partir dos serviços que implementa.

Crie uma pasta chamada *configuração de Lva* na sua máquina local para guardar cópias destes ficheiros. Em seguida, clique com o botão direito em cada um dos seguintes links e escolha **Guardar como** guardar o ficheiro na pasta *de configuração de Iva:*

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt)
- [deployment.amd64.js](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.amd64.json)
- [LvaEdgeGatewayDcm.js](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.js](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json)

> [!NOTE]
> O repositório GitHub também inclui o código fonte para os módulos **LvaEdgeGatewayModule** e **LvaYolov3** IoT Edge. Para obter mais informações sobre o trabalho com o código fonte, consulte os [Módulos de Gateway Build the LVA](tutorial-video-analytics-build-module.md).

## <a name="deploy-and-configure-azure-media-services"></a>Implementar e configurar serviços de mídia Azure

A solução utiliza uma conta Azure Media Services para armazenar os videoclips de deteção de objetos feitos pelo dispositivo IoT Edge gateway.

Quando cria a conta serviços de comunicação:

- Você precisa fornecer um nome de conta, uma subscrição Azure, uma localização, um grupo de recursos e uma conta de armazenamento. Crie uma nova conta de armazenamento utilizando as definições predefinidos enquanto está a criar a conta De Serviços de Mídia.

- Escolha a região **leste dos EUA** para a localização.

- Criar um novo grupo de recursos chamado *lva-rg* na região **leste dos EUA** para os Serviços de Media e contas de armazenamento. Quando termina os tutoriais, é fácil remover todos os recursos eliminando o grupo de recursos *Lva-rg.*

Criar a [conta dos Serviços de Comunicação social no portal Azure.](https://portal.azure.com/?r=1#create/Microsoft.MediaService)

> [!TIP]
> Estes tutoriais usam a região **leste dos EUA** em todos os exemplos. Pode usar a região mais próxima, se preferir.

Tome nota do nome da sua conta **media** no ficheiro *scratchpad.txt.*

Quando a implementação estiver concluída, navegue na página **Propriedades** para a sua conta **De Serviços de Mídia.** Tome nota do **ID** de recurso no ficheiro *scratchpad.txt, * utilize este valor mais tarde quando configurar o módulo IoT Edge.

Em seguida, configurar um diretor de serviço Azure Ative Para o seu recurso De Serviços de Mídia. Selecione **o acesso a API** e, em **seguida, a autenticação principal do serviço.** Crie uma nova aplicação Azure Ative Directory com o mesmo nome que o seu recurso Media Services e crie um segredo com uma descrição *IoT Edge Access*.

:::image type="content" source="./media/tutorial-video-analytics-create-app/media-service-authentication.png" alt-text="Configure a aplicação AAD para AMS":::

Quando o segredo for gerado, desloque-se até à **Cópia das suas credenciais para ligar a secção de aplicação principal do seu serviço.** Em seguida, selecione **JSON**. Pode copiar todas as informações credenciais daqui de uma vez. Tome nota destas informações no ficheiro *scratchpad.txt, * utilize-as mais tarde quando configurar o dispositivo IoT Edge.

> [!WARNING]
> Esta é a sua única oportunidade de ver e salvar o segredo. Se o perderes, tens de gerar outro segredo.

## <a name="create-the-azure-iot-central-application"></a>Criar a aplicação Azure IoT Central

Nesta secção, você cria uma nova aplicação Azure IoT Central a partir de um modelo. Você usa esta aplicação ao longo da série tutorial para construir uma solução completa.

Para criar uma nova aplicação do Azure IoT Central:

1. Navegue para o site do [gestor de aplicações Azure IoT Central.](https://aka.ms/iotcentral)

1. Faça o sômis com as credenciais que utiliza para aceder à sua assinatura Azure.

1. Para começar a criar uma nova aplicação Azure IoT Central, selecione **Nova Aplicação** na página **Build.**

1. Selecione **Varejo**. A página de varejo apresenta vários modelos de aplicações de varejo.

Para criar uma nova aplicação de análise de vídeo:

1. Selecione o **modelo de aplicação de deteção de objetos e movimentos** de vídeo. Este modelo inclui modelos de dispositivo para os dispositivos utilizados no tutorial. O modelo inclui painéis de amostra que os operadores podem usar para executar tarefas como monitorizar e gerir câmaras.

1. Opcionalmente, escolha um nome de **aplicação**amigável. Esta aplicação baseia-se numa loja fictícia chamada Northwind Traders. O tutorial usa o **nome de aplicação** *Northwind Traders video analytics*.

    > [!NOTE]
    > Se utilizar um nome de **aplicação**amigável, ainda deve utilizar um valor único para o **URL**da aplicação.

1. Se tiver uma subscrição Azure, selecione o seu **Diretório,** **subscrição Azure**e **Estados Unidos** como **Localização**. Se não tiver uma subscrição, pode ativar um **teste gratuito de 7 dias** e completar as informações de contacto necessárias. Este tutorial utiliza três dispositivos - duas câmaras e um dispositivo IoT Edge - por isso, se não utilizar o teste gratuito, será cobrado para uso.

    Para obter mais informações sobre diretórios, subscrições e locais, consulte a [criação de um quickstart de aplicação](../core/quick-deploy-iot-central.md).

1. Selecione **Criar**.

    :::image type="content" source="./media/tutorial-video-analytics-create-app/new-application.png" alt-text="Página da Aplicação do Azure IoT Central":::

### <a name="retrieve-the-configuration-data"></a>Recuperar os dados de configuração

Mais tarde neste tutorial, quando configurar o gateway IoT Edge, precisa de alguns dados de configuração da aplicação IoT Central. O dispositivo IoT Edge necessita desta informação para registar e ligar à aplicação.

Na secção **Administração,** selecione **a sua aplicação** e tome nota do URL de **aplicação** e do ID de **aplicação** no ficheiro *scratchpad.txt:*

:::image type="content" source="./media/tutorial-video-analytics-create-app/administration.png" alt-text="O Screenshot mostra o painel de administração de uma página de análise de vídeo com a aplicação U R L e a aplicação I D em destaque.":::

Selecione **Tokens API** e gere um novo token chamado **LVAEdgeToken** para o papel **de Operador:**

:::image type="content" source="./media/tutorial-video-analytics-create-app/token.png" alt-text="Gerar Token":::

Tome nota do token no ficheiro *scratchpad.txt* para mais tarde. Depois do diálogo fechar, não pode voltar a ver o símbolo.

Na secção **Administração,** selecione **a ligação do dispositivo**e, em seguida, selecione **dispositivos SAS-IoT**.

Tome nota da **chave primária** para dispositivos no ficheiro *scratchpad.txt.* Você usa este *símbolo de assinatura de acesso compartilhado* do grupo primário mais tarde quando configurar o dispositivo IoT Edge.

## <a name="edit-the-deployment-manifest"></a>Editar o manifesto de implantação

Você implanta um módulo IoT Edge usando um manifesto de implantação. No IoT Central pode importar o manifesto como modelo de dispositivo e, em seguida, deixar a IoT Central gerir a implementação do módulo.

Para preparar o manifesto de implantação:

1. Abra a *deployment.amd64.jsno* ficheiro, que guardou na pasta *de configuração de Iva,* utilizando um editor de texto.

1. Encontre as `LvaEdgeGatewayModule` definições e altere o nome da imagem como mostrado no seguinte corte:

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. Adicione o nome da sua conta de Media Services no `env` nó na `LvaEdgeGatewayModule` secção. Fez uma nota deste nome da conta no ficheiro *scratchpad.txt:*

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_ACCOUNT_NAME>"
        }
    }
    ```

1. O modelo não expõe estas propriedades no IoT Central, pelo que é necessário adicionar os valores de configuração dos Serviços de Mídia ao manifesto de implantação. Localize o `lvaEdge` módulo e substitua os espaços reservados pelos valores que fez no ficheiro *scratchpad.txt* quando criou a sua conta de Serviços de Comunicação Social.

    O `azureMediaServicesArmId` **ID de recursos** que fez uma nota no ficheiro *scratchpad.txt* quando criou a conta dos Serviços de Comunicação Social.

    Fez uma nota do `aadTenantId` ficheiro , e no ficheiro `aadServicePrincipalAppId`scratchpad.txtquando criou o principal serviço para a sua conta de Media `aadServicePrincipalSecret` Services: *scratchpad.txt*

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. Guarde as alterações.

Opcionalmente, pode substituir o módulo Yolov3 pelo [OpenVINO Model Server – Módulo de &trade; Extensão Edge AI](https://github.com/openvinotoolkit/model_server/tree/ams/extras/ams_wrapper) otimizado da Intel. Você pode baixar um manifesto de implementação [ de amostrasdeployment.openvino.amd64.jsem](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.openvino.amd64.json). Este manifesto substitui as definições de configuração do `lvaYolov3` módulo pela seguinte configuração:

```json
"OpenVINOModelServerEdgeAIExtensionModule": {
    "settings": {
        "image": "marketplace.azurecr.io/intel_corporation/open_vino",
        "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"4000/tcp\":[{\"HostPort\":\"4000\"}]}},\"Cmd\":[\"/ams_wrapper/start_ams.py\",\"--ams_port=4000\",\"--ovms_port=9000\"]}"
    },
    "type": "docker",
    "version": "1.0",
    "status": "running",
    "restartPolicy": "always"
}
```

## <a name="create-the-azure-iot-edge-gateway-device"></a>Crie o dispositivo de gateway Azure IoT Edge

A análise de vídeo - aplicação de deteção de objetos e movimentos inclui um modelo de detetor **de objetos de borda LVA** e um modelo de **dispositivo de deteção de movimento de borda LVA.** Nesta secção, cria-se um modelo de dispositivo de gateway utilizando o manifesto de implantação e adiciona o dispositivo gateway à sua aplicação IoT Central.

### <a name="create-a-device-template-for-the-lva-edge-gateway"></a>Crie um modelo de dispositivo para o Gateway LVA Edge

Para importar o manifesto de implantação e criar o modelo do dispositivo **LVA Edge Gateway:**

1. Na sua aplicação IoT Central, navegue para **modelos de dispositivo,** e selecione **+ Novo**.

1. Na página **de tipo de modelo Select,** selecione o azulejo **Azure IoT Edge.** Em seguida, **selecione Seguinte: Personalize**.

1. Na página manifesto de **implementação do Azure IoT Edge,** insira o *LVA Edge Gateway* como o nome do modelo e verifique o **dispositivo Gateway com dispositivos a jusante**.

    Não navegue pelo manifesto de implantação ainda. Se o fizer, o assistente de implementação espera uma interface para cada módulo, mas só precisa de expor a interface para o **LvaEdgeGatewayModule**. Faça o upload do manifesto num passo posterior.

    :::image type="content" source="./media/tutorial-video-analytics-create-app/upload-deployment-manifest.png" alt-text="Não carre deixe de carregar o manifesto de implantação":::

    Selecione **Seguinte: Revisão**.

1. Na página **'Revisão',** **selecione Criar**.

### <a name="import-the-device-capability-model"></a>Importar o modelo de capacidade do dispositivo

O modelo do dispositivo deve incluir um modelo de capacidade do dispositivo. Na página **LVA Edge Gateway,** selecione o **azulejo do modelo de capacidade de importação.** Navegue para a pasta *de configuração de Iva* que criou anteriormente e selecione as *LvaEdgeGatewayDcm.jsno* ficheiro.

O modelo do dispositivo **LVA Edge Gateway** inclui agora o **Módulo LVA Edge Gateway** juntamente com três interfaces: **Informações do dispositivo,** **Definições de Gateway de Borda LVA**e **Interface de Gateway de Borda LVA**.

### <a name="replace-the-manifest"></a>Substitua o manifesto

Na página **LVA Edge Gateway,** selecione **+ Substitua o manifesto**.

:::image type="content" source="./media/tutorial-video-analytics-create-app/replace-manifest.png" alt-text="Substituir Manifesto":::

Navegue na pasta *de configuração Lva* e selecione a *deployment.amd64.jsno* ficheiro manifesto que editou anteriormente. Selecione **Carregar**. Quando a validação estiver concluída, selecione **Substitua.**

### <a name="add-relationships"></a>Adicionar relacionamentos

No modelo do dispositivo **LVA Edge Gateway,** em **Módulos/Módulo gateway de borda LVA,** selecione **Relacionamentos**. Selecione **+ Adicione a relação** e adicione as seguintes duas relações:

|Nome a Apresentar               |Name          |Destino |
|-------------------------- |------------- |------ |
|Detetor de movimento lVA Edge   |Utilizar predefinição   |Dispositivo de detetor de movimento de borda LVA |
|Detetor de Objetos de Borda LVA   |Utilizar predefinição   |Dispositivo de detetor de objetos de borda LVA |

Em seguida, selecione **Guardar**.

:::image type="content" source="media/tutorial-video-analytics-create-app/relationships.png" alt-text="Adicionar relacionamentos":::

### <a name="add-views"></a>Adicionar vistas

O modelo do dispositivo **LVA Edge Gateway** não inclui definições de visualização.

Para adicionar uma vista ao modelo do dispositivo:

1. No modelo do dispositivo **LVA Edge Gateway,** navegue para **Visualizações** e **selecione o azulejo do dispositivo visualizando.**

1. Introduza o *dispositivo LVA Edge Gateway* como o nome de visualização.

1. Adicione os seguintes azulejos à vista:

    * Um azulejo com as propriedades de **Info dispositivo:** **Modelo de dispositivo,** **fabricante,** **sistema operativo,** **arquitetura de processador,** **versão de software,** **memória total**e **armazenamento total**.
    * Um gráfico de linha com os **valores de telemetria de memória livre** e de **batimentos cardíacos** do sistema.
    * Um azulejo histórico do evento com os seguintes eventos: **Criar Câmara,** **Eliminar Câmara,** **Reiniciar módulos,** **Módulo Iniciado,** **Módulo parado**.
    * Um último azulejo de valor conhecido 2x1 mostrando a telemetria **do Estado cliente central IoT.**
    * Um último azulejo de valor conhecido 2x1 mostrando a telemetria **do Estado do Módulo.**
    * Um último azulejo de valor conhecido 1x1 mostrando a telemetria **do batimento cardíaco do sistema.**
    * Um último azulejo de valor conhecido 1x1 mostrando a telemetria **das câmaras conectadas.**

    :::image type="content" source="media/tutorial-video-analytics-create-app/gateway-dashboard.png" alt-text="Dashboard":::

1. Selecione **Guardar**.

### <a name="publish-the-device-template"></a>Publicar o modelo de dispositivo

Antes de poder adicionar um dispositivo à aplicação, tem de publicar o modelo do dispositivo:

1. No modelo do dispositivo **LVA Edge Gateway,** selecione **Publicar**.

1. No modelo de publicação deste dispositivo para a página **de aplicação,** selecione **Publicar**.

**LVA Edge Gateway** está agora disponível como tipo de dispositivo para usar na página **dispositivos** na aplicação.

## <a name="add-a-gateway-device"></a>Adicione um dispositivo de gateway

Para adicionar um dispositivo **LVA Edge Gateway** à aplicação:

1. Navegue na página **dispositivos** e selecione o modelo do dispositivo **LVA Edge Gateway.**

1. Selecione **+ Novo**.

1. No Diálogo do **dispositivo Criar um novo dispositivo,** mude o nome do dispositivo para *LVA Gateway 001*, e altere o ID do dispositivo para *lva-gateway-001*.

    > [!NOTE]
    > O ID do dispositivo deve ser único na aplicação.

1. Selecione **Criar**.

O estado do dispositivo está **registado.**

### <a name="get-the-device-credentials"></a>Obtenha as credenciais do dispositivo

Precisa das credenciais que permitem que o dispositivo se conecte à sua aplicação IoT Central. Obter as credenciais do dispositivo:

1. Na página **Dispositivos,** selecione o dispositivo **Lva-gateway-001** que criou.

1. Selecione **Ligar**.

1. Na página **de ligação** do dispositivo, tome nota no ficheiro *scratchpad.txt* do **ID Scope,** do **ID do dispositivo**e da chave **primária**do dispositivo . Usa estes valores mais tarde.

1. Certifique-se de que o método de ligação está definido para **a assinatura de acesso partilhado**.

1. Selecione **Fechar**.

## <a name="next-steps"></a>Passos seguintes

Criou agora uma aplicação IoT Central utilizando o modelo de aplicação **de deteção de objetos e movimentos,** criou um modelo de dispositivo para o dispositivo gateway e adicionou um dispositivo de gateway à aplicação.

Se quiser experimentar a análise de vídeo - aplicação de deteção de objetos e movimentos utilizando módulos IoT Edge que executam um VM em nuvem com streams de vídeo simulados:

> [!div class="nextstepaction"]
> [Crie uma instância IoT Edge para análise de vídeo (Linux VM)](./tutorial-video-analytics-iot-edge-vm.md)

Se quiser experimentar a análise de vídeo - aplicação de deteção de objetos e movimentos utilizando módulos IoT Edge que executam um dispositivo real com uma câmara **ONVIF** real:

> [!div class="nextstepaction"]
> [Crie uma instância IoT Edge para análise de vídeo (Intel NUC)](./tutorial-video-analytics-iot-edge-nuc.md)
