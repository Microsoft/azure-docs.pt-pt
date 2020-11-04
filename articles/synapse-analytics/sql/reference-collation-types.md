---
title: Apoio à colagem
description: Suporte de tipos de colagem para Synapse SQL em Azure Synapse Analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 436dbac814197556385a33d956928f97fd4716bf
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93311921"
---
# <a name="database-collation-support-for-synapse-sql-in-azure-synapse-analytics"></a>Suporte de colagem de base de dados para Synapse SQL em Azure Synapse Analytics 

As colagens fornecem as regras de local, página de código, ordem de classificação e sensibilidade ao carácter para tipos de dados baseados em caracteres. Uma vez escolhidos, todas as colunas e expressões que necessitem de informações de colagem herdam a colagem escolhida a partir da definição da base de dados. A herança predefinida pode ser ultrapassada declarando explicitamente uma colagem diferente para um tipo de dados baseado em caracteres.

Pode alterar a colagem da base de dados predefinida a partir do portal Azure quando criar uma nova base de dados dedicada à piscina SQL. Esta capacidade torna ainda mais fácil criar uma nova base de dados utilizando uma das 3800 colagens de dados suportadas.

Pode especificar a colagem padrão da base de dados de piscinas SQL sem servidor no momento da criação utilizando a declaração CREATE DATABASE.

## <a name="change-collation"></a>Alterar colagem
Para alterar a colagem padrão para base de dados de piscinas SQL dedicada, atualize o campo Collation na experiência de provisionamento. Por exemplo, se quisesse alterar a colagem por defeito para sensível a casos, mudaria o nome da Collation de SQL_Latin1_General_CP1_CI_AS para SQL_Latin1_General_CP1_CS_AS. 

Para alterar a colagem padrão para uma base de dados de piscinas SQL sem servidor, pode utilizar a declaração ALTER DATABASE.

## <a name="list-of-unsupported-collation-types"></a>Lista de tipos de colagem não suportados
*    Japanese_Bushu_Kakusu_140_BIN
*    Japanese_Bushu_Kakusu_140_BIN2
*    Japanese_Bushu_Kakusu_140_CI_AI_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI
*    Japanese_Bushu_Kakusu_140_CI_AI_WS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AI
*    Japanese_Bushu_Kakusu_140_CS_AI_WS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS
*    Japanese_XJIS_140_BIN
*    Japanese_XJIS_140_BIN2
*    Japanese_XJIS_140_CI_AI_VSS
*    Japanese_XJIS_140_CI_AI_WS_VSS
*    Japanese_XJIS_140_CI_AI_KS_VSS
*    Japanese_XJIS_140_CI_AI_KS_WS_VSS
*    Japanese_XJIS_140_CI_AS_VSS
*    Japanese_XJIS_140_CI_AS_WS_VSS
*    Japanese_XJIS_140_CI_AS_KS_VSS
*    Japanese_XJIS_140_CI_AS_KS_WS_VSS
*    Japanese_XJIS_140_CS_AI_VSS
*    Japanese_XJIS_140_CS_AI_WS_VSS
*    Japanese_XJIS_140_CS_AI_KS_VSS
*    Japanese_XJIS_140_CS_AI_KS_WS_VSS
*    Japanese_XJIS_140_CS_AS_VSS
*    Japanese_XJIS_140_CS_AS_WS_VSS
*    Japanese_XJIS_140_CS_AS_KS_VSS
*    Japanese_XJIS_140_CS_AS_KS_WS_VSS
*    Japanese_XJIS_140_CI_AI
*    Japanese_XJIS_140_CI_AI_WS
*    Japanese_XJIS_140_CI_AI_KS
*    Japanese_XJIS_140_CI_AI_KS_WS
*    Japanese_XJIS_140_CI_AS
*    Japanese_XJIS_140_CI_AS_WS
*    Japanese_XJIS_140_CI_AS_KS
*    Japanese_XJIS_140_CI_AS_KS_WS
*    Japanese_XJIS_140_CS_AI
*    Japanese_XJIS_140_CS_AI_WS
*    Japanese_XJIS_140_CS_AI_KS
*    Japanese_XJIS_140_CS_AI_KS_WS
*    Japanese_XJIS_140_CS_AS
*    Japanese_XJIS_140_CS_AS_WS
*    Japanese_XJIS_140_CS_AS_KS
*    Japanese_XJIS_140_CS_AS_KS_WS

Além disso, a piscina SQL dedicada não suporta os seguintes tipos de colagem:

*    SQL_EBCDIC1141_CP1_CS_AS
*    SQL_EBCDIC277_2_CP1_CS_AS
*    UTF-8

## <a name="check-the-current-collation"></a>Verifique a atual colagem
Para verificar a colagem atual da base de dados, pode executar o seguinte corte T-SQL:
```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```
Quando passou 'Collation' como parâmetro de propriedade, a função DatabasePropertyEx devolve a atual colagem para a base de dados especificada. Pode saber mais sobre a função DatabasePropertyEx na MSDN.

## <a name="next-steps"></a>Passos seguintes

Informações adicionais sobre as melhores práticas para piscina SQL dedicada e piscina SQL sem servidor podem ser encontradas nos seguintes artigos:

- [Boas Práticas para piscina SQL dedicada](best-practices-sql-pool.md)
- [Melhores práticas para piscina SQL sem servidor](best-practices-sql-on-demand.md)


