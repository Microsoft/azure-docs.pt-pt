---
title: Configure aplicações Linux Python
description: Saiba como configurar um recipiente Python pré-construído para a sua aplicação. Este artigo mostra as tarefas de configuração mais comuns.
ms.topic: quickstart
ms.date: 03/28/2019
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18, devx-track-python
ms.openlocfilehash: 0a0422dadd1c9b59e63da00b27a22fc7020bff93
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87849969"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Configure uma aplicação Linux Python para o Azure App Service

Este artigo descreve como o [Azure App Service](app-service-linux-intro.md) gere as aplicações Python e como pode personalizar o comportamento do Serviço de Aplicações quando necessário. As aplicações Python devem ser implementadas com todos os módulos [pip](https://pypi.org/project/pip/) necessários.

O motor de implementação do Serviço de Aplicações ativa automaticamente um ambiente virtual e funciona `pip install -r requirements.txt` para si quando implementa um [repositório Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), ou um pacote [Zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) com processos de construção ligados.

Este guia fornece conceitos e instruções fundamentais para os desenvolvedores de Python que usam um recipiente Linux incorporado no Serviço de Aplicações. Se nunca usou o Azure App Service, deve seguir primeiro o [quickstart python](quickstart-python.md) e [python com o tutorial PostgreSQL.](tutorial-python-postgresql-app.md)

> [!NOTE]
> O Linux é atualmente a opção recomendada para executar aplicações Python no Serviço de Aplicações. Para obter informações sobre a opção Windows, consulte [Python no sabor Windows do Serviço de Aplicações.](https://docs.microsoft.com/visualstudio/python/managing-python-on-azure-app-service)
>

## <a name="show-python-version"></a>Mostrar versão Python

Para mostrar a versão atual python, executar o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões Python suportadas, executar o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PYTHON
```

Em vez disso, pode executar uma versão não suportada do Python construindo a sua própria imagem de contentor. Para obter mais informações, consulte [a utilização de uma imagem personalizada do Docker.](tutorial-custom-docker-image.md)

## <a name="set-python-version"></a>Definir versão Python

Executar o seguinte comando na [Cloud Shell](https://shell.azure.com) para definir a versão Python para 3.7:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
```

## <a name="customize-build-automation"></a>Personalize a automatização de construção

Se implementar a sua aplicação utilizando pacotes Git ou zip com automatização de construção ligada, o Serviço de Aplicações constrói passos de automação através da seguinte sequência:

1. Executar script personalizado se especificado por `PRE_BUILD_SCRIPT_PATH` .
1. Execute o `pip install -r requirements.txt`.
1. Se *manage.py* for encontrado na raiz do repositório, corra *manage.py coleta*. No entanto, se `DISABLE_COLLECTSTATIC` estiver definido para , este passo é `true` ignorado.
1. Executar script personalizado se especificado por `POST_BUILD_SCRIPT_PATH` .

`PRE_BUILD_COMMAND`, `POST_BUILD_COMMAND` e `DISABLE_COLLECTSTATIC` são variáveis ambientais que estão vazias por defeito. Para executar comandos pré-construção, defina `PRE_BUILD_COMMAND` . Para executar comandos pós-construção, defina `POST_BUILD_COMMAND` . Para desativar a coletividade em execução ao construir aplicações Django, desabrocora `DISABLE_COLLECTSTATIC=true` .

O exemplo a seguir especifica as duas variáveis a uma série de comandos, separados por vírgulas.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Para variáveis ambientais adicionais para personalizar a automatização de construção, consulte [a configuração oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Para obter mais informações sobre como o App Service funciona e constrói aplicações Python em Linux, consulte [a documentação do Oryx: Como as aplicações Python são detetadas e construídas.](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/python.md)

## <a name="container-characteristics"></a>Características do contentor

As aplicações Python implementadas para o Serviço de Aplicações em Linux funcionam dentro de um contentor Docker que está definido no [repositório do Serviço de Aplicações Python GitHub.](https://github.com/Azure-App-Service/python) Pode encontrar as configurações de imagem dentro dos diretórios específicos da versão.

Este contentor tem as seguintes características:

- As aplicações são executadas com o [Servidor HTTP WSGI do Gunicorn](https://gunicorn.org/), com os argumentos adicionais `--bind=0.0.0.0 --timeout 600`.

- Por predefinição, a imagem base inclui a arquitetura Web Flask, mas o contentor suporta outras arquiteturas compatíveis com o WSGI e o Python 3.7, como o Django.

- Para instalar pacotes adicionais, como o Django, crie um ficheiro [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) na raiz do projeto com `pip freeze > requirements.txt`. Em seguida, publique o projeto no Serviço de Aplicações com a implementação do Git, que executa automaticamente `pip install -r requirements.txt` no contentor para instalar as dependências da aplicação.

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
# <module> is the path to the folder that contains wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Se quiser um controlo mais específico sobre o comando de arranque, utilize um [comando de arranque personalizado](#customize-startup-command) e substitua `<module>` pelo nome do módulo que contém *wsgi.py*.

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

![Serviço de Aplicações predefinido na página Web do Linux](media/how-to-configure-python/default-python-app.png)

## <a name="customize-startup-command"></a>Personalizar o comando de arranque

Pode controlar o comportamento de arranque do contentor ao fornecer um comando de arranque do Gunicorn personalizado. Para isso, executando o seguinte comando na [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

Por exemplo, se tiver uma aplicação Flask cujo módulo principal é *hello.py* e o objeto da aplicação Flask nesse ficheiro for `myapp` nomeado, então *\<custom-command>* é o seguinte:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
```

Se o seu módulo principal estiver numa subpasta, como `website`, especifique essa pasta com o argumento `--chdir`:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
```

Também pode adicionar quaisquer argumentos adicionais para gunicorn *\<custom-command>* para , tais como `--workers=4` . Para obter mais informações, veja [Executar o Gunicorn](https://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org).

Para utilizar um servidor não-Gunicorn, como [aiohttp,](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html)pode substituir *\<custom-command>* por algo assim:

```bash
python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
```

> [!Note]
> O Serviço de Aplicações ignora todos os erros ocorridos durante o processamento de um ficheiro de comando personalizado e continua o processo de arranque ao procurar as aplicações Django e Flask. Se não vir o comportamento esperado, verifique se o ficheiro de arranque está implementado no Serviço de Aplicações e não contém quaisquer erros.

## <a name="access-environment-variables"></a>Aceder a variáveis de ambiente

No Serviço de Aplicações, pode [definir definições](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) de aplicações fora do seu código de aplicação. Em seguida, pode acessá-los usando o padrão [os.environ.](https://docs.python.org/3/library/os.html#os.environ) Por exemplo, para aceder a uma configuração de aplicação chamada `WEBSITE_SITE_NAME` , use o seguinte código:

```python
os.environ['WEBSITE_SITE_NAME']
```

## <a name="detect-https-session"></a>Detetar sessão HTTPS

No Serviço de Aplicações, a [rescisão de SSL](https://wikipedia.org/wiki/TLS_termination_proxy) ocorre nos equilibristas de carga de rede, pelo que todos os pedidos HTTPS chegam à sua aplicação como pedidos HTTP não encriptados. Se a lógica da sua aplicação precisar de verificar se os pedidos do utilizador estão encriptados ou não, inspecione o `X-Forwarded-Proto` cabeçalho.

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

As estruturas web populares permitem-lhe aceder à `X-Forwarded-*` informação no seu padrão de aplicação padrão. No [CodeIgniter,](https://codeigniter.com/)o [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) verifica o valor por `X_FORWARDED_PROTO` defeito.

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH aberto no navegador

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Resolução de problemas

- **Vê a aplicação predefinida depois de implementar o seu próprio código de aplicação.** A aplicação padrão aparece porque ou não implementou o seu código de aplicação para o Serviço de Aplicações, ou o Serviço de Aplicações não encontrou o seu código de aplicação e executou a aplicação padrão em vez disso.
- Reinicie o Serviço de Aplicações, aguarde 15 a 20 segundos e verifique novamente a aplicação.
- Certifique-se de que está a utilizar o Serviço de Aplicações para Linux, em vez de uma instância baseada no Windows. A partir da CLI do Azure, execute o comando `az webapp show --resource-group <resource_group_name> --name <app_service_name> --query kind`, ao substituir `<resource_group_name>` e `<app_service_name>`, respetivamente. Deverá ver `app,linux` como resultado; caso contrário, recrie o Serviço de Aplicações e escolha o Linux.
- Utilize o SSH ou a consola Kudu para ligar diretamente ao Serviço de Aplicações e verifique se os ficheiros existem em *site/wwwroot*. Se os ficheiros não existirem, reveja o processo de implementação e volte a implementar a aplicação.
- Se os ficheiros existirem, o Serviço de Aplicações não conseguiu identificar o ficheiro de arranque específico. Verifique se a aplicação está estruturada como Serviço de Aplicações para o [Django](#django-app) ou o [Flask](#flask-app), ou utilize um [comando de arranque personalizado](#customize-startup-command).
- **Vê a mensagem "Serviço Indisponível" no browser.** O browser esgotou o tempo limite ao aguardar uma resposta do Serviço de Aplicações, o que indica que o Serviço de Aplicações iniciou o servidor do Gunicorn, mas os argumentos que especificam o código da aplicação estão incorretos.
- Atualize o browser, especialmente se estiver a utilizar os escalões de preços mais baixos no seu Plano do Serviço de Aplicações. A aplicação poderá demorar mais tempo a iniciar quando utilizar, por exemplo, escalões gratuitos e responde depois de atualizar o browser.
- Verifique se a aplicação está estruturada como Serviço de Aplicações para o [Django](#django-app) ou o [Flask](#flask-app), ou utilize um [comando de arranque personalizado](#customize-startup-command).
- [Aceda ao fluxo de registo](#access-diagnostic-logs).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: App Python com PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Tutorial: Implantação do repositório de contentores privados](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Serviço de Aplicações Linux FAQ](app-service-linux-faq.md)
