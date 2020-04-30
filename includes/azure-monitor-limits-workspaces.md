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
ms.openlocfilehash: 864b37c9e59786546ad2c29faf8457cfc3a21f6b
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "82161159"
---
**Volume e retenção de recolha de dados** 

| Escalão | Limite por dia | Retenção de dados | Comentário |
|:---|:---|:---|:---|
| Nível de preços atual por GB<br>(introduzido abril de 2018) | Sem limite | 30 - 730 dias | A retenção de dados para além dos 31 dias está disponível para encargos adicionais. Saiba mais sobre os preços do Monitor Azure. |
| Níveis livres legados<br>(introduzido abril de 2016) | 500 MB | 7 dias | Quando o seu espaço de trabalho atinge o limite de 500 MB por dia, a ingestão de dados para e retoma no início do dia seguinte. Os dias são baseados no fuso horário UTC. Note que os dados recolhidos pelo Azure Security Center não estão incluídos neste limite de 500 MB por dia e continuarão a ser recolhidos acima deste limite.  |
| Legado Autónomo por nível GB<br>(introduzido abril de 2016) | Sem limite | 30 a 730 dias | A retenção de dados para além dos 31 dias está disponível para encargos adicionais. Saiba mais sobre os preços do Monitor Azure. |
| Legado por nó (OMS)<br>(introduzido abril de 2016) | Sem limite | 30 a 730 dias | A retenção de dados para além dos 31 dias está disponível para encargos adicionais. Saiba mais sobre os preços do Monitor Azure. |
| Nível Padrão Legado | Sem limite | 30 dias  | A retenção não pode ser ajustada |
| Nível Legacy Premium | Sem limite | 365 dias  | A retenção não pode ser ajustada |

**Número de espaços de trabalho por subscrição.**

| Escalão de preço    | Limite de espaço de trabalho | Comentários
|:---|:---|:---|
| Escalão gratuito  | 10 | Este limite não pode ser aumentado. |
| Todos os outros níveis | Sem limite | Está limitado pelo número de recursos dentro de um grupo de recursos e pelo número de grupos de recursos por subscrição. |

**Portal do Azure**

| Categoria | Limite | Comentários |
|:---|:---|:---|
| Registos máximos devolvidos por uma consulta de registo | 10,000 | Reduza os resultados utilizando o âmbito de consulta, o intervalo de tempo e os filtros na consulta. |


**API de colecionador de dados**

| Categoria | Limite | Comentários |
|:---|:---|:---|
| Tamanho máximo para um único poste | 30 MB | Divida volumes maiores em vários postes. |
| Tamanho máximo para valores de campo  | 32 KB | Os campos com mais de 32 KB são truncados. |

**API de Pesquisa**

| Categoria | Limite | Comentários |
|:---|:---|:---|
| Registos máximos devolvidos numa única consulta | 500 000 | |
| Tamanho máximo dos dados devolvidos | 64.000.000 bytes (~61 MiB)| |
| Tempo máximo de consulta | 10 minutos | Consulte [os Timeouts](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) para obter mais detalhes.  |
| Taxa máxima de pedido | 200 pedidos por 30 segundos por utilizador aAD ou endereço IP do cliente | Consulte [os limites de tarifa](https://dev.loganalytics.io/documentation/Using-the-API/Limits) ção para mais detalhes. |

**Limites gerais do espaço de trabalho**

| Categoria | Limite | Comentários |
|:---|:---|:---|
| Colunas máximas numa tabela         | 500 | |
| Caracteres máximos para o nome da coluna | 500 | |
| Exportação de dados | Não disponível atualmente | Utilize a Função Azure ou a App Lógica para agregar e exportar dados. | 

**Taxa de volume de ingestão de dados**


O Azure Monitor é um serviço de dados de alta escala que serve milhares de clientes que enviam terabytes de dados todos os meses a um ritmo crescente. O limite de taxa de volume de ingestão padrão para os dados enviados a partir de recursos Do Azure utilizando [configurações](../articles/azure-monitor/platform/diagnostic-settings.md) de diagnóstico é de aproximadamente **6 GB/min** por espaço de trabalho. Este é um valor aproximado, uma vez que o tamanho real pode variar entre os tipos de dados dependendo do comprimento do registo e da sua relação de compressão. Este limite não se aplica aos dados enviados por agentes ou pela API do [Coletor](../articles/azure-monitor/platform/data-collector-api.md)de Dados .

Se enviar dados a uma taxa mais elevada para um único espaço de trabalho, alguns dados são retirados e um evento é enviado para a tabela *Operação* no seu espaço de trabalho a cada 6 horas enquanto o limiar continua a ser ultrapassado. Se o seu volume de ingestão continuar a exceder o limite de taxa ou se estiver à LAIngestionRate@microsoft.com espera de o atingir em breve, poderá solicitar um aumento para o seu espaço de trabalho enviando um e-mail para ou abrindo um pedido de apoio.
 
Para ser notificado sobre tal evento no seu espaço de trabalho, crie uma regra de alerta de [registo](../articles/azure-monitor/platform/alerts-log.md) utilizando a seguinte consulta com base lógica de alerta no número de resultados do que zero.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


>[!NOTE]
>Dependendo do tempo que tem usado o Log Analytics, poderá ter acesso a níveis de preços antigos. Saiba mais sobre os níveis de [preços do legado Log Analytics.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers) 
