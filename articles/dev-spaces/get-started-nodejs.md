---
title: Criar um ambiente de desenvolvimento de node. js do Kubernetes na cloud com o VS Code
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 09/26/2018
ms.topic: tutorial
description: Desenvolvimento rápido da Kubernetes com contentores e microsserviços no Azure
keywords: Docker, o Kubernetes, o Azure, o AKS, o serviço Kubernetes do Azure, contentores, Helm, a malha de serviço, roteamento de malha do serviço, kubectl, k8s
ms.openlocfilehash: b7437264bd51bcdc1be4d378cd4fbee506a6e83f
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57193658"
---
# <a name="get-started-on-azure-dev-spaces-with-nodejs"></a>Começar a trabalhar com espaços de desenvolvimento do Azure com node. js

Neste guia, vai aprender a:

- Criar um ambiente baseado no Kubernetes no Azure otimizado para o desenvolvimento - um _espaço de programação_.
- Utilizar o VS Code e a linha de comandos para desenvolver iterativamente código em contentores.
- Desenvolver e testar de forma produtiva o seu código num ambiente de equipa.

> [!Note]
> **Se ficar bloqueado** em qualquer altura, veja a secção [Resolução de problemas](troubleshooting.md) ou publique um comentário nesta página.

Está agora pronto para criar um ambiente de desenvolvimento baseado no Kubernetes no Azure.

## <a name="install-the-azure-cli"></a>Instalar a CLI do Azure
O Azure Dev Spaces só precisa de configuração mínima do computador local. A maior parte da configuração do espaço de desenvolvimento é armazenada na cloud e é partilhável com outros utilizadores. O seu computador local pode executar Windows, Mac ou Linux. Para o Linux, há suporte para as distribuições seguintes: Ubuntu (18.04, 16.04, and 14.04), Debian 8 and 9, RHEL 7, Fedora 26+, CentOS 7, openSUSE 42.2, and SLES 12.

Comece por transferir e executar a [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). 

> [!IMPORTANT]
> Se já tiver a CLI do Azure instalada, certifique-se de que está a utilizar a versão 2.0.43 ou superior.

### <a name="sign-in-to-azure-cli"></a>Iniciar sessão na CLI do Azure
Inicie sessão no Azure. Escreva o seguinte comando numa janela de terminal:

```cmd
az login
```

> [!Note]
> Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free).

#### <a name="if-you-have-multiple-azure-subscriptions"></a>Se tiver várias subscrições do Azure...
Pode ver as suas subscrições ao executar: 

```cmd
az account list
```
Localize a subscrição que tem `isDefault: true` na saída do JSON.
Se esta não for a subscrição que pretende utilizar, pode alterar a subscrição predefinida:

```cmd
az account set --subscription <subscription ID>
```

## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Criar um cluster do Kubernetes ativado para os Espaços de Programador do Azure

Na linha de comandos, crie o grupo de recursos. Utilize uma das regiões suportadas atualmente (EUA Leste, EUA Leste 2, EUA Central, EUA Oeste 2, Europa Ocidental, Sudeste Asiático, Canadá Central ou Leste do Canadá).

```cmd
az group create --name MyResourceGroup --location <region>
```

Crie um cluster do Kubernetes com o seguinte comando:

```cmd
az aks create -g MyResourceGroup -n MyAKS --location <region> --kubernetes-version 1.10.9 --generate-ssh-keys
```

A criação do cluster demora alguns minutos.

### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Configurar o seu cluster do AKS para utilizar os espaços de desenvolvimento do Azure

Introduza o seguinte comando da CLI do Azure com o grupo de recursos que contém o cluster do AKS e o nome do cluster do AKS. O comando configura o seu cluster com suporte para espaços de desenvolvimento do Azure.

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

> [!IMPORTANT]
> O processo de configuração de espaços de desenvolvimento do Azure irá remover o `azds` espaço de nomes do cluster, se existir.

## <a name="get-kubernetes-debugging-for-vs-code"></a>Depurar o Kubernetes para o VS Code
Estão disponíveis funcionalidades avançadas, como a depuração do Kubernetes, para programadores de .NET Core e o Node.js com o VS Code.

