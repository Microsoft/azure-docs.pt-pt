---
title: Criar uma aplicação de web do Flask com a API do Azure Cosmos DB para MongoDB e o SDK de Python
description: Apresenta um exemplo de código Python Flask, que pode utilizar para ligar e consultar com a API do Azure Cosmos DB do MongoDB.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: 35159a1630c7f1268119cd55cc6e0ac99dfd7b5f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58119212"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Início rápido: Criar uma aplicação de Python com a API do Azure Cosmos DB do MongoDB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

O Azure Cosmos DB é um serviço de base de dados com vários modelos e de distribuição global da Microsoft. Pode criar e consultar documentos e bases de dados de gráfico, que beneficiam de capacidades de escalamento horizontal no núcleo do Cosmos DB e distribuição global chave/valor rapidamente.

Este guia de início rápido utiliza as seguintes [exemplo de Flask](https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample) e demonstra como criar uma aplicação Flask de tarefas simples com o [emulador do Azure Cosmos DB](local-emulator.md) e a API do Azure Cosmos DB para o MongoDB.

## <a name="prerequisites"></a>Pré-requisitos

- Transfira o [Emulador do Azure Cosmos DB](local-emulator.md). Atualmente, o emulador é suportado apenas em Windows. O exemplo mostra como utilizar o exemplo com uma chave de produção do Azure, o que pode ser feito em qualquer plataforma.

- Se ainda não tiver o Visual Studio Code instalado, pode instalar rapidamente o [VS Code](https://code.visualstudio.com/Download) para a sua plataforma (Windows, Mac, Linux).

- Certifique-se de que adiciona suporte de Linguagem Python, instalando uma das extensões Python populares.
  1. Selecione uma extensão.
  2. Instale a extensão, escrevendo `ext install` Paleta de Comandos `Ctrl+Shift+P`.

     O exemplo neste documento utiliza a [Extensão Python](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python) popular e com todas as funcionalidades de Don Jayamanne.

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos clonar uma Flask-MongoDB aplicação partir do GitHub, definir a cadeia de ligação e executá-lo. Vai ver como é fácil trabalhar com dados programaticamente.

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

3. Em seguida, defina a variável de ambiente para a aplicação Flask com `set FLASK_APP=app.py` ou `export FLASK_APP=app.py` se estiver a utilizar um Mac.

4. Execute a aplicação com `flask run` e navegue até [http://127.0.0.1:5000/](http://127.0.0.1:5000/).

5. Adicione e remova tarefas e veja-as adicionadas e alteradas na coleção.

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Se pretender testar o código numa conta do Cosmos em direto, aceda ao portal do Azure para criar uma conta e obter as informações de cadeia de ligação. Em seguida, copie-as para a aplicação.

1. Na [portal do Azure](https://portal.azure.com/), na sua conta do Cosmos, no painel de navegação esquerdo clique **cadeia de ligação**e, em seguida, clique em **chaves de leitura-escrita**. Vai utilizar os botões de copiar no lado direito do ecrã para copiar o Nome de Utilizador, a Palavra-passe e o Anfitrião para o ficheiro Dal.cs no próximo passo.

2. Abra o ficheiro **app.py** no diretório de raiz.

3. Copie o valor de **nome de utilizador** a partir do portal (com o botão Copiar) e faça deste o valor de **nome** no ficheiro **app.py**.

4. Em seguida, copie o valor da sua **cadeia de ligação** a partir do portal e faça deste o valor do MongoClient no ficheiro **app.py**.

5. Por fim, copie o valor de **palavra-passe** a partir do portal e faça deste o valor de **palavra-passe** no ficheiro **app.py**.

Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Cosmos DB. Pode executá-la tal como fez anteriormente.

## <a name="deploy-to-azure"></a>Implementar no Azure

Para implementar esta aplicação, pode criar uma nova aplicação web no Azure e ativar a implementação contínua com um fork deste repositório do GitHub. Siga este [tutorial](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment) para configurar a implementação contínua com o GitHub no Azure.

Ao implementar no Azure, deve remover as chaves da aplicação e certificar-se de que a secção abaixo não está comentada:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Em seguida, tem de adicionar o seu MONGOURL, MONGO_PASSWORD e MONGO_USERNAME às definições da aplicação. Pode seguir este [tutorial](https://docs.microsoft.com/azure/app-service-web/web-sites-configure#application-settings) para saber mais sobre as Definições da Aplicação nas Aplicações Web do Azure.

Se não quiser criar um fork deste repositório, também pode clicar no botão Implementar no Azure abaixo. Em seguida, deve entrar no Azure e configurar as definições da aplicação com as informações da sua conta do Cosmos DB.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> Se pretender armazenar o código no GitHub ou outras opções de controlo de origem, não se esqueça de remover as cadeias de ligação do código. Em alternativa, podem ser definidas com as definições da aplicação para a aplicação Web.

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu como criar uma conta do Cosmos e executar uma aplicação Flask. Agora, pode importar dados adicionais à sua base de dados do Cosmos. 

> [!div class="nextstepaction"]
> [Importar dados da MongoDB para o Azure Cosmos DB](mongodb-migrate.md)
