---
title: Ligar à Base de Dados do Azure para PostgreSQL a partir do Node.js
description: Este guia de início rápido proporciona um código de exemplo de Node.js que pode utilizar para se ligar e consultar dados da Base de Dados do Azure para PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc, devcenter
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 02/28/2018
ms.openlocfilehash: 4c7f2f71ff302cbaa0aab66f97b93cb1c5b9e5e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60623868"
---
# <a name="azure-database-for-postgresql-use-nodejs-to-connect-and-query-data"></a>Base de dados do Azure para PostgreSQL: Utilizar o node. js para ligar e consultar dados
Este guia de introdução explica como se pode ligar a uma Base de Dados do Azure para PostgreSQL através de uma aplicação [Node.js](https://nodejs.org/). Explica como utilizar as instruções SQL para consultar, inserir, atualizar e eliminar dados da base de dados. Os passos neste artigo pressupõem que está familiarizado com a programação com Node.js e que nunca trabalhou com a Base de Dados do Azure para PostgreSQL.

## <a name="prerequisites"></a>Pré-requisitos
Este guia de início rápido utiliza os recursos criados em qualquer um destes guias como ponto de partida:
- [Criar BD - Portal](quickstart-create-server-database-portal.md)
- [Criar BD - CLI](quickstart-create-server-database-azure-cli.md)

Também tem de:
- Instalar o [Node. js](https://nodejs.org)

## <a name="install-pg-client"></a>Instalar o cliente pg
Instale o [pg](https://www.npmjs.com/package/pg), que é um cliente PostgreSQL para Node.js.

Para isso, execute o gestor de pacotes de nós (npm) para JavaScript a partir da linha de comandos para instalar o cliente pg.
```bash
npm install pg
```

Verifique a instalação através de uma lista de pacotes instalados.
```bash
npm list
```

## <a name="get-connection-information"></a>Obter informações da ligação
Obtenha as informações de ligação necessárias para se ligar à Base de Dados do Azure para PostgreSQL. Necessita do nome do servidor e das credenciais de início de sessão totalmente qualificados.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. No menu esquerdo do portal do Azure, clique em **Todos os recursos** e, em seguida, procure o servidor que acabou de criar, (por exemplo, **mydemoserver**).
3. Clique no nome do servidor.
4. No painel **Descrição geral** do servidor, tome nota do **Nome do servidor** e do **Nome de início de sessão de administrador do servidor**. Caso se esqueça da sua palavra-passe, também pode repor a palavra-passe neste painel.
 ![Nome do servidor da Base de Dados do Azure para PostgreSQL](./media/connect-nodejs/1-connection-string.png)

## <a name="running-the-javascript-code-in-nodejs"></a>Executar o código JavaScript no Node.js
Pode iniciar o Node.js a partir do Bash shell, Terminal ou da Linha de Comandos do Windows ao introduzir `node` e, em seguida, executar o exemplo de código JavaScript, de forma interativa, copiando-o e colando-o na linha. Como alternativa, pode guardar o código JavaScript num ficheiro de texto e iniciar `node filename.js` com o nome do ficheiro como um parâmetro para o executar.

## <a name="connect-create-table-and-insert-data"></a>Ligar, criar tabela e inserir dados
Utilize o seguinte código para se ligar e carregar os dados com as instruções SQL **CREATE TABLE** e **INSERT INTO**.
O objeto [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) é utilizado para comunicar com o servidor PostgreSQL. A função [pg. Client.Connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) é utilizada para estabelecer ligação com o servidor. A função [pg. Client.Query()](https://github.com/brianc/node-postgres/wiki/Query) é utilizada para executar a consulta SQL na base de dados PostgreSQL. 

Substitua os parâmetros do sistema anfitrião, nome da base de dados, utilizador e palavra-passe pelos valores que especificou ao criar o servidor e a base de dados.

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        DROP TABLE IF EXISTS inventory;
        CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
        INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
        INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
        INSERT INTO inventory (name, quantity) VALUES ('apple', 100);
    `;

    client
        .query(query)
        .then(() => {
            console.log('Table created successfully!');
            client.end(console.log('Closed client connection'));
        })
        .catch(err => console.log(err))
        .then(() => {
            console.log('Finished execution, exiting now');
            process.exit();
        });
}
```

## <a name="read-data"></a>Ler dados
Utilize o código seguinte para se ligar e ler dados com uma instrução SQL **SELECT**. O objeto [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) é utilizado para comunicar com o servidor PostgreSQL. A função [pg. Client.Connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) é utilizada para estabelecer ligação com o servidor. A função [pg. Client.Query()](https://github.com/brianc/node-postgres/wiki/Query) é utilizada para executar a consulta SQL na base de dados PostgreSQL. 

Substitua os parâmetros do sistema anfitrião, nome da base de dados, utilizador e palavra-passe pelos valores que especificou ao criar o servidor e a base de dados. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else { queryDatabase(); }
});

function queryDatabase() {
  
    console.log(`Running query to PostgreSQL server: ${config.host}`);

    const query = 'SELECT * FROM inventory;';

    client.query(query)
        .then(res => {
            const rows = res.rows;

            rows.map(row => {
                console.log(`Read: ${JSON.stringify(row)}`);
            });

            process.exit();
        })
        .catch(err => {
            console.log(err);
        });
}
```

## <a name="update-data"></a>Atualizar dados
Utilize o código seguinte para se ligar e ler dados com uma instrução SQL **UPDATE**. O objeto [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) é utilizado para comunicar com o servidor PostgreSQL. A função [pg. Client.Connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) é utilizada para estabelecer ligação com o servidor. A função [pg. Client.Query()](https://github.com/brianc/node-postgres/wiki/Query) é utilizada para executar a consulta SQL na base de dados PostgreSQL. 

Substitua os parâmetros do sistema anfitrião, nome da base de dados, utilizador e palavra-passe pelos valores que especificou ao criar o servidor e a base de dados. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        UPDATE inventory 
        SET quantity= 1000 WHERE name='banana';
    `;

    client
        .query(query)
        .then(result => {
            console.log('Update completed');
            console.log(`Rows affected: ${result.rowCount}`);
        })
        .catch(err => {
            console.log(err);
            throw err;
        });
}
```

## <a name="delete-data"></a>Eliminar dados
Utilize o código seguinte para se ligar e ler os dados com a instrução SQL **DELETE**. O objeto [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) é utilizado para comunicar com o servidor PostgreSQL. A função [pg. Client.Connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) é utilizada para estabelecer ligação com o servidor. A função [pg. Client.Query()](https://github.com/brianc/node-postgres/wiki/Query) é utilizada para executar a consulta SQL na base de dados PostgreSQL. 

Substitua os parâmetros do sistema anfitrião, nome da base de dados, utilizador e palavra-passe pelos valores que especificou ao criar o servidor e a base de dados. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) {
        throw err;
    } else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        DELETE FROM inventory 
        WHERE name = 'apple';
    `;

    client
        .query(query)
        .then(result => {
            console.log('Delete completed');
            console.log(`Rows affected: ${result.rowCount}`);
        })
        .catch(err => {
            console.log(err);
            throw err;
        });
}
```

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Migrar a base de dados com Exportar e Importar](./howto-migrate-using-export-and-import.md)
