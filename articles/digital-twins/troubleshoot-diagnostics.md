---
title: Configurar diagnósticos
titleSuffix: Azure Digital Twins
description: Veja como ativar o registo com as definições de diagnóstico.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 11a7b4876c773922d4b0ed28f7047912b738ee6a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93091740"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Resolução de problemas Azure Digital Twins: Registo de diagnósticos

A Azure Digital Twins recolhe [métricas](troubleshoot-metrics.md) para a sua instância de serviço que dão informações sobre o estado dos seus recursos. Você pode usar estas métricas para avaliar a saúde geral do serviço Azure Digital Twins e os recursos ligados a ele. Estas estatísticas viradas para o utilizador ajudam-no a ver o que se passa com as suas Gémeas Digitais Azure e ajudam a realizar análises de causa-raiz em problemas sem necessidade de contactar o suporte do Azure.

Este artigo mostra-lhe como ligar **o registo de diagnósticos** para os seus dados de métricas a partir da sua instância Azure Digital Twins. Pode utilizar estes registos para ajudá-lo a resolver problemas de serviço e configurar definições de diagnóstico para enviar métricas Azure Digital Twins para diferentes destinos. Pode ler mais sobre estas definições na [*Criar definições de diagnóstico para enviar registos e métricas da plataforma para diferentes destinos.*](../azure-monitor/platform/diagnostic-settings.md)

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Ligue as definições de diagnóstico com o portal Azure

Eis como ativar as definições de diagnóstico para a sua instância Azure Digital Twins:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para a sua instância Azure Digital Twins. Pode encontrá-lo digitando o seu nome na barra de pesquisa do portal. 

2. Selecione **as definições** de diagnóstico do menu e, em seguida, **adicione a definição de diagnóstico** .

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Screenshot mostrando a página e botão de definições de diagnóstico para adicionar":::

