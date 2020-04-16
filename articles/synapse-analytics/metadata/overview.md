---
title: Modelo de metadados partilhados azure Synapse Analytics
description: O Azure Synapse Analytics permite que os diferentes motores computacionais do espaço de trabalho partilhem bases de dados e tabelas entre as suas piscinas Spark (pré-visualização), o motor on-demand SQL (pré-visualização) e as piscinas SQL.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: 3b26d516080961a482a3ba67f314e98ece4c9f24
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424119"
---
# <a name="azure-synapse-analytics-shared-metadata"></a>Metadados partilhados da Azure Synapse Analytics

O Azure Synapse Analytics permite que os diferentes motores computacionais do espaço de trabalho partilhem bases de dados e tabelas entre as suas piscinas Spark (pré-visualização), o motor on-demand SQL (pré-visualização) e as piscinas SQL.

[!INCLUDE [preview](../includes/note-preview.md)]



A partilha suporta o chamado padrão moderno de armazém de dados e dá aos motores SQL espaço de trabalho acesso a bases de dados e tabelas criadas com a Spark. Também permite que os motores SQL criem os seus próprios objetos que não estão a ser partilhados com os outros motores.

## <a name="support-the-modern-data-warehouse"></a>Apoiar o armazém de dados moderno

O modelo de metadados partilhados suporta o padrão moderno de armazém de dados da seguinte forma:

1. Os dados do lago de dados são preparados e estruturados de forma eficiente com a Spark, armazenando os dados preparados em tabelas (possivelmente divididas) apoiadas pelo Parquet contidas em possivelmente várias bases de dados.

2. A Spark criou bases de dados e todas as suas mesas tornam-se visíveis em qualquer uma das instâncias de piscina de spark do espaço de trabalho Azure Synapse e pode ser usada a partir de qualquer um dos trabalhos spark. Esta capacidade está sujeita às [permissões,](#security-model-at-a-glance) uma vez que todas as piscinas spark num espaço de trabalho partilham a mesma loja de catálogo subjacente.

3. A Spark criou bases de dados e as suas mesas apoiadas pelo Parquet tornam-se visíveis no motor a pedido do Espaço de Trabalho SQL. As bases de dados são [criadas](database.md) automaticamente nos metadados a pedido do SQL, e tanto as [tabelas externas como geridas criadas](table.md) por um trabalho spark são tornadas acessíveis como tabelas externas nos metadados on-demand sQL no `dbo` esquema da base de dados correspondente. <!--For more details, see [ADD LINK].-->

4. Se houver casos de piscina SQL no espaço de trabalho que tenham a sincronização dos metadados ativada <!--[ADD LINK]--> ou se for criada uma nova instância de piscina SQL com a sincronização de metadados ativada, as bases de dados criadas pela Spark e as suas tabelas apoiadas pelo Parquet serão automaticamente mapeadas na base de dados de piscinas SQL, tal como descrito na base de [dados partilhada Azure Synapse Analytics.](database.md)

<!--[INSERT PICTURE]-->

<!--__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with shared metadata-->

A sincronização do objeto ocorre assincronicamente. Os objetos terão um ligeiro atraso de alguns segundos até aparecerem no contexto SQL. Uma vez que apareçam, podem ser consultados, mas não atualizados nem alterados pelos motores SQL que têm acesso aos mesmos.

## <a name="which-metadata-objects-are-shared"></a>Que objetos de metadados são partilhados

A Spark permite-lhe criar bases de dados, tabelas externas, tabelas geridas e vistas. Uma vez que as vistas spark requerem um motor Spark para processar a declaração de Spark SQL, e não podem ser processadas por um motor SQL, apenas bases de dados e suas tabelas externas e geridas contidas que usam o formato de armazenamento Parquet são partilhadas com os motores SQL do espaço de trabalho. As vistas de faíscas só são partilhadas entre os casos da piscina de Spark.

## <a name="security-model-at-a-glance"></a>Modelo de segurança num ápice

As bases de dados e tabelas Spark, juntamente com as suas representações sincronizadas nos motores SQL, estão seguras ao nível de armazenamento subjacente. Quando a tabela é questionada por qualquer um dos motores que o submetidor de consulta tem o direito de usar, o diretor de segurança do submissão está a ser transmitido para os ficheiros subjacentes. As permissões são verificadas ao nível do sistema de ficheiros.

Para mais informações, consulte a [base de dados partilhada azure Synapse Analytics](database.md).

## <a name="change-maintenance"></a>Alterar a manutenção

Se um objeto de metadados for eliminado ou alterado com a Spark, as alterações são captadas e propagadas ao motor on-demand SQL e às piscinas SQL que têm os objetos sincronizados. A sincronização é assíncrona e as mudanças refletem-se nos motores SQL após um curto atraso.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre as bases de dados de metadados partilhados da Azure Synapse Analytics](database.md)
- [Saiba mais sobre as tabelas de metadados partilhados da Azure Synapse Analytics](table.md)

