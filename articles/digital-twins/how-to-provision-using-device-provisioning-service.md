---
title: Gerir automaticamente dispositivos utilizando DPS
titleSuffix: Azure Digital Twins
description: Veja como configurar um processo automatizado para a disponibilização e aposentação de dispositivos IoT em Gémeos Digitais Azure utilizando o Serviço de Provisionamento de Dispositivos (DPS).
author: baanders
ms.author: baanders
ms.date: 9/1/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0a18e6cef568afa8a0092fc06d8f6bb526739b2a
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145808"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Dispositivos de gestão automática em Gémeos Digitais Azure utilizando o Serviço de Provisionamento de Dispositivos (DPS)

Neste artigo, você vai aprender a integrar Azure Digital Twins com [o Serviço de Provisionamento de Dispositivos (DPS)](../iot-dps/about-iot-dps.md).

A solução descrita neste artigo permitirá automatizar o processo para **_provisões_** e **_aposentar_** dispositivos IoT Hub em Azure Digital Twins, utilizando o Serviço de Provisionamento de Dispositivos. 

Para obter mais informações sobre as fases _de fornecimento_ e _aposentação,_ e para melhor compreender o conjunto de etapas gerais de gestão de dispositivos que são comuns a todos os projetos IoT da empresa, consulte a secção de ciclo de vida do [ *Dispositivo*](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) da documentação de gestão de dispositivos do IoT Hub.

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar o provisionamento, precisa de ter uma **instância Azure Digital Twins** que contenha modelos e gémeos. Este caso também deve ser configurado com a capacidade de atualizar informações digitais de gémeos com base em dados. 

Se ainda não tiver esta configuração, pode criá-la seguindo o Tutorial de Gémeos Digitais Azure: [*Conecte uma solução de ponta a ponta*](tutorial-end-to-end.md). O tutorial irá acompanhar-te pela criação de um exemplo de Azure Digital Twins com modelos e gémeos, um [Azure IoT Hub](../iot-hub/about-iot-hub.md)conectado, e várias [funções Azure](../azure-functions/functions-overview.md) para propagar o fluxo de dados.

