---
title: API de utilização de recursos do fornecedor | Documentos da Microsoft
description: Referência para a utilização de recursos de API, que recupera informações de utilização do Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/04/2019
ms.author: mabrigg
ms.reviewer: alfredop
ms.lastreviewed: 01/25/2018
ms.openlocfilehash: 48002ecb3186218d2cf86e38b43b8e35ae39d240
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57338387"
---
# <a name="provider-resource-usage-api"></a>API de utilização do recurso do fornecedor

O termo *fornecedor* aplica-se para o administrador de serviços e para quaisquer fornecedores delegados. Operadores do Azure Stack e fornecedores delegados podem utilizar a API de utilização do fornecedor para ver a utilização dos seus inquilinos diretos. Por exemplo, conforme mostrado no diagrama, P0 pode chamar a API para obter informações de utilização do P1 do fornecedor e a utilização de direta do P2 e P1 podem chamar para informações de utilização sobre P3 e P4.

![Modelo conceitual da hierarquia de fornecedor](media/azure-stack-provider-resource-api/image1.png)

## <a name="api-call-reference"></a>Referência da chamada de API
### <a name="request"></a>Pedir
O pedido obtém os detalhes de consumo para as subscrições de pedido e para o intervalo de tempo pedido. Não existe nenhum corpo de pedido.

Esta API de utilização é um fornecedor de API, para que o chamador tem de ser atribuído uma função de proprietário, contribuinte ou leitor na subscrição do fornecedor.

| **Método** | **URI do pedido** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity} & subscriberId = {sub1.1} & api-version = 2015-06-01-preview & continuationToken = {token-value} |

### <a name="arguments"></a>Argumentos
| **Argument** | **Descrição** |
| --- | --- |
| *armendpoint* |Gestor de recursos ponto final do Azure do seu ambiente do Azure Stack. A Convenção do Azure Stack é o nome do ponto final do Azure Resource Manager está no formato `https://adminmanagement.{domain-name}`. Por exemplo, para o kit de desenvolvimento, se o nome de domínio for *local.azurestack.external*, em seguida, o ponto de final do Gestor de recursos é `https://adminmanagement.local.azurestack.external`. |
| *subId* |ID de subscrição do utilizador que faz a chamada. |
| *reportedStartTime* |Hora de início da consulta. O valor para *DateTime* deve ser na hora Universal Coordenada (UTC) e no início da hora, por exemplo, 13:00. Para agregação diária, defina este valor como meia-noite de UTC. O formato é *caracteres de escape* ISO 8601. Por exemplo, *2015-06-16T18% 3a53% 3a11% 2b00% 3a00Z*, em que os dois pontos é escape para *% 3a* e o sinal de adição é escape para *% 2b* para que seja amigável de URI. |
| *reportedEndTime* |Hora de fim da consulta. As restrições que se aplicam à *reportedStartTime* também se aplicam a este argumento. O valor para *reportedEndTime* não pode ser no futuro ou a data atual. Se for o caso, o resultado é definido como "processamento não concluída." |
| *aggregationGranularity* |O parâmetro opcional que tem dois valores possíveis discretas: diários e hora a hora. Como os valores sugerem, um devolve os dados na granularidade diária e o outro é uma resolução por hora. A opção diária é a predefinição. |
| *subscriberId* |ID da subscrição. Para obter os dados filtrados, o ID de subscrição de um inquilino direto do fornecedor, é necessário. Se não for especificado nenhum parâmetro de ID de subscrição, a chamada retorna dados de utilização para os inquilinos de direta do fornecedor. |
| *api-version* |Versão do protocolo que é utilizado para efetuar este pedido. Este valor é definido como *2015-06-01-pré-visualização*. |
| *continuationToken* |Token obtido a partir da última chamada para o fornecedor de API de utilização. Este token é necessário quando uma resposta for superior a 1000 linhas e atua como um indicador para conhecer o progresso. Se o token não estiver presente, os dados são recuperados desde o início do dia ou hora, com base na granularidade passado. |

### <a name="response"></a>Resposta
OBTER /subscriptions/sub1/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 & reportedEndTime = 2015-06-01T00% 3a00% 3a00% 2b00% 3a00 & aggregationGranularity = diariamente e subscriberId = sub1.1 e a api-version = 1,0

