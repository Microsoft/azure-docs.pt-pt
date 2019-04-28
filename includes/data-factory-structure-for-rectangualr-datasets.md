---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 1ab404b838af65dcb75395dfeee1ca0553e497a1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309202"
---
## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Especificar a definição da estrutura para conjuntos de dados retangulares
A seção de estrutura em conjuntos de dados JSON é um **opcional** secção para tabelas retangulares (com linhas e colunas) e contém uma coleção de colunas da tabela. Irá utilizar a secção de estrutura para qualquer um com informações de tipo para conversões de tipo ou fazer o mapeamento de colunas. As secções seguintes descrevem estas funcionalidades em detalhes. 

Cada coluna contém as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| nome |Nome da coluna. |Sim |
| tipo |Tipo de dados da coluna. Consulte a secção de conversões de tipo abaixo para obter mais detalhes sobre quando deve especificar as informações de tipo |Não |
| cultura |.NET com a base de cultura a ser utilizado quando o tipo especificado e é o tipo de .NET Datetime ou Datetimeoffset. A predefinição é "en-us". |Não |
| Formato |Formato de cadeia de caracteres a ser utilizado quando o tipo especificado e é o tipo de .NET Datetime ou Datetimeoffset. |Não |

O exemplo a seguir mostra a secção de estrutura JSON para uma tabela com três colunas ID de utilizador, o nome e lastlogindate.

```json
"structure": 
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```

Utilize as seguintes diretrizes para quando incluir informações de "estrutura" e o que incluir na **estrutura** secção.

* **Para origens de dados estruturados** que dados esquema e o tipo de informações da loja, juntamente com os dados propriamente ditos (origens, como tabelas do Azure do SQL Server, Oracle, etc.), deverá especificar a seção de "estrutura" apenas se desejar façam o mapeamento de colunas de origem específico colunas a colunas específicas nos seus nomes e de sink não são os mesmos (ver detalhes na seção de mapeamento de coluna abaixo). 
  
    Conforme mencionado acima, as informações de tipo são opcionais na seção de "estrutura". Para origens de estruturados, informações de tipo já estão disponíveis como parte da definição de conjunto de dados no arquivo de dados, por isso, não deve incluir informações sobre o tipo ao incluir a seção de "estrutura".
* **Para o esquema em origens de dados de leitura (especificamente BLOBs do Azure)** pode optar por armazenar os dados sem armazenar as informações de esquema ou tipo com os dados. Para estes tipos de origens de dados deve incluir "estrutura" nos seguintes 2 casos:
  * Deseja fazer o mapeamento de colunas.
  * Quando o conjunto de dados é uma origem numa atividade de cópia, pode fornecer informações de tipo "estrutura" e o data factory irá utilizar estas informações de tipo para a conversão para tipos nativos para o sink. Ver [mover dados de Blobs do Azure e para](../articles/data-factory/v1/data-factory-azure-blob-connector.md) artigo para obter mais informações.

### <a name="supported-net-based-types"></a>Suportado. Tipos baseados em NET
Fábrica de dados suporta os seguintes CLS compatível com .NET com base em tipo valores por fornecer informações de tipo "estrutura" para o esquema nas origens de dados de leitura, como BLOBs do Azure.

* Int16
* Int32 
* Int64
* Single
* Double
* Decimal
* Byte[]
* Booleano
* String 
* Guid
* Datetime
* Datetimeoffset
* Timespan 

Para Datetime & Datetimeoffset, opcionalmente, também é possível especificar "Cultura" & "format" cadeia de caracteres para facilitar a análise da sua cadeia de Datetime personalizada. Veja o exemplo para a conversão de tipo abaixo.

