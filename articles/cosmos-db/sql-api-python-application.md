---
title: Tutorial de aplicação web Python Flask para o Azure Cosmos DB
description: Reveja um tutorial de base de dados sobre a utilização do Azure Cosmos DB para armazenar e aceder a dados a partir de uma aplicação Web Python Flask alojada no Azure. Encontre soluções de desenvolvimento de aplicações.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: tutorial
ms.date: 02/23/2017
ms.author: sngun
ms.openlocfilehash: 2830772dd69ac94c2b8373936665b9445f9f5f10
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57431211"
---
# <a name="build-a-python-flask-web-application-using-azure-cosmos-db"></a>Criar uma aplicação Web Python Flask com o Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Este tutorial mostra-lhe como utilizar o Azure Cosmos DB para armazenar e aceder a dados a partir de uma aplicação Web Python Flask alojada no Serviço de Aplicações do Azure. Este tutorial parte do pressuposto de que tem alguma experiência anterior na utilização de Python e de sites do Azure.

Este tutorial de base de dados abrange:

1. A criação e o aprovisionamento de uma conta do Azure Cosmos DB.
2. A criação de uma aplicação Python Flask.
3. A ligação e a utilização do Azure Cosmos DB a partir da sua aplicação Web.
4. A implementação da aplicação Web no Serviço de Aplicações do Azure.

Ao seguir este tutorial, irá criar uma aplicação de voto simples que lhe permite votar num inquérito.

![Captura de ecrã da aplicação de voto criada por este tutorial de base de dados](./media/sql-api-python-application/cosmos-db-pythonr-run-application.png)

## <a name="database-tutorial-prerequisites"></a>Pré-requisitos do tutorial da base de dados
Antes de seguir as instruções deste artigo, deve certificar-se de que os seguintes elementos estão instalados:

