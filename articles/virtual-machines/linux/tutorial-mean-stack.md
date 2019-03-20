---
title: Tutorial – Criar uma pilha MEAN numa máquina virtual do Linux no Azure | Microsoft Docs
description: Neste tutorial, vai aprender a criar uma pilha MongoDB, Express, AngularJS e Node.js (MEAN) numa VM do Linux no Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/08/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a8f756385b62dfb21e910b9373dc275c7f679d3e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58009666"
---
# <a name="tutorial-create-a-mongodb-express-angularjs-and-nodejs-mean-stack-on-a-linux-virtual-machine-in-azure"></a>Tutorial: Criar uma pilha MongoDB, Express, AngularJS e node. js (MEAN) numa máquina virtual do Linux no Azure

Este tutorial mostra como criar uma pilha MongoDB, Express, AngularJS e Node.js (MEAN) numa máquina virtual (VM) do Linux no Azure. A pila MEAN que cria permite adicionar, eliminar e listar livros numa base de dados. Saiba como:

> [!div class="checklist"]
> * Criar uma VM do Linux
> * Instalar o Node.js
> * Instalar o MongoDB e configurar o servidor
> * Instalar o Express e configurar caminhos para o servidor
> * Aceder a caminhos com o AngularJS
> * Executar a aplicação

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial requer que execute uma versão da CLI do Azure que seja a 2.0.30 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).


## <a name="create-a-linux-vm"></a>Criar uma VM do Linux

Crie um grupo de recursos com o comando [az group create](https://docs.microsoft.com/cli/azure/group) e crie uma VM do Linux com o comando [az vm create](https://docs.microsoft.com/cli/azure/vm). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo seguinte utiliza a CLI do Azure para criar um grupo de recursos com o nome *myResourceGroupMEAN* na localização *eastus*. É criada uma VM com o nome *myVM* com chaves SSH caso estas ainda não existam numa localização chave predefinida. Para utilizar um conjunto específico de chaves, utilize a opção --ssh-key-value.

```azurecli-interactive
az group create --name myResourceGroupMEAN --location eastus
az vm create \
    --resource-group myResourceGroupMEAN \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password 'Azure12345678!' \
    --generate-ssh-keys
az vm open-port --port 3300 --resource-group myResourceGroupMEAN --name myVM
```

Quando a VM tiver sido criada, a CLI do Azure mostra informações semelhantes ao seguinte exemplo: 

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroupMEAN/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.72.77.9",
  "resourceGroup": "myResourceGroupMEAN"
}
```
Tome nota do `publicIpAddress`. Este endereço é utilizado para aceder à VM.

Utilize o seguinte comando para criar uma sessão SSH com a VM. Certifique-se de que utiliza o endereço IP público correto. No exemplo acima, o nosso endereço IP era 13.72.77.9.

```bash
ssh azureuser@13.72.77.9
```

## <a name="install-nodejs"></a>Instalar o Node.js

O [Node.js](https://nodejs.org/en/) é um runtime JavaScript criado no motor JavaScript V8 do Chrome. O Node.js é utilizado neste tutorial para configurar as rotas Express e os controladores AngularJS.

Na VM, através da shell de bash que abriu com o SSH, instale o Node.js.

```bash
sudo apt-get install -y nodejs
```

## <a name="install-mongodb-and-set-up-the-server"></a>Instalar o MongoDB e configurar o servidor
O [MongoDB](https://www.mongodb.com) armazena dados em documentos flexíveis, semelhantes a JSON. Os campos numa base de dados podem variar de documento para documento e a estrutura de dados pode mudar ao longo do tempo. Na nossa aplicação de exemplo, vamos adicionar registos de livros ao MongoDB que contêm o nome do livro, o número ISBN, o autor e o número de páginas. 

1. Na VM, através da shell de bash que abriu com o SSH, defina a chave MongoDB.

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
    echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
    ```

