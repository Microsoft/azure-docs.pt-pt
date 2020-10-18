---
title: Configure monitorização para funções Azure
description: Saiba como ligar a sua aplicação de função a Application Insights para monitorização e como configurar a recolha de dados.
ms.date: 8/31/2020
ms.topic: how-to
ms.openlocfilehash: 4b2c4e23bf54dc87325af7068b287fad2f9314a0
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92169004"
---
# <a name="how-to-configure-monitoring-for-azure-functions"></a>Como configurar a monitorização para as funções do Azure

As Funções Azure integram-se com o Application Insights para melhor monitorizar as suas aplicações de função. Application Insights, uma funcionalidade do Azure Monitor, é um serviço extensível de Gestão de Desempenho de Aplicações (APM) que recolhe dados gerados pela sua aplicação de função, incluindo informações que a sua aplicação escreve para registos. A integração do Application Insights é normalmente ativada quando a sua aplicação de função é criada. Se a sua aplicação não tiver o conjunto de teclas de instrumentação, deve primeiro [ativar a integração do Application Insights.](#enable-application-insights-integration) 

Pode utilizar o Application Insights sem qualquer configuração personalizada. A configuração predefinida pode resultar em volumes elevados de dados. Se estiver a utilizar uma subscrição do Visual Studio Azure, poderá atingir a sua tampa de dados para Insights de Aplicação. Para saber mais sobre os custos da Application Insights, consulte [Gerir a utilização e os custos para a Application Insights.](../azure-monitor/app/pricing.md)

Mais tarde neste artigo, aprende-se a configurar e personalizar os dados que as suas funções enviam para o Application Insights. Para uma aplicação de função, o registo está configurado na [host.jsno] ficheiro. 

> [!NOTE]
> Pode utilizar definições de aplicação especialmente configuradas para representar configurações específicas num host.jsem ficheiro para um ambiente específico. Isto permite-lhe alterar efetivamente host.jsnas definições sem ter de reeditar os host.jsem arquivo no seu projeto. Para saber mais, consulte [Override host.jssobre valores](functions-host-json.md#override-hostjson-values).

## <a name="configure-categories"></a>Categorias de configuração

O madeireiro Azure Functions inclui uma *categoria* para cada registo. A categoria indica qual parte do código de execução ou o seu código de função escreveu o registo. As categorias diferem entre as versões 1.x e posterior. O gráfico seguinte descreve as principais categorias de registos que o tempo de execução cria. 

# <a name="v2x"></a>[v2.x+](#tab/v2)

| Categoria | Tabela | Descrição |
| ----- | ----- | ----- |
| **`Function.<YOUR_FUNCTION_NAME>`** | **dependências**| Os dados de dependência são automaticamente recolhidos para alguns serviços. Para corridas bem sucedidas, estes registos estão ao `Information` nível. Para saber mais, consulte [Dependências.](functions-monitoring.md#dependencies) As exceções são registadas ao `Error` nível. O tempo de execução também cria `Warning` registos de nível, como quando as mensagens de fila são enviadas para a [fila de venenos](functions-bindings-storage-queue-trigger.md#poison-messages). | 
| **`Function.<YOUR_FUNCTION_NAME>`** | **costumesMetricos**<br/>**costumesEvents** | C# e JavaScript SDKs permitem-lhe recolher métricas personalizadas e registar eventos personalizados. Para saber mais, consulte [os dados de telemetria personalizados.](functions-monitoring.md#custom-telemetry-data)|
| **`Function.<YOUR_FUNCTION_NAME>`** | **vestígios**| Inclui registos de função iniciados e concluídos para execuções de funções específicas. Para corridas bem sucedidas, estes registos estão ao `Information` nível. As exceções são registadas ao `Error` nível. O tempo de execução também cria `Warning` registos de nível, como quando as mensagens de fila são enviadas para a [fila de venenos](functions-bindings-storage-queue-trigger.md#poison-messages). | 
| **`Function.<YOUR_FUNCTION_NAME>.User`** | **vestígios**| Registos gerados pelo utilizador, que podem ser qualquer nível de registo. Para saber mais sobre escrever para registar as suas funções, consulte [escrever para registar registos](functions-monitoring.md#writing-to-logs). | 
| **`Host.Aggregator`** | **costumesMetricos** | Estes registos gerados pelo tempo de execução fornecem contagens e médias de invocações de funções durante um período de tempo [configurável.](#configure-the-aggregator) O período de incumprimento é de 30 segundos ou 1.000 resultados, o que vier primeiro. Exemplos são o número de corridas, taxa de sucesso e duração. Todos estes registos estão escritos ao `Information` nível. Se filtrar ou `Warning` ultrapassar, não verá nenhum destes dados. |
| **`Host.Results`** | **pedidos** | Estes registos gerados pelo tempo de execução indicam o sucesso ou falha de uma função. Todos estes registos estão escritos ao `Information` nível. Se filtrar ou `Warning` ultrapassar, não verá nenhum destes dados. |
| **`Microsoft`** | **vestígios** | Categoria de registo totalmente qualificado que reflete um componente de tempo de execução .NET invocado pelo anfitrião.  |
| **`Worker`** | **vestígios** | Registos gerados pelo processo de trabalho linguístico para non-.NET línguas. Os registos de trabalhadores linguísticos também podem ser registados numa `Microsoft.*` categoria, como `Microsoft.Azure.WebJobs.Script.Workers.Rpc.RpcFunctionInvocationDispatcher` . Estes registos estão escritos ao `Information` nível.|

# <a name="v1x"></a>[v1.x](#tab/v1)

| Categoria | Tabela | Descrição |
| ----- | ----- | ----- |
| **`Function`** | **vestígios**| Registos gerados pelo utilizador, que podem ser qualquer nível de registo. Para saber mais sobre escrever para registar as suas funções, consulte [escrever para registar registos](functions-monitoring.md#writing-to-logs). | 
| **`Host.Aggregator`** | **costumesMetricos** | Estes registos gerados pelo tempo de execução fornecem contagens e médias de invocações de funções durante um período de tempo [configurável.](#configure-the-aggregator) O período de incumprimento é de 30 segundos ou 1.000 resultados, o que vier primeiro. Exemplos são o número de corridas, taxa de sucesso e duração. Todos estes registos estão escritos ao `Information` nível. Se filtrar ou `Warning` ultrapassar, não verá nenhum destes dados. |
| **`Host.Executor`** | **vestígios** | Inclui **funções iniciadas** e **funções de** registos preenchidos para execuções de funções específicas. Para corridas bem sucedidas, estes registos estão `Information` nivelados. As exceções são registadas ao `Error` nível. O tempo de execução também cria `Warning` registos de nível, como quando as mensagens de fila são enviadas para a [fila de venenos](functions-bindings-storage-queue-trigger.md#poison-messages).  |
| **`Host.Results`** | **pedidos** | Estes registos gerados pelo tempo de execução indicam o sucesso ou falha de uma função. Todos estes registos estão escritos ao `Information` nível. Se filtrar ou `Warning` ultrapassar, não verá nenhum destes dados. |

---

A coluna **tabela** indica a que tabela em Insights de Aplicação o registo está escrito. 

## <a name="configure-log-levels"></a>Configure os níveis de registo

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

Para cada categoria, indica o nível mínimo de registo a enviar. A host.jsnas definições varia consoante a versão de [tempo de execução de Funções](functions-versions.md). 

O exemplo abaixo define a exploração madeireira com base nas seguintes regras:

+ Para registos de `Host.Results` `Function` ou, apenas registar eventos a `Error` um nível superior ou superior. 
+ Para registos `Host.Aggregator` de, registar todas as métricas geradas `Trace` ().
+ Para todos os outros registos, incluindo registos de utilizador, regista apenas `Information` nível e eventos mais altos.

# <a name="v2x"></a>[v2.x+](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

# <a name="v1x"></a>[v1.x](#tab/v1) 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

---

Se [host.js] inclui vários registos que começam com a mesma corda, os registos mais definidos são combinados primeiro. Considere o seguinte exemplo que regista tudo no tempo de execução, `Host.Aggregator` exceto, ao `Error` nível:

# <a name="v2x"></a>[v2.x+](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

# <a name="v1x"></a>[v1.x](#tab/v1) 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

---

Pode utilizar uma definição de nível de registo `None` para evitar que quaisquer registos sejam escritos para uma categoria. 

## <a name="configure-the-aggregator"></a>Configure o agregador

Como indicado na secção anterior, o tempo de execução agrega dados sobre execuções de funções durante um período de tempo. O período de incumprimento é de 30 segundos ou 1.000 corridas, o que vier primeiro. Pode configurar esta definição na [host.jsno] ficheiro.  Eis um exemplo:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Amostragem de configure

A Application Insights tem uma funcionalidade [de amostragem](../azure-monitor/app/sampling.md) que pode protegê-lo de produzir demasiados dados de telemetria em execuções concluídas em momentos de carga máxima. Quando a taxa de execuções recebidas excede um limiar especificado, os Insights de Aplicação começam a ignorar aleatoriamente algumas das execuções recebidas. A definição predefinição para o número máximo de execuções por segundo é de 20 (cinco na versão 1.x). Pode configurar a amostragem em [host.js.](./functions-host-json.md#applicationinsights)  Eis um exemplo:

# <a name="v2x"></a>[v2.x+](#tab/v2)

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20,
        "excludedTypes": "Request;Exception"
      }
    }
  }
}
```


Pode excluir certos tipos de telemetria da amostragem. Neste exemplo, os dados do tipo `Request` `Exception` estão excluídos da amostragem. Isto garante que *todas as* execuções de funções (pedidos) e exceções são registadas enquanto outros tipos de telemetria permanecem sujeitos a amostragem. 

# <a name="v1x"></a>[v1.x](#tab/v1)  

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```
---

Para saber mais, consulte [a Amostragem em Insights de Aplicação.](../azure-monitor/app/sampling.md)

## <a name="configure-scale-controller-logs"></a>Configure registos de controlador de escala

_Esta funcionalidade está em pré-visualização._ 

Pode ter o controlador de [escala Azure Functions](./functions-scale.md#runtime-scaling) emite registos para insights de aplicação ou para armazenamento blob para entender melhor as decisões que o controlador de escala está a tomar para a sua aplicação de função.

Para ativar esta funcionalidade, adicione uma definição de aplicação nomeada `SCALE_CONTROLLER_LOGGING_ENABLED` para as definições da aplicação da sua função. O valor desta definição deve ser do `<DESTINATION>:<VERBOSITY>` formato, com base no seguinte:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

Por exemplo, o seguinte comando Azure CLI liga a registar verbose do controlador de escala para Insights de Aplicação:

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:Verbose
```

Neste exemplo, substitua `<FUNCTION_APP_NAME>` e pelo nome da sua app de `<RESOURCE_GROUP_NAME>` função e pelo nome do grupo de recursos, respectivamente. 

O seguinte comando Azure CLI desativa o registo, definindo a verbosidade `None` para:

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:None
```

Também pode desativar a sessão, removendo a `SCALE_CONTROLLER_LOGGING_ENABLED` definição utilizando o seguinte comando Azure CLI:

```azurecli-interactive
az functionapp config appsettings delete --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--setting-names SCALE_CONTROLLER_LOGGING_ENABLED
```

## <a name="enable-application-insights-integration"></a>Ativar a integração do Application Insights

Para que uma aplicação de função envie dados para o Application Insights, precisa de conhecer a chave de instrumentação de um recurso Application Insights. A chave deve estar numa definição de aplicação chamada **APPINSIGHTS_INSTRUMENTATIONKEY**.

Quando cria a sua aplicação de função [no portal Azure](functions-create-first-azure-function.md), a partir da linha de comando utilizando [ferramentas principais de funções Azure](functions-create-first-azure-function-azure-cli.md), ou utilizando [o Código do Estúdio Visual,](functions-create-first-function-vs-code.md)a integração de Insights de Aplicação é ativada por padrão. O recurso Application Insights tem o mesmo nome que a sua aplicação de função, e é criado na mesma região ou na região mais próxima.

### <a name="new-function-app-in-the-portal"></a>Nova aplicação de função no portal

Para rever o recurso Application Insights que está a ser criado, selecione-o para expandir a janela **'Insights de Aplicação'.** Pode alterar o nome de **novo recurso** ou escolher uma **localização** diferente numa [geografia Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde pretende armazenar os seus dados.

![Ativar insights de aplicação ao criar uma aplicação de função](media/functions-monitoring/enable-ai-new-function-app.png)

Quando escolhe **Criar**, é criado um recurso Application Insights com a sua aplicação de função, que tem o `APPINSIGHTS_INSTRUMENTATIONKEY` conjunto nas definições de aplicação. Tudo está pronto para ir.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Adicione a uma aplicação de função existente 

Se um recurso Application Insights não foi criado com a sua aplicação de função, use os seguintes passos para criar o recurso. Em seguida, pode adicionar a chave de instrumentação desse recurso como uma definição de [aplicação](functions-how-to-use-azure-function-app-settings.md#settings) na sua aplicação de função.

1. No [portal Azure,](https://portal.azure.com)procure e selecione **a aplicação de funções**, e, em seguida, escolha a sua aplicação de função. 

1. Selecione a faixa **O Application Insights não está configurado** na parte superior da janela. Se não vir este banner, então a sua aplicação pode já ter o Application Insights ativado.

    :::image type="content" source="media/configure-monitoring/enable-application-insights.png" alt-text="Ativar a o Application Insights a partir do portal":::

1. Expanda **o seu recurso** e crie um recurso Application Insights utilizando as definições especificadas na tabela seguinte.  

    | Definição      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Novo nome do recurso** | Nome de aplicação exclusivo | É mais fácil utilizar o mesmo nome da aplicação de funções, que tem de ser exclusivo na subscrição. | 
    | **Localização** | Europa Ocidental | Se possível, utilize a mesma [região](https://azure.microsoft.com/regions/) da aplicação de funções, ou uma que esteja perto dessa região. |

    :::image type="content" source="media/configure-monitoring/ai-general.png" alt-text="Ativar a o Application Insights a partir do portal":::

1. Selecione **Aplicar**. 

   O recurso do Application Insights é criado no mesmo grupo de recursos e subscrição da aplicação de funções. Após a criação do recurso, feche a janela do Application Insights.

1. Na sua aplicação de função, selecione **Configuração** em **Definições**e, em seguida, selecione **definições de Aplicação**. Se vir uma definição chamada `APPINSIGHTS_INSTRUMENTATIONKEY`, a integração do Application Insights está ativada para a aplicação de funções em execução no Azure. Se, por alguma razão, esta definição não existir, adicione-a utilizando a sua chave de instrumentação Application Insights como valor.

> [!NOTE]
> As primeiras versões de Funções utilizadas na monitorização incorporada, que já não é recomendada. Ao ativar a integração do Application Insights para uma aplicação de tal função, também deve [desativar a sessão embutimento.](#disable-built-in-logging)  

## <a name="disable-built-in-logging"></a>Desativar o registo integrado

Quando ativar o Application Insights, desative a exploração madeireira incorporada que utiliza o Azure Storage. O registo embutida é útil para testes com cargas de trabalho leves, mas não se destina a uma utilização de produção de alta carga. Para monitorização da produção, recomendamos a Application Insights. Se a exploração madeireira incorporada for utilizada na produção, o registo de registos pode estar incompleto devido ao estrangulamento no Azure Storage.

Para desativar a sessão de registos incorporados, elimine a definição da `AzureWebJobsDashboard` aplicação. Para obter informações sobre como eliminar as definições de aplicações no portal Azure, consulte a secção de **definições** de Aplicação de [Como gerir uma aplicação de função](functions-how-to-use-azure-function-app-settings.md#settings). Antes de eliminar a definição da aplicação, certifique-se de que nenhuma função existente na mesma aplicação de função utiliza a definição para gatilhos ou encadernações de armazenamento Azure.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre monitorização, consulte:

+ [Monitorizar as Funções do Azure](functions-monitoring.md)
+ [Analisar dados de telemetria de funções Azure em Insights de Aplicação](analyze-telemetry-data.md)
+ [Application Insights](/azure/application-insights/)


[host.js]: functions-host-json.md
