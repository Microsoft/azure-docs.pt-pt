---
title: Azure Analysis Services limites de recurso e objeto | Microsoft Docs
description: Este artigo descreve os limites de recurso e objeto para um servidor de Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f309c9863eb2f3065251537380a2977839f990d8
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73573202"
---
# <a name="analysis-services-resource-and-object-limits"></a>Analysis Services limites de recurso e objeto

Este artigo descreve os limites de objeto de modelo e de recurso.

## <a name="tier-limits"></a>Limites de camada

Para obter limites de QPU e memória para as camadas de desenvolvedor, básica e Standard, consulte a [página de preços Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="object-limits"></a>Limites de objeto

Esses limites são teóricos. O desempenho será reduzido em números inferiores.

|Object|Tamanhos/números máximos|  
|------------|----------------------------|  
|Bancos de dados em uma instância|16.000|  
|Número combinado de tabelas e colunas em um banco de dados|16.000|  
|Linhas em uma tabela|Ilimitado<br /><br /> **AVISO:** Com a restrição de que nenhuma coluna única na tabela pode ter mais de 1.999.999.997 valores distintos.|  
|Hierarquias em uma tabela|15.999|  
|Níveis em uma hierarquia|15.999|  
|Relações|8,000|  
|Colunas de chave em todas as tabelas|15.999|  
|Medidas em tabelas|2 ^ 31-1 = 2.147.483.647|  
|Células retornadas por uma consulta|2 ^ 31-1 = 2.147.483.647|  
|Tamanho do registro da consulta de origem|64 K|  
|Comprimento dos nomes de objeto|512 caracteres|  


