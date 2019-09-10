---
title: incluir ficheiro
description: incluir ficheiro
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: b298c87d802314bd865bd6f38c35e4361eb69f3f
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "67184639"
---
O armazenamento é restrito por espaço em disco ou por um limite rígido no *número máximo* de índices, documentos ou outros recursos de alto nível, o que ocorrer primeiro. A tabela a seguir documenta os limites de armazenamento. Para obter os limites máximos de índices, documentos e outros objetos, consulte [limites por recurso](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Resource | Livre | Básico<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> | L1 | ERROS DE L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| SLA (contrato de nível de serviço)<sup>3</sup>  |Não |Sim |Sim |Sim |Sim |Sim |Sim |Sim |
| Armazenamento por partição |50 MB |2GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Partições por serviço |N/A |1 |12 |12 |12 |3 |12 |12 |
| Tamanho da partição |N/A |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Réplicas |N/A |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> básico tem uma partição fixa. Nessa camada, as unidades de pesquisa adicionais são usadas para alocar mais réplicas para cargas de trabalho de consulta aumentadas.

<sup>2</sup> S3 HD tem um limite rígido de três partições, que é menor do que o limite de partição para S3. O limite de partição inferior é imposto porque a contagem de índice do S3 HD é significativamente mais elevada. Dado que existem limites de serviço para recursos informáticos (processamento e armazenamento) e conteúdo (índices e documentos), o limite de conteúdo é atingido primeiro.

<sup>3</sup> os contratos de nível de serviço são oferecidos para serviços faturáveis em recursos dedicados. Serviços gratuitos e recursos de visualização não têm SLA. Para serviços faturáveis, os SLAs entram em vigor quando você provisiona redundância suficiente para seu serviço. Duas ou mais réplicas são necessárias para os SLAs de consulta (leitura). Três ou mais réplicas são necessárias para os SLAs de consulta e indexação (leitura/gravação). O número de partições não é uma consideração de SLA. 