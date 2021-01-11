---
title: Registos de resolução de problemas do Monitor Azure (Pré-visualização)
description: Utilize o Azure Monitor para investigar rapidamente, ou periodicamente, problemas de código ou configuração de resolução de problemas ou casos de apoio, que muitas vezes dependem da pesquisa sobre o elevado volume de dados para obter informações específicas.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/29/2020
ms.openlocfilehash: 816cdddc1f3d0a9bc9ebc3f277bc223a688cba31
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2021
ms.locfileid: "98067371"
---
# <a name="azure-monitor-troubleshooting-logs-preview"></a>Registos de resolução de problemas do Monitor Azure (Pré-visualização)
Utilize o Azure Monitor para investigar rapidamente e/ou periodicamente problemas, código de resolução de problemas ou problemas de configuração ou casos de suporte de endereço, que muitas vezes dependem da pesquisa sobre o volume elevado de dados para obter informações específicas.

>[!NOTE]
> * Os Registos de Resolução de Problemas estão no modo de pré-visualização.
>* Contacte a [equipa do Log Analytics](mailto:orens@microsoft.com) com quaisquer perguntas ou para aplicar a funcionalidade.
## <a name="troubleshoot-and-query-your-code-or-configuration-issues"></a>Resolução de problemas e consulta dos seus problemas de código ou configuração
Utilize registos de resolução de problemas do Monitor Azure para recolher os seus registos e investigar problemas e problemas de forma mais simples e barata utilizando o KQL.
A resolução de problemas dos Registos decreta as suas acusações, dando-lhe capacidades básicas para a resolução de problemas.

> [!NOTE]
>* A decisão para o modo de resolução de problemas é configurável.
>* Os registos de resolução de problemas podem ser aplicados em tabelas específicas, atualmente em tabelas "Registos de Contentores" e "Rastreios de Aplicações".
>* Há um período de retenção gratuito de 4 dias, podendo ser alargado a custos além.
>* Por predefinição, as tabelas herdam a retenção do espaço de trabalho. Para evitar custos adicionais, recomenda-se alterar a retenção destas tabelas. [Clique aqui para aprender a mudar a retenção de mesa.](https://docs.microsoft.com//azure/azure-monitor/platform/manage-cost-storage)

## <a name="turn-on-troubleshooting-logs-on-your-tables"></a>Ligue registos de resolução de problemas nas suas mesas

Para ligar os Registos de Resolução de Problemas no seu espaço de trabalho, tem de utilizar a seguinte chamada API.
```http
PUT https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

(With body in the form of a GET single table request response)

Response:

{
        "properties": {
          "retentionInDays": 40,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": true
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": "{tableName}"
      }
```
## <a name="check-if-the-troubleshooting-logs-feature-is-enabled-for-a-given-table"></a>Verifique se a função de registos de resolução de problemas está ativada para uma determinada tabela
Para verificar se o Registo de Resolução de Problemas está ativado para uma determinada tabela, pode utilizar a seguinte chamada API.

```http
GET https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

Response: 
"properties": {
          "retentionInDays": 30,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": true,
          "lastTroubleshootDate": "Thu, 19 Nov 2020 07:40:51 GMT"
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": " {tableName}"

```
## <a name="check-if-the-troubleshooting-logs-feature-is-enabled-for-all-of-the-tables-in-a-workspace"></a>Verifique se a função de registos de resolução de problemas está ativada para todas as tabelas num espaço de trabalho
Para verificar quais as tabelas que têm o Registo de Resolução de Problemas ativado, pode utilizar a seguinte chamada API.

```http
GET "https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables"

Response: 
{
          "properties": {
            "retentionInDays": 30,
            "isTroubleshootingAllowed": true,
            "isTroubleshootEnabled": true,
            "lastTroubleshootDate": "Thu, 19 Nov 2020 07:40:51 GMT"
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table1",
          "name": "table1"
        },
        {
          "properties": {
            "retentionInDays": 7,
            "isTroubleshootingAllowed": true
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table2",
          "name": "table2"
        },
        {
          "properties": {
            "retentionInDays": 7,
            "isTroubleshootingAllowed": false
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table3",
          "name": "table3"
        }
```
## <a name="turn-off-troubleshooting-logs-on-your-tables"></a>Desligue os registos de resolução de problemas nas suas mesas

Para desativar os Registos de Resolução de Problemas no seu espaço de trabalho, tem de utilizar a seguinte chamada API.
```http
PUT https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

(With body in the form of a GET single table request response)

Response:

{
        "properties": {
          "retentionInDays": 40,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": false
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": "{tableName}"
      }
```
>[!TIP]
>* Pode utilizar qualquer ferramenta REST API para executar os comandos. [Ler Mais](https://docs.microsoft.com/rest/api/azure/)
>* Tens de usar o símbolo do Portador para autenticação. [Ler Mais](https://social.technet.microsoft.com/wiki/contents/articles/51140.azure-rest-management-api-the-quickest-way-to-get-your-bearer-token.aspx)

>[!NOTE]
>* A bandeira "isTroubleshootingAllowed" – descreve se a tabela é permitida no serviço
>* O "isTroubleshootEnabled" indica se a função está ativada para a tabela - pode ser ligada ou desligada (verdadeira ou falsa)
>* Ao desativar a bandeira "isTroubleshootEnabled" para uma tabela específica, a possibilidade de voltar a permitir é possível apenas uma semana após a data de habilitação prévia.
>* Atualmente isto é suportado apenas para tabelas em baixo (alguns outros SKUs também serão apoiados no futuro) - [Leia mais sobre preços](https://docs.microsoft.com/services-hub/health/azure_pricing).

## <a name="query-limitations-for-troubleshooting"></a>Limitações de consulta para resolução de problemas
Existem poucas limitações para uma tabela que é marcada como "Logs de resolução de problemas":
*   Obterá menos recursos de processamento e, portanto, não será adequado para grandes dashboards, análises complexas ou muitas chamadas de API simultâneas.
*   As consultas estão limitadas a um intervalo de tempo de dois dias.
* purga não vai funcionar – [Leia mais sobre purga.](https://docs.microsoft.com/rest/api/loganalytics/workspacepurge/purge)
* Os alertas não são suportados através deste serviço.
## <a name="next-steps"></a>Passos seguintes
* [Escrever consultas](https://docs.microsoft.com/azure/data-explorer/write-queries)


