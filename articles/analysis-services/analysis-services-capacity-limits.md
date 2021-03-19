---
title: Recursos dos Serviços de Análise Azure e limites de objetos | Microsoft Docs
description: Este artigo descreve limites de recursos e objetos para um servidor Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c2caab4e449f4299d00fff14b697887ec00f35e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "83697486"
---
# <a name="analysis-services-resource-and-object-limits"></a>Recursos de serviços de análise e limites de objetos

Este artigo descreve limites de recursos e objetos de modelo.

## <a name="tier-limits"></a>Limites de nível

Para limites de QPU e Memória para os níveis de desenvolvedor, básico e padrão, consulte a [página de preços dos Serviços de Análise Azure](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="object-limits"></a>Limites de objetos

Estes limites são teóricos. O desempenho será diminuído em números mais baixos.

|Objeto|Tamanhos/números máximos|  
|------------|----------------------------|  
|Bases de dados em um caso|16 000|  
|Número combinado de tabelas e colunas numa base de dados|16 000|  
|Fileiras em uma mesa|Ilimitado<br /><br /> **Aviso:** Com a restrição de que nenhuma coluna na tabela pode ter mais de 1.999.999.997 valores distintos.|  
|Hierarquias em uma mesa|15,999|  
|Níveis numa hierarquia|15,999|  
|Relações|8,000|  
|Colunas-chave em todas as tabelas|15,999|  
|Medidas em tabelas|2^31-1 = 2.147.483.647|  
|Células devolvidas por uma consulta|2^31-1 = 2.147.483.647|  
|Tamanho recorde da consulta de origem|64 K|  
|Comprimento dos nomes dos objetos|512 caracteres|  


