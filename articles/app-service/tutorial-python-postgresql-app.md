---
title: 'Tutorial: Implementar uma app Python Django com Postgres'
description: Crie uma aplicação web Python com uma base de dados PostgreSQL e implemente-a para a Azure. O tutorial utiliza a estrutura do Django e a aplicação está hospedada no Azure App Service no Linux.
ms.devlang: python
ms.topic: tutorial
ms.date: 02/02/2021
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
- devx-track-python
- devx-track-azurecli
ms.openlocfilehash: 882a9fb0f8d528ca21cdc8149c60b9d5bdaf1723
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767100"
---
# <a name="tutorial-deploy-a-django-web-app-with-postgresql-in-azure-app-service"></a>Tutorial: Implementar uma aplicação web Django com PostgreSQL no Azure App Service

Este tutorial mostra como implementar uma aplicação web Python [Django](https://www.djangoproject.com/) orientada por dados para o [Azure App Service](overview.md) e conectá-la a uma base de dados Azure para postgres. O App Service fornece um serviço de hospedagem web altamente escalável e auto-remendado.

Neste tutorial, você usa o CLI Azure para completar as seguintes tarefas:

> [!div class="checklist"]
> * Crie o seu ambiente inicial com python e o CLI Azure
> * Criar uma base de dados Azure para base de dados PostgreSQL
> * Implementar código para o Serviço de Aplicações Azure e ligar-se ao PostgreSQL
> * Atualize o seu código e reimplante
> * Ver registos de diagnóstico
> * Gerir a aplicação web no portal Azure

Também pode utilizar a [versão do portal Azure deste tutorial.](/azure/developer/python/tutorial-python-postgresql-app-portal)


## <a name="1-set-up-your-initial-environment"></a>1. Crie o seu ambiente inicial

1. Tenha uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
1. Instale <a href="https://www.python.org/downloads/" target="_blank">python 3.6 ou superior</a>.
1. Instale o <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.18.0 ou superior, com o qual execute comandos em qualquer concha para provisões e configurar recursos Azure.

Abra uma janela de terminal e verifique se a sua versão Python é 3.6 ou superior:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Verifique se a sua versão Azure CLI é 2.18.0 ou superior:

```azurecli
az --version
```

Se precisar de atualizar, experimente o `az upgrade` comando (requer a versão 2.11+) ou consulte <a href="/cli/azure/install-azure-cli" target="_blank">instalar o Azure CLI</a>.

Em seguida, inscreva-se em Azure através do CLI:

```azurecli
az login
```

Este comando abre um browser para recolher as suas credenciais. Quando o comando termina, mostra a saída JSON contendo informações sobre as suas subscrições.

Uma vez assinado, pode executar comandos Azure com o CLI Azure para trabalhar com recursos na sua subscrição.

Tendo problemas? [Deixe-nos saber.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="2-clone-or-download-the-sample-app"></a>2. Clone ou descarregue o aplicativo de amostra

# <a name="git-clone"></a>[Clone de Git](#tab/clone)

Clone o repositório de amostras:

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

Em seguida, navegue nessa pasta:

```terminal
cd djangoapp
```

# <a name="download"></a>[Transferência](#tab/download)

Visite [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp) , selecione **Clone** e, em seguida, selecione **Download ZIP**. 

Desembale o ficheiro ZIP numa pasta chamada *djangoapp*. 

Em seguida, abra uma janela terminal naquela pasta *de djangoapp.*

---

A amostra do Djangoapp contém a aplicação de sondagens Django orientada por dados que obtém depois de [escrever a sua primeira aplicação Django](https://docs.djangoproject.com/en/3.1/intro/tutorial01/) na documentação do Django. A aplicação completa é fornecida aqui para sua conveniência.

A amostra também é modificada para funcionar num ambiente de produção como o App Service:

- As definições de produção estão no ficheiro *azuresite/produção.py.* As definições de desenvolvimento estão em *azuresite/configurações.py*.
- A aplicação utiliza definições de produção quando a `WEBSITE_HOSTNAME` variável ambiental é definida. O Azure App Service define automaticamente esta variável para o URL da aplicação web, tal como `msdocs-django.azurewebsites.net` .

As definições de produção são específicas para configurar o Django para funcionar em qualquer ambiente de produção e não são particulares para o App Service. Para mais informações, consulte a [lista de verificação de implementação do Django.](https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/) Consulte também [as definições de Produção de Django em Azure](configure-language-python.md#production-settings-for-django-apps) para obter detalhes sobre algumas das alterações.

Tendo problemas? [Deixe-nos saber.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="3-create-postgres-database-in-azure"></a>3. Criar base de dados postgres em Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](../postgresql/concepts-pricing-tiers.md#compute-generations-and-vcores) is available in your region. -->

Instale a `db-up` extensão para o Azure CLI:

```azurecli
az extension add --name db-up
```

Se o `az` comando não for reconhecido, certifique-se de que tem o CLI Azure instalado como descrito no [Conjunto do seu ambiente inicial](#1-set-up-your-initial-environment).

Em seguida, crie a base de dados Postgres em Azure com o [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) comando:

```azurecli
az postgres up --resource-group DjangoPostgres-tutorial-rg --location westus2 --sku-name B_Gen5_1 --server-name <postgres-server-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

- **Substituir** *\<postgres-server-name>* com um nome **único em todo o Azure** (o ponto final do servidor torna-se). `https://<postgres-server-name>.postgres.database.azure.com` Um bom padrão é usar uma combinação do nome da sua empresa e outro valor único.
- Para *\<admin-username>* e *\<admin-password>* , especificar credenciais para criar um utilizador de administrador para este servidor Postgres. O nome de utilizador não pode ser *azure_superuser*, *azure_pg_admin,* *administrador,* *administrador,* *raiz,* *hóspede,* ou *público.* Não pode começar com *pg_.* A palavra-passe deve conter **8 a 128 caracteres** de três das seguintes categorias: letras maiúsculas inglesas, letras minúsculas inglesas, números (0 a 9) e caracteres não alfanuméricos (por exemplo, !, #, %). A palavra-passe não pode conter o nome de utilizador.
- Não utilize o `$` carácter no nome de utilizador ou na palavra-passe. Mais tarde cria-se variáveis ambientais com estes valores onde o `$` personagem tem um significado especial dentro do recipiente Linux usado para executar aplicações Python.
- O B_Gen5_1 (Básico, Gen5, 1 core) [nível de preços](../postgresql/concepts-pricing-tiers.md) usado aqui é o menos caro. Para as bases de dados de produção, omita o `--sku-name` argumento de utilizar o nível GP_Gen5_2 (Final geral, Gen 5, 2 núcleos).

Este comando executa as seguintes ações, que podem demorar alguns minutos:

- Criar um [grupo de recursos](../azure-resource-manager/management/overview.md#terminology) chamado , se já não `DjangoPostgres-tutorial-rg` existe.
- Crie um servidor Postgres nomeado pelo `--server-name` argumento.
- Criar uma conta de administrador utilizando os `--admin-user` argumentos e `--admin-password` argumentos. Pode omitir estes argumentos para permitir que o comando gere credenciais únicas para si.
- Crie uma `pollsdb` base de dados com o nome do `--database-name` argumento.
- Ative o acesso a partir do seu endereço IP local.
- Ativar o acesso a partir dos serviços Azure.
- Criar um utilizador de base de dados com acesso à `pollsdb` base de dados.

Pode fazer todos os passos separadamente com `az postgres` `psql` outros comandos, mas `az postgres up` faz todos os passos juntos.

Quando o comando termina, produz um objeto JSON que contém diferentes cadeias de ligação para a base de dados juntamente com o URL do servidor, um nome de utilizador gerado (como " joyfulKoala@msdocs-djangodb-12345 "), e uma palavra-passe GUID. **Copie o nome de utilizador e a palavra-passe para um ficheiro de texto temporário,** pois necessita deles mais tarde neste tutorial.

<!-- not all locations support az postgres up -->
> [!TIP]
> `-l <location-name>`, pode ser definido para qualquer uma das [regiões de Azure](https://azure.microsoft.com/global-infrastructure/regions/). Pode disponibilizar as regiões à sua subscrição com o [`az account list-locations`](/cli/azure/account#az_account_list_locations) comando. Para aplicações de produção, coloque a sua base de dados e a sua aplicação no mesmo local.

Tendo problemas? [Deixe-nos saber.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="4-deploy-the-code-to-azure-app-service"></a>4. Implementar o código para o Serviço de Aplicações Azure

Nesta secção, cria-se um anfitrião de aplicações na aplicação App Service, liga esta aplicação à base de dados postgres e, em seguida, implementa o seu código para esse anfitrião.

### <a name="41-create-the-app-service-app"></a>4.1 Criar a app De Serviço de Aplicações

No terminal, certifique-se de que está na pasta do repositório *de djangoapp* que contém o código da aplicação.

Criar uma aplicação de Serviço de Aplicações (o processo de anfitrião) com o [`az webapp up`](/cli/azure/webapp#az_webapp_up) comando:

```azurecli
az webapp up --resource-group DjangoPostgres-tutorial-rg --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>
```
<!-- without --sku creates PremiumV2 plan -->

- Para o `--location` argumento, utilize a mesma localização que fez para a base de dados na secção anterior.
- **Substituir** *\<app-name>* com um nome único em todo o Azure (o ponto final do servidor `https://<app-name>.azurewebsites.net` é). Personagens *\<app-name>* permitidos são `A` - `Z` `0` - `9` , e `-` . Um bom padrão é usar uma combinação do nome da sua empresa e um identificador de aplicativos.

Este comando executa as seguintes ações, que podem demorar alguns minutos:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Crie o [grupo de recursos](../azure-resource-manager/management/overview.md#terminology) se já não existir. (Neste comando utiliza-se o mesmo grupo de recursos em que criou a base de dados anteriormente.)
- Crie o plano de Serviço de [Aplicações](overview-hosting-plans.md) *DjangoPostgres-tutorial-plano* no nível básico de preços (B1), se não existir. `--plan` e `--sku` são opcionais.
- Crie a aplicação De Serviço de Aplicações se não existir.
- Ativar o registo predefinido para a aplicação, se ainda não estiver ativado.
- Faça o upload do repositório utilizando a implementação ZIP com automatização de construção ativada.
- Cache parâmetros comuns, como o nome do grupo de recursos e plano de Serviço de Aplicações, no ficheiro *.azure/config*. Como resultado, não precisa de especificar todos os mesmos parâmetros com comandos posteriores. Por exemplo, para recolocar a app depois de efetuar alterações, pode simplesmente voltar a funcionar `az webapp up` sem quaisquer parâmetros. Os comandos provenientes de extensões CLI, tais `az postgres up` como, no entanto, não utilizam atualmente a cache, razão pela qual precisava de especificar o grupo de recursos e a localização aqui com a utilização inicial de `az webapp up` .

Após uma implementação bem sucedida, o comando gera saída JSON como o seguinte exemplo:

![Exemplo az webapp up saída de comando](./media/tutorial-python-postgresql-app/az-webapp-up-output.png)

Tendo problemas? Consulte primeiro o [guia de resolução de problemas,](configure-language-python.md#troubleshooting)caso contrário, [avise-nos](https://aka.ms/DjangoCLITutorialHelp).

### <a name="42-configure-environment-variables-to-connect-the-database"></a>4.2 Configurar variáveis ambientais para ligar a base de dados

Com o código agora implementado para o Serviço de Aplicações, o próximo passo é ligar a app à base de dados postgres em Azure.

O código da aplicação espera encontrar informação sobre bases de dados em quatro variáveis ambientais denominada `DBHOST` `DBNAME` , e `DBUSER` `DBPASS` .

Para definir variáveis ambientais no Serviço de Aplicações, crie "configurações de aplicações" com o seguinte comando de [configuração de configurações de appsapp az.](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set)

```azurecli
az webapp config appsettings set --settings DBHOST="<postgres-server-name>" DBNAME="pollsdb" DBUSER="<username>" DBPASS="<password>"
```

- *\<postgres-server-name>* Substitua-o pelo nome que usou anteriormente pelo `az postgres up` comando. O código em *azuresite/produção.py* anexa automaticamente `.postgres.database.azure.com` para criar o URL completo do servidor Postgres.
- Substitua *\<username>* e *\<password>* pelas credenciais de administrador que usou com o `az postgres up` comando anterior, ou pelas que `az postgres up` geraram para si. O código em *azuresite/produção.py* constrói automaticamente o nome de utilizador completo postgres de `DBUSER` `DBHOST` e, por isso, não inclua a `@server` parte. (Além disso, como já foi notado anteriormente, não deve utilizar o `$` personagem em nenhum dos valores, uma vez que tem um significado especial para variáveis ambientais Linux.)
- O grupo de recursos e os nomes das aplicações são extraídos dos valores em cache no ficheiro *.azure/config.*

No seu código Python, acede a estas definições como variáveis ambientais com declarações como `os.environ.get('DBHOST')` . Para obter mais informações, consulte [as variáveis do ambiente Access.](configure-language-python.md#access-environment-variables)

Tendo problemas? Consulte primeiro o [guia de resolução de problemas,](configure-language-python.md#troubleshooting)caso contrário, [avise-nos](https://aka.ms/DjangoCLITutorialHelp).

### <a name="43-run-django-database-migrations"></a>4.3 Executar migrações na base de dados de Django

As migrações na base de dados de Django asseguram que o esquema no PostgreSQL na base de dados Azure corresponde aos descritos no seu código.

1. Abra uma sessão SSH **no navegador** navegando para o seguinte URL e iniciando sessão com as suas credenciais de conta Azure (não as credenciais do servidor de base de dados).

    ```
    https://<app-name>.scm.azurewebsites.net/webssh/host
    ```

    `<app-name>`Substitua-o pelo nome utilizado anteriormente no `az webapp up` comando.

    Pode alternadamente ligar-se a uma sessão de SSH com o [`az webapp ssh`](/cli/azure/webapp#az_webapp_ssh) comando. No Windows, este comando requer o Azure CLI 2.18.0 ou superior.

    Se não conseguir ligar-se à sessão SSH, então a própria aplicação falhou em arrancar. [Verifique os registos de diagnóstico](#6-stream-diagnostic-logs) para obter mais detalhes. Por exemplo, se não tiver criado as definições de aplicações necessárias na secção anterior, os registos indicarão `KeyError: 'DBNAME'` .

1. Na sessão SSH, executar os seguintes comandos (pode colar comandos usando **Ctrl** + **Shift** + **V**):

    ```bash
    # Change to the app folder
    cd $APP_PATH
    
    # Activate the venv
    source /antenv/bin/activate

    # Install requirements
    pip install -r requirements.txt

    # Run database migrations
    python manage.py migrate

    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```

    Se encontrar algum erro relacionado com a ligação à base de dados, verifique os valores das definições de aplicação criadas na secção anterior.

1. O `createsuperuser` comando pede-lhe credenciais de super-20. Para efeitos deste tutorial, utilize o nome de utilizador predefinido `root` , prima **Enter** para o endereço de e-mail para o deixar em branco e introduza `Pollsdb1` a palavra-passe.

1. Se vir um erro de que a base de dados esteja bloqueada, certifique-se de que executou o `az webapp settings` comando na secção anterior. Sem estas definições, o comando migratório não pode comunicar com a base de dados, resultando no erro.

Tendo problemas? Consulte primeiro o [guia de resolução de problemas,](configure-language-python.md#troubleshooting)caso contrário, [avise-nos](https://aka.ms/DjangoCLITutorialHelp).
    
### <a name="44-create-a-poll-question-in-the-app"></a>4.4 Criar uma pergunta de sondagem na app

1. Num browser, abra o `http://<app-name>.azurewebsites.net` URL. A aplicação deve exibir a mensagem "App Polls" e "Não há sondagens disponíveis" porque ainda não há sondagens específicas na base de dados.

    Se vir "Erro de Aplicação", é provável que não tenha criado as definições necessárias no passo anterior, [configurar variáveis ambientais para ligar a base de dados](#42-configure-environment-variables-to-connect-the-database), ou que esses valores contenham erros. Verifique o comando `az webapp config appsettings list` para verificar as definições. Também pode [verificar os registos de diagnóstico](#6-stream-diagnostic-logs) para ver erros específicos durante o arranque da aplicação. Por exemplo, se não criar as definições, os registos mostrarão o erro, `KeyError: 'DBNAME'` .

    Depois de atualizar as definições para corrigir quaisquer erros, dê à app um minuto para reiniciar e, em seguida, refresque o navegador.

1. Navegue para `http://<app-name>.azurewebsites.net/admin`. Inscreva-se utilizando credenciais de super-utilização de Django da secção anterior `root` (e `Pollsdb1` . In **Polls**, selecione **Adicionar** ao Lado **das Perguntas** e crie uma pergunta de sondagem com algumas escolhas.

1. Procure novamente `http://<app-name>.azurewebsites.net` para confirmar que as perguntas são agora apresentadas ao utilizador. Responda a perguntas como quiser gerar alguns dados na base de dados.

**Parabéns!** Está a executar uma aplicação web Python Django no Azure App Service for Linux, com uma base de dados de Postgres ativa.

Tendo problemas? [Deixe-nos saber.](https://aka.ms/DjangoCLITutorialHelp)

> [!NOTE]
> O App Service deteta um projeto Django procurando um ficheiro *wsgi.py* em cada sub-dobradeira, o que `manage.py startproject` cria por padrão. Quando o Serviço de Aplicações encontra esse ficheiro, carrega a aplicação web do Django. Para mais informações, consulte [a imagem de Configure incorporada python.](configure-language-python.md)

## <a name="5-make-code-changes-and-redeploy"></a>5. Fazer alterações de código e reimplantar

Nesta secção, efetua alterações locais na aplicação e reimplante o código para o Serviço de Aplicações. No processo, você configura um ambiente virtual Python que suporta o trabalho em curso.

### <a name="51-run-the-app-locally"></a>5.1 Executar a aplicação localmente

Numa janela de terminal, execute os comandos seguintes. Certifique-se de seguir as indicações ao criar o superuser:

# <a name="bash"></a>[festa](#tab/bash)

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# Configure the Python virtual environment
py -3 -m venv venv
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned -Force
venv\scripts\activate

# Install dependencies
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="cmd"></a>[CMD](#tab/cmd)

```cmd
:: Configure the Python virtual environment
py -3 -m venv venv
venv\scripts\activate

:: Install dependencies
pip install -r requirements.txt
:: Run Django migrations
python manage.py migrate
:: Create Django superuser (follow prompts)
python manage.py createsuperuser
:: Run the dev server
python manage.py runserver
```
---

Uma vez que a aplicação web está totalmente carregada, o servidor de desenvolvimento Django fornece o URL da aplicação local na mensagem: "Iniciar o servidor de desenvolvimento em http://127.0.0.1:8000/ . Saia do servidor com CTRL-BREAK".

![Exemplo, saída do servidor de desenvolvimento de Django](./media/tutorial-python-postgresql-app/django-dev-server-output.png)

Teste a aplicação localmente com os seguintes passos:

1. Aceda a `http://localhost:8000` um browser, que deve exibir a mensagem "Não há sondagens disponíveis". 

1. Vá `http:///localhost:8000/admin` e inscreva-se utilizando o utilizador de administração que criou anteriormente. Em **Sondagens**, mais uma vez selecione **Adicionar** ao Lado **das Perguntas** e criar uma pergunta de sondagem com algumas escolhas. 

1. Vá a *http: \/ /localhost:8000* novamente e responda à pergunta para testar a app. 

1. Pare o servidor Django pressionando **o Ctrl** + **C**.

Ao correr localmente, a aplicação está a usar uma base de dados local do Sqlite3 e não interfere com a sua base de dados de produção. Também pode utilizar uma base de dados postgreSQL local, se desejar, para simular melhor o seu ambiente de produção.

Tendo problemas? [Deixe-nos saber.](https://aka.ms/DjangoCLITutorialHelp)

### <a name="52-update-the-app"></a>5.2 Atualizar a aplicação

Em `polls/models.py` , localizar a linha que começa com e alterar o parâmetro para `choice_text` `max_length` 100:

```python
# Find this line of code and set max_length to 100 instead of 200
choice_text = models.CharField(max_length=100)
```

Porque mudou o modelo de dados, criou uma nova migração de Django e migra a base de dados:

```
python manage.py makemigrations
python manage.py migrate
```

Executar novamente o servidor de desenvolvimento com `python manage.py runserver` e testar a aplicação em *http: \/ /localhost:8000/administrador*:

Pare o servidor web do Django novamente com **ctrl** + **C**.

Tendo problemas? Consulte primeiro o [guia de resolução de problemas,](configure-language-python.md#troubleshooting)caso contrário, [avise-nos](https://aka.ms/DjangoCLITutorialHelp).

### <a name="53-redeploy-the-code-to-azure"></a>5.3 Recolocar o código para Azure

Executar o seguinte comando na raiz do repositório:

```azurecli
az webapp up
```

Este comando utiliza os parâmetros em cache no ficheiro *.azure/config.* Como o Serviço de Aplicações deteta que a app já existe, apenas redistribui o código.

Tendo problemas? Consulte primeiro o [guia de resolução de problemas,](configure-language-python.md#troubleshooting)caso contrário, [avise-nos](https://aka.ms/DjangoCLITutorialHelp).

### <a name="54-rerun-migrations-in-azure"></a>5.4 Rerun migrações em Azure

Como fez alterações no modelo de dados, precisa de repetir as migrações na base de dados no Serviço de Aplicações.

Abra novamente uma sessão de SSH no browser navegando para `https://<app-name>.scm.azurewebsites.net/webssh/host` . Em seguida, execute os seguintes comandos:

```
cd $APP_PATH
source /antenv/bin/activate
pip install -r requirements.txt
python manage.py migrate
```

Tendo problemas? Consulte primeiro o [guia de resolução de problemas,](configure-language-python.md#troubleshooting)caso contrário, [avise-nos](https://aka.ms/DjangoCLITutorialHelp).

### <a name="55-review-app-in-production"></a>5.5 App de revisão em produção

Navegue para `http://<app-name>.azurewebsites.net` testar novamente a aplicação em produção. (Como mudou apenas o comprimento de um campo de base de dados, a alteração só é percetível se tentar introduzir uma resposta mais longa ao criar uma pergunta.)

Tendo problemas? Consulte primeiro o [guia de resolução de problemas,](configure-language-python.md#troubleshooting)caso contrário, [avise-nos](https://aka.ms/DjangoCLITutorialHelp).

## <a name="6-stream-diagnostic-logs"></a>6. Fluxo de registos de diagnóstico

Pode aceder aos registos de consola gerados a partir do interior do contentor que acolhe a aplicação no Azure.

Executar o seguinte comando Azure CLI para ver o fluxo de registo. Este comando utiliza parâmetros em cache no ficheiro *.azure/config.*

```azurecli
az webapp log tail
```

Se não vir os registos da consola imediatamente, volte a consultar dentro de 30 segundos.

Para parar o streaming de registo a qualquer momento, digite **Ctrl** + **C**.

Tendo problemas? [Deixe-nos saber.](https://aka.ms/DjangoCLITutorialHelp)

> [!NOTE]
> Também pode inspecionar os ficheiros de registo no browser em `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.
>
> `az webapp up` liga o registo padrão para si. Por razões de desempenho, esta exploração se desliga após algum tempo, mas volta a funcionar sempre que `az webapp up` corre novamente. Para ligá-lo manualmente, executar o seguinte comando:
>
> ```azurecli
> az webapp log config --docker-container-logging filesystem
> ```

## <a name="7-manage-your-app-in-the-azure-portal"></a>7. Gerencie a sua app no portal Azure

No [portal Azure,](https://portal.azure.com)procure o nome da aplicação e selecione a aplicação nos resultados.

![Navegue para a sua app Python Django no portal Azure](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Por predefinição, o portal mostra a página **geral** da sua aplicação, que fornece uma visão geral do desempenho. Aqui, também pode executar tarefas básicas de gestão como navegar, parar, reiniciar e eliminar. Os separadores no lado esquerdo da página mostram as várias páginas de configuração que pode abrir.

![Gerencie a sua app Python Django na página Overview no portal Azure](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

Tendo problemas? Consulte primeiro o [guia de resolução de problemas,](configure-language-python.md#troubleshooting)caso contrário, [avise-nos](https://aka.ms/DjangoCLITutorialHelp).

## <a name="8-clean-up-resources"></a>8. Limpar recursos

Se quiser manter a app ou continuar com os tutoriais adicionais, avance para os [próximos passos.](#next-steps) Caso contrário, para evitar incorrer em encargos em curso, pode eliminar o grupo de recursos criado para este tutorial:

```azurecli
az group delete --no-wait
```

O comando utiliza o nome do grupo de recursos em cache no ficheiro *.azure/config.* Ao eliminar o grupo de recursos, também lida e elimina todos os recursos contidos no mesmo.

Apagar todos os recursos pode levar algum tempo. O `--no-wait` argumento permite que o comando regresse imediatamente.

Tendo problemas? [Deixe-nos saber.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="next-steps"></a>Passos seguintes

Saiba como mapear um nome DNS personalizado para a sua aplicação:

> [!div class="nextstepaction"]
> [Tutorial: Mapeie o nome DNS personalizado para a sua aplicação](app-service-web-tutorial-custom-domain.md)

Saiba como o App Service executa uma aplicação Python:

> [!div class="nextstepaction"]
> [Configure app Python](configure-language-python.md)
