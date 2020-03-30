---
title: incluir ficheiro
description: incluir ficheiro
services: search
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 179f525b7b6a7e51889b14b66df6c537ca56bd75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272941"
---
O armazenamento é limitado pelo espaço do disco ou por um limite rígido no *número máximo* de índices, documentos ou outros recursos de alto nível, o que vier em primeiro lugar. Os seguintes documentos de tabela limitam-se ao armazenamento. Para limites máximos em índices, documentos e outros objetos, consulte [Limites por recurso](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Recurso | Gratuito | Básico<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Acordo de nível de serviço (SLA)<sup>3</sup>  |Não |Sim |Sim |Sim |Sim |Sim |Sim |Sim |
| Armazenamento por partição |50 MB |2GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Partições por serviço |N/D |1 |12 |12 |12 |3 |12 |12 |
| Tamanho da partição |N/D |2GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Réplicas |N/D |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> O básico tem uma divisória fixa. Neste nível, são utilizadas unidades de pesquisa adicionais para alocar mais réplicas para trabalhos de trabalho de consulta aumentados.

<sup>2</sup> S3 HD tem um limite rígido de três divisórias, que é inferior ao limite de partição para S3. O limite de partição inferior é imposto porque a contagem de índice do S3 HD é significativamente mais elevada. Dado que existem limites de serviço para recursos informáticos (processamento e armazenamento) e conteúdo (índices e documentos), o limite de conteúdo é atingido primeiro.

<sup>3</sup> São oferecidos contratos de nível de serviço para serviços de faturação em recursos dedicados. Serviços gratuitos e funcionalidades de pré-visualização não têm SLA. Para serviços de faturação, os SLAs efetuam efeitos quando prevê redundância suficiente para o seu serviço. São necessárias duas ou mais réplicas para consulta (ler) SLAs. São necessárias três ou mais réplicas para consulta e indexação (read-write) SLAs. O número de divisórias não é uma consideração da SLA. 