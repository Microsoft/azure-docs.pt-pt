---
title: Construa uma aplicação web Python Flask utilizando a API do Azure Cosmos DB para o MongoDB
description: Apresenta uma amostra de código Python Flask que pode usar para ligar e consultar usando a API do Azure Cosmos DB para MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: 43f4cf7e4008aa01a26c48a8e99f7465eeeb234b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77061748"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Quickstart: Construa uma app Python utilizando a API da Azure Cosmos DB para o MongoDB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Nó.js](create-mongodb-nodejs.md)
> * [Pitão](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Neste arranque rápido, você usa um Azure Cosmos DB para a conta Mongo DB API ou o Emulador Db Azure Cosmos para executar uma aplicação web Python Flask To-Do clonada a partir do GitHub. Azure Cosmos DB é um serviço de base de dados multi-modelo que permite criar e consultar rapidamente documentos, tabelas, basede-chaves e bases de dados de gráficos com capacidades de distribuição global e escala horizontal.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Ou [experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição Azure. Ou, pode usar o [Emulador DB Azure Cosmos.](local-emulator.md) 
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

Os seguintes cortes são todos retirados do ficheiro *app.py* e usam a corda de ligação para o Emulador DB azure cosmos local. A palavra-passe tem de ser dividida, como pode ver baixo, para adaptar-se às barras que não podem ser analisadas de outra forma.

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

3. Em seguida, detete a `set FLASK_APP=app.py`variável ambiental para a aplicação Flask com , `$env:FLASK_APP = app.py` para os editores PowerShell, ou `export FLASK_APP=app.py` se estiver a usar um Mac. 

4. Executar a `flask run` aplicação com e navegar para *http:\//127.0.0.1:5000/*.

5. Adicione e remova tarefas e veja-as adicionadas e alteradas na coleção.

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

Se quiser testar o código contra uma conta Azure Cosmos DB ao vivo, aceda ao portal Azure para criar uma conta.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Para testar o código contra a conta LIVE Azure Cosmos DB, obtenha a sua informação de cadeia de ligação. Em seguida, copie-as para a aplicação.

1. Na sua conta Azure Cosmos DB no portal Azure, na linha de navegação à esquerda selecione **Connection String**, e, em seguida, selecione **Teclas de leitura de escrita**. Utilizará os botões de cópia do lado direito do ecrã para copiar o nome de utilizador, o fio de ligação e a palavra-passe. 

2. Abra o ficheiro *app.py* no diretório de raiz.

3. Copie o valor de **nome de utilizador** a partir do portal (com o botão Copiar) e faça deste o valor de **nome** no ficheiro *app.py*.

4. Em seguida, copie o valor de cadeia de **ligação** do portal e faça dele o valor do **MongoClient** no seu ficheiro *app.py.*

5. Por fim, copie o valor de **palavra-passe** a partir do portal e faça deste o valor de **palavra-passe** no ficheiro *app.py*.

Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB. Pode executá-la tal como fez anteriormente.

## <a name="deploy-to-azure"></a>Implementar no Azure

Para implementar esta aplicação, pode criar uma nova aplicação web no Azure e permitir a implementação contínua com uma bifurcação deste repo GitHub. Siga este [tutorial](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment) para configurar uma implantação contínua com o GitHub em Azure.

Ao implementar no Azure, deve remover as chaves da aplicação e certificar-se de que a secção abaixo não está comentada:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Em seguida, tem de adicionar o seu MONGOURL, MONGO_PASSWORD e MONGO_USERNAME às definições da aplicação. Pode seguir este [tutorial](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings) para saber mais sobre as Definições da Aplicação nas Aplicações Web do Azure.

Se não quiser criar uma bifurcação deste repo, também pode selecionar o botão **Deploy para Azure** abaixo. Em seguida, deverá entrar no Azure e configurar as definições de aplicação com as informações da sua conta Azure Cosmos DB.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> Se planeia armazenar o seu código no GitHub ou noutras opções de controlo de fonte, certifique-se de remover as cordas de ligação do código. Em alternativa, podem ser definidas com as definições da aplicação para a aplicação Web.

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar um Azure Cosmos DB para a conta Mongo DB API, e usou o Emulador Db Azure Cosmos para executar uma aplicação web Python Flask To-Do clonada do GitHub. Agora, pode importar dados adicionais para a sua conta do Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Import MongoDB data into Azure Cosmos DB](mongodb-migrate.md) (Importar dados do MongoDB para o Azure Cosmos DB)
