---
title: Configure aplicações Linux Python
description: Saiba como configurar o recipiente Python no qual são executadas aplicações web, utilizando tanto o portal Azure como o Azure CLI.
ms.topic: quickstart
ms.date: 11/16/2020
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 7589b5c66bf4fa86db243574f551ec585ccccea1
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855061"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Configure uma aplicação Linux Python para o Azure App Service

Este artigo descreve como o [Azure App Service](overview.md) gere as aplicações Python, como pode migrar as aplicações existentes para o Azure e como pode personalizar o comportamento do Serviço de Aplicações quando necessário. As aplicações Python devem ser implementadas com todos os módulos [pip](https://pypi.org/project/pip/) necessários.

O motor de implementação do Serviço de Aplicações ativa automaticamente um ambiente virtual e funciona `pip install -r requirements.txt` para si quando implementa um [repositório Git](deploy-local-git.md), ou um pacote [zip](deploy-zip.md).

Este guia fornece conceitos e instruções fundamentais para os desenvolvedores de Python que usam um recipiente Linux incorporado no Serviço de Aplicações. Se nunca usou o Azure App Service, siga primeiro o [quickstart Python](quickstart-python.md) e [python com o tutorial postgreSQL.](tutorial-python-postgresql-app.md)

Pode utilizar o [portal Azure](https://portal.azure.com) ou o CLI Azure para configuração:

- **Portal Azure**, use a página de Configuração de **Configurações** da aplicação  >  **Configuration** como descrito no [Configure uma aplicação de Serviço de Aplicações no portal Azure](configure-common.md).

- **Azure CLI:** tem duas opções.

    - Executar comandos na [Azure Cloud Shell](../cloud-shell/overview.md).
    - Executar comandos localmente instalando a versão mais recente do [Azure CLI,](/cli/azure/install-azure-cli)em seguida, inicie sessão no Azure usando [login az](/cli/azure/reference-index#az-login).
    
> [!NOTE]
> O Linux é atualmente a opção recomendada para executar aplicações Python no Serviço de Aplicações. Para obter informações sobre a opção Windows, consulte [Python no sabor Windows do Serviço de Aplicações.](/visualstudio/python/managing-python-on-azure-app-service)

## <a name="configure-python-version"></a>Versão Configure Python

- **Portal Azure**: utilize o separador **definições gerais** na página **de Configuração,** conforme descrito nas [definições gerais de Configuração](configure-common.md#configure-general-settings) para recipientes Linux.

- **Azure CLI:**

    -  Mostre a versão atual python com [az webapp config show](/cli/azure/webapp/config#az_webapp_config_show):
    
        ```azurecli
        az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
        ```
        
        Substitua `<resource-group-name>` e `<app-name>` pelos nomes apropriados para a sua aplicação web.
    
    - Desconfig de versão Python com [conjunto de config do webapp az](/cli/azure/webapp/config#az_webapp_config_set)
        
        ```azurecli
        az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
        ```
    
    - Mostrar todas as versões Python que são suportadas no Azure App Service com [az webapp list-runtimes](/cli/azure/webapp#az_webapp_list_runtimes):
    
        ```azurecli
        az webapp list-runtimes --linux | grep PYTHON
        ```
    
Em vez disso, pode executar uma versão não suportada do Python construindo a sua própria imagem de contentor. Para obter mais informações, veja [Utilizar uma imagem personalizada do Docker](tutorial-custom-container.md?pivots=container-linux).

<!-- <a> element here to preserve external links-->
<a name="access-environment-variables"></a>

## <a name="customize-build-automation"></a>Personalize a automatização de construção

O sistema de construção do App Service, chamado Oryx, executa os seguintes passos quando implementa a sua aplicação utilizando pacotes Git ou zip:

1. Executar um script pré-construção personalizado se especificado pela `PRE_BUILD_COMMAND` definição.
1. Execute o `pip install -r requirements.txt`. O *ficheirorequirements.txt* deve estar presente na pasta raiz do projeto. Caso contrário, o processo de construção relata o erro: "Não foi possível encontrar setup.py ou requirements.txt; Não a funcionar pip instalar."
1. Se *manage.py* for encontrado na raiz do repositório (indicando uma aplicação Django), executar *manage.py a collectásta .* No entanto, se a `DISABLE_COLLECTSTATIC` regulação `true` for, este passo é ignorado.
1. Executar script pós-construção personalizado se especificado pela `POST_BUILD_COMMAND` definição.

Por predefinição, as `PRE_BUILD_COMMAND` `POST_BUILD_COMMAND` definições e `DISABLE_COLLECTSTATIC` as definições estão vazias. 

- Para desativar a coleta em execução ao construir aplicações Django, defina a `DISABLE_COLLECTSTATIC` definição como verdadeira.

- Para executar comandos de pré-construção, defina a `PRE_BUILD_COMMAND` definição para conter um comando, `echo Pre-build command` como, por exemplo, ou um caminho para um ficheiro de script em relação à raiz do seu projeto, como `scripts/prebuild.sh` . Todos os comandos devem utilizar caminhos relativos para a pasta raiz do projeto.

- Para executar comandos pós-construção, defina a `POST_BUILD_COMMAND` definição para conter um comando, `echo Post-build command` como, por exemplo, ou um caminho para um ficheiro de script em relação à raiz do seu projeto, como `scripts/postbuild.sh` . Todos os comandos devem utilizar caminhos relativos para a pasta raiz do projeto.

Para configurações adicionais que personalizem a automatização de construção, consulte [a configuração oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md). 

Para aceder aos registos de construção e implantação, consulte [os registos de implementação de acesso](#access-deployment-logs).

Para obter mais informações sobre como o App Service funciona e constrói aplicações Python em Linux, veja [como a Oryx deteta e constrói aplicações Python.](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/python.md)

> [!NOTE]
> As `PRE_BUILD_SCRIPT_PATH` `POST_BUILD_SCRIPT_PATH` configurações são idênticas e são `PRE_BUILD_COMMAND` `POST_BUILD_COMMAND` suportadas para fins antigos.
> 
> Uma definição chamada `SCM_DO_BUILD_DURING_DEPLOYMENT` , se contiver `true` ou 1, ativa uma construção oryx acontece durante a implantação. A definição é verdadeira quando se implanta o recurso ao git, ao comando Azure CLI `az webapp up` e ao Código do Estúdio Visual.

> [!NOTE]
> Utilize sempre caminhos relativos em todos os scripts pré e pós-construção porque o recipiente de construção em que o Oryx funciona é diferente do recipiente de tempo de execução em que a aplicação funciona. Nunca confie na colocação exata da pasta do projeto da sua aplicação dentro do recipiente (por exemplo, que é colocada no *local/wwwroot).*

## <a name="migrate-existing-applications-to-azure"></a>Migrar as aplicações existentes para a Azure

As aplicações web existentes podem ser redistribuídas para a Azure da seguinte forma:

1. **Repositório de origem**: Mantenha o seu código fonte num repositório adequado como o GitHub, que lhe permite configurar uma implantação contínua mais tarde neste processo.
    1. O seu ficheiro *requirements.txt* deve estar na raiz do seu repositório para o Serviço de Aplicações instalar automaticamente as embalagens necessárias.    

1. **Base de dados**: Se a aplicação depender de uma base de dados, também fornece os recursos necessários ao Azure. Ver [Tutorial: Implementar uma aplicação web Django com PostgreSQL - crie uma base de dados](tutorial-python-postgresql-app.md#3-create-postgres-database-in-azure) por exemplo.

1. **Recursos de serviço de aplicações**: Crie um grupo de recursos, Plano de Serviço de Aplicações e aplicação web do Serviço de Aplicações para hospedar a sua aplicação. Pode fazê-lo mais facilmente fazendo uma primeira implementação do seu código através do comando Azure CLI `az webapp up` , como mostrado no [Tutorial: Implementar uma aplicação web Django com PostgreSQL - implementar o código](tutorial-python-postgresql-app.md#4-deploy-the-code-to-azure-app-service). Substitua os nomes do grupo de recursos, o Plano de Serviço de Aplicações e a aplicação web para ser mais adequado para a sua aplicação.

1. **Variáveis ambientais**: Se a sua aplicação necessitar de variáveis ambientais, crie [configurações equivalentes de aplicações do Serviço de Aplicações](configure-common.md#configure-app-settings)de Aplicações . Estas definições de Serviço de Aplicações aparecem no seu código como variáveis ambientais, conforme descrito nas [variáveis do ambiente Access](#access-app-settings-as-environment-variables).
    - As ligações de base de dados, por exemplo, são frequentemente geridas através de tais configurações, como mostrado no [Tutorial: Implementar uma aplicação web Django com PostgreSQL - configurar variáveis para ligar a base de dados](tutorial-python-postgresql-app.md#42-configure-environment-variables-to-connect-the-database).
    - Consulte [as definições de Produção para apps Django](#production-settings-for-django-apps) para configurações específicas para aplicações típicas do Django.

1. **Startup de aplicações**: Reveja a secção, [processo de arranque do Container](#container-startup-process) mais tarde neste artigo para entender como o Serviço de Aplicações tenta executar a sua app. O Serviço de Aplicações utiliza o servidor web Gunicorn por padrão, que deve ser capaz de encontrar o seu objeto de aplicação ou *wsgi.py* pasta. Se necessário, pode [personalizar o comando de arranque](#customize-startup-command).

1. **Implementação contínua**: Crie uma implementação contínua, conforme descrito na [implementação contínua do Serviço de Aplicações Azure](deploy-continuous-deployment.md) se utilizar pipelines Azure ou implantação kudu, ou [implementar para o Serviço de Aplicações utilizando ações do GitHub](deploy-github-actions.md) se utilizar ações do GitHub.

1. **Ações personalizadas**: Para executar ações dentro do recipiente de Serviço de Aplicações que acolhe a sua aplicação, como migrações de base de dados Django, pode [ligar-se ao recipiente através do SSH](configure-linux-open-ssh-session.md). Para um exemplo de execução das migrações na base de dados do Django, consulte [Tutorial: Implemente uma aplicação web Django com PostgreSQL - executar migrações de base de dados](tutorial-python-postgresql-app.md#43-run-django-database-migrations).
    - Ao utilizar a implementação contínua, pode executar essas ações utilizando comandos pós-construção, conforme descrito anteriormente no [Customize build automation](#customize-build-automation).

Com estes passos concluídos, deverá ser capaz de comprometer alterações no seu repositório de origem e ter essas atualizações automaticamente implantadas no Serviço de Aplicações.

### <a name="production-settings-for-django-apps"></a>Configurações de produção para apps django

Para um ambiente de produção como o Azure App Service, as aplicações do Django devem seguir a lista de verificação de [implementação](https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/) do Django (djangoproject.com).

O quadro seguinte descreve as definições de produção relevantes para o Azure. Estas definições são definidas no ficheiro *setting.py* da aplicação.

| Configuração de Django | Instruções para Azure |
| --- | --- |
| `SECRET_KEY` | Armazenar o valor numa definição de Serviço de Aplicações como descrito nas [definições de aplicações do Access como variáveis de ambiente.](#access-app-settings-as-environment-variables) Pode armazenar alternadamente [o valor como um "secrete" no Cofre da Chave Azure.](../key-vault/secrets/quick-create-python.md) |
| `DEBUG` | Crie uma `DEBUG` definição no Serviço de Aplicações com o valor 0 (falso), em seguida, carregue o valor como uma variável ambiental. No seu ambiente de desenvolvimento, crie uma `DEBUG` variável ambiental com o valor 1 (verdadeiro). |
| `ALLOWED_HOSTS` | Na produção, o Django requer que inclua o URL da aplicação na `ALLOWED_HOSTS` variedade de *settings.py.* Pode recuperar este URL em tempo de execução com o código, `os.environ['WEBSITE_HOSTNAME']` . O Serviço de Aplicações define automaticamente a `WEBSITE_HOSTNAME` variável ambiental para o URL da aplicação. |
| `DATABASES` | Defina as definições no Serviço de Aplicações para a ligação da base de dados e carregue-as como variáveis ambientais para povoar o [`DATABASES`](https://docs.djangoproject.com/en/3.1/ref/settings/#std:setting-DATABASES) dicionário. Pode armazenar alternadamente os valores (especialmente o nome de utilizador e a palavra-passe) como [segredos do Azure Key Vault](../key-vault/secrets/quick-create-python.md). |

## <a name="container-characteristics"></a>Características do contentor

Quando implementadas no Serviço de Aplicações, as aplicações Python funcionam dentro de um contentor Linux Docker que está definido no [repositório do Serviço de Aplicações Python GitHub.](https://github.com/Azure-App-Service/python) Pode encontrar as configurações de imagem dentro dos diretórios específicos da versão.

Este contentor tem as seguintes características:

- As aplicações são executadas com o [Servidor HTTP WSGI do Gunicorn](https://gunicorn.org/), com os argumentos adicionais `--bind=0.0.0.0 --timeout 600`.
    - Pode fornecer configurações de configuração para Gunicorn através de um ficheiro *gunicorn.conf.py* na raiz do projeto, conforme descrito na [visão geral da configuração de Gunicorn](https://docs.gunicorn.org/en/stable/configure.html#configuration-file) (docs.gunicorn.org). Pode personalizar alternadamente [o comando de arranque.](#customize-startup-command)

    - Para proteger a sua aplicação web contra ataques de DDOS acidentais ou deliberados, Gunicorn é executado atrás de um representante invertido Nginx, conforme descrito no [Deploying Gunicorn](https://docs.gunicorn.org/en/latest/deploy.html) (docs.gunicorn.org).

- Por predefinição, a imagem do contentor base inclui apenas a estrutura web flask, mas o recipiente suporta outros quadros que são compatíveis com o WSGI e compatíveis com Python 3.6+, como o Django.

- Para instalar pacotes adicionais, como o Django, crie um ficheiro [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) na raiz do seu projeto que especifique as suas dependências diretas. O Serviço de Aplicações instala essas dependências automaticamente quando implementa o seu projeto.

    O *ficheirorequirements.txt* *deve* estar na raiz do projeto para a instalação de dependências. Caso contrário, o processo de construção relata o erro: "Não foi possível encontrar setup.py ou requirements.txt; Não a funcionar pip instalar." Se encontrar este erro, verifique a localização do seu ficheiro de requisitos.

- O Serviço de Aplicações define automaticamente uma variável ambiental nomeada `WEBSITE_HOSTNAME` com o URL da aplicação web, tal como `msdocs-hello-world.azurewebsites.net` . Também define `WEBSITE_SITE_NAME` com o nome da sua app, como `msdocs-hello-world` . 
   
## <a name="container-startup-process"></a>Processo de arranque de contentores

Durante o arranque, o Serviço de Aplicações no contentor do Linux executa os seguintes passos:

1. Utilize um [comando de arranque personalizado,](#customize-startup-command)se fornecido.
2. Verifique a existência de uma [aplicação Django](#django-app)e lance Gunicorn para a ver se for detetada.
3. Verifique a existência de uma [aplicação Flask](#flask-app)e lance Gunicorn para a ver se for detetada.
4. Não se for encontrada nenhuma outra aplicação, iniciar uma aplicação predefinida incorporada no contentor.

As secções seguintes fornecem detalhes adicionais para cada opção.

### <a name="django-app"></a>Aplicação Django

Para aplicações Django, o Serviço de Aplicações procura um ficheiro chamado `wsgi.py` no código da aplicação e, em seguida, executa o Gunicorn com o seguinte comando:

```bash
# <module> is the name of the folder that contains wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Se pretender um controlo mais específico sobre o comando de arranque, utilize um [comando de arranque personalizado](#customize-startup-command), substitua-o pelo nome da pasta que contém `<module>` *wsgi.py*– e adicione um argumento se esse módulo não estiver na raiz `--chdir` do projeto. Por exemplo, se o seu *wsgi.py* estiver localizado sob *a malha/backend/config* da raiz do seu projeto, utilize os argumentos `--chdir knboard/backend config.wsgi` .

Para ativar o registo de produção, adicione os `--access-logfile` parâmetros e `--error-logfile` os parâmetros como mostrado nos exemplos para [comandos de arranque personalizados](#customize-startup-command).

### <a name="flask-app"></a>Aplicação Flask

Para o Flask, o Serviço de Aplicações procura um ficheiro chamado *application.py* ou *app.py* e inicia o Gunicorn da seguinte forma:

```bash
# If application.py
gunicorn --bind=0.0.0.0 --timeout 600 application:app

# If app.py
gunicorn --bind=0.0.0.0 --timeout 600 app:app
```

Se o módulo da aplicação principal estiver num ficheiro diferente, utilize outro nome para o objeto de aplicação ou se quiser fornecer argumentos adicionais para o Gunicorn, utilize um [comando de arranque personalizado](#customize-startup-command).

### <a name="default-behavior"></a>Comportamento predefinido

Se o Serviço de Aplicações não encontrar um comando personalizado, uma aplicação Django ou uma aplicação Flask, então executa uma aplicação apenas de leitura padrão, localizada na pasta _opt/predefinição_ e mostrada na imagem seguinte.

Se implementou o código e ainda vê a aplicação predefinida, consulte [a Resolução de Problemas - A app não aparece](#app-doesnt-appear).

[![Serviço de Aplicações predefinido na página Web do Linux](media/configure-language-python/default-python-app.png)](#app-doesnt-appear)

Mais uma vez, se espera ver uma aplicação implementada em vez da aplicação padrão, consulte [Troubleshooting - App não aparece](#app-doesnt-appear).

## <a name="customize-startup-command"></a>Personalizar o comando de arranque

Como indicado anteriormente neste artigo, pode fornecer configurações de configuração para Gunicorn através de um ficheiro *gunicorn.conf.py* na raiz do projeto, conforme descrito na [visão geral da configuração de Gunicorn](https://docs.gunicorn.org/en/stable/configure.html#configuration-file).

Se tal configuração não for suficiente, pode controlar o comportamento de arranque do recipiente fornecendo um comando de arranque personalizado ou vários comandos num ficheiro de comando de arranque. Um ficheiro de comando de arranque pode usar qualquer nome que escolha, como *startup.sh*, *startup.cmd,* *startup.txt*, e assim por diante.

Todos os comandos devem utilizar caminhos relativos para a pasta raiz do projeto.

Para especificar um ficheiro de comando ou comando de arranque:

- **Portal Azure**: selecione a página de Configuração da **aplicação** e, em seguida, selecione **definições gerais**. No campo **Comando de Arranque,** coloque o texto completo do seu comando de arranque ou o nome do seu ficheiro de comando de arranque. Em seguida, **selecione Guardar** para aplicar as alterações. Consulte [configurar as definições gerais](configure-common.md#configure-general-settings) para recipientes Linux.

- **Azure CLI**: use o comando [configurar az webapp com](/cli/azure/webapp/config#az_webapp_config_set) o `--startup-file` parâmetro para definir o comando ou ficheiro de arranque:

    ```azurecli
    az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
    ```
        
    Substitua `<custom-command>` pelo texto completo do seu comando inicial ou pelo nome do seu ficheiro de comando de arranque.
        
O Serviço de Aplicações ignora quaisquer erros que ocorram ao processar um comando ou ficheiro de arranque personalizado, e depois continua o seu processo de arranque procurando aplicações Django e Flask. Se não vir o comportamento que espera, verifique se o seu comando ou ficheiro de arranque está isento de erros e que um ficheiro de comando de arranque é implantado no Serviço de Aplicações juntamente com o seu código de aplicação. Também pode verificar os [registos de Diagnóstico](#access-diagnostic-logs) para obter informações adicionais. Verifique também o Diagnóstico da aplicação **e resolva** a página de problemas no [portal Azure.](https://portal.azure.com)

### <a name="example-startup-commands"></a>Exemplo de comandos de arranque

- **Adicionados argumentos de Gunicorn**: O exemplo a seguir adiciona `--workers=4` a uma linha de comando gunicorn para iniciar uma aplicação Django: 

    ```bash
    # <module-path> is the relative path to the folder that contains the module
    # that contains wsgi.py; <module> is the name of the folder containing wsgi.py.
    gunicorn --bind=0.0.0.0 --timeout 600 --workers=4 --chdir <module_path> <module>.wsgi
    ```    

    Para obter mais informações, veja [Executar o Gunicorn](https://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org).

- **Ativar a exploração madeireira para Django**: Adicione os `--access-logfile '-'` argumentos e os argumentos à linha de `--error-logfile '-'` comando:

    ```bash    
    # '-' for the log files means stdout for --access-logfile and stderr for --error-logfile.
    gunicorn --bind=0.0.0.0 --timeout 600 --workers=4 --chdir <module_path> <module>.wsgi --access-logfile '-' --error-logfile '-'
    ```    

    Estes registos aparecerão no [fluxo de registo do Serviço de Aplicações.](#access-diagnostic-logs)

    Para obter mais informações, consulte [a exploração madeireira de Gunicorn](https://docs.gunicorn.org/en/stable/settings.html#logging) (docs.gunicorn.org).
    
- **Módulo principal frasco personalizado**: por padrão, o Serviço de Aplicações assume que o módulo principal de uma aplicação Flask é *application.py* ou *app.py*. Se o seu módulo principal utilizar um nome diferente, então deve personalizar o comando de arranque. Por exemplo, tem uma aplicação Flask cujo módulo principal é *hello.py* e o objeto da aplicação Flask nesse ficheiro é `myapp` nomeado, então o comando é o seguinte:

    ```bash
    gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
    ```
    
    Se o seu módulo principal estiver numa subpasta, como `website`, especifique essa pasta com o argumento `--chdir`:
    
    ```bash
    gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
    ```
    
- **Utilize um servidor não-Gunicorn**: Para utilizar um servidor web diferente, como [aiohttp,](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html)utilize o comando apropriado como comando de arranque ou no ficheiro de comando de arranque:

    ```bash
    python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
    ```

## <a name="access-app-settings-as-environment-variables"></a>Aceda às definições de aplicativos como variáveis de ambiente

As definições de aplicações são valores armazenados na nuvem especificamente para a sua aplicação, conforme descrito nas [definições da aplicação Configure](configure-common.md#configure-app-settings). Estas definições estão disponíveis para o seu código de aplicação como variáveis ambientais e acedidas usando o padrão [os.environ.](https://docs.python.org/3/library/os.html#os.environ)

Por exemplo, se criou a definição de aplicações chamada `DATABASE_SERVER` , o seguinte código recupera o valor da definição:

```python
db_server = os.environ['DATABASE_SERVER']
```
    
## <a name="detect-https-session"></a>Detetar sessão HTTPS

No Serviço de Aplicações, [a rescisão de SSL](https://wikipedia.org/wiki/TLS_termination_proxy) (wikipedia.org) ocorre nos equilibristas de carga de rede, pelo que todos os pedidos HTTPS chegam à sua aplicação como pedidos HTTP não encriptados. Se a lógica da sua aplicação precisar de verificar se os pedidos do utilizador estão encriptados ou não, inspecione o `X-Forwarded-Proto` cabeçalho.

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

As estruturas web populares permitem-lhe aceder à `X-Forwarded-*` informação no seu padrão de aplicação padrão. No [CodeIgniter,](https://codeigniter.com/)o [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) verifica o valor por `X_FORWARDED_PROTO` defeito.

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

Para aceder aos registos através do portal Azure, selecione **Monitoring**  >  **Log stream** no menu do lado esquerdo para a sua aplicação.

## <a name="access-deployment-logs"></a>Registos de implementação de acesso

Quando implementa o seu código, o Serviço de Aplicações executa o processo de construção descrito anteriormente na secção Personalizar a [automatização de construção](#customize-build-automation). Como a construção funciona no seu próprio contentor, os registos de construção são armazenados separadamente dos registos de diagnóstico da aplicação.

Utilize os seguintes passos para aceder aos registos de implantação:

1. No portal Azure para a **Deployment** sua aplicação web, selecione  >  **Deployment Deployment Center (Preview)** no menu esquerdo.
1. No **separador Registares,** selecione o **ID do Compromisso** para o compromisso mais recente.
1. Na página de detalhes do **Registo** que aparece, selecione os Registos de **Espetáculos...** link que aparece ao lado de "Running oryx build...".

Criar problemas como dependências incorretas em *requirements.txt* e erros em scripts pré ou pós-construção aparecerão nestes registos. Os erros também aparecem se o seu ficheiro de requisitos não estiver exatamente nomeado *requirements.txt* ou não aparecer na pasta raiz do seu projeto.

## <a name="open-ssh-session-in-browser"></a>Abrir sessão SSH no browser

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

Quando estiver ligado com sucesso à sessão SSH, deverá ver a mensagem "SSH CONNECTION ESTABLISHED" na parte inferior da janela. Se vir erros como "SSH_CONNECTION_CLOSED" ou uma mensagem de que o contentor está a reiniciar, pode estar a impedir o início do recipiente da aplicação. Consulte [a resolução de problemas](#troubleshooting) para obter medidas para investigar possíveis problemas.

## <a name="troubleshooting"></a>Resolução de problemas

Em geral, o primeiro passo na resolução de problemas é utilizar o App Service Diagnostics:

1. No portal Azure para a sua aplicação web, selecione **Diagnosticar e resolver problemas** a partir do menu esquerdo.
1. Selecione **Disponibilidade e desempenho**.
1. Examine as informações nas opções de Registos de **Aplicação,** **Colisão de Contentores** e **Problemas de Contentores,** onde as questões mais comuns serão aparentam.

Em seguida, examine os registos de [implementação](#access-deployment-logs) e os registos da [aplicação](#access-diagnostic-logs) para obter quaisquer mensagens de erro. Estes registos identificam frequentemente problemas específicos que podem impedir a implementação de aplicações ou o arranque de apps. Por exemplo, a construção pode falhar se o seu ficheiro *requirements.txt* tiver o nome de ficheiro errado ou não estiver presente na pasta raiz do seu projeto.

As secções seguintes fornecem orientações adicionais para questões específicas.

- [App não aparece - app padrão mostra](#app-doesnt-appear)
- [App não aparece - mensagem "serviço indisponível"](#service-unavailable)
- [Não consegui encontrar setup.py ou requirements.txt](#could-not-find-setuppy-or-requirementstxt)
- [As palavras-passe não aparecem na sessão de SSH quando dactilografada](#other-issues)
- [Os comandos na sessão SSH parecem estar cortados](#other-issues)
- [Os ativos estáticos não aparecem numa aplicação do Django](#other-issues)
- [É necessária uma ligação SSL fatal](#other-issues)

#### <a name="app-doesnt-appear"></a>App não aparece

- **Vê a aplicação predefinida depois de implementar o seu próprio código de aplicação.** A [aplicação padrão](#default-behavior) aparece porque ou não implementou o seu código de aplicação para o Serviço de Aplicações, ou o Serviço de Aplicações não encontrou o seu código de aplicação e executou a aplicação padrão em vez disso.

    - Reinicie o Serviço de Aplicações, aguarde 15 a 20 segundos e verifique novamente a aplicação.
    
    - Certifique-se de que está a utilizar o Serviço de Aplicações para Linux, em vez de uma instância baseada no Windows. A partir da CLI do Azure, execute o comando `az webapp show --resource-group <resource-group-name> --name <app-name> --query kind`, ao substituir `<resource-group-name>` e `<app-name>`, respetivamente. Deverá ver `app,linux` como resultado; caso contrário, recrie o Serviço de Aplicações e escolha o Linux.
    
    - Utilize [o SSH](#open-ssh-session-in-browser) para ligar diretamente ao recipiente do Serviço de Aplicações e verificar se os seus ficheiros existem no *site/wwwroot*. Se os seus ficheiros não existirem, utilize os seguintes passos:
      1. Crie uma configuração de aplicação com `SCM_DO_BUILD_DURING_DEPLOYMENT` o valor de 1, reimplante o seu código, aguarde alguns minutos e tente aceder novamente à aplicação. Para obter mais informações sobre a criação de configurações de aplicações, consulte [configurar uma aplicação de Serviço de Aplicações no portal Azure.](configure-common.md)
      1. Reveja o seu processo de implementação, [verifique os registos de implementação,](#access-deployment-logs)corrija quaisquer erros e reimplante a aplicação.
    
    - Se os ficheiros existirem, o Serviço de Aplicações não conseguiu identificar o ficheiro de arranque específico. Verifique se a aplicação está estruturada como Serviço de Aplicações para o [Django](#django-app) ou o [Flask](#flask-app), ou utilize um [comando de arranque personalizado](#customize-startup-command).

- <a name="service-unavailable"></a>**Vê a mensagem "Serviço Indisponível" no navegador.** O navegador tem cronometrado à espera de uma resposta do App Service, o que indica que o Serviço de Aplicações iniciou o servidor Gunicorn, mas a própria app não começou. Esta condição pode indicar que os argumentos de Gunicorn estão incorretos, ou que há um erro no código da aplicação.

    - Atualize o browser, especialmente se estiver a utilizar os escalões de preços mais baixos no seu Plano do Serviço de Aplicações. A aplicação poderá demorar mais tempo a iniciar quando utilizar, por exemplo, escalões gratuitos e responde depois de atualizar o browser.

    - Verifique se a aplicação está estruturada como Serviço de Aplicações para o [Django](#django-app) ou o [Flask](#flask-app), ou utilize um [comando de arranque personalizado](#customize-startup-command).

    - Examine o fluxo de registo de [aplicações](#access-diagnostic-logs) para obter quaisquer mensagens de erro. Os registos mostrarão quaisquer erros no código da aplicação.

#### <a name="could-not-find-setuppy-or-requirementstxt"></a>Não consegui encontrar setup.py ou requirements.txt

- **O fluxo de registo mostra "Não foi possível encontrar setup.py ou requirements.txt; Não funcionando a instalação do pip."**: O processo de construção oryx não conseguiu encontrar o seu ficheiro *requirements.txt.*

    - Ligue-se ao contentor da aplicação web via [SSH](#open-ssh-session-in-browser) e verifique se *requirements.txt* é nomeado corretamente e existe diretamente no *site/wwwroot*. Se não existir, faça do site que o ficheiro existe no seu repositório e está incluído na sua implantação. Se existir numa pasta separada, mova-a para a raiz.

#### <a name="other-issues"></a>Outros problemas

- **As palavras-passe não aparecem na sessão SSH quando escritas**: Por razões de segurança, a sessão SSH mantém a sua palavra-passe escondida à medida que escreve. Os caracteres estão a ser gravados, no entanto, por isso digite a sua palavra-passe como de costume e prima **Enter** quando for feito.

- **Os comandos na sessão SSH parecem estar cortados**: O editor pode não ser comandos de embrulho de palavras, mas ainda devem funcionar corretamente.

- **Os ativos estáticos não aparecem numa aplicação do Django**: Certifique-se de que ativou o [módulo whitenoise](http://whitenoise.evans.io/en/stable/django.html)

- **Vê a mensagem: "A Ligação SSL Fatal é necessária"**: Verifique quaisquer nomes de utilizador e palavras-passe utilizados para aceder a recursos (como bases de dados) a partir da aplicação.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: App Python com PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Tutorial: Implantação do repositório de contentores privados](tutorial-custom-container.md?pivots=container-linux)

> [!div class="nextstepaction"]
> [FAQ do Serviço de Aplicações no Linux](faq-app-service-linux.md)