---
title: Base de dados partilhada Azure Synapse Analytics
description: A Azure Synapse Analytics fornece um modelo de metadados partilhadoonde a criação de uma base de dados em Apache Spark irá torná-lo acessível a partir dos seus motores de piscina SQL on-demand (pré-visualização) e SQL.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: da1bd9c812c20f60264d1a5ee1f8821128900618
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83698846"
---
# <a name="azure-synapse-analytics-shared-database"></a>Base de dados partilhada Azure Synapse Analytics

O Azure Synapse Analytics permite que os diferentes motores computacionais do espaço de trabalho partilhem bases de dados e tabelas entre as suas piscinas Spark (pré-visualização) e o motor on-demand (pré-visualização) da SQL.

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Uma base de dados criada com um trabalho spark tornar-se-á visível com esse mesmo nome para todas as piscinas spark atuais e futuras (pré-visualização) no espaço de trabalho, bem como o motor on-demand SQL.

A base de dados padrão Spark, `default` chamada, também será visível no contexto on-demand da SQL como uma base de dados chamada `default` .

Uma vez que as bases de dados são sincronizadas para sql on-demand assíncrona, haverá um atraso até que apareçam.

## <a name="manage-a-spark-created-database"></a>Gerir uma base de dados criada pela Spark

Use a Spark para gerir as bases de dados criadas pela Spark. Por exemplo, apague-o através de um trabalho de piscina spark, e crie mesas nele a partir de Spark.

Se criar objetos numa base de dados criada pela Spark utilizando a Pedido SQL ou tentar deixar cair a base de dados, a operação terá sucesso. Mas a base de dados original do Spark não será alterada.

## <a name="handling-of-name-conflicts"></a>Tratamento de conflitos de nomes

Se o nome de uma base de dados Spark entrar em conflito com o nome de uma base de dados sql existente a pedido, um sufixo é anexado na SQL a pedido da base de dados Spark. O sufixo no SQL a pedido é `_<workspace name>-ondemand-DefaultSparkConnector` .

Por exemplo, se uma base de dados Spark chamada for criada no espaço de `mydb` trabalho Azure Synapse `myws` e numa base de dados sql on-demand com esse nome já existente, então a base de dados Spark em SQL on-demand terá de ser referenciada usando o nome `mydb_myws-ondemand-DefaultSparkConnector` .

> [!CAUTION]
> Atenção: Não deve assumir uma dependência deste comportamento.

## <a name="security-model"></a>Modelo de segurança

As bases de dados e tabelas Spark, juntamente com as suas representações sincronizadas no motor SQL serão fixadas ao nível de armazenamento subjacente.

O responsável pela segurança que cria uma base de dados é considerado o proprietário dessa base de dados, e tem todos os direitos sobre a base de dados e os seus objetos.

Para dar a um diretor de segurança, como um utilizador ou um grupo de segurança, o acesso a uma base de dados, fornecer a pasta POSIX adequada e obter permissões de ficheiros para as pastas e ficheiros subjacentes no `warehouse` diretório. 

Por exemplo, para que um diretor de segurança leia uma tabela numa base de dados, todas as pastas que começam na pasta de base de dados do `warehouse` diretório precisam de ter `X` e `R` permissões atribuídas a esse diretor de segurança. Além disso, ficheiros (como os ficheiros de dados subjacentes da tabela) requerem `R` permissões. 

Se um diretor de segurança necessitar da capacidade de criar objetos ou de largar objetos numa base de dados, `W` são necessárias permissões adicionais nas pastas e ficheiros da `warehouse` pasta.

## <a name="examples"></a>Exemplos

### <a name="create--connect-to-spark-database---sql-on-demand"></a>Criar & ligação à base de dados Spark - SQL on-demand

Primeiro crie uma nova base de dados Spark com o nome `mytestdb` de um cluster Spark que já criou no seu espaço de trabalho. Pode conseguir isso, por exemplo, utilizando um Notebook Spark C# com a seguinte declaração .NET para Spark:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Após um curto atraso, pode ver a base de dados da SQL a pedido. Por exemplo, executar a seguinte declaração da SQL a pedido.

```sql
SELECT * FROM sys.databases;
```

Verifique se `mytestdb` está incluído nos resultados.

## <a name="next-steps"></a>Próximos passos

- [Saiba mais sobre os metadados partilhados da Azure Synapse Analytics](overview.md)
- [Saiba mais sobre as tabelas de metadados partilhados da Azure Synapse Analytics](table.md)