```json
{
"value": [
{

"id":
"/subscriptions/sub1.1/providers/Microsoft.Commerce.Admin/UsageAggregate/sub1.1-

meterID1",
"name": "sub1.1-meterID1",
"type": "Microsoft.Commerce.Admin/UsageAggregate",

"properties": {
"subscriptionId":"sub1.1",
"usageStartTime": "2015-03-03T00:00:00+00:00",
"usageEndTime": "2015-03-04T00:00:00+00:00",
"instanceData":"{\"Microsoft.Resources\":{\"resourceUri\":\"resourceUri1\",\"location\":\"Alaska\",\"tags\":null,\"additionalInfo\":null}}",
"quantity":2.4000000000,
"meterId":"meterID1"

}
},

…
```

### <a name="response-details"></a>Detalhes da resposta
| **Argument** | **Descrição** |
| --- | --- |
| *id* |ID exclusivo do agregado de utilização. |
| *name* |Nome do agregado de utilização. |
| *tipo* |Definição do recurso. |
| *subscriptionId* |Identificador de subscrição do utilizador do Azure Stack. |
| *usageStartTime* |Hora de registo de utilização a que pertence este agregado de utilização de início de UTC.|
| *usageEndTime* |Hora de fim de UTC de registo de utilização a que pertence este agregado de utilização. |
| *instanceData* |Pares de chave-valor dos detalhes de instância (num novo formato):<br> *resourceUri*: ID de recurso completamente qualificado, que inclui os grupos de recursos e o nome da instância. <br> *Localização*: Região em que este serviço foi executado. <br> *tags*: Sinalizadores de recursos que são especificados pelo utilizador. <br> *additionalInfo*: Tipo de obter mais detalhes sobre o recurso que foi consumido, por exemplo, a versão do SO ou a imagem. |
| *quantity* |Quantidade de consumo de recursos que ocorreram neste período de tempo. |
| *meterId* |ID exclusivo para o recurso que foi consumido (também denominado *ResourceID*). |


## <a name="retrieve-usage-information"></a>Obter informações de utilização

### <a name="powershell"></a>PowerShell

Para gerar os dados de utilização, deve ter recursos que estão em execução e a utilizar ativamente o sistema, por exemplo, uma máquina virtual do Active Directory ou uma conta de armazenamento que contém alguns dados etc. Se não tiver a certeza se tem recursos em execução no mercado do Azure Stack, implementar uma máquina virtual (VM) e verifique se a VM monitorização painel para garantir que ele é executado. Utilize os seguintes cmdlets do PowerShell para ver os dados de utilização:

1. [Instale o PowerShell para o Azure Stack.](azure-stack-powershell-install.md)
2. [Configurar o utilizador Azure Stack](user/azure-stack-powershell-configure-user.md) ou o [do operador do Azure Stack](azure-stack-powershell-configure-admin.md) ambiente do PowerShell 
3. Para obter os dados de utilização, utilize o [Get-UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates) cmdlet do PowerShell:
```powershell
Get-UsageAggregates -ReportedStartTime "<Start time for usage reporting>" -ReportedEndTime "<end time for usage reporting>" -AggregationGranularity <Hourly or Daily>
```
### <a name="rest-api"></a>API REST

Pode recolher informações de utilização para subscrições eliminadas ao chamar o serviço de Microsoft.Commerce.Admin. 

**Para retornar todas as utilizações de inquilino para eliminadas para usuários do Active Directory:**

| **Método** | **URI do pedido** |
| --- | --- |
| GET | https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&api-version= 2015-06-01-pré-visualização |

**Para voltar a utilização do inquilino foi eliminada ou Active Directory:**

| **Método** | **URI do pedido** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&subscriberId={ id do assinante} & api-version = 2015-06-01-pré-visualização |


## <a name="next-steps"></a>Passos Seguintes
[Referência da API de utilização de recursos de inquilino](azure-stack-tenant-resource-usage-api.md)

[Perguntas Freqüentes relacionadas com a utilização](azure-stack-usage-related-faq.md)
