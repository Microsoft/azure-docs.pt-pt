---
title: Recursos dos Serviços de Análise Azure e limites de objetos Microsoft Docs
description: Este artigo descreve limites de recursos e objetos para um servidor de Serviços de Análise Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f309c9863eb2f3065251537380a2977839f990d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73573202"
---
# <a name="analysis-services-resource-and-object-limits"></a>Recursos de serviços de análise e limites de objetos

Este artigo descreve os limites de objetos de recursos e modelos.

## <a name="tier-limits"></a>Limites de nível

Para os limites de QPU e Memória para os níveis de programador, básicos e standard, consulte a página de preços dos Serviços de [Análise Azure](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="object-limits"></a>Limites de objetos

Estes limites são teóricos. O desempenho será diminuído em números mais baixos.

|Objeto|Tamanhos/números máximos|  
|------------|----------------------------|  
|Bases de dados, num caso,|16 000|  
|Número combinado de tabelas e colunas numa base de dados|16 000|  
|Filas em uma mesa|Ilimitado<br /><br /> **Aviso:** Com a restrição de que nenhuma coluna na tabela pode ter mais de 1.999.999.997 valores distintos.|  
|Hierarquias em uma mesa|15,999|  
|Níveis numa hierarquia|15,999|  
|Relações|8,000|  
|Colunas-chave em todas as tabelas|15,999|  
|Medidas em mesas|2^31-1 = 2.147.483.647|  
|Células devolvidas por uma consulta|2^31-1 = 2.147.483.647|  
|Tamanho recorde da consulta de origem|64 K|  
|Comprimento dos nomes dos objetos|512 caracteres|  


