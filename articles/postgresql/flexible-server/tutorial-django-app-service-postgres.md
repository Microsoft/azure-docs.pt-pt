---
title: 'Tutorial: Implementar app Django com Serviço de Aplicações e Base de Dados Azure para PostgreSQL - Servidor Flexível (Pré-visualização) em rede virtual'
description: Implementar aplicativo Django com App Serice e Azure Database para PostgreSQL - Servidor Flexível (Pré-visualização) em rede virtual
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 09/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 84fd0c368bcf39af1bae90dc83336ffdb8489768
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791522"
---
# <a name="tutorial-deploy-django-app-with-app-service-and-azure-database-for-postgresql---flexible-server-preview"></a>Tutorial: Implementar aplicativo Django com Serviço de Aplicações e Base de Dados Azure para PostgreSQL - Servidor Flexível (Pré-visualização)

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

Neste tutorial você aprenderá a implementar uma aplicação Django em Azure usando Serviços de Aplicações e Base de Dados Azure para PostgreSQL - Servidor Flexível numa rede virtual.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Este artigo requer que esteja a executar a versão 2.0 do Azure CLI ou mais tarde localmente. Para ver a versão instalada, execute o comando `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

Terá de iniciar sessão na sua conta utilizando o comando [de login az.](/cli/azure/authenticate-azure-cli) Anote a propriedade **id** da saída de comando para o nome de subscrição correspondente.

```azurecli
az login
```

Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso deve ser cobrado. Selecione o ID da subscrição específica na sua conta com o comando [az account set](/cli/azure/account). Substitua a propriedade de **ID** por subscrição da saída **de login az** para a sua subscrição no espaço reservado de iD de subscrição.

```azurecli
az account set --subscription <subscription id>
```
## <a name="clone-or-download-the-sample-app"></a>Clone ou descarregue o aplicativo de amostra

# <a name="git-clone"></a>[Clone de Git](#tab/clone)

Clone o repositório de amostras:

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

Em seguida, entrar na pasta:

```terminal
cd djangoapp
```

# <a name="download"></a>[Transferência](#tab/download)

Visite [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp) , selecione **Clone** e, em seguida, selecione **Download ZIP**.

Desembale o ficheiro ZIP numa pasta chamada *djangoapp*.

Em seguida, abra uma janela terminal naquela pasta *de djangoapp.*

---

A amostra do Djangoapp contém a aplicação de sondagens Django orientada por dados que obtém depois de [escrever a sua primeira aplicação Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) na documentação do Django. A aplicação completa é fornecida aqui para sua conveniência.

A amostra também é modificada para funcionar num ambiente de produção como o App Service:

- As definições de produção estão no ficheiro *azuresite/produção.py.* Os detalhes do desenvolvimento estão em *azuresite/configurações.py*.
- A aplicação utiliza definições de produção quando a `DJANGO_ENV` variável ambiente está definida para "produção". Você cria esta variável de ambiente mais tarde no tutorial juntamente com outros usados para a configuração da base de dados PostgreSQL.

Estas alterações são específicas para configurar o Django para funcionar em qualquer ambiente de produção e não são particulares para o App Service. Para mais informações, consulte a [lista de verificação de implementação do Django.](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>Criar um Servidor Flexível PostgreSQL numa nova rede virtual

Crie um servidor flexível privado e uma base de dados dentro de uma rede virtual (VNET) utilizando o seguinte comando:
```azurecli
# Create Flexible server in a VNET

az postgres flexible-server create --resource-group myresourcegroup --location westus2

```

Este comando executa as seguintes ações, que podem demorar alguns minutos:

- Crie o grupo de recursos se já não existir.
- Gera um nome de servidor se não for fornecido.
- Crie uma nova rede virtual para o seu novo servidor postgreSQL. **Tome nota do nome da rede virtual e nome da sub-rede** criado para o seu servidor, uma vez que precisa de adicionar a aplicação web à mesma rede virtual.
- Cria nome de utilizador de administração, palavra-passe para o seu servidor se não for fornecido. **Tome nota do nome de utilizador e da palavra-passe** para utilizar no passo seguinte.
- Crie uma base de dados ```postgres``` que possa ser usada para o desenvolvimento. Pode executar [ **o PSQL** para ligar à base de dados](quickstart-create-server-portal.md#connect-to-the-postgresql-database-using-psql) para criar uma base de dados diferente.

> [!NOTE]
> Tome nota da sua palavra-passe que será gerada para si se não for fornecida. Se esquecer a palavra-passe, terá de redefinir a palavra-passe usando o ``` az postgres flexible-server update``` comando


## <a name="deploy-the-code-to-azure-app-service"></a>Implementar o código para o Serviço de Aplicações Azure

Nesta secção, cria-se um anfitrião de aplicações na aplicação App Service, liga esta aplicação à base de dados postgres e, em seguida, implementa o seu código para esse anfitrião.


### <a name="create-the-app-service-web-app-in-a-virtual-network"></a>Crie a aplicação web do Serviço de Aplicações numa rede virtual

No terminal, certifique-se de que está na raiz do repositório `djangoapp` ( ) que contém o código da aplicação.

Criar uma aplicação de Serviço de Aplicações (o processo de anfitrião) com o [`az webapp up`](/cli/azure/webapp#az_webapp_up) comando:

```azurecli