* [Uma subscrição do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Visual Studio 2017](https://www.visualstudio.com/downloads/) com **desenvolvimento do Azure** e **desenvolvimento de Python** ativados. Pode verificar se estes pré-requisitos estão instalados e, se não, instalá-los ao abrir o **Instalador do Visual Studio** localmente.   
* [SDK do Microsoft Azure para Python 2.7](https://azure.microsoft.com/downloads/). 
* [Python 2.7](https://www.python.org/downloads/windows/). Pode utilizar a instalação de 32 bits ou de 64 bits.

> [!IMPORTANT]
> Se estiver a instalar o Python 2.7 pela primeira vez, certifique-se de que selecionou **Add python.exe to Path** (Adicionar python.exe ao Caminho) no ecrã Personalizar Python 2.7.13.
> 
> ![Captura de ecrã do ecrã Personalizar Python 2.7.11, onde tem de selecionar adicionar python.exe ao caminho](./media/sql-api-python-application/cosmos-db-python-install.png)
> 
> 

* [Microsoft Visual C++ Compiler para Python 2.7](https://www.microsoft.com/en-us/download/details.aspx?id=44266).

## <a name="step-1-create-an-azure-cosmos-db-database-account"></a>Passo 1: Criar uma conta de base de dados do Azure Cosmos DB
Comecemos por criar uma conta do Azure Cosmos DB. Se já tiver uma conta ou se estiver a utilizar o emulador do Azure Cosmos DB para este tutorial, pode avançar para [passo 2: Criar um novo aplicativo de web do Python Flask](#step-2-create-a-new-python-flask-web-application).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<br/>
Agora, vamos guiá-lo pela criação de uma nova aplicação Web Python Flask a partir do zero.

## <a name="step-2-create-a-new-python-flask-web-application"></a>Passo 2: Criar um novo aplicativo de web do Python Flask
1. No Visual Studio, no menu **Ficheiro**, aponte para **Novo** e, em seguida, clique em **Projeto**.
   
    Aparece a caixa de diálogo **Novo Projeto**.
2. No painel da esquerda, expanda **Modelos** e **Python**, e, em seguida, clique em **Web**. 
3. Selecione **Flask Web Project** no painel central e, na caixa **Nome**, introduza **Tutorial**, e, em seguida, clique em **OK**. Lembre-se de que os nomes de pacote Python devem ser todos escritos em minúsculas, conforme descrito no [Guia de Estilo para Código Python](https://www.python.org/dev/peps/pep-0008/#package-and-module-names).
   
    Para aqueles que dão os primeiros passos com o Python Flask, trata-se de uma estrutura de desenvolvimento de aplicações Web que o ajuda a criar rapidamente aplicações Web no Python.
   
    ![Captura de ecrã da janela novo projeto no Visual Studio com o Python realçado à esquerda, projeto da Web Python Flask selecionado no meio e o tutorial de nome na caixa Nome](./media/sql-api-python-application/image9.png)
4. Na janela **Ferramentas do Python para o Visual Studio**, clique em **Instalar num ambiente virtual**. 
   
    ![Captura de ecrã do tutorial da base de dados - ferramentas do Python para a janela do Visual Studio](./media/sql-api-python-application/python-install-virtual-environment.png)
5. Na janela **Adicionar Ambiente Virtual**, selecione Python 2.7 ou Python 3.5 na caixa “Selecionar um interpretador”, aceite as outras predefinições e clique em **Criar**. Esta ação configura o ambiente virtual do Python necessário para o seu projeto.
   
    ![Captura de ecrã do tutorial da base de dados - ferramentas do Python para a janela do Visual Studio](./media/sql-api-python-application/image10_A.png)
   
    A janela de saída apresenta `Successfully installed Flask-0.10.1 Jinja2-2.8 MarkupSafe-0.23 Werkzeug-0.11.5 itsdangerous-0.24 'requirements.txt' was installed successfully.` quando o ambiente tiver sido instalado com êxito.

## <a name="step-3-modify-the-python-flask-web-application"></a>Passo 3: Modificar o aplicativo da web Python Flask
### <a name="add-the-python-flask-packages-to-your-project"></a>Adicione pacotes do Python Flask ao seu projeto
Depois de configurar o seu projeto, terá de adicionar os pacotes Flask necessários ao seu projeto, incluindo o pydocumentdb, o pacote Python para a API para SQL do Azure Cosmos DB.

1. No Explorador de Soluções, abra o ficheiro denominado **requirements.txt** e substitua o conteúdo pelo seguinte:
   
        flask==0.9
        flask-mail==0.7.6
        sqlalchemy==0.7.9
        flask-sqlalchemy==0.16
        sqlalchemy-migrate==0.7.2
        flask-whooshalchemy==0.55a
        flask-wtf==0.8.4
        pytz==2013b
        flask-babel==0.8
        flup
        pydocumentdb>=1.0.0
2. Guarde o ficheiro **requirements.txt**. 
3. No Explorador de Soluções, clique com o botão direito do rato em **env** e clique em **Instalar a partir do requirements.txt**.
   
    ![Captura de ecrã que ilustra o env (Python 2.7) selecionado com instalar a partir do Requirements. txt realçado na lista](./media/sql-api-python-application/cosmos-db-python-install-from-requirements.png)
   
    Após a instalação com êxito, a janela de saída apresenta o seguinte:
   
        Successfully installed Babel-2.3.2 Tempita-0.5.2 WTForms-2.1 Whoosh-2.7.4 blinker-1.4 decorator-4.0.9 flask-0.9 flask-babel-0.8 flask-mail-0.7.6 flask-sqlalchemy-0.16 flask-whooshalchemy-0.55a0 flask-wtf-0.8.4 flup-1.0.2 pydocumentdb-1.6.1 pytz-2013b0 speaklater-1.3 sqlalchemy-0.7.9 sqlalchemy-migrate-0.7.2
   
   > [!NOTE]
   > Em casos raros, poderá ver uma falha na janela de saída. Se isto acontecer, verifique se o erro está relacionado com a limpeza. Por vezes, a limpeza falha, mas a instalação ainda será concluída com êxito (desloque-se para cima na janela da saída para confirmar). Pode verificar a sua instalação ao [Verificar o ambiente virtual](#verify-the-virtual-environment). Se a instalação falhou, mas a verificação for bem sucedida, poderá prosseguir.
   > 
   > 

### <a name="verify-the-virtual-environment"></a>Verificar o ambiente virtual
Certifiquemo-nos de que tudo foi corretamente instalado.

1. Compile a solução ao premir **Ctrl**+**Shift**+**B**.
2. Assim que a compilação for concluída com êxito, inicie o Web site premindo **F5**. Esta ação irá abrir o servir de desenvolvimento do Flask e iniciar o seu browser. Deverá ver a seguinte página:
   
    ![O projeto Web vazio de desenvolvimento do Python Flask apresentado num browser](./media/sql-api-python-application/image12.png)
3. Interrompa a depuração do web site premindo **Shift**+**F5** no Visual Studio.

### <a name="create-database-collection-and-document-definitions"></a>Criar uma base de dados, uma coleção e definições do documento
Criemos agora a sua aplicação de voto adicionando novos ficheiros e atualizando outros.

1. No Explorador de Soluções, clique com o botão direito do rato em projeto **tutorial**, clique em **Adicionar** e, em seguida, clique em **Novo Item**. Selecione **Ficheiro Vazio do Python** e atribua o nome **forms.py** ao ficheiro.  
2. Copie o seguinte código para o ficheiro forms.psy e, em seguida, guarde o ficheiro.

```python
from flask_wtf import Form
from wtforms import RadioField

class VoteForm(Form):
    deploy_preference  = RadioField('Deployment Preference', choices=[
        ('Web Site', 'Web Site'),
        ('Cloud Service', 'Cloud Service'),
        ('Virtual Machine', 'Virtual Machine')], default='Web Site')
```


### <a name="add-the-required-imports-to-viewspy"></a>Adicionar as importações requeridas ao views.psy
1. No Explorador de soluções, expanda a pasta **tutorial** e abra o ficheiro **views.py**. 
2. Adicione as seguintes instruções de importação à parte superior do ficheiro **views.py** e, em seguida, guarde o ficheiro. Estas ações importam os pacotes PythonSDK e Flask do Azure Cosmos DB.
   
    ```python
    from forms import VoteForm
    import config_cosmos
    import pydocumentdb.document_client as document_client
    ```

### <a name="create-database-collection-and-document"></a>Criar uma base de dados, uma coleção e um documento
* Ainda no **views.py**, adicione o seguinte código no fim do ficheiro. Tal trata da criação da base de dados utilizada pelo formulário. Não elimine nenhum código existente no **views.py**. Adicione apenas o código no fim.

```python
@app.route('/create')
def create():
    """Renders the contact page."""
    client = document_client.DocumentClient(config_cosmos.COSMOSDB_HOST, {'masterKey': config_cosmos.COSMOSDB_KEY})

    # Attempt to delete the database.  This allows this to be used to recreate as well as create
    try:
        db = next((data for data in client.ReadDatabases() if data['id'] == config_cosmos.COSMOSDB_DATABASE))
        client.DeleteDatabase(db['_self'])
    except:
        pass

    # Create database
    db = client.CreateDatabase({ 'id': config_cosmos.COSMOSDB_DATABASE })

    # Create collection
    collection = client.CreateCollection(db['_self'],{ 'id': config_cosmos.COSMOSDB_COLLECTION })

    # Create document
    document = client.CreateDocument(collection['_self'],
        { 'id': config_cosmos.COSMOSDB_DOCUMENT,
          'Web Site': 0,
          'Cloud Service': 0,
          'Virtual Machine': 0,
          'name': config_cosmos.COSMOSDB_DOCUMENT 
        })

    return render_template(
       'create.html',
        title='Create Page',
        year=datetime.now().year,
        message='You just created a new database, collection, and document.  Your old votes have been deleted')
```


### <a name="read-database-collection-document-and-submit-form"></a>Ler a base de dados, coleção, documentos e submeter o formulário
* Ainda no **views.py**, adicione o seguinte código no fim do ficheiro. Esta ação trata da configuração do formulário, da leitura da base de dados, da coleção e do documento. Não elimine nenhum código existente no **views.py**. Adicione apenas o código no fim.

```python
@app.route('/vote', methods=['GET', 'POST'])
def vote(): 
    form = VoteForm()
    replaced_document ={}
    if form.validate_on_submit(): # is user submitted vote  
        client = document_client.DocumentClient(config_cosmos.COSMOSDB_HOST, {'masterKey': config_cosmos.COSMOSDB_KEY})

        # Read databases and take first since id should not be duplicated.
        db = next((data for data in client.ReadDatabases() if data['id'] == config_cosmos.COSMOSDB_DATABASE))

        # Read collections and take first since id should not be duplicated.
        coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config_cosmos.COSMOSDB_COLLECTION))

        # Read documents and take first since id should not be duplicated.
        doc = next((doc for doc in client.ReadDocuments(coll['_self']) if doc['id'] == config_cosmos.COSMOSDB_DOCUMENT))

        # Take the data from the deploy_preference and increment our database
        doc[form.deploy_preference.data] = doc[form.deploy_preference.data] + 1
        replaced_document = client.ReplaceDocument(doc['_self'], doc)

        # Create a model to pass to results.html
        class VoteObject:
            choices = dict()
            total_votes = 0

        vote_object = VoteObject()
        vote_object.choices = {
            "Web Site" : doc['Web Site'],
            "Cloud Service" : doc['Cloud Service'],
            "Virtual Machine" : doc['Virtual Machine']
        }
        vote_object.total_votes = sum(vote_object.choices.values())

        return render_template(
            'results.html', 
            year=datetime.now().year, 
            vote_object = vote_object)

    else :
        return render_template(
            'vote.html', 
            title = 'Vote',
            year=datetime.now().year,
            form = form)
```


### <a name="create-the-html-files"></a>Criar os ficheiros HTML
1. No Explorador de Soluções, na pasta **tutorial**, clique com o botão direito do rato na pasta **modelos**, clique em **Adicionar** e, em seguida, clique em **Novo Item**. 
2. Selecione **Página HTML** e, em seguida, na caixa de nome, introduza **create.html**. 
3. Repita os passos 1 e 2 para criar dois ficheiros adicionais de HTML: results.html e vote.html.
4. Adicione o seguinte código em **create.html** no `<body>` elemento. É apresentada uma mensagem com a informação de que foi criada uma nova base de dados, coleção e documento.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}
    ```
5. Adicione o seguinte código ao **results.html** no `<body`> elemento. Apresenta os resultados do inquérito.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>Results of the vote</h2>
        <br />
   
    {% for choice in vote_object.choices %}
    <div class="row">
        <div class="col-sm-5">{{choice}}</div>
            <div class="col-sm-5">
                <div class="progress">
                    <div class="progress-bar" role="progressbar" aria-valuenow="{{vote_object.choices[choice]}}" aria-valuemin="0" aria-valuemax="{{vote_object.total_votes}}" style="width: {{(vote_object.choices[choice]/vote_object.total_votes)*100}}%;">
                                {{vote_object.choices[choice]}}
                </div>
            </div>
            </div>
    </div>
    {% endfor %}
   
    <br />
    <a class="btn btn-primary" href="{{ url_for('vote') }}">Vote again?</a>
    {% endblock %}
    ```
6. Adicione o seguinte código ao **vote.html** no `<body`> elemento. Apresenta o inquérito e aceita os votos. Quando os votos são registados, o controlo é transmitido ao views.py, onde o Azure Cosmos DB reconhece o voto depositado e acrescenta o documento em conformidade.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>What is your favorite way to host an application on Azure?</h2>
    <form action="" method="post" name="vote">
        {{form.hidden_tag()}}
            {{form.deploy_preference}}
            <button class="btn btn-primary" type="submit">Vote</button>
    </form>
    {% endblock %}
    ```
7. Na pasta **modelos**, substitua o conteúdo **index.html** pelo seguinte. Esta página irá servir de página de destino para a sua aplicação.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>Python + Azure Cosmos DB Voting Application.</h2>
    <h3>This is a sample Cosmos DB voting application using PyDocumentDB</h3>
    <p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}
    ```

### <a name="add-a-configuration-file-and-change-the-initpy"></a>Adicionar um ficheiro de configuração e alterar o \_\_init\_\_.py
1. No Explorador de Soluções, clique com o botão direito do rato no projeto **tutorial**, clique em **Adicionar**, clique em **Novo Item**, selecione **Ficheiro Vazio do Python** e, em seguida, dê o nome **config_cosmos.py** ao ficheiro. Este ficheiro de configuração é necessário para formulários no Flask. Pode ainda utilizá-lo para fornecer uma chave secreta. No entanto, essa chave não é necessária para este tutorial.
2. Adicione o seguinte código ao config_cosmos.py. No próximo passo, vai ter de alterar os valores de **COSMOSDB\_HOST** e **COSMOSDB\_KEY**.
   
    ```python
    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
   
    COSMOSDB_HOST = 'https://YOUR_COSMOSDB_NAME.documents.azure.com:443/'
    COSMOSDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='
   
    COSMOSDB_DATABASE = 'voting database'
    COSMOSDB_COLLECTION = 'voting collection'
    COSMOSDB_DOCUMENT = 'voting document'
    ```
3. No [portal do Azure](https://portal.azure.com/), navegue para a página **Chaves** ao clicar em **Procurar**, **Contas do Azure Cosmos DB**, faça duplo clique no nome da conta que vai ser utilizada e, em seguida, clique no botão **Chaves**, na área **Essenciais**. Na página **Chaves**, copie o valor de **URI** e cole-o no ficheiro **config.py** como o valor da propriedade **COSMOSDB\_HOST**. 
4. De regresso ao portal do Azure, na página **Chaves**, copie o valor da **Chave Primária** ou da **Chave Secundária** e cole-o no ficheiro **config_cosmos.py** como o valor da propriedade **COSMOSDB\_KEY**.
5. No ficheiro **\_\_init\_\_.py**, adicione as seguintes linhas para incluir a leitura do ficheiro de configuração e alguns registos básicos: 
   
        app.config.from_object('config_cosmos')
        logging.basicConfig(level=logging.INFO,format='%(asctime)s - %(name)s - %(levelname)s - %(message)s')
        logger = logging.getLogger(__name__)
   
    Para que o conteúdo do ficheiro seja:
   
    ```python
    import logging
    from flask import Flask
    app = Flask(__name__)
    app.config.from_pyfile('config_cosmos')
    logging.basicConfig(level=logging.INFO,format='%(asctime)s - %(name)s - %(levelname)s - %(message)s')
    logger = logging.getLogger(__name__)

    import tutorial.views
    ```
6. Depois de adicionar todos os ficheiros, o Explorador de Soluções deverá ter o seguinte aspeto:
   
    ![Captura de ecrã da janela do Explorador de soluções do Visual Studio](./media/sql-api-python-application/cosmos-db-python-solution-explorer.png)

## <a name="step-4-run-your-web-application-locally"></a>Passo 4: Executar a sua aplicação localmente
1. Compile a solução ao premir **Ctrl**+**Shift**+**B**.
2. Assim que a compilação for concluída com êxito, inicie o Web site premindo **F5**. No seu ecrã, deverá ver o seguinte.
   
    ![Captura de ecrã do Python + aplicação do Azure Cosmos DB votação apresentada num browser](./media/sql-api-python-application/cosmos-db-pythonr-run-application.png)
3. Clique em **Criar/Limpar a Base de dados de Voto** para gerar a base de dados.
   
    ![Captura de ecrã da página Criar da aplicação web – detalhes de desenvolvimento](./media/sql-api-python-application/cosmos-db-python-run-create-page.png)
4. Em seguida, clique em **Votar** e selecione a sua opção.
   
    ![Captura de ecrã da aplicação web com uma pergunta de voto colocada](./media/sql-api-python-application/cosmos-db-vote.png)
5. Cada voto que transmitir será contado no contador adequado.
   
    ![Captura de ecrã dos resultados da página de voto exibida](./media/sql-api-python-application/cosmos-db-voting-results.png)
6. Interrompa a depuração do projeto premindo Shift+F5.

## <a name="step-5-deploy-the-web-application-to-azure"></a>Passo 5: Implementar a aplicação web no Azure
Agora que já tem a aplicação completa a funcionar corretamente no Azure Cosmos DB localmente, vamos criar um ficheiro web.config, atualizar os ficheiros no servidor para que correspondam ao ambiente local e, depois, ver a aplicação concluída no Azure. Este procedimento é específico para o Visual Studio 2017. Se estiver a utilizar uma versão diferente do Visual Studio, veja [Publishing to Azure App Service](/visualstudio/python/publishing-to-azure) (Publicar no Serviço de Aplicações do Azure).

1. No **Explorador de Soluções** do Visual Studio, clique com o botão direito do rato no projeto e selecione **Adicionar > Novo Item...**. Na caixa de diálogo que aparece, selecione o modelo **Azure web.config (Fast CGI)** e selecione **OK**. É criado um ficheiro `web.config` na raiz do projeto. 

2. Modifique a secção `<system.webServer>` em `web.config`, de modo a que o caminho corresponda à instalação de Python. Por exemplo, para Python 2.7 x64, a entrada deverá aparecer da seguinte forma:
    
    ```xml
    <system.webServer>
        <handlers>
            <add name="PythonHandler" path="*" verb="*" modules="FastCgiModule" scriptProcessor="D:\home\Python27\python.exe|D:\home\Python27\wfastcgi.py" resourceType="Unspecified" requireAccess="Script"/>
        </handlers>
    </system.webServer>
    ```

3. Defina a entrada `WSGI_HANDLER` em `web.config` como `tutorial.app`, de modo a que corresponda ao nome do seu projeto. 

    ```xml
    <!-- Flask apps only: change the project name to match your app -->
    <add key="WSGI_HANDLER" value="tutorial.app"/>
    ```

4. No **Explorador de Soluções** do Visual Studio, expanda a pasta **tutorial**, clique com o botão direito do rato na pasta `static`, selecione **Adicionar > Novo Item...**, selecione o modelo "Azure static files web.config" e selecione **OK**. Esta ação cria outro ficheiro `web.config` na pasta `static` que desativa o processamento de Python na mesma. Esta configuração envia pedidos para ficheiros estáticos ao servidor Web predefinido em vez de utilizar a aplicação Python.

5. Guarde os ficheiros e clique com o botão direito do rato no Explorador de Soluções (confirme que já não está a executá-lo localmente) e selecione **Publicar**.  
   
     ![Captura de ecrã do tutorial selecionado no Explorador de soluções, com a opção publicar realçada](./media/sql-api-python-application/image20.png)
6. Na caixa de diálogo **Publicar**, selecione **Serviço de Aplicações do Microsoft Azure**, selecione **Criar nova** e clique em **Publicar**.
   
    ![Captura de ecrã da janela Publicar na Web com o serviço de aplicações do Microsoft Azure realçadas](./media/sql-api-python-application/cosmos-db-python-publish.png)
7. Na caixa de diálogo **Criar Serviço de Aplicações**, introduza o nome da sua aplicação Web e a **Subscrição**, o **Grupo de Recursos** e o **Plano do Serviço de Aplicações** e clique em **Criar**.
   
    ![Captura de ecrã da janela da janela de aplicações Web do Microsoft Azure](./media/sql-api-python-application/cosmos-db-python-create-app-service.png)
8. Em alguns segundos, o Visual Studio termina a cópia dos ficheiros para o servidor e apresenta “Não é possível apresentar a página porque ocorreu um erro de servidor interno” na página `http://<your app service>.azurewebsites.net/`.

9. No portal do Azure, abra a sua conta nova do Serviço de Aplicações e, no menu de navegação, desloque-se para baixo para a secção **Ferramentas de programação**, selecione **Extensões** e clique em **+ Adicionar**.

10. Na página **Escolher extensão**, desloque-se para baixo para a instalação mais recente de Python 2.7, selecione a opção de x86 ou x64 bits e clique em **OK** para aceitar os termos legais.  
   
11. Utilize a consola do Kudu, à qual pode aceder em `https://<your app service name>.scm.azurewebsites.net/DebugConsole`, para instalar os pacotes indicados no ficheiro `requirements.txt` da aplicação. Para tal, na consola de diagnósticos do Kudu, navegue para a pasta de Python, `D:\home\Python27`, e execute o seguinte comando, conforme descrito na secção [Consola do Kudu](/visualstudio/python/managing-python-on-azure-app-service#azure-app-service-kudu-console):

    ```
    D:\home\Python27>python -m pip install --upgrade -r /home/site/wwwroot/requirements.txt
    ```          

12. Prima o botão **Reiniciar** para reiniciar o Serviço de Aplicações no portal do Azure depois de instalar os pacotes novos. 

    > [!Tip] 
    > Se fizer alterações ao ficheiro `requirements.txt` da aplicação, certifique-se de que volta a utilizar a consola do Kudu para instalar eventuais pacotes que não estejam indicados nesse ficheiro. 

13. Depois de configurar completamente o ambiente do servidor, atualize a página no browser e a aplicação Web deverá aparecer.

    ![Resultados da publicação de aplicações Bottle, Flask e Django no Serviço de Aplicações](./media/sql-api-python-application/python-published-app-services.png)

    > [!Tip] 
    > Se a página Web não for apresentada ou continuar a receber a mensagem “Não foi possível apresentar a página porque ocorreu um erro de servidor interno”, abra o ficheiro web.config no Kudo, adicione ` <httpErrors errorMode="Detailed"></httpErrors>` à secção system.webServer e atualize a página. É enviada uma saída detalhada do erro para o browser. 

## <a name="troubleshooting"></a>Resolução de problemas
Se esta for a primeira aplicação de Python que executar no seu computador, certifique-se de que as seguintes pastas (ou as localizações de instalação equivalentes) estão incluídas na sua variável de CAMINHO:

    C:\Python27\site-packages;C:\Python27\;C:\Python27\Scripts;

Se receber um erro na sua página de voto e se atribuiu outro nome que não **tutorial** ao seu projeto, certifique-se de que o **\_\_init\_\_.py** referencia o nome do projeto correto na linha: `import tutorial.view`.

## <a name="next-steps"></a>Passos Seguintes
Parabéns! Concluiu a sua primeira aplicação Web Python com o Azure Cosmos DB e publicou-a no Azure.

Para adicionar mais funcionalidades à sua aplicação Web, reveja as APIs disponíveis em [Azure Cosmos DB Python SDK](sql-api-sdk-python.md) (SDK de Python para o Azure Cosmos DB).

Para obter mais informações sobre o Azure, o Visual Studio e o Python, consulte o artigo [Centro de Programadores do Python](https://azure.microsoft.com/develop/python/). 

Para tutoriais adicionais do Python Flask, consulte [o Mega Tutorial Flask, parte i: Olá, mundo! ](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world). 
