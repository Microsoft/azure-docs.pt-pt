---
title: Configurar uma função Azure para o processamento de dados
titleSuffix: Azure Digital Twins
description: Veja como criar uma função Azure que possa aceder e ser desencadeada por gémeos digitais.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: bf2894cc30873e3cbf7f379e22078dbb863ac179
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91970440"
---
# <a name="connect-azure-functions-apps-for-processing-data"></a>Ligue as aplicações Azure Functions para processamento de dados

A atualização de gémeos digitais com base em dados é tratada utilizando rotas de [**eventos**](concepts-route-events.md) através de recursos compute, tais como [Funções Azure.](../azure-functions/functions-overview.md) Uma função Azure pode ser usada para atualizar um gémeo digital em resposta a:
* dados de telemetria do dispositivo provenientes do IoT Hub
* mudança de propriedade ou outros dados provenientes de outro gémeo digital dentro do gráfico gémeo

Este artigo acompanha-o através da criação de uma função Azure para uso com a Azure Digital Twins. 

Aqui está uma visão geral dos passos que contém:

1. Criar uma app Azure Functions no Estúdio Visual
2. Escreva uma função Azure com um gatilho [de Grade de Eventos](../event-grid/overview.md)
3. Adicione código de autenticação à função (para poder aceder a Azure Digital Twins)
4. Publique a app de função para a Azure
5. Confiem o acesso à [segurança](concepts-security.md) da aplicação de função Azure

## <a name="create-an-azure-functions-app-in-visual-studio"></a>Criar uma app Azure Functions no Estúdio Visual

No Visual Studio 2019, selecione _File > New > Project_ e procure o modelo de _Funções Azure,_ selecione _Next_.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Estúdio Visual: novo diálogo de projeto":::

Especifique um nome para a aplicação de função e _selecione Criar._

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Estúdio Visual: novo diálogo de projeto":::

Selecione o tipo de aplicação de função *Despoletamento de Grelha* de Evento e selecione _Criar_.

:::image type="content" source="media/how-to-create-azure-function/eventgridtrigger-function.png" alt-text="Estúdio Visual: novo diálogo de projeto":::

Uma vez criada a aplicação de função, o seu estúdio visual terá uma amostra de código auto-povoada em **function.cs** ficheiro na sua pasta de projeto. Esta função curta de Azure é usada para registar eventos.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Estúdio Visual: novo diálogo de projeto":::

## <a name="write-an-azure-function-with-an-event-grid-trigger"></a>Escreva uma função Azure com um gatilho de Grade de Eventos

Pode escrever uma função Azure adicionando SDK à sua aplicação de função. A aplicação de função interage com a Azure Digital Twins utilizando a [biblioteca de clientes Azure IoT Digital Twin para .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). 

Para utilizar o SDK, terá de incluir os seguintes pacotes no seu projeto. Pode instalar os pacotes utilizando o gestor de pacotes do estúdio visual NuGet ou adicionar as embalagens utilizando uma `dotnet` ferramenta de linha de comando. Escolha qualquer um destes métodos: 

**Opção 1. Adicione pacotes utilizando o gestor de pacotes Visual Studio:**
    
Pode fazê-lo selecionando à direita no seu projeto e selecione _Gerir Pacotes NuGet_ da lista. Em seguida, na janela que se abre, selecione procurar o separador _Browse_ e procure os seguintes pacotes. Selecione _Instalar_ e _aceitar_ o contrato de Licença para instalar as embalagens.

* `Azure.DigitalTwins.Core`
* `Azure.Identity` 

Para a configuração do gasoduto Azure SDK para configurar corretamente para as Funções Azure, também necessitará das seguintes embalagens. Repita o mesmo processo que acima para instalar todas as embalagens.

* `System.Net.Http`
* `Azure.Core.Pipeline`

**Opção 2. Adicione pacotes utilizando `dotnet` a ferramenta de linha de comando:**