2. Atualize o gestor de pacotes com a chave.
  
    ```bash
    sudo apt-get update
    ```

3. Instale o MongoDB.

    ```bash
    sudo apt-get install -y mongodb
    ```

4. Inicie o servidor.

    ```bash
    sudo service mongodb start
    ```

5. Precisamos também de instalar o pacote [body-parser](https://www.npmjs.com/package/body-parser-json) para ajudar-nos a processar o JSON passado em pedidos ao servidor.

    Instale o gestor de pacotes npm.

    ```bash
    sudo apt-get install npm
    ```

    Instale o pacote de analisador de corpo.
    
    ```bash
    sudo npm install body-parser
    ```

6. Crie uma pasta com o nome *Livros* e adicione um ficheiro à mesma, que contenha a configuração do servidor Web, com o nome *server.js*.

    ```javascript
    var express = require('express');
    var bodyParser = require('body-parser');
    var app = express();
    app.use(express.static(__dirname + '/public'));
    app.use(bodyParser.json());
    require('./apps/routes')(app);
    app.set('port', 3300);
    app.listen(app.get('port'), function() {
        console.log('Server up: http://localhost:' + app.get('port'));
    });
    ```

## <a name="install-express-and-set-up-routes-to-the-server"></a>Instalar o Express e configurar caminhos para o servidor

O [Express](https://expressjs.com) é uma arquitetura de aplicação Web Node.js mínima e flexível que fornece funcionalidades para aplicações Web e móveis. O Express é utilizado neste tutorial para enviar informações de livros de e para a nossa base de dados MongoDB. O [Mongoose](https://mongoosejs.com) proporciona uma solução simples e baseada em esquema para modelar os seus dados de aplicações. O Mongoose é utilizado neste tutorial para fornecer um esquema de livros para a base de dados.

1. Instale o Express e o Mongoose.

    ```bash
    sudo npm install express mongoose
    ```

2. Na pasta *Livros*, crie uma pasta com o nome *aplicações* e adicione um ficheiro com o nome *routes.js* com os caminhos expressos definidos.

    ```javascript
    var Book = require('./models/book');
    module.exports = function(app) {
      app.get('/book', function(req, res) {
        Book.find({}, function(err, result) {
          if ( err ) throw err;
          res.json(result);
        });
      }); 
      app.post('/book', function(req, res) {
        var book = new Book( {
          name:req.body.name,
          isbn:req.body.isbn,
          author:req.body.author,
          pages:req.body.pages
        });
        book.save(function(err, result) {
          if ( err ) throw err;
          res.json( {
            message:"Successfully added book",
            book:result
          });
        });
      });
      app.delete("/book/:isbn", function(req, res) {
        Book.findOneAndRemove(req.query, function(err, result) {
          if ( err ) throw err;
          res.json( {
            message: "Successfully deleted the book",
            book: result
          });
        });
      });
      var path = require('path');
      app.get('*', function(req, res) {
        res.sendfile(path.join(__dirname + '/public', 'index.html'));
      });
    };
    ```

3. Na pasta *aplicações*, crie uma pasta com o nome *modelos* e adicione um ficheiro com o nome *book.js*, com a configuração de modelo de livros definida.  

    ```javascript
    var mongoose = require('mongoose');
    var dbHost = 'mongodb://localhost:27017/test';
    mongoose.connect(dbHost);
    mongoose.connection;
    mongoose.set('debug', true);
    var bookSchema = mongoose.Schema( {
      name: String,
      isbn: {type: String, index: true},
      author: String,
      pages: Number
    });
    var Book = mongoose.model('Book', bookSchema);
    module.exports = mongoose.model('Book', bookSchema); 
    ```

## <a name="access-the-routes-with-angularjs"></a>Aceder a caminhos com o AngularJS

O [AngularJS](https://angularjs.org) fornece uma arquitetura Web para criar vistas dinâmicas nas suas aplicações Web. Neste tutorial, utilizamos o AngularJS para ligar a nossa página Web ao Express e efetuar ações na nossa base de dados de livros.

1. Reverta o diretório para *Livros* (`cd ../..`) e, em seguida, crie uma pasta com o nome *público* e adicione um ficheiro com o nome *script.js* com a configuração de controlador definida.

    ```javascript
    var app = angular.module('myApp', []);
    app.controller('myCtrl', function($scope, $http) {
      $http( {
        method: 'GET',
        url: '/book'
      }).then(function successCallback(response) {
        $scope.books = response.data;
      }, function errorCallback(response) {
        console.log('Error: ' + response);
      });
      $scope.del_book = function(book) {
        $http( {
          method: 'DELETE',
          url: '/book/:isbn',
          params: {'isbn': book.isbn}
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
      $scope.add_book = function() {
        var body = '{ "name": "' + $scope.Name + 
        '", "isbn": "' + $scope.Isbn +
        '", "author": "' + $scope.Author + 
        '", "pages": "' + $scope.Pages + '" }';
        $http({
          method: 'POST',
          url: '/book',
          data: body
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
    });
    ```
    
2. Na pasta *público*, crie um ficheiro com o nome *index.html* com a página Web definida.

    ```html
    <!doctype html>
    <html ng-app="myApp" ng-controller="myCtrl">
      <head>
        <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
        <script src="script.js"></script>
      </head>
      <body>
        <div>
          <table>
            <tr>
              <td>Name:</td> 
              <td><input type="text" ng-model="Name"></td>
            </tr>
            <tr>
              <td>Isbn:</td>
              <td><input type="text" ng-model="Isbn"></td>
            </tr>
            <tr>
              <td>Author:</td> 
              <td><input type="text" ng-model="Author"></td>
            </tr>
            <tr>
              <td>Pages:</td>
              <td><input type="number" ng-model="Pages"></td>
            </tr>
          </table>
          <button ng-click="add_book()">Add</button>
        </div>
        <hr>
        <div>
          <table>
            <tr>
              <th>Name</th>
              <th>Isbn</th>
              <th>Author</th>
              <th>Pages</th>
            </tr>
            <tr ng-repeat="book in books">
              <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
              <td>{{book.name}}</td>
              <td>{{book.isbn}}</td>
              <td>{{book.author}}</td>
              <td>{{book.pages}}</td>
            </tr>
          </table>
        </div>
      </body>
    </html>
    ```

##  <a name="run-the-application"></a>Executar a aplicação

1. Reverta o diretório para *Livros* (`cd ..`) e inicie o servidor ao executar este comando:

    ```bash
    nodejs server.js
    ```

2. Abra um browser Web no endereço que registou para a VM. Por exemplo, *http://13.72.77.9:3300*. Deverá ver algo semelhante à seguinte página:

    ![Registo de livros](media/tutorial-mean/meanstack-init.png)

3. Introduza dados nas caixas de texto e clique em **Adicionar**. Por exemplo:

    ![Adicionar registo de livros](media/tutorial-mean/meanstack-add.png)

4. Após atualizar a página, deverá ver algo parecido com esta página:

    ![Listar registos de livros](media/tutorial-mean/meanstack-list.png)

5. Pode clicar em **Eliminar** e remover o registo de livros da base de dados.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criou uma aplicação Web que acompanha os registos de livros através de uma pilha MEAN numa VM do Linux. Aprendeu a:

> [!div class="checklist"]
> * Criar uma VM do Linux
> * Instalar o Node.js
> * Instalar o MongoDB e configurar o servidor
> * Instalar o Express e configurar caminhos para o servidor
> * Aceder a caminhos com o AngularJS
> * Executar a aplicação

Avance para o tutorial seguinte para aprender a proteger os servidores Web com certificados SSL.

> [!div class="nextstepaction"]
> [Proteger o servidor Web com SSL](tutorial-secure-web-server.md)
