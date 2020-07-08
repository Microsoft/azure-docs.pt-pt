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
ms.openlocfilehash: 864b37c9e59786546ad2c29faf8457cfc3a21f6b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82161159"
---
**Volume e retenção de recolha de dados** 

| Escalão | Limite por dia | Retenção de dados | Comentário |
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
| Taxa máxima de pedido | 200 pedidos por 30 segundos por utilizador AAD ou endereço IP do cliente | Consulte [os limites da taxa](https://dev.loganalytics.io/documentation/Using-the-API/Limits) para mais detalhes. |

**Limites gerais do espaço de trabalho**

| Categoria | Limite | Comentários |
|:---|:---|:---|
| Colunas máximas numa tabela         | 500 | |
| Caracteres máximos para nome de coluna | 500 | |
| Exportação de dados | Não disponível atualmente | Utilize a Azure Function ou a Logic App para agregar e exportar dados. | 

**Taxa de volume de ingestão de dados**


O Azure Monitor é um serviço de dados de alta escala que serve milhares de clientes que enviam terabytes de dados todos os meses a um ritmo crescente. O limite de taxa de volume de ingestão predefinido para os dados enviados a partir de recursos Azure utilizando [definições de diagnóstico](../articles/azure-monitor/platform/diagnostic-settings.md) é de aproximadamente **6 GB/min** por espaço de trabalho. Este é um valor aproximado, uma vez que o tamanho real pode variar entre os tipos de dados dependendo do comprimento do registo e da sua relação de compressão. Este limite não se aplica aos dados enviados de agentes ou da [API do Colecionador de Dados.](../articles/azure-monitor/platform/data-collector-api.md)

Se enviar dados a uma taxa mais elevada para um único espaço de trabalho, alguns dados são retirados e um evento é enviado para a tabela *Operação* no seu espaço de trabalho a cada 6 horas enquanto o limiar continua a ser ultrapassado. Se o seu volume de ingestão continuar a exceder o limite de taxa ou estiver à espera de o atingir em breve, pode solicitar um aumento para o seu espaço de trabalho enviando um e-mail LAIngestionRate@microsoft.com ou abrindo um pedido de apoio.
 
Para ser notificado em tal evento no seu espaço de trabalho, crie uma [regra de alerta de registo](../articles/azure-monitor/platform/alerts-log.md) utilizando a seguinte consulta com base lógica de alerta no número de resultados mais ralados do que zero.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


>[!NOTE]
>Dependendo do tempo que usa o Log Analytics, poderá ter acesso a níveis de preços antigos. Saiba mais sobre [os níveis de preços do legado Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 
