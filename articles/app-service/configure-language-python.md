---
title: Configure aplicações Linux Python
description: Saiba como configurar o recipiente Python no qual são executadas aplicações web, utilizando tanto o portal Azure como o Azure CLI.
ms.topic: quickstart
ms.date: 10/06/2020
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18, devx-track-python
ms.openlocfilehash: b489f7daebc9232088020948752c3792dca65095
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018751"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Configure uma aplicação Linux Python para o Azure App Service

Este artigo descreve como o [Azure App Service](overview.md) gere as aplicações Python e como pode personalizar o comportamento do Serviço de Aplicações quando necessário. As aplicações Python devem ser implementadas com todos os módulos [pip](https://pypi.org/project/pip/) necessários.

O motor de implementação do Serviço de Aplicações ativa automaticamente um ambiente virtual e funciona `pip install -r requirements.txt` para si quando implementa um [repositório Git](deploy-local-git.md), ou um pacote [zip](deploy-zip.md).

Este guia fornece conceitos e instruções fundamentais para os desenvolvedores de Python que usam um recipiente Linux incorporado no Serviço de Aplicações. Se nunca usou o Azure App Service, siga primeiro o [quickstart Python](quickstart-python.md) e [python com o tutorial postgreSQL.](tutorial-python-postgresql-app.md)

Pode utilizar o [portal Azure](https://portal.azure.com) ou o CLI Azure para configuração:

- **Portal Azure**, use a página de Configuração de **Configurações**da aplicação  >  **Configuration** como descrito no [Configure uma aplicação de Serviço de Aplicações no portal Azure](configure-common.md).

- **Azure CLI:** tem duas opções.

    - Executar comandos no [Azure Cloud Shell,](../cloud-shell/overview.md)que pode abrir utilizando o botão **Try It** no canto superior direito dos blocos de código.
    - Executar comandos localmente instalando a versão mais recente do [Azure CLI,](/cli/azure/install-azure-cli)em seguida, inicie sessão no Azure usando [login az](/cli/azure/reference-index#az-login).
    
> [!NOTE]
> O Linux é atualmente a opção recomendada para executar aplicações Python no Serviço de Aplicações. Para obter informações sobre a opção Windows, consulte [Python no sabor Windows do Serviço de Aplicações.](/visualstudio/python/managing-python-on-azure-app-service)

## <a name="configure-python-version"></a>Versão Configure Python

- **Portal Azure**: utilize o separador **definições gerais** na página **de Configuração,** conforme descrito nas [definições gerais de Configuração](configure-common.md#configure-general-settings) para recipientes Linux.

- **Azure CLI:**

    -  Mostre a versão atual python com [az webapp config show](/cli/azure/webapp/config#az_webapp_config_show):
    
        ```azurecli-interactive
        az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
        ```
        
        Substitua `<resource-group-name>` e `<app-name>` pelos nomes apropriados para a sua aplicação web.
    
    - Desconfig de versão Python com [conjunto de config do webapp az](/cli/azure/webapp/config#az_webapp_config_set)
        
        ```azurecli-interactive
        az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
        ```
    
    - Mostrar todas as versões Python que são suportadas no Azure App Service com [az webapp list-runtimes](/cli/azure/webapp#az_webapp_list_runtimes):
    
        ```azurecli-interactive
        az webapp list-runtimes --linux | grep PYTHON
        ```
    
Em vez disso, pode executar uma versão não suportada do Python construindo a sua própria imagem de contentor. Para obter mais informações, veja [Utilizar uma imagem personalizada do Docker](tutorial-custom-container.md?pivots=container-linux).

<!-- <a> element here to preserve external links-->
<a name="access-environment-variables"></a>

## <a name="customize-build-automation"></a>Personalize a automatização de construção

O sistema de construção do App Service, chamado Oryx, executa os seguintes passos quando implementa a sua aplicação utilizando pacotes Git ou zip:

1. Executar um script pré-construção personalizado se especificado pela `PRE_BUILD_COMMAND` definição.
1. Execute `pip install -r requirements.txt`. O * ficheirorequirements.txt* deve estar presente na pasta raiz do projeto. Caso contrário, o processo de construção relata o erro: "Não foi possível encontrar setup.py ou requirements.txt; Não a funcionar pip instalar."
1. Se *manage.py* for encontrado na raiz do repositório (indicando uma aplicação Django), executar *manage.py a collectásta .* No entanto, se a `DISABLE_COLLECTSTATIC` regulação `true` for, este passo é ignorado.
1. Executar script pós-construção personalizado se especificado pela `POST_BUILD_COMMAND` definição.

Por predefinição, as `PRE_BUILD_COMMAND` `POST_BUILD_COMMAND` definições e `DISABLE_COLLECTSTATIC` as definições estão vazias. 

- Para desativar a coleta em execução ao construir aplicações Django, defina a `DISABLE_COLLECTSTATIC` definição como verdadeira.

- Para executar comandos de pré-construção, defina a `PRE_BUILD_COMMAND` definição para conter um comando, `echo Pre-build command` como, por exemplo, ou um caminho para um ficheiro de script em relação à raiz do seu projeto, como `scripts/prebuild.sh` . Todos os comandos devem utilizar caminhos relativos para a pasta raiz do projeto.

- Para executar comandos pós-construção, defina a `POST_BUILD_COMMAND` definição para conter um comando, `echo Post-build command` como, por exemplo, ou um caminho para um ficheiro de script em relação à raiz do seu projeto, como `scripts/postbuild.sh` . Todos os comandos devem utilizar caminhos relativos para a pasta raiz do projeto.

Para configurações adicionais que personalizem a automatização de construção, consulte [a configuração oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md). 

Para obter mais informações sobre como o App Service funciona e constrói aplicações Python em Linux, veja [como a Oryx deteta e constrói aplicações Python.](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/python.md)

> [!NOTE]
> As `PRE_BUILD_SCRIPT_PATH` `POST_BUILD_SCRIPT_PATH` configurações são idênticas e são `PRE_BUILD_COMMAND` `POST_BUILD_COMMAND` suportadas para fins antigos.
> 
> Uma definição chamada `SCM_DO_BUILD_DURING_DEPLOYMENT` , se contiver `true` ou 1, ativa uma construção oryx acontece durante a implantação. A definição é verdadeira quando se implanta o recurso ao git, ao comando Azure CLI `az webapp up` e ao Código do Estúdio Visual.

> [!NOTE]
> Utilize sempre caminhos relativos em todos os scripts pré e pós-construção porque o recipiente de construção em que o Oryx funciona é diferente do recipiente de tempo de execução em que a aplicação funciona. Nunca confie na colocação exata da pasta do projeto da sua aplicação dentro do recipiente (por exemplo, que é colocada no *local/wwwroot).*

## <a name="production-settings-for-django-apps"></a>Configurações de produção para apps django

Para um ambiente de produção como o Azure App Service, as aplicações do Django devem seguir a lista de verificação de [implementação](https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/) do Django (djangoproject.com).

O quadro seguinte descreve as definições de produção relevantes para o Azure. Estas definições são definidas no ficheiro *setting.py* da aplicação.

| Configuração de Django | Instruções para Azure |
| --- | --- |
| `SECRET_KEY` | Armazenar o valor numa definição de Serviço de Aplicações como descrito nas [definições de aplicações do Access como variáveis de ambiente.](#access-app-settings-as-environment-variables) Pode armazenar alternadamente [o valor como um "secrete" no Cofre da Chave Azure.](/azure/key-vault/secrets/quick-create-python) |
| `DEBUG` | Crie uma `DEBUG` definição no Serviço de Aplicações com o valor 0 (falso), em seguida, carregue o valor como uma variável ambiental. No seu ambiente de desenvolvimento, crie uma `DEBUG` variável ambiental com o valor 1 (verdadeiro). |
| `ALLOWED_HOSTS` | Na produção, o Django requer que inclua o URL da aplicação na `ALLOWED_HOSTS` variedade de *settings.py.* Pode recuperar este URL em tempo de execução com o código, `os.environ['WEBSITE_HOSTNAME']` . O Serviço de Aplicações define automaticamente a `WEBSITE_HOSTNAME` variável ambiental para o URL da aplicação. |
| `DATABASES` | Defina as definições no Serviço de Aplicações para a ligação da base de dados e carregue-as como variáveis ambientais para povoar o [`DATABASES`](https://docs.djangoproject.com/en/3.1/ref/settings/#std:setting-DATABASES) dicionário. Pode armazenar alternadamente os valores (especialmente o nome de utilizador e a palavra-passe) como [segredos do Azure Key Vault](/azure/key-vault/secrets/quick-create-python). |

## <a name="container-characteristics"></a>Características do contentor

Quando implementadas no Serviço de Aplicações, as aplicações Python funcionam dentro de um contentor Linux Docker que está definido no [repositório do Serviço de Aplicações Python GitHub.](https://github.com/Azure-App-Service/python) Pode encontrar as configurações de imagem dentro dos diretórios específicos da versão.

Este contentor tem as seguintes características:

- As aplicações são executadas com o [Servidor HTTP WSGI do Gunicorn](https://gunicorn.org/), com os argumentos adicionais `--bind=0.0.0.0 --timeout 600`.
    - Pode fornecer configurações de configuração para Gunicorn através de um ficheiro *gunicorn.conf.py* na raiz do projeto, conforme descrito na [visão geral da configuração de Gunicorn](https://docs.gunicorn.org/en/stable/configure.html#configuration-file) (docs.gunicorn.org). Pode personalizar alternadamente [o comando de arranque.](#customize-startup-command)

    - Para proteger a sua aplicação web contra ataques de DDOS acidentais ou deliberados, Gunicorn é executado atrás de um representante invertido Nginx, conforme descrito no [Deploying Gunicorn](https://docs.gunicorn.org/en/latest/deploy.html) (docs.gunicorn.org).

- Por predefinição, a imagem do contentor base inclui apenas a estrutura web flask, mas o recipiente suporta outros quadros que são compatíveis com o WSGI e compatíveis com Python 3.6+, como o Django.

- Para instalar pacotes adicionais, como o Django, crie um ficheiro [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) na raiz do seu projeto que especifique as suas dependências diretas. O Serviço de Aplicações instala essas dependências automaticamente quando implementa o seu projeto.

    O * ficheirorequirements.txt* *deve* estar na raiz do projeto para a instalação de dependências. Caso contrário, o processo de construção relata o erro: "Não foi possível encontrar setup.py ou requirements.txt; Não a funcionar pip instalar." Se encontrar este erro, verifique a localização do seu ficheiro de requisitos.

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

Se o Serviço de Aplicações não encontrar um comando personalizado, uma aplicação Django ou uma aplicação Flask, executa uma aplicação só de leitura predefinida, localizada na pasta _opt/defaultsite_. A aplicação predefinida é apresentada da seguinte forma:

![Serviço de Aplicações predefinido na página Web do Linux](media/configure-language-python/default-python-app.png)

## <a name="customize-startup-command"></a>Personalizar o comando de arranque

Como indicado anteriormente neste artigo, pode fornecer configurações de configuração para Gunicorn através de um ficheiro *gunicorn.conf.py* na raiz do projeto, conforme descrito na [visão geral da configuração de Gunicorn](https://docs.gunicorn.org/en/stable/configure.html#configuration-file).

Se tal configuração não for suficiente, pode controlar o comportamento de arranque do recipiente fornecendo um comando de arranque personalizado ou vários comandos num ficheiro de comando de arranque. Um ficheiro de comando de arranque pode usar qualquer nome que escolha, como *startup.sh*, *startup.cmd,* *startup.txt*, e assim por diante.

Todos os comandos devem utilizar caminhos relativos para a pasta raiz do projeto.

Para especificar um ficheiro de comando ou comando de arranque:

- **Portal Azure**: selecione a página de Configuração da **aplicação** e, em seguida, selecione **definições gerais**. No campo **Comando de Arranque,** coloque o texto completo do seu comando de arranque ou o nome do seu ficheiro de comando de arranque. Em seguida, **selecione Guardar** para aplicar as alterações. Consulte [configurar as definições gerais](configure-common.md#configure-general-settings) para recipientes Linux.

- **Azure CLI**: use o comando [configurar az webapp com](/cli/azure/webapp/config#az_webapp_config_set) o `--startup-file` parâmetro para definir o comando ou ficheiro de arranque:

    ```azurecli-interactive
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

## <a name="open-ssh-session-in-browser"></a>Abrir sessão SSH no browser

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Resolução de problemas

- **Vê a aplicação predefinida depois de implementar o seu próprio código de aplicação.** A aplicação padrão aparece porque ou não implementou o seu código de aplicação para o Serviço de Aplicações, ou o Serviço de Aplicações não encontrou o seu código de aplicação e executou a aplicação padrão em vez disso.

    - Reinicie o Serviço de Aplicações, aguarde 15 a 20 segundos e verifique novamente a aplicação.
    
    - Certifique-se de que está a utilizar o Serviço de Aplicações para Linux, em vez de uma instância baseada no Windows. A partir da CLI do Azure, execute o comando `az webapp show --resource-group <resource-group-name> --name <app-name> --query kind`, ao substituir `<resource-group-name>` e `<app-service-name>`, respetivamente. Deverá ver `app,linux` como resultado; caso contrário, recrie o Serviço de Aplicações e escolha o Linux.
    
    - Utilize o SSH ou a consola Kudu para ligar diretamente ao Serviço de Aplicações e verifique se os ficheiros existem em *site/wwwroot*. Se os ficheiros não existirem, reveja o processo de implementação e volte a implementar a aplicação.
    
    - Se os ficheiros existirem, o Serviço de Aplicações não conseguiu identificar o ficheiro de arranque específico. Verifique se a aplicação está estruturada como Serviço de Aplicações para o [Django](#django-app) ou o [Flask](#flask-app), ou utilize um [comando de arranque personalizado](#customize-startup-command).

- **Vê a mensagem "Serviço Indisponível" no browser.** O browser esgotou o tempo limite ao aguardar uma resposta do Serviço de Aplicações, o que indica que o Serviço de Aplicações iniciou o servidor do Gunicorn, mas os argumentos que especificam o código da aplicação estão incorretos.

    - Atualize o browser, especialmente se estiver a utilizar os escalões de preços mais baixos no seu Plano do Serviço de Aplicações. A aplicação poderá demorar mais tempo a iniciar quando utilizar, por exemplo, escalões gratuitos e responde depois de atualizar o browser.

    - Verifique se a aplicação está estruturada como Serviço de Aplicações para o [Django](#django-app) ou o [Flask](#flask-app), ou utilize um [comando de arranque personalizado](#customize-startup-command).

    - Examine o [fluxo de registo](#access-diagnostic-logs) para obter quaisquer mensagens de erro.

- **O fluxo de registo mostra "Não foi possível encontrar setup.py ou requirements.txt; Não funcionando a instalação do pip."**: O processo de construção oryx não conseguiu encontrar o seu ficheiro *requirements.txt.*

    - Utilize o SSH ou a consola Kudu para ligar diretamente ao Serviço de Aplicações e verificar se *requirements.txt* existe diretamente no *site/wwwroot*. Se não existir, faça do site que o ficheiro existe no seu repositório e está incluído na sua implantação. Se existir numa pasta separada, mova-a para a raiz.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: App Python com PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Tutorial: Implantação do repositório de contentores privados](tutorial-custom-container.md?pivots=container-linux)

> [!div class="nextstepaction"]
> [FAQ do Serviço de Aplicações no Linux](faq-app-service-linux.md)