# Create a web app
az webapp up --resource-group myresourcegroup --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>

# Enable VNET integration for web app.
# Replace <vnet-name> and <subnet-name> with the virtual network and subnet name that the flexible server is using.

az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet <vnet-name> --subnet <subnet-name>

# Configure database information as environment variables
# Use the postgres server name , database name , username , password for the database created in the previous steps

az webapp config appsettings set --settings DJANGO_ENV="production" DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>"
```
- Para o `--location` argumento, utilize a mesma localização que fez para a base de dados na secção anterior.
- Substitua *\<app-name>* por um nome único em todo o Azure (o ponto final do servidor é `https://\<app-name>.azurewebsites.net` ). Personagens *\<app-name>* permitidos são `A` - `Z` `0` - `9` , e `-` . Um bom padrão é usar uma combinação do nome da sua empresa e um identificador de aplicativos.
- Crie o plano de Serviço de [Aplicações](../../app-service/overview-hosting-plans.md) *DjangoPostgres-tutorial-plano* no nível básico de preços (B1), se não existir. `--plan` e `--sku` são opcionais.
- Crie a aplicação De Serviço de Aplicações se não existir.
- Ativar o registo predefinido para a aplicação, se ainda não estiver ativado.
- Faça o upload do repositório utilizando a implementação ZIP com automatização de construção ativada.
- o comando **de integração de vnets da webapp** adiciona a aplicação web na mesma rede virtual que o servidor postgres.
- O código da aplicação espera encontrar informações sobre bases de dados em várias variáveis ambientais. Para definir variáveis ambientais no Serviço de Aplicações, cria "configurações de aplicações" com o comando [de configuração de configurações de appsettings az webapp.](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set)

> [!TIP]
> Muitos comandos Azure CLI cache parâmetros comuns, como o nome do grupo de recursos e plano de Serviço de Aplicações, no ficheiro *.azure/config*. Como resultado, não precisa de especificar todos os mesmos parâmetros com comandos posteriores. Por exemplo, para recolocar a app depois de efetuar alterações, pode simplesmente voltar a funcionar `az webapp up` sem quaisquer parâmetros.

### <a name="run-django-database-migrations"></a>Executar migrações de base de dados de Django

As migrações na base de dados de Django asseguram que o esquema no PostgreSQL na base de dados Azure corresponde aos descritos no seu código.

1. Abra uma sessão de SSH no navegador navegando para *https:// \<app-name> .scm.azurewebsites.net/webssh/host* e inscreva-se com as suas credenciais de conta Azure (não as credenciais do servidor de base de dados).

1. Na sessão SSH, executar os seguintes comandos (pode colar comandos usando **Ctrl** + **Shift** + **V**):

    ```bash
    cd site/wwwroot

    # Activate default virtual environment in App Service container
    source /antenv/bin/activate
    # Install packages
    pip install -r requirements.txt
    # Run database migrations
    python manage.py migrate
    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```

1. O `createsuperuser` comando pede-lhe credenciais de super-20. Para efeitos deste tutorial, utilize o nome de utilizador predefinido `root` , prima **Enter** para o endereço de e-mail para o deixar em branco e introduza `postgres1` a palavra-passe.

### <a name="create-a-poll-question-in-the-app"></a>Crie uma pergunta de sondagem na app

1. Num browser, abra o URL *http: \/ / \<app-name> .azurewebsites.net*. A aplicação deve exibir a mensagem "Não há sondagens disponíveis" porque ainda não há sondagens específicas na base de dados.