3. Na página que se segue, preencha os seguintes valores:
     * **Nome de definição de** diagnóstico : Dê um nome às definições de diagnóstico.
     * **Detalhes da categoria** : Escolha quais as operações que pretende monitorizar e verifique as caixas para permitir diagnósticos para essas operações. As operações que as definições de diagnóstico podem reportar são:
        - DigitalTwinsOperação
        - EventRoutesOperação
        - ModelosOperação
        - Consulta
        - AllMetrics
        
        Para mais detalhes sobre estas opções, consulte a secção [*de detalhes*](#category-details) da categoria abaixo.
     * **Detalhes do destino** : Escolha para onde pretende enviar os registos. Pode selecionar qualquer combinação das três opções:
        - Enviar para o Log Analytics
        - Arquivar numa conta de armazenamento
        - Transmitir em fluxo para um hub de eventos

        Pode ser-lhe pedido que preencha detalhes adicionais se forem necessários para a sua seleção de destino.  
    
4. Guarde as novas definições. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Screenshot mostrando a página e botão de definições de diagnóstico para adicionar":::

As novas definições fazem efeito em cerca de 10 minutos. Depois disso, os registos aparecem no alvo configurado de volta na página **de definições de Diagnóstico,** para o seu exemplo. 

## <a name="category-details"></a>Detalhes da categoria

Aqui estão mais detalhes sobre as categorias de registo que podem ser selecionadas em **detalhes de categoria** ao configurar configurações de diagnóstico.

| Categoria do registo | Description |
| --- | --- |
| ADTModelsOperação | Registar todas as chamadas da API relativas a Modelos |
| ADTQueryOperação | Registar todas as chamadas da API relativas a consultas |
| ADTEventRoutesOperação | Regisque todas as chamadas da API relativas às Rotas de Eventos, bem como a saída de eventos da Azure Digital Twins para um serviço de ponto final como Event Grid, Event Hubs e Service Bus |
| ADTDigitalTwinsOperação | Registar todas as chamadas da API relativas às Gémeas Digitais Azure |

Cada categoria de registo consiste em operações de escrita, leitura, exclusão e ação.  Estes mapas para rest API chamadas da seguinte forma:

| Tipo de evento | Operações REST API |
| --- | --- |
| Escrita | PUT E PATCH |
| Ler | GET |
| Eliminar | DELETE |
| Ação | POST |

Aqui está uma lista completa das operações e [correspondentes chamadas API de Azure Digital Twins](/rest/api/azure-digitaltwins/) REST que são registadas em cada categoria. 

>[!NOTE]
> Cada categoria de registo contém várias chamadas de API de operações/REST. Na tabela abaixo, cada categoria de registo mapeia todas as chamadas de API de operações/REST por baixo até à lista da próxima categoria de registo. 

| Categoria do registo | Operação | REST Chamadas API e outros eventos |
| --- | --- | --- |
| ADTModelsOperação | Microsoft.DigitalTwins/models/write | Atualização de modelos digitais twin api |
|  | Microsoft.DigitalTwins/modelos/ler | Modelos Twin Digital Get By ID e List APIs |
|  | Microsoft.DigitalTwins/models/delete | Modelos Gémeos Digitais Delete API |
|  | Microsoft.DigitalTwins/modelos/ação | Modelos Twin Digitais Adicionam API |
| ADTQueryOperação | Microsoft.DigitalTwins/consulta/ação | Consulta Gémeas API |
| ADTEventRoutesOperação | Microsoft.DigitalTwins/eventroutes/write | Rotas do Evento Adicionar API |
|  | Microsoft.DigitalTwins/eventroutes/read | Rotas de eventos obter por ID e APIs listar |
|  | Microsoft.DigitalTwins/eventroutes/delete | Rotas do evento Eliminar API |
|  | Microsoft.DigitalTwins/eventroutes/action | Falha ao tentar publicar eventos para um serviço de ponto final (não uma chamada da API) |
| ADTDigitalTwinsOperação | Microsoft.DigitalTwins/digitaltwins/write | Gémeos Digitais Adicionar, Adicionar Relacionamento, Atualização, Componente de Atualização |
|  | Microsoft.DigitalTwins/digitaltwins/read | Gémeos Digitais Obter Por ID, Obter Componente, Obter Relação por ID, Listar relações recebidas, relacionamentos de lista |
|  | Microsoft.DigitalTwins/digitaltwins/delete | Gémeos Digitais Delete, Excluir Relacionamento |
|  | Microsoft.DigitalTwins/digitaltwins/action | Gémeos Digitais Enviam Telemetria Componente, Enviar Telemetria |

## <a name="log-schemas"></a>Esquemas de registo 

Cada categoria de registo tem um esquema que define como os eventos nessa categoria são relatados. Cada entrada de registo individual é armazenada como texto e formatada como uma bolha JSON. Os campos no log e exemplo os corpos JSON são fornecidos para cada tipo de registo abaixo. 

`ADTDigitalTwinsOperation`, `ADTModelsOperation` e use um esquema de registo `ADTQueryOperation` API consistente; `ADTEventRoutesOperation` tem o seu próprio esquema separado.

### <a name="api-log-schemas"></a>Esquemas de registo da API

Este esquema de registo é consistente para `ADTDigitalTwinsOperation` `ADTModelsOperation` , e `ADTQueryOperation` . Contém informações pertinentes para chamadas da API para uma instância Azure Digital Twins.

Aqui estão as descrições de campo e propriedade para registos API.

| Nome do campo | Tipo de dados | Descrição |
|-----|------|-------------|
| `Time` | DateTime | A data e hora em que este evento ocorreu, na UTC |
| `ResourceID` | String | O ID de Recursos Azure Resource Manager para o recurso onde o evento teve lugar |
| `OperationName` | String  | O tipo de ação que está a ser realizada durante o evento |
| `OperationVersion` | String | A versão API utilizada durante o evento |
| `Category` | String | O tipo de recurso que está a ser emitido |
| `ResultType` | String | Resultado do evento |
| `ResultSignature` | String | Código de estado http para o evento |
| `ResultDescription` | String | Detalhes adicionais sobre o evento |
| `DurationMs` | String | Quanto tempo demorou a realizar o evento em milissegundos |
| `CallerIpAddress` | String | Um endereço IP de origem mascarada para o evento |
| `CorrelationId` | GUID | O cliente forneceu um identificador único para o evento |
| `Level` | String | A gravidade do registo do evento |
| `Location` | String | A região onde o evento teve lugar |
| `RequestUri` | Uri | O ponto final utilizado durante o evento |

Abaixo estão os corpos JSON exemplo para este tipo de registos.

#### <a name="adtdigitaltwinsoperation"></a>ADTDigitalTwinsOperação

```json
{
  "time": "2020-03-14T21:11:14.9918922Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/digitaltwins/write",
  "operationVersion": "2020-10-31",
  "category": "DigitalTwinOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "314",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "2f6a8e64-94aa-492a-bc31-16b9f0b16ab3",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/digitaltwins/factory-58d81613-2e54-4faa-a930-d980e6e2a884?api-version=2020-10-31"
}
```

#### <a name="adtmodelsoperation"></a>ADTModelsOperação

```json
{
  "time": "2020-10-29T21:12:24.2337302Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/models/write",
  "operationVersion": "2020-10-31",
  "category": "ModelsOperation",
  "resultType": "Success",
  "resultSignature": "201",
  "resultDescription": "",
  "durationMs": "935",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "9dcb71ea-bb6f-46f2-ab70-78b80db76882",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/Models?api-version=2020-10-31",
}
```

#### <a name="adtqueryoperation"></a>ADTQueryOperação

```json
{
  "time": "2020-12-04T21:11:44.1690031Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/query/action",
  "operationVersion": "2020-10-31",
  "category": "QueryOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "255",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "1ee2b6e9-3af4-4873-8c7c-1a698b9ac334",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/query?api-version=2020-10-31",
}
```

### <a name="egress-log-schemas"></a>Esquemas de log da Egress

Este é o esquema para `ADTEventRoutesOperation` registos. Estes contêm detalhes relativos a exceções e as operações da API em torno de pontos finais de saída ligados a uma instância Azure Digital Twins.

|Nome do campo | Tipo de dados | Descrição |
|-----|------|-------------|
| `Time` | DateTime | A data e hora em que este evento ocorreu, na UTC |
| `ResourceId` | String | O ID de Recursos Azure Resource Manager para o recurso onde o evento teve lugar |
| `OperationName` | String  | O tipo de ação que está a ser realizada durante o evento |
| `Category` | String | O tipo de recurso que está a ser emitido |
| `ResultDescription` | String | Detalhes adicionais sobre o evento |
| `Level` | String | A gravidade do registo do evento |
| `Location` | String | A região onde o evento teve lugar |
| `EndpointName` | String | O nome do ponto final da saída criado em Azure Digital Twins |

Abaixo estão os corpos JSON exemplo para este tipo de registos.

#### <a name="adteventroutesoperation"></a>ADTEventRoutesOperação

```json
{
  "time": "2020-11-05T22:18:38.0708705Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/eventroutes/action",
  "category": "EventRoutesOperation",
  "resultDescription": "Unable to send EventGrid message to [my-event-grid.westus-1.eventgrid.azure.net] for event Id [f6f45831-55d0-408b-8366-058e81ca6089].",
  "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
  "level": "3",
  "location": "southcentralus",
  "properties": {
    "endpointName": "endpointEventGridInvalidKey"
  }
}
```

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a configuração de diagnósticos, consulte [*recolher e consumir dados de registo dos seus recursos Azure.*](../azure-monitor/platform/platform-logs-overview.md)
* Para obter informações sobre as métricas Azure Digital Twins, consulte [*resolução de problemas: Ver métricas com O Monitor Azure*](troubleshoot-metrics.md).
* Para ver como ativar alertas para as suas métricas, consulte [*Resolução de Problemas: Configurar alertas*](troubleshoot-alerts.md).