---
title: SQL a pedido De pré-ajuda) autoajuda
description: Esta secção contém informações que podem ajudá-lo a resolver problemas com a SQL a pedido (pré-visualização).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 05/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 8b2a9b6c5324240d71a80cde904057757d6ef421
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658871"
---
# <a name="self-help-for-sql-on-demand-preview"></a>Autoajuda para sQL a pedido (pré-visualização)

Este artigo contém informações sobre como resolver problemas mais frequentes com a SQL on-demand (pré-visualização) no Azure Synapse Analytics.

## <a name="sql-on-demand-is-grayed-out-in-synapse-studio"></a>SQL on-demand é cinza para fora em Estúdio Synapse

Se o Estúdio Synapse não conseguir estabelecer a ligação à SQL a pedido, notará que a SQL on-demand está cinzenta ou mostra o estado "Offline". Normalmente, este problema ocorre quando um dos seguintes casos acontece:

1) A sua rede impede a comunicação com o backend Azure Synapse. O caso mais frequente é que o porto 1443 está bloqueado. Para obter o SQL a pedido para trabalhar desbloquear este porto. Outros problemas poderiam impedir que a SQL a pedido também funcionasse, visitar guia completo de resolução de [problemas para mais informações.](../troubleshoot/troubleshoot-synapse-studio.md)
2) Não tem permissão para entrar na SQL a pedido. Para ter acesso, um dos administradores do espaço de trabalho Azure Synapse deve adicioná-lo ao administrador do espaço de trabalho ou ao papel de administrador do SQL. Visite o guia completo sobre o controlo de [acessos para mais informações.](access-control.md)

## <a name="query-fails-because-file-cannot-be-opened"></a>A consulta falha porque o ficheiro não pode ser aberto

Se a sua consulta falhar com o erro dizendo "O ficheiro não pode ser aberto porque não existe ou é usado por outro processo" e tem a certeza de que ambos os ficheiros existem e não é utilizado por outro processo, significa que a SQL a pedido não pode aceder ao ficheiro. Este problema geralmente acontece porque a sua identidade de Diretório Ativo Azure não tem direitos de acesso ao ficheiro. Por padrão, a SQL on-demand está a tentar aceder ao ficheiro utilizando a sua identidade de Diretório Ativo Azure. Para resolver este problema, precisa de ter direitos adequados para aceder ao ficheiro. A forma mais fácil é conceder a si próprio a função de“Contribuinte de Dados do Armazenamento de Blobs” na conta de armazenamento que está a tentar consultar. [Veja o guia completo no controlo de acesso do Azure Active Directory para obter mais informações](../../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). 

## <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>A consulta falha porque não pode ser executada devido a restrições de recursos atuais 

Se a sua consulta falhar com a mensagem de erro "Esta consulta não pode ser executada devido a restrições de recursos atuais", significa que a SQL a pedido não é capaz de executá-la neste momento devido a restrições de recursos: 

- Confirme que são utilizados tipos de dados de tamanhos razoáveis. Além disso, especifique o esquema para ficheiros Parquet para colunas de cadeias, uma vez que, por predefinição, vão ser VARCHAR(8000). 

- Se a sua consulta visa ficheiros CSV, considere [criar estatísticas](develop-tables-statistics.md#statistics-in-sql-on-demand-preview). 

- Visite [as melhores práticas de desempenho para a SQL a pedido](best-practices-sql-on-demand.md) para otimizar a consulta.  

## <a name="create-statement-is-not-supported-in-master-database"></a>CREATE 'STATEMENT' não é suportado na base de dados principal

Se a sua consulta falhar com a mensagem de erro:

> Falhou em executar a consulta. Erro: Criar QUADRO EXTERNO/FONTE DE DADOS/BASE DE DADOS O FORMATO CREDENTIAL/ARQUIVO não é suportado na base de dados principal.». 

significa que a base de dados principal da SQL a pedido não suporta a criação de:
  - Tabelas externas
  - Fontes de dados externas
  - Credenciais de base de dados
  - Formatos externos de ficheiros

Solução:

  1. Criar uma base de dados de utilizadores:

```sql
CREATE DATABASE <DATABASE_NAME>
```

  2. Execute a criação de uma declaração no contexto de <DATABASE_NAME> que falharam anteriormente para a base de dados principal. 
  
  Exemplo para a criação do formato de ficheiro externo:
    
```sql
USE <DATABASE_NAME>
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] 
WITH ( FORMAT_TYPE = PARQUET)
```

## <a name="next-steps"></a>Próximos passos

Reveja os seguintes artigos para saber mais sobre como utilizar a SQL a pedido:

- [Consulta single CSV arquivo](query-single-csv-file.md)

- [Questiões e vários ficheiros CSV](query-folders-multiple-csv-files.md)

- [Consulta de ficheiros específicos](query-specific-files.md)

- [Consultar ficheiros de Parquet](query-parquet-files.md)

- [Consultar tipos aninhados de Parquet](query-parquet-nested-types.md)

- [Consultar ficheiros JSON](query-json-files.md)

- [Criar e usar vistas](create-use-views.md)

- [Criar e usar tabelas externas](create-use-external-tables.md)

- [Armazenar resultados de consulta para armazenamento](create-external-table-as-select.md)
