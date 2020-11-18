---
title: Piscina SQL sem servidor (pré-visualização) autoajuda
description: Esta secção contém informações que podem ajudá-lo a resolver problemas com piscina SQL sem servidor (pré-visualização).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: aefdb90c3e6c8d3abc3924ecebb2aa46739e99ad
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682626"
---
# <a name="self-help-for-serverless-sql-pool-preview"></a>Autoajuda para piscina SQL sem servidor (pré-visualização)

Este artigo contém informações sobre como resolver problemas mais frequentes com piscina SQL sem servidor (pré-visualização) em Azure Synapse Analytics.

## <a name="serverless-sql-pool-is-grayed-out-in-synapse-studio"></a>Piscina SQL sem servidor é acinzentada no Estúdio Synapse

Se o Synapse Studio não conseguir estabelecer ligação à piscina SQL sem servidor, irá notar que a piscina SQL sem servidor está acinzentada ou mostra o estado "Offline". Normalmente, este problema ocorre quando um dos seguintes casos acontece:

1) A sua rede impede a comunicação ao backend Azure Synapse. O caso mais frequente é que o porto 1443 está bloqueado. Para que a piscina SQL sem servidor funcione, desbloqueie esta porta. Outros problemas poderiam impedir que a piscina SQL sem servidor funcionasse também, [visite o guia completo de resolução de problemas para obter mais informações.](../troubleshoot/troubleshoot-synapse-studio.md)
2) Você não tem permissões para iniciar sessão na piscina SQL sem servidor. Para ter acesso, um dos administradores do espaço de trabalho Azure Synapse deve adicioná-lo ao administrador do espaço de trabalho ou à função de administrador do SQL. [Visite o guia completo sobre o controlo de acessos para mais informações.](access-control.md)

## <a name="query-fails-because-file-cannot-be-opened"></a>Consulta falha porque o ficheiro não pode ser aberto

Se a sua consulta falhar com o erro dizendo "O Ficheiro não pode ser aberto porque não existe ou é usado por outro processo" e tem a certeza de que ambos os ficheiros existem e não são utilizados por outro processo, significa que o pool SQL sem servidor não pode aceder ao ficheiro. Este problema geralmente acontece porque a sua identidade do Azure Ative Directory não tem direitos de acesso ao ficheiro. Por predefinição, o pool SQL sem servidor está a tentar aceder ao ficheiro utilizando a sua identidade do Azure Ative Directory. Para resolver este problema, é necessário ter os direitos adequados de acesso ao ficheiro. A forma mais fácil é conceder a si próprio a função de“Contribuinte de Dados do Armazenamento de Blobs” na conta de armazenamento que está a tentar consultar. [Veja o guia completo no controlo de acesso do Azure Active Directory para obter mais informações](../../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). 

## <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>Consulta falha porque não pode ser executada devido a restrições de recursos atuais 

Se a sua consulta falhar com a mensagem de erro "Esta consulta não pode ser executada devido a restrições de recursos atuais", significa que a piscina SQL sem servidor não é capaz de executá-la neste momento devido a restrições de recursos: 

- Certifique-se de que os tipos de dados de tamanhos razoáveis são utilizados. Além disso, especifique o esquema para ficheiros Parquet para colunas de cordas, uma vez que serão VARCHAR(8000) por padrão. 

- Se a sua consulta tiver como alvo ficheiros CSV, considere [criar estatísticas](develop-tables-statistics.md#statistics-in-serverless-sql-pool-preview). 

- Visite as [melhores práticas de desempenho para a piscina SQL sem servidor](best-practices-sql-on-demand.md) para otimizar a consulta.  

## <a name="create-statement-is-not-supported-in-master-database"></a>CREATE 'STATEMENT' não é suportado na base de dados principal

Se a sua consulta falhar com a mensagem de erro:

> Falhou em executar consulta. Erro: CRIAR QUADRO EXTERNO/FONTE DE DADOS/BASE DE DADOS SCOPED O FORMATO CREDENCIAL/FICHEIRO SCOPED não é suportado na base de dados principal.» 

significa que a base de dados principal no pool SQL sem servidor não suporta a criação de:
  - Tabelas externas
  - Fontes de dados externas
  - Credenciais de âmbito de dados
  - Formatos de ficheiros externos

Solução:

  1. Criar uma base de dados de utilizadores:

```sql
CREATE DATABASE <DATABASE_NAME>
```

  2. Executar criar declaração no contexto de <DATABASE_NAME>, que falhou anteriormente para a base de dados principal. 
  
  Exemplo para a criação de formato de ficheiro externo:
    
```sql
USE <DATABASE_NAME>
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] 
WITH ( FORMAT_TYPE = PARQUET)
```

## <a name="next-steps"></a>Próximos passos

Reveja os seguintes artigos para saber mais sobre como usar a piscina SQL sem servidor:

- [Consulta único ficheiro CSV](query-single-csv-file.md)

- [Pastas de consulta e vários ficheiros CSV](query-folders-multiple-csv-files.md)

- [Consulta de ficheiros específicos](query-specific-files.md)

- [Consultar ficheiros de Parquet](query-parquet-files.md)

- [Consultar tipos aninhados de Parquet](query-parquet-nested-types.md)

- [Consultar ficheiros JSON](query-json-files.md)

- [Criar e utilizar vistas](create-use-views.md)

- [Criar e utilizar tabelas externas](create-use-external-tables.md)

- [Armazenar resultados de consulta para armazenamento](create-external-table-as-select.md)
