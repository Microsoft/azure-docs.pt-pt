---
title: Propriedades de ingestão de dados para o Azure Data Explorer
description: Conheça as várias propriedades de ingestão de dados suportadas pelo Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 48bce1bf03a0a4c8d81fff7ae54e0b22261b70f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109579"
---
# <a name="azure-data-explorer-data-ingestion-properties"></a>Propriedades de ingestão de dados do Azure Data Explorer 

A ingestão de dados é o processo pelo qual os dados são adicionados a uma tabela e são disponibilizados para consulta no Azure Data Explorer. Adiciona propriedades ao comando de ingestão após a `with` palavra-chave.

## <a name="ingestion-properties"></a>Propriedades de ingestão

A tabela seguinte lista as propriedades suportadas pelo Azure Data Explorer, descreve-as e fornece exemplos: 

|Propriedade              |Descrição                                              |Exemplo                                             |
|----------------------|---------------------------------------------------------|----------------------------------------------------|
|`ingestionMapping`    |Um valor de cadeia que indica como mapear dados do ficheiro fonte para as colunas reais da tabela. Defina `format` o valor com o tipo de mapeamento relevante. Ver [mapeamento de dados](/azure/kusto/management/mappings).|`with (format="json", ingestionMapping = "[{\"column\":\"rownumber\", \"Properties\":{\"Path\":\"$.RowNumber\"}}, {\"column\":\"rowguid\", \"Properties\":{\"Path\":\"$.RowGuid\"}}]")`<br>(depreciado: `avroMapping`, `csvMapping`, `jsonMapping`) |
|`ingestionMappingReference`|Um valor de cadeia que indica como mapear dados do ficheiro fonte para as colunas reais da tabela usando um objeto de política de mapeamento nomeado. Defina `format` o valor com o tipo de mapeamento relevante. Ver [mapeamento de dados](/azure/kusto/management/mappings).|`with (format="csv", ingestionMappingReference = "Mapping1")`<br>(depreciado: `avroMappingReference`, `csvMappingReference`, `jsonMappingReference`)|
|`creationTime` |O valor da data (formatado como uma cadeia ISO8601) para utilizar no momento de criação das extensões de dados ingeridas. Se não especificado,`now()`o valor atual () será utilizado. A sobreposição do padrão é útil na ingestão de dados mais antigos, de modo a que a política de retenção seja aplicada corretamente.|`with (creationTime="2017-02-13T11:09:36.7992775Z")`|
|`extend_schema`|Um valor booleano que, se especificado, instrui o comando para estender `false`o esquema da tabela (incumprimentos para ). Esta opção aplica-se apenas a `.append` e `.set-or-append` comandos. As únicas extensões de esquema sitia permitidas têm colunas adicionais adicionadas à mesa no final.|Se o esquema de `(a:string, b:int)`mesa original for , uma `(a:string, b:int, c:datetime, d:string)`extensão de esquema válida seria , mas `(a:string, c:datetime)` não seria válido|
|`folder` |Para [comandos ingerir a partir da consulta,](/azure/kusto/management/data-ingestion/ingest-from-query) a pasta para atribuir à mesa. Se a tabela já existir, esta propriedade irá anular a pasta da tabela.|`with (folder="Tables/Temporary")`|
|`format` |O formato de dados (ver [formatos de dados suportados).](ingestion-supported-formats.md)|`with (format="csv")`|
|`ingestIfNotExists`|Um valor de cadeia que, se especificado, impede que a ingestão `ingest-by:` tenha sucesso se a tabela já tiver dados marcados com uma etiqueta com o mesmo valor. Isto garante a ingestão de dados impotentes. Para mais informações, [consulte: tags](/azure/kusto/management/extents-overview#ingest-by-extent-tags).|As `with (ingestIfNotExists='["Part0001"]', tags='["ingest-by:Part0001"]')` propriedades indicam que se `ingest-by:Part0001` os dados com a etiqueta já existirem, então não complete a ingestão atual. Se ainda não existir, esta nova ingestão deverá ter esta etiqueta definida (no caso de uma futura ingestão tentar ingerir novamente os mesmos dados.)|
|`ignoreFirstRecord` |Um valor booleano que, se definido, `true`indica que a ingestão deve ignorar o primeiro registo de cada ficheiro. Esta propriedade é útil `CSV`para ficheiros em formatos semelhantes, se o primeiro registo no ficheiro for o nome da coluna. Por defeito, `false` assume-se.|`with (ignoreFirstRecord=false)`|
|`persistDetails` |Um valor booleano que, se especificado, indica que o comando deve persistir os resultados detalhados (mesmo que bem sucedidos) para que o comando de detalhes de [operação .show](/azure/kusto/management/operations#show-operation-details) possa recuperá-los. Incumprimentos `false`para .|`with (persistDetails=true)`|
|`policy_ingestiontime`|Um valor booleano que, se especificado, descreve se permite a Política de Tempo de [Ingestion](/azure/kusto/management/ingestiontimepolicy) numa tabela criada por este comando. A predefinição é `true`.|`with (policy_ingestiontime=false)`|
|`recreate_schema` |Um valor booleano que, se especificado, descreve se o comando pode recriar o esquema da mesa. Esta propriedade aplica-se `.set-or-replace` apenas ao comando. Esta propriedade tem precedência sobre a `extend_schema` propriedade se ambos estiverem definidos.|`with (recreate_schema=true)`|
|`tags`|Uma lista de [tags](/azure/kusto/management/extents-overview#extent-tagging) para associar com os dados ingeridos, formatadocomo uma cadeia JSON |`with (tags="['Tag1', 'Tag2']")`|
|`validationPolicy`|Uma cadeia JSON que indica quais as validações a executar durante a ingestão. Consulte a [ingestão](/azure/kusto/management/data-ingestion/) de Dados para obter uma explicação das diferentes opções.| `with (validationPolicy='{"ValidationOptions":1, "ValidationImplications":1}')`(esta é, na verdade, a política de incumprimento)|
|`zipPattern`|Utilize esta propriedade ao ingerir dados a partir do armazenamento que tenha um arquivo ZIP. Trata-se de um valor de cadeia que indica a expressão regular a utilizar ao selecionar quais os ficheiros do arquivo ZIP para ingerir.  Todos os outros ficheiros no arquivo serão ignorados.|`with (zipPattern="*.csv")`|

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a ingestão de dados](/azure/data-explorer/ingest-data-overview)
* Saiba mais sobre [formatos de dados suportados](ingestion-supported-formats.md)
