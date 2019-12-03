---
title: Configurar aplicativos Python do Linux
description: Saiba como configurar um contêiner Python pré-compilado para seu aplicativo. Este artigo mostra as tarefas de configuração mais comuns.
ms.topic: quickstart
ms.date: 03/28/2019
ms.reviewer: astay; kraigb
ms.custom: seodec18
ms.openlocfilehash: b8de6df5761baef79310062614f578a92f17b826
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670485"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Configurar um aplicativo Linux Python para o Azure App Service

Este artigo descreve como [Azure app serviço](app-service-linux-intro.md) executa aplicativos Python e como você pode personalizar o comportamento do serviço de aplicativo quando necessário. Os aplicativos Python devem ser implantados com todos os módulos [Pip](https://pypi.org/project/pip/) necessários.

O mecanismo de implantação do serviço de aplicativo ativa automaticamente um ambiente virtual e executa `pip install -r requirements.txt` para você quando você implanta um [repositório git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)ou um [pacote zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) com processos de compilação ativados.

Este guia fornece os principais conceitos e instruções para desenvolvedores de Python que usam um contêiner do Linux interno no serviço de aplicativo. Se você nunca usou Azure App serviço, deverá seguir o tutorial de [início rápido do Python](quickstart-python.md) e [Python com PostgreSQL](tutorial-python-postgresql-app.md) primeiro.

> [!NOTE]
> Atualmente, o Linux é a opção recomendada para executar aplicativos Python no serviço de aplicativo. Para obter informações sobre a opção do Windows, consulte [Python no tipo Windows do serviço de aplicativo](https://docs.microsoft.com/visualstudio/python/managing-python-on-azure-app-service).
>

## <a name="show-python-version"></a>Mostrar versão do Python

Para mostrar a versão atual do Python, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões do Python com suporte, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PYTHON
```

Você pode executar uma versão sem suporte do Python criando sua própria imagem de contêiner em vez disso. Para obter mais informações, consulte [usar uma imagem personalizada do Docker](tutorial-custom-docker-image.md).

## <a name="set-python-version"></a>Definir versão do Python

Execute o seguinte comando no [Cloud Shell](https://shell.azure.com) para definir a versão do Python como 3,7:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
```

## <a name="container-characteristics"></a>Características do contentor

Os aplicativos Python implantados no serviço de aplicativo no Linux são executados em um contêiner do Docker que é definido no repositório GitHub, [python 3,6](https://github.com/Azure-App-Service/python/tree/master/3.6.6) ou [Python 3,7](https://github.com/Azure-App-Service/python/tree/master/3.7.0).

Este contentor tem as seguintes características:

- As aplicações são executadas com o [Servidor HTTP WSGI do Gunicorn](https://gunicorn.org/), com os argumentos adicionais `--bind=0.0.0.0 --timeout 600`.

- Por predefinição, a imagem base inclui a arquitetura Web Flask, mas o contentor suporta outras arquiteturas compatíveis com o WSGI e o Python 3.7, como o Django.

- Para instalar pacotes adicionais, como o Django, crie um ficheiro [*requirements.txt* ](https://pip.pypa.io/en/stable/user_guide/#requirements-files) na raiz do projeto com `pip freeze > requirements.txt`. Em seguida, publique o projeto no Serviço de Aplicações com a implementação do Git, que executa automaticamente `pip install -r requirements.txt` no contentor para instalar as dependências da aplicação.

## <a name="container-startup-process"></a>Processo de inicialização do contêiner

Durante o arranque, o Serviço de Aplicações no contentor do Linux executa os seguintes passos:

1. Use um [comando de inicialização personalizado](#customize-startup-command), se fornecido.
2. Verifique a existência de um [aplicativo Django](#django-app)e inicie o Gunicorn para ele, se detectado.
3. Verifique a existência de um [aplicativo Flask](#flask-app)e inicie o Gunicorn para ele, se detectado.
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

Para Flask, o serviço de aplicativo procura um arquivo chamado *Application.py* ou *app.py* e inicia Gunicorn da seguinte maneira:

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

## <a name="customize-startup-command"></a>Personalizar o comando de inicialização

Pode controlar o comportamento de arranque do contentor ao fornecer um comando de arranque do Gunicorn personalizado. Para fazer isso, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

Por exemplo, se você tiver um aplicativo Flask cujo módulo principal é *Hello.py* e o objeto de aplicativo Flask nesse arquivo é denominado `myapp`, *\<> de comando personalizado* é o seguinte:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
```

Se o seu módulo principal estiver numa subpasta, como `website`, especifique essa pasta com o argumento `--chdir`:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
```

Você também pode adicionar argumentos adicionais para Gunicorn para *\<> de comando personalizado*, como `--workers=4`. Para obter mais informações, veja [Executar o Gunicorn](https://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org).

Para usar um servidor não Gunicorn, como [aiohttp](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html), você pode substituir *\<> de comando personalizado* por algo parecido com este:

```bash
python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
```

> [!Note]
> O Serviço de Aplicações ignora todos os erros ocorridos durante o processamento de um ficheiro de comando personalizado e continua o processo de arranque ao procurar as aplicações Django e Flask. Se não vir o comportamento esperado, verifique se o ficheiro de arranque está implementado no Serviço de Aplicações e não contém quaisquer erros.

## <a name="access-environment-variables"></a>Variáveis de ambiente de acesso

No serviço de aplicativo, você pode [definir configurações de aplicativo](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) fora do seu código do aplicativo. Em seguida, você pode acessá-los usando o padrão [. Environ padrão do sistema operacional](https://docs.python.org/3/library/os.html#os.environ) . Por exemplo, para acessar uma configuração de aplicativo chamada `WEBSITE_SITE_NAME`, use o seguinte código:

```python
os.environ['WEBSITE_SITE_NAME']
```

## <a name="detect-https-session"></a>Detectar sessão HTTPS

No serviço de aplicativo, a [terminação SSL](https://wikipedia.org/wiki/TLS_termination_proxy) ocorre nos balanceadores de carga de rede, portanto, todas as solicitações HTTPS atingem seu aplicativo como solicitações HTTP não criptografadas. Se a lógica do aplicativo precisar verificar se as solicitações do usuário estão criptografadas ou não, inspecione o cabeçalho `X-Forwarded-Proto`.

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

Estruturas da Web populares permitem que você acesse as informações de `X-Forwarded-*` em seu padrão de aplicativo padrão. No [CodeIgniter](https://codeigniter.com/), o [is_https ()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) verifica o valor de `X_FORWARDED_PROTO` por padrão.

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Abrir sessão SSH no navegador

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Resolução de problemas

- **Vê a aplicação predefinida depois de implementar o seu próprio código de aplicação.** O aplicativo padrão é exibido porque você não implantou o código do aplicativo no serviço de aplicativo ou o serviço de aplicativo não encontrou o código do aplicativo e executou o aplicativo padrão em vez disso.
- Reinicie o Serviço de Aplicações, aguarde 15 a 20 segundos e verifique novamente a aplicação.
- Certifique-se de que está a utilizar o Serviço de Aplicações para Linux, em vez de uma instância baseada no Windows. A partir da CLI do Azure, execute o comando `az webapp show --resource-group <resource_group_name> --name <app_service_name> --query kind`, ao substituir `<resource_group_name>` e `<app_service_name>`, respetivamente. Deverá ver `app,linux` como resultado; caso contrário, recrie o Serviço de Aplicações e escolha o Linux.
- Utilize o SSH ou a consola Kudu para ligar diretamente ao Serviço de Aplicações e verifique se os ficheiros existem em *site/wwwroot*. Se os ficheiros não existirem, reveja o processo de implementação e volte a implementar a aplicação.
- Se os ficheiros existirem, o Serviço de Aplicações não conseguiu identificar o ficheiro de arranque específico. Verifique se a aplicação está estruturada como Serviço de Aplicações para o [Django](#django-app) ou o [Flask](#flask-app), ou utilize um [comando de arranque personalizado](#customize-startup-command).
- **Vê a mensagem "Serviço Indisponível" no browser.** O browser esgotou o tempo limite ao aguardar uma resposta do Serviço de Aplicações, o que indica que o Serviço de Aplicações iniciou o servidor do Gunicorn, mas os argumentos que especificam o código da aplicação estão incorretos.
- Atualize o browser, especialmente se estiver a utilizar os escalões de preços mais baixos no seu Plano do Serviço de Aplicações. A aplicação poderá demorar mais tempo a iniciar quando utilizar, por exemplo, escalões gratuitos e responde depois de atualizar o browser.
- Verifique se a aplicação está estruturada como Serviço de Aplicações para o [Django](#django-app) ou o [Flask](#flask-app), ou utilize um [comando de arranque personalizado](#customize-startup-command).
- [Acessar o fluxo de log](#access-diagnostic-logs).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: aplicativo Python com PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Tutorial: implantar do repositório de contêiner privado](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Perguntas frequentes sobre o serviço de aplicativo Linux](app-service-linux-faq.md)
