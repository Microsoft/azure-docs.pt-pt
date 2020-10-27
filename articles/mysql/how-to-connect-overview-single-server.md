---
title: Conecte-se e consulta - Servidor Único MySQL
description: Links para quickstarts mostrando como ligar ao seu Azure My SQL Database Single Server e executar consultas.
services: mysql
ms.service: mysql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/22/2020
ms.openlocfilehash: 0414aaad5a1cf6edb9c2152eed70f8753946cca1
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546437"
---
# <a name="connect-and-query-overview-for-azure-database-for-mysql--single-server"></a>Conecte e consulta visão geral para base de dados Azure para MySQL- Servidor Único

O documento que se segue inclui links para exemplos que mostram como conectar e consultar a Base de Dados Azure para o MySQL Single Server. Este guia também inclui recomendações e bibliotecas TLS que pode usar para ligar ao servidor em idiomas suportados abaixo.

## <a name="quickstarts"></a>Guias de Início Rápido

| Início Rápido | Descrição |
|---|---|
|[Bancada mySQL](connect-workbench.md)|Este quickstart demonstra como usar o Cliente de Workbench MySQL para se ligar a uma base de dados. Em seguida, pode utilizar as declarações do MySQL para consultar, inserir, atualizar e eliminar dados na base de dados.|
|[Azure Cloud Shell](./quickstart-create-mysql-server-database-using-azure-cli.md#connect-to-azure-database-for-mysql-server-using-mysql-command-line-client)|Este artigo mostra como executar **mysql.exe** em [Azure Cloud Shell](../cloud-shell/overview.md) para ligar ao seu servidor e, em seguida, executar declarações para consultar, inserir, atualizar e apagar dados na base de dados.|
|[MySQL com Estúdio Visual](https://www.mysql.com/why-mysql/windows/visualstudio)|Pode utilizar o MySQL para Visual Studio para se ligar ao seu servidor MySQL. O MySQL para Visual Studio integra-se diretamente no Server Explorer, facilitando a configuração de novas ligações e trabalhando com objetos de base de dados.|
|[PHP](connect-php.md)|Este quickstart demonstra como usar PHP para criar um programa para ligar a uma base de dados e usar declarações mySQL para consultar dados.|
|[Java](connect-java.md)|Este quickstart demonstra como usar Java para ligar a uma base de dados e, em seguida, usar declarações mySQL para consultar dados.|
|[Node.js](connect-nodejs.md)|Este quickstart demonstra como usar Node.js para criar um programa para ligar a uma base de dados e usar declarações do MySQL para consultar dados.|
|[.NET(C#)](connect-csharp.md)|Este quickstart demonstra como use.NET (C#) para criar um programa C# para ligar a uma base de dados e usar declarações do MySQL para consultar dados.|
|[Ir](connect-go.md)|Este quickstart demonstra como usar o Go para ligar a uma base de dados. As declarações Transact-SQL para consultar e modificar dados também são explicadas.|
|[Python](connect-python.md)|Este quickstart demonstra como usar python para ligar a uma base de dados e usar declarações mySQL para consultar dados. |
|[Ruby](connect-ruby.md)|Este quickstart demonstra como usar a Ruby para criar um programa para ligar a uma base de dados e usar declarações do MySQL para consultar dados.|
|[C++](connect-cpp.md)|Este quickstart demonstra como usar o C+++ para criar um programa para ligar a uma base de dados e utilizar dados de consulta.|

## <a name="tls-considerations-for-database-connectivity"></a>Considerações TLS para conectividade de base de dados

A Segurança da Camada de Transporte (TLS) é utilizada por todos os controladores que a Microsoft fornece ou suporta para se ligar a bases de dados em Azure Database para o MySQL. Não é necessária nenhuma configuração especial, mas faça cumprir o TLS 1.2 para os servidores recém-criados. Recomendamos que se estiver a utilizar TLS 1.0 e 1.1, então atualize a versão TLS para os seus servidores. Ver [Como Configurar TLS](howto-tls-configurations.md)

## <a name="libraries"></a>Bibliotecas

A Azure Database for MySQL usa a edição comunitária mais popular do mundo da base de dados MySQL. Assim, é compatível com uma grande variedade de linguagens de programação e motoristas. O objetivo é apoiar as três versões mais recentes dos condutores do MySQL, e os esforços com autores da comunidade de código aberto para melhorar constantemente a funcionalidade e usabilidade dos condutores do MySQL continuam.

Veja quais [os controladores](concepts-compatibility.md) compatíveis com a Base de Dados Azure para o servidor MySQL Single.

## <a name="next-steps"></a>Passos seguintes

- [Migrar dados usando o despejo e restaurar](concepts-migrate-dump-restore.md)
- [Migrar dados utilizando a importação e exportação](concepts-migrate-import-export.md)