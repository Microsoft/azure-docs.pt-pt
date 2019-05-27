---
title: Configurar aplicações Python - serviço de aplicações do Azure
description: Este tutorial descreve as opções para a criação e a configuração de aplicações Python para o Serviço de Aplicações do Azure no Linux.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: astay;cephalin;kraigb
ms.custom: seodec18
ms.openlocfilehash: ad2ea32749c6556d17460b2a16ed41bbaa2ec62e
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956147"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Configurar uma aplicação Python de Linux para o serviço de aplicações do Azure

Este artigo descreve como [App Service do Azure](app-service-linux-intro.md) executa aplicações Python, e como pode personalizar o comportamento do serviço de aplicações quando necessário. Aplicações Python tem de ser implementadas com todo o necessário [pip](https://pypi.org/project/pip/) módulos.

O motor de implementação do serviço de aplicações automaticamente ativa um ambiente virtual e executa `pip install -r requirements.txt` para quando implanta um [repositório de Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), ou um [pacote Zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) com processos de criação ativados.

Este guia fornece os conceitos chave e instruções para os programadores de Python que utilizam um contentor de Linux incorporado no serviço de aplicações. Se nunca tiver utilizado o serviço de aplicações do Azure, deve seguir a [guia de introdução do Python](quickstart-python.md) e [Python com PostgreSQL tutorial](tutorial-python-postgresql-app.md) primeiro.

> [!NOTE]
> Linux está, atualmente, a opção recomendada para a execução de aplicações Python no serviço de aplicações. Para obter informações sobre a opção do Windows, consulte [Python no sabor de Windows do serviço de aplicações](https://docs.microsoft.com/visualstudio/python/managing-python-on-azure-app-service).
>

## <a name="show-python-version"></a>Mostrar a versão do Python

Para mostrar a versão de Python atual, execute o seguinte comando [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões suportadas do Python, execute o seguinte comando [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PYTHON
```

Pode executar uma versão não suportada do Python criando sua própria imagem de contentor. Para obter mais informações, consulte [utilizar uma imagem personalizada do Docker](tutorial-custom-docker-image.md).

## <a name="set-python-version"></a>Definir a versão do Python

Execute o seguinte comando [Cloud Shell](https://shell.azure.com) para definir a versão do Python para 3.7:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
```

## <a name="container-characteristics"></a>Características do contentor

Aplicações Python implementadas no serviço de aplicações no Linux em execução num contentor de Docker que está definido no repositório do GitHub, [Python 3.6](https://github.com/Azure-App-Service/python/tree/master/3.6.6) ou [Python 3.7](https://github.com/Azure-App-Service/python/tree/master/3.7.0).

Este contentor tem as seguintes características:

- As aplicações são executadas com o [Servidor HTTP WSGI do Gunicorn](https://gunicorn.org/), com os argumentos adicionais `--bind=0.0.0.0 --timeout 600`.

- Por predefinição, a imagem base inclui a arquitetura Web Flask, mas o contentor suporta outras arquiteturas compatíveis com o WSGI e o Python 3.7, como o Django.

- Para instalar pacotes adicionais, como o Django, crie um ficheiro [*requirements.txt* ](https://pip.pypa.io/en/stable/user_guide/#requirements-files) na raiz do projeto com `pip freeze > requirements.txt`. Em seguida, publique o projeto no Serviço de Aplicações com a implementação do Git, que executa automaticamente `pip install -r requirements.txt` no contentor para instalar as dependências da aplicação.

## <a name="container-startup-process"></a>Processo de inicialização do contentor

Durante o arranque, o Serviço de Aplicações no contentor do Linux executa os seguintes passos:

1. Utilize um [comando de arranque personalizadas](#customize-startup-command), se fornecida.
2. Verifique a existência de um [aplikace Django](#django-app)e iniciar Gunicorn para o mesmo se detetado.
3. Verifique a existência de um [aplicação Flask](#flask-app)e iniciar Gunicorn para o mesmo se detetado.
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

Para Flask, o serviço de aplicações procura um ficheiro denominado *application.py* ou *app.py* e inicia Gunicorn da seguinte forma:

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

Pode controlar o comportamento de arranque do contentor ao fornecer um comando de arranque do Gunicorn personalizado. Para tal, executando o seguinte comando [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

Por exemplo, se tiver uma aplicação Flask cujo módulo principal é *hello.py* e o objeto de aplicação Flask desse arquivo é denominado `myapp`, em seguida,  *\<custom-command >* é o seguinte:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
```

Se o seu módulo principal estiver numa subpasta, como `website`, especifique essa pasta com o argumento `--chdir`:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
```

Também pode adicionar todos os argumentos adicionais para Gunicorn para  *\<comando personalizado >*, como `--workers=4`. Para obter mais informações, veja [Executar o Gunicorn](https://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org).

Para utilizar um servidor de não-Gunicorn, tal como [aiohttp](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html), pode substituir  *\<personalizada-command >* com algo semelhante a isto:

```bash
python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
```

> [!Note]
> O Serviço de Aplicações ignora todos os erros ocorridos durante o processamento de um ficheiro de comando personalizado e continua o processo de arranque ao procurar as aplicações Django e Flask. Se não vir o comportamento esperado, verifique se o ficheiro de arranque está implementado no Serviço de Aplicações e não contém quaisquer erros.

## <a name="access-environment-variables"></a>Aceder a variáveis de ambiente

No serviço de aplicações, pode [configurar definições de aplicação](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) fora do seu código de aplicação. Em seguida, pode acessá-los a através da norma [os.environ](https://docs.python.org/3/library/os.html#os.environ) padrão. Por exemplo aceder a uma definição de aplicação denominada `WEBSITE_SITE_NAME`, utilize o seguinte código:

```python
os.environ['WEBSITE_SITE_NAME']
```

## <a name="detect-https-session"></a>Detetar a sessão HTTPS

No serviço de aplicações [terminação de SSL](https://wikipedia.org/wiki/TLS_termination_proxy) ocorre aos balanceadores de carga de rede, para que todos os pedidos HTTPS à sua aplicação, como solicitações HTTP não criptografadas. Se as suas necessidades de lógica de aplicação para verificar se os pedidos de utilizador são encriptados ou não, Inspecione o `X-Forwarded-Proto` cabeçalho.

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

Web populares estruturas permitem-lhe acesso a `X-Forwarded-*` informações no seu padrão de aplicativo padrão. Na [CodeIgniter](https://codeigniter.com/), o [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) verifica o valor de `X_FORWARDED_PROTO` por predefinição.

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Abrir sessão SSH no browser

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Resolução de problemas

- **Vê a aplicação predefinida depois de implementar o seu próprio código de aplicação.** A aplicação predefinida é apresentada uma vez que optar por não tiver implementado o código da aplicação no serviço de aplicações, ou não foi possível encontrar o código da aplicação de serviço de aplicações e executou a aplicação predefinida em vez disso.
- Reinicie o Serviço de Aplicações, aguarde 15 a 20 segundos e verifique novamente a aplicação.
- Certifique-se de que está a utilizar o Serviço de Aplicações para Linux, em vez de uma instância baseada no Windows. A partir da CLI do Azure, execute o comando `az webapp show --resource-group <resource_group_name> --name <app_service_name> --query kind`, ao substituir `<resource_group_name>` e `<app_service_name>`, respetivamente. Deverá ver `app,linux` como resultado; caso contrário, recrie o Serviço de Aplicações e escolha o Linux.
- Utilize o SSH ou a consola Kudu para ligar diretamente ao Serviço de Aplicações e verifique se os ficheiros existem em *site/wwwroot*. Se os ficheiros não existirem, reveja o processo de implementação e volte a implementar a aplicação.
- Se os ficheiros existirem, o Serviço de Aplicações não conseguiu identificar o ficheiro de arranque específico. Verifique se a aplicação está estruturada como Serviço de Aplicações para o [Django](#django-app) ou o [Flask](#flask-app), ou utilize um [comando de arranque personalizado](#customize-startup-command).
- **Vê a mensagem "Serviço Indisponível" no browser.** O browser esgotou o tempo limite ao aguardar uma resposta do Serviço de Aplicações, o que indica que o Serviço de Aplicações iniciou o servidor do Gunicorn, mas os argumentos que especificam o código da aplicação estão incorretos.
- Atualize o browser, especialmente se estiver a utilizar os escalões de preços mais baixos no seu Plano do Serviço de Aplicações. A aplicação poderá demorar mais tempo a iniciar quando utilizar, por exemplo, escalões gratuitos e responde depois de atualizar o browser.
- Verifique se a aplicação está estruturada como Serviço de Aplicações para o [Django](#django-app) ou o [Flask](#flask-app), ou utilize um [comando de arranque personalizado](#customize-startup-command).
- [O fluxo de registo de acesso](#access-diagnostic-logs).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial: Aplicação de Python com PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Tutorial: Implemente a partir do repositório de contentor privado](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [FAQ do serviço de aplicações Linux](app-service-linux-faq.md)