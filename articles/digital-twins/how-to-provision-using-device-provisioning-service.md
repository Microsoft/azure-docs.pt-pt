---
title: Dispositivos de gestão automática utilizando o Serviço de Provisionamento de Dispositivos
titleSuffix: Azure Digital Twins
description: Veja como configurar um processo automatizado para a disponibilização e aposentação de dispositivos IoT em Gémeos Digitais Azure utilizando o Serviço de Provisionamento de Dispositivos (DPS).
author: baanders
ms.author: baanders
ms.date: 3/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: ad1351b7c9a649a553ce54422b99a13c286437d6
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107300"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Dispositivos de gestão automática em Gémeos Digitais Azure utilizando o Serviço de Provisionamento de Dispositivos (DPS)

Neste artigo, você vai aprender a integrar Azure Digital Twins com [o Serviço de Provisionamento de Dispositivos (DPS)](../iot-dps/about-iot-dps.md).

A solução descrita neste artigo permitirá automatizar o processo para **_provisões_** e **_aposentar_** dispositivos IoT Hub em Azure Digital Twins, utilizando o Serviço de Provisionamento de Dispositivos. 

Para obter mais informações sobre as fases _de fornecimento_ e _aposentação,_ e para melhor compreender o conjunto de etapas gerais de gestão de dispositivos que são comuns a todos os projetos IoT da empresa, consulte a secção de ciclo de vida do [ *Dispositivo*](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) da documentação de gestão de dispositivos do IoT Hub.

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar o provisionamento, terá de configurar o seguinte:
* um **exemplo de Azure Digital Twins**. Siga as instruções em [*Como-a-: Confi ressalta uma instância e autenticação*](how-to-set-up-instance-portal.md) para criar uma instância de gémeos digitais Azure. Recolha o nome de **_anfitrião_** do caso no portal Azure [(instruções).](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)
* um **hub IoT.** Para obter instruções, consulte a secção *Criar um hub IoT* deste [quickstart IoT Hub](../iot-hub/quickstart-send-telemetry-cli.md).
* uma [**função Azure**](../azure-functions/functions-overview.md) que atualiza informações digitais de gémeos com base em dados do IoT Hub. Siga as instruções em [*Como: Ingerir dados do hub IoT*](how-to-ingest-iot-hub-data.md) para criar esta função Azure. Reúna o **_nome_** da função para usá-lo neste artigo.

