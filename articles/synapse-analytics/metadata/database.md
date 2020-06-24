---
title: Base de dados partilhada Azure Synapse Analytics
description: O Azure Synapse Analytics fornece um modelo de metadados partilhado onde a criação de uma base de dados em Apache Spark o tornará acessível a partir dos seus motores de piscina SQL on-demand (pré-visualização) e SQL.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: 68919457cc045f50ebef249ced9c117fea8d6984
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85079031"
---
# <a name="azure-synapse-analytics-shared-database"></a>Base de dados partilhada Azure Synapse Analytics

O Azure Synapse Analytics permite que os diferentes motores de espaço de trabalho computacionais partilhem bases de dados e tabelas entre as suas piscinas Spark (pré-visualização) e o motor a pedido (pré-visualização) sql.

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Uma base de dados criada com uma obra Spark tornar-se-á visível com o mesmo nome para todas as piscinas spark atuais e futuras (pré-visualização) no espaço de trabalho, bem como o motor a pedido do SQL.

A base de dados padrão Spark, chamada `default` , também será visível no contexto de procura do SQL como uma base de dados chamada `default` .

Uma vez que as bases de dados são sincronizadas para a SQL on-demand asynchronously, haverá um atraso até que apareçam.

## <a name="manage-a-spark-created-database"></a>Gerir uma base de dados criada por Spark

Use o Spark para gerir as bases de dados criadas pela Spark. Por exemplo, elimine-o através de uma experiência de piscina spark e crie mesas nele a partir de Spark.

Se criar objetos numa base de dados criada pela Spark usando o SQL a pedido ou tentar largar a base de dados, a operação terá sucesso. Mas a base de dados original do Spark não será alterada.

## <a name="handling-of-name-conflicts"></a>Tratamento de conflitos de nomes

Se o nome de uma base de dados Spark entrar em conflito com o nome de uma base de dados a pedido do SQL existente, um sufixo é anexado em SQL a pedido à base de dados Spark. O sufixo em SQL a pedido é `_<workspace name>-ondemand-DefaultSparkConnector` .

Por exemplo, se uma base de dados Spark chamada `mydb` for criada no espaço de trabalho Azure Synapse e uma base de `myws` dados SQL a pedido com esse nome já existir, então a base de dados Spark em SQL a pedido terá de ser referenciada usando o nome `mydb_myws-ondemand-DefaultSparkConnector` .

> [!CAUTION]
> Atenção: Não deve depender deste comportamento.

## <a name="security-model"></a>Modelo de segurança

As bases de dados e tabelas Spark, juntamente com as suas representações sincronizadas no motor SQL, serão asseguradas ao nível de armazenamento subjacente.

O principal de segurança que cria uma base de dados é considerado o proprietário dessa base de dados, e tem todos os direitos sobre a base de dados e seus objetos.

Para dar a um diretor de segurança, como um utilizador ou um grupo de segurança, o acesso a uma base de dados, forneça a pasta POSIX adequada e as permissões de ficheiros para as pastas e ficheiros subjacentes no `warehouse` diretório. 

Por exemplo, para que um diretor de segurança leia uma tabela numa base de dados, todas as pastas a partir da pasta de base de dados no `warehouse` diretório precisam de ter `X` e `R` permissões atribuídas a esse diretor de segurança. Além disso, ficheiros (como os ficheiros de dados subjacentes da tabela) requerem `R` permissões. 

Se um princípio de segurança necessitar da capacidade de criar objetos ou largar objetos numa base de `W` dados, são necessárias permissões adicionais nas pastas e ficheiros da `warehouse` pasta.

## <a name="examples"></a>Exemplos

### <a name="create--connect-to-spark-database---sql-on-demand"></a>Criar & ligar à base de dados Spark - SQL on-demand

Primeiro crie uma nova base de dados Spark chamada `mytestdb` usando um cluster Spark que já criou no seu espaço de trabalho. Pode conseguir isso, por exemplo, utilizando um Caderno Spark C# com o seguinte .NET para declaração spark:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Após um curto atraso, pode ver a base de dados da SQL on demand. Por exemplo, executar a seguinte declaração da SQL a pedido.

```sql
SELECT * FROM sys.databases;
```

Verifique se `mytestdb` está incluído nos resultados.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre os metadados partilhados da Azure Synapse Analytics](overview.md)
- [Saiba mais sobre as tabelas de metadados partilhados da Azure Synapse Analytics](table.md)
