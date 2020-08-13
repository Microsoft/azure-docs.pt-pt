---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 6979ce65022f350a93f533951d634b8e436283bc
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88186251"
---
**Volume e retenção de recolha de dados** 

| Escalão de serviço | Limite por dia | Retenção de dados | Comentário |
|:---|:---|:---|:---|
| Nível atual de preços por GB<br>(introduzido abril de 2018) | Sem limite | 30 - 730 dias | A retenção de dados para além de 31 dias está disponível para custos adicionais. Saiba mais sobre os preços do Azure Monitor. |
| Legacy Free Tiers<br>(introduzido abril de 2016) | 500 MB | 7 dias | Quando o seu espaço de trabalho atinge o limite de 500 MB por dia, a ingestão de dados para e retoma no início do dia seguinte. Os dias são baseados no fuso horário UTC. Note que os dados recolhidos pelo Azure Security Center não estão incluídos neste limite de 500 MB por dia e continuarão a ser recolhidos acima deste limite.  |
| Legacy Autónomo Por NÍVEL GB<br>(introduzido abril de 2016) | Sem limite | 30 a 730 dias | A retenção de dados para além de 31 dias está disponível para custos adicionais. Saiba mais sobre os preços do Azure Monitor. |
| Legado por nó (OMS)<br>(introduzido abril de 2016) | Sem limite | 30 a 730 dias | A retenção de dados para além de 31 dias está disponível para custos adicionais. Saiba mais sobre os preços do Azure Monitor. |
| Nível padrão legado | Sem limite | 30 dias  | A retenção não pode ser ajustada |
| Nível Legacy Premium | Sem limite | 365 dias  | A retenção não pode ser ajustada |

**Número de espaços de trabalho por subscrição.**

| Escalão de preço    | Limite do espaço de trabalho | Comentários
|:---|:---|:---|
| Escalão gratuito  | 10 | Este limite não pode ser aumentado. |
| Todos os outros níveis | Sem limite | Está limitado pelo número de recursos dentro de um grupo de recursos e pelo número de grupos de recursos por subscrição. |

**Portal do Azure**

| Categoria | Limite | Comentários |
|:---|:---|:---|
| Registos máximos devolvidos por uma consulta de registo | 10,000 | Reduza os resultados utilizando o âmbito de consulta, o intervalo de tempo e os filtros na consulta. |


**API do Colecionador de Dados**

| Categoria | Limite | Comentários |
|:---|:---|:---|
| Tamanho máximo para um único poste | 30 MB | Divida volumes maiores em vários postes. |
| Tamanho máximo para valores de campo  | 32 KB | Os campos com mais de 32 KB são truncados. |

**API de Pesquisa**

| Categoria | Limite | Comentários |
|:---|:---|:---|
| Registos máximos devolvidos numa única consulta | 500 000 | |
| Tamanho máximo dos dados devolvidos | 64.000.000 bytes (~61 MiB)| |
| Tempo máximo de funcionamento da consulta | 10 minutos | Consulte [timeouts](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) para mais detalhes.  |
| Taxa máxima de pedido | 200 pedidos por 30 segundos por utilizador AD Azure ou endereço IP do cliente | Consulte [os limites da taxa](https://dev.loganalytics.io/documentation/Using-the-API/Limits) para mais detalhes. |

**Limites gerais do espaço de trabalho**

| Categoria | Limite | Comentários |
|:---|:---|:---|
| Colunas máximas numa tabela         | 500 | |
| Caracteres máximos para nome de coluna | 500 | |
| Exportação de dados | Não disponível atualmente | Utilize a Azure Function ou a Logic App para agregar e exportar dados. | 

**Taxa de volume de ingestão de dados**

O Azure Monitor é um serviço de dados de alta escala que serve milhares de clientes que enviam terabytes de dados todos os meses a um ritmo crescente. O limite da taxa de volume pretende proteger os clientes do Azure Monitor de picos de ingestão súbita em ambiente multitenancy. Um limiar de taxa de volume de ingestão predefinido de 500 MB (comprimido) aplica-se aos espaços de trabalho, que é aproximadamente **6 GB/min** não comprimido -- o tamanho real pode variar entre os tipos de dados dependendo do comprimento do tronco e da sua relação de compressão. Este limiar aplica-se a todos os dados ingeridos, quer sejam enviados a partir de recursos Azure utilizando definições de [Diagnóstico,](../articles/azure-monitor/platform/diagnostic-settings.md) [API do Colecionador de Dados](../articles/azure-monitor/platform/data-collector-api.md) ou agentes.

Quando envia dados para um espaço de trabalho a uma taxa de volume superior a 80% do limiar configurado no seu espaço de trabalho, é enviado um evento para a tabela *Operação* no seu espaço de trabalho a cada 6 horas enquanto o limiar continua a ser ultrapassado. Quando a taxa de volume ingerida é superior ao limiar, alguns dados são eliminados e um evento é enviado para a tabela *Operação* no seu espaço de trabalho a cada 6 horas enquanto o limiar continua a ser ultrapassado. Se a sua taxa de volume de ingestão continuar a exceder o limiar ou se espera alcançá-lo em breve, pode solicitar um aumento no seu espaço de trabalho abrindo um pedido de apoio. 

Para ser notificado em tal evento no seu espaço de trabalho, crie uma [regra de alerta de registo](../articles/azure-monitor/platform/alerts-log.md) utilizando a seguinte consulta com base lógica de alerta no número de resultados ralador do que zero, período de avaliação de 5 minutos e frequência de 5 minutos.

A taxa de volume de ingestão atingiu 80% do limiar:
```Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The data ingestion volume rate crossed 80% of the threshold"
```

Taxa de volume de ingestão atingiu o limiar:
```Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The data ingestion volume rate crossed the threshold"
```

>[!NOTE]
>Dependendo do tempo que usa o Log Analytics, poderá ter acesso a níveis de preços antigos. Saiba mais sobre [os níveis de preços do legado Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 
