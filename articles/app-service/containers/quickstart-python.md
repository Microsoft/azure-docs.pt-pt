---
title: 'Início rápido: criar um aplicativo do Linux Python'
description: Introdução aos aplicativos do Linux no serviço de Azure App implantando seu primeiro aplicativo Python em um contêiner do Linux no serviço de aplicativo.
ms.topic: quickstart
ms.date: 10/22/2019
ms.custom: seo-python-october2019
experimental: false
experiment_id: 1e304dc9-5add-4b
ms.openlocfilehash: 266f6e37584807e139e80a581d16f404c6636f6e
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768406"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Início rápido: criar um aplicativo Python no serviço de Azure App no Linux

Neste guia de início rápido, você implanta um aplicativo Web Python [no serviço de aplicativo no Linux](app-service-linux-intro.md), o serviço de hospedagem na Web altamente escalonável e com aplicação de patch automática do Azure. Você usa a [CLI (interface de linha de comando) local do Azure](/cli/azure/install-azure-cli) em um computador Mac, Linux ou Windows. O aplicativo Web que você configura usa uma camada de serviço de aplicativo gratuita, portanto, não há custos no decorrer deste artigo.

Se preferir implantar aplicativos por meio de um IDE, consulte [implantar aplicativos Python no serviço de aplicativo de Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- <a href="https://www.python.org/downloads/" target="_blank">Python 3,7</a> (também há suporte para Python 3,6)
- <a href="https://git-scm.com/downloads" target="_blank">Git</a>
- <a href="https://docs.microsoft.com/cli/azure/install-azure-cli" target="_blank">CLI do Azure</a>

## <a name="download-the-sample"></a>Transferir o exemplo

Em uma janela de terminal, execute o comando a seguir para clonar o aplicativo de exemplo em seu computador local. 

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Em seguida, vá para essa pasta:

```terminal
cd python-docs-hello-world
```

O repositório contém um arquivo *Application.py* , que informa ao serviço de aplicativo que o código contém um aplicativo Flask. Para obter mais informações, consulte [personalização e processo de inicialização do contêiner](how-to-configure-python.md).

## <a name="run-the-sample"></a>Executar o exemplo

Em uma janela de terminal, use os comandos abaixo (conforme apropriado para o seu sistema operacional) para instalar as dependências necessárias e iniciar o servidor de desenvolvimento interno. 

# <a name="bashtabbash"></a>[Raso](#tab/bash)

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
export FLASK_APP=application.py
flask run
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

# <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
SET FLASK_APP=application.py
flask run
```

---

Abra um navegador da Web e vá para o aplicativo de exemplo em `http://localhost:5000/`. O aplicativo exibe a mensagem **Olá, mundo!** .

![Executar um aplicativo Python de exemplo localmente](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

Na janela do seu terminal, pressione **Ctrl**+**C** para sair do servidor Web.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

O CLI do Azure fornece muitos comandos convenientes que você usa em um terminal local para provisionar e gerenciar recursos do Azure na linha de comando. Você pode usar comandos para concluir as mesmas tarefas que você faria por meio do portal do Azure em um navegador. Você também pode usar comandos da CLI em scripts para automatizar os processos de gerenciamento.

Para executar comandos do Azure no CLI do Azure, primeiro você deve entrar usando o comando `az login`. Esse comando abre um navegador para coletar suas credenciais.

```terminal
az login
```

## <a name="deploy-the-sample"></a>Implementar o exemplo

O comando [`az webapp up`](/cli/azure/webapp#az-webapp-up) cria o aplicativo Web no serviço de aplicativo e implanta seu código.

Na pasta *Python-docs-Olá-mundo* que contém o código de exemplo, execute o comando `az webapp up` a seguir. Substitua `<app-name>` por um nome de aplicativo exclusivo globalmente (os*caracteres válidos são `a-z`, `0-9`e `-`* ). Substitua também `<location-name>` por uma região do Azure, como **centralus**, **eastasia**, **westeurope**, **koreasouth**, **brazilsouth**, **centralindia**e assim por diante. (Você pode recuperar uma lista de regiões permitidas para sua conta do Azure executando o comando [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) .)


```terminal
az webapp up --sku F1 -n <app-name> -l <location-name>
```

Esse comando pode levar alguns minutos para concluir a execução. Ao executar, apresenta informações semelhantes ao exemplo seguinte:

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

Navegue até o aplicativo implantado no navegador da Web na URL `http://<app-name>.azurewebsites.net`.

O código de exemplo do Python está executando um contêiner do Linux no serviço de aplicativo usando uma imagem interna.

![Executar um aplicativo Python de exemplo no Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Parabéns!** Você implantou seu aplicativo Python no serviço de aplicativo no Linux.

## <a name="redeploy-updates"></a>Reimplantar atualizações

No editor de código favorito, abra *Application.py* e altere a instrução `return` na última linha para corresponder ao código a seguir. A instrução `print` está incluída aqui para gerar o log de saída com o qual você trabalha na próxima seção. 

```python
print("Handling request to home page.")
return "Hello Azure!"
```

Salve as alterações e saia do editor. 

Reimplante o aplicativo usando o seguinte comando `az webapp up`, usando o mesmo comando usado para implantar o aplicativo na primeira vez, substituindo `<app-name>` e `<location-name>` pelos mesmos nomes que você usou antes. 

```terminal
az webapp up --sku F1 -n <app-name> -l <location-name>
```

Depois que a implantação for concluída, volte para a janela do navegador aberta para `http://<app-name>.azurewebsites.net` e atualize a página, que deve exibir a mensagem modificada:

![Executar um aplicativo Python de exemplo atualizado no Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> O Visual Studio Code fornece extensões poderosas para o Python e o serviço de Azure App, que simplificam o processo de implantação de aplicativos Web Python no serviço de aplicativo. Para obter mais informações, consulte [implantar aplicativos Python no serviço de aplicativo de Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Transmitir registos

Você pode acessar os logs de console gerados de dentro do aplicativo e o contêiner no qual ele é executado. Os logs incluem qualquer saída gerada usando instruções `print`.

Primeiro, ative o log de contêiner executando o seguinte comando em um terminal, substituindo `<app-name>` pelo nome do seu aplicativo e `<resource-group-name>` pelo nome do grupo de recursos mostrado na saída do comando `az webapp up` usado (como "appsvc_rg_Linux_centralus"):

```terminal
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

Depois que o log de contêiner estiver ativado, execute o seguinte comando para mostrar o fluxo de log:

```terminal
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

Atualize o aplicativo no navegador para gerar logs de console, que devem incluir linhas semelhantes ao texto a seguir. Se você não vir a saída imediatamente, tente novamente em 30 segundos.

```output
2019-10-23T12:40:03.815574424Z Handling request to home page.
2019-10-23T12:40:03.815602424Z 172.16.0.1 - - [23/Oct/2019:12:40:03 +0000] "GET / HTTP/1.1" 200 12 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.63 Safari/537.36 Edg/78.0.276.19"
```

Você também pode inspecionar os arquivos de log no navegador em `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Para interromper o streaming de log a qualquer momento, digite `Ctrl`+`C`.

## <a name="manage-the-azure-app"></a>Gerenciar o aplicativo do Azure

Vá para o <a href="https://portal.azure.com" target="_blank">portal do Azure</a> para gerenciar o aplicativo que você criou. Pesquise e selecione **serviços de aplicativos**.

![Navegue até serviços de aplicativos no portal do Azure](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Selecione o nome do seu aplicativo do Azure.

![Navegue até seu aplicativo Python nos serviços de aplicativos na portal do Azure](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Você vê a página de visão geral do seu aplicativo. Aqui, pode realizar tarefas de gestão básicas, como navegar, parar, iniciar, reiniciar e eliminar.

![Gerencie seu aplicativo Python na página Visão geral no portal do Azure](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

O menu serviço de aplicativo fornece páginas diferentes para configurar seu aplicativo.

## <a name="clean-up-resources"></a>Limpar recursos

Nos passos anteriores, criou os recursos do Azure num grupo de recursos. O grupo de recursos tem um nome como "appsvc_rg_Linux_CentralUS", dependendo do seu local. Se você usar um SKU do serviço de aplicativo diferente da camada F1 gratuita, esses recursos incorrerão em custos contínuos.

Se você não espera precisar desses recursos no futuro, exclua o grupo de recursos executando o comando a seguir, substituindo `<resource-group-name>` pelo grupo de recursos mostrado na saída do comando `az webapp up`, como "appsvc_rg_Linux_centralus". O comando pode levar um minuto para ser concluído.

```terminal
az group delete -n <resource-group-name>
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: aplicativo Web Python (Django) com PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Configurar o aplicativo Python](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Tutorial: executar o aplicativo Python no contêiner personalizado](tutorial-custom-docker-image.md)
