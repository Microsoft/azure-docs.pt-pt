---
title: incluir ficheiro
description: incluir ficheiro
services: log-analytics
author: MGoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 06/10/2019
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: c5fedc59c80c68fc222693a67664ef60ddd210a9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133697"
---
Os limites seguintes aplicam-se a cada área de trabalho do Log Analytics no atual baseado no consumo escalão de preço introduzido em Abril de 2018:

|     | Por GB de 2018 |
| --- | --- | 
| Volume de dados recolhido por dia | Nenhuma |
| Período de retenção de dados | 30 para 730 dias<sup>1</sup> |

Os limites seguintes aplicam-se para cada área de trabalho do Log Analytics o mais recente legado escalões de preço:

|  | Livre | Autónomo (por GB) | Por nó (OMS) |
| --- | --- | --- | --- | --- | --- |--- |
| Volume de dados recolhido por dia |500 MB<sup>2</sup> |Nenhuma |Nenhuma |
| Período de retenção de dados |7 dias | 30 para 730 dias<sup>1</sup> | 30 para 730 dias<sup>1</sup> |

Os limites seguintes aplicam-se para cada área de trabalho do Log Analytics a legada mais antiga escalões de preço:

|  | Standard | Premium | 
| --- | --- | --- | --- | --- | --- |--- |
| Volume de dados recolhido por dia | Nenhuma | Nenhuma | 
| Período de retenção de dados |30 dias | 365 dias |

<sup>1</sup>retenção de dados para além dos 31 dias está disponível para encargos adicionais. Saiba mais sobre [preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

<sup>2</sup>quando a sua área de trabalho atinge o limite de transferência de dados diário 500 MB, a análise de dados é parada e retomada no início do dia seguinte. Os dias são baseados no fuso horário UTC.

>[!NOTE]
>Consoante o tempo que estava usando o Log Analytics, poderá ter acesso aos escalões de preços legado. Saiba mais sobre [legado do Log Analytics escalões de preço](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 
>

Os limites seguintes aplicam-se aos recursos de Log Analytics do Azure (áreas de trabalho), por subscrição.

| Escalão de preço    | Número de áreas de trabalho por subscrição | Comentários
| --- | --- | --- |
| Escalão gratuito  | 10 | Não é possível aumentar este limite. |
| Todas as camadas que não seja gratuito | N/A | Está limitado pelo número de recursos dentro de um grupo de recursos e o número de grupos de recursos por subscrição. | 

Os limites seguintes aplicam-se às APIs de análise do registo:

| Category | Limits | Comentários
| --- | --- | --- |
| API do Recoletor de Dados | Tamanho máximo para um post individual é 30 MB.<br>Tamanho máximo de valores de campos é 32 KB. | Dividir volumes maiores em várias postagens.<br>Os campos com mais de 32 KB são truncados. |
| API de Pesquisa | 5000 registos devolvidos para dados não agregados.<br>500 000 registos para dados agregados. | Dados agregados são uma pesquisa que inclui o `summarize` comando.
 
