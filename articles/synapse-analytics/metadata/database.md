---
title: Base de dados partilhada
description: O Azure Synapse Analytics fornece um modelo de metadados partilhado onde a criação de uma base de dados no conjunto Apache Spark sem servidor tornará acessível a partir da sua piscina SQL sem servidor e motores de piscina SQL.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 93602e522338166abac98c3e4a198e1aff392d21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97934973"
---
# <a name="azure-synapse-analytics-shared-database"></a>Base de dados partilhada Azure Synapse Analytics

O Azure Synapse Analytics permite que os diferentes motores computacionais do espaço de trabalho partilhem bases de dados e tabelas. Atualmente, as bases de dados e as tabelas Parquet que são criadas nas piscinas Apache Spark são automaticamente partilhadas com o motor de piscina SQL sem servidor.

Uma base de dados criada com uma obra Spark tornar-se-á visível com o mesmo nome para todas as piscinas spark atuais e futuras no espaço de trabalho, incluindo o motor de piscina SQL sem servidor. Não é possível adicionar objetos personalizados (tabelas externas, vistas, procedimentos) diretamente nesta base de dados replicada utilizando a piscina SQL sem servidor.

A base de dados padrão Spark, chamada `default` , também será visível no contexto da piscina SQL sem servidor como uma base de dados chamada `default` .

Uma vez que as bases de dados são sincronizadas para a piscina SQL sem servidor assíncronea, haverá um atraso até que apareçam.

## <a name="manage-a-spark-created-database"></a>Gerir uma base de dados criada por Spark

Use o Spark para gerir as bases de dados criadas pela Spark. Por exemplo, elimine-o através de uma experiência de piscina spark e crie mesas nele a partir de Spark.

Se criar objetos numa base de dados criada pelo Spark utilizando a piscina SQL sem servidor ou tentar largar a base de dados, a operação terá sucesso. Mas a base de dados original do Spark não será alterada.

## <a name="how-name-conflicts-are-handled"></a>Como os conflitos de nome são tratados

Se o nome de uma base de dados Spark entrar em conflito com o nome de uma base de dados de piscinas SQL sem servidor existente, um sufixo é anexado na piscina SQL sem servidor à base de dados Spark. O sufixo na piscina SQL sem servidor é `_<workspace name>-ondemand-DefaultSparkConnector` .

Por exemplo, se uma base de dados Spark chamada `mydb` for criada no espaço de trabalho Azure Synapse e uma base de `myws` dados de piscinas SQL sem servidor com esse nome já existir, então a base de dados Spark no pool SQL sem servidor terá de ser referenciada usando o nome `mydb_myws-ondemand-DefaultSparkConnector` .

> [!CAUTION]
> Atenção: Não deve depender deste comportamento.

## <a name="security-model"></a>Modelo de segurança

As bases de dados e tabelas Spark, juntamente com as suas representações sincronizadas no motor SQL, serão asseguradas ao nível de armazenamento subjacente.

O principal de segurança que cria uma base de dados é considerado o proprietário dessa base de dados, e tem todos os direitos sobre a base de dados e os seus objetos.

Para dar a um diretor de segurança, como um utilizador ou um grupo de segurança, o acesso a uma base de dados, forneça a pasta POSIX adequada e as permissões de ficheiros para as pastas e ficheiros subjacentes no `warehouse` diretório. 

Por exemplo, para que um diretor de segurança leia uma tabela numa base de dados, todas as pastas a partir da pasta de base de dados no `warehouse` diretório precisam de ter `X` e `R` permissões atribuídas a esse diretor de segurança. Além disso, ficheiros (como os ficheiros de dados subjacentes da tabela) requerem `R` permissões. 

Se um princípio de segurança necessitar da capacidade de criar objetos ou largar objetos numa base de `W` dados, são necessárias permissões adicionais nas pastas e ficheiros da `warehouse` pasta.

## <a name="examples"></a>Exemplos

### <a name="create-and-connect-to-spark-database-with-serverless-sql-pool"></a>Criar e ligar à base de dados Spark com piscina SQL sem servidor

Primeiro crie uma nova base de dados Spark chamada `mytestdb` usando um cluster Spark que já criou no seu espaço de trabalho. Pode conseguir isso, por exemplo, utilizando um Caderno Spark C# com o seguinte .NET para declaração spark:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Após um curto atraso, pode ver a base de dados a partir da piscina SQL sem servidor. Por exemplo, executar a seguinte declaração a partir da piscina SQL sem servidor.

```sql
SELECT * FROM sys.databases;
```

Verifique se `mytestdb` está incluído nos resultados.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre os metadados partilhados da Azure Synapse Analytics](overview.md)
- [Saiba mais sobre as tabelas de metadados partilhados da Azure Synapse Analytics](table.md)
