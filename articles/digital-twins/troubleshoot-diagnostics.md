---
title: Ativar e consultar registos de diagnóstico
titleSuffix: Azure Digital Twins
description: Veja como ativar o registo com as definições de diagnóstico e consulte os registos para visualização imediata.
author: baanders
ms.author: baanders
ms.date: 11/9/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 797de242b4b4464c0bfb5ae18af05710ab36bce6
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285484"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Resolução de problemas Azure Digital Twins: Registo de diagnósticos

A Azure Digital Twins pode recolher registos para a sua instância de serviço para monitorizar o seu desempenho, acesso e outros dados. Pode utilizar estes registos para ter uma ideia do que está a acontecer na sua instância Azure Digital Twins e realizar análises de causa-raiz em questões sem precisar de contactar o suporte do Azure.

Este artigo mostra-lhe como [**configurar as definições**](#turn-on-diagnostic-settings) de diagnóstico no [portal Azure](https://portal.azure.com) para começar a recolher registos a partir da sua instância Azure Digital Twins. Também pode especificar onde os registos devem ser armazenados (como Log Analytics ou uma conta de armazenamento à sua escolha).

Este artigo também contém [listas](#log-categories) de todas as categorias de registos e [esquemas de registo](#log-schemas) que a Azure Digital Twins recolhe.

Depois de configurar registos, também pode [**consultar os registos**](#view-and-query-logs) para recolher rapidamente insights personalizados.

## <a name="turn-on-diagnostic-settings"></a>Ligue as definições de diagnóstico 

Ligue as definições de diagnóstico para começar a recolher registos na sua instância Azure Digital Twins. Também pode escolher o destino onde os registos exportados devem ser armazenados. Aqui está como ativar as definições de diagnóstico para o seu exemplo Azure Digital Twins.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para a sua instância Azure Digital Twins. Pode encontrá-lo digitando o seu nome na barra de pesquisa do portal. 

2. Selecione **as definições** de diagnóstico do menu e, em seguida, **adicione a definição de diagnóstico**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Screenshot mostrando a página e botão de definições de diagnóstico para adicionar" lightbox="media/troubleshoot-diagnostics/diagnostic-settings.png":::

3. Na página que se segue, preencha os seguintes valores:
     * **Nome de definição de** diagnóstico : Dê um nome às definições de diagnóstico.
     * **Detalhes da categoria**: Escolha quais as operações que pretende monitorizar e verifique as caixas para permitir diagnósticos para essas operações. As operações que as definições de diagnóstico podem reportar são:
        - DigitalTwinsOperação
        - EventRoutesOperação
        - ModelosOperação
        - Consulta
        - AllMetrics
        
        Para obter mais detalhes sobre estas categorias e as informações que contêm, consulte a secção [*de categorias de Registo*](#log-categories) abaixo.
     * **Detalhes do destino**: Escolha para onde pretende enviar os registos. Pode selecionar qualquer combinação das três opções:
        - Enviar para o Log Analytics
        - Arquivar numa conta de armazenamento
        - Transmitir em fluxo para um hub de eventos

        Pode ser-lhe pedido que preencha detalhes adicionais se forem necessários para a sua seleção de destino.  
    
4. Guarde as novas definições. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Screenshot mostrando a página de definição de diagnóstico onde o utilizador preencheu um nome de definição de diagnóstico, e fez algumas seleções de caixas de verificação para detalhes da categoria e detalhes do destino. O botão Guardar é realçado." lightbox="media/troubleshoot-diagnostics/diagnostic-settings-details.png":::

As novas definições fazem efeito em cerca de 10 minutos. Depois disso, os registos aparecem no alvo configurado de volta na página **de definições de Diagnóstico,** para o seu exemplo. 

Para obter informações mais detalhadas sobre as definições de diagnóstico e as suas opções de configuração, pode visitar [*Criar configurações de diagnóstico para enviar registos e métricas da plataforma para diferentes destinos.*](../azure-monitor/essentials/diagnostic-settings.md)

## <a name="log-categories"></a>Categorias de registo

Aqui estão mais detalhes sobre as categorias de registos que a Azure Digital Twins recolhe.

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
| `ResourceId` | String | O ID de Recursos Azure Resource Manager para o recurso onde o evento teve lugar |
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

## <a name="view-and-query-logs"></a>Ver e consultar registos

No início deste artigo, configuraste os tipos de registos para armazenar e especificou a sua localização de armazenamento.

Para resolver problemas e gerar insights a partir destes registos, pode gerar **consultas personalizadas**. Para começar, também pode aproveitar algumas consultas de exemplo fornecidas pelo serviço, que abordam questões comuns que os clientes podem ter sobre o seu caso.

Aqui está como consultar os registos, por exemplo.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para a sua instância Azure Digital Twins. Pode encontrá-lo digitando o seu nome na barra de pesquisa do portal. 

2. Selecione **Registos** do menu para abrir a página de consulta de registo. A página abre para uma janela chamada *Consultas.*

    :::image type="content" source="media/troubleshoot-diagnostics/logs.png" alt-text="Screenshot mostrando a página de Logs para uma instância Azure Digital Twins. É sobreposta com uma janela de consultas mostrando consultas pré-construídas nomeadas após diferentes opções de log, como a Latência API digitalTwin e a latência da API modelo." lightbox="media/troubleshoot-diagnostics/logs.png":::

    Estas são consultas de exemplo pré-construídas escritas para vários registos. Pode selecionar uma das consultas para carregá-lo no editor de consulta e executá-lo para ver estes registos para o seu exemplo.

    Também pode fechar a janela *consultas* sem executar nada para ir diretamente para a página do editor de consulta, onde pode escrever ou editar código de consulta personalizado.

3. Depois de sair da janela *consultas,* verá a página principal do editor de consultas. Aqui pode ver e editar o texto das consultas de exemplo, ou escrever as suas próprias consultas de raiz.
    :::image type="content" source="media/troubleshoot-diagnostics/logs-query.png" alt-text="Screenshot mostrando a página de Logs para uma instância Azure Digital Twins. A janela de consultas desapareceu, e em vez disso há uma lista de registos diferentes, um painel de edição mostrando código de consulta editável, e um painel mostrando Histórico de Consultas." lightbox="media/troubleshoot-diagnostics/logs-query.png":::

    No painel esquerdo, 
    - O separador *Tabelas* mostra as [diferentes categorias de registo](#log-categories) de gémeos Azure Digital que estão disponíveis para usar nas suas consultas. 
    - O separador *Consultas* contém as consultas de exemplo que pode carregar no editor.
    - O *separador Filtro* permite-lhe personalizar uma visão filtrada dos dados que a consulta devolve.

Para obter informações mais detalhadas sobre consultas de registo e como escrevê-las, pode visitar a [*Visão Geral das consultas de registo no Azure Monitor*](../azure-monitor/logs/log-query-overview.md).

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a configuração de diagnósticos, consulte [*recolher e consumir dados de registo dos seus recursos Azure.*](../azure-monitor/essentials/platform-logs-overview.md)
* Para obter informações sobre as métricas Azure Digital Twins, consulte [*resolução de problemas: Ver métricas com O Monitor Azure*](troubleshoot-metrics.md).
* Para ver como ativar alertas para as suas métricas, consulte [*Resolução de Problemas: Configurar alertas*](troubleshoot-alerts.md).