1. Se não tiver, instale o [VS Code](https://code.visualstudio.com/Download).
1. Transfira e instale a [extensão do Azure Dev Spaces do VS](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Clique em Instalar uma vez na página do Marketplace da extensão e, novamente, no VS Code. 

## <a name="create-a-nodejs-container-in-kubernetes"></a>Criar um contentor de Node.js no Kubernetes

Nesta secção, vai criar uma aplicação Web em Node.js e executá-la num contentor no Kubernetes.

### <a name="create-a-nodejs-web-app"></a>Criar uma aplicação Web em Node.js
Transfira o código do GitHub ao navegar para https://github.com/Azure/dev-spaces e selecione **Clone or Download** (Clonar ou Transferir) para transferir o repositório do GitHub para o seu ambiente local. O código para este guia está em `samples/nodejs/getting-started/webfrontend`.

## <a name="prepare-code-for-docker-and-kubernetes-development"></a>Preparar o código para o desenvolvimento do Docker e Kubernetes
Neste momento, tem uma aplicação Web básica que pode ser executada localmente. Agora, irá colocá-la em contentor ao criar recursos que definem o contentor da aplicação e como esta será implementada no Kubernetes. É fácil executar esta tarefa com o Azure Dev Spaces: 

1. Inicie o VS Code e abra a pasta `webfrontend`. (Pode ignorar os pedidos predefinidos para adicionar recursos de erro ou restaurar o projeto.)
1. Abra o Terminal Integrado no VS Code (através do menu **Ver > Terminal Integrado**).
1. Execute este comando (certifique-se de que **webfrontend** é a sua pasta atual):

    ```cmd
    azds prep --public
    ```

O comando `azds prep` da CLI do Azure gera recursos do Docker e Kubernetes com as predefinições:
* `./Dockerfile` descreve a imagem de contentor da aplicação e como o código-fonte é construído e executado no contentor.
* Um [gráfico Helm](https://docs.helm.sh) em `./charts/webfrontend` descreve como implementar o contentor no Kubernetes.

Por enquanto, não é necessário entender o conteúdo completo desses ficheiros. No entanto, importa realçar que **os mesmos recursos de configuração como código do Kubernetes e do Docker podem ser utilizados do desenvolvimento à produção, dando maior consistência em diferentes ambientes.**
 
É também geral um ficheiro com o nome `./azds.yaml` pelo comando `prep`, que é o ficheiro de configuração do Azure Dev Spaces. Este complementa os artefactos do Docker e do Kubernetes com uma configuração adicional que permite uma experiência de desenvolvimento iterativa no Azure.

## <a name="build-and-run-code-in-kubernetes"></a>Criar e executar códigos no Kubernetes
Vamos executar o nosso código! Na janela do terminal, execute este comando a partir da **pasta de código raiz**, webfrontend:

```cmd
azds up
```

Fique de olho no resultado do comando, vai reparar em várias coisas à medida que progride:
- O código fonte é sincronizado com o espaço de programação no Azure.
- É criada uma imagem de contentor no Azure, conforme especificado pelos ativos do Docker na sua pasta de códigos.
- São criados objetos do Kubernetes que utilizam a imagem do contentor, conforme especificado pelo gráfico Helm na sua pasta de códigos.
- São apresentadas as informações sobre o(s) ponto(s) final(ais) do contentor. No nosso caso, contamos com um URL HTTP público.
- Assumindo que os estágios acima sejam concluídos com sucesso, deve começar a ver o resultado `stdout` (e `stderr`), à medida que o contentor é iniciado.

> [!Note]
> Estes passos vão demorar mais tempo quando o comando `up` é executado pela primeira vez, mas as execuções seguintes deverão ser mais rápidas.

### <a name="test-the-web-app"></a>Testar a aplicação Web
Analise o resultado da consola para obter informações sobre o URL público que foi criado pelo comando `up`. Estará na forma: 

```
(pending registration) Service 'webfrontend' port 'http' will be available at <url>
Service 'webfrontend' port 80 (TCP) is available at http://localhost:<port>
```

Abra este URL numa janela do browser e deve ver o carregamento da aplicação Web. À medida que o contentor é executado, os resultados `stdout` e `stderr` são transmitidos para a janela do terminal.

> [!Note]
> Na primeira execução, pode demorar alguns minutos para que o DNS público esteja pronto. Se o URL público não resolver, pode utilizar o URL alternativo http://localhost:<portnumber> apresentado no resultado da consola. Se utilizar o URL de anfitrião local, poderá parecer que o contentor está a ser executado localmente, contudo, está a ser executado no AKS. Para sua comodidade e para facilitar a interação com o serviço da sua máquina local, os Espaços de Programador do Azure criam um túnel SSH temporário para o contentor em execução no Azure. Pode voltar atrás e tentar o URL público mais tarde, quando o registo DNS estiver pronto.

### <a name="update-a-content-file"></a>Atualizar um ficheiro de conteúdo
O Azure Dev Spaces não se limita apenas a pôr o código em execução no Kubernetes. Tem que ver com permitir-lhe ver, de forma rápida e iterativa, as alterações ao código serem aplicadas num ambiente do Kubernetes na cloud.

1. Localize o ficheiro `./public/index.html` e faça uma edição ao HTML. Por exemplo, altere a cor de fundo da página para um tom de azul:

    ```html
    <body style="background-color: #95B9C7; margin-left:10px; margin-right:10px;">
    ```

2. Guarde o ficheiro. Pouco depois, na janela do terminal, verá uma mensagem que diz que um ficheiro no contentor em execução foi atualizado.
1. Aceda ao seu browser e atualize a página. Deverá ver a cor atualizada.

O que aconteceu? As edições aos ficheiros de conteúdos, como HTML e CSS, não requerem o reinício do processo de Node.js, pelo que um comando `azds up` ativo irá sincronizar, de forma automática, qualquer ficheiro de conteúdo modificado diretamente com o contentor em execução no Azure, oferecendo-lhe, assim, um modo rápido de ver as edições aos seus conteúdos.

### <a name="test-from-a-mobile-device"></a>Testar num dispositivo móvel
Abra a aplicação Web num dispositivo móvel através do URL público para webfrontend. Pode copiar e enviar o URL da sua área de trabalho para o dispositivo, para não ter de inserir o endereço comprido. Quando a aplicação Web é carregada no dispositivo móvel, irá reparar que a IU não aparece corretamente num dispositivo pequeno.

Para corrigir este problema, vai adicionar uma etiqueta meta `viewport`:
1. Abra o ficheiro `./public/index.html`
1. Adicione a etiqueta meta `viewport` ao elemento `head` existente:

    ```html
    <head>
        <!-- Add this line -->
        <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    ```

1. Guarde o ficheiro.
1. Atualize o browser do dispositivo. Deverá ver a aplicação Web composta corretamente. 

Este exemplo mostra que alguns problemas só são detetados quando testa nos dispositivos nos quais a aplicação deve ser utilizada. Com o Azure Dev Spaces, pode iterar rapidamente o seu código e validar todas as alterações nos dispositivos de destino.

### <a name="update-a-code-file"></a>Atualizar um ficheiro de código
Atualizar os ficheiros de código do lado do servidor requer um pouco mais trabalho, porque a aplicação Node.js tem de ser reiniciada.

1. Na janela de terminal, prima `Ctrl+C` (para parar `azds up`).
1. Abra o ficheiro de código com o nome `server.js` e edite a mensagem “hello” do serviço: 

    ```javascript
    res.send('Hello from webfrontend running in Azure!');
    ```

3. Guarde o ficheiro.
1. Execute `azds up` na janela de terminal. 

Este comando recria a imagem do contentor e reimplementa o gráfico Helm. Recarregue a página do browser para ver as alterações ao código entrarem em vigor.

Mas existe um *método ainda mais rápido* para programar código, que vai explorar na próxima secção. 

## <a name="debug-a-container-in-kubernetes"></a>Depurar um contentor no Kubernetes

Nesta secção, ai utilizar o VS Code para depurar diretamente o nosso contentor em execução no Azure. Também vai aprender como obter um ciclo de edição-execução-teste mais rápido.

![](media/common/edit-refresh-see.png)

> [!Note]
> **Se ficar bloqueado** em qualquer altura, veja a secção [Resolução de problemas](troubleshooting.md) ou publique um comentário nesta página.

### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Inicializar ativos de depuração com a extensão do VS Code
Primeiro tem de configurar o seu projeto de código para que o VS Code comunique com o nosso espaço de programação no Azure. A extensão do VS Code para Espaços de Programação do Azure oferece um comando auxiliar para contribuir para a configuração da depuração. 

Abra a **Paleta de Comandos** (através do menu **Ver | Paleta de Comandos**), e utilize o preenchimento automático para escrever e selecione este comando: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. 

Esta ação adiciona a configuração de depuração para os Espaços de Programação do Azure na pasta `.vscode`. Este comando não deve ser confundido com o comando `azds prep`, que configura o projeto para implementação.

![](media/common/command-palette.png)

### <a name="select-the-azds-debug-configuration"></a>Selecionar a configuração de depuração do AZDS
1. Para abrir a vista Debug (Depuração), clique no ícone Debug em **Activity Bar** (Barra de Atividades), no lado do VS Code.
1. Selecione **Launch Program (AZDS)** (Iniciar Programa [AZDS]) como a configuração de depuração ativa.

![](media/get-started-node/debug-configuration-nodejs2.png)

> [!Note]
> Se não vir nenhum comando do Azure Dev Spaces em Command Palette (Paleta de Comandos), confirme que tem [instalada a extensão do VS Code para o Azure Dev Spaces](get-started-nodejs.md#get-kubernetes-debugging-for-vs-code).

### <a name="debug-the-container-in-kubernetes"></a>Depurar o contentor no Kubernetes
Prima **F5** para depurar o código no Kubernetes.

Semelhante ao comando `up`, o código é sincronizado com o ambiente de desenvolvimento quando iniciar a depuração e é criado e implementado um contentor no Kubernetes. Desta vez, o depurador é ligado ao contentor remoto.

> [!Tip]
> A barra de estado do VS Code apresentará um URL clicável.

![](media/common/vscode-status-bar-url.png)

Defina um ponto de interrupção num ficheiro de código do lado do servidor, como, por exemplo, dentro de `app.get('/api'...` em `server.js`. Atualize a página do browser ou prima o botão “Say It Again” (“Repetir”) e deverá atingir o ponto de interrupção e conseguir aceder ao código.

Tem acesso total às informações da depuração, tal como aconteceria se o código estivesse a ser executado localmente, como, por exemplo, a pilha de chamadas, as variáveis locais, as informações de exceção, etc.

### <a name="edit-code-and-refresh-the-debug-session"></a>Editar o código e atualizar a sessão de depuração
Com o depurador ativo, faça uma edição ao código. Por exemplo, modifique outra vez a mensagem “hello”:

```javascript
app.get('/api', function (req, res) {
    res.send('**** Hello from webfrontend running in Azure! ****');
});
```

Guarde o ficheiro e, no **painel Debug actions** (Ações de depuração), clique no botão **Refresh** (Atualizar). 

![](media/get-started-node/debug-action-refresh-nodejs.png)

Em vez de reconstruir e reimplementar uma imagem de contentor nova sempre que são feitas edições ao código, o que, muitas vezes, demorará tempo considerável, o Azure Dev Spaces irá reiniciar o processo de Node.js entre sessões de depuração para proporcionar um ciclo de edição/depuração mais rápido.

Atualize a aplicação Web no browser ou prima o botão *Say It Again* (Repetir). Deverá ver a mensagem personalizada apresentada na IU.

### <a name="use-nodemon-to-develop-even-faster"></a>Utilizar Nodemon para desenvolver ainda mais depressa
O *Nodemon* é uma ferramenta popular que os programadores de Node.js utilizam para o desenvolvimento rápido. Em vez de reiniciar manualmente o processo de Node sempre que é feita uma edição ao código do lado do servidor, os programadores irão, muitas vezes, configurar os projetos de Node de modo a que o *nodemon* monitorize as alterações aos ficheiros e reinicie automaticamente o processo do servidor. Neste estilo de trabalho, o programador só tem de atualizar o browser e fazer uma edição ao código.

Com o Azure Dev Spaces, pode utilizar muitos dos fluxos de trabalho de desenvolvimento que utiliza quando desenvolve localmente. Para ilustrar este aspeto, o projeto de exemplo `webfrontend` foi configurado para utilizar o *nodemon* (está configurado como dependência de desenvolvimento em `package.json`).

Experimente os passos seguintes:
1. Pare o depurador do VS Code.
1. Clique no ícone de Depuração em **Activity Bar** (Barra de Atividade), no lado do VS Code. 
1. Selecione **Attach (AZDS)** (Anexar [AZDS] como a configuração ativa do depurador.
1. Prima F5.

Nesta configuração, o contentor é configurado para iniciar o *nodemon*. Quando forem feitas edições ao código, o *nodemon* reinicia automaticamente o processo de Node, tal como acontece quando desenvolve localmente. 
1. Edite a mensagem “hello” novamente em `server.js`e guarde o ficheiro.
1. Atualize o browser ou clique no botão *Say It Again* (Repetir) para ver as alterações a entrarem em vigor.

**Agora, tem um método para iterar rapidamente no código e depurar diretamente no Kubernetes.** Em seguida, irá ver como pode criar e chamar um segundo contentor.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Aprenda sobre o desenvolvimento de múltiplos serviço](multi-service-nodejs.md)

