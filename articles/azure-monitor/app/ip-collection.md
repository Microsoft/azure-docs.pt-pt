---
title: Azure Application Insights RECOLHA de endereços IP | Microsoft Docs
description: Compreender como os endereços IP e geolocalização são tratados com Insights de Aplicação Azure
ms.topic: conceptual
ms.date: 09/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 91b3aa07720e39aa8aeeceb9c35e38205e7d7c76
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584071"
---
# <a name="geolocation-and-ip-address-handling"></a>Geolocalização e tratamento de endereços IP

Este artigo explica como a procura de geolocalização e o tratamento de endereços IP funcionam em Insights de Aplicação, juntamente com como modificar o comportamento padrão.

## <a name="default-behavior"></a>Comportamento predefinido

Por predefinição, os endereços IP são recolhidos temporariamente, mas não armazenados em Insights de Aplicação. O processo básico é o seguinte:

Quando a telemetria é enviada para a Azure, o endereço IP é utilizado para fazer uma procura de geolocalização utilizando [o GeoLite2 da MaxMind](https://dev.maxmind.com/geoip/geoip2/geolite2/). Os resultados desta procura são usados para povoar os `client_City` `client_StateOrProvince` campos, e `client_CountryOrRegion` . O endereço é então descartado e `0.0.0.0` é escrito para o `client_IP` campo.

* Telemetria do navegador: Recolhemos temporariamente o endereço IP do remetente. O endereço IP é calculado pelo ponto final de ingestão.
* Telemetria do servidor: O módulo de telemetria Application Insights recolhe temporariamente o endereço IP do cliente. O endereço IP não é recolhido localmente quando o `X-Forwarded-For` cabeçalho está definido.

Este comportamento é por design para ajudar a evitar a recolha desnecessária de dados pessoais. Sempre que possível, recomendamos evitar a recolha de dados pessoais. 

## <a name="overriding-default-behavior"></a>Comportamento predefinido primordial

Enquanto o padrão é não recolher endereços IP. Ainda oferecemos a flexibilidade para anular este comportamento. No entanto, recomendamos verificar se a recolha não infringe quaisquer requisitos de conformidade ou regulamentos locais. 

Para saber mais sobre o tratamento de dados pessoais em Application Insights, consulte a [orientação para dados pessoais.](../logs/personal-data-mgmt.md)

## <a name="storing-ip-address-data"></a>Armazenar dados de endereços IP

Para permitir a recolha e armazenamento ip, a `DisableIpMasking` propriedade do componente Application Insights deve ser configurada para `true` . Esta propriedade pode ser definida através de modelos Azure Resource Manager ou chamando a API REST. 

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

Se necessitar apenas de modificar o comportamento para um único recurso Application Insights, utilize o portal Azure. 

1. Vá ao seu recurso de insights de aplicação > modelo **de exportação de automação**  >   

2. Selecione **Implementar**

    ![Botão com palavra "Implementar" realçado a vermelho](media/ip-collection/deploy.png)

3. Selecione **o modelo de edição**.

    ![Botão com palavra "Editar" realçado em vermelho](media/ip-collection/edit-template.png)

4. Faça as seguintes alterações no json para o seu recurso e, em seguida, **selecione Guardar**:

    ![Screenshot adiciona uma vírgula após "IbizaAIExtension" e adiciona uma nova linha abaixo com "DisableIpMasking": verdadeiro](media/ip-collection/save.png)

    > [!WARNING]
    > Se sentir um erro que diga: **_O grupo de recursos está num local que não é suportado por um ou mais recursos no modelo. Por favor, escolha um grupo de recursos diferente._** Selecione temporariamente um grupo de recursos diferente do dropdown e, em seguida, reescorê-lo o seu grupo de recursos original para resolver o erro.

5. **Selecione Eu concordo**  >  **Compra.** 

    ![Caixa verificada com as palavras "Concordo com os termos e condições acima indicados" realçadas a vermelho acima de um botão com a palavra "Compra" realçada a vermelho.](media/ip-collection/purchase.png)

    Neste caso, nada de novo está realmente a ser comprado. Estamos apenas a atualizar a configuração do recurso Application Insights existente.

6. Uma vez concluída a implementação, serão registados novos dados de telemetria.

    Se selecionar e editar novamente o modelo, só verá o modelo padrão sem a propriedade recém-adicionada. Se não estiver a ver os dados do endereço IP e quiser confirmar que `"DisableIpMasking": true` está definido, execute o seguinte PowerShell: 
    
    ```powershell
    # Replace `Fabrikam-dev` with the appropriate resource and resource group name.
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    Como resultado, será devolvida uma lista de propriedades. Uma das propriedades deve `DisableIpMasking: true` ler. Se executar o PowerShell antes de implantar a nova propriedade com o Azure Resource Manager, a propriedade não existirá.

### <a name="rest-api"></a>API REST

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

Se precisar de uma alternativa mais flexível do `DisableIpMasking` que, pode utilizar um [inicializador de telemetria](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) para copiar a totalidade ou parte do endereço IP para um campo personalizado. 

# <a name="net"></a>[.NET](#tab/net)

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
> Se não conseguir `ISupportProperties` aceder, verifique e certifique-se de que está a executar a mais recente versão estável do Application Insights SDK. `ISupportProperties` destinam-se a valores elevados de cardinaldade, ao passo que `GlobalProperties` são mais adequados para valores de baixa cardinaldade, como o nome da região, o nome do ambiente, etc. 

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
# <a name="nodejs"></a>[Node.js](#tab/nodejs)

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
# <a name="client-side-javascript"></a>[JavaScript do lado do cliente](#tab/javascript)

### <a name="client-side-javascript"></a>JavaScript do lado do cliente

Ao contrário dos SDKs do lado do servidor, o JavaScript SDK do lado do cliente não calcula o endereço IP. Por padrão, o cálculo do endereço IP para telemetria do lado do cliente ocorre no ponto final de ingestão em Azure. 

Se pretender calcular o endereço IP diretamente no lado do cliente, terá de adicionar a sua própria lógica personalizada e utilizar o resultado para definir a `ai.location.ip` etiqueta. Quando `ai.location.ip` definido, o cálculo do endereço IP não é realizado pelo ponto final de ingestão, e o endereço IP fornecido é utilizado para o exame de geolocalização. Neste cenário, o endereço IP ainda será eliminado por padrão. 

Para manter todo o endereço IP calculado a partir da sua lógica personalizada, poderá utilizar um inicializador de telemetria que copie os dados de endereço IP fornecidos num `ai.location.ip` campo personalizado separado. Mas, mais uma vez, ao contrário dos SDKs do lado do servidor, sem depender de bibliotecas de terceiros ou da sua própria lógica de recolha personalizada, o SDK do lado do cliente não calculará o endereço para si.    


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

Se os dados do lado do cliente atravessarem um representante antes de serem encaminhados para o ponto final de ingestão, o cálculo do endereço IP pode mostrar o endereço IP do representante e não do cliente. 

---

### <a name="view-the-results-of-your-telemetry-initializer"></a>Veja os resultados do seu inicializador de telemetria

Se enviar novo tráfego para o seu site, e esperar alguns minutos. Em seguida, pode executar uma consulta para confirmar que a recolha está a funcionar:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Os endereços IP recentemente recolhidos aparecerão na `customDimensions_client-ip` coluna. A `client-ip` coluna predefinida ainda terá os quatro octetos a zeros. 

Se os testes realizados a partir de local, e o valor para `customDimensions_client-ip` o valor `::1` for, este valor é o comportamento esperado. `::1` representa o endereço loopback no IPv6. É equivalente ao `127.0.0.1` IPv4.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [a recolha de dados pessoais](../logs/personal-data-mgmt.md) no Application Insights.

* Saiba mais sobre como funciona a [recolha de endereços IP](https://apmtips.com/posts/2016-07-05-client-ip-address/) no Application Insights. (Este artigo é uma publicação de blogue externa mais antiga escrita por um dos nossos engenheiros. Antecede o comportamento predefinido atual em que o endereço IP é registado como `0.0.0.0` , mas vai em maior profundidade na mecânica do incorporado `ClientIpHeaderTelemetryInitializer` .)
