---
title: Compilar um aplicativo Web Python Flask usando a API do Azure Cosmos DB para MongoDB
description: Apresenta um exemplo de código do Python Flask que você pode usar para se conectar e consultar usando a API do Azure Cosmos DB para MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: 8e58d0bdaaa5e4fb4564a68b46de7887ec28336d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445479"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Início rápido: compilar um aplicativo Python usando a API do Azure Cosmos DB para MongoDB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente. Você pode criar e consultar rapidamente documentos, chave/valor e bancos de dados de grafo, todos beneficiando-se dos recursos de escala horizontal e distribuição global no núcleo de Cosmos DB.

Este guia de início rápido usa o [exemplo de Flask](https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample) a seguir e demonstra como criar um aplicativo de Flask de tarefa simples com o [emulador de Azure Cosmos DB](local-emulator.md) e a API do Azure Cosmos DB para MongoDB.

## <a name="prerequisites"></a>Pré-requisitos

- Transfira o [Emulador do Azure Cosmos DB](local-emulator.md). Atualmente, o emulador é suportado apenas em Windows. O exemplo mostra como utilizar o exemplo com uma chave de produção do Azure, o que pode ser feito em qualquer plataforma.

- Se ainda não tiver o Visual Studio Code instalado, pode instalar rapidamente o [VS Code](https://code.visualstudio.com/Download) para a sua plataforma (Windows, Mac, Linux).

- Certifique-se de que adiciona suporte de Linguagem Python, instalando uma das extensões Python populares.
  1. Selecione uma extensão.
  2. Instale a extensão, escrevendo `ext install` Paleta de Comandos `Ctrl+Shift+P`.

     O exemplo neste documento utiliza a [Extensão Python](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python) popular e com todas as funcionalidades de Don Jayamanne.

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos clonar um aplicativo Flask-MongoDB do GitHub, definir a cadeia de conexão e executá-lo. Vai ver como é fácil trabalhar com dados programaticamente.

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

Os seguintes fragmentos são obtidos do ficheiro app.py e utilizam a cadeia de ligação para o Emulador local do Azure Cosmos DB. A palavra-passe tem de ser dividida, como pode ver baixo, para adaptar-se às barras que não podem ser analisadas de outra forma.

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

3. Em seguida, defina a variável de ambiente para o aplicativo Flask com `set FLASK_APP=app.py`, `$env:FLASK_APP = app.py` para editores do PowerShell ou `export FLASK_APP=app.py` se você estiver usando um Mac. 

4. Execute a aplicação com `flask run` e navegue até [http://127.0.0.1:5000/](http://127.0.0.1:5000/).

5. Adicione e remova tarefas e veja-as adicionadas e alteradas na coleção.

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Se você quiser testar o código em uma conta Cosmos ao vivo, vá para a portal do Azure para criar uma conta e obter as informações da cadeia de conexão. Em seguida, copie-as para a aplicação.

1. Na [portal do Azure](https://portal.azure.com/), em sua conta do cosmos, no painel de navegação esquerdo, clique em **cadeia de conexão**e clique em **chaves de leitura/gravação**. Vai utilizar os botões de copiar no lado direito do ecrã para copiar o Nome de Utilizador, a Palavra-passe e o Anfitrião para o ficheiro Dal.cs no próximo passo.

2. Abra o ficheiro **app.py** no diretório de raiz.

3. Copie o valor de **nome de utilizador** a partir do portal (com o botão Copiar) e faça deste o valor de **nome** no ficheiro **app.py**.

4. Em seguida, copie o valor da sua **cadeia de ligação** a partir do portal e faça deste o valor do MongoClient no ficheiro **app.py**.

5. Por fim, copie o valor de **palavra-passe** a partir do portal e faça deste o valor de **palavra-passe** no ficheiro **app.py**.

Agora você atualizou seu aplicativo com todas as informações de que ele precisa para se comunicar com Cosmos DB. Pode executá-la tal como fez anteriormente.

## <a name="deploy-to-azure"></a>Implementar no Azure

Para implantar esse aplicativo, você pode criar um novo aplicativo Web no Azure e habilitar a implantação contínua com uma bifurcação deste repositório GitHub. Siga este [tutorial](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment) para configurar a implantação contínua com o GitHub no Azure.

Ao implementar no Azure, deve remover as chaves da aplicação e certificar-se de que a secção abaixo não está comentada:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Em seguida, tem de adicionar o seu MONGOURL, MONGO_PASSWORD e MONGO_USERNAME às definições da aplicação. Pode seguir este [tutorial](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings) para saber mais sobre as Definições da Aplicação nas Aplicações Web do Azure.

Se não quiser criar um fork deste repositório, também pode clicar no botão Implementar no Azure abaixo. Em seguida, deve entrar no Azure e configurar as definições da aplicação com as informações da sua conta do Cosmos DB.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> Se você planeja armazenar seu código no GitHub ou outras opções de controle do código-fonte, certifique-se de remover as cadeias de conexão do código. Em alternativa, podem ser definidas com as definições da aplicação para a aplicação Web.

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu como criar uma conta do cosmos e executar um aplicativo Flask. Agora você pode importar dados adicionais para o banco de Cosmos. 

> [!div class="nextstepaction"]
> [Import MongoDB data into Azure Cosmos DB](mongodb-migrate.md) (Importar dados do MongoDB para o Azure Cosmos DB)
