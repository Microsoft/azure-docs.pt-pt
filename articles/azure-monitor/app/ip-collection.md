---
title: Coleção de endereços IP Azure Application Insights / Microsoft Docs
description: Compreender como os endereços IP e geolocalização são tratados com Insights de Aplicação Azure
ms.topic: conceptual
ms.date: 09/11/2019
ms.custom: devx-track-javascript
ms.openlocfilehash: 28a7fa50a06dc8b80c7d8dd284cd88ebe4645da6
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371656"
---
# <a name="geolocation-and-ip-address-handling"></a>Geolocalização e tratamento de endereços IP

Este artigo explica como a procura de geolocalização e o tratamento de endereços IP ocorrem em Insights de Aplicação, juntamente com como modificar o comportamento padrão.

## <a name="default-behavior"></a>Comportamento predefinido

Por predefinição, os endereços IP são recolhidos temporariamente, mas não armazenados em Insights de Aplicação. O processo básico é o seguinte:

Os endereços IP são enviados para o Application Insights como parte dos dados da telemetria. Ao chegar ao ponto final de ingestão em Azure, o endereço IP é utilizado para realizar uma procura de geolocalização utilizando [o GeoLite2 da MaxMind](https://dev.maxmind.com/geoip/geoip2/geolite2/). Os resultados desta procura são utilizados para povoar os seguintes `client_City` `client_StateOrProvince` campos, . `client_CountryOrRegion` . . . . . Neste ponto, o endereço IP é descartado e `0.0.0.0` é escrito para o `client_IP` campo.

* Telemetria do navegador: Recolhemos temporariamente o endereço IP do remetente. O endereço IP é calculado pelo ponto final de ingestão.
* Telemetria do servidor: O módulo Application Insights recolhe temporariamente o endereço IP do cliente. Não é recolhido se `X-Forwarded-For` estiver definido.

Este comportamento é por design para ajudar a evitar a recolha desnecessária de dados pessoais. Sempre que possível, recomendamos evitar a recolha de dados pessoais. 

## <a name="overriding-default-behavior"></a>Comportamento predefinido primordial

Embora o comportamento padrão seja minimizar a recolha de dados pessoais, ainda oferecemos a flexibilidade para recolher e armazenar dados de endereços IP. Antes de optar por armazenar quaisquer dados pessoais, como endereços IP, recomendamos vivamente verificar se este não infringe quaisquer requisitos de conformidade ou regulamentos locais a que possa estar sujeito. Para saber mais sobre o tratamento de dados pessoais em Application Insights, consulte a [orientação para dados pessoais.](../platform/personal-data-mgmt.md)

## <a name="storing-ip-address-data"></a>Armazenar dados de endereços IP

Para permitir a recolha e armazenamento ip, a `DisableIpMasking` propriedade do componente Application Insights deve ser definida para `true` . Esta propriedade pode ser definida através dos modelos Azure Resource Manager ou através da chamada REST API. 

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>Portal 

Se apenas precisar de modificar o comportamento de um único recurso Application Insights, a forma mais fácil de o conseguir é através do portal Azure.  

1. Vá ao seu recurso de **Settings**insights de aplicação >  >  **modelo de exportação de** definições 

    ![Modelo de exportação](media/ip-collection/export-template.png)

2. Selecione **Implementar**

    ![Botão de implantação realçado a vermelho](media/ip-collection/deploy.png)

3. Selecione **o modelo de edição**. (Se o seu modelo tiver propriedades ou recursos adicionais que não apareçam neste modelo de exemplo, proceda com cuidado para garantir que todos os recursos aceitarão a implementação do modelo como uma alteração/atualização incremental.)

    ![Editar Modelo](media/ip-collection/edit-template.png)

4. Faça as seguintes alterações no json para o seu recurso e, em seguida, clique em **Guardar**:

    ![Screenshot adiciona uma vírgula após "IbizaAIExtension" e adiciona uma nova linha abaixo com "DisableIpMasking": verdadeiro](media/ip-collection/save.png)

    > [!WARNING]
    > Se sentir um erro que diga: ** _O grupo de recursos está num local que não é suportado por um ou mais recursos no modelo. Por favor, escolha um grupo de recursos diferente._** Selecione temporariamente um grupo de recursos diferente do dropdown e, em seguida, reescorê-lo o seu grupo de recursos original para resolver o erro.

5. **Selecione Eu concordo**  >  **Compra.** 

    ![Editar Modelo](media/ip-collection/purchase.png)

    Neste caso, não está a ser adquirido nada de novo, estamos apenas a atualizar a configuração do recurso Desconsequição de Aplicações existente.

6. Uma vez que a implementação esteja completa, novos dados de telemetria serão registados.

    Se você selecionasse e editasse novamente o modelo, só veria o modelo padrão e não veria a sua nova propriedade e o seu valor associado. Se não estiver a ver os dados do endereço IP e quiser confirmar que `"DisableIpMasking": true` está definido. Executar o seguinte PowerShell: `Fabrikam-dev` (Substitua pelo nome apropriado do grupo de recursos e recursos.)
    
    ```powershell
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    Como resultado, será devolvida uma lista de propriedades. Uma das propriedades deve `DisableIpMasking: true` ler. Se executar o PowerShell antes de implantar o novo imóvel com o Azure Resource Manager, a propriedade não existirá.

### <a name="rest-api"></a>Repouso API

A carga útil [da API](/rest/api/azure/) para fazer as mesmas modificações é a seguinte:

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>Inicializador de telemetria

Se precisar de uma alternativa mais flexível do que `DisableIpMasking` gravar a totalidade ou parte dos endereços IP, pode utilizar um [inicializador de telemetria](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) para copiar a totalidade ou parte do IP para um campo personalizado. 

### <a name="aspnet--aspnet-core"></a>Núcleo ASP.NET / ASP.NET

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> Se não conseguir `ISupportProperties` aceder, verifique e certifique-se de que está a executar a mais recente versão estável do Application Insights SDK. `ISupportProperties`destinam-se a valores elevados de cardinaldade, ao passo que `GlobalProperties` são mais adequados para valores de baixa cardinaldade, como o nome da região, o nome do ambiente, etc. 

### <a name="enable-telemetry-initializer-for-aspnet"></a>Ativar o inicializador de telemetria para ASP.NET

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>Ativar o inicializador de telemetria para ASP.NET Core

Pode criar o seu inicializador de telemetria da mesma forma para ASP.NET Core como ASP.NET mas para ativar o inicializador, utilize o seguinte exemplo como referência:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```

### <a name="client-side-javascript"></a>JavaScript do lado do cliente

Ao contrário dos SDKs do lado do servidor, o Javascript SDK do lado do cliente não calcula o endereço IP. Por padrão, o cálculo do endereço IP para telemetria do lado do cliente é realizado no ponto final de ingestão em Azure após a chegada da telemetria. Isto significa que se você estava enviando dados do lado do cliente para um proxy e, em seguida, reencaminhando para o ponto final de ingestão, o cálculo do endereço IP pode mostrar o endereço IP do representante e não do cliente. Se não for utilizado nenhum representante, isto não deve ser um problema.

Se desejar calcular o endereço IP diretamente no lado do cliente, terá de adicionar a sua própria lógica personalizada para realizar este cálculo e utilizar o resultado para definir a `ai.location.ip` etiqueta. Quando `ai.location.ip` definido, o cálculo do endereço IP não é realizado pelo ponto final de ingestão e o endereço IP fornecido é honrado e utilizado para a realização da procura de geo. Neste cenário, o endereço IP ainda será eliminado por padrão. 

Para reter todo o endereço IP calculado a partir da sua lógica personalizada, poderá utilizar um inicializador de telemetria que copie os dados de endereço IP fornecidos num `ai.location.ip` campo personalizado separado. Mas, mais uma vez, ao contrário dos SDKs do lado do servidor, sem depender de bibliotecas de terceiros ou da sua lógica de cobrança IP personalizada do lado do cliente, o SDK do lado do cliente não calculará o IP para si.    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

### <a name="view-the-results-of-your-telemetry-initializer"></a>Veja os resultados do seu inicializador de telemetria

Se, em seguida, desencadear um novo tráfego contra o seu site e esperar aproximadamente 2-5 minutos para garantir que tinha tempo para ser ingerido, pode executar uma consulta kusto para ver se a recolha de endereços IP está funcionando:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Os endereços IP recentemente recolhidos devem aparecer na `customDimensions_client-ip` coluna. A `client-ip` coluna predefinida ainda terá todos os 4 octetos a zero ou apenas a exibir os três primeiros octetos, dependendo da forma como configura a recolha de endereços IP ao nível do componente. Se estiver a testar localmente depois de implementar o inicializador de telemetria e o valor que vê `customDimensions_client-ip` é `::1` este comportamento esperado. `::1`representa o endereço loopback no IPv6. É equivalente ao `127.0.01` IPv4 e é o resultado que verá ao testar a partir de local.

## <a name="next-steps"></a>Próximos Passos

* Saiba mais sobre [a recolha de dados pessoais](../platform/personal-data-mgmt.md) no Application Insights.

* Saiba mais sobre como funciona a [recolha de endereços IP](https://apmtips.com/posts/2016-07-05-client-ip-address/) no Application Insights. (Esta é uma publicação de blog externa mais antiga escrita por um dos nossos engenheiros. Antecede o comportamento predefinido atual em que o endereço IP é registado como `0.0.0.0` , mas vai em maior profundidade na mecânica do incorporado `ClientIpHeaderTelemetryInitializer` .)
