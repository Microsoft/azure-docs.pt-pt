---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: search
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/11/2020
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: fe5c8129434ddb4eec2dd25a3f123f28b4db221b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83682642"
---
Um serviço de pesquisa é limitado pelo espaço do disco ou por um limite rígido no número máximo de índices ou indexadores, o que vier primeiro. Os seguintes limites de armazenamento de documentos de tabela. Para obter limites máximos de objetos, consulte [limites por recurso.](../articles/search/search-limits-quotas-capacity.md#index-limits)

| Recurso | Gratuito | Básico<sup>1</sup> | S1 | S2 | S3 | S3 &nbsp; HD | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Contrato de nível de serviço (SLA)<sup>2</sup>  |Não |Sim |Sim |Sim |Sim |Sim |Sim |Sim |
| Armazenamento por partição |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Partições por serviço |N/D |1 |12 |12 |12 |3 |12 |12 |
| Tamanho da partição |N/D |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Réplicas |N/D |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> Básico tem uma divisória fixa. Unidades de pesquisa adicionais podem ser usadas para adicionar réplicas para volumes de consulta maiores.

<sup>2 Os</sup> contratos de nível de serviço estão em vigor para serviços faturados em recursos dedicados. Os serviços gratuitos e as funcionalidades de pré-visualização não têm SLA. Para serviços de faturação, as SLAs fazem efeito quando prestam despedimentos suficientes para o seu serviço. São necessárias duas ou mais réplicas para consulta (ler) SLAs. São necessárias três ou mais réplicas para consultas e indexação (ler-escrever) SLAs. O número de divisórias não é uma consideração de SLA. 