Esta amostra também utiliza um **simulador de dispositivo** que inclui o provisionamento utilizando o Serviço de Provisionamento de Dispositivos. O simulador do dispositivo está localizado aqui: [Azure Digital Twins e IoT Hub Integration Sample](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Obtenha o projeto de amostra na sua máquina navegando para o link de amostra e selecionando o botão *Download ZIP* por baixo do título. Desaperte a pasta descarregada.

Vai precisar de [**Node.js**](https://nodejs.org/download) instalado na sua máquina. O simulador do dispositivo baseia-se **emNode.js**, versão 10.0.x ou posterior.

## <a name="solution-architecture"></a>Arquitetura de soluções

A imagem abaixo ilustra a arquitetura desta solução utilizando as Gémeas Digitais Azure com o Serviço de Provisionamento de Dispositivos. Mostra tanto a provisão do dispositivo como o fluxo de aposentadoria.

:::image type="content" source="media/how-to-provision-using-dps/flows.png" alt-text="Diagrama de dispositivo e vários serviços Azure num cenário de ponta a ponta. Os dados fluem para trás e para a frente entre um dispositivo termóstato e DPS. Os dados também fluem de DPS para IoT Hub, e para Azure Digital Twins através de uma função Azure com o rótulo de &quot;Atribuição&quot;. Os dados de uma ação manual de &quot;Eliminar dispositivo&quot; fluem através do IoT Hub > Event Hubs > Azure Functions > Azure Digital Twins." lightbox="media/how-to-provision-using-dps/flows.png":::

Este artigo é dividido em duas secções:
* [*Dispositivo de fornecimento automático utilizando o Serviço de Provisionamento de Dispositivos*](#auto-provision-device-using-device-provisioning-service)
* [*Dispositivo de auto-aposentadoria utilizando eventos de ciclo de vida IoT Hub*](#auto-retire-device-using-iot-hub-lifecycle-events)

Para obter explicações mais profundas de cada passo na arquitetura, consulte as suas secções individuais mais tarde no artigo.

## <a name="auto-provision-device-using-device-provisioning-service"></a>Dispositivo de fornecimento automático utilizando o Serviço de Provisionamento de Dispositivos

Nesta secção, irá anexar o Serviço de Provisionamento de Dispositivos a Gémeos Digitais Azure a dispositivos de provisão automática através do caminho abaixo. Este é um excerto da arquitetura completa mostrada [anteriormente.](#solution-architecture)

:::image type="content" source="media/how-to-provision-using-dps/provision.png" alt-text="Diagrama de Provisão flow -- um excerto do diagrama de arquitetura de solução, com secções de rotulagem de números do fluxo. Os dados fluem para trás e para a frente entre um dispositivo termóstato e DPS (1 para dispositivo > DPS e 5 para o dispositivo DPS >). Os dados também fluem de DPS para IoT Hub (4), e para Azure Digital Twins (3) através de uma função Azure rotulada de 'Atribuição' (2)." lightbox="media/how-to-provision-using-dps/provision.png":::

Aqui está uma descrição do fluxo do processo:
1. O dispositivo contacta o ponto final do DPS, passando informações identificando para comprovar a sua identidade.
2. O DPS valida a identidade do dispositivo validando o ID de registo e a chave contra a lista de inscrição, e chama uma [função Azure](../azure-functions/functions-overview.md) para fazer a atribuição.
3. A função Azure cria um novo [gémeo](concepts-twins-graph.md) em Azure Digital Twins para o dispositivo. O gémeo terá o mesmo nome que o **ID** de registo do dispositivo.
4. O DPS regista o dispositivo com um hub IoT e povoa o estado gémeo desejado pelo dispositivo.
5. O hub IoT devolve informações de identificação do dispositivo e as informações de ligação do hub IoT ao dispositivo. O dispositivo pode agora ligar-se ao hub IoT.

As secções seguintes percorrem os passos para configurar este fluxo de dispositivo de auto-provisão.

### <a name="create-a-device-provisioning-service"></a>Criar um serviço de provisionamento de dispositivos

Quando um novo dispositivo é a provisionado usando o Serviço de Provisionamento de Dispositivos, um novo gémeo para este dispositivo pode ser criado em Azure Digital Twins com o mesmo nome que o ID de registo.

Crie uma instância de Serviço de Provisionamento de Dispositivos, que será utilizada para o fornecimento de dispositivos IoT. Pode utilizar as instruções Azure CLI abaixo, ou utilizar o portal Azure: [*Quickstart: Configurar o Serviço de Provisionamento de Dispositivos IoT Hub com o portal Azure*](../iot-dps/quick-setup-auto-provision.md).

O seguinte comando Azure CLI criará um serviço de provisionamento de dispositivos. Você precisará especificar um nome de serviço de fornecimento de dispositivos, grupo de recursos e região. Para ver que regiões suportam o Serviço de Provisionamento de Dispositivos, visite [*os produtos Azure disponíveis por região.*](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub)
O comando pode ser executado em [Cloud Shell,](https://shell.azure.com)ou localmente se tiver o CLI Azure [instalado na sua máquina](/cli/azure/install-azure-cli).

```azurecli-interactive
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region>
```

### <a name="add-a-function-to-use-with-device-provisioning-service"></a>Adicione uma função a utilizar com o Serviço de Provisionamento de Dispositivos

Dentro do projeto de aplicação de função que criou na secção [pré-requisitos,](#prerequisites) irá criar uma nova função a utilizar com o Serviço de Provisionamento de Dispositivos. Esta função será utilizada pelo Serviço de Provisionamento de Dispositivos numa [Política de Atribuição Personalizada](../iot-dps/how-to-use-custom-allocation-policies.md) para a disponibilização de um novo dispositivo.

Comece por abrir o projeto de aplicação de função no Estúdio Visual na sua máquina e siga os passos abaixo.

#### <a name="step-1-add-a-new-function"></a>Passo 1: Adicionar uma nova função 

Adicione uma nova função do tipo *HTTP-trigger* ao projeto de aplicação de função no Visual Studio.

:::image type="content" source="media/how-to-provision-using-dps/add-http-trigger-function-visual-studio.png" alt-text="Screenshot da vista do Estúdio Visual para adicionar a função Azure do tipo Http Trigger no seu projeto de aplicação de função." lightbox="media/how-to-provision-using-dps/add-http-trigger-function-visual-studio.png":::

#### <a name="step-2-fill-in-function-code"></a>Passo 2: Preencha o código de função

Adicione um novo pacote NuGet ao projeto: [Microsoft.Azure.Devices.Provisioning.Service](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/). Você pode precisar adicionar mais pacotes ao seu projeto também, se os pacotes usados no código já não fazem parte do projeto.

No ficheiro de código de função recém-criado, cole no seguinte código, rebatize a função para *DpsAdtAllocationFunc.cs*, e guarde o ficheiro.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DpsAdtAllocationFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>Passo 3: Publicar a app de função para a Azure

Publique o projeto com *DpsAdtAllocationFunc.cs* função à aplicação de função em Azure.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-device-provisioning-enrollment"></a>Criar inscrição no Provisionamento de Dispositivos

Em seguida, você precisará criar uma inscrição no Serviço de Provisionamento de Dispositivos usando uma **função de atribuição personalizada**. Siga as instruções para o fazer na secção Criar a secção de [*inscrição*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) do artigo de políticas de atribuição personalizada na documentação do Serviço de Provisionamento de Dispositivos.

Ao passar por esse fluxo, certifique-se de que seleciona as seguintes opções para ligar a inscrição à função que acabou de criar.

* **Selecione como pretende atribuir dispositivos aos hubs**: Personalizado (Use Azure Function).
* **Selecione os hubs IoT a que este grupo pode ser designado:** Escolha o nome do seu hub IoT ou selecione o Link um novo botão *de hub IoT* e escolha o seu hub IoT a partir do dropdown.

Em seguida, escolha o novo botão *de função* para ligar a sua aplicação de função ao grupo de inscrição. Em seguida, preencha os seguintes valores:

* **Subscrição**: A sua subscrição Azure é auto-povoada. Certifique-se de que é a subscrição certa.
* **App de função**: Escolha o nome da aplicação da sua função.
* **Função**: Escolha DpsAdtAllocationFunc.

Guarde os seus detalhes.                  

:::image type="content" source="media/how-to-provision-using-dps/link-enrollment-group-to-iot-hub-and-function-app.png" alt-text="Screenshot da janela de detalhes do grupo de inscrição da Alfândega para Selecionar Personalizado (Use Azure Function) e o seu nome de hub IoT nas secções Selecione como pretende atribuir dispositivos aos hubs e Selecione os hubs IoT a que este grupo pode ser atribuído. Além disso, selecione a sua aplicação de subscrição, função a partir do dropdown e certifique-se de selecionar DpsAdtAllocationFunc." lightbox="media/how-to-provision-using-dps/link-enrollment-group-to-iot-hub-and-function-app.png":::

Após a criação da inscrição, a **Chave Primária** para a inscrição será usada mais tarde para configurar o simulador do dispositivo para este artigo.

### <a name="set-up-the-device-simulator"></a>Configurar o simulador de dispositivo

Esta amostra utiliza um simulador de dispositivo que inclui o provisionamento utilizando o Serviço de Provisionamento de Dispositivos. O simulador do dispositivo está localizado aqui: [Azure Digital Twins e IoT Hub Integration Sample](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Se ainda não descarregou a amostra, obtenha-a agora navegando para o link de amostra e selecionando o botão *Download ZIP* por baixo do título. Desaperte a pasta descarregada.

#### <a name="upload-the-model"></a>Faça upload do modelo

O simulador do dispositivo é um dispositivo do tipo termóstato que utiliza o modelo com este ID: `dtmi:contosocom:DigitalTwins:Thermostat;1` . Terá de fazer o upload deste modelo para a Azure Digital Twins antes de poder criar um gémeo deste tipo para o dispositivo.

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

Para obter mais informações sobre os modelos, consulte [*como:Gerir modelos*](how-to-manage-model.md#upload-models).

#### <a name="configure-and-run-the-simulator"></a>Configure e executar o simulador

Na sua janela de comando, navegue para a amostra descarregada *Azure Digital Twins e IoT Hub Integration* que abriu mais cedo e, em seguida, para o diretório *de simuladores de dispositivos.* Em seguida, instale as dependências do projeto utilizando o seguinte comando:

```cmd
npm install
```

Em seguida, no diretório do simulador do dispositivo, copie o ficheiro .env.template para um novo ficheiro chamado .env e recolha os seguintes valores para preencher as definições:

* PROVISIONING_IDSCOPE: Para obter este valor, navegue para o serviço de fornecimento de dispositivos no [portal Azure](https://portal.azure.com/), em seguida, selecione *Overview* nas opções do menu e procure o campo *ID Scope*.

    :::image type="content" source="media/how-to-provision-using-dps/id-scope.png" alt-text="Screenshot da visão do portal Azure do dispositivo que disponibiliza a página geral para copiar o valor do ID Scope." lightbox="media/how-to-provision-using-dps/id-scope.png":::

* PROVISIONING_REGISTRATION_ID: Pode escolher uma identificação de registo para o seu dispositivo.
* ADT_MODEL_ID: `dtmi:contosocom:DigitalTwins:Thermostat;1`
* PROVISIONING_SYMMETRIC_KEY: Esta é a chave principal para a inscrição que configuraste anteriormente. Para obter este valor novamente, navegue para o serviço de fornecimento de dispositivos no portal Azure, *selecione Gerir as inscrições,* em seguida, selecione o grupo de inscrição que criou anteriormente e copie a *Chave Primária*.

    :::image type="content" source="media/how-to-provision-using-dps/sas-primary-key.png" alt-text="Screenshot da visão do portal Azure do serviço de fornecimento de dispositivos gerir a página de matrículas para copiar o valor principal da chave SAS." lightbox="media/how-to-provision-using-dps/sas-primary-key.png":::

Agora, utilize os valores acima para atualizar as definições de ficheiro .env.

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary SAS key>"
```

Guarde e feche o ficheiro.

### <a name="start-running-the-device-simulator"></a>Comece a executar o simulador do dispositivo

Ainda no diretório *do simulador de dispositivos* na janela de comando, inicie o simulador do dispositivo utilizando o seguinte comando:

```cmd
node .\adt_custom_register.js
```

Deverá ver o dispositivo a ser registado e ligado ao IoT Hub e, em seguida, começar a enviar mensagens.
:::image type="content" source="media/how-to-provision-using-dps/output.png" alt-text="Screenshot da janela do Comando mostrando o registo do dispositivo e enviando mensagens" lightbox="media/how-to-provision-using-dps/output.png":::

### <a name="validate"></a>Validação

Como resultado do fluxo que criou neste artigo, o dispositivo será automaticamente registado em Azure Digital Twins. Utilize o seguinte comando [CLI Azure Digital Twins](how-to-use-cli.md) para encontrar o gémeo do dispositivo na instância Azure Digital Twins que criou.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

Deve ver o gémeo do dispositivo encontrado no caso Azure Digital Twins.
:::image type="content" source="media/how-to-provision-using-dps/show-provisioned-twin.png" alt-text="Screenshot da janela do Comando mostrando gémeo recém-criado." lightbox="media/how-to-provision-using-dps/show-provisioned-twin.png":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>Dispositivo de auto-aposentadoria utilizando eventos de ciclo de vida IoT Hub

Nesta secção, você vai anexar eventos de ciclo de vida IoT Hub a Azure Digital Twins para auto-aposentar dispositivos através do caminho abaixo. Este é um excerto da arquitetura completa mostrada [anteriormente.](#solution-architecture)

:::image type="content" source="media/how-to-provision-using-dps/retire.png" alt-text="Diagrama do fluxo do dispositivo Retire-- um excerto do diagrama de arquitetura de solução, com secções de rotulagem de números do fluxo. O dispositivo termóstato é mostrado sem ligações aos serviços Azure no diagrama. Os dados de uma ação manual de &quot;Delete Device&quot; fluem através do IoT Hub (1) > Event Hubs (2) > Funções Azure > Gémeas Digitais Azure (3)." lightbox="media/how-to-provision-using-dps/retire.png":::

Aqui está uma descrição do fluxo do processo:
1. Um processo externo ou manual desencadeia a supressão de um dispositivo no Hub IoT.
2. O IoT Hub elimina o dispositivo e gera um evento [de ciclo de vida do dispositivo](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) que será encaminhado para um centro de [eventos.](../event-hubs/event-hubs-about.md)
3. Uma função Azure elimina o gémeo do dispositivo em Azure Digital Twins.

As secções seguintes percorrem os degraus para configurar este fluxo de dispositivo de auto-aposentadoria.

### <a name="create-an-event-hub"></a>Criar um hub de eventos

Em seguida, você vai criar um centro de [eventos](../event-hubs/event-hubs-about.md) Azure para receber eventos de ciclo de vida IoT Hub. 

Siga os passos descritos no Início rápido do [*centro de eventos.*](../event-hubs/event-hubs-create.md) Nomeie os seus eventos de *ciclo de vida hub de eventos.* Você usará este nome do centro do evento quando configurar a rota IoT Hub e uma função Azure nas secções seguintes.

A imagem abaixo ilustra a criação do centro de eventos.
:::image type="content" source="media/how-to-provision-using-dps/create-event-hub-lifecycle-events.png" alt-text="Screenshot da janela do portal Azure para criar um centro de eventos com o nome lifecycleevents." lightbox="media/how-to-provision-using-dps/create-event-hub-lifecycle-events.png":::

#### <a name="create-sas-policy-for-your-event-hub"></a>Crie a política SAS para o seu centro de eventos

Em seguida, terá de criar uma [política de assinatura de acesso partilhado (SAS)](../event-hubs/authorize-access-shared-access-signature.md) para configurar o centro de eventos com a sua aplicação de função.
Para tal,
1. Navegue para o centro de eventos que acaba de criar no portal Azure e selecione **políticas de acesso partilhado** nas opções de menu à esquerda.
2. Selecione **Adicionar**. Na janela *'Adicionar A POLÍTICA SAS'* que se abre, introduza um nome de política à sua escolha e selecione a caixa de verificação *Listen.*
3. Selecione **Criar**.
    
:::image type="content" source="media/how-to-provision-using-dps/add-event-hub-sas-policy.png" alt-text="Screenshot do portal Azure para adicionar uma política SAS do centro de eventos." lightbox="media/how-to-provision-using-dps/add-event-hub-sas-policy.png":::

#### <a name="configure-event-hub-with-function-app"></a>Configurar o centro de eventos com app de função

Em seguida, configurar a aplicação de função Azure que criou na secção [pré-requisitos](#prerequisites) para trabalhar com o seu novo centro de eventos. Fá-lo-á definindo uma variável ambiental dentro da aplicação de função com a cadeia de ligação do centro de eventos.

1. Abra a política que acabou de criar e copie o valor **da chave primária de ligação.**

    :::image type="content" source="media/how-to-provision-using-dps/event-hub-sas-policy-connection-string.png" alt-text="Screenshot do portal Azure para copiar a chave primária de ligação." lightbox="media/how-to-provision-using-dps/event-hub-sas-policy-connection-string.png":::

2. Adicione a cadeia de ligação como variável nas definições da aplicação de função com o seguinte comando Azure CLI. O comando pode ser executado em [Cloud Shell,](https://shell.azure.com)ou localmente se tiver o CLI Azure [instalado na sua máquina](/cli/azure/install-azure-cli).

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="add-a-function-to-retire-with-iot-hub-lifecycle-events"></a>Adicione uma função para se aposentar com eventos de ciclo de vida IoT Hub

Dentro do projeto de aplicação de função que criou na secção [pré-requisitos,](#prerequisites) criará uma nova função para reformar um dispositivo existente usando eventos de ciclo de vida IoT Hub.

Para mais informações sobre eventos de ciclo de vida, consulte [*eventos ioT Hub Non-telemetria*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events). Para obter mais informações sobre a utilização de Centros de Eventos com funções Azure, consulte [*o gatilho Azure Event Hubs para Funções Azure*](../azure-functions/functions-bindings-event-hubs-trigger.md).

Comece por abrir o projeto de aplicação de função no Estúdio Visual na sua máquina e siga os passos abaixo.

#### <a name="step-1-add-a-new-function"></a>Passo 1: Adicionar uma nova função
     
Adicione uma nova função do tipo *Event Hub Trigger* ao projeto de aplicação de função no Estúdio Visual.

:::image type="content" source="media/how-to-provision-using-dps/create-event-hub-trigger-function.png" alt-text="Screenshot da janela Do Estúdio Visual para adicionar uma função Azure do tipo Event Hub Trigger no seu projeto de aplicação de função." lightbox="media/how-to-provision-using-dps/create-event-hub-trigger-function.png":::

#### <a name="step-2-fill-in-function-code"></a>Passo 2: Preencha o código de função

No ficheiro de código de função recém-criado, cole no seguinte código, rebatize a função para `DeleteDeviceInTwinFunc.cs` , e guarde o ficheiro.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DeleteDeviceInTwinFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>Passo 3: Publicar a app de função para a Azure

Publique o projeto com *DeleteDeviceInTwinFunc.cs* função à aplicação de função em Azure.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>Criar uma rota IoT Hub para eventos de ciclo de vida

Agora você vai configurar uma rota IoT Hub, para encaminhar eventos de ciclo de vida do dispositivo. Neste caso, irá ouvir especificamente os eventos de eliminação de dispositivos, identificados por `if (opType == "deleteDeviceIdentity")` . Isto irá desencadear a eliminação do item digital twin, finalizando a retirada de um dispositivo e do seu gémeo digital.

Primeiro, terás de criar um ponto final no teu centro de IoT. Em seguida, você adicionará uma rota no hub IoT para enviar eventos de ciclo de vida para este ponto final do centro de eventos.
Siga estes passos para criar um ponto final do centro de eventos:

1. No [portal Azure,](https://portal.azure.com/)navegue para o hub IoT que criou na secção [pré-requisitos](#prerequisites) e selecione o **encaminhamento de mensagens** nas opções do menu à esquerda.
2. Selecione o **separador pontos finais personalizados.**
3. Selecione **+ Adicionar** e escolha **centros de eventos** para adicionar um ponto final do tipo de centros de eventos.

    :::image type="content" source="media/how-to-provision-using-dps/event-hub-custom-endpoint.png" alt-text="Screenshot da janela do Estúdio Visual para adicionar um ponto final personalizado do centro de eventos." lightbox="media/how-to-provision-using-dps/event-hub-custom-endpoint.png":::

4. Na janela Adicione um ponto final do centro de *eventos* que se abre, escolha os seguintes valores:
    * **Nome do ponto final**: Escolha um nome de ponto final.
    * **Espaço de nome do hub do** evento : Selecione o seu espaço de nome do centro de eventos na lista de dropdown.
    * **Instância do centro do** evento : Escolha o nome do hub de evento que criou no passo anterior.
5. Selecione **Criar**. Mantenha esta janela aberta para adicionar uma rota no próximo passo.

    :::image type="content" source="media/how-to-provision-using-dps/add-event-hub-endpoint.png" alt-text="Screenshot da janela do Estúdio Visual para adicionar um ponto final do centro de eventos." lightbox="media/how-to-provision-using-dps/add-event-hub-endpoint.png":::

Em seguida, irá adicionar uma rota que se conecta ao ponto final que criou no passo acima, com uma consulta de encaminhamento que envia os eventos de eliminação. Siga estes passos para criar uma rota:

1. Navegue no separador *Rotas* e selecione **Adicionar** para adicionar uma rota.

    :::image type="content" source="media/how-to-provision-using-dps/add-message-route.png" alt-text="Screenshot da janela do Estúdio Visual para adicionar uma rota para enviar eventos." lightbox="media/how-to-provision-using-dps/add-message-route.png":::

2. Na página Adicionar uma página *de rota* que abre, escolha os seguintes valores:

   * **Nome**: Escolha um nome para o seu percurso. 
   * **Ponto final**: Escolha o ponto final dos centros de eventos que criou mais cedo a partir do dropdown.
   * **Fonte de dados**: Escolha *eventos de ciclo de vida do dispositivo*.
   * **Consulta de encaminhamento**: Insira `opType='deleteDeviceIdentity'` . Esta consulta limita os eventos do ciclo de vida do dispositivo apenas para enviar os eventos de eliminação.

3. Selecione **Guardar**.

    :::image type="content" source="media/how-to-provision-using-dps/lifecycle-route.png" alt-text="Screenshot da janela do portal Azure para adicionar uma rota para enviar eventos de ciclo de vida." lightbox="media/how-to-provision-using-dps/lifecycle-route.png":::

Uma vez que tenha passado por este fluxo, tudo está definido para retirar os dispositivos de ponta a ponta.

### <a name="validate"></a>Validação

Para desencadear o processo de aposentadoria, é necessário eliminar manualmente o dispositivo do IoT Hub.

Pode fazê-lo com um [comando Azure CLI](/cli/azure/iot/hub/module-identity#az_iot_hub_module_identity_delete) ou no portal Azure. Siga os passos abaixo para eliminar o dispositivo no portal Azure:

1. Navegue para o seu hub IoT e escolha **dispositivos IoT** nas opções de menu à esquerda. 
2. Verá um dispositivo com o ID de registo do dispositivo que escolheu na [primeira metade deste artigo.](#auto-provision-device-using-device-provisioning-service) Em alternativa, pode escolher qualquer outro dispositivo para eliminar, desde que tenha um gémeo em Azure Digital Twins para que possa verificar se o gémeo é automaticamente eliminado após a eliminação do dispositivo.
3. Selecione o dispositivo e escolha **Eliminar**.

:::image type="content" source="media/how-to-provision-using-dps/delete-device-twin.png" alt-text="Screenshot do portal Azure para eliminar o dispositivo gémeo dos dispositivos IoT." lightbox="media/how-to-provision-using-dps/delete-device-twin.png":::

Pode levar alguns minutos para ver as mudanças refletidas nas Gémeas Digitais Azure.

Utilize o seguinte comando [CLI dos Gémeos Digitais Azure](how-to-use-cli.md) para verificar se o gémeo do dispositivo na instância Azure Digital Twins foi eliminado.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

Deve ver que o gémeo do dispositivo já não pode ser encontrado no caso Azure Digital Twins.

:::image type="content" source="media/how-to-provision-using-dps/show-retired-twin.png" alt-text="Screenshot da janela do Comando mostrando gémeos não encontrados." lightbox="media/how-to-provision-using-dps/show-retired-twin.png":::

## <a name="clean-up-resources"></a>Limpar os recursos

Se já não necessitar dos recursos criados neste artigo, siga estes passos para os eliminar.

Utilizando o Azure Cloud Shell ou o Azure CLI local, pode eliminar todos os recursos Azure num grupo de recursos com o comando de eliminação do [grupo Az.](/cli/azure/group#az-group-delete) Isto remove o grupo de recursos; o exemplo das Gémeas Digitais Azure; o hub IoT e o registo do dispositivo do hub; o tópico da grelha de eventos e as subscrições associadas; o espaço de nomes dos centros de eventos e ambas as aplicações Azure Functions, incluindo recursos associados como o armazenamento.

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos nele contidos são eliminados permanentemente. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Em seguida, elimine a pasta de amostra de projeto que descarregou da sua máquina local.

## <a name="next-steps"></a>Passos seguintes

Os gémeos digitais criados para os dispositivos são armazenados como uma hierarquia plana em Azure Digital Twins, mas podem ser enriquecidos com informação de modelo e uma hierarquia de vários níveis para organização. Para saber mais sobre este conceito, leia:

* [*Conceitos: Gémeos digitais e o gráfico gémeo*](concepts-twins-graph.md)

Para obter mais informações sobre a utilização de pedidos HTTP com funções Azure, consulte:

* [*Azure Http solicita o gatilho para funções Azure*](../azure-functions/functions-bindings-http-webhook-trigger.md)

Pode escrever lógica personalizada para fornecer automaticamente esta informação utilizando os dados do modelo e gráfico já armazenados em Azure Digital Twins. Para ler mais sobre gestão, atualização e recolha de informações do gráfico de gémeos, consulte o seguinte:

* [*Como fazer: Gerir um gémeo digital*](how-to-manage-twin.md)
* [*Como fazer: Consulta do gráfico gémeo*](how-to-query-graph.md)