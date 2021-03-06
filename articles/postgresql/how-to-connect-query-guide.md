---
title: Conecte-se e consulta - Servidor Único PostgreSQL
description: Links para quickstarts que mostram como ligar à sua Base de Dados Azure para o Servidor Único Pós-SQL e executar consultas.
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/21/2020
ms.openlocfilehash: 1506ce311fe443247050a36e1b9fa4600360ac6e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604146"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--single-server"></a>Descrição geral da ligação e consulta da Base de Dados do Azure para PostgreSQL – Servidor Único

O documento que se segue inclui links para exemplos que mostram como conectar e consultar a Base de Dados Azure para o Servidor Único Pós-SQL. Este guia também inclui recomendações e extensão TLS que pode usar para ligar ao servidor em idiomas suportados abaixo.

## <a name="quickstarts"></a>Guias de Início Rápido

| Início Rápido | Description |
|---|---|
|[Pgadmin](https://www.pgadmin.org/)|Pode utilizar a pgadmin para se ligar ao servidor e simplifica a criação, manutenção e utilização de objetos de base de dados.|
|[psql em Azure Cloud Shell](quickstart-create-server-database-azure-cli.md#connect-to-the-azure-database-for-postgresql-server-by-using-psql)|Este artigo mostra como executar [**o psql**](https://www.postgresql.org/docs/current/static/app-psql.html) em [Azure Cloud Shell](../cloud-shell/overview.md) para ligar ao seu servidor e, em seguida, executar declarações para consultar, inserir, atualizar e apagar dados na base de dados. Você pode executar **psql** se instalado no seu ambiente de desenvolvimento|
|[PostgreSQL com Código VS](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)|A extensão de Bases de Dados Azure para O Código VS (Preview) permite-lhe navegar e consultar o servidor PostgreSQL tanto localmente como na nuvem utilizando livros de recortes com intellisense rico. |
|[PHP](connect-php.md)|Este quickstart demonstra como usar PHP para criar um programa para ligar a uma base de dados e usar trabalho com objetos de base de dados para consultar dados.|
|[Java](connect-java.md)|Este quickstart demonstra como usar Java para ligar a uma base de dados e, em seguida, usar o trabalho com objetos de base de dados para consultar dados.|
|[Node.js](connect-nodejs.md)|Este quickstart demonstra como usar Node.js para criar um programa para ligar a uma base de dados e usar o trabalho com objetos de base de dados para consultar dados.|
|[.NET(C#)](connect-csharp.md)|Este quickstart demonstra como use.NET (C#) para criar um programa C# para ligar a uma base de dados e usar o trabalho com objetos de base de dados para consultar dados.|
|[Ir](connect-go.md)|Este quickstart demonstra como usar o Go para ligar a uma base de dados. As declarações Transact-SQL para consultar e modificar dados também são explicadas.|
|[Python](connect-python.md)|Este quickstart demonstra como usar python para ligar a uma base de dados e usar trabalho com objetos de base de dados para consultar dados. |
|[Ruby](connect-ruby.md)|Este quickstart demonstra como usar a Ruby para criar um programa para ligar a uma base de dados e usar o trabalho com objetos de base de dados para consultar dados.|

## <a name="tls-considerations-for-database-connectivity"></a>Considerações TLS para conectividade de base de dados

A Segurança da Camada de Transporte (TLS) é utilizada por todos os controladores que a Microsoft fornece ou suporta para se ligar a bases de dados em Azure Database for PostgreSQL. Não é necessária nenhuma configuração especial, mas faça cumprir o TLS 1.2 para os servidores recém-criados. Recomendamos que se estiver a utilizar TLS 1.0 e 1.1, então atualize a versão TLS para os seus servidores. Ver [Como Configurar TLS](howto-tls-configurations.md)

## <a name="postgresql-extensions"></a>Extensões do PostgreSQL

O PostgreSQL fornece a capacidade de alargar a funcionalidade da sua base de dados utilizando extensões. As extensões agrupam múltiplos objetos SQL relacionados num pacote individual, o qual pode ser carregado ou removido a partir da base de dados com um único comando. Após ser carregado para a base de dados, as extensões funcionam como funcionalidades incorporadas.

- [Extensões postgres 11](./concepts-extensions.md#postgres-11-extensions)
- [Extensões postgres 10](./concepts-extensions.md#postgres-10-extensions)
- [Extensões postgres 9.6](./concepts-extensions.md#postgres-96-extensions)

Para obter mais detalhes, consulte [como utilizar as extensões PostgreSQL no servidor Single](concepts-extensions.md).

## <a name="next-steps"></a>Passos seguintes

- [Migrar dados usando o despejo e restaurar](howto-migrate-using-dump-and-restore.md)
- [Migrar dados utilizando a importação e exportação](howto-migrate-using-export-and-import.md)
