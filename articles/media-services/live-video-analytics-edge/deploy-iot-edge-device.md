---
title: Implemente o Live Video Analytics num dispositivo IoT Edge - Azure
description: Este artigo lista os passos que o ajudarão a implementar o Live Video Analytics no seu dispositivo IoT Edge. Fá-lo-ia, por exemplo, se tivesse acesso a uma máquina Linux local e/ou criasse previamente uma conta Azure Media Services.
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: a64fc11d7afa70d5200fdbd24bd3facdb8a95a7e
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019584"
---
# <a name="deploy-live-video-analytics-on-an-iot-edge-device"></a>Implemente o Live Video Analytics num dispositivo IoT Edge

Este artigo lista os passos que o ajudarão a implementar o Live Video Analytics no seu dispositivo IoT Edge. Fá-lo-ia, por exemplo, se tivesse acesso a uma máquina Linux local e/ou criasse previamente uma conta Azure Media Services.

> [!NOTE]
> O suporte para dispositivos ARM64 está disponível em Live Video Analytics em construções IoT Edge `1.0.4` e mais recentes.
> O suporte para executar o tempo de funcionamento do Azure IoT Edge em dispositivos ARM64 está em [visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

* Um dispositivo x86-64 ou um ARM64 que executa um dos [sistemas operativos Linux suportados](../../iot-edge/support.md#operating-systems)
* Assinatura Azure para a qual tem [privilégios de proprietário](../../role-based-access-control/built-in-roles.md#owner)
* [Criar e configurar o Hub IoT](../../iot-hub/iot-hub-create-through-portal.md)
* [Registar dispositivo IoT Edge](../../iot-edge/how-to-register-device.md)
* [Instalar o runtime do Azure IoT Edge em sistemas Linux baseados em Debian](../../iot-edge/how-to-install-iot-edge-linux.md)
* [Criar uma conta Azure Media Services](../latest/create-account-howto.md)

    * Utilize uma destas regiões: Leste dos EUA 2, Leste dos EUA, Central EUA, Norte Central dos EUA, Japão Leste, Oeste dos EUA, Oeste dos EUA 2, Centro Ocidental dos EUA, Canadá Leste, Reino Unido Sul, França Central, França Sul, Suíça Norte, Suíça Oeste e Japão Oeste.
    * Recomenda-se que utilize contas de armazenamento v2 (GPv2) para fins gerais

## <a name="configuring-azure-resources-for-using-live-video-analytics"></a>Configurar recursos do Azure para a utilização de Vídeo Analytics ao Vivo

### <a name="create-custom-azure-resource-manager-role"></a>Criar função do Azure Resource Manager personalizada

Consulte [Criar uma função personalizada de Gestor de Recursos Azure](create-custom-azure-resource-manager-role-how-to.md) e atribua-a a um diretor de serviço para utilização em Vídeo Analytics ao Vivo.

### <a name="set-up-a-premium-streaming-endpoint"></a>Criar um ponto final de streaming premium

Se pretender utilizar o Live Video Analytics para gravar o vídeo continuamente na nuvem e, posteriormente, utilizar [APIs de consulta](playback-recordings-how-to.md#query-api) antes de reproduzi-lo, recomendamos a atualização do seu Serviço de Media para utilizar um [ponto final de streaming premium.](../latest/streaming-endpoint-concept.md#types)  

Este passo é opcional. Pode utilizar este comando Azure CLI para o fazer:

```azurecli
az ams streaming-endpoint scale --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --scale-units 1
```

Pode usar este comando para iniciar o ponto final de streaming 

> [!IMPORTANT]
> A sua subscrição começará a ser cobrada neste momento.

```azurecli
az ams streaming-endpoint start --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --no-wait
```

Siga os passos deste artigo para obter credenciais para aceder às APIs do Serviço de Mídia: [aceda às APIs do Serviço de Mídia](../latest/access-api-howto.md?tabs=portal) e selecione o separador Portal.

## <a name="create-and-use-local-user-account-for-deployment"></a>Criar e utilizar a conta de utilizador local para implantação
Para executar o módulo Live Video Analytics no módulo IoT Edge crie uma conta de utilizador local com o mínimo de privilégios possível. Como exemplo, execute os seguintes comandos na sua máquina Linux:

```
sudo groupadd -g 1010 localuser
sudo adduser --home /home/edgeuser --uid 1010 -gid 1010 edgeuser
```

## <a name="granting-permissions-to-device-storage"></a>Concessão de permissões ao armazenamento de dispositivos

Agora que criou uma conta de utilizador local, 

* Necessitará de uma pasta local para armazenar os dados de configuração da aplicação. Crie uma pasta e conceda permissões à conta local escrever para essa pasta utilizando os seguintes comandos:

```
sudo mkdir /var/lib/azuremediaservices
sudo chown -R edgeuser /var/lib/azuremediaservices
```

* Também necessitará de uma pasta para [gravar vídeos num ficheiro local](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources). Utilize os seguintes comandos para criar uma pasta local para o mesmo:

```
sudo mkdir /var/media
sudo chown -R edgeuser /var/media
```

## <a name="deploy-live-video-analytics-edge-module"></a>Implementar módulo de borda de análise de vídeo ao vivo

O Live Video Analytics on IoT Edge expõe propriedades gémeas do módulo que estão documentadas no [esquema de configuração do Módulo Twin.](module-twin-configuration-schema.md) 

### <a name="deploy-using-the-azure-portal"></a>Implementar com o portal do Azure

O portal Azure guia-o através da criação de um manifesto de implantação e empurrando a implementação para um dispositivo IoT Edge.  
#### <a name="select-your-device-and-set-modules"></a>Selecione o seu dispositivo e definir módulos

1. Inscreva-se no [portal Azure](https://ms.portal.azure.com/) e navegue até ao seu hub IoT.
1. Selecione **IoT Edge** no menu.
1. Clique no ID do dispositivo alvo a partir da lista de dispositivos.
1. Selecione **módulos de conjunto**.

#### <a name="configure-a-deployment-manifest"></a>Configure um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos a implementar, como os dados fluem entre os módulos e as propriedades desejadas dos gémeos módulos. O portal Azure tem um assistente que o acompanha através da criação de um manifesto de implantação. Tem três passos organizados em separadores: **Módulos,** **Rotas**e **Revisão + Criar.**

#### <a name="add-modules"></a>Adicionar módulos

1. Na secção **módulos IoT Edge modules** da página, clique no **Add** dropdown e selecione **O Módulo de Borda IoT** para exibir a página do Módulo de Borda Add **IoT.**
1. No **separador Definições** do Módulo, forneça um nome para o módulo e, em seguida, especifique a imagem do recipiente URI:   
    Exemplos:
    
    * **Nome do módulo IoT Edge**: lvaEdge
    * **Imagem URI**: mcr.microsoft.com/media/live-video-analytics:1.0    
    
    ![A screenshot mostra o separador Definições do Módulo.](./media/deploy-iot-edge-device/add.png)
    
    > [!TIP]
    > Não **selecione Adicionar** até ter especificado **valores**nas Definições do Módulo , **Opções de Criação de Recipientes**e Separadores **de Configurações Gémeas do Módulo,** conforme descrito neste procedimento.
    
    > [!WARNING]
    > O Azure IoT Edge é sensível a casos quando faz chamadas para módulos. Tome nota da cadeia exata que usa como nome do módulo.»

1. Abra o **separador Variáveis ambientais.**
   
   Adicione os seguintes valores nas caixas de entrada que vê ![ Variáveis ambientais](./media/deploy-iot-edge-device/environment-variables.png) 

1. Abra o **separador Opções de Criação de Recipientes.**

    ![Opções de criação de contentores](./media/deploy-iot-edge-device/container-create-options.png)
 
    Copiar e colar o seguinte JSON na caixa, para limitar o tamanho dos ficheiros de registo produzidos pelo módulo.
    
    ```    
    {
        "HostConfig": {
            "LogConfig": {
                "Type": "",
                "Config": {
                    "max-size": "10m",
                    "max-file": "10"
                }
            },
            "Binds": [
               "/var/lib/azuremediaservices:/var/lib/azuremediaservices",
               "/var/media:/var/media"
            ]
        }
    }
    ````
   
   A secção "Ligações" no JSON tem 2 entradas:
   1. "/var/lib/azuremediaservices:/var/lib/azuremediaservices": Isto é usado para ligar os dados persistentes de configuração da aplicação do recipiente e armazená-lo no dispositivo de borda.
   1. "/var/media:/var/media": Isto liga as pastas de mídia entre o dispositivo de borda e o recipiente. Isto é usado para armazenar as gravações de vídeo quando você executar uma topologia de gráficos de mídia que suporta o armazenamento de clips de vídeo no dispositivo de borda.
   
1. No **separador Definições Gémeas** do Módulo, copie o JSON seguinte e cole-o na caixa.
 
    ![Configurações gémeas](./media/deploy-iot-edge-device/twin-settings.png)

    Live Video Analytics on IoT Edge requer um conjunto de propriedades gémeas obrigatórias para ser executado, como listado no [esquema de configuração Módulo Twin](module-twin-configuration-schema.md).  

    O JSON que precisa de introduzir na caixa de edição de Configurações Gémeas do Módulo será assim:    
    ```
    {
        "applicationDataDirectory": "/var/lib/azuremediaservices",
        "azureMediaServicesArmId": "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{AMS-account-name}",
        "aadTenantId": "{the-ID-of-your-tenant}",
        "aadServicePrincipalAppId": "{the-ID-of-the-service-principal-app-for-ams-account}",
        "aadServicePrincipalSecret": "{secret}"
    }
    ```
    Estas são propriedades **necessárias** e para o JSON acima,  
    * {subscriçãoID} - este é o seu ID de subscrição Azure
    * {resourceGroupName} - este é o grupo de recursos a que pertence a sua conta De Serviço de Mídia
    * {AMS-account-name} - este é o nome da sua conta de Media Services
    
    Para obter os outros valores, consulte [a API dos Serviços de Media do Access Azure](../latest/access-api-howto.md?tabs=portal) e selecione o separador Portal.  
    * aadTenantId - esta é a identificação do seu inquilino e é a mesma que o "AadTenantId" do link acima.
    * aadServicePrincipalAppId - este é o ID da aplicação principal do serviço para a sua Conta de Serviço de Mídia e é o mesmo que o "AadClientId" do link acima.
    * aadServicePrincipalSecret - esta é a palavra-passe do principal serviço e é a mesma que o "AadSecret" do link acima.

    Abaixo estão algumas propriedades **recomendadas** adicionais que podem ser adicionadas ao JSON e ajudarão na monitorização do módulo. Para obter mais informações, consulte [a monitorização e a exploração madeireira:](monitoring-logging.md)
    
    ```
    "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
    "OperationalEventsOutputName": "lvaEdgeOperational",
    "logLevel": "Information",
    "logCategories": "Application,Events"
    ```
    
    Seguem-se algumas propriedades **opcionais** que pode adicionar no JSON:
    
    ```
    "aadEndpoint": "https://login.microsoftonline.com",
    "aadResourceId": "https://management.core.windows.net/",
    "armEndpoint": "https://management.azure.com/",
    "allowUnsecuredEndpoints": true
    ```

   > [!Note]
   > A propriedade twin **permite que Os pontos de Finalidades de Sedcured** são definidos como verdadeiros para o propósito dos tutoriais e dos quickstarts.   
   Você deve definir esta propriedade **falsa** quando estiver correndo em ambiente de produção. Isto garantirá que a aplicação bloqueará todos os pontos finais não protegidos e, para executar as topologias de gráficos, serão necessárias credenciais de ligação válidas.  
   
    Selecione Adicionar para adicionar as propriedades gémeas do módulo.
1. Selecione **Seguinte: Rotas** para continuar até à secção de rotas.
    Especifique rotas.

Mantenha as rotas predefinidos e selecione **Seguinte: Revisão + criar** para continuar na secção de revisão.

#### <a name="review-deployment"></a>Implementação de revisão

A secção de revisão mostra-lhe o manifesto de implantação JSON que foi criado com base nas suas seleções nas duas secções anteriores. Há também dois módulos declarados que não acrescentou: $edgeAgent e $edgeHub. Estes dois módulos compõem o tempo de funcionamento do IoT Edge e são necessários predefinidos em todas as implementações.

Reveja as suas informações de implementação e, em seguida, selecione Criar.

### <a name="verify-your-deployment"></a>Verifique a sua implantação

Depois de criar a implementação, volte à página IoT Edge do seu hub IoT.

1. Selecione o dispositivo IoT Edge que direcionou com a implementação para abrir os seus detalhes.
2. Nos dados do dispositivo, verifique se o módulo de armazenamento de bolhas está listado como **especificado na implementação e reportado pelo dispositivo**.

Pode levar alguns momentos para que o módulo seja iniciado no dispositivo e depois reportado de volta ao IoT Hub. Refresque a página para ver um estado atualizado.
Código de estado: 200 -OK significa que [o tempo de funcionamento do IoT Edge](../../iot-edge/iot-edge-runtime.md) está saudável e está a funcionar bem.

![A screenshot mostra um valor de estado para um tempo de execução IoT Edge.](./media/deploy-iot-edge-device/status.png)

#### <a name="invoke-a-direct-method"></a>Invocar um método direto

Em seguida, vamos testar a amostra invocando um método direto. Leia [os métodos diretos para live video analytics no IoT Edge](direct-methods.md) para entender todos os métodos diretos fornecidos pelo nosso módulo LvaEdge.

1. Clicar no módulo de borda que criou, irá levá-lo à sua página de configuração.  

    ![A screenshot mostra a página de configuração de um módulo de borda.](./media/deploy-iot-edge-device/modules.png)
1. Clique na opção de menu Método Direto.

    > [!NOTE] 
    > Terá de adicionar um valor nas secções de cadeia de Ligação, como pode ver na página atual. Não é necessário esconder ou alterar nada na secção **de nomes de definição.** Não faz mal que seja público.

    ![Método direto](./media/deploy-iot-edge-device/module-details.png)
1. Em seguida, introduza "GraphTopologyList" na caixa De Nome de Método.
1. Em seguida, copie e cole a carga útil abaixo do JSON na caixa de carga útil.
    
    ```
    {
        "@apiVersion" : "1.0"
    }
    ```
1. Clique na opção "Invocar Método" no topo da página

    ![Métodos diretos](./media/deploy-iot-edge-device/direct-method.png)
1. Deve ver uma mensagem de estado 200 na caixa resultado

    ![A mensagem status 200](./media/deploy-iot-edge-device/connection-timeout.png) 

## <a name="next-steps"></a>Passos seguintes

Experimente [Quickstart: Get start - Live Video Analytics on IoT Edge](get-started-detect-motion-emit-events-quickstart.md#deploy-modules-on-your-edge-device)

> [!TIP]
> Se proceder ao quickstart acima, ao invocar os métodos diretos utilizando o Código do Estúdio Visual, utilizará o dispositivo que foi adicionado ao IoT Hub através deste artigo, em vez do padrão `lva-sample-device` .