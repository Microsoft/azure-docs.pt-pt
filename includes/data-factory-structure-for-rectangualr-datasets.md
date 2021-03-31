---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 1ab404b838af65dcb75395dfeee1ca0553e497a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "67184929"
---
## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Especificar a definição de estrutura para conjuntos de dados retangulares
A secção de estrutura nos conjuntos de dados JSON é uma secção **opcional** para tabelas retangulares (com linhas & colunas) e contém uma coleção de colunas para a tabela. Utilizará a secção de estrutura para fornecer informações de tipo para conversões de tipo ou para fazer mapeamentos de colunas. As seguintes secções descrevem estas características em detalhe. 

Cada coluna contém as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| name |O nome da coluna. |Yes |
| tipo |Tipo de dados da coluna. Consulte a secção de conversões de tipo abaixo para obter mais detalhes sobre quando deve especificar as informações do tipo |No |
| cultura |.CULTURA baseada em NET a ser utilizada quando o tipo é especificado e é .NET tipo Datatime ou Datetimeoffset. O padrão é "en-us". |No |
| formato |Cadeia de formato a utilizar quando o tipo é especificado e é .NET tipo Datatime ou Datetimeoffset. |No |

A amostra a seguir mostra a secção de estrutura JSON para uma tabela que tem três colunas userid, nome e lastlogindate.

```json
"structure": 
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```

Por favor, utilize as seguintes diretrizes para quando incluir informações de "estrutura" e o que incluir na secção de **estrutura.**

* **Para fontes de dados estruturadas** que armazenam esquemas de dados e informações de tipo, juntamente com os próprios dados (fontes como SQL Server, Oracle, Azure table, etc.), deve especificar a secção "estrutura" apenas se pretender fazer o mapeamento de colunas de colunas específicas para colunas específicas na pia e os seus nomes não são os mesmos (ver detalhes na secção de mapeamento de colunas abaixo). 
  
    Como mencionado acima, a informação do tipo é opcional na secção "estrutura". Para fontes estruturadas, a informação do tipo já está disponível como parte da definição de conjunto de dados na loja de dados, pelo que não deve incluir informações de tipo quando incluir a secção "estrutura".
* **Para esquemas em fontes de dados de leitura (especificamente Azure blob)**  pode optar por armazenar dados sem armazenar qualquer esquema ou tipo de informação com os dados. Para este tipo de fontes de dados deve incluir "estrutura" nos seguintes 2 casos:
  * Quer fazer mapeamento de colunas.
  * Quando o conjunto de dados é uma fonte numa atividade de Cópia, pode fornecer informações de tipo em "estrutura" e a fábrica de dados utilizará este tipo de informação para conversão em tipos nativos para o lavatório. Consulte os dados de e para o artigo [Azure Blob](../articles/data-factory/v1/data-factory-azure-blob-connector.md) para obter mais informações.

### <a name="supported-net-based-types"></a>Suportado. Tipos baseados em NET
A fábrica de dados suporta os seguintes valores de tipo cls .NET compatíveis com base para fornecer informações de tipo em "estrutura" para esquemas em fontes de dados de leitura como a bolha Azure.

* Int16
* Int32 
* Int64
* Único
* Double (Duplo)
* Decimal
* Byte[]
* Booleano
* String 
* GUID
* Datetime
* Datatimeoff
* Timespan 

Para datatime & datatimeoff também pode especificar opcionalmente "cultura" & "formato" para facilitar a análise da sua cadeia de datagem personalizada. Consulte a amostra para a conversão do tipo abaixo.

