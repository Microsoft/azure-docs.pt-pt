---
title: 'Quickstart: Criar uma aplicação Python'
description: Começa com o Azure App Service implementando a sua primeira aplicação Python num contentor Linux no Serviço de Aplicações.
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: seo-python-october2019, cli-validate, devx-track-python
ms.openlocfilehash: 77aafb53c1346c6723d055a8ae1c96297fdfbd52
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89568919"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Quickstart: Criar uma aplicação Python no Azure App Service em Linux

Neste quickstart, você implementa uma aplicação web Python para [o App Service no Linux,](overview.md#app-service-on-linux)o serviço de hospedagem web altamente escalável e auto-remendado da Azure. Utilize a [interface de linha de comando Azure local (CLI)](/cli/azure/install-azure-cli) num computador Mac, Linux ou Windows. A aplicação web que configura utiliza um nível de Serviço de Aplicações gratuito, para que não incorre em custos no decurso deste artigo.

> [!NOTE]
> Se preferir implementar aplicações através de um IDE, consulte **[aplicações implementar python para o Serviço de Aplicações a partir do Código do Estúdio Visual.](/azure/developer/python/tutorial-deploy-app-service-on-linux-01)**

## <a name="set-up-your-initial-environment"></a>Configurar o seu ambiente inicial

Antes de começar, deve ter o seguinte:

1. Tenha uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
1. Instale <a href="https://www.python.org/downloads/" target="_blank">python 3.6 ou superior</a>.
1. Instale o <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.0.80 ou superior, com o qual execute comandos em qualquer concha para provisões e configurar recursos Azure.

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

Verifique se a sua versão Azure CLI é 2.0.80 ou superior:

```azurecli
az --version
```

Em seguida, inscreva-se em Azure através do CLI:

```azurecli
az login
```

Este comando abre um browser para recolher as suas credenciais. Quando o comando termina, mostra a saída JSON contendo informações sobre as suas subscrições.

Uma vez assinado, pode executar comandos Azure com o CLI Azure para trabalhar com recursos na sua subscrição.

[Tendo problemas? Deixe-nos saber.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="clone-the-sample"></a>Clonar o exemplo

Clone o repositório da amostra com o seguinte comando e, em seguida, navegue na pasta. [(Instale git](https://git-scm.com/downloads) se ainda não tiver git.)

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
cd python-docs-hello-world
```

O código de amostra contém um ficheiro *application.py,* que diz ao Serviço de Aplicações que o código contém uma aplicação Flask. Para obter mais informações, consulte [o processo de arranque do Contentor.](configure-language-python.md#container-startup-process)

[Tendo problemas? Deixe-nos saber.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="run-the-sample"></a>Executar o exemplo

# <a name="bash"></a>[Bash](#tab/bash)

Primeiro criar um ambiente virtual e instalar dependências:

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

Em seguida, desaprote a `FLASK_APP` variável ambiente para o módulo de entrada da aplicação e execute o servidor de desenvolvimento flask:

```
export FLASK_APP=application.py
flask run
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Primeiro criar um ambiente virtual e instalar dependências:

```powershell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
```

Em seguida, desaprote a `FLASK_APP` variável ambiente para o módulo de entrada da aplicação e execute o servidor de desenvolvimento flask:

```powershell
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

Primeiro criar um ambiente virtual e instalar dependências:

```cmd
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
```

Em seguida, desaprote a `FLASK_APP` variável ambiente para o módulo de entrada da aplicação e execute o servidor de desenvolvimento flask:

```cmd
SET FLASK_APP=application.py
flask run
```

---

Abra um navegador web e vá para a aplicação de amostras em `http://localhost:5000/` . A aplicação exibe a mensagem **Hello World!**

![Executar uma amostra python app localmente](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)

Na janela do terminal, pressione **o Ctrl** + **C** para sair do servidor de desenvolvimento do Flask.

[Tendo problemas? Deixe-nos saber.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="deploy-the-sample"></a>Implementar a amostra

Desloque o código na sua pasta local *(python-docs-hello-world)* utilizando o `az webapp up` comando:

```azurecli
az webapp up --sku F1 -n <app-name>
```

- Se o `az` comando não for reconhecido, certifique-se de que tem o CLI Azure instalado como descrito no [Conjunto do seu ambiente inicial](#set-up-your-initial-environment).
- Substitua `<app_name>` por um nome único em todo o Azure *(caracteres válidos `a-z` `0-9` são, e `-` *. Um bom padrão é usar uma combinação do nome da sua empresa e um identificador de aplicativos.
- O `--sku F1` argumento cria a aplicação web no nível de preços gratuitos. Omita este argumento para usar um nível premium mais rápido, que incorre num custo de hora a hora.
- Pode opcionalmente incluir o argumento `-l <location-name>` onde `<location_name>` está uma região do Azure como **central,** **eastasia,** **westeurope,** **koreasouth,** **brazilsouth**, **centralindia**, e assim por diante. Pode recuperar uma lista de regiões admissíveis para a sua conta Azure, executando o [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) comando.
- Se vir o erro, "Não consegui detetar automaticamente a pilha de tempo de execução da sua aplicação", certifique-se de que está a executar o comando na pasta *python-docs-hello-world* que contém o ficheiro *requirements.txt.* (Ver [problemas de deteção automática de problemas com webapp az](https://github.com/Azure/app-service-linux-docs/blob/master/AzWebAppUP/runtime_detection.md) (GitHub).)

O comando pode demorar alguns minutos a ser concluído. Durante a execução, fornece mensagens sobre a criação do grupo de recursos, o plano de Serviço de Aplicações e a aplicação de hospedagem, configurando o registo e, em seguida, executando a implementação zip. Em seguida, dá a mensagem: "Pode lançar a app em http:// &lt; nome de app &gt; .azurewebsites.net", que é o URL da aplicação no Azure.

![Exemplo de saída do comando az webapp up](./media/quickstart-python/az-webapp-up-output.png)

[Tendo problemas? Deixe-nos saber.](https://aka.ms/FlaskCLIQuickstartHelp)

[!INCLUDE [AZ Webapp Up Note](../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Navegar para a aplicação

Navegue pela aplicação implementada no seu navegador web no URL `http://<app-name>.azurewebsites.net` .

O código de amostra Python está a executar um contentor Linux no Serviço de Aplicações usando uma imagem incorporada.

![Executar uma amostra python app em Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Parabéns!** Implementou a sua aplicação Python para o Serviço de Aplicações.

[Tendo problemas? Deixe-nos saber.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="redeploy-updates"></a>Atualizações de recolocação

No seu editor de código favorito, abra *application.py* e atualize a `hello` função da seguinte forma. Esta alteração adiciona uma `print` declaração para gerar a saída de registo com a que trabalha na secção seguinte. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello Azure!"
```

Guarde as suas alterações e saia do editor. 

Recolocar a aplicação utilizando o `az webapp up` comando novamente:

```azurecli
az webapp up
```

Este comando utiliza valores que estão em cache localmente no ficheiro *.azure/config,* incluindo o nome da aplicação, grupo de recursos e plano de Serviço de Aplicações.

Uma vez concluída a implementação, volte para a janela do navegador aberta para `http://<app-name>.azurewebsites.net` . Refresque a página, que deve exibir a mensagem modificada:

![Executar uma aplicação python de amostra atualizada em Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

[Tendo problemas? Deixe-nos saber.](https://aka.ms/FlaskCLIQuickstartHelp)

> [!TIP]
> O Visual Studio Code fornece extensões poderosas para o Serviço de Aplicações Python e Azure, que simplificam o processo de implementação de aplicações web Python para o App Service. Para obter mais informações, consulte [aplicações implementar python para o Serviço de Aplicações a partir do Código do Estúdio Visual.](/azure/python/tutorial-deploy-app-service-on-linux-01)

## <a name="stream-logs"></a>Transmitir registos

Pode aceder aos registos de consola gerados a partir do interior da app e do contentor em que funciona. Os registos incluem qualquer saída gerada através de `print` declarações.

Para transmitir registos, executar o seguinte comando:

```azurecli
az webapp log tail
```

Atualize a aplicação no navegador para gerar registos de consolas, que incluem mensagens que descrevem pedidos HTTP para a aplicação. Se não aparecer imediatamente, tente novamente em 30 segundos.

Também pode inspecionar os ficheiros de registo do navegador em `https://<app-name>.scm.azurewebsites.net/api/logs/docker` .

Para parar o streaming de registo a qualquer momento, digite **Ctrl** + **C**.

[Tendo problemas? Deixe-nos saber.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="manage-the-azure-app"></a>Gerir a app Azure

Vá ao <a href="https://portal.azure.com" target="_blank">portal Azure</a> para gerir a app que criou. Procure e selecione **Serviços de Aplicações.**

![Navegue para Serviços de Aplicações no portal Azure](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Selecione o nome da sua aplicação Azure.

![Navegue para a sua aplicação Python em Serviços de Aplicações no portal Azure](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

A seleção da aplicação abre a sua página **de Visão Geral,** onde pode executar tarefas básicas de gestão como navegar, parar, iniciar, reiniciar e eliminar.

![Gerencie a sua aplicação Python na página Overview no portal Azure](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

O menu Serviço de Aplicações fornece diferentes páginas para configurar a sua aplicação.

[Tendo problemas? Deixe-nos saber.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="clean-up-resources"></a>Limpar os recursos

Nos passos anteriores, criou os recursos do Azure num grupo de recursos. O grupo de recursos tem um nome como "appsvc_rg_Linux_CentralUS" dependendo da sua localização. Se utilizar um SKU de Serviço de Aplicações que não seja o nível F1 gratuito, estes recursos incorrem em custos contínuos (ver [preços do Serviço de Aplicações).](https://azure.microsoft.com/pricing/details/app-service/linux/)

Se não espera precisar destes recursos no futuro, elimine o grupo de recursos executando o seguinte comando:

```azurecli
az group delete
```

O comando utiliza o nome do grupo de recursos em cache no ficheiro *.azure/config.*

O comando pode demorar um minuto a ser concluído.

[Tendo problemas? Deixe-nos saber.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Python (Django) web app com PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Adicione o sign-in do utilizador a uma aplicação web Python](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Configure app Python](configure-language-python.md)

> [!div class="nextstepaction"]
> [Tutorial: Executar app Python em recipiente personalizado](tutorial-custom-container.md)
