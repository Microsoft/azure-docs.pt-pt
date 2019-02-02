---
title: Criar um espaço de programador do Kubernetes na cloud | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: stepro
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
ms.author: stephpr
ms.date: 09/26/2018
ms.topic: quickstart
description: Desenvolvimento rápido da Kubernetes com contentores e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores
manager: mmontwil
ms.openlocfilehash: 24bb5a044db813e1ee3cf2c9ff3e36a9b9ff86d7
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2019
ms.locfileid: "55657064"
---
# <a name="quickstart-create-a-kubernetes-dev-space-with-azure-dev-spaces-java-and-vs-code"></a>Início rápido: Criar um espaço de desenvolvimento de Kubernetes com espaços de desenvolvimento do Azure (Java e o VS Code)

Neste guia, vai aprender a:

- Configurar os Espaços de Programador do Azure com um cluster Kubernetes gerido no Azure.
- Utilizar o VS Code e a linha de comandos para desenvolver iterativamente código em contentores.
- Depurar o código no espaço de programador do VS Code.

> [!Note]
> **Se ficar bloqueado** em qualquer altura, veja a secção [Resolução de problemas](troubleshooting.md) ou publique um comentário nesta página. Também pode consultar o [tutorial](get-started-java.md) mais detalhado.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma, poderá [criar uma conta gratuita](https://azure.microsoft.com/free).
- [Visual Studio Code](https://code.visualstudio.com/download).
- Versão 2.0.43 ou superior da [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).
- Um cluster Kubernetes a executar o Kubernetes 1.10.3 ou posterior, na região EUA Leste, EUA Leste 2, EUA Central, EUA Oeste 2, Europa Ocidental, Sudeste Asiático, Canadá Central ou Leste do Canadá, com o **Encaminhamento de Aplicações de HTTP** ativado.

    ```cmd
    az group create --name MyResourceGroup --location <region>
    az aks create -g MyResourceGroup -n myAKS --location <region> --kubernetes-version 1.10.9 --enable-addons http_application_routing --generate-ssh-keys
    ```

## <a name="set-up-azure-dev-spaces"></a>Configurar os Espaços de Programador do Azure

1. Configure Espaços de Programador no cluster AKS: `az aks use-dev-spaces -g MyResourceGroup -n MyAKS`
1. Transfira a [extensão dos Espaços de Programador do Azure](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) para o VS Code. Clique em Instalar uma vez na página do Marketplace da extensão e, novamente, no VS Code.
1. Transfira a extensão [Java Debugger for Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) para o VS Code. Clique em Instalar uma vez na página do Marketplace da extensão e, novamente, no VS Code.

## <a name="build-and-run-code-in-kubernetes"></a>Criar e executar códigos no Kubernetes

1. Transfira o código de exemplo do GitHub: [https://github.com/Azure/dev-spaces](https://github.com/Azure/dev-spaces) 
1. Altere o diretório para a pasta de webfrontend: `cd dev-spaces/samples/java/getting-started/webfrontend`
1. Gere elementos de gráfico do Docker e do Helm: `azds prep --public`
1. Compile e execute o código no AKS. Na janela do terminal, na **pasta webfrontend**, execute este comando: `azds up`
1. Analise o resultado da consola para obter informações sobre o URL que foi criado pelo comando `up`. Estará na forma:

   ```output
    (pending registration) Service 'webfrontend' port 'http' will be available at <url>
    Service 'webfrontend' port 80 (TCP) is available at http://localhost:<port>
   ```

   Abra este URL numa janela do browser e deve ver o carregamento da aplicação Web.

   > [!Note]
   > Na primeira execução, pode demorar alguns minutos para que o DNS público esteja pronto. Se o URL público não resolver, pode utilizar o URL alternativo http://localhost:<portnumber> apresentado no resultado da consola. Se utilizar o URL de anfitrião local, poderá parecer que o contentor está a ser executado localmente, contudo, está a ser executado no AKS. Para sua comodidade e para facilitar a interação com o serviço da sua máquina local, os Espaços de Programador do Azure criam um túnel SSH temporário para o contentor em execução no Azure. Pode voltar atrás e tentar o URL público mais tarde, quando o registo DNS estiver pronto.

### <a name="update-a-code-file"></a>Atualizar um ficheiro de código

1. Na janela de terminal, prima `Ctrl+C` (para parar `azds up`).
1. Abra o ficheiro de código com o nome `src/main/java/com/ms/sample/webfrontend/Application.java` e edite a mensagem de saudação: `return "Hello from webfrontend in Azure!";`
1. Guarde o ficheiro.
1. Execute `azds up` na janela de terminal.

Este comando recria a imagem do contentor e reimplementa o gráfico Helm. Para ver as alterações ao código entrarem em vigor na aplicação em execução, basta atualizar o browser.

Mas existe um *método ainda mais rápido* para programar código, que vai explorar na próxima secção.

## <a name="debug-a-container-in-kubernetes"></a>Depurar um contentor no Kubernetes

Nesta secção, vai utilizar o VS Code para depurar diretamente o contentor em execução no Azure. Também vai aprender como obter um ciclo de edição-execução-teste mais rápido.

![](./media/common/edit-refresh-see.png)

### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Inicializar ativos de depuração com a extensão do VS Code
Primeiro tem de configurar o projeto de código para que o VS Code comunique com o espaço de programador no Azure. A extensão do VS Code para Espaços de Programação do Azure oferece um comando auxiliar para contribuir para a configuração da depuração.

Abra a **Paleta de Comandos** (através do menu **Ver | Paleta de Comandos**), e utilize o preenchimento automático para escrever e selecione este comando: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.

Esta ação adiciona a configuração de depuração para os Espaços de Programação do Azure na pasta `.vscode`.

![](./media/common/command-palette.png)

### <a name="select-the-azds-debug-configuration"></a>Selecionar a configuração de depuração do AZDS
1. Para abrir a vista Debug (Depuração), clique no ícone Debug em **Activity Bar** (Barra de Atividades), no lado do VS Code.
1. Selecione **Iniciar Programa Java (AZDS)** como a configuração de depuração ativa.

![](media/get-started-java/debug-configuration.png)

> [!Note]
> Se não vir nenhum comando do Azure Dev Spaces em Command Palette (Paleta de Comandos), confirme que tem instalada a extensão do VS Code para o Azure Dev Spaces. Lembre-se de que a área de trabalho que abriu no VS Code é a pasta que contém azds.yaml.

### <a name="debug-the-container-in-kubernetes"></a>Depurar o contentor no Kubernetes
Prima **F5** para depurar o código no Kubernetes.

Tal como sucede com o comando `up`, o código é sincronizado com o espaço de programador e é criado e implementado um contentor no Kubernetes. Desta vez, obviamente, o depurador está ligado ao contentor remoto.

> [!Tip]
> A barra de estado do VS Code apresentará um URL clicável.

Defina um ponto de interrupção num ficheiro de código do lado do servidor, como, por exemplo, a função `greeting()` no ficheiro de origem `src/main/java/com/ms/sample/webfrontend/Application.java`. Atualizar a página do browser faz com que o ponto de interrupção seja atingido.

Tem acesso total às informações da depuração, tal como aconteceria se o código estivesse a ser executado localmente, como, por exemplo, a pilha de chamadas, as variáveis locais, as informações de exceção, etc.

### <a name="edit-code-and-refresh"></a>Editar o código e atualizar
Com o depurador ativo, faça uma edição ao código. Por exemplo, modifique a saudação em `src/main/java/com/ms/sample/webfrontend/Application.java`. 

```java
public String greeting()
{
    return "I'm debugging Java code in Azure!";
}
```

Guarde o ficheiro e, no **painel Debug actions** (Ações de depuração), clique no botão **Refresh** (Atualizar). 

![](media/get-started-java/debug-action-refresh.png)

Em vez de reconstruir e reimplementar uma imagem de contentor nova sempre que forem feitas edições ao código, o que, muitas vezes, irá demorar um tempo considerável, o Azure Dev Spaces recompilará incrementalmente o código dentro do contentor existente para proporcionar um ciclo de edição/depuração mais rápido.

Atualize a aplicação Web no browser. Deverá ver a mensagem personalizada apresentada na IU.

**Agora, tem um método para iterar rapidamente no código e depurar diretamente no Kubernetes.**

## <a name="next-steps"></a>Passos Seguintes

Saiba como os Espaços de Programador do Azure ajudam a desenvolver aplicações mais complexas em vários contentores e como pode simplificar o desenvolvimento de colaboração ao trabalhar com diferentes versões ou ramos do seu código em diferentes espaços.

> [!div class="nextstepaction"]
> [Working with multiple containers and team development](multi-service-java.md) (Trabalhar com vários contentores e o desenvolvimento em equipa)
