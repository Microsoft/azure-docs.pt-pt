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
ms.openlocfilehash: 21e2d3f75028d239175effa7a3608cc18ccfc95c
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305320"
---
**Volume de recolha de dados e retenção** 

| Escalão | Limitar por dia | Retenção de dados | Comentário |
|:---|:---|:---|:---|
| Escalão de preço atual por GB<br>(introduzida em Abril de 2018) | Sem limite | 30 - 730 dias | Retenção de dados para além dos 31 dias está disponível para encargos adicionais. Saiba mais sobre os preços do Azure Monitor. |
| Escalões gratuitos legados<br>(introduzida em Abril de 2016) | 500 MB | 7 dias | Quando a sua área de trabalho atinge a 500 MB por limite de dias, ingestão de dados parada e retomada no início do dia seguinte. Os dias são baseados no fuso horário UTC. Tenha em atenção que os dados recolhidos pelo centro de segurança do Azure não estão incluído neste 500 MB por limite de dias e continuará a ser recolhidos acima desse limite.  |
| Escalão de legado autónomo por GB<br>(introduzida em Abril de 2016) | Sem limite | 30 para 730 dias | Retenção de dados para além dos 31 dias está disponível para encargos adicionais. Saiba mais sobre os preços do Azure Monitor. |
| Herdado por nó (OMS)<br>(introduzida em Abril de 2016) | Sem limite | 30 para 730 dias | Retenção de dados para além dos 31 dias está disponível para encargos adicionais. Saiba mais sobre os preços do Azure Monitor. |
| Escalão Standard legado | Sem limite | 30 dias  | Retenção não pode ser ajustada |
| Escalão Premium legado | Sem limite | 365 dias  | Retenção não pode ser ajustada |

**Número de áreas de trabalho por subscrição.**

| Escalão de preço    | Limite de área de trabalho | Comentários
|:---|:---|:---|
| Escalão gratuito  | 10 | Não é possível aumentar este limite. |
| Todos os outros escalões | Sem limite | Está limitado pelo número de recursos dentro de um grupo de recursos e o número de grupos de recursos por subscrição. |

**Azure portal**

| Category | Limits | Comentários |
|:---|:---|:---|
| Máximo de registos devolvido por uma consulta de registo | 10,000 | Reduza os resultados usando o âmbito de consulta e filtros de intervalo de tempo na consulta. |


**API do Recoletor de dados**

| Category | Limits | Comentários |
|:---|:---|:---|
| Tamanho máximo para um post individual | 30 MB | Dividir volumes maiores em várias postagens. |
| Tamanho máximo de valores de campo  | 32 KB | Os campos com mais de 32 KB são truncados. |

**API de pesquisa**

| Category | Limits | Comentários |
|:---|:---|:---|
| Máximo de registos devolvido para dados não agregados | 5,000 | |
| Máximo de registos para dados agregados | 500,000 | Dados agregados são uma pesquisa que inclui o `summarize` comando. |
| Tamanho máximo de dados devolvidos | bytes 64,000,000 (~ 61 MiB)| |
| Tempo de execução máxima de consulta | 10 minutos | Ver [tempos limite](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) para obter detalhes.  |
| Taxa máxima de pedidos | 200 pedidos por 30 segundos por endereço IP de cliente ou de utilizador do AAD | Ver [limites de velocidade](https://dev.loganalytics.io/documentation/Using-the-API/Limits) para obter detalhes. |

**Limites da área de trabalho geral**

| Category | Limits | Comentários |
|:---|:---|:---|
| Máximo de colunas numa tabela         | 500 | |
| Número máximo de carateres para o nome da coluna | 500 | |
| Regiões atingiu o limite | EUA Centro-Oeste | Atualmente não é possível criar uma nova área de trabalho nesta região, uma vez que está no limite de capacidade temporário. Este limite está previsto para serem resolvidas pela final de Setembro de 2019. |
| Exportação de dados | Não se encontra disponível | Utilize a função do Azure ou a aplicação lógica para agregar e exportar dados. | 

>[!NOTE]
>Consoante o tempo que estava usando o Log Analytics, poderá ter acesso aos escalões de preços legado. Saiba mais sobre [legado do Log Analytics escalões de preço](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 