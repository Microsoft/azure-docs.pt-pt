---
title: Coleção de endereços IP da Aplicação Azure Insights Microsoft Docs
description: Compreender como os endereços IP e a geolocalização são tratados com insights de aplicação azure
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 969061ec89ddd0f13caa675bc324207c6c5d8843
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656522"
---
# <a name="geolocation-and-ip-address-handling"></a>Gestão de endereços de geolocalização e IP

Este artigo explica como a procura de geolocalização e o tratamento de endereços IP ocorrem em Insights de Aplicação, juntamente com como modificar o comportamento padrão.

## <a name="default-behavior"></a>Comportamento predefinido

Por padrão, os endereços IP são temporariamente recolhidos, mas não armazenados em Insights de Aplicação. O processo básico é o seguinte:

Os endereços IP são enviados para Application Insights como parte de dados de telemetria. Ao chegar ao ponto final de ingestão em Azure, o endereço IP é usado para realizar um lookup de geolocalização usando [GeoLite2 da MaxMind](https://dev.maxmind.com/geoip/geoip2/geolite2/). Os resultados desta procura são usados `client_City` `client_StateOrProvince`para `client_CountryOrRegion`povoar os seguintes campos, . Neste ponto, o endereço IP `0.0.0.0` é descartado e `client_IP` está escrito no campo.

* Telemetria do navegador: Recolhemos temporariamente o endereço IP do remetente. O endereço IP é calculado pelo ponto final da ingestão.
* Telemetria do servidor: O módulo Application Insights recolhe temporariamente o endereço IP do cliente. Não é recolhido `X-Forwarded-For` se estiver definido.

Este comportamento é por design para ajudar a evitar recolha desnecessária de dados pessoais. Sempre que possível, recomendamos evitar a recolha de dados pessoais. 

## <a name="overriding-default-behavior"></a>Comportamento predefinido

Embora o comportamento predefinido seja para minimizar a recolha de dados pessoais, ainda oferecemos a flexibilidade para recolher e armazenar dados de endereçoip. Antes de optar por armazenar quaisquer dados pessoais como endereços IP, recomendamos vivamente verificar se isso não quebra quaisquer requisitos de conformidade ou regulamentos locais a que possa estar sujeito. Para saber mais sobre o tratamento de dados pessoais em Insights de Aplicação, consulte as [orientações para dados pessoais](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt).

## <a name="storing-ip-address-data"></a>Armazenar dados de endereçoip

Para permitir a recolha e `DisableIpMasking` armazenamento ip, a propriedade do `true`componente Application Insights deve ser definida para . Esta propriedade pode ser definida através de modelos de Gestor de Recursos Azure ou chamando a API REST. 

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

Se apenas necessitar de modificar o comportamento de um único recurso Application Insights, a forma mais fácil de o conseguir é através do portal Azure.  

1. Vá ao seu recurso insights de aplicação > modelo de**exportação** **de configurações** >  

    ![Modelo de exportação](media/ip-collection/export-template.png)

2. Selecione **Implementar**

    ![Botão de desdobrar realçado a vermelho](media/ip-collection/deploy.png)

3. Selecione **modelo de edição**. (Se o seu modelo tiver propriedades ou recursos adicionais que não apareçam neste modelo de exemplo, proceda com cuidado para garantir que todos os recursos aceitarão a implementação do modelo como uma mudança/atualização incremental.)

    ![Editar Modelo](media/ip-collection/edit-template.png)

4. Faça as seguintes alterações no json para o seu recurso e, em seguida, clique em **Guardar:**

    ![Screenshot adiciona uma vírgula após "IbizaAIExtension" e adiciona uma nova linha abaixo com "DisableIpMasking": verdadeiro](media/ip-collection/save.png)

    > [!WARNING]
    > Se experimentar um erro que diz: ** _O grupo de recursos está num local que não é suportado por um ou mais recursos no modelo. Por favor, escolha um grupo de recursos diferente._** Selecione temporariamente um grupo de recursos diferente a partir do dropdown e, em seguida, reselecione o seu grupo de recursos original para resolver o erro.

5. Selecione **concordo que** > **comprar.** 

    ![Editar Modelo](media/ip-collection/purchase.png)

    Neste caso, nada de novo está a ser adquirido, estamos apenas a atualizar o config do recurso Desinformação de Aplicações existente.

6. Uma vez concluída a implementação, serão registados novos dados de telemetria.

    Se voltasse a selecionar e editar o modelo, só veria o modelo predefinido e não veria a sua propriedade recém-adicionada e o seu valor associado. Se não estiver a ver os dados `"DisableIpMasking": true` do endereço IP e quiser confirmar que está definido. Executar o seguinte PowerShell: (Substitua `Fabrikam-dev` pelo nome adequado do grupo de recursos e recursos.)
    
    ```powershell
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    Como resultado, uma lista de propriedades será devolvida. Uma das propriedades `DisableIpMasking: true`deve ler. Se executar o PowerShell antes de implementar a nova propriedade com o Azure Resource Manager, a propriedade não existirá.

### <a name="rest-api"></a>API de descanso

A carga útil [da API restante](https://docs.microsoft.com/rest/api/azure/) para fazer as mesmas modificações é a seguinte:

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

Se precisar de uma `DisableIpMasking` alternativa mais flexível do que gravar a toda ou parte dos endereços IP, pode utilizar um inicializador de [telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) para copiar a toda ou parte do IP para um campo personalizado. 

### <a name="aspnet--aspnet-core"></a>ASP.NET / núcleo ASP.NET

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
> Se não conseguir aceder, `ISupportProperties`verifique e certifique-se de que está a executar a mais recente versão estável do SDK de Insights de Aplicação. `ISupportProperties`destinam-se a valores elevados de cardeal, enquanto `GlobalProperties` que são mais adequados para valores de baixa cardealidade, como nome da região, nome do ambiente, etc. 

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

Pode criar o seu inicializador de telemetria da mesma forma para ASP.NET Core como ASP.NET mas para ativar o inicializador, utilize o seguinte exemplo de referência:

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

Ao contrário dos SDKs do lado do servidor, o Javascript SDK do lado do cliente não calcula o endereço IP. Por predefinição, o cálculo do endereço IP para telemetria do lado do cliente é realizado no ponto final de ingestão em Azure após a chegada da telemetria. Isto significa que se você estava enviando dados do lado do cliente para um proxy e, em seguida, reencaminhamento para o ponto final de ingestão, o cálculo do endereço IP pode mostrar o endereço IP do representante e não o cliente. Se não for utilizado qualquer procuração, isto não deve ser um problema.

Se desejar calcular o endereço IP diretamente no lado do cliente, terá de adicionar a sua `ai.location.ip` própria lógica personalizada para realizar este cálculo e utilizar o resultado para definir a etiqueta. Quando `ai.location.ip` definido, o cálculo do endereço IP não é realizado pelo ponto final da ingestão e o endereço IP fornecido é honrado e utilizado para realizar a procura geo. Neste cenário, o endereço IP continuará a ser desfocado por padrão. 

Para reter todo o endereço IP calculado a partir da sua lógica personalizada, você poderia `ai.location.ip` usar um inicializador de telemetria que copiaria os dados de endereço IP que forneceu num campo personalizado separado. Mas, mais uma vez, ao contrário dos SDKs do lado do servidor, sem depender de bibliotecas de terceiros ou da sua lógica personalizada de recolha IP do lado do cliente, o SDK do lado do cliente não calculará o IP para si.    


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

Se, em seguida, desencadear um novo tráfego contra o seu site e esperar aproximadamente 2-5 minutos para garantir que teve tempo de ser ingerido, pode fazer uma consulta de Kusto para ver se a recolha de endereços IP está funcionando:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Os endereços IP recentemente `customDimensions_client-ip` recolhidos devem aparecer na coluna. A `client-ip` coluna predefinida ainda terá todos os 4 octetos sem parar ou apenas exibir os três primeiros octetos dependendo de como configurar a recolha de endereços IP ao nível do componente. Se estiver a testar localmente depois de implementar o inicializador de telemetria e o valor que vê `customDimensions_client-ip` é `::1` um comportamento esperado. `::1`representa o endereço de loopback no IPv6. É equivalente `127.0.01` ao IPv4 e é o resultado que verá quando testar a partir do local.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre a recolha de [dados pessoais](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt) em Insights de Aplicação.

* Saiba mais sobre como funciona a recolha de [endereços IP](https://apmtips.com/blog/2016/07/05/client-ip-address/) em Application Insights. (Este é um post de blog externo mais antigo escrito por um dos nossos engenheiros. Antecede o comportamento padrão atual em que `0.0.0.0`o endereço IP é registado como , mas `ClientIpHeaderTelemetryInitializer`entra em maior profundidade na mecânica do incorporado .)
