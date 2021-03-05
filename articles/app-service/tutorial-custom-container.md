---
title: 'Tutorial: Construa e execute uma imagem personalizada no Azure App Service'
description: Um guia passo a passo para construir uma imagem personalizada do Linux ou do Windows, empurrar a imagem para o Registo de Contentores Azure e, em seguida, implementar essa imagem para o Serviço de Aplicações Azure. Saiba como migrar implementar software personalizado para o Serviço de Aplicações num recipiente personalizado.
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: msangapu
keywords: serviço de aplicativos azure, web app, linux, janelas, estivador, recipiente
ms.custom: devx-track-csharp, mvc, seodec18, devx-track-python, devx-track-azurecli
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 5d3a714230f0279bd68b39cd02624866b9b3bacf
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180518"
---
# <a name="migrate-custom-software-to-azure-app-service-using-a-custom-container"></a>Migrar software personalizado para o Azure App Service usando um recipiente personalizado

::: zone pivot="container-windows"  

O [Serviço de Aplicações do Azure](overview.md) disponibiliza pilhas de aplicações predefinidas no Windows, como ASP.NET ou Node.js, em execução no IIS. O ambiente pré-configurado do Windows bloqueia o sistema operativo contra acesso administrativo, instalações de software, alterações ao Global Assembly Cache, etc. (veja [Operating system functionality on Azure App Service](operating-system-functionality.md) (Funcionalidade do sistema operativo no Serviço de Aplicações do Azure). No entanto, a utilização de um contentor personalizado do Windows no Serviço de Aplicações permite efetuar as alterações ao SO de que a aplicação precisa, pelo que é fácil migrar uma aplicação no local que requer uma configuração personalizada do SO e software. Este tutorial demonstra como migrar para o Serviço de Aplicações uma aplicação ASP.NET que utiliza tipos de letra personalizados instalados na biblioteca de tipos de letra do Windows. Implemente uma imagem do Windows configurada e personalizada do Visual Studio no [Azure Container Registry](../container-registry/index.yml), e, em seguida, execute-a no Serviço de Aplicações.

![Mostra a aplicação web a funcionar num recipiente Windows.](media/tutorial-custom-container/app-running.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- <a href="https://hub.docker.com/" target="_blank">Inscrever numa conta do Docker Hub</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Instale o Docker para Windows</a>.
- <a href="/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Mude o Docker para executar contentores do Windows</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Instale o Visual Studio 2019</a> com as **ASP.NET e desenvolvimento web** e cargas de trabalho de desenvolvimento **Azure.** Se já instalou o Visual Studio 2019:
    - Instale as últimas atualizações no Estúdio Visual clicando em **Procurar ajuda**  >  **para atualizações**.
    - Adicione as cargas de trabalho no Estúdio Visual clicando em **Ferramentas**  >  **Obter Ferramentas e Funcionalidades**.

## <a name="set-up-the-app-locally"></a>Configurar a aplicação localmente

### <a name="download-the-sample"></a>Transferir o exemplo

Neste passo, vai configurar o projeto .NET local.

- [Transfira o projeto de exemplo](https://github.com/Azure-Samples/custom-font-win-container/archive/master.zip).
- Extraia (deszipe) o ficheiro *custom-font-win-container.zip*.

O projeto de exemplo contém uma aplicação ASP.NET simples que utiliza um tipo de letra personalizado que está instalado na biblioteca de tipos de letra do Windows. Não é necessário instalar os tipos de letra, mas é um exemplo de uma aplicação que está integrada no SO subjacente. Para migrar uma aplicação como esta para o Serviço de Aplicações, rearquitete o seu código para remover a integração, ou migre-a tal como está num contentor personalizado do Windows.

### <a name="install-the-font"></a>Instalar o tipo de letra

No Explorador do Windows, navegue para _custom-font-win-container-master/CustomFontSample_, clique com o botão direito do rato em _FrederickatheGreat-Regular.ttf_ e selecione **Instalar**.

Este tipo de letra está disponível publicamente em [Tipos de Letra do Google](https://fonts.google.com/specimen/Fredericka+the+Great).

### <a name="run-the-app"></a>Executar a aplicação

Abra o ficheiro *custom-font-win-container/CustomFontSample.sln* no Visual Studio. 

Escreva `Ctrl+F5` para executar a aplicação sem a depurar. A aplicação é apresentada no browser predefinido. 

:::image type="content" source="media/tutorial-custom-container/local-app-in-browser.png" alt-text="Screenshot mostrando a aplicação exibida no navegador predefinido.":::

Uma vez que a aplicação utiliza um tipo de letra instalado, não é possível executá-la na sandbox do Serviço de Aplicações. No entanto, pode implementá-la com um contentor do Windows em alternativa, pois pode instalar o tipo de letra no contentor do Windows.

### <a name="configure-windows-container"></a>Configurar o contentor do Windows

No Explorador de Soluções, clique com o botão direito do rato no projeto **CustomFontSample** e selecione **Adicionar** > **Suporte de Orquestração de Contentores**.

:::image type="content" source="media/tutorial-custom-container/enable-container-orchestration.png" alt-text="Screenshot da janela Solution Explorer mostrando o projeto CustomFontSample, Add e Container Orchestrator Support itens selecionados.":::

Selecione **Docker Compose**  >  **OK**.

O projeto está agora configurado para ser executado num contentor do Windows. Um _Dockerfile_ é adicionado ao projeto **CustomFontSample** e um projeto **docker-compose** é adicionado à solução. 

No Explorador de Soluções, abra **Dockerfile**.

Tem de utilizar uma [ imagem principal suportada](configure-custom-container.md#supported-parent-images). Altere a imagem principal, substituindo a linha `FROM` pelo seguinte código:

```dockerfile
FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
```

No final do ficheiro, adicione a seguinte linha e guarde o ficheiro:

```dockerfile
RUN ${source:-obj/Docker/publish/InstallFont.ps1}
```

Pode encontrar _InstallFont.ps1_ no projeto **CustomFontSample**. É um script simples que instala o tipo de letra. Pode encontrar uma versão mais complexa do script no [Centro de Scripts](https://gallery.technet.microsoft.com/scriptcenter/fb742f92-e594-4d0c-8b79-27564c575133).

> [!NOTE]
> Para testar o recipiente do Windows localmente, certifique-se de que o Docker é iniciado na sua máquina local.
>

## <a name="publish-to-azure-container-registry"></a>Publicar no Azure Container Registry

[O Azure Container Registry](../container-registry/index.yml) pode armazenar as imagens de implementação de contentores. Pode configurar o Serviço de Aplicações para utilizar imagens alojadas no Azure Container Registry.

### <a name="open-publish-wizard"></a>Abrir o assistente de publicação

No Explorador de Soluções, clique com o botão direito do rato no projeto **CustomFontSample** e selecione **Publicar**.

:::image type="content" source="media/tutorial-custom-container/open-publish-wizard.png" alt-text="Screenshot do Solution Explorer mostrando o projeto CustomFontSample e Publicar selecionado.":::

### <a name="create-registry-and-publish"></a>Criar registo e publicar

No assistente de publicação, selecione **Registo de Contentores**  >  **Crie a Nova Publicação do Registo de Contentores Azure**  >  .

:::image type="content" source="media/tutorial-custom-container/create-registry.png" alt-text="Screenshot do assistente de publicação que mostra o registo do contentor, crie o registo do novo azure e o botão Publicar selecionado.":::

### <a name="sign-in-with-azure-account"></a>Iniciar sessão com a conta do Azure

Na caixa de diálogo **Criar um Novo Registo de Contentor do Azure**, selecione **Adicionar uma conta** e inicie sessão na sua subscrição do Azure. Se já tem sessão iniciada, selecione a conta que contém a subscrição pretendida na lista pendente.

![Iniciar sessão no Azure](./media/tutorial-custom-container/add-an-account.png)

### <a name="configure-the-registry"></a>Configurar o registo

Configure o novo registo de contentor com base nos valores sugeridos na tabela seguinte. Quando terminar, clique em **Criar**.

| Definição  | Valor sugerido | Para obter mais informações: |
| ----------------- | ------------ | ----|
|**Prefixo DNS**| Mantenha o nome do registo gerado ou altere-o para outro nome exclusivo. |  |
|**Grupo de Recursos**| Clique em **Novo**, escreva **myResourceGroup** e clique em **OK**. |  |
|**SKU**| Básico | [Escalões de preço](https://azure.microsoft.com/pricing/details/container-registry/)|
|**Localização do registo**| Europa Ocidental | |

![Configurar o registo de contentor do Azure](./media/tutorial-custom-container/configure-registry.png)

É aberta uma janela de terminal que mostra o progresso de implementação da imagem. Aguarde pela conclusão da implementação.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-web-app"></a>Criar uma aplicação Web

A partir do menu esquerdo, **selecione Criar uma** Web App de recursos para  >    >  **contentores.**

### <a name="configure-app-basics"></a>Configurar o básico da aplicação

No **separador Básicos,** configufique as definições de acordo com a tabela seguinte e, em seguida, clique em **Seguinte: Docker**.

| Definição  | Valor sugerido | Para obter mais informações: |
| ----------------- | ------------ | ----|
|**Subscrição**| Certifique-se de que a subscrição correta está selecionada. |  |
|**Grupo de Recursos**| **Selecione Criar novo,** digite **myResourceGroup** e clique **em OK**. |  |
|**Nome**| Escreva um nome exclusivo. | O URL da aplicação Web é `http://<app-name>.azurewebsites.net`, em que `<app-name>` é o nome da aplicação. |
|**Publicar**| Recipiente de estivador | |
|**Sistema operativo**| Windows | |
|**Região**| Europa Ocidental | |
|**Plano do Windows**| Selecione **Criar novo,** **digite o myAppServicePlan** e clique **em OK**. | |

O seu separador **Básico** deve ser assim:

![Mostra o separador Basics usado para configurar a aplicação web.](media/tutorial-custom-container/configure-app-basics.png)

### <a name="configure-windows-container"></a>Configurar o contentor do Windows

No **separador Docker,** configuure o seu recipiente Windows personalizado como mostrado na tabela seguinte e selecione **Review + create**.

| Definição  | Valor sugerido |
| ----------------- | ------------ |
|**Origem da Imagem**| Registo de Contentores Azure |
|**Registo**| Selecione [o registo que criou anteriormente](#publish-to-azure-container-registry). |
|**Imagem**| customfontsample |
|**Tag**| mais recente |

### <a name="complete-app-creation"></a>Criação de aplicação concluída

Clique em **Criar** e aguarde que o Azure crie os recursos necessários.

## <a name="browse-to-the-web-app"></a>Navegar para a aplicação Web

Quando a operação do Azure estiver concluída, é apresentada uma caixa de notificação.

![Mostra que a operação Azure está completa.](media/tutorial-custom-container/portal-create-finished.png)

1. Clique em **Ir para recurso**.

2. Na página da aplicação, clique na ligação em **URL**.

É aberta uma página nova do browser na seguinte página:

![Mostra a nova página do navegador para a aplicação web.](media/tutorial-custom-container/app-starting.png)

Aguarde alguns minutos e tente novamente, até chegar à página de boas-vindas com o tipo de letra elegante de que está à espera:

![Mostra a página inicial com o tipo de letra configurado.](media/tutorial-custom-container/app-running.png)

**Parabéns!** Migrou uma aplicação ASP.NET para o Serviço de Aplicações do Azure num contentor do Windows.

## <a name="see-container-start-up-logs"></a>Ver os registos de arranque do contentor

O contentor do Windows poderá demorar algum tempo até ser carregado. Para ver o progresso, navegue para o seguinte URL substituindo *\<app-name>* pelo nome da sua aplicação.
```
https://<app-name>.scm.azurewebsites.net/api/logstream
```

Os registos transmitidos têm o seguinte aspeto:

```
14/09/2018 23:16:19.889 INFO - Site: fonts-win-container - Creating container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest.
14/09/2018 23:16:19.928 INFO - Site: fonts-win-container - Create container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest succeeded. Container Id 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:23.405 INFO - Site: fonts-win-container - Start container succeeded. Container: 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Configuring container
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container start-up and configuration completed successfully
```

::: zone-end

::: zone pivot="container-linux"

O Azure App Service utiliza a tecnologia de contentores Docker para acolher imagens incorporadas e imagens personalizadas. Para ver uma lista de imagens incorporadas, execute o comando Azure CLI, ['az webapp list-runtimes --linux'](/cli/azure/webapp#az-webapp-list-runtimes). Se essas imagens não satisfizerem as suas necessidades, pode construir e implementar uma imagem personalizada.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Construa uma imagem personalizada se nenhuma imagem incorporada satisfaz as suas necessidades
> * Empurre a imagem personalizada para um registo de contentores privados em Azure
> * Executar a imagem personalizada no Serviço de Aplicações
> * Configurar as variáveis de ambiente
> * Atualizar e recolocar a imagem
> * Aceder aos registos de diagnósticos
> * Ligar ao contentor com SSH

A conclusão deste tutorial incorre numa pequena taxa na sua conta Azure para o registo do contentor e pode incorrer em custos adicionais para hospedar o contentor por mais de um mês.

## <a name="set-up-your-initial-environment"></a>Configurar o seu ambiente inicial

- Tenha uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Instale [o Docker,](https://docs.docker.com/get-started/#setup)que usa para construir imagens do Docker. A instalação do Docker pode exigir um reinício do computador.
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
- Este tutorial requer a versão 2.0.80 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

Depois de instalar o Docker ou executar a Azure Cloud Shell, abra uma janela do terminal e verifique se o estivador está instalado:

```bash
docker --version
```

## <a name="clone-or-download-the-sample-app"></a>Clone ou descarregue o aplicativo de amostra

Pode obter a amostra para este tutorial através do clone git ou do download.

### <a name="clone-with-git"></a>Clone com git

Clone o repositório de amostras:

```terminal
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
```

Certifique-se de incluir o `--config core.autocrlf=input` argumento para garantir as terminações de linha adequadas em ficheiros que são usados dentro do recipiente Linux:

Em seguida, entrar na pasta:

```terminal
cd docker-django-webapp-linux
```

### <a name="download-from-github"></a>Transferir a partir do GitHub

Em vez de usar o clone git, pode visitar [https://github.com/Azure-Samples/docker-django-webapp-linux](https://github.com/Azure-Samples/docker-django-webapp-linux) , selecione **Clone** e, em seguida, selecione **Download ZIP**. 

Desembale o ficheiro ZIP numa pasta chamada *docker-django-webapp-linux*. 

Em seguida, abra uma janela terminal naquela pasta *docker-django-webapp-linux.*

## <a name="optional-examine-the-docker-file"></a>(Opcional) Examine o ficheiro Docker

O ficheiro da amostra chamada _Dockerfile_ que descreve a imagem do estivador e contém instruções de configuração:

```Dockerfile
FROM tiangolo/uwsgi-nginx-flask:python3.6

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt --no-cache-dir
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222

#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

* O primeiro grupo de comandos instala os requisitos da aplicação no ambiente.
* O segundo grupo de comandos cria um servidor [SSH](https://www.ssh.com/ssh/protocol/) para uma comunicação segura entre o recipiente e o hospedeiro.
* A última linha, `ENTRYPOINT ["init.sh"]` invoca para iniciar o serviço `init.sh` SSH e o servidor Python.

## <a name="build-and-test-the-image-locally"></a>Construa e teste a imagem localmente

> [!NOTE]
> O Docker Hub tem [quotas no número de atrações anónimas por IP e no número de atrações autenticadas por utilizador gratuito (ver transferência de **dados).**](https://www.docker.com/pricing) Se notar que as suas atrações do Docker Hub estão a ser limitadas, tente `docker login` se ainda não tiver iniciado sessão.
> 

1. Executar o seguinte comando para construir a imagem:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```
    
1. Teste que a construção funciona executando o contentor Docker localmente:

    ```bash
    docker run -p 8000:8000 appsvc-tutorial-custom-image
    ```
    
    Este [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) comando especifica a porta com o argumento seguido pelo nome da `-p` imagem. 
    
    > [!TIP]
    > Se estiver a executar o Windows e vir o erro, *standard_init_linux.go:211: o processo do utilizador executivo não causou "nenhum ficheiro ou diretório"*, o ficheiro *init.sh* contém terminações de linha CR-LF em vez das finais LF esperadas. Este erro acontece se usou o git para clonar o repositório da amostra, mas omitiu o `--config core.autocrlf=input` parâmetro. Neste caso, clone o repositório novamente com o argumento "-config". Também pode ver o erro se editar *init.sh* e guardá-lo com finais de CRLF. Neste caso, guarde o ficheiro novamente apenas com terminações LF.

1. Procure `http://localhost:8000` para verificar se a aplicação web e o recipiente estão funcionando corretamente.

    ![Testar a aplicação Web localmente](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Nesta secção e nas que se seguem, você disponibiliza recursos em Azure para o qual empurra a imagem e, em seguida, implanta um contentor para o Azure App Service. Começa-se por criar um grupo de recursos para recolher todos estes recursos.

Executar o [grupo az criar](/cli/azure/group#az-group-create) comando para criar um grupo de recursos:

```azurecli-interactive
az group create --name AppSvc-DockerTutorial-rg --location westus2
```

Pode alterar o `--location` valor para especificar uma região próxima de si.

## <a name="push-the-image-to-azure-container-registry"></a>Empurre a imagem para o Registo do Contentor de Azure

Nesta secção, você empurra a imagem para o Registo de Contentores Azure a partir do qual o Serviço de Aplicações pode implantá-la.

1. Executar o [`az acr create`](/cli/azure/acr#az-acr-create) comando para criar um registo de contentores Azure:

    ```azurecli-interactive
    az acr create --name <registry-name> --resource-group AppSvc-DockerTutorial-rg --sku Basic --admin-enabled true
    ```
    
    `<registry-name>`Substitua-o por um nome adequado para o seu registo. O nome deve conter apenas letras e números e deve ser único em todo o Azure.

1. Executar o [`az acr show`](/cli/azure/acr#az-acr-show) comando para obter credenciais para o registo:

    ```azurecli-interactive
    az acr credential show --resource-group AppSvc-DockerTutorial-rg --name <registry-name>
    ```
    
    A saída JSON deste comando fornece duas palavras-passe juntamente com o nome de utilizador do registo.
    
1. Utilize o `docker login` comando para iniciar serção no registo do contentor:

    ```bash
    docker login <registry-name>.azurecr.io --username <registry-username>
    ```
    
    Substitua `<registry-name>` e `<registry-username>` por valores dos passos anteriores. Quando solicitado, escreva uma das palavras-passe do passo anterior.

    Utilize o mesmo nome de registo em todos os passos restantes desta secção.

1. Assim que o login tiver sucesso, marque a sua imagem local do Docker para o registo:

    ```bash
   docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```    

1. Utilize o `docker push` comando para empurrar a imagem para o registo:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    Carregar a imagem pela primeira vez pode demorar alguns minutos porque inclui a imagem base. Os uploads subsequentes são normalmente mais rápidos.

    Enquanto espera, pode completar os passos na secção seguinte para configurar o Serviço de Aplicações para implantar a partir do registo.

1. Utilize o `az acr repository list` comando para verificar se o impulso foi bem sucedido:

    ```azurecli-interactive
    az acr repository list -n <registry-name>
    ```
    
    A saída deve mostrar o nome da sua imagem.


## <a name="configure-app-service-to-deploy-the-image-from-the-registry"></a>Configure o Serviço de Aplicações para implementar a imagem a partir do registo

Para implantar um contentor no Azure App Service, primeiro cria uma aplicação web no Serviço de Aplicações e, em seguida, liga a aplicação web ao registo de contentores. Quando a aplicação web começa, o Serviço de Aplicações retira automaticamente a imagem do registo.

1. Criar um plano de Serviço de Aplicações utilizando o [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) comando:

    ```azurecli-interactive
    az appservice plan create --name AppSvc-DockerTutorial-plan --resource-group AppSvc-DockerTutorial-rg --is-linux
    ```

    Um plano de Serviço de Aplicações corresponde à máquina virtual que acolhe a aplicação web. Por padrão, o comando anterior utiliza um [nível de preços B1](https://azure.microsoft.com/pricing/details/app-service/linux/) barato que é gratuito durante o primeiro mês. Pode controlar o nível com o `--sku` parâmetro.

1. Crie a aplicação web com o [`az webpp create`](/cli/azure/webapp#az-webapp-create) comando:

    ```azurecli-interactive
    az webapp create --resource-group AppSvc-DockerTutorial-rg --plan AppSvc-DockerTutorial-plan --name <app-name> --deployment-container-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```
    
    Substitua `<app-name>` por um nome para a aplicação web, que deve ser única em todo o Azure. Substitua também `<registry-name>` o nome do seu registo da secção anterior.

1. Utilize [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) para definir a `WEBSITES_PORT` variável ambiental como esperado pelo código da aplicação: 

    ```azurecli-interactive
    az webapp config appsettings set --resource-group AppSvc-DockerTutorial-rg --name <app-name> --settings WEBSITES_PORT=8000
    ```

    `<app-name>`Substitua-o pelo nome utilizado no passo anterior.
    
    Para obter mais informações sobre esta variável ambiental, consulte a [leitura no repositório GitHub da amostra.](https://github.com/Azure-Samples/docker-django-webapp-linux)

1. Ativar [a identidade gerida](./overview-managed-identity.md) para a aplicação web utilizando o [`az webapp identity assign`](/cli/azure/webapp/identity#az-webapp-identity-assign) comando:

    ```azurecli-interactive
    az webapp identity assign --resource-group AppSvc-DockerTutorial-rg --name <app-name> --query principalId --output tsv
    ```

    `<app-name>`Substitua-o pelo nome utilizado no passo anterior. A saída do comando (filtrada pelos `--query` `--output` e pelos argumentos) é o principal de serviço da identidade atribuída, que utiliza em breve.

    A identidade gerida permite-lhe conceder permissões à aplicação web para aceder a outros recursos Azure sem precisar de credenciais específicas.

1. Recupere o seu ID de subscrição com o [`az account show`](/cli/azure/account#az-account-show) comando, que precisa no passo seguinte:

    ```azurecli-interactive
    az account show --query id --output tsv
    ``` 

1. Conceda a permissão da aplicação web para aceder ao registo do contentor:

    ```azurecli-interactive
    az role assignment create --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourceGroups/AppSvc-DockerTutorial-rg/providers/Microsoft.ContainerRegistry/registries/<registry-name> --role "AcrPull"
    ```

    Substitua os seguintes valores:
    - `<principal-id>` com o iD principal de serviço do `az webapp identity assign` comando
    - `<registry-name>` com o nome do seu registo de contentores
    - `<subscription-id>` com o ID de subscrição recuperado do `az account show` comando

Para obter mais informações sobre estas permissões, consulte [o que é o controlo de acesso baseado em funções do Azure](../role-based-access-control/overview.md) e 

## <a name="deploy-the-image-and-test-the-app"></a>Implemente a imagem e teste a aplicação

Pode completar estes passos assim que a imagem for empurrada para o registo do contentor e o Serviço de Aplicações for totalmente aprovisionado.

1. Utilize o [`az webapp config container set`](/cli/azure/webapp/config/container#az-webapp-config-container-set) comando para especificar o registo do contentor e a imagem para implementar para a aplicação web:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-custom-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest --docker-registry-server-url https://<registry-name>.azurecr.io
    ```
    
    `<app_name>`Substitua-o pelo nome da sua aplicação web e substitua `<registry-name>` em dois locais o nome do seu registo. 

    - Quando utilizar um registo diferente do Docker Hub (como mostra este exemplo), `--docker-registry-server-url` deve ser formatado de acordo `https://` com o nome de domínio totalmente qualificado do registo.
    - A mensagem: "Não foi fornecida nenhuma credencial para aceder ao Registo do Contentor de Azure. Tentando olhar para cima..." diz-lhe que o Azure está a usar a identidade gerida da aplicação para autenticar com o registo do contentor em vez de pedir um nome de utilizador e senha.
    - Se encontrar o erro, "AttributeError: 'NoneType' não tem nenhum atributo 'reservado'", certifique-se de que `<app-name>` está correto.

    > [!TIP]
    > Pode recuperar as definições do contentor da aplicação web a qualquer momento com o comando `az webapp config container show --name <app-name> --resource-group AppSvc-DockerTutorial-rg` . A imagem é especificada na `DOCKER_CUSTOM_IMAGE_NAME` propriedade. Quando a aplicação web é implementada através dos modelos Azure DevOps ou Azure Resource Manager, a imagem também pode aparecer numa propriedade chamada `LinuxFxVersion` . Ambas as propriedades servem o mesmo propósito. Se ambos estiverem presentes na configuração da aplicação web, `LinuxFxVersion` tem precedência.

1. Uma vez que o `az webapp config container set` comando esteja concluído, a aplicação web deve estar a funcionar no contentor do Serviço de Aplicações.

    Para testar a aplicação, navegue `http://<app-name>.azurewebsites.net` para, substituindo `<app-name>` pelo nome da sua aplicação web. No primeiro acesso, pode levar algum tempo para a app responder porque o Serviço de Aplicações tem de retirar toda a imagem do registo. Se o navegador tiver esgotado, basta refrescar a página. Uma vez que a imagem inicial é puxada, os testes subsequentes serão executados muito mais rapidamente.

    ![Teste bem sucedido da aplicação web no Azure](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="modify-the-app-code-and-redeploy"></a>Modifique o código de aplicação e reimplante

Nesta secção, faz uma alteração ao código da aplicação web, reconstrói o recipiente e, em seguida, empurra o recipiente para o registo. O Serviço de Aplicações retira automaticamente a imagem atualizada do registo para atualizar a aplicação web em execução.

1. Na sua pasta local *de docker-django-webapp-linux,* abra a *aplicação de ficheiros/modelos/app/index.html*.

1. Altere o primeiro elemento HTML para corresponder ao seguinte código.

    ```html
    <nav class="navbar navbar-inverse navbar-fixed-top">
      <div class="container">
        <div class="navbar-header">
          <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
        </div>
      </div>
    </nav>
    ```
    
1. Guarde as alterações.

1. Mude para a pasta *docker-django-webapp-linux* e reconstrua a imagem:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```

1. Atualize o número da versão na etiqueta da imagem para v1.0.1:

    ```bash
    docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    Substitua `<registry-name>` pelo nome do seu registo.

1. Empurre a imagem para o registo:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

1. Reiniciar a aplicação web:

    ```azurecli-interactive
    az webapp restart --name <app_name> --resource-group AppSvc-DockerTutorial-rg
    ```

    Substitua `<app_name>` pelo nome da aplicação Web. Após o reinício, o Serviço de Aplicações retira a imagem atualizada do registo do contentor.

1. Verifique se a atualização foi implementada navegando para `http://<app-name>.azurewebsites.net` .

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

1. Ligue a exploração do contentor:

    ```azurecli-interactive
    az webapp log config --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-container-logging filesystem
    ```
    
1. Ativar o fluxo de registo:

    ```azurecli-interactive
    az webapp log tail --name <app-name> --resource-group AppSvc-DockerTutorial-rg
    ```
    
    Se não vir os registos da consola imediatamente, volte a consultar dentro de 30 segundos.

    Também pode inspecionar os ficheiros de registo no browser em `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

1. Para parar o streaming de registo a qualquer momento, digite **Ctrl** + **C**.

## <a name="connect-to-the-container-using-ssh"></a>Ligar ao contentor com SSH

O SSH permite a comunicação segura entre um contentor e um cliente. Para ativar a ligação SSH ao seu recipiente, a sua imagem personalizada deve ser configurada para o mesmo. Uma vez que o recipiente esteja em funcionamento, pode abrir uma ligação SSH.

### <a name="configure-the-container-for-ssh"></a>Configure o recipiente para sSH

A aplicação de amostra utilizada neste tutorial já tem a configuração necessária no *Dockerfile*, que instala o servidor SSH e também define as credenciais de login. Esta secção é apenas informativa. Para ligar ao recipiente, salte para a secção seguinte

```Dockerfile
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
  && apt-get install -y --no-install-recommends openssh-server \
  && echo "$SSH_PASSWD" | chpasswd 
```

> [!NOTE]
> Esta configuração não permite ligações externas ao recipiente. O SSH só está disponível através do Site Kudu/SCM. O site Kudu/SCM é autenticado com a sua conta Azure.

O *Dockerfile* também copia o ficheiro *sshd_config* para a pasta */etc/ssh/* e expõe a porta 2222 no recipiente:

```Dockerfile
COPY sshd_config /etc/ssh/

# ...

EXPOSE 8000 2222
```

O Porto 2222 é um porto interno acessível apenas por contentores dentro da rede de pontes de uma rede virtual privada. 

Finalmente, o script de entrada, *init.sh,* inicia o servidor SSH.

```bash
#!/bin/bash
service ssh start
```

### <a name="open-ssh-connection-to-container"></a>Abrir a ligação SSH ao contentor

1. Navegue e `https://<app-name>.scm.azurewebsites.net/webssh/host` inscreva-se na sua conta Azure. Substitua `<app-name>` pelo nome da aplicação Web.

1. Uma vez assinado, é redirecionado para uma página informativa para a aplicação web. Selecione **SSH** no topo da página para abrir a concha e utilizar comandos.

    Por exemplo, pode examinar os processos que estão a decorrer no seu interior utilizando o `top` comando.
    
## <a name="clean-up-resources"></a>Limpar os recursos

Os recursos que criou neste artigo podem incorrer em custos contínuos. para limpar os recursos, basta eliminar o grupo de recursos que os contém:

```azurecli
az group delete --name AppSvc-DockerTutorial-rg
```

::: zone-end

## <a name="next-steps"></a>Passos seguintes

O que aprendeu:

> [!div class="checklist"]
> * Implementar uma imagem personalizada para um registo de contentores privados
> * Implementar e a imagem personalizada no Serviço de Aplicações
::: zone pivot="container-linux"
> * Atualizar e recolocar a imagem
::: zone-end
> * Aceder aos registos de diagnósticos
::: zone pivot="container-linux"
> * Ligar ao contentor com SSH
::: zone-end

No próximo tutorial, aprende a mapear um nome DNS personalizado para a sua aplicação.

> [!div class="nextstepaction"]
> [Tutorial: Mapeie o nome DNS personalizado para a sua aplicação](app-service-web-tutorial-custom-domain.md)

Ou, confira outros recursos:

> [!div class="nextstepaction"]
> [Configurar um contentor personalizado](configure-custom-container.md)

::: zone pivot="container-linux"
> [!div class="nextstepaction"]
> [Tutorial: App WordPress multi-contentor](tutorial-multi-container-app.md)
::: zone-end
