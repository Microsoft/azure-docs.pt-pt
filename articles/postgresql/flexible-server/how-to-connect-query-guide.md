---
title: Ligar e consultar - Servidor Flexível PostgreSQL
description: Links para quickstarts que mostram como ligar à sua Base de Dados Azure para o Servidor Flexível PostgreSQL e executar consultas.
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 12/08/2020
ms.openlocfilehash: ee3b1f7db8bdafb1233b32579e032e8c864c37a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97364583"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--flexible-server"></a>Conecte e consulta visão geral para base de dados Azure para PostgreSQL- Servidor Flexível

O documento que se segue inclui links para exemplos que mostram como conectar e consultar a Base de Dados Azure para o Servidor Único Pós-SQL. Este guia também inclui recomendações e extensão TLS que pode usar para ligar ao servidor em idiomas suportados abaixo.

>[!IMPORTANT]
> A base de dados Azure para o Servidor Flexível PostgreSQL está em **pré-visualização**.

## <a name="quickstarts"></a>Guias de Início Rápido

| Início Rápido | Description |
|---|---|
|[Pgadmin](https://www.pgadmin.org/)|Pode utilizar a pgadmin para se ligar ao servidor e simplifica a criação, manutenção e utilização de objetos de base de dados.|
|[psql em Azure Cloud Shell](./quickstart-create-server-cli.md#connect-using-postgresql-command-line-client)|Este artigo mostra como executar [**o psql**](https://www.postgresql.org/docs/current/static/app-psql.html) em [Azure Cloud Shell](../../cloud-shell/overview.md) para ligar ao seu servidor e, em seguida, executar declarações para consultar, inserir, atualizar e apagar dados na base de dados. Você pode executar **psql** se instalado no seu ambiente de desenvolvimento|
|[Python](connect-python.md)|Este quickstart demonstra como usar python para ligar a uma base de dados e usar trabalho com objetos de base de dados para consultar dados. |
|[Django com Serviço de Aplicações](tutorial-django-app-service-postgres.md)|Este tutorial demonstra como usar a Ruby para criar um programa para ligar a uma base de dados e usar o trabalho com objetos de base de dados para consultar dados.|

## <a name="tls-considerations-for-database-connectivity"></a>Considerações TLS para conectividade de base de dados

A Segurança da Camada de Transporte (TLS) é utilizada por todos os controladores que a Microsoft fornece ou suporta para se ligar a bases de dados em Azure Database for PostgreSQL. Não é necessária nenhuma configuração especial, mas faça cumprir o TLS 1.2 para os servidores recém-criados. Recomendamos que se estiver a utilizar TLS 1.0 e 1.1, então atualize a versão TLS para os seus servidores. Ver [Como Configurar TLS](how-to-connect-tls-ssl.md)

## <a name="postgresql-extensions"></a>Extensões do PostgreSQL

O PostgreSQL fornece a capacidade de alargar a funcionalidade da sua base de dados utilizando extensões. As extensões agrupam múltiplos objetos SQL relacionados num pacote individual, o qual pode ser carregado ou removido a partir da base de dados com um único comando. Após ser carregado para a base de dados, as extensões funcionam como funcionalidades incorporadas.

- [Extensões postgres 12](./concepts-extensions.md#postgres-12-extensions)
- [Extensões postgres 11](./concepts-extensions.md#postgres-11-extensions)
- [dblink e postgres_fdw](./concepts-extensions.md#dblink-and-postgres_fdw)
- [pg_prewarm](./concepts-extensions.md#pg_prewarm)
- [pg_stat_statements](./concepts-extensions.md#pg_stat_statements)

Para obter mais detalhes, consulte [como utilizar extensões PostgreSQL no servidor Flexível](concepts-extensions.md).

## <a name="next-steps"></a>Passos seguintes

- [Migrar dados usando o despejo e restaurar](../howto-migrate-using-dump-and-restore.md)
- [Migrar dados utilizando a importação e exportação](../howto-migrate-using-export-and-import.md)
