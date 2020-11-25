---
title: Construa uma aplicação web Python Flask usando a API da Azure Cosmos DB para a MongoDB
description: Apresenta uma amostra de código Python Flask que pode usar para ligar e consultar usando a API da Azure Cosmos DB para o MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.custom: devx-track-python
ms.openlocfilehash: 58f22a335f4c619a6348e9e127e60f5a79f658b2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994567"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Quickstart: Construa uma app Python utilizando a API da Azure Cosmos DB para a MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Neste quickstart, você usa um DB Azure Cosmos para a conta API da Mongo DB ou o Emulador Azure Cosmos DB para executar uma aplicação web Python Flask To-Do clonada do GitHub. Azure Cosmos DB é um serviço de base de dados multi-modelo que permite criar e consultar rapidamente documentos, tabelas, valor-chave e bases de dados de gráficos com capacidades de distribuição global e escala horizontal.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Ou [experimente a Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição do Azure. Ou pode usar o [Emulador Azure Cosmos DB](local-emulator.md). 
- [Python 3.6+](https://www.python.org/downloads/)
- [Código de estúdio visual](https://code.visualstudio.com/Download) com a [extensão Python](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python).

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora vamos clonar uma aplicação Flask-MongoDB do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente.

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
    git clone https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample.git
    ```
3. Execute o seguinte comando para instalar os módulos de python.

    ```bash 
    pip install -r .\requirements.txt
    ```
4. Abra a pasta no Visual Studio Code.

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em aprender de que forma os recursos da base de dados são criados no código, pode consultar os seguintes fragmentos. Caso contrário, pode avançar para [Executar a aplicação Web](#run-the-web-app). 

Os seguintes snippets são todos retirados do ficheiro *app.py* e usam a cadeia de ligação para o Emulador Azure Cosmos DB local. A palavra-passe tem de ser dividida, como pode ver baixo, para adaptar-se às barras que não podem ser analisadas de outra forma.

* Inicialize o cliente MongoDB, obtenha a base de dados e faça a autenticação.

    ```python
    client = MongoClient("mongodb://127.0.0.1:10250/?ssl=true") #host uri
    db = client.test    #Select the database
    db.authenticate(name="localhost",password='C2y6yDjf5' + r'/R' + '+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw' + r'/Jw==')
    ```

* Obtenha a coleção ou crie-a, caso ainda não exista.

    ```python
    todos = db.todo #Select the collection
    ```

* Criar a aplicação

    ```Python
    app = Flask(__name__)
    title = "TODO with Flask"
    heading = "ToDo Reminder"
    ```
    
## <a name="run-the-web-app"></a>Executar a aplicação Web

1. Certifique-se de que o Emulador do Azure Cosmos DB está em execução.

2. Abra uma janela de terminal e `cd` para o diretório onde a aplicação está guardada.

3. Em seguida, desaprote a variável ambiente para a aplicação Flask com `set FLASK_APP=app.py` , `$env:FLASK_APP = app.py` para editores PowerShell, ou `export FLASK_APP=app.py` se estiver a usar um Mac. 

4. Execute a aplicação com `flask run` e navegue para *http: \/ /127.0.0.1:5000/*.

5. Adicione e remova tarefas e veja-as adicionadas e alteradas na coleção.

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

Se quiser testar o código contra uma conta DB Azure Cosmos ao vivo, vá ao portal Azure para criar uma conta.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Para testar o código na conta DB do Azure Cosmos ao vivo, obtenha a informação da sua ligação. Em seguida, copie-as para a aplicação.

1. Na sua conta DB Azure Cosmos no portal Azure, na navegação esquerda selecione **Connection String** e, em seguida, selecione **'Ler-escrever Chaves'.** Utilizará os botões de cópia no lado direito do ecrã para copiar o nome de utilizador, a cadeia de ligação e a palavra-passe. 

2. Abra o ficheiro *app.py* no diretório de raiz.

3. Copie o valor de **nome de utilizador** a partir do portal (com o botão Copiar) e faça deste o valor de **nome** no ficheiro *app.py*.

4. Em seguida, copie o valor da sua cadeia de **ligação** a partir do portal e faça-o o valor do **MongoClient** no seu ficheiro *app.py.*

5. Por fim, copie o valor de **palavra-passe** a partir do portal e faça deste o valor de **palavra-passe** no ficheiro *app.py*.

Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB. Pode executá-la tal como fez anteriormente.

## <a name="deploy-to-azure"></a>Implementar no Azure

Para implementar esta aplicação, pode criar uma nova aplicação web em Azure e ativar a implementação contínua com um garfo deste repo GitHub. Siga este [tutorial](../app-service/deploy-continuous-deployment.md) para configurar a implementação contínua com o GitHub em Azure.

Ao implementar no Azure, deve remover as chaves da aplicação e certificar-se de que a secção abaixo não está comentada:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Em seguida, tem de adicionar o seu MONGOURL, MONGO_PASSWORD e MONGO_USERNAME às definições da aplicação. Pode seguir este [tutorial](../app-service/configure-common.md#configure-app-settings) para saber mais sobre as Definições da Aplicação nas Aplicações Web do Azure.

Se não quiser criar um garfo deste repo, também pode selecionar o botão **Implementar para Azure** abaixo. Em seguida, deverá entrar no Azure e configurar as definições de aplicação com a sua informação de conta DB Azure Cosmos.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> Se planeia armazenar o seu código no GitHub ou noutras opções de controlo de origem, certifique-se de remover as cadeias de ligação do código. Em alternativa, podem ser definidas com as definições da aplicação para a aplicação Web.

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você aprendeu a criar um Azure Cosmos DB para a conta API da Mongo DB, e usar o Emulador Azure Cosmos DB para executar uma aplicação web python Flask To-Do clonada do GitHub. Agora, pode importar dados adicionais para a sua conta do Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Import MongoDB data into Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%252fazure%252fcosmos-db%252ftoc.json%253ftoc%253d%252fazure%252fcosmos-db%252ftoc.json) (Importar dados do MongoDB para o Azure Cosmos DB)