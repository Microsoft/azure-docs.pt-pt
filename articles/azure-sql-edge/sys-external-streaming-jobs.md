---
title: sys.external_streaming_jobs (Transact-SQL) - Ponta SQL Azure (Pré-visualização)
description: Saiba mais sobre o uso de sys.external_streaming_jobs em Azure SQL Edge (Pré-visualização)
keywords: sys.external_streaming_jobs, SQL Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 7d4f66375c5490906e773c6f105a029c3a88465c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597276"
---
# <a name="sysexternal_streaming_jobs-transact-sql"></a>sys.external_streaming_jobs (Transact-SQL)

Devolve uma linha para cada trabalho de streaming externo criado no âmbito da base de dados.

|Nome da coluna|Tipo de dados|Descrição|  
|-----------------|---------------|-----------------|
|**nome**|**sysname**|Nome do riacho. É único dentro da base de dados.|
|**object_id**|**int**|número de identificação do objeto para o objeto de fluxo. É único dentro da base de dados.|
|**principal_id**|**int**|ID do diretor que detém esta assembleia|
|**schema_id**|**int**| Identificação do esquema que contém o objeto.|
|**parent_object_id**|**id**| número de identificação do objeto para o objeto-mãe para este fluxo. Na implementação atual, este valor é sempre nulo|
|**tipo**|**char(2)**|Tipo de objeto. Para objetos de fluxo, o tipo é sempre 'EJ'|
|**type_desc**|**nvarchar(60)**| Descrição do tipo de objeto. Para objetos de fluxo, o tipo é sempre 'EXTERNAL_STREAMING_JOB'|
|**create_date**|**datetime**| Data em que o objeto foi criado.|
|**modify_date**|**datetime**| Na implementação atual, este valor é o mesmo que o create_date para o objeto de fluxo |
|**is_ms_shipped**|**bit**| Objeto criado por um componente interno.|  
|**is_published**|**bit**| O objeto é publicado.|  
|**is_schema_published**|**bit**|Apenas o esquema do objeto é publicado.|
|**uses_ansi_nulls**|**bit**| O objeto de fluxo foi criado com a opção set ANSI_NULLS base de dados ON|
|**declaração**|**varchar(max)**| O texto de consulta de análise de fluxo para o trabalho de streaming. Para mais informações, consulte [sp_create_streaming_job](overview.md) |
|**estado**|**int**| O estado atual do trabalho de streaming. Os valores possíveis são <br /><br /> **Criado** = 0. O trabalho de streaming foi criado, mas ainda não foi iniciado. <br /><br /> **Início** = 1. O trabalho de streaming está na fase inicial. <br /><br /> **Falhado** = 6. O trabalho de streaming falhou. Isto é geralmente uma indicação de um erro fatal durante o processamento. <br /><br /> **Parado** = 4. O trabalho de streaming foi interrompido. <br /><br /> **Idle** = 7. O trabalho de streaming está a decorrer, no entanto não há entrada para processar. <br /><br /> **Processamento** = 8. O trabalho de streaming está a decorrer e está a processar inputs. Este estado indica um estado saudável para o trabalho de streaming. <br /><br /> **Degradado** = 9. O trabalho de streaming está em execução, no entanto houve alguns erros não fatais de serialização/desserialização durante o processamento de entradas. O trabalho de entrada continuará a funcionar, mas vai deixar cair as inputs que encontram erros.|

## <a name="permissions"></a>Permissões

A visibilidade dos metadados nas vistas do catálogo limita-se a securáveis que um utilizador detém ou nos quais o utilizador obteve alguma permissão. Para mais informações, consulte Configuração de [Visibilidade de Metadados](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>Ver também

- [Vistas do catálogo de streaming T-SQL](overview.md)
- [Vistas de catálogo (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Pontos de vista do sistema (Transact-SQL)](/sql/t-sql/language-reference/)