1. Navegue para *http: \/ / \<app-name> .azurewebsites.net/admin*. Inscreva-se utilizando credenciais de super-utilização da secção anterior `root` (e `postgres1` . In **Polls**, selecione **Adicionar** ao Lado **das Perguntas** e crie uma pergunta de sondagem com algumas escolhas.

1. Consulte novamente *http: \/ / \<app-name> .azurewebsites.net/* confirmar que as perguntas são agora apresentadas ao utilizador. Responda a perguntas como quiser gerar alguns dados na base de dados.

**Parabéns!** Está a executar uma aplicação web Python Django no Azure App Service for Linux, com uma base de dados de Postgres ativa.

> [!NOTE]
> O App Service deteta um projeto Django procurando um ficheiro *wsgi.py* em cada sub-dobradeira, o que `manage.py startproject` cria por padrão. Quando o Serviço de Aplicações encontra esse ficheiro, carrega a aplicação web do Django. Para mais informações, consulte [a imagem de Configure incorporada python.](../../app-service/configure-language-python.md)

## <a name="make-code-changes-and-redeploy"></a>Fazer alterações de código e reimplantar

Nesta secção, efetua alterações locais na aplicação e reimplante o código para o Serviço de Aplicações. No processo, você configura um ambiente virtual Python que suporta o trabalho em curso.

### <a name="run-the-app-locally"></a>Executar a aplicação localmente

Numa janela de terminal, execute os comandos seguintes. Certifique-se de seguir as indicações ao criar o superuser:

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```
Uma vez que a aplicação web está totalmente carregada, o servidor de desenvolvimento Django fornece o URL da aplicação local na mensagem: "Iniciar o servidor de desenvolvimento em http://127.0.0.1:8000/ . Saia do servidor com CTRL-BREAK".

:::image type="content" source="./media/tutorial-django-app-service-postgres/django-dev-server-output.png" alt-text="Exemplo, saída do servidor de desenvolvimento de Django":::

Teste a aplicação localmente com os seguintes passos:

1. Vá a *http: \/ /localhost:8000* em um browser, que deve exibir a mensagem "Não há sondagens disponíveis".

1. Vá a *http: \/ /localhost:8000/administrador* e inscreva-se utilizando o utilizador administrativo que criou anteriormente. Em **Sondagens**, mais uma vez selecione **Adicionar** ao Lado **das Perguntas** e criar uma pergunta de sondagem com algumas escolhas.

1. Vá a *http: \/ /localhost:8000* novamente e responda à pergunta para testar a app.

1. Pare o servidor Django pressionando **o Ctrl** + **C**.

Ao correr localmente, a aplicação está a usar uma base de dados local do Sqlite3 e não interfere com a sua base de dados de produção. Também pode utilizar uma base de dados postgreSQL local, se desejar, para simular melhor o seu ambiente de produção.



### <a name="update-the-app"></a>Atualizar a aplicação

Em `polls/models.py` , localizar a linha que começa com e alterar o parâmetro para `choice_text` `max_length` 100:

```python
# Find this lie of code and set max_length to 100 instead of 200
choice_text = models.CharField(max_length=100)
```

Porque mudou o modelo de dados, criou uma nova migração de Django e migra a base de dados:

```
python manage.py makemigrations
python manage.py migrate
```

Executar novamente o servidor de desenvolvimento com `python manage.py runserver` e testar a aplicação em *http: \/ /localhost:8000/administrador*:

Pare o servidor web do Django novamente com **ctrl** + **C**.


### <a name="redeploy-the-code-to-azure"></a>Recolocar o código para Azure

Executar o seguinte comando na raiz do repositório:

```azurecli
az webapp up
```

Este comando utiliza os parâmetros em cache no ficheiro *.azure/config.* Como o Serviço de Aplicações deteta que a app já existe, apenas redistribui o código.



### <a name="rerun-migrations-in-azure"></a>Rerun migrações em Azure

Como fez alterações no modelo de dados, precisa de repetir as migrações na base de dados no Serviço de Aplicações.

Abra novamente uma sessão de SSH no navegador navegando para *https:// \<app-name> .scm.azurewebsites.net/webssh/host*. Em seguida, execute os seguintes comandos:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>App de revisão em produção

Navegue para *http: \/ / \<app-name> .azurewebsites.net* e testar novamente a aplicação em produção. (Como só alterou o comprimento de um campo de base de dados, a alteração só é percetível se tentar introduzir uma resposta mais longa na criação de uma pergunta.)

> [!TIP]
> Você pode usar [django-armazenamento para](https://django-storages.readthedocs.io/en/latest/backends/azure.html) armazenar ativos estáticos & media no armazenamento Azure. Pode utilizar o Azure CDN para gzipping para ficheiros estáticos.


## <a name="manage-your-app-in-the-azure-portal"></a>Gerencie a sua app no portal Azure

No [portal Azure,](https://portal.azure.com)procure o nome da aplicação e selecione a aplicação nos resultados.

:::image type="content" source="./media/tutorial-django-app-service-postgres/navigate-to-django-app-in-app-services-in-the-azure-portal.png" alt-text="Navegue para a sua app Python Django no portal Azure":::

Por predefinição, o portal mostra a página **geral** da sua aplicação, que fornece uma visão geral do desempenho. Aqui, também pode executar tarefas básicas de gestão como navegar, parar, reiniciar e eliminar. Os separadores no lado esquerdo da página mostram as várias páginas de configuração que pode abrir.

:::image type="content" source="./media/tutorial-django-app-service-postgres/manage-django-app-in-app-services-in-the-azure-portal.png" alt-text="Gerencie a sua app Python Django na página Overview no portal Azure":::


## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser manter a app ou continuar até ao próximo tutorial, avance para os [próximos passos](#next-steps). Caso contrário, para evitar incorrer em encargos em curso, pode eliminar o grupo de recursos criado para este tutorial:

```azurecli
az group delete -g myresourcegroup
```

O comando utiliza o nome do grupo de recursos em cache no ficheiro *.azure/config.* Ao eliminar o grupo de recursos, também lida e elimina todos os recursos contidos no mesmo.

## <a name="next-steps"></a>Passos seguintes

Saiba como mapear um nome DNS personalizado para a sua aplicação:

> [!div class="nextstepaction"]
> [Tutorial: Mapeie o nome DNS personalizado para a sua aplicação](../../app-service/app-service-web-tutorial-custom-domain.md)

Saiba como o App Service executa uma aplicação Python:

> [!div class="nextstepaction"]
> [Configure app Python](../../app-service/configure-language-python.md)
