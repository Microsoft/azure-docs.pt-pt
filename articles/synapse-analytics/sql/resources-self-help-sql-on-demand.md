---
title: SQL a pedido De pré-ajuda) autoajuda
description: Esta secção contém informações que podem ajudá-lo a resolver problemas com a SQL a pedido (pré-visualização).
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: e2c262915c928cf487cb84aeb3423d67e7a96e97
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424833"
---
# <a name="self-help-for-sql-on-demand-preview"></a>Autoajuda para sQL a pedido (pré-visualização)

Este artigo contém informações sobre como resolver problemas mais frequentes com a SQL on-demand (pré-visualização) no Azure Synapse Analytics.

## <a name="sql-on-demand-is-grayed-out-in-synapse-studio"></a>SQL on-demand é cinza para fora em Estúdio Synapse

Se o Estúdio Synapse não conseguir estabelecer a ligação à SQL a pedido, notará que a SQL on-demand está cinzenta ou mostra o estado "Offline". Normalmente, este problema ocorre quando um dos seguintes casos acontece:

1) A sua rede impede a comunicação com o backend Azure Synapse. O caso mais frequente é que o porto 1443 está bloqueado. Para obter o SQL a pedido para trabalhar desbloquear este porto. Outros problemas poderiam impedir que a SQL a pedido também funcionasse, visitar guia completo de resolução de [problemas para mais informações.](../troubleshoot/troubleshoot-synapse-studio.md)
2) Não tem permissão para entrar na SQL a pedido. Para ter acesso, um dos administradores do espaço de trabalho Azure Synapse deve adicioná-lo ao administrador do espaço de trabalho ou ao papel de administrador do SQL. Visite o guia completo sobre o controlo de [acessos para mais informações.](access-control.md)

## <a name="query-fails-because-file-cannot-be-opened"></a>A consulta falha porque o ficheiro não pode ser aberto

Se a sua consulta falhar com o erro dizendo "O ficheiro não pode ser aberto porque não existe ou é usado por outro processo" e tem a certeza de que ambos os ficheiros existem e não é utilizado por outro processo, significa que a SQL a pedido não pode aceder ao ficheiro. Este problema geralmente acontece porque a sua identidade de Diretório Ativo Azure não tem direitos de acesso ao ficheiro. Por padrão, a SQL on-demand está a tentar aceder ao ficheiro utilizando a sua identidade de Diretório Ativo Azure. Para resolver este problema, precisa de ter direitos adequados para aceder ao ficheiro. A forma mais fácil é conceder a si mesmo o papel de 'Storage Blob Data Contributor' na conta de armazenamento que está a tentar consultar. Visite o guia completo sobre o controlo de acesso ao [Diretório Ativo do Azure para obter mais informações.](../../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 

## <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>A consulta falha porque não pode ser executada devido a restrições de recursos atuais 

Se a sua consulta falhar com a mensagem de erro "Esta consulta não pode ser executada devido a restrições de recursos atuais", significa que o OD SQL não é capaz de executá-lo neste momento devido a restrições de recursos: 

- Por favor, certifique-se de que são utilizados tipos de dados de tamanhos razoáveis. Além disso, especifique o esquema para ficheiros Parquet para colunas de cordas, uma vez que serão VARCHAR(8000) por defeito. 

- Se a sua consulta visa ficheiros CSV, considere [criar estatísticas](develop-tables-statistics.md#statistics-in-sql-on-demand-preview). 

- Visite [as melhores práticas de desempenho para a SQL a pedido](best-practices-sql-on-demand.md) para otimizar a consulta.  

## <a name="next-steps"></a>Passos seguintes

Reveja os seguintes artigos para saber mais sobre como utilizar a SQL a pedido:

- [Consulta single CSV arquivo](query-single-csv-file.md)

- [Questiões e vários ficheiros CSV](query-folders-multiple-csv-files.md)

- [Consulta de ficheiros específicos](query-specific-files.md)

- [Arquivos De Consulta Parquet](query-parquet-files.md)

- [Tipos aninhados de Parquet de consulta](query-parquet-nested-types.md)

- [Consulta de ficheiros JSON](query-json-files.md)

- [Criar e usar vistas](create-use-views.md)

- [Criar e usar tabelas externas](create-use-external-tables.md)

- [Armazenar resultados de consulta para armazenamento](create-external-table-as-select.md)
