---
title: 'Quickstart: Crie uma aplicação Linux Python'
description: Inicie-se com aplicações Linux no Azure App Service, implementando a sua primeira aplicação Python para um contentor Linux no App Service.
ms.topic: quickstart
ms.date: 04/03/2020
ms.custom: seo-python-october2019, cli-validate
experimental: true
experiment_id: 01a9132f-eaab-4c
ms.openlocfilehash: 7f8d21cbccc1a6eec7f62ab5d129f3f0637b8d13
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085132"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Quickstart: Criar uma app Python no Azure App Service no Linux

Neste arranque rápido, você implementa uma aplicação web Python para [app service no Linux](app-service-linux-intro.md), o serviço de hospedagem web altamente escalável e auto-remendado do Azure. Utiliza a interface de linha de comando azure local [(CLI)](/cli/azure/install-azure-cli) num computador Mac, Linux ou Windows. A aplicação web que configura utiliza um nível gratuito de Serviço de Aplicações, pelo que não incorre em custos no decurso deste artigo.

Se preferir implementar aplicações através de um IDE, consulte [implementar aplicações Python para o Serviço de Aplicações a partir do Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- <a href="https://www.python.org/downloads/" target="_blank">Python 3.7</a> (Python 3.6 também é suportado)
- <a href="https://git-scm.com/downloads" target="_blank">Git</a>
- <a href="https://docs.microsoft.com/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.0.80 ou superior. Execute `az --version` para verificar a sua versão.

## <a name="download-the-sample"></a>Transferir o exemplo

Numa janela de terminais, execute o seguinte comando para clonar a aplicação da amostra para o seu computador local. 

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Em seguida, vá para a pasta:

```terminal
cd python-docs-hello-world
```

O repositório contém um ficheiro *application.py,* que diz ao App Service que o código contém uma aplicação Do Flask. Para mais informações, consulte o processo de arranque do [Recipiente e as personalizações.](how-to-configure-python.md)

## <a name="run-the-sample"></a>Executar o exemplo

Numa janela de terminais, utilize os comandos abaixo (conforme adequado para o seu sistema operativo) para instalar as dependências necessárias e lançar o servidor de desenvolvimento incorporado. 

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
export FLASK_APP=application.py
flask run
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
SET FLASK_APP=application.py
flask run
```

---

Abra um navegador web e vá `http://localhost:5000/`ao aplicativo de amostra em . A aplicação exibe a mensagem **Hello World!**

![Executar uma amostra de app Python localmente](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)

Na janela do terminal, prima **Ctrl**+**C** para sair do servidor web.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

O Azure CLI fornece-lhe muitos comandos convenientes que utiliza a partir de um terminal local para fornecer e gerir os recursos azure a partir da linha de comando. Pode utilizar comandos para completar as mesmas tarefas que faria através do portal Azure num browser. Também pode utilizar comandos CLI em scripts para automatizar processos de gestão.

Para executar comandos Azure no Azure CLI, deve `az login` primeiro assinar com o comando. Este comando abre um navegador para recolher as suas credenciais.

```azurecli
az login
```

## <a name="deploy-the-sample"></a>Implementar a amostra

O [`az webapp up`](/cli/azure/webapp#az-webapp-up) comando cria a aplicação web no Serviço de Aplicações e implementa o seu código.

Na pasta *python-docs-hello-world* que contém o código `az webapp up` da amostra, execute o seguinte comando. Substitua-a `<app-name>` por um nome de aplicação globalmente único *(caracteres válidos `a-z`são, `0-9`e). `-` *


```azurecli
az webapp up --sku F1 -n <app-name>
```

O `--sku F1` argumento cria a aplicação web no nível de preços gratuitos. Em vez disso, pode omitir este argumento para utilizar um nível premium, que incorre num custo horária.

Você pode incluir `-l <location-name>` opcionalmente `<location_name>` o argumento onde está uma região de Azure como **central,** **eastasia**, **Westeurope**, **Koreasouth,** **Brasilsouth,** **centralindia**, e assim por diante. Pode recuperar uma lista de regiões admissíveis para [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) a sua conta Azure, executando o comando.

O `az webapp up` comando pode demorar alguns minutos a funcionar completamente. Durante a execução, exibe informações `<app-name>` semelhantes ao seguinte exemplo, onde estará o nome que forneceu anteriormente:

<pre>
Creating Resource group 'appsvc_rg_Linux_centralus' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_centralus' ...
App service plan creation complete
Creating app '&lt;app-name&gt;' ....
Configuring default logging for the app, if not already enabled
Creating zip with contents of dir D:\Examples\python-docs-hello-world ...
Getting scm site credentials for zip deployment
Starting zip deployment. This operation can take a while to complete ...
Deployment endpoint responded with status code 202
You can launch the app at http://&lt;app-name&gt;.azurewebsites.net
{
  "URL": "http://&lt;app-name&gt;.net",
  "appserviceplan": "appsvc_asp_Linux_centralus",
  "location": "eastus",
  "name": "&lt;app-name&gt;",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_centralus",
  "runtime_version": "python|3.7",
  "runtime_version_detected": "-",
  "sku": "FREE",
  "src_path": "D:\\Examples\\python-docs-hello-world"
}
</pre>

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Navegar para a aplicação

Navegue na aplicação implementada no seu `http://<app-name>.azurewebsites.net`navegador web no URL .

O código de amostra Python está a executar um contentor Linux no Serviço de Aplicações utilizando uma imagem incorporada.

![Executar uma amostra de app Python em Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Parabéns!** Implementou a sua aplicação Python para o Serviço de Aplicações no Linux.

## <a name="redeploy-updates"></a>Reutilizar atualizações

No seu editor de código favorito, abra *application.py* e atualize a `hello` função da seguinte forma. Esta alteração `print` adiciona uma declaração para gerar saída de registo com a sua trabalho na secção seguinte. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello Azure!"
```

Guarde as suas alterações e saia do editor. 

Reutilizar a aplicação utilizando novamente o `az webapp up` comando:

```azurecli
az webapp up
```

Este comando utiliza valores que estão em cache no ficheiro *.azure/config,* incluindo o nome da aplicação, grupo de recursos e plano de Serviço de Aplicações.

Uma vez concluída a implementação, volte `http://<app-name>.azurewebsites.net` para a janela do navegador aberta e refresque a página, que deve exibir a mensagem modificada:

![Executar uma aplicação de python de amostra atualizada em Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> O Visual Studio Code fornece extensões poderosas para o Python e o Azure App Service, que simplificam o processo de implementação de aplicações web Python para o App Service. Para mais informações, consulte [implementar aplicações Python para o Serviço de Aplicações a partir do Código do Estúdio Visual](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Transmitir registos

Pode aceder aos registos de consolas gerados a partir de dentro da aplicação e ao recipiente em que funciona. Os registos incluem `print` qualquer saída gerada usando declarações.

Para transmitir registos, execute o seguinte comando:

```azurecli
az webapp log tail
```

Atualizar a aplicação no navegador para gerar registos de consolas, que devem incluir linhas semelhantes ao seguinte texto. Se não vir a saída imediatamente, tente novamente em 30 segundos.

<pre>
2020-04-03T22:54:04.236405938Z Handling request to home page.
2020-04-03T22:54:04.236497641Z 172.16.0.1 - - [03/Apr/2020:22:54:04 +0000] "GET / HTTP/1.1" 200 12 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.83 Safari/537.36 Edg/81.0.416.41"
</pre>

Também pode inspecionar os ficheiros `https://<app-name>.scm.azurewebsites.net/api/logs/docker`de registo do navegador em .

Para parar o streaming de `Ctrl` + `C`registo a qualquer momento, escreva .

## <a name="manage-the-azure-app"></a>Gerir a app Azure

Vá ao <a href="https://portal.azure.com" target="_blank">portal Azure</a> para gerir a app que criou. Procure e selecione Serviços de **Aplicações**.

![Navegue para serviços de aplicações no portal Azure](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Selecione o nome da sua aplicação Azure.

![Navegue para a sua app Python em Serviços de Aplicações no portal Azure](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Veja a página de visão geral da sua aplicação. Aqui, pode realizar tarefas de gestão básicas, como navegar, parar, iniciar, reiniciar e eliminar.

![Gerencie a sua aplicação Python na página overview no portal Azure](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

O menu do Serviço de Aplicações fornece páginas diferentes para configurar a sua aplicação.

## <a name="clean-up-resources"></a>Limpar recursos

Nos passos anteriores, criou os recursos do Azure num grupo de recursos. O grupo de recursos tem um nome como "appsvc_rg_Linux_CentralUS" dependendo da sua localização. Se utilizar um SKU de serviço de aplicação que não seja o nível gratuito de F1, estes recursos incorrem em custos contínuos (ver preços do [Serviço de Aplicações).](https://azure.microsoft.com/pricing/details/app-service/linux/)

Se não espera precisar destes recursos no futuro, elimine o grupo de `<resource-group-name>` recursos executando o seguinte `az webapp up` comando, substituindo o grupo de recursos mostrado na saída do comando, como "appsvc_rg_Linux_centralus". O comando pode levar um minuto para ser concluído.

```azurecli
az group delete -n <resource-group-name>
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: App web Python (Django) com PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Adicione o sessão de sessão do utilizador a uma aplicação web Python](../../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [App Configure Python](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Tutorial: Executar app Python em recipiente personalizado](tutorial-custom-docker-image.md)
