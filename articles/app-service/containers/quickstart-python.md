---
title: 'Quickstart: Crie uma aplicação Linux Python'
description: Inicie-se com aplicações Linux no Azure App Service, implementando a sua primeira aplicação Python para um contentor Linux no App Service.
ms.topic: quickstart
ms.date: 10/22/2019
ms.custom: seo-python-october2019, cli-validate
experimental: true
experiment_id: 01a9132f-eaab-4c
ms.openlocfilehash: 9cc314edf35d6a327522ed49fcc0c7798c7dcf63
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80045673"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Quickstart: Criar uma app Python no Azure App Service no Linux

Neste arranque rápido, você implementa uma aplicação web Python para [app service no Linux](app-service-linux-intro.md), o serviço de hospedagem web altamente escalável e auto-remendado do Azure. Utiliza a interface de linha de comando azure local [(CLI)](/cli/azure/install-azure-cli) num computador Mac, Linux ou Windows. A aplicação web que configura utiliza um nível gratuito de Serviço de Aplicações, pelo que não incorre em custos no decurso deste artigo.

Se preferir implementar aplicações através de um IDE, consulte [implementar aplicações Python para o Serviço de Aplicações a partir do Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- <a href="https://www.python.org/downloads/" target="_blank">Python 3.7</a> (Python 3.6 também é suportado)
- <a href="https://git-scm.com/downloads" target="_blank">Git</a>
- <a href="https://docs.microsoft.com/cli/azure/install-azure-cli" target="_blank">Azure CLI</a>

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

Na pasta *python-docs-hello-world* que contém o código `az webapp up` da amostra, execute o seguinte comando. Substitua-a `<app-name>` por um nome de aplicação globalmente único *(caracteres válidos `a-z`são, `0-9`e). `-` * Substitua `<location-name>` também por uma região de Azure como **central,** **eastasia**, **Westeurope,** **Koreasouth**, **Brazilsouth**, **centralindia**, e assim por diante. (Pode recuperar uma lista de regiões admissíveis para [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) a sua conta Azure executando o comando.)


```azurecli
az webapp up --sku F1 -n <app-name> -l <location-name>
```

Este comando pode demorar alguns minutos a funcionar completamente. Ao executar, apresenta informações semelhantes ao exemplo seguinte:

```output
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_centralus' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_centralus' ...
App service plan creation complete
Creating app '<app-name>' ....
Webapp creation complete
Creating zip with contents of dir /home/username/quickstart/python-docs-hello-world ...
Preparing to deploy contents to app.
All done.
{
  "app_url": "https:/<app-name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app-name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_centralus ",
  "serverfarm": "appsvc_asp_Linux_centralus",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Navegar para a aplicação

Navegue na aplicação implementada no seu `http://<app-name>.azurewebsites.net`navegador web no URL .

O código de amostra Python está a executar um contentor Linux no Serviço de Aplicações utilizando uma imagem incorporada.

![Executar uma amostra de app Python em Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Parabéns!** Implementou a sua aplicação Python para o Serviço de Aplicações no Linux.

## <a name="redeploy-updates"></a>Reutilizar atualizações

No seu editor de código favorito, abra *application.py* e altere a `return` declaração na última linha para corresponder ao seguinte código. A `print` declaração está incluída aqui para gerar saída de exploração madeireira com a sua posição na secção seguinte. 

```python
print("Handling request to home page.")
return "Hello Azure!"
```

Guarde as suas alterações e saia do editor. 

Recoloque a aplicação `az webapp up` utilizando o seguinte comando, utilizando o mesmo comando `<app-name>` utilizado `<location-name>` para implementar a aplicação pela primeira vez, substituindo e com os mesmos nomes que utilizou anteriormente. 

```azurecli
az webapp up --sku F1 -n <app-name> -l <location-name>
```

Uma vez concluída a implementação, volte `http://<app-name>.azurewebsites.net` para a janela do navegador aberta e refresque a página, que deve exibir a mensagem modificada:

![Executar uma aplicação de python de amostra atualizada em Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> O Visual Studio Code fornece extensões poderosas para o Python e o Azure App Service, que simplificam o processo de implementação de aplicações web Python para o App Service. Para mais informações, consulte [implementar aplicações Python para o Serviço de Aplicações a partir do Código do Estúdio Visual](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Transmitir registos

Pode aceder aos registos de consolas gerados a partir de dentro da aplicação e ao recipiente em que funciona. Os registos incluem `print` qualquer saída gerada usando declarações.

Em primeiro lugar, ligue o registo do contentor `<app-name>` executando o seguinte `<resource-group-name>` comando num terminal, substituindo o nome `az webapp up` da sua aplicação e com o nome do grupo de recursos mostrado na saída do comando utilizado (como "appsvc_rg_Linux_centralus"):

```azurecli
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

Quando o registo do contentor estiver ligado, execute o seguinte comando para mostrar o fluxo de registo:

```azurecli
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

Atualizar a aplicação no navegador para gerar registos de consolas, que devem incluir linhas semelhantes ao seguinte texto. Se não vir a saída imediatamente, tente novamente em 30 segundos.

```output
2019-10-23T12:40:03.815574424Z Handling request to home page.
2019-10-23T12:40:03.815602424Z 172.16.0.1 - - [23/Oct/2019:12:40:03 +0000] "GET / HTTP/1.1" 200 12 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.63 Safari/537.36 Edg/78.0.276.19"
```

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

Nos passos anteriores, criou os recursos do Azure num grupo de recursos. O grupo de recursos tem um nome como "appsvc_rg_Linux_CentralUS" dependendo da sua localização. Se utilizar um SKU de serviço de aplicação que não seja o nível gratuito de F1, estes recursos incorrerão em custos contínuos.

Se não espera precisar destes recursos no futuro, elimine o grupo de `<resource-group-name>` recursos executando o seguinte `az webapp up` comando, substituindo o grupo de recursos mostrado na saída do comando, como "appsvc_rg_Linux_centralus". O comando pode levar um minuto para ser concluído.

```azurecli
az group delete -n <resource-group-name>
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: App web Python (Django) com PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [App Configure Python](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Tutorial: Executar app Python em recipiente personalizado](tutorial-custom-docker-image.md)
