---
title: Política de versão - Base de Dados Azure para PostgreSQL - Servidor Único e Servidor Flexível (Pré-visualização)
description: Descreve a política em torno de postgres principais e versões menores na Base de Dados Azure para PostgreSQL - Servidor Único.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: f92864cea4332157b0bf8b171a9d88b34f79a5ac
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422115"
---
# <a name="azure-database-for-postgresql-versioning-policy"></a>Base de dados Azure para a política de versão pós-SQL

Esta página descreve a base de dados Azure para a política de versão PostgreSQL, e é aplicável à Base de Dados Azure para pós-SQL - Servidor Único e Base de Dados Azure para modos de implementação pós-SQL - Servidor Flexível (Pré-visualização).

## <a name="supported--postgresql-versions"></a>Versões PostgreSQL suportadas

A Azure Database for PostgreSQL suporta as seguintes versões de base de dados.

| Versão | Servidor Único | Servidor Flexível (Pré-visualização) |
| ----- | :------: | :----: |
| PostgresQL 12 |  | X  | 
| PostgresqL 11 | X | X |
| PostgresQL 10 | X |  |
| PostgresQL 9.6 | X |  |
| PostgresQL 9.5 | X |  |

## <a name="major-version-support"></a>Suporte de versão principal
Cada versão principal do PostgreSQL será suportada pela Azure Database for PostgreSQL a partir da data em que o Azure começa a suportar a versão até que a versão seja retirada pela comunidade PostgreSQL, conforme indicado na [política de versão comunitária PostgreSQL.](https://www.postgresql.org/support/versioning/)

## <a name="minor-version-support"></a>Suporte de versão menor
A Azure Database for PostgreSQL executa automaticamente atualizações de versão menores para a versão PósgreSQL preferida do Azure como parte da manutenção periódica. 

## <a name="major-version-retirement-policy"></a>Política de reforma da versão principal
O quadro abaixo fornece os detalhes da reforma para as principais versões postgresQL. As datas seguem a [política de versão comunitária postgreSQL.](https://www.postgresql.org/support/versioning/)

| Versão | Novidades | Data de início do suporte Azure | Data da reforma|
| ----- | ----- | ------ | ----- |
| PostgresQL 9.5| [Funcionalidades](https://www.postgresql.org/docs/9.5/release-9-5.html)  | 18 de abril de 2018    | 11 de fevereiro de 2021
| [PostgresQL 9.6](https://www.postgresql.org/about/news/postgresql-96-released-1703/) | [Funcionalidades](https://wiki.postgresql.org/wiki/NewIn96) | 18 de abril de 2018  | 11 de novembro de 2021
| [PostgresQL 10](https://www.postgresql.org/about/news/postgresql-10-released-1786/) | [Funcionalidades](https://wiki.postgresql.org/wiki/New_in_postgres_10) | 4 de junho de 2018  | 10 de novembro de 2022
| [PostgresqL 11](https://www.postgresql.org/about/news/postgresql-11-released-1894/) | [Funcionalidades](https://www.postgresql.org/docs/11/release-11.html) | 24 de julho de 2019  | 9 de novembro de 2023
| [PostgresQL 12](https://www.postgresql.org/about/news/postgresql-12-released-1976/) | [Funcionalidades](https://www.postgresql.org/docs/12/release-12.html) | 22 de setembro de 2020  | 14 de novembro de 2024

## <a name="retired-postgresql-engine-versions-not-supported-in-azure-database-for-postgresql"></a>Versões de motores PostgreSQL aposentadas não suportadas na Base de Dados Azure para PostgreSQL

Após a data de aposentadoria de cada versão da base de dados PostgreSQL, se continuar a executar a versão aposentada, note as seguintes restrições:
- Uma vez que a comunidade não irá lançar quaisquer correções de bugs ou correções de segurança, o Azure for PostgreSQL não irá remendar o motor de base de dados reformado para quaisquer erros ou problemas de segurança ou de outra forma tomar medidas de segurança no que diz respeito ao motor de base de dados reformado. Como resultado, poderá experimentar vulnerabilidades de segurança ou outros problemas. No entanto, o Azure continuará a realizar manutenção e remendos periódicos para o hospedeiro, os contentores e quaisquer outros componentes relacionados com o serviço.
- Se algum problema de suporte que possa ter relacionado com a base de dados PostgreSQL, poderemos não ser capazes de lhe fornecer suporte. Nesses casos, terá de atualizar a sua base de dados para que lhe forneçamos qualquer suporte.
- Não será possível criar novos servidores de base de dados para a versão reformada. No entanto, poderá realizar recuperações pontuais e criar réplicas de leitura para os seus servidores existentes.
- As novas capacidades de serviço desenvolvidas pela Azure Database para PostgreSQL só podem estar disponíveis para versões suportadas do servidor de bases de dados.
- As SLAs de uptime aplicar-se-ão exclusivamente à Base de Dados Azure para problemas relacionados com o serviço PostgreSQL e não a qualquer tempo de inatividade causado por erros relacionados com o motor da base de dados.  
- No caso extremo de uma ameaça séria ao serviço causado pela vulnerabilidade do motor de base de dados PostgreSQL identificada na versão de base de dados aposentada, o Azure pode optar por parar o nó de computação do servidor de base de dados para proteger o serviço. Nesse caso, poderá ser notificado para atualizar o servidor antes de colocar o servidor online.

## <a name="postgresql-version-syntax"></a>Sintaxe da versão Pós-SQL
Antes da versão 10 do PostgreSQL, a [política de versão PostgreSQL](https://www.postgresql.org/support/versioning/) considerou uma _atualização de versão importante_ como um aumento no primeiro _ou_ segundo número. Por exemplo, 9.5 a 9.6 foi considerado uma _grande_ atualização de versão. A partir da versão 10, apenas uma alteração no primeiro número é considerada uma grande atualização de versão. Por exemplo, 10.0 a 10.1 é uma _pequena_ atualização de lançamento. A versão 10 a 11 é uma _grande_ atualização de versão.

## <a name="next-steps"></a>Passos seguintes
- Ver Base de Dados Azure para versões pós-SQL - Servidor [Único suportadas](./concepts-supported-versions.md)
- Consulte a base de dados Azure para versões [suportadas](flexible-server/concepts-supported-versions.md) por PostgreSQL - Servidor Flexível (Pré-visualização)
- Para obter informações sobre como realizar atualizações de grandes versões, consulte a documentação [de atualizações da versão Principal.](how-to-upgrade-using-dump-and-restore.md)
- Para obter informações sobre extensões postgresQL suportadas, consulte [o documento de extensões](concepts-extensions.md).
