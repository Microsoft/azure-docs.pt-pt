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
ms.openlocfilehash: 5d0c43fbcc1c59c3281f412aad96a3942a5c79b1
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "70392899"
---
**Retenção e volume de coleta de dados** 

| Escalão | Limite por dia | Retenção de dados | Comentário |
|:---|:---|:---|:---|
| Tipo de preço atual por GB<br>(introduzido em abril de 2018) | Sem limite | 30-730 dias | A retenção de dados além de 31 dias está disponível para encargos adicionais. Saiba mais sobre preços de Azure Monitor. |
| Camadas gratuitas herdadas<br>(introduzido em abril de 2016) | 500 MB | 7 dias | Quando seu espaço de trabalho atinge o limite de 500 MB por dia, a ingestão de dados é interrompida e retomada no início do dia seguinte. Os dias são baseados no fuso horário UTC. Observe que os dados coletados pela central de segurança do Azure não estão incluídos neste limite de 500 MB por dia e continuarão a ser coletados acima desse limite.  |
| Camada autônoma herdada por GB<br>(introduzido em abril de 2016) | Sem limite | 30 a 730 dias | A retenção de dados além de 31 dias está disponível para encargos adicionais. Saiba mais sobre preços de Azure Monitor. |
| Herdado por nó (OMS)<br>(introduzido em abril de 2016) | Sem limite | 30 a 730 dias | A retenção de dados além de 31 dias está disponível para encargos adicionais. Saiba mais sobre preços de Azure Monitor. |
| Camada padrão herdada | Sem limite | 30 dias  | A retenção não pode ser ajustada |
| Camada Premium herdada | Sem limite | 365 dias  | A retenção não pode ser ajustada |

**Número de espaços de trabalho por assinatura.**

| Escalão de preço    | Limite do espaço de trabalho | Comentários
|:---|:---|:---|
| Escalão gratuito  | 10 | Esse limite não pode ser aumentado. |
| Todas as outras camadas | Sem limite | Você está limitado pelo número de recursos dentro de um grupo de recursos e o número de grupos de recursos por assinatura. |

**Azure portal**

| Category | Limites | Comentários |
|:---|:---|:---|
| Máximo de registros retornados por uma consulta de log | 10,000 | Reduza os resultados usando o escopo da consulta, o intervalo de tempo e os filtros na consulta. |


**API do coletor de dados**

| Category | Limites | Comentários |
|:---|:---|:---|
| Tamanho máximo para uma única postagem | 30 MB | Dividir volumes maiores em várias postagens. |
| Tamanho máximo para valores de campo  | 32 KB | Os campos com mais de 32 KB são truncados. |

**API de pesquisa**

| Category | Limites | Comentários |
|:---|:---|:---|
| Máximo de registros retornados em uma única consulta | 500,000 | |
| Tamanho máximo dos dados retornados | 64 milhões bytes (~ 61 MiB)| |
| Tempo de execução máximo da consulta | 10 minutos | Consulte [tempos limite](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) para obter detalhes.  |
| Taxa de solicitação máxima | 200 solicitações por 30 segundos por usuário do AAD ou endereço IP do cliente | Consulte [limites de taxa](https://dev.loganalytics.io/documentation/Using-the-API/Limits) para obter detalhes. |

**Limites gerais do espaço de trabalho**

| Category | Limites | Comentários |
|:---|:---|:---|
| Máximo de colunas em uma tabela         | 500 | |
| Máximo de caracteres para o nome da coluna | 500 | |
| Regiões em capacidade | EUA Centro-Oeste | No momento, não é possível criar um novo espaço de trabalho nesta região, pois ele está em um limite de capacidade temporário. Esse limite está planejado para ser resolvido até o final de outubro de 2019. |
| Exportação de dados | Não disponível no momento | Use o Azure Function ou o aplicativo lógico para agregar e exportar dados. | 

>[!NOTE]
>Dependendo de quanto tempo você esteve usando Log Analytics, você pode ter acesso a tipos de preço herdados. Saiba mais sobre [log Analytics tipos de preço herdados](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 