Vai precisar dos seguintes valores mais tarde neste artigo a partir do momento em que configurar o seu caso. Se precisar de voltar a recolher estes valores, utilize os links abaixo para obter instruções.
* Nome **_de anfitrião_** de exemplo de Azure Digital Twins [(encontre no portal)](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)
* Cadeia de **_ligação_** Azure Event Hubs [(encontre no portal)](../event-hubs/event-hubs-get-connection-string.md#get-connection-string-from-the-portal)

Esta amostra também utiliza um **simulador de dispositivo** que inclui o provisionamento utilizando o Serviço de Provisionamento de Dispositivos. O simulador do dispositivo está localizado aqui: [Azure Digital Twins e IoT Hub Integration Sample](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Obtenha o projeto de amostra na sua máquina navegando para o link de amostra e selecionando o botão *Download ZIP* por baixo do título. Desaperte a pasta descarregada.

O simulador do dispositivo baseia-se **emNode.js** , versão 10.0.x ou posterior. [*Prepare o seu ambiente de desenvolvimento*](https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md) descreve como instalar Node.js para este tutorial no Windows ou linux.

## <a name="solution-architecture"></a>Arquitetura de soluções

A imagem abaixo ilustra a arquitetura desta solução utilizando as Gémeas Digitais Azure com o Serviço de Provisionamento de Dispositivos. Mostra tanto a provisão do dispositivo como o fluxo de aposentadoria.

:::image type="content" source="media/how-to-provision-using-dps/flows.png" alt-text="Uma visão de um dispositivo e vários serviços Azure num cenário de ponta a ponta. Os dados fluem para trás e para a frente entre um dispositivo termóstato e DPS. Os dados também fluem de DPS para IoT Hub, e para Azure Digital Twins através de uma função Azure com o rótulo de &quot;Atribuição&quot;. Os dados de uma ação manual de &quot;Eliminar dispositivo&quot; fluem através do IoT Hub > Event Hubs > Azure Functions > Azure Digital Twins.":::

Este artigo é dividido em duas secções:
* [*Dispositivo de fornecimento automático utilizando o Serviço de Provisionamento de Dispositivos*](#auto-provision-device-using-device-provisioning-service)
* [*Dispositivo de auto-aposentadoria utilizando eventos de ciclo de vida IoT Hub*](#auto-retire-device-using-iot-hub-lifecycle-events)

Para obter explicações mais profundas de cada passo na arquitetura, consulte as suas secções individuais mais tarde no artigo.

## <a name="auto-provision-device-using-device-provisioning-service"></a>Dispositivo de fornecimento automático utilizando o Serviço de Provisionamento de Dispositivos

Nesta secção, irá anexar o Serviço de Provisionamento de Dispositivos a Gémeos Digitais Azure a dispositivos de provisão automática através do caminho abaixo. Este é um excerto da arquitetura completa mostrada [anteriormente.](#solution-architecture)

:::image type="content" source="media/how-to-provision-using-dps/provision.png" alt-text="Fluxo de provisão- um excerto do diagrama de arquitetura de solução, com secções de rotulagem de números do fluxo. Os dados fluem para trás e para a frente entre um dispositivo termóstato e DPS (1 para dispositivo > DPS e 5 para o dispositivo DPS >). Os dados também fluem de DPS para IoT Hub (4), e para Azure Digital Twins (3) através de uma função Azure rotulada de 'Atribuição' (2).":::

Aqui está uma descrição do fluxo do processo:
1. O dispositivo contacta o ponto final do DPS, passando informações identificando para comprovar a sua identidade.
2. O DPS valida a identidade do dispositivo validando o ID de registo e a chave contra a lista de inscrição, e chama uma [função Azure](../azure-functions/functions-overview.md) para fazer a atribuição.
3. A função Azure cria um novo [gémeo](concepts-twins-graph.md) em Azure Digital Twins para o dispositivo.
4. O DPS regista o dispositivo com um hub IoT e povoa o estado gémeo desejado pelo dispositivo.
5. O hub IoT devolve informações de identificação do dispositivo e as informações de ligação do hub IoT ao dispositivo. O dispositivo pode agora ligar-se ao hub IoT.

As secções seguintes percorrem os passos para configurar este fluxo de dispositivo de auto-provisão.

### <a name="create-a-device-provisioning-service"></a>Criar um serviço de provisionamento de dispositivos

Quando um novo dispositivo é a provisionado usando o Serviço de Provisionamento de Dispositivos, um novo gémeo para este dispositivo pode ser criado em Azure Digital Twins.

Crie uma instância de Serviço de Provisionamento de Dispositivos, que será utilizada para o fornecimento de dispositivos IoT. Pode utilizar as instruções Azure CLI abaixo, ou utilizar o portal Azure: [*Quickstart: Configurar o Serviço de Provisionamento de Dispositivos IoT Hub com o portal Azure*](../iot-dps/quick-setup-auto-provision.md).

O seguinte comando Azure CLI criará um serviço de provisionamento de dispositivos. Você precisará especificar um nome, grupo de recursos e região. O comando pode ser executado em [Cloud Shell,](https://shell.azure.com)ou localmente se tiver o CLI Azure [instalado na sua máquina](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).

```azurecli-interactive
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region; for example, eastus>
```

### <a name="create-an-azure-function"></a>Criar uma função do Azure

Em seguida, irá criar uma função acionada por pedidos HTTP dentro de uma aplicação de função. Pode utilizar a aplicação de função criada no tutorial de ponta a ponta [*(Tutorial: Conecte uma solução de ponta a ponta),*](tutorial-end-to-end.md)ou a sua própria.

Esta função será utilizada pelo Serviço de Provisionamento de Dispositivos numa [Política de Atribuição Personalizada](../iot-dps/how-to-use-custom-allocation-policies.md) para a disponibilização de um novo dispositivo. Para obter mais informações sobre a utilização de pedidos HTTP com funções Azure, consulte [*Azure Http solicite o gatilho para funções Azure*](../azure-functions/functions-bindings-http-webhook-trigger.md).

Dentro do projeto da aplicação de funções, adicione uma nova função. Além disso, adicione um novo pacote NuGet ao projeto: `Microsoft.Azure.Devices.Provisioning.Service` .

No ficheiro de código de função recém-criado, cole no seguinte código.

```C#
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Devices.Shared;
using Microsoft.Azure.Devices.Provisioning.Service;
using System.Net.Http;
using Azure.Identity;
using Azure.DigitalTwins.Core;
using Azure.Core.Pipeline;
using Azure;
using System.Collections.Generic;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Samples.AdtIothub
{
    public static class DpsAdtAllocationFunc
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("DpsAdtAllocationFunc")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger log)
        {
            // Get request body
            string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogDebug($"Request.Body: {requestBody}");
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            // Get registration ID of the device
            string regId = data?.deviceRuntimeContext?.registrationId;

            bool fail = false;
            string message = "Uncaught error";
            ResponseObj obj = new ResponseObj();

            // Must have unique registration ID on DPS request 
            if (regId == null)
            {
                message = "Registration ID not provided for the device.";
                log.LogInformation("Registration ID: NULL");
                fail = true;
            }
            else
            {
                string[] hubs = data?.linkedHubs.ToObject<string[]>();

                // Must have hubs selected on the enrollment
                if (hubs == null)
                {
                    message = "No hub group defined for the enrollment.";
                    log.LogInformation("linkedHubs: NULL");
                    fail = true;
                }
                else
                {
                    // Find or create twin based on the provided registration ID and model ID
                    dynamic payloadContext = data?.deviceRuntimeContext?.payload;
                    string dtmi = payloadContext.modelId;
                    log.LogDebug($"payload.modelId: {dtmi}");
                    string dtId = await FindOrCreateTwin(dtmi, regId, log);

                    // Get first linked hub (TODO: select one of the linked hubs based on policy)
                    obj.iotHubHostName = hubs[0];

                    // Specify the initial tags for the device.
                    TwinCollection tags = new TwinCollection();
                    tags["dtmi"] = dtmi;
                    tags["dtId"] = dtId;

                    // Specify the initial desired properties for the device.
                    TwinCollection properties = new TwinCollection();

                    // Add the initial twin state to the response.
                    TwinState twinState = new TwinState(tags, properties);
                    obj.initialTwin = twinState;
                }
            }

            log.LogDebug("Response: " + ((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message));

            return (fail)
                ? new BadRequestObjectResult(message)
                : (ActionResult)new OkObjectResult(obj);
        }

        public static async Task<string> FindOrCreateTwin(string dtmi, string regId, ILogger log)
        {
            // Create Digital Twins client
            var cred = new ManagedIdentityCredential(adtAppId);
            var client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });

            // Find existing twin with registration ID
            string dtId;
            string query = $"SELECT * FROM DigitalTwins T WHERE $dtId = '{regId}' AND IS_OF_MODEL('{dtmi}')";
            AsyncPageable<string> twins = client.QueryAsync(query);

            await foreach (string twinJson in twins)
            {
                // Get DT ID from the Twin
                JObject twin = (JObject)JsonConvert.DeserializeObject(twinJson);
                dtId = (string)twin["$dtId"];
                log.LogInformation($"Twin '{dtId}' with Registration ID '{regId}' found in DT");
                return dtId;
            }

            // Not found, so create new twin
            log.LogInformation($"Twin ID not found, setting DT ID to regID");
            dtId = regId; // use the Registration ID as the DT ID

            // Define the model type for the twin to be created
            Dictionary<string, object> meta = new Dictionary<string, object>()
            {
                { "$model", dtmi }
            };
            // Initialize the twin properties
            Dictionary<string, object> twinProps = new Dictionary<string, object>()
            {
                { "$metadata", meta }
            };
            twinProps.Add("Temperature", 0.0);

            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(dtId, twinProps);
            log.LogInformation($"Twin '{dtId}' created in DT");

            return dtId;
        }
    }

    public class ResponseObj
    {
        public string iotHubHostName { get; set; }
        public TwinState initialTwin { get; set; }
    }
}
```

Guarde o ficheiro e, em seguida, re-publique a sua aplicação de função. Para obter instruções sobre a publicação da aplicação de funções, consulte a secção de [*aplicações*](tutorial-end-to-end.md#publish-the-app) do tutorial de ponta a ponta.

### <a name="configure-your-function"></a>Configure a sua função

Em seguida, terá de definir variáveis ambientais na sua aplicação de função anteriormente, contendo a referência à instância Azure Digital Twins que criou. Se utilizar o tutorial de ponta a ponta [*(Tutorial: Conecte uma solução de ponta a ponta),*](tutorial-end-to-end.md)a definição já estará configurada.

Adicione a definição com este comando Azure CLI:

```azurecli-interactive
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

Certifique-se de que as permissões e a atribuição de funções de identidade gerida estão configuradas corretamente para a aplicação de função, conforme descrito na secção [*Atribua permissões à aplicação*](tutorial-end-to-end.md#assign-permissions-to-the-function-app) de função no tutorial de ponta a ponta.

### <a name="create-device-provisioning-enrollment"></a>Criar inscrição no Provisionamento de Dispositivos

Em seguida, você precisará criar uma inscrição no Serviço de Provisionamento de Dispositivos usando uma **função de atribuição personalizada**. Siga as instruções para o fazer na [*Secção de Inscrição*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) e [*Obtém secções únicas*](../iot-dps/how-to-use-custom-allocation-policies.md#derive-unique-device-keys) de chaves de dispositivo do artigo serviços de fornecimento de dispositivos sobre políticas de atribuição personalizada.

Ao passar por esse fluxo, irá ligar a inscrição à função que acabou de criar selecionando a sua função durante o passo para **Selecionar como pretende atribuir dispositivos aos hubs**. Após a criação da inscrição, o nome de inscrição e a chave SAS primária ou secundária serão usadas mais tarde para configurar o simulador do dispositivo para este artigo.

### <a name="set-up-the-device-simulator"></a>Configurar o simulador de dispositivo

Esta amostra utiliza um simulador de dispositivo que inclui o provisionamento utilizando o Serviço de Provisionamento de Dispositivos. O simulador do dispositivo está localizado aqui: [Azure Digital Twins e IoT Hub Integration Sample](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Se ainda não descarregou a amostra, obtenha-a agora navegando para o link de amostra e selecionando o botão *Download ZIP* por baixo do título. Desaperte a pasta descarregada.

Abra uma janela de comando e navegue na pasta descarregada e, em seguida, no diretório *do simulador de dispositivo.* Instale as dependências do projeto utilizando o seguinte comando:

```cmd
npm install
```

Em seguida, copie o ficheiro *.env.template* para um novo ficheiro chamado *.env* , e preencha estas definições:

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary or secondary SAS key>"
```

Guarde e feche o ficheiro.

### <a name="start-running-the-device-simulator"></a>Comece a executar o simulador do dispositivo

Ainda no diretório *do simulador de dispositivos* na janela de comando, inicie o simulador do dispositivo utilizando o seguinte comando:

```cmd
node .\adt_custom_register.js
```

Deverá ver o dispositivo a ser registado e ligado ao IoT Hub e, em seguida, começar a enviar mensagens.
:::image type="content" source="media/how-to-provision-using-dps/output.png" alt-text="Janela de comando mostrando o registo do dispositivo e enviando mensagens":::

### <a name="validate"></a>Validação

Como resultado do fluxo que criou neste artigo, o dispositivo será automaticamente registado em Azure Digital Twins. Utilizando o seguinte comando [CLI das Gémeas Digitais Azure](how-to-use-cli.md) para encontrar o gémeo do dispositivo na instância Azure Digital Twins que criou.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

Deve ver o gémeo do dispositivo encontrado no caso Azure Digital Twins.
:::image type="content" source="media/how-to-provision-using-dps/show-provisioned-twin.png" alt-text="Janela de comando mostrando gémeo recém-criado":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>Dispositivo de auto-aposentadoria utilizando eventos de ciclo de vida IoT Hub

Nesta secção, você vai anexar eventos de ciclo de vida IoT Hub a Azure Digital Twins para auto-aposentar dispositivos através do caminho abaixo. Este é um excerto da arquitetura completa mostrada [anteriormente.](#solution-architecture)

:::image type="content" source="media/how-to-provision-using-dps/retire.png" alt-text="Retire o fluxo do dispositivo- um excerto do diagrama de arquitetura de solução, com secções de rotulagem de números do fluxo. O dispositivo termóstato é mostrado sem ligações aos serviços Azure no diagrama. Os dados de uma ação manual de &quot;Delete Device&quot; fluem através do IoT Hub (1) > Event Hubs (2) > Funções Azure > Gémeas Digitais Azure (3).":::

Aqui está uma descrição do fluxo do processo:
1. Um processo externo ou manual desencadeia a supressão de um dispositivo no Hub IoT.
2. O IoT Hub elimina o dispositivo e gera um evento [de ciclo de vida do dispositivo](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) que será encaminhado para um centro de [eventos.](../event-hubs/event-hubs-about.md)
3. Uma função Azure elimina o gémeo do dispositivo em Azure Digital Twins.

As secções seguintes percorrem os degraus para configurar este fluxo de dispositivo de auto-aposentadoria.

### <a name="create-an-event-hub"></a>Criar um hub de eventos

Você precisa agora criar um centro de [eventos](../event-hubs/event-hubs-about.md)Azure , que será usado para receber os eventos do ciclo de vida IoT Hub. 

Ver os passos descritos no [*Centro de Eventos um*](../event-hubs/event-hubs-create.md) quickstart do centro de eventos, utilizando as seguintes informações:
* Se estiver a utilizar o tutorial de ponta a ponta [*(Tutorial: Conecte uma solução de ponta a ponta),*](tutorial-end-to-end.md)pode reutilizar o grupo de recursos que criou para o tutorial de ponta a ponta.
* Nomeie os seus eventos de ciclo de vida hub de *eventos,* ou outra coisa à sua escolha, e lembre-se do espaço de nome que criou. Utilizará estes quando configurar a função de ciclo de vida e a rota IoT Hub nas secções seguintes.

### <a name="create-an-azure-function"></a>Criar uma função do Azure

Em seguida, irá criar uma função desencadeada por Event Hubs dentro de uma aplicação de função. Pode utilizar a aplicação de função criada no tutorial de ponta a ponta [*(Tutorial: Conecte uma solução de ponta a ponta),*](tutorial-end-to-end.md)ou a sua própria. 

Nomeie o seu centro de eventos para desencadear *eventos de ciclo de vida* e ligue o gatilho do centro de eventos ao centro de eventos que criou no passo anterior. Se usou um nome de hub de evento diferente, altere-o para corresponder ao nome do gatilho abaixo.

Esta função utilizará o evento de ciclo de vida do dispositivo IoT Hub para retirar um dispositivo existente. Para mais informações sobre eventos de ciclo de vida, consulte [*eventos ioT Hub Non-telemetria*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events). Para obter mais informações sobre a utilização de Centros de Eventos com funções Azure, consulte [*o gatilho Azure Event Hubs para Funções Azure*](../azure-functions/functions-bindings-event-hubs-trigger.md).

Dentro da sua aplicação de função publicada, adicione uma nova classe de função do tipo *Event Hub Trigger* , e cole no código abaixo.

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Azure;
using Azure.Core.Pipeline;
using Azure.DigitalTwins.Core;
using Azure.DigitalTwins.Core.Serialization;
using Azure.Identity;
using Microsoft.Azure.EventHubs;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Samples.AdtIothub
{
    public static class DeleteDeviceInTwinFunc
    {
        private static string adtAppId = "https://digitaltwins.azure.net";
        private static readonly string adtInstanceUrl = System.Environment.GetEnvironmentVariable("ADT_SERVICE_URL", EnvironmentVariableTarget.Process);
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("DeleteDeviceInTwinFunc")]
        public static async Task Run(
            [EventHubTrigger("lifecycleevents", Connection = "EVENTHUB_CONNECTIONSTRING")] EventData[] events, ILogger log)
        {
            var exceptions = new List<Exception>();

            foreach (EventData eventData in events)
            {
                try
                {
                    //log.LogDebug($"EventData: {System.Text.Json.JsonSerializer.Serialize(eventData)}");

                    string opType = eventData.Properties["opType"] as string;
                    if (opType == "deleteDeviceIdentity")
                    {
                        string deviceId = eventData.Properties["deviceId"] as string;
                        
                        // Create Digital Twin client
                        var cred = new ManagedIdentityCredential(adtAppId);
                        var client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });

                        // Find twin based on the original Registration ID
                        string regID = deviceId; // simple mapping
                        string dtId = await GetTwinId(client, regID, log);
                        if (dtId != null)
                        {
                            await DeleteRelationships(client, dtId, log);

                            // Delete twin
                            await client.DeleteDigitalTwinAsync(dtId);
                            log.LogInformation($"Twin '{dtId}' deleted in DT");
                        }
                    }
                }
                catch (Exception e)
                {
                    // We need to keep processing the rest of the batch - capture this exception and continue.
                    exceptions.Add(e);
                }
            }

            if (exceptions.Count > 1)
                throw new AggregateException(exceptions);

            if (exceptions.Count == 1)
                throw exceptions.Single();
        }


        public static async Task<string> GetTwinId(DigitalTwinsClient client, string regId, ILogger log)
        {
            string query = $"SELECT * FROM DigitalTwins T WHERE T.$dtId = '{regId}'";
            AsyncPageable<string> twins = client.QueryAsync(query);
            await foreach (string twinJson in twins)
            {
                JObject twin = (JObject)JsonConvert.DeserializeObject(twinJson);
                string dtId = (string)twin["$dtId"];
                log.LogInformation($"Twin '{dtId}' found in DT");
                return dtId;
            }

            return null;
        }

        public static async Task DeleteRelationships(DigitalTwinsClient client, string dtId, ILogger log)
        {
            var relationshipIds = new List<string>();

            AsyncPageable<string> relationships = client.GetRelationshipsAsync(dtId);
            await foreach (var relationshipJson in relationships)
            {
                BasicRelationship relationship = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relationshipJson);
                relationshipIds.Add(relationship.Id);
            }

            foreach (var relationshipId in relationshipIds)
            {
                client.DeleteRelationship(dtId, relationshipId);
                log.LogInformation($"Twin '{dtId}' relationship '{relationshipId}' deleted in DT");
            }
        }
    }
}
```

Guarde o projeto e, em seguida, publique novamente a aplicação de função. Para obter instruções sobre a publicação da aplicação de funções, consulte a secção de [*aplicações*](tutorial-end-to-end.md#publish-the-app) do tutorial de ponta a ponta.

### <a name="configure-your-function"></a>Configure a sua função

Em seguida, terá de definir variáveis ambientais na sua app de função anteriormente, contendo a referência à instância Azure Digital Twins que criou e ao centro de eventos. Se utilizar o tutorial de ponta a ponta [*(Tutorial: Conecte uma solução de ponta a ponta),*](./tutorial-end-to-end.md)a primeira definição já estará configurada.

Adicione a definição com este comando Azure CLI. O comando pode ser executado em [Cloud Shell,](https://shell.azure.com)ou localmente se tiver o CLI Azure [instalado na sua máquina](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).

```azurecli-interactive
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

Em seguida, terá de configurar a variável ambiente de função para ligar ao centro de eventos recém-criado.

```azurecli-interactive
az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
```

Certifique-se de que as permissões e a atribuição de funções de identidade gerida estão configuradas corretamente para a aplicação de função, conforme descrito na secção [*Atribua permissões à aplicação*](tutorial-end-to-end.md#assign-permissions-to-the-function-app) de função no tutorial de ponta a ponta.

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>Criar uma rota IoT Hub para eventos de ciclo de vida

Agora tens de configurar uma rota IoT Hub para encaminhar eventos de ciclo de vida do dispositivo. Neste caso, irá ouvir especificamente os eventos de eliminação de dispositivos, identificados por `if (opType == "deleteDeviceIdentity")` . Isto irá desencadear a eliminação do item digital twin, finalizando a retirada de um dispositivo e do seu gémeo digital.

As instruções para a criação de uma rota IoT Hub são descritas neste artigo: [*Utilize o encaminhamento de mensagens IoT Hub para enviar mensagens dispositivo-a-nuvem para diferentes pontos finais*](../iot-hub/iot-hub-devguide-messages-d2c.md). A secção *Eventos de Não Telemetria* explica que pode utilizar **eventos de ciclo de vida** do dispositivo como fonte de dados para a rota.

Os passos que precisa de passar para esta configuração são:
1. Crie um ponto final personalizado do centro de eventos IoT Hub. Este ponto final deve ter como alvo o centro de eventos que criou na secção [*Criar um hub de eventos.*](#create-an-event-hub)
2. Adicione uma rota *de eventos de ciclo de vida do dispositivo.* Utilize o ponto final criado no passo anterior. Pode limitar os eventos do ciclo de vida do dispositivo apenas para enviar os eventos de eliminação adicionando a consulta de encaminhamento `opType='deleteDeviceIdentity'` .
    :::image type="content" source="media/how-to-provision-using-dps/lifecycle-route.png" alt-text="Adicionar uma rota":::

Uma vez que tenha passado por este fluxo, tudo está definido para retirar os dispositivos de ponta a ponta.

### <a name="validate"></a>Validação

Para desencadear o processo de aposentadoria, é necessário eliminar manualmente o dispositivo do IoT Hub.

Na [primeira metade deste artigo,](#auto-provision-device-using-device-provisioning-service)criou-se um dispositivo no IoT Hub e um gémeo digital correspondente. 

Agora, vá ao IoT Hub e elimine esse dispositivo (pode fazê-lo com um [comando Azure CLI](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest&preserve-view=true#ext-azure-cli-iot-ext-az-iot-hub-device-identity-delete) ou no [portal Azure).](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Devices%2FIotHubs) 

O dispositivo será automaticamente removido da Azure Digital Twins. 

Utilize o seguinte comando [CLI dos Gémeos Digitais Azure](how-to-use-cli.md) para verificar se o gémeo do dispositivo na instância Azure Digital Twins foi eliminado.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

Deve ver que o gémeo do dispositivo já não pode ser encontrado no caso Azure Digital Twins.
:::image type="content" source="media/how-to-provision-using-dps/show-retired-twin.png" alt-text="Janela de comando mostrando gémeo não encontrado":::

## <a name="clean-up-resources"></a>Limpar recursos

Se já não necessitar dos recursos criados neste artigo, siga estes passos para os eliminar.

Utilizando o Azure Cloud Shell ou o Azure CLI local, pode eliminar todos os recursos Azure num grupo de recursos com o comando de eliminação do [grupo Az.](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete) Isto remove o grupo de recursos; o exemplo das Gémeas Digitais Azure; o hub IoT e o registo do dispositivo do hub; o tópico da grelha de eventos e as subscrições associadas; o espaço de nomes dos centros de eventos e ambas as aplicações Azure Functions, incluindo recursos associados como o armazenamento.

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos nele contidos são eliminados permanentemente. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Em seguida, elimine a pasta de amostra de projeto que descarregou da sua máquina local.

## <a name="next-steps"></a>Passos seguintes

Os gémeos digitais criados para os dispositivos são armazenados como uma hierarquia plana em Azure Digital Twins, mas podem ser enriquecidos com informação de modelo e uma hierarquia de vários níveis para organização. Para saber mais sobre este conceito, leia:

* [*Conceitos: Gémeos digitais e o gráfico gémeo*](concepts-twins-graph.md)

Pode escrever lógica personalizada para fornecer automaticamente esta informação utilizando os dados do modelo e gráfico já armazenados em Azure Digital Twins. Para ler mais sobre gestão, atualização e recolha de informações do gráfico de gémeos, consulte o seguinte:

* [*Como fazer: Gerir um gémeo digital*](how-to-manage-twin.md)
* [*Como fazer: Consulta do gráfico gémeo*](how-to-query-graph.md)