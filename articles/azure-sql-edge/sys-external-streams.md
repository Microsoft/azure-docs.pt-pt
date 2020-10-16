---
title: sys.external_streams (Transact-SQL) - Azure SQL Edge
description: Saiba como utilizar sys.external_streams em Azure SQL Edge
keywords: sys.external_streams, SqL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 04950f01c06bc3c8ed3bb11a790310c2319a0579
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90900313"
---
# <a name="sysexternal_streams-transact-sql"></a>sys.external_streams (Transact-SQL)

Devolve uma linha para cada objeto de fluxo externo criado no âmbito da base de dados.

|Nome da coluna|Tipo de dados|Descrição|  
|-----------------|---------------|-----------------|
|**nome**|**sysname**|O nome do riacho. É único dentro da base de dados.|
|**object_id**|**int**|número de identificação de objeto para o objeto de fluxo. É único dentro da base de dados.|
|**principal_id**|**int**|ID do principal que é dono desta assembleia|
|**schema_id**|**int**| Identificação do esquema que contém o objeto.|
|**parent_object_id**|**id**| número de identificação de objeto para o objeto-mãe para este fluxo. Na implementação atual, este valor é sempre nulo|
|**tipo**|**char(2)**|Tipo de objeto. Para objetos de fluxo, o tipo é sempre 'ES'|
|**type_desc**|**nvarchar(60)**| Descrição do tipo de objeto. Para objetos de fluxo, o tipo é sempre 'EXTERNAL_STREAM'|
|**create_date**|**datetime**| Data em que o objeto foi criado.|
|**modify_date**|**datetime**| Data em que o objeto foi modificado pela última vez utilizando uma declaração ALTER.|
|**is_ms_shipped**|**bit**| Objeto criado por um componente interno.|  
|**is_published**|**bit**|Objeto é publicado.|  
|**is_schema_published**|**bit**|Apenas o esquema do objeto é publicado.|
|**max_column_id_used**|**bit**| Esta coluna é usada para fins internos e será removida no futuro|  
|**uses_ansi_nulls**|**bit**| O objeto de fluxo foi criado com a opção de base de dados SET ANSI_NULLS ON|
|**data_source_id**|**int**| O ID do objeto para a fonte de dados externos representado pelo objeto de fluxo |  
|**file_format_id**|**int**| O ID do objeto para o formato de ficheiro externo utilizado pelo objeto de fluxo. O formato de ficheiro externo é necessário para especificar o layout real dos dados referenciados por um fluxo externo| 
|**localização**|**varchar(max)**| O alvo do objeto de fluxo externo. Para mais informações, consulte [Criar Fluxo Externo](overview.md) |
|**input_option**|**varchar(max)**| As opções de entrada utilizadas durante a criação do fluxo externo. Para mais informações, consulte [Criar Fluxo Externo](overview.md) |
|**output_option**|**varchar(max)**| As opções de saída utilizadas durante a criação do fluxo externo. Para mais informações, consulte [Criar Fluxo Externo](overview.md) | 

## <a name="permissions"></a>Permissões

A visibilidade dos metadados nas vistas do catálogo limita-se a securáveis que um utilizador possui ou sobre os quais o utilizador tenha alguma permissão. Para obter mais informações, consulte [a Configuração de Visibilidade dos Metadados](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>Consulte também

- [Vistas do Catálogo (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Vistas do Sistema (Transact-SQL)](/sql/t-sql/language-reference/)
