---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 1ab404b838af65dcb75395dfeee1ca0553e497a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67184929"
---
## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Especificação da definição de estrutura para conjuntos de dados retangulares
A secção de estrutura nos conjuntos de dados JSON é uma secção **opcional** para tabelas retangulares (com linhas & colunas) e contém uma coleção de colunas para a tabela. Utilizará a secção da estrutura para fornecer informações do tipo para conversões de tipos ou para fazer mapeamentos de colunas. As seguintes secções descrevem estas características em detalhe. 

Cada coluna contém as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| nome |Nome da coluna. |Sim |
| tipo |Tipo de dados da coluna. Consulte a secção de conversões de tipo abaixo para obter mais detalhes sobre quando deve especificar as informações do tipo |Não |
| cultura |Cultura baseada em .NET a ser utilizada quando o tipo é especificado e é .NET tipo Datatime ou Datetimeoffset. Padrão é "en-us". |Não |
| formato |Cadeia de formato a utilizar quando o tipo é especificado e é .NET tipo Datatime ou Datetimeoffset. |Não |

A amostra que se segue mostra a secção de estrutura JSON para uma tabela que tem três colunas usadas, nome e última data.

```json
"structure": 
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```

Por favor, utilize as seguintes diretrizes para quando incluir informações de "estrutura" e o que incluir na secção **de estrutura.**

* **Para fontes de dados estruturadas** que armazenam informações de dados e suem informações juntamente com os próprios dados (fontes como SQL Server, Oracle, tabela Azure, etc.), deve especificar a secção "estrutura" apenas se pretender fazer o mapeamento de colunas específicas de colunas específicas para colunas específicas na pia e os seus nomes não são os mesmos (ver detalhes na secção de mapeamento de colunas abaixo). 
  
    Como mencionado acima, a informação do tipo é opcional na secção "estrutura". Para fontes estruturadas, a informação do tipo já está disponível como parte da definição de dataset no datastore, pelo que não deve incluir informações de tipo quando inclui a secção "estrutura".
* **Para esquemas sobre fontes de dados de leitura (especificamente blob Azure)** pode optar por armazenar dados sem armazenar qualquer esquema ou digitar informações com os dados. Para este tipo de fontes de dados deve incluir "estrutura" nos seguintes 2 casos:
  * Queres fazer mapeamento de colunas.
  * Quando o conjunto de dados é uma fonte numa atividade de Cópia, pode fornecer informações de tipo em "estrutura" e a fábrica de dados usará este tipo de informação para conversão para tipos nativos para o lavatório. Consulte o artigo do [Move de e para o Azure Blob](../articles/data-factory/v1/data-factory-azure-blob-connector.md) para mais informações.

### <a name="supported-net-based-types"></a>Apoiado. Tipos baseados em NET
A fábrica de dados suporta os seguintes valores de tipo cls compatíveis com CLS para fornecer informações de tipo em "estrutura" para schema sobre fontes de dados lidas como o Blob Azure.

* Int16
* Int32 
* Int64
* Único
* Double
* Decimal
* Byte[]
* Booleano
* Cadeia 
* GUID
* Datetime
* Datatimeoffset
* Timespan 

Para datas & Datatimeoffset também pode especificar opcionalmente "cultura" & "formato" para facilitar a análise da sua cadeia de datas personalizada. Consulte a amostra para a conversão do tipo abaixo.

