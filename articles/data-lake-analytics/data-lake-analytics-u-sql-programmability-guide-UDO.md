---
title: Guia de programabilidade UDO UDO UDO para O Lago de Dados Azure
description: Conheça a programabilidade U-SQL UDO Azure Data Lake Analytics para permitir criar um bom script USQL.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 02360c68e5e830ceee69075fd5532b126d85bec2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512654"
---
# <a name="u-sql-user-defined-objects-overview"></a>Visão geral de objetos definidos pelo utilizador U-SQL


## <a name="u-sql-user-defined-objects-udo"></a>U-SQL: objetos definidos pelo utilizador: UDO
O U-SQL permite-lhe definir objetos de programabilidade personalizados, que são chamados objetos definidos pelo utilizador ou UDO.

Segue-se uma lista de UDO em U-SQL:

* Extratores definidos pelo utilizador
    * Extrair linha por linha
    * Usado para implementar a extração de dados a partir de ficheiros estruturados personalizados

* Saídadores definidos pelo utilizador
    * Linha de saída por linha
    * Usado para desatar tipos de dados personalizados ou formatos de ficheiros personalizados

* Processadores definidos pelo utilizador
    * Pegue uma linha e produza uma linha
    * Usado para reduzir o número de colunas ou produzir novas colunas com valores derivados de um conjunto de colunas existente

* Aplicadores definidos pelo utilizador
    * Pegue uma linha e produza 0 a n linhas
    * Usado com OUTER/CROSS APPLY

* Combinadores definidos pelo utilizador
    * Combina conjuntos de linhas -- JOINs definidos pelo utilizador

* Redutores definidos pelo utilizador
    * Tome n linhas e produza uma linha
    * Usado para reduzir o número de linhas

UDO é tipicamente chamado explicitamente no script U-SQL como parte das seguintes declarações U-SQL:

* EXTRAIR
* SAÍDA
* PROCESSO
* COMBINAR
* REDUZIR

> [!NOTE]  
> Os UDO's limitam-se a consumir 0,5Gb de memória.  Esta limitação de memória não se aplica às execuções locais.

## <a name="next-steps"></a>Passos seguintes
* [Guia de programabilidade U-SQL - visão geral](data-lake-analytics-u-sql-programmability-guide.md)
* [Guia de programabilidade U-SQL - UDT e UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)