---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: ff7ba04271c150018f2c55b62e40542a686608cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184769"
---
## <a name="create-a-client-connection"></a><a name="create-client"></a>Criar uma ligação de cliente
Criar uma ligação de cliente através da criação de um objeto `WindowsAzure.MobileServiceClient`.  Substitua `appUrl` pelo URL da sua Aplicação Móvel.

```javascript
var client = WindowsAzure.MobileServiceClient(appUrl);
```

## <a name="work-with-tables"></a><a name="table-reference"></a>Trabalhar com tabelas
Para atualizar ou aceder a dados, crie uma referência para a tabela de back-end. Substitua `tableName` pelo nome da sua tabela

```javascript
var table = client.getTable(tableName);
```

Assim que tiver uma referência da tabela, pode trabalhar mais com a mesma:

* [Consultar uma Tabela](#querying)
  * [Filtrar Dados](#table-filter)
  * [Paginar através de Dados](#table-paging)
  * [Ordenar Dados](#sorting-data)
* [Inserir Dados](#inserting)
* [Modificar dados](#modifying)
* [Eliminar Dados](#deleting)

### <a name="how-to-query-a-table-reference"></a><a name="querying"></a>Como: consultar uma referência de tabela
Assim que tiver uma referência de tabela, pode utilizá-la para consultar os dados no servidor.  As consultas são efetuadas num idioma "tipo LINQ".
Para devolver todos os dados da tabela, utilize o seguinte código:

```javascript
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

A função de êxito é chamada com os resultados.  Não utilize `for (var i in results)` na função de sucesso, uma vez que irá iterar sobre informações que estão incluídas nos resultados quando são utilizadas outras funções de consulta (como `.includeTotalCount()`).

Para obter mais informações sobre a Sintaxe da consulta, consulte a [Documentação de objeto de consulta].

#### <a name="filtering-data-on-the-server"></a><a name="table-filter"></a>Filtrar dados no servidor
Pode utilizar uma cláusula `where` na referência de tabela:

```javascript
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

Também pode utilizar uma função que filtra o objeto.  Neste caso, a variável `this` é atribuída ao objeto atual a ser filtrado.  O código seguinte é funcionalmente equivalente ao exemplo anterior:

```javascript
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

#### <a name="paging-through-data"></a><a name="table-paging"></a>Paging através de dados
Utilize os métodos `take()` e `skip()`.  Por exemplo, se pretender dividir a tabela em registos de 100 linhas:

```javascript
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

O método `.includeTotalCount()` é utilizado para adicionar um campo totalCount para o objeto de resultados.  O campo totalCount é preenchido com o número total de registos que seria devolvido se a paginação não fosse utilizada.

Em seguida, pode utilizar a variável de páginas e alguns botões da IU para fornecer uma lista de páginas; utilize `loadPage()` para carregar os registos novos para cada página.  Implemente a colocação em cache para acelerar o acesso aos registos que já foram carregados.

#### <a name="how-to-return-sorted-data"></a><a name="sorting-data"></a>Como: devolver dados ordenados
Utilize os métodos de consulta `.orderBy()` ou `.orderByDescending()`:

```javascript
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

Para obter mais informações sobre o Objeto da consulta, consulte a [Documentação de objeto de consulta].

### <a name="how-to-insert-data"></a><a name="inserting"></a>Como: inserir dados
Crie um objeto de JavaScript com a data adequada e chame `table.insert()` assincronamente:

```javascript
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

Ao inserir com êxito, o item inserido é devolvido com os campos adicionais que são necessários para operações de sincronização.  Atualize a sua própria cache com estas informações para atualizações posteriores.

O SDK do Servidor Node.js das Aplicações Móveis do Azure suporta um esquema dinâmica para fins de desenvolvimento.  O Esquema Dinâmico permite-lhe adicionar colunas à tabela, especificando-as numa operação de inserção ou atualização.  Recomendamos que desative o esquema dinâmico antes de mover a sua aplicação para produção.

### <a name="how-to-modify-data"></a><a name="modifying"></a>Como: modificar dados
De forma semelhante ao método `.insert()`, deve criar um Objeto de atualização e, em seguida, chamar `.update()`.  O objeto de atualização tem de conter o ID do registo a ser atualizado - o ID é obtido ao ler o registo ou quando chamar `.insert()`.

```javascript
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

### <a name="how-to-delete-data"></a><a name="deleting"></a>Como: eliminar dados
Para eliminar um registo, chame o método `.del()`.  Introduza o ID numa referência de objeto:

```javascript
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```