```cmd/sh
dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
dotnet add package Azure.identity --version 1.2.2
dotnet add package System.Net.Http
dotnet add package Azure.Core.Pipeline
```
Em seguida, no seu Visual Studio Solution Explorer, abra _function.cs_ ficheiro onde tem código de amostra e adicione as seguintes declarações _usando_ a sua função Azure. 

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;
```
## <a name="add-authentication-code-to-the-azure-function"></a>Adicione código de autenticação à função Azure

Irá agora declarar variáveis de nível de classe e adicionar código de autenticação que permitirá a função aceder a Azure Digital Twins. Irá adicionar o seguinte à sua função Azure no ficheiro {your function name}.cs.

* Leia o URL de serviço ADT como uma variável ambiental. É uma boa prática ler o URL de serviço a partir de uma variável ambiental, em vez de codificar duramente na função.
```csharp     
private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
```
* Uma variável estática para manter um exemplo httpClient. HttpClient é relativamente caro para criar, e queremos evitar ter que fazer isso para cada invocação de funções.
```csharp
private static readonly HttpClient httpClient = new HttpClient();
```
* Pode utilizar as credenciais de identidade geridas na função Azure.
```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
```
* Adicione uma variável local _DigitalTwinsClient_ dentro da sua função para manter a sua instância de cliente Azure Digital Twins no projeto de função. *Não* faça esta variável estática dentro da sua classe.
```csharp
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
```
* Adicione uma verificação nula para _adtInstanceUrl_ e envolva a sua lógica de função num bloco de captura para apanhar quaisquer exceções.

Após estas alterações, o seu código de função será semelhante ao seguinte:

```csharp
// Default URL for triggering event grid function in the local environment.
// http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
using System;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;

