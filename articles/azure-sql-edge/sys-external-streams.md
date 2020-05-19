---
title: sys.external_streams (Transact-SQL) - Ponta SQL Azure (Pré-visualização)
description: Saiba mais sobre o uso de sys.external_streams em Azure SQL Edge (Pré-visualização)
keywords: sys.external_streams, SQL Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: d47139b0b2a20ecfcf92bfc55a8b47c8e3cf7dab
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597283"
---
# <a name="sysexternal_streams-transact-sql"></a>sys.external_streams (Transact-SQL)

Devolve uma linha para cada objeto de fluxo externo criado no âmbito da base de dados.

|Nome da coluna|Tipo de dados|Descrição|  
|-----------------|---------------|-----------------|
|**nome**|**sysname**|Nome do riacho. É único dentro da base de dados.|
|**object_id**|**int**|número de identificação do objeto para o objeto de fluxo. É único dentro da base de dados.|
|**principal_id**|**int**|ID do diretor que detém esta assembleia|
|**schema_id**|**int**| Identificação do esquema que contém o objeto.|
|**parent_object_id**|**id**| número de identificação do objeto para o objeto-mãe para este fluxo. Na implementação atual, este valor é sempre nulo|
|**tipo**|**char(2)**|Tipo de objeto. Para objetos de fluxo, o tipo é sempre 'ES'|
|**type_desc**|**nvarchar(60)**| Descrição do tipo de objeto. Para objetos de fluxo, o tipo é sempre 'EXTERNAL_STREAM'|
|**create_date**|**datetime**| Data em que o objeto foi criado.|
|**modify_date**|**datetime**| Data o objeto foi modificado pela última vez através de uma declaração ALTER.|
|**is_ms_shipped**|**bit**| Objeto criado por um componente interno.|  
|**is_published**|**bit**|O objeto é publicado.|  
|**is_schema_published**|**bit**|Apenas o esquema do objeto é publicado.|
|**max_column_id_used**|**bit**| Esta coluna é usada para fins internos e será removida no futuro|  
|**uses_ansi_nulls**|**bit**| O objeto de fluxo foi criado com a opção set ANSI_NULLS base de dados ON|
|**data_source_id**|**int**| O ID do objeto para a fonte de dados externa representada pelo objeto de fluxo |  
|**file_format_id**|**int**| O ID do objeto para o formato de ficheiro externo utilizado pelo objeto de fluxo. O formato de ficheiro externo é necessário para especificar o layout real dos dados referenciados por um fluxo externo| 
|**localização**|**varchar(max)**| O alvo do objeto de fluxo externo. Para mais informações, consulte [Criar Fluxo Externo](overview.md) |
|**input_option**|**varchar(max)**| As opções de entrada utilizadas durante a criação do fluxo externo. Para mais informações, consulte [Criar Fluxo Externo](overview.md) |
|**output_option**|**varchar(max)**| As opções de saída utilizadas durante a criação do fluxo externo. Para mais informações, consulte [Criar Fluxo Externo](overview.md) | 

## <a name="permissions"></a>Permissões

A visibilidade dos metadados nas vistas do catálogo limita-se a securáveis que um utilizador detém ou nos quais o utilizador obteve alguma permissão. Para mais informações, consulte Configuração de [Visibilidade de Metadados](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>Ver também

- [Vistas de catálogo (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Pontos de vista do sistema (Transact-SQL)](/sql/t-sql/language-reference/)
