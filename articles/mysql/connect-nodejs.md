---
title: Conecte-se usando o Node.js - Base de Dados Azure para MySQL
description: Este guia de início rápido disponibiliza vários exemplos de código Node.js que pode utilizar para se ligar e consultar dados da Base de Dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 3/18/2020
ms.openlocfilehash: a8c4f84fe958c1b2762509432596fea772e39d7e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067940"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-in-azure-database-for-mysql"></a>Quickstart: Use Node.js para ligar e consultar dados na Base de Dados Azure para MySQL

Neste arranque rápido, ligue-se a uma Base de Dados Azure para MySQL utilizando o Node.js. Em seguida, utiliza declarações SQL para consultar, inserir, atualizar e eliminar dados na base de dados das plataformas Mac, Ubuntu Linux e Windows. 

Este tópico pressupõe que está familiarizado com o desenvolvimento usando Node.js, mas você é novo em trabalhar com a Base de Dados Azure para MySQL.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Uma base de dados Azure para servidor MySQL. [Crie uma base de dados Azure para servidor MySQL utilizando o portal Azure](quickstart-create-mysql-server-database-using-azure-portal.md) ou [crie uma base de dados Azure para o servidor MySQL utilizando o Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

## <a name="install-nodejs-and-the-mysql-connector"></a>Instalar Node.js e o conector de MySQL

Dependendo da sua plataforma, siga as instruções na secção apropriada para instalar [o Nó.js](https://nodejs.org). Utilize o npm para instalar o pacote [mysql](https://www.npmjs.com/package/mysql) e as suas dependências na sua pasta de projeto.

### <a name="windows"></a>**Windows**

1. Visite a [página de transferências do Node. js](https://nodejs.org/en/download/) e, em seguida, selecione a opção de instalador Windows que pretende.
2. Crie uma pasta de projeto local , como `nodejsmysql`. 
3. Abra o pedido de comando e, em seguida, mude diretório na pasta do projeto, como`cd c:\nodejsmysql\`
4. Executar a ferramenta NPM para instalar a biblioteca mysql na pasta do projeto.

   ```cmd
   cd c:\nodejsmysql\
   "C:\Program Files\nodejs\npm" install mysql
   "C:\Program Files\nodejs\npm" list
   ```

5. Verifique a instalação `npm list` verificando o texto de saída. O número da versão pode variar à medida que forem sendo lançados patches novos.

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

1. Execute os seguintes comandos para instalar **Node.js** e **npm**, o gestor de pacotes para Node.js.

   ```bash
   sudo apt-get install -y nodejs npm
   ```

2. Execute os seguintes comandos `mysqlnodejs` para criar uma pasta de projeto e instale o pacote mysql nessa pasta.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```
3. Verifique a instalação verificando o texto de saída da lista npm. O número da versão pode variar à medida que forem sendo lançados patches novos.

### <a name="mac-os"></a>**Mac OS**

1. Introduza os seguintes comandos para instalar **brew**, um gestor de pacotes de fácil utilização para Mac OS X e **Node.js**.

   ```bash
   ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
   brew install node
   ```
2. Execute os seguintes comandos `mysqlnodejs` para criar uma pasta de projeto e instale o pacote mysql nessa pasta.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```

3. Verifique a instalação `npm list` verificando o texto de saída. O número da versão pode variar à medida que forem sendo lançados patches novos.

## <a name="get-connection-information"></a>Obter informações da ligação

Obtenha as informações de ligação necessárias para se ligar à Base de Dados do Azure para MySQL. Necessita do nome do servidor e das credenciais de início de sessão totalmente qualificados.

1. Faça login no [portal Azure.](https://portal.azure.com/)
2. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos**, e depois procure o servidor que criou (como o **mydemoserver).**
3. Selecione o nome do servidor.
4. No painel **Descrição geral** do servidor, tome nota do **Nome do servidor** e do **Nome de início de sessão de administrador do servidor**. Caso se esqueça da sua palavra-passe, também pode repor a palavra-passe neste painel.
 ![Nome do servidor da Base de Dados do Azure para o MySQL](./media/connect-nodejs/server-name-azure-database-mysql.png)

## <a name="running-the-javascript-code-in-nodejs"></a>Executar o código JavaScript no Node.js

1. Cole o código JavaScript em ficheiros de texto e, em seguida, guarde-o numa pasta de projeto com a extensão de ficheiro .js, tal como (C:\nodejsmysql\createtable.js ou /home/username/nodejsmysql/createtable.js).
2. Abra o pedido de comando ou a concha de `cd nodejsmysql`bater e, em seguida, mude o diretório para a pasta do projeto .
3. Para executar a aplicação, insira o comando `node createtable.js`do nó seguido pelo nome do ficheiro, como .
4. No Windows, se a aplicação node não estiver no caminho de variável do seu ambiente, poderá ter de utilizar o caminho completo para iniciá-la, como, por exemplo, `"C:\Program Files\nodejs\node.exe" createtable.js`.

## <a name="connect-create-table-and-insert-data"></a>Ligar, criar tabela e inserir dados

Utilize o seguinte código para ligar e carregar os dados utilizando a **CREATE TABLE** e **insira as** declarações SQL.

O método [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) é utilizado para fazer a interface com o servidor MySQL. A função [connect()](https://github.com/mysqljs/mysql#establishing-connections) é utilizada para estabelecer a ligação ao servidor. A função [query()](https://github.com/mysqljs/mysql#performing-queries) é utilizada para executar a consulta SQL na base de dados MySQL. 

Substitua os parâmetros `host`, `user`, `password`, e `database` pelos valores que especificou quando criar o servidor e a base de dados.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
    if (err) { 
        console.log("!!! Cannot connect !!! Error:");
        throw err;
    }
    else
    {
       console.log("Connection established.");
           queryDatabase();
    }   
});

function queryDatabase(){
       conn.query('DROP TABLE IF EXISTS inventory;', function (err, results, fields) { 
            if (err) throw err; 
            console.log('Dropped inventory table if existed.');
        })
       conn.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);', 
            function (err, results, fields) {
                if (err) throw err;
            console.log('Created inventory table.');
        })
       conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['banana', 150], 
            function (err, results, fields) {
                if (err) throw err;
            else console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
       conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['orange', 154], 
            function (err, results, fields) {
                if (err) throw err;
            console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
       conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['apple', 100], 
        function (err, results, fields) {
                if (err) throw err;
            console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
       conn.end(function (err) { 
        if (err) throw err;
        else  console.log('Done.') 
        });
};
```

## <a name="read-data"></a>Ler dados

Utilize o código seguinte para se ligar e ler dados com uma instrução SQL **SELECT**. 

O método [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) é utilizado para fazer a interface com o servidor MySQL. O método [connect()](https://github.com/mysqljs/mysql#establishing-connections) é utilizado para estabelecer a ligação ao servidor. O método [query()](https://github.com/mysqljs/mysql#performing-queries) é utilizado para executar a consulta SQL na base de dados MySQL. A matriz de resultados é utilizada para armazenar os resultados da consulta.

Substitua os parâmetros `host`, `user`, `password`, e `database` pelos valores que especificou quando criar o servidor e a base de dados.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            readData();
        }   
    });

function readData(){
        conn.query('SELECT * FROM inventory', 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Selected ' + results.length + ' row(s).');
                for (i = 0; i < results.length; i++) {
                    console.log('Row: ' + JSON.stringify(results[i]));
                }
                console.log('Done.');
            })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Closing connection.') 
        });
};
```

## <a name="update-data"></a>Atualizar dados

Utilize o código seguinte para se ligar e ler os dados com uma instrução SQL **UPDATE**. 

O método [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) é utilizado para fazer a interface com o servidor MySQL. O método [connect()](https://github.com/mysqljs/mysql#establishing-connections) é utilizado para estabelecer a ligação ao servidor. O método [query()](https://github.com/mysqljs/mysql#performing-queries) é utilizado para executar a consulta SQL na base de dados MySQL. 

Substitua os parâmetros `host`, `user`, `password`, e `database` pelos valores que especificou quando criar o servidor e a base de dados.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            updateData();
        }   
    });

function updateData(){
       conn.query('UPDATE inventory SET quantity = ? WHERE name = ?', [200, 'banana'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Updated ' + results.affectedRows + ' row(s).');
        })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
```

## <a name="delete-data"></a>Eliminar dados

Utilize o código seguinte para se ligar e ler os dados com a instrução SQL **DELETE**. 

O método [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) é utilizado para fazer a interface com o servidor MySQL. O método [connect()](https://github.com/mysqljs/mysql#establishing-connections) é utilizado para estabelecer a ligação ao servidor. O método [query()](https://github.com/mysqljs/mysql#performing-queries) é utilizado para executar a consulta SQL na base de dados MySQL. 

Substitua os parâmetros `host`, `user`, `password`, e `database` pelos valores que especificou quando criar o servidor e a base de dados.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            deleteData();
        }   
    });

function deleteData(){
       conn.query('DELETE FROM inventory WHERE name = ?', ['orange'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Deleted ' + results.affectedRows + ' row(s).');
        })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Migrar a base de dados com Exportar e Importar](./concepts-migrate-import-export.md)
