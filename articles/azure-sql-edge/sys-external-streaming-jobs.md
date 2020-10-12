---
title: sys.external_streaming_jobs (Transact-SQL) - Azure SQL Edge
description: Saiba como utilizar sys.external_streaming_jobs em Azure SQL Edge
keywords: sys.external_streaming_jobs, SqL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 9643c58f5c9fa1db3e3eb7ec75ce6d3b41620aa3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90900345"
---
# <a name="sysexternal_streaming_jobs-transact-sql"></a>sys.external_streaming_jobs (Transact-SQL)

Devolve uma linha para cada trabalho de streaming externo criado no âmbito da base de dados.

|Nome da coluna|Tipo de dados|Descrição|  
|-----------------|---------------|-----------------|
|**nome**|**sysname**|O nome do riacho. É único dentro da base de dados.|
|**object_id**|**int**|número de identificação de objeto para o objeto de fluxo. É único dentro da base de dados.|
|**principal_id**|**int**|ID do principal que é dono desta assembleia|
|**schema_id**|**int**| Identificação do esquema que contém o objeto.|
|**parent_object_id**|**id**| número de identificação de objeto para o objeto-mãe para este fluxo. Na implementação atual, este valor é sempre nulo|
|**tipo**|**char(2)**|Tipo de objeto. Para objetos de fluxo, o tipo é sempre 'EJ'|
|**type_desc**|**nvarchar(60)**| Descrição do tipo de objeto. Para objetos de fluxo, o tipo é sempre 'EXTERNAL_STREAMING_JOB'|
|**create_date**|**datetime**| Data em que o objeto foi criado.|
|**modify_date**|**datetime**| Na implementação atual, este valor é o mesmo que o create_date para o objeto de fluxo |
|**is_ms_shipped**|**bit**| Objeto criado por um componente interno.|  
|**is_published**|**bit**| Objeto é publicado.|  
|**is_schema_published**|**bit**|Apenas o esquema do objeto é publicado.|
|**uses_ansi_nulls**|**bit**| O objeto de fluxo foi criado com a opção de base de dados SET ANSI_NULLS ON|
|**declaração**|**varchar(max)**| O texto de consulta de análise de streaming para o trabalho de streaming. Para mais informações, consulte [sp_create_streaming_job](overview.md) |
|**estado**|**int**| O estado atual do trabalho de streaming. Os valores possíveis são <br /><br /> **Criado** = 0. O trabalho de streaming foi criado, mas ainda não foi iniciado. <br /><br /> **Início** = 1. O trabalho de streaming está na fase inicial. <br /><br /> **Falhado** = 6. O trabalho de streaming falhou. Isto é geralmente uma indicação de um erro fatal durante o processamento. <br /><br /> **Parado** = 4. O trabalho de streaming foi interrompido. <br /><br /> **Idle** = 7. O trabalho de streaming está em execução, no entanto não há nenhuma entrada para processar. <br /><br /> **Processamento** = 8. O trabalho de streaming está a funcionar e está a processar entradas. Este estado indica um estado saudável para o trabalho de streaming. <br /><br /> **Degradado** = 9. O trabalho de streaming está em execução, no entanto houve alguns erros de entrada/produção/des-serialização não fatal durante o processamento de entradas. O trabalho de entrada continuará a funcionar, mas deixará cair as entradas que encontram erros.|

## <a name="permissions"></a>Permissões

A visibilidade dos metadados nas vistas do catálogo limita-se a securáveis que um utilizador possui ou sobre os quais o utilizador tenha alguma permissão. Para obter mais informações, consulte [a Configuração de Visibilidade dos Metadados](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>Consulte também

- [Vistas do catálogo de streaming T-SQL](overview.md)
- [Vistas do Catálogo (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Vistas do Sistema (Transact-SQL)](/sql/t-sql/language-reference/)

