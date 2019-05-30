---
title: incluir ficheiro
description: incluir ficheiro
services: log-analytics
author: MGoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 05/16/2018
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: 34f2ab8f7ccafb8b30e298cd71e09171ad8c87cb
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238586"
---
Os limites seguintes aplicam-se aos recursos do Azure Log Analytics por subscrição.

| Resource | Limite predefinido | Comentários
| --- | --- | --- |
| Número de áreas de trabalho gratuitas por subscrição | 10 | Não é possível aumentar este limite. |
| Número de áreas de trabalho pagas por subscrição | N/A | Está limitado pelo número de recursos dentro de um grupo de recursos e o número de grupos de recursos por subscrição. | 

>[!NOTE]
>A partir de 2 de Abril de 2018, novas áreas de trabalho numa nova subscrição utilizam automaticamente o *por GB* plano de preços. Para as subscrições existentes criadas antes 2 de Abril, ou uma subscrição que estava associada a uma inscrição de Enterprise Agreement já existente, pode continuar a escolha de entre três escalões de preço para novas áreas de trabalho. 
>

Os limites seguintes aplicam-se para cada área de trabalho do Log Analytics.

|  | Livre | Standard | Premium | Autónomo | OMS | Por GB |
| --- | --- | --- | --- | --- | --- |--- |
| Volume de dados recolhido por dia |500 MB<sup>1</sup> |Nenhuma |Nenhum | Nenhum | Nenhum | Nenhuma
| Período de retenção de dados |7 dias |1 mês |12 meses | 1 mês<sup>2</sup> | 1 mês<sup>2</sup>| 1 mês<sup>2</sup>|

<sup>1</sup>quando os clientes atingem o seu limite de transferência de dados diário 500 MB, a análise de dados é parada e retomada no início do dia seguinte. Os dias são baseados no fuso horário UTC.

<sup>2</sup>o período de retenção de dados para o autónomo, OMS e planos de preços por GB, pode ser aumentado para 730 dias.

| Category | Limits | Comentários
| --- | --- | --- |
| API do Recoletor de Dados | Tamanho máximo para um post individual é 30 MB.<br>Tamanho máximo de valores de campos é 32 KB. | Dividir volumes maiores em várias postagens.<br>Os campos com mais de 32 KB são truncados. |
| API de Pesquisa | 5000 registos devolvidos para dados não agregados.<br>500 000 registos para dados agregados. | Dados agregados são uma pesquisa que inclui o `summarize` comando.
 
