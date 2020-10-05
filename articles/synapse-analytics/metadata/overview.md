---
title: Modelo de metadados partilhados
description: O Azure Synapse Analytics permite que os diferentes motores computacionais do espaço de trabalho partilhem bases de dados e tabelas entre as suas piscinas Spark (pré-visualização), motor a pedido SQL (pré-visualização) e piscinas SQL.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: c11a0ccb08f03775a07716e6c547d849cda347dd
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "87387341"
---
# <a name="azure-synapse-analytics-shared-metadata"></a>Azure Synapse Analytics partilhou metadados

O Azure Synapse Analytics permite que os diferentes motores computacionais do espaço de trabalho partilhem bases de dados e tabelas entre as suas piscinas Spark (pré-visualização) e o motor a pedido do SQL (pré-visualização).

[!INCLUDE [preview](../includes/note-preview.md)]

A partilha suporta o chamado padrão moderno de armazém de dados e dá aos motores SQL espaço de trabalho acesso a bases de dados e tabelas criadas com Spark. Também permite que os motores SQL criem os seus próprios objetos que não estão a ser partilhados com os outros motores.

## <a name="support-the-modern-data-warehouse"></a>Apoie o moderno armazém de dados

O modelo de metadados partilhado suporta o padrão moderno de armazém de dados da seguinte forma:

1. Os dados do lago de dados são preparados e estruturados eficientemente com a Spark armazenando os dados preparados em (possivelmente divididos) quadros com base de dados contidos em várias bases de dados.

2. A Spark criou bases de dados e todas as suas tabelas tornam-se visíveis em qualquer um dos casos de piscina spark do Azure Synapse e podem ser usados a partir de qualquer um dos trabalhos da Spark. Esta capacidade está sujeita às [permissões,](#security-model-at-a-glance) uma vez que todas as piscinas Spark num espaço de trabalho partilham a mesma loja de catálogos subjacente.

3. A Spark criou bases de dados e as suas tabelas com apoio parquet tornam-se visíveis no motor a pedido do SQL do espaço de trabalho. [As bases de dados](database.md) são criadas automaticamente nos metadados a pedido do SQL, e tanto as [tabelas externas como geridas criadas](table.md) por um trabalho Spark são tornadas acessíveis como tabelas externas nos metadados a pedido do SQL no `dbo` esquema da base de dados correspondente. 

<!--[INSERT PICTURE]-->

<!--__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with shared metadata-->

A sincronização do objeto ocorre assíncronia. Os objetos terão um ligeiro atraso de alguns segundos até que apareçam no contexto SQL. Uma vez que apareçam, podem ser questionados, mas não atualizados nem alterados pelos motores SQL que têm acesso aos mesmos.

## <a name="shared-metadata-objects"></a>Objetos de metadados partilhados

A spark permite-lhe criar bases de dados, tabelas externas, tabelas geridas e vistas. Uma vez que as vistas spark requerem um motor Spark para processar a declaração de Definição do Spark SQL, e não podem ser processadas por um motor SQL, apenas as bases de dados e as suas tabelas externas e geridas que utilizam o formato de armazenamento Parquet são partilhadas com o motor SQL do espaço de trabalho. As vistas de faíscas são partilhadas apenas entre os casos da piscina spark.

## <a name="security-model-at-a-glance"></a>Modelo de segurança num relance

As bases de dados e tabelas Spark, juntamente com as suas representações sincronizadas no motor SQL, estão asseguradas ao nível de armazenamento subjacente. Quando a tabela é consultada por qualquer um dos motores que o proponente de consulta tem o direito de usar, o principal de segurança do proponente de consulta está a ser transmitido para os ficheiros subjacentes. As permissões são verificadas ao nível do sistema de ficheiros.

Para mais informações, consulte [a base de dados partilhada Azure Synapse Analytics.](database.md)

## <a name="change-maintenance"></a>Alterar manutenção

Se um objeto de metadados for eliminado ou alterado com o Spark, as alterações são captadas e propagadas para o motor a pedido do SQL. A sincronização é assíncronia e as alterações refletem-se no motor SQL após um curto atraso.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre as bases de dados de metadados partilhados da Azure Synapse Analytics](database.md)
- [Saiba mais sobre as tabelas de metadados partilhados da Azure Synapse Analytics](table.md)

