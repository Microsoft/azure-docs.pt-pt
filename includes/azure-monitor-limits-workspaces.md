---
title: incluir ficheiro
description: incluir ficheiro
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 49dc69eb73bd19509b5e14c5a4976cfa7208545a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392380"
---
**Retenção e volume de coleta de dados** 

| Escalão | Limite por dia | Retenção de dados | Comentário |
|:---|:---|:---|:---|
| Tipo de preço atual por GB<br>(introduzido em abril de 2018) | Sem limite | 30-730 dias | A retenção de dados além de 31 dias está disponível para encargos adicionais. Saiba mais sobre preços de Azure Monitor. |
| Camadas gratuitas herdadas<br>(introduzido em abril de 2016) | 500 MB | 7 dias | Quando seu espaço de trabalho atinge o limite de 500 MB por dia, a ingestão de dados é interrompida e retomada no início do dia seguinte. Um dia é baseado em UTC. Observe que os dados coletados pela central de segurança do Azure não estão incluídos neste limite de 500 MB por dia e continuarão a ser coletados acima desse limite.  |
| Camada autônoma herdada por GB<br>(introduzido em abril de 2016) | Sem limite | 30 a 730 dias | A retenção de dados além de 31 dias está disponível para encargos adicionais. Saiba mais sobre preços de Azure Monitor. |
| Herdado por nó (OMS)<br>(introduzido em abril de 2016) | Sem limite | 30 a 730 dias | A retenção de dados além de 31 dias está disponível para encargos adicionais. Saiba mais sobre preços de Azure Monitor. |
| Camada padrão herdada | Sem limite | 30 dias  | A retenção não pode ser ajustada |
| Camada Premium herdada | Sem limite | 365 dias  | A retenção não pode ser ajustada |

**Número de espaços de trabalho por assinatura.**

| Escalão de preço    | Limite do espaço de trabalho | Comentários
|:---|:---|:---|
| Escalão gratuito  | 10 | Esse limite não pode ser aumentado. |
| Todas as outras camadas | Sem limite | Você está limitado pelo número de recursos dentro de um grupo de recursos e o número de grupos de recursos por assinatura. |

**Portal do Azure**

| Categoria | Limites | Comentários |
|:---|:---|:---|
| Máximo de registros retornados por uma consulta de log | 10,000 | Reduza os resultados usando o escopo da consulta, o intervalo de tempo e os filtros na consulta. |


**API do coletor de dados**

| Categoria | Limites | Comentários |
|:---|:---|:---|
| Tamanho máximo para uma única postagem | 30 MB | Dividir volumes maiores em várias postagens. |
| Tamanho máximo para valores de campo  | 32 KB | Os campos com mais de 32 KB são truncados. |

**API de pesquisa**

| Categoria | Limites | Comentários |
|:---|:---|:---|
| Máximo de registros retornados em uma única consulta | 500,000 | |
| Tamanho máximo dos dados retornados | 64 milhões bytes (~ 61 MiB)| |
| Tempo de execução máximo da consulta | 10 minutos | Consulte [tempos limite](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) para obter detalhes.  |
| Taxa de solicitação máxima | 200 solicitações por 30 segundos por usuário do AAD ou endereço IP do cliente | Consulte [limites de taxa](https://dev.loganalytics.io/documentation/Using-the-API/Limits) para obter detalhes. |

**Limites gerais do espaço de trabalho**

| Categoria | Limites | Comentários |
|:---|:---|:---|
| Máximo de colunas em uma tabela         | 500 | |
| Máximo de caracteres para o nome da coluna | 500 | |
| Exportar dados | Não disponível no momento | Use o Azure Function ou o aplicativo lógico para agregar e exportar dados. | 

**Taxa de volume de ingestão de dados**


Azure Monitor é um serviço de dados de alta escala que atende a milhares de clientes enviando terabytes de dados por mês em um ritmo crescente. O limite de taxa de volume de ingestão padrão para os dados enviados dos recursos do Azure usando [as configurações de diagnóstico](../articles/azure-monitor/platform/diagnostic-settings.md) é de aproximadamente **6 GB/min** por espaço de trabalho. Esse é um valor aproximado, pois o tamanho real pode variar entre os tipos de dados, dependendo do tamanho do log e de sua taxa de compactação. Esse limite não se aplica aos dados enviados de agentes ou da [API do coletor de dados](../articles/azure-monitor/platform/data-collector-api.md).

Se você enviar dados a uma taxa mais alta para um único espaço de trabalho, alguns dados serão descartados e um evento será enviado para a tabela de *operações* no seu espaço de trabalho a cada 6 horas, enquanto o limite continuará sendo excedido. Se o volume de ingestão continuar exceder o limite de taxa ou você estiver esperando contatá-lo em breve, poderá solicitar um aumento no espaço de trabalho abrindo uma solicitação de suporte.
 
Para ser notificado sobre esse evento em seu espaço de trabalho, crie uma [regra de alerta de log](../articles/azure-monitor/platform/alerts-log.md) usando a consulta a seguir com a base de lógica de alerta no número de resultados mais rígidos que zero.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


>[!NOTE]
>Dependendo de quanto tempo você esteve usando Log Analytics, você pode ter acesso a tipos de preço herdados. Saiba mais sobre [log Analytics tipos de preço herdados](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 