namespace adtIngestFunctionSample
{
    public class Function1
    {
        //Your Digital Twin URL is stored in an application setting in Azure Functions
        private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("TwinsFunction")]
        public void Run([EventGridTrigger] EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
            if (adtInstanceUrl == null) log.LogError("Application setting \"ADT_SERVICE_URL\" not set");
            try
            {
                //Authenticate with Digital Twins
                ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
                DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                log.LogInformation($"ADT service client connection created.");
                /*
                * Add your business logic here
                */
            }
            catch (Exception e)
            {
                log.LogError(e.Message);
            }

        }
    }
}
```

## <a name="publish-the-function-app-to-azure"></a>Publique a app de função para a Azure

Para publicar a aplicação de função para a Azure, selecione à direita o projeto de função (não a solução) no Solution Explorer e escolha **publicar**.

> [!IMPORTANT] 
> A publicação de uma função Azure incorrerá em encargos adicionais na sua subscrição, independente da Azure Digital Twins.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function.png" alt-text="Estúdio Visual: novo diálogo de projeto":::

Selecione **Azure** como o alvo de publicação e selecione **Next**.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-1.png" alt-text="Estúdio Visual: novo diálogo de projeto":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-2.png" alt-text="Estúdio Visual: novo diálogo de projeto":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-3.png" alt-text="Estúdio Visual: novo diálogo de projeto":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-4.png" alt-text="Estúdio Visual: novo diálogo de projeto":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-5.png" alt-text="Estúdio Visual: novo diálogo de projeto":::

Na página seguinte, insira o nome desejado para a nova aplicação de função, um grupo de recursos e outros detalhes.
Para que a sua aplicação Functions possa aceder ao Azure Digital Twins, precisa de ter uma identidade gerida pelo sistema e ter permissões para aceder à sua instância Azure Digital Twins.

Em seguida, pode configurar o acesso de segurança para a função utilizando o portal CLI ou Azure. Escolha qualquer um destes métodos:

## <a name="set-up-security-access-for-the-azure-function-app"></a>Confiem o acesso à segurança da aplicação de função Azure
Pode configurar o acesso à segurança da aplicação de função Azure utilizando uma destas opções:

### <a name="option-1-set-up-security-access-for-the-azure-function-app-using-cli"></a>Opção 1: Configurar o acesso à segurança para a aplicação de função Azure utilizando o CLI

O esqueleto da função Azure de exemplos anteriores requer que lhe seja passado um símbolo portador, de modo a poder autenticar-se com as Gémeas Digitais Azure. Para se certificar de que este token ao portador é passado, terá de configurar [a Identidade de Serviço Gerido (MSI)](../active-directory/managed-identities-azure-resources/overview.md) para a aplicação de função. Isto só precisa de ser feito uma vez para cada aplicação de função.

Pode criar identidade gerida pelo sistema e atribuir a identidade da aplicação de função ao papel _de Azure Digital Twins Owner (Preview)_ para a sua instância Azure Digital Twins. Isto dará à aplicação de função permissão no caso de realizar atividades de data plane. Em seguida, torne o URL da instância Azure Digital Twins acessível à sua função definindo uma variável ambiental.

 Utilize [a Azure Cloud Shell](https://shell.azure.com) para executar os comandos.

Utilize o seguinte comando para criar a identidade gerida pelo sistema. Tome nota do campo _principalid_ na saída.

```azurecli 
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>   
```
Utilize o valor _principalid_ no seguinte comando para atribuir a identidade da aplicação de função ao papel _Azure Digital Twins Owner (Preview)_ para a sua instância Azure Digital Twins.

```azurecli 
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```
Por último, pode tornar o URL da sua instância Azure Digital Twins acessível à sua função, definindo uma variável ambiental. Para obter mais informações sobre a definição de variáveis ambientais, consulte [*variáveis ambientais*](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables). 

> [!TIP]
> O URL da instância Azure Digital Twins é feito adicionando *https://* ao início do *nome de anfitrião*da sua instância Azure Digital Twins . Para ver o nome do anfitrião, juntamente com todas as propriedades do seu caso, pode `az dt show --dt-name <your-Azure-Digital-Twins-instance>` correr.

```azurecli 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-hostname>"
```
### <a name="option-2-set-up-security-access-for-the-azure-function-app-using-azure-portal"></a>Opção 2: Configurar o acesso à segurança para a aplicação de função Azure utilizando o portal Azure

Um sistema atribuído à identidade gerida permite que os recursos do Azure autentem serviços na nuvem (por exemplo, Azure Key Vault) sem armazenar credenciais em código. Uma vez ativados, todas as permissões necessárias podem ser concedidas através do controlo de acesso baseado em funções da Azure. O ciclo de vida deste tipo de identidade gerida está ligado ao ciclo de vida deste recurso. Além disso, cada recurso (por exemplo, Máquina Virtual) só pode ter um sistema atribuído à identidade gerida.

No [portal Azure](https://portal.azure.com/), procure _uma aplicação_ de função na barra de pesquisa com o nome da aplicação de função que criou anteriormente. Selecione a *App de Função* da lista. 

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-functionapp.png" alt-text="Estúdio Visual: novo diálogo de projeto":::

Na janela da aplicação de funções, selecione _Identidade_ na barra de navegação à esquerda para permitir a identidade gerida.
No _separador System atribuído,_ altere o _Estado_ para On e guarde-o. _save_ Você verá um pop-up para _Ativar o sistema atribuído identidade gerida_.
Selecione o botão _Sim._ 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Estúdio Visual: novo diálogo de projeto":::

Pode verificar nas notificações que a sua função está registada com sucesso no Azure Ative Directory.

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Estúdio Visual: novo diálogo de projeto":::

Note também o **ID** do objeto mostrado na página _Identidade,_ uma vez que será usado na secção seguinte.

:::image type="content" source="media/how-to-create-azure-function/object-id.png" alt-text="Estúdio Visual: novo diálogo de projeto":::

### <a name="assign-access-roles-using-azure-portal"></a>Atribuir funções de acesso usando o portal Azure

Selecione o botão _de atribuições de funções Azure,_ que abrirá a página *de atribuições de funções Azure.* Em seguida, _selecione + Adicionar atribuição de função (Pré-visualização)_.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Estúdio Visual: novo diálogo de projeto":::

Na página _de atribuição de funções adicionar (Pré-visualização)_ que se abre, selecione:

* _Âmbito_: grupo de recursos
* _Subscrição_: selecione a sua subscrição Azure
* _Grupo de recursos_: selecione o seu grupo de recursos a partir do dropdown
* _Função_: selecione _Azure Digital Twins Owner (Preview)_ from the dropdown

Em seguida, guarde os seus dados premindo o botão _Guardar._

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Estúdio Visual: novo diálogo de projeto":::

### <a name="configure-application-settings-using-azure-portal"></a>Configurar configurações de aplicações usando o portal Azure

Pode tornar o URL da sua instância Azure Digital Twins acessível à sua função definindo uma variável ambiental. Para obter mais informações sobre este ponto, consulte [*as variáveis Ambiente.*](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables) As configurações da aplicação são expostas como variáveis ambientais para aceder à instância de gémeos digitais. 

Terá de ADT_INSTANCE_URL para criar uma definição de aplicação.

Você pode obter ADT_INSTANCE_URL, anexando **_https://_** ao nome de anfitrião do seu caso. No portal Azure, pode encontrar o nome de anfitrião de exemplo dos seus gémeos digitais procurando o seu exemplo na barra de pesquisa. Em seguida, _selecione visão geral_ na barra de navegação esquerda para ver o _nome anfitrião_. Copie este valor para criar uma definição de aplicação.

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Estúdio Visual: novo diálogo de projeto":::

Pode agora criar uma definição de aplicação seguindo os passos abaixo:

* Procure a sua função Azure utilizando o nome da função na barra de pesquisa e selecione a função da lista
* Selecione _Configuração_ na barra de navegação à esquerda para criar uma nova definição de aplicação
* No separador definições de _aplicação,_ selecione _+ Nova definição de aplicação_

:::image type="content" source="media/how-to-create-azure-function/search-for-azure-function.png" alt-text="Estúdio Visual: novo diálogo de projeto":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Estúdio Visual: novo diálogo de projeto":::

Na janela que se abre, utilize o valor copiado de cima para criar uma definição de aplicação. \
_Nome:_  ADT_SERVICE_URL \
_Valor_ : https://{your-azure-digital-twins-hostname}

Selecione _OK_ para criar uma definição de aplicação.

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Estúdio Visual: novo diálogo de projeto":::

Pode ver as definições da sua aplicação com o nome da aplicação no campo _Nome._ Em seguida, guarde as definições da sua aplicação selecionando o botão _Guardar._

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Estúdio Visual: novo diálogo de projeto":::

Quaisquer alterações às definições da aplicação precisam de um reinício da aplicação. _Selecione Continue_ a reiniciar a sua aplicação.

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Estúdio Visual: novo diálogo de projeto":::

Pode ver que as definições de aplicação são atualizadas selecionando o ícone _de Notificações._ Se a definição da sua aplicação não for criada, pode voltar a tentar adicionar uma definição de aplicação seguindo o processo acima.

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Estúdio Visual: novo diálogo de projeto":::

## <a name="next-steps"></a>Passos seguintes

Neste artigo, seguiu os passos para configurar uma função Azure para uso com a Azure Digital Twins. Em seguida, pode subscrever a sua função Azure para Event Grid, para ouvir num ponto final. Este ponto final pode ser:
* Um ponto final da Grade de Eventos anexado às Gémeas Digitais Azure para processar mensagens provenientes da própria Azure Digital Twins (tais como mensagens de mudança de propriedade, mensagens de telemetria geradas por [gémeos digitais](concepts-twins-graph.md) no gráfico gémeo ou mensagens de ciclo de vida)
* Os tópicos do sistema IoT utilizados pelo IoT Hub para enviar telemetria e outros eventos de dispositivos
* Um ponto final da Grade de Eventos recebendo mensagens de outros serviços

Em seguida, veja como basear-se na sua função básica de Azure para ingerir dados do IoT Hub em Azure Digital Twins:
* [*Como fazer: Ingerir telemetria a partir do IoT Hub*](how-to-ingest-iot-hub-data.md)