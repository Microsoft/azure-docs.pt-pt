---
title: Ligar uma aplicação MongoDB node. js ao Azure Cosmos DB
description: Este início rápido demonstra como se pode ligar uma aplicação MongoDB existente escrita em node. js ao Azure Cosmos DB.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 12/06/2018
ms.openlocfilehash: 99f6de4d0441594c09e5e8c29834b90aeb1d43a6
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56586483"
---
# <a name="quickstart-migrate-an-existing-mongodb-nodejs-web-app-to-azure-cosmos-db"></a>Início rápido: Migrar uma aplicação web do node. js do MongoDB existente para o Azure Cosmos DB 

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

O Azure Cosmos DB é um serviço de base de dados com vários modelos e de distribuição global da Microsoft. Pode criar e consultar documentos e bases de dados de gráfico, que beneficiam de capacidades de escalamento horizontal no núcleo do Cosmos DB e distribuição global chave/valor rapidamente. 

Este início rápido demonstra como utilizar uma aplicação MongoDB existente escrita em node. js e ligá-la à sua base de dados do Cosmos, que suporta o cliente do MongoDB. Em outras palavras, é transparente para a aplicação que os dados são armazenados numa base de dados do Cosmos.

Quando tiver terminado, terá uma aplicação MEAN (MongoDB, Express, Angular e node. js) em execução no [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

![Aplicação MEAN.js em execução no Serviço de Aplicações do Azure](./media/create-mongodb-nodejs/meanjs-in-azure.png)


[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="prerequisites"></a>Pré-requisitos 
Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Além da CLI do Azure, precisa de ter o [Node.js](https://nodejs.org/) e o [Git](https://www.git-scm.com/downloads) instalados localmente para executar os comandos `npm` e `git`.

Deve ter conhecimento prático do Node.js. Este guia de introdução não foi concebido para o ajudar a desenvolver aplicações Node.js em geral.

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Execute os seguintes comandos para clonar o repositório de exemplo. Este repositório de exemplo contém a aplicação [MEAN.js](https://meanjs.org/) predefinida.

1. Abra uma linha de comandos, crie uma nova pasta designada git-samples e, em seguida, feche a linha de comandos.

    ```bash
    md "C:\git-samples"
    ```

2. Abra uma janela de terminal do git, como o git bash e utilize o comando `cd` para alterar para uma nova pasta e instalar a aplicação de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador. 

    ```bash
    git clone https://github.com/prashanthmadi/mean
    ```

## <a name="run-the-application"></a>Executar a aplicação

Instale os pacotes necessários e inicie a aplicação.

```bash
cd mean
npm install
npm start
```
A aplicação irá tentar ligar a uma origem do MongoDB e falhar, saia da aplicação quando devolver o resultado "[MongoError: connect ECONNREFUSED 127.0.0.1:27017]".

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Se estiver a utilizar uma CLI do Azure instalada, inicie sessão na sua subscrição do Azure com o comando [az login](/cli/azure/reference-index#az-login) e siga as instruções no ecrã. Pode ignorar esta etapa se estiver a utilizar o Azure Cloud Shell.

```azurecli
az login 
``` 
   
## <a name="add-the-azure-cosmos-db-module"></a>Adicionar o módulo Azure Cosmos DB

Se estiver a utilizar uma CLI do Azure instalada, veja se o componente `cosmosdb` já está instalado ao executar o comando `az`. Se `cosmosdb` está na lista de comandos de base, siga para o próximo comando. Pode ignorar esta etapa se estiver a utilizar o Azure Cloud Shell.

Se `cosmosdb` não estiver na lista de comandos de base, reinstale a [CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos](../azure-resource-manager/resource-group-overview.md) com o [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure, como aplicações Web, bases de dados e contas de armazenamento, são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos na região Europa Ocidental. Escolha um nome exclusivo para o grupo de recursos.

Se estiver a utilizar o Azure Cloud Shell, clique em **Experimente**, siga as instruções no ecrã para iniciar sessão e, em seguida, copie o comando para a linha de comandos.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Criar uma conta do Cosmos com o [az cosmosdb criar](/cli/azure/cosmosdb#az-cosmosdb-create) comando.

No comando seguinte, substitua o nome de conta do Cosmos exclusivo onde vir o `<cosmosdb-name>` marcador de posição. Este nome exclusivo será utilizado como parte do ponto final do Cosmos DB (`https://<cosmosdb-name>.documents.azure.com/`), por isso, o nome tem de ser exclusivo em todas as contas do Cosmos no Azure. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

O parâmetro `--kind MongoDB` permite ligações de cliente da MongoDB.

Após criar a conta do DB Cosmos Azure, a CLI do Azure mostra informações semelhantes ao exemplo seguinte. 

> [!NOTE]
> Este exemplo utiliza o JSON como formato de saída da CLI do Azure, que é a predefinição. Para utilizar outro formato de saída, veja [Formatos de saída para os comandos da CLI do Azure](https://docs.microsoft.com/cli/azure/format-output-azure-cli).

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>Ligar a aplicação Node.js à base de dados

Neste passo, vai ligar a aplicação de exemplo Mean. js à base de dados do Cosmos que acabou de criar. 

<a name="devconfig"></a>
## <a name="configure-the-connection-string-in-your-nodejs-application"></a>Configurar a cadeia de ligação na aplicação Node.js

No repositório MEAN.js, abra `config/env/local-development.js`.

Substitua o conteúdo deste ficheiro pelo código seguinte. Certifique-se de que também substitui os dois `<cosmosdb-name>` marcadores de posição pelo nome da sua conta Cosmos.

```javascript
'use strict';

module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean-dev?ssl=true&sslverifycertificate=false'
  }
};
```

## <a name="retrieve-the-key"></a>Obter a chave

Para ligar a uma base de dados do Cosmos, precisa da chave de base de dados. Utilize o comando [az cosmosdb list-keys](/cli/azure/cosmosdb#az-cosmosdb-list-keys) para obter a chave primária.

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

A CLI do Azure apresenta informações semelhantes ao exemplo seguinte. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

Copie o valor de `primaryMasterKey`. Cole-o em `<primary_master_key>` em `local-development.js`.

Guarde as alterações.

### <a name="run-the-application-again"></a>Execute novamente a aplicação.

Execute novamente `npm start`. 

```bash
npm start
```

Uma mensagem de consola deverá agora indicar que o ambiente de desenvolvimento está ativo e em execução. 

Navegue para `http://localhost:3000` num browser. Clique em **Inscrever-se**, no menu superior, e tente criar dois utilizadores fictícios. 

A aplicação MEAN.js de exemplo armazena os dados do utilizador na base de dados. Se o procedimento for concluído com êxito e a aplicação MEAN.js iniciar automaticamente sessão com o utilizador criado, significa que a ligação do DB Cosmos Azure está a funcionar. 

![A aplicação MEAN.js estabelece ligação com êxito ao MongoDB](./media/create-mongodb-nodejs/mongodb-connect-success.png)

## <a name="view-data-in-data-explorer"></a>Ver dados no Data Explorer

Dados armazenados num banco de dados do Cosmos estão disponíveis para exibição e a consulta no portal do Azure.

Para ver, consultar e trabalhar com os dados do utilizador criados no passo anterior, inicie a sessão no [portal do Azure](https://portal.azure.com) no browser.

Na caixa de pesquisa na parte superior, escreva Azure Cosmos DB. Quando abre o painel da conta do Cosmos, selecione a sua conta do Cosmos. No painel de navegação esquerdo, clique em Data Explorer. Expanda a coleção no painel Coleções e, em seguida, pode ver os documentos na coleção, consultar os dados e, ainda, criar e executar UDFs, acionadores e procedimentos armazenados. 

![Data Explorer no portal do Azure](./media/create-mongodb-nodejs/cosmosdb-connect-mongodb-data-explorer.png)


## <a name="deploy-the-nodejs-application-to-azure"></a>Implementar a aplicação Node.js no Azure

Neste passo, vai implementar a aplicação do node. js do Cosmos DB.

Poderá ter reparado que o ficheiro de configuração que alterou anteriormente se destina ao ambiente de desenvolvimento (`/config/env/local-development.js`). Ao implementar a aplicação no Serviço de Aplicações, esta será executada, por predefinição, no ambiente de produção. Por isso, agora, tem de efetuar a mesma alteração no ficheiro de configuração respetivo.

No repositório MEAN.js, abra `config/env/production.js`.

No objeto `db`, substitua o valor do `uri`, conforme apresentado no exemplo seguinte. Confirme que substitui os marcadores de posição, como indicado anteriormente.

```javascript
'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean?ssl=true&sslverifycertificate=false',
```

> [!NOTE] 
> O `ssl=true` opção é importante porque [Cosmos DB requer SSL](connect-mongodb-account.md#connection-string-requirements). 
>
>

No terminal, confirme todas as alterações no Git. Pode copiar os dois comandos para executá-los em simultâneo.

```bash
git add .
git commit -m "configured MongoDB connection string"
```
## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu como criar uma conta do Cosmos, criar uma coleção e executar uma aplicação de consola. Agora, pode importar dados adicionais à sua base de dados do Cosmos. 

> [!div class="nextstepaction"]
> [Importar dados da MongoDB para o Azure Cosmos DB](mongodb-migrate.md)
