---
title: Desenvolvimento em equipa no Kubernetes através dos espaços de desenvolvimento do Azure
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 04/25/2019
ms.topic: quickstart
description: Desenvolvimento em equipe Kubernetes com contentores e microsserviços no Azure
keywords: Docker, o Kubernetes, o Azure, o AKS, o serviço Kubernetes do Azure, contentores, Helm, a malha de serviço, roteamento de malha do serviço, kubectl, k8s
manager: jeconnoc
ms.openlocfilehash: 1414cc56809263a90a4bfea16db332e2f6a5e0bc
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/25/2019
ms.locfileid: "64514083"
---
# <a name="quickstart-team-development-on-kubernetes-using-azure-dev-spaces"></a>Início rápido: Desenvolvimento em equipa no Kubernetes através dos espaços de desenvolvimento do Azure

Neste guia, vai aprender a:

- Configure espaços de desenvolvimento do Azure num cluster de Kubernetes gerido no Azure.
- Implantar um aplicativo grande com múltiplos microsserviços para um espaço de desenvolvimento.
- Teste um único microsserviço num espaço de desenvolvimento isolado dentro do contexto de todo o aplicativo.

![Aplicação de exemplo partilha de bicicletas de espaços de desenvolvimento do Azure](media/quickstart-team-development/bikeshare.png)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free).
- A [CLI do Azure instalada](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Helm 2.13 ou superior instalado](https://github.com/helm/helm/blob/master/docs/install.md).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Criar um cluster do Azure Kubernetes Service

Tem de criar um cluster do AKS numa [suportada região](https://docs.microsoft.com/azure/dev-spaces/#a-rapid,-iterative-kubernetes-development-experience-for-teams). Os comandos abaixo, crie um grupo de recursos chamado *MyResourceGroup* e um cluster do AKS chamado *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --disable-rbac --generate-ssh-keys
```

O *MyAKS* cluster também é criado com um nó, utilizando o *Standard_DS2_v2* tamanho e com RBAC desativada.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Ativar os espaços de desenvolvimento do Azure no seu cluster do AKS

Utilize o `use-dev-spaces` comando para ativar os espaços de desenvolvimento no seu cluster do AKS e siga as instruções. O comando abaixo permite que os espaços de desenvolvimento no *MyAKS* cluster no *MyResourceGroup* agrupar e cria um espaço de desenvolvimento chamado *dev*.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Obter o código de aplicativo de exemplo

Neste artigo, vai utilizar o [aplicação de exemplo de partilha de bicicletas do Azure Dev espaços](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) para demonstrar a utilização de espaços de desenvolvimento do Azure.

Clonar a aplicação a partir do GitHub e navegue para o seu diretório:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>Obter o HostSuffix para *dev*

Utilize o `azds show-context` comando para mostrar o HostSuffix para *dev*.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>Atualizar o gráfico Helm com sua HostSuffix

Open [charts/values.yaml](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml) e substitua todas as instâncias de `<REPLACE_ME_WITH_HOST_SUFFIX>` com o valor de HostSuffix obtido anteriormente. Guardar as alterações e feche o ficheiro.

## <a name="run-the-sample-application-in-kubernetes"></a>Executar o exemplo de aplicação no Kubernetes

Os comandos para executar o exemplo de aplicação no Kubernetes fazem parte de um processo existente e não tem nenhuma dependência nas ferramentas de espaços de desenvolvimento do Azure. Neste caso, o Helm é as ferramentas utilizadas para executar este aplicativo de exemplo mas outras ferramentas podem ser utilizadas para executar seu aplicativo inteiro num espaço de nomes dentro de um cluster. Os comandos de Helm como objetivo o espaço de desenvolvimento com o nome *dev* que criou anteriormente, mas este espaço de desenvolvimento também é um espaço de nomes do Kubernetes. Como resultado, os espaços de desenvolvimento podem ser alvo de outras ferramentas o mesmo que outros espaços de nomes.

Pode utilizar espaços de desenvolvimento do Azure para desenvolvimento em equipe, depois de um aplicativo está em execução num cluster, independentemente das ferramentas utilizadas para implementá-la.

Utilize o `helm init` e `helm install` comandos para configurar e instalar a aplicação de exemplo no seu cluster.

```cmd
cd charts/
helm init --wait
helm install -n bikesharing . --dep-up --namespace dev --atomic --wait
```

O `helm install` comando pode demorar vários minutos a concluir. O resultado do comando mostra o estado de todos os serviços que ele implementada no cluster quando concluída:

```cmd
$ cd charts/
$ helm init --wait
...
Happy Helming!

$ helm install -n bikesharing . --dep-up --namespace dev --atomic --wait

Hang tight while we grab the latest from your chart repositories...
...
NAME               READY  UP-TO-DATE  AVAILABLE  AGE
bikes              1/1    1           1          4m32s
bikesharingweb     1/1    1           1          4m32s
billing            1/1    1           1          4m32s
gateway            1/1    1           1          4m32s
reservation        1/1    1           1          4m32s
reservationengine  1/1    1           1          4m32s
users              1/1    1           1          4m32s
```

Depois do exemplo de aplicação é instalada no seu cluster e, uma vez que tiver ativado no seu cluster de espaços de desenvolvimento, utilize o `azds list-uris` comando para apresentar os URLs para a aplicação de exemplo na *dev* que está selecionada.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Navegue para o *bikesharingweb* service ao abrir o URL público a partir do `azds list-uris` comando. No exemplo acima, o URL público para o *bikesharingweb* é de serviço *http://dev.bikesharingweb.fedcab0987.eus.azds.io/*. Selecione *Aurelia Briggs (cliente)* como o utilizador. Certifique-se de ver o texto *Olá Aurelia Briggs | Terminar sessão* na parte superior.

## <a name="create-child-dev-spaces"></a>Criar subordinado espaços de desenvolvimento

Utilize o `azds space select` comando para criar dois espaços de subordinado, sob *dev*:

```cmd
azds space select -n dev/azureuser1 -y
azds space select -n dev/azureuser2 -y
```

Os comandos acima criam dois espaços de subordinados sob *dev* com o nome *azureuser1* e *azureuser2*. Estes espaços de dois subordinados representam espaços distintos de desenvolvimento para desenvolvedores *azureuser1* e *azureuser2* a utilizar para fazer alterações à aplicação de exemplo.

Utilize o `azds space list` comando para listar todos os espaços de desenvolvimento e confirmar *dev/azureuser2* está selecionada.

```cmd
$ azds space list
Name            Selected
--------------  --------
default         False
dev             False
dev/azureuser1  False
dev/azureuser2  True
```

Utilize o `azds list-uris` para apresentar os URLs da aplicação de exemplo no espaço atualmente selecionado que é *dev/azureuser2*.

```cmd
$ azds list-uris
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
```

Confirme que os URLs apresentado pela `azds list-uris` comando tem o *azureuser2.s.dev* prefixo. Este prefixo confirma que é o atual espaço selecionado *azureuser2*, que é um elemento subordinado *dev*.

Navegue para o *bikesharingweb* serviço para o *dev/azureuser2* espaço de desenvolvimento, abrindo a URL pública a partir do `azds list-uris` comando. No exemplo acima, o URL público para o *bikesharingweb* é de serviço *http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/*. Selecione *Aurelia Briggs (cliente)* como o utilizador. Certifique-se de ver o texto *Olá Aurelia Briggs | Terminar sessão* na parte superior.

## <a name="update-code"></a>Atualizar código

Open *BikeSharingWeb/components/Header.js* com um editor de texto e alterar o texto no [elemento span com o `userSignOut` className](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/components/Header.js#L16).

```html
<span className="userSignOut">
    <Link href="/devsignin"><span tabIndex="0">Welcome {props.userName} | Sign out</span></Link>
</span>
```

Guardar as alterações e feche o ficheiro.

## <a name="build-and-run-the-updated-bikesharingweb-service-in-the-devazureuser2-dev-space"></a>Criar e executar o serviço de bikesharingweb atualizado na *dev/azureuser2* espaço do Programador

Navegue para o *BikeSharingWeb /* diretório e execute o `azds up` comando.

```cmd
$ cd ../BikeSharingWeb/
$ azds up

Using dev space 'dev/azureuser2' with target 'MyAKS'
Synchronizing files...2s
...
Service 'bikesharingweb' port 'http' is available at http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/
Service 'bikesharingweb' port 80 (http) is available at http://localhost:54256
...
```

Este comando é compilada e executada o *bikesharingweb* serviço no *dev/azureuser2* espaço de desenvolvimento. Este serviço é executado com o *bikesharingweb* serviço em execução na *dev* e é usado apenas para pedidos com o *azureuser2.s* prefixo de URL. Para obter mais informações sobre como roteamento funciona entre pai e filho espaços de desenvolvimento, consulte [como os espaços de desenvolvimento do Azure funciona e é configurado](how-dev-spaces-works.md).

Navegue para o *bikesharingweb* serviço para o *dev/azureuser2* espaço de desenvolvimento ao abrir o URL público apresentado no resultado do `azds up` comando. Selecione *Aurelia Briggs (cliente)* como o utilizador. Certifique-se de que veja o texto atualizado no canto superior direito. Se pretender atualizar a página ou limpar a cache do browser se não vir logo esta alteração.

![O Azure partilha de bicicletas de espaços de desenvolvimento aplicativo de exemplo atualizado](media/quickstart-team-development/bikeshare-update.png)

## <a name="verify-other-dev-spaces-are-unchanged"></a>Certifique-se de que outros espaços de desenvolvimento permanecem inalterados em

Se o `azds up` ainda está a executar o comando, prima *Ctrl + c*.

```cmd
$ azds list-uris --all
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser1.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser1.s.dev.gateway.fedcab0987.eus.azds.io/         Available
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
http://dev.bikesharingweb.fedcab0987.eus.azds.io/               Available
http://dev.gateway.fedcab0987.eus.azds.io/                      Available
```

Navegue para o *dev* versão do *bikesharingweb* no seu browser, escolha *Aurelia Briggs (cliente)* como o utilizador e certifique-se de que veja o texto original no canto superior direito canto. Repita estes passos com o *dev/azureuser1* URL. Observe que as alterações só são aplicadas para o *dev/azureuser2* versão do *bikesharingweb*. Esse isolamento de alterações *dev/azureuser2* permite *azureuser2* para efetuar alterações sem afetar *azureuser1*.

Ter essas alterações serão refletidas na *dev* e *dev/azureuser1*, deve seguir o fluxo de trabalho existente ou pipeline CI/CD da sua equipa. Por exemplo, este fluxo de trabalho pode envolver a consolidar as alterações ao seu sistema de controle de versão e implantando a atualização com um pipeline de CI/CD ou as ferramentas, como o Helm.

## <a name="clean-up-your-azure-resources"></a>Limpar os recursos do Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passos Seguintes

Saiba como os Espaços de Programador do Azure ajudam a desenvolver aplicações mais complexas em vários contentores e como pode simplificar o desenvolvimento de colaboração ao trabalhar com diferentes versões ou ramos do seu código em diferentes espaços.

> [!div class="nextstepaction"]
> [Working with multiple containers and team development](multi-service-nodejs.md) (Trabalhar com vários contentores e o desenvolvimento em equipa)