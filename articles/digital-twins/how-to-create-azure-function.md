---
title: Configurar uma função Azure para o processamento de dados
titleSuffix: Azure Digital Twins
description: Veja como criar uma função Azure que possa aceder e ser desencadeada por gémeos digitais.
author: baanders
ms.author: baanders
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 66f514f4c5d299ef11efda541f16f4ef2fe61aed
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88930167"
---
# <a name="connect-azure-functions-apps-for-processing-data"></a>Ligue as aplicações Azure Functions para processamento de dados

Durante a pré-visualização, a atualização de gémeos digitais com base em dados é tratada utilizando rotas de [**eventos**](concepts-route-events.md) através de recursos computacional, tais como [Funções Azure](../azure-functions/functions-overview.md). Uma função Azure pode ser usada para atualizar um gémeo digital em resposta a:
* dados de telemetria do dispositivo provenientes do IoT Hub
* mudança de propriedade ou outros dados provenientes de outro gémeo digital dentro do gráfico gémeo

Este artigo acompanha-o através da criação de uma função Azure para uso com a Azure Digital Twins. 

Aqui está uma visão geral dos passos que contém:

1. Criar uma app Azure Functions no Estúdio Visual
2. Escreva uma função Azure com um gatilho [de Grade de Eventos](../event-grid/overview.md)
3. Adicione código de autenticação à função (para poder aceder a Azure Digital Twins)
4. Publique a app de função para a Azure
5. Estabeleça acesso à [segurança.](concepts-security.md) Para que a função Azure receba um token de acesso no tempo de execução para aceder ao serviço, terá de configurar a Identidade de Serviço Gerido para a aplicação de função.

O restante deste artigo percorre os passos de configuração da função Azure, um de cada vez.

## <a name="create-an-azure-functions-app-in-visual-studio"></a>Criar uma app Azure Functions no Estúdio Visual

No Visual Studio 2019, selecione *File > New Project*. Procure o modelo *Azure Functions,* selecione-o e prima "Seguinte".

:::image type="content" source="media/how-to-create-azure-function/visual-studio-new-project.png" alt-text="Estúdio Visual: novo diálogo de projeto":::

Especifique um nome para a aplicação de função e prima "Criar".

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-config.png" alt-text="Estúdio Visual: configurar o diálogo do projeto":::

Selecione o *gatilho da grelha de eventos* e prima "Criar".

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-trigger.png" alt-text="Visual Studio: Diálogo do gatilho do projeto de função Azure":::

## <a name="write-an-azure-function-with-an-event-grid-trigger"></a>Escreva uma função Azure com um gatilho de Grade de Eventos

O seguinte código cria uma função Azure curta que pode utilizar para registar eventos: 

```csharp
// Default URL for triggering Event Grid function in the local environment
// http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
using System;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;

namespace FunctionSample
{
    public static class FooFunction    {
        [FunctionName("Foo")]
        public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Esta é a sua função básica de Azure.

### <a name="run-and-debug-the-azure-function-app"></a>Executar e depurar a app de função Azure

Agora pode compilar e executar a função. Enquanto as funções de Azure são, em última análise, destinadas a funcionar na nuvem, também pode executar e depurar funções Azure localmente.

Para obter mais informações sobre isto, consulte [*o gatilho da Grelha de Eventos de Debug localmente.*](../azure-functions/functions-debug-event-grid-trigger-local.md)

### <a name="add-the-azure-digital-twins-sdk-to-your-azure-function-app"></a>Adicione o Azure Digital Twins SDK à sua aplicação de função Azure

A aplicação de função interage com a Azure Digital Twins utilizando a [biblioteca de clientes Azure IoT Digital Twin para .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). Para utilizar o SDK, terá de incluir os seguintes pacotes no seu projeto:
* `Azure.DigitalTwins.Core``1.0.0-preview.2`(versão)
* `Azure.Identity`

Para a configuração do gasoduto Azure SDK para configurar corretamente para funções Azure, também necessitará:
* `Azure.Net.Http`
* `Azure.Core`

Dependendo das suas ferramentas de eleição, pode fazê-lo com o gestor de pacotes Visual Studio ou com a `dotnet` ferramenta da linha de comando. 

Adicione as seguintes declarações usando a sua função Azure.

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;
```

## <a name="add-authentication-code-to-the-azure-function"></a>Adicione código de autenticação à função Azure

Em seguida, adicione código de autenticação que permitirá a função aceder a Azure Digital Twins.

Adicione variáveis à sua classe de função para estes valores: 
* O ID da app Azure Digital Twins
* O URL da sua instância Azure Digital Twins. É uma boa prática ler o URL de serviço a partir de uma variável ambiental, em vez de codificar duramente na função.
* Uma variável estática para manter um exemplo httpClient. HttpClient é relativamente caro para criar, e queremos evitar ter que fazer isso para cada invocação de funções.

Adicione também uma variável local dentro da sua função para manter a sua instância de cliente Azure Digital Twins no projeto de função. *Não* faça disto uma variável estática dentro da sua classe.

Por último, altere a assinatura da função para ser assíncronea.

Após estas alterações, o seu código de função deve ser semelhante ao seguinte:

```csharp
namespace FunctionSample
{
    public static class FooFunction
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("Foo")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            DigitalTwinsClient client = null;
            try
            {
                ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
                DigitalTwinsClientOptions opts = 
                    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
                                                
                log.LogInformation($"ADT service client connection created.");
            }
            catch (Exception e)
            {
                log.LogError($"ADT service client connection failed. " + e.ToString());
                return;
            }
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Para que a sua aplicação Functions possa aceder ao Azure Digital Twins, precisa de ter uma identidade gerida pelo sistema e ter permissões para aceder à sua instância Azure Digital Twins.

Utilize o seguinte comando para criar a identidade gerida pelo sistema. Tome nota do campo *principalid* na saída.

```azurecli
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Utilize o valor *principalid* no seguinte comando para atribuir a identidade da aplicação de função ao papel *Azure Digital Twins Owner (Preview)* para a sua instância Azure Digital Twins. Isto dará à aplicação de função permissão no caso de realizar atividades de data plane.

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```

Para obter mais informações sobre a identidade gerida, consulte [*como utilizar identidades geridas para o Serviço de Aplicações e Funções Azure*](../app-service/overview-managed-identity.md).

Por último, pode tornar o URL da sua instância Azure Digital Twins acessível à sua função, definindo uma variável ambiental. Para obter mais informações sobre este ponto, consulte [*as variáveis Ambiente.*](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables)

> [!TIP]
> O URL da instância Azure Digital Twins é feito adicionando *https://* ao início do *nome de anfitrião*da sua instância Azure Digital Twins . Para ver o nome do anfitrião, juntamente com todas as propriedades do seu caso, pode `az dt show --dt-name <your-Azure-Digital-Twins-instance>` correr.

```azurecli
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-URL>"
```

## <a name="publish-the-azure-function-app"></a>Publicar a app de função Azure

Para publicar a aplicação de função para a Azure, selecione à direita o projeto de função (não a solução) no Solution Explorer e escolha *Publicar()*.

Aparecerá o seguinte separador:

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-1.png" alt-text="Estúdio Visual: publicar diálogo de função, página 1":::

Selecione ou crie um plano de Serviço de Aplicações para utilizar com Funções Azure. Se não tiver a certeza, comece a utilizar o plano de consumo predefinido.

> [!IMPORTANT] 
> A publicação de uma função Azure incorrerá em encargos adicionais na sua subscrição, independente da Azure Digital Twins.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-2.png" alt-text="Estúdio Visual: publicar diálogo de função, página 2":::

Na página seguinte, insira o nome desejado para a nova aplicação de função, um grupo de recursos e outros detalhes.

## <a name="set-up-security-access-for-the-azure-function-app"></a>Confiem o acesso à segurança da aplicação de função Azure

O esqueleto da função Azure de exemplos anteriores requer que lhe seja passado um símbolo portador, de modo a poder autenticar-se com as Gémeas Digitais Azure. Para se certificar de que este token ao portador é passado, terá de configurar [a Identidade de Serviço Gerido (MSI)](../active-directory/managed-identities-azure-resources/overview.md) para a aplicação de função. Isto só precisa de ser feito uma vez para cada aplicação de função.

Para configurar isto, vá ao [portal Azure](https://portal.azure.com/) e navegue para a sua aplicação de função.

No separador funcionalidades da *Plataforma,* selecione *Identidade*:

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-1.png" alt-text="Portal Azure: Selecionando identidade para uma função Azure":::

Na página de identidade, desa um pouco de *estado* para *On*. 

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-2.png" alt-text="Portal Azure: Ligar o estado de identidade":::

Note também o **ID** do objeto mostrado nesta página, uma vez que será usado na secção seguinte.

### <a name="assign-access-roles"></a>Atribuir funções de acesso

Uma vez que a Azure Digital Twins utiliza o controlo de acesso baseado em funções para gerir o acesso (ver [*Conceitos: Segurança para as soluções Azure Digital Twins*](concepts-security.md) para obter mais informações sobre isso), também precisa de adicionar uma função para cada aplicação de função que pretende permitir aceder ao Azure Digital Twins.

Para atribuir um papel, precisa do **ID** de recursos da instância Azure Digital Twins que criou. Se não o gravou antes quando criou o seu caso, pode recuperá-lo usando este comando:

```bash
az dt show --name <your-instance-name> -g <your-resource-group-name>
```
O ID do recurso fará parte da saída, como uma longa cadeia chamada "id" que começa com as letras "/subscrições/...".

Utilize o ID do recurso juntamente com o ID do objeto da função Azure anteriormente no comando abaixo:

```azurecli
az role assignment create --role "Azure Digital Twins Owner (Preview)" --assignee <object-ID> --scope <resource-ID>
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, seguiu os passos para configurar uma função Azure para uso com a Azure Digital Twins. Em seguida, pode subscrever a sua função Azure para Event Grid, para ouvir num ponto final. Este ponto final pode ser:
* Um ponto final da Grade de Eventos anexado às Gémeas Digitais Azure para processar mensagens provenientes da própria Azure Digital Twins (tais como mensagens de mudança de propriedade, mensagens de telemetria geradas por [gémeos digitais](concepts-twins-graph.md) no gráfico gémeo ou mensagens de ciclo de vida)
* Os tópicos do sistema IoT utilizados pelo IoT Hub para enviar telemetria e outros eventos de dispositivos
* Um ponto final da Grade de Eventos recebendo mensagens de outros serviços

Em seguida, veja como basear-se na sua função básica de Azure para ingerir dados do IoT Hub em Azure Digital Twins:
* [*Como fazer: Ingerir telemetria a partir do IoT Hub*](how-to-ingest-iot-hub-data.md)