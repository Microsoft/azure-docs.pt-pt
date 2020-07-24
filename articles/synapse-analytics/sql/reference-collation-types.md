---
title: Apoio à colagem
description: Tipos de colagem suportados no Azure Synapse SQL
author: filippopovic
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: c36df4745e276471e35addf4774470dbaaa9e150
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080714"
---
# <a name="database-collation-support-for-synapse-sql"></a>Suporte de colagem de base de dados para Sinaapse SQL

As colagens fornecem as regras de local, página de código, ordem de classificação e sensibilidade ao carácter para tipos de dados baseados em caracteres. Uma vez escolhidos, todas as colunas e expressões que necessitem de informações de colagem herdam a colagem escolhida a partir da definição da base de dados. A herança predefinida pode ser ultrapassada declarando explicitamente uma colagem diferente para um tipo de dados baseado em caracteres.

Pode alterar a colagem da base de dados predefinida a partir do portal Azure quando criar uma nova base de dados de piscinas SQL. Esta capacidade torna ainda mais fácil criar uma nova base de dados utilizando uma das 3800 colagens de dados suportadas.

Pode especificar a colisão padrão da base de dados synapse SQL a pedido no momento da criação utilizando a declaração CREATE DATABASE.

## <a name="changing-collation"></a>Mudança de colagem
Para alterar a colagem padrão para base de dados de piscinas SQL, você simples atualização para o campo Collation na experiência de provisionamento. Por exemplo, se quisesse alterar a colagem por defeito para sensível a casos, simplesmente mudaria o nome da Collation de SQL_Latin1_General_CP1_CI_AS para SQL_Latin1_General_CP1_CS_AS. 

Para alterar a colagem predefinida para base de dados a pedido do SQL, pode utilizar a declaração ALTER DATABASE.

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

Além disso, a piscina SQL não suporta os seguintes tipos de colagem:

*    SQL_EBCDIC1141_CP1_CS_AS
*    SQL_EBCDIC277_2_CP1_CS_AS
*    UTF-8

## <a name="checking-the-current-collation"></a>Verificação da atual colagem
Para verificar a colagem atual da base de dados, pode executar o seguinte corte T-SQL:
```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```
Quando passou 'Collation' como parâmetro de propriedade, a função DatabasePropertyEx devolve a atual colagem para a base de dados especificada. Pode saber mais sobre a função DatabasePropertyEx na MSDN.

## <a name="next-steps"></a>Passos seguintes

Informações adicionais sobre as melhores práticas para piscina SQL e SQL on-demand podem ser encontradas nos seguintes artigos:

- [Boas Práticas para piscina SQL](best-practices-sql-pool.md)
- [Melhores práticas para o SQL a pedido](best-practices-sql-on-demand.md)


