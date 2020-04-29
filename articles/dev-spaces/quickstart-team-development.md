---
title: Desenvolvimento de equipa em Kubernetes
services: azure-dev-spaces
ms.date: 01/22/2020
ms.topic: quickstart
description: Este quickstart mostra-lhe como fazer o desenvolvimento da equipa Kubernetes com recipientes e microserviços com espaços Azure Dev
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 0fe177db420913e5d68807dd803df791653c0914
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78244945"
---
# <a name="quickstart-team-development-on-kubernetes---azure-dev-spaces"></a>Quickstart: Desenvolvimento de equipa em Kubernetes - Espaços Azure Dev

Neste guia, vai aprender a:

- Instale o Azure Dev Spaces num aglomerado de Kubernetes gerido em Azure.
- Implemente uma grande aplicação com vários microserviços para um espaço de v.
- Teste um único microserviço num espaço de v isolado no contexto da aplicação completa.

![Desenvolvimento da equipa Azure Dev Spaces](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição Azure, pode criar uma [conta gratuita.](https://azure.microsoft.com/free)
- A [CLI do Azure instalada](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Leme 3 instalado.][helm-installed]

## <a name="create-an-azure-kubernetes-service-cluster"></a>Criar um cluster de serviço Azure Kubernetes

É preciso criar um aglomerado AKS numa [região apoiada.][supported-regions] Os comandos abaixo criam um grupo de recursos chamado *MyResourceGroup* e um cluster AKS chamado *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Enable Azure Dev Spaces no seu cluster AKS

Utilize `use-dev-spaces` o comando para ativar os Espaços Dev no seu cluster AKS e siga as instruções. O comando abaixo permite a Dev Spaces no cluster *MyAKS* no grupo *MyResourceGroup* e cria um espaço dev chamado *dev*.

> [!NOTE]
> O `use-dev-spaces` comando também instalará o Azure Dev Spaces CLI se ainda não estiver instalado. Não é possível instalar o Azure Dev Spaces CLI na Casca de Nuvem Azure.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Obter código de aplicação de amostra

Neste artigo, utiliza a aplicação de partilha de [bicicletas Azure Dev Spaces](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) para demonstrar a utilização dos Espaços Azure Dev.

Clone a aplicação do GitHub e navegue para o seu diretório:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>Recupere o HostSuffix para *dev*

Utilize `azds show-context` o comando para mostrar o HostSuffix para *dev*.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>Atualize a tabela Helm com o seu HostSuffix

Abra [gráficos/valores.yaml](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml) e `<REPLACE_ME_WITH_HOST_SUFFIX>` substitua todas as instâncias com o valor hostSufix que recuperou anteriormente. Guarde as suas alterações e feche o ficheiro.

## <a name="run-the-sample-application-in-kubernetes"></a>Executar a aplicação da amostra em Kubernetes

Os comandos para executar a aplicação da amostra em Kubernetes fazem parte de um processo existente e não têm dependência da ferramenta Azure Dev Spaces. Neste caso, helm é a ferramenta usada para executar esta aplicação de amostra, mas outras ferramentas poderiam ser usadas para executar toda a sua aplicação em um espaço de nome dentro de um cluster. Os comandos Helm estão a visar o espaço dev chamado *dev* que criaste anteriormente, mas este espaço de v também é um espaço de nome Kubernetes. Como resultado, os espaços de dev podem ser direcionados por outras ferramentas que outros espaços de nome.

Pode utilizar o Azure Dev Spaces para o desenvolvimento da equipa depois de uma aplicação estar a funcionar num cluster, independentemente da ferramenta utilizada para a sua implantação.

Utilize `helm install` o comando para configurar e instalar a aplicação da amostra no seu cluster.

```cmd
cd charts/
helm install bikesharingsampleappsampleapp . --dependency-update --namespace dev --atomic
```

O `helm install` comando pode levar vários minutos para ser concluído. Depois de a aplicação da amostra ser instalada no seu cluster e `azds list-uris` uma vez que tem Espaços Dev ativados no seu cluster, utilize o comando para exibir os URLs para a aplicação da amostra em *dev* que está atualmente selecionada.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Navegue para o serviço *bikesharingweb* abrindo o URL público a partir do `azds list-uris` comando. No exemplo acima, o URL público para `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`o serviço *bikesharingweb* é . Selecione *Aurelia Briggs (cliente)* como utilizador. Verifique se vê o texto *Hi Aurelia Briggs Assine* no topo.

![Aplicação de amostra de partilha de bicicletas Azure Dev Spaces](media/quickstart-team-development/bikeshare.png)

## <a name="create-child-dev-spaces"></a>Criar espaços de dev para crianças

Utilize `azds space select` o comando para criar dois espaços infantis sob *dev:*

```cmd
azds space select -n dev/azureuser1 -y
azds space select -n dev/azureuser2 -y
```

Os comandos acima criam dois espaços infantis sob *o* nome de *azureuser1* e *azureuser2*. Estes dois espaços infantis representam espaços de v distintos para os desenvolvedores *azureuser1* e *azureuser2* para fazer alterações na aplicação da amostra.

Utilize `azds space list` o comando para listar todos os espaços de v e confirmar se foi selecionado *dev/azureuser2.*

```cmd
$ azds space list
   Name            DevSpacesEnabled
-  --------------  ----------------
   default         False
   dev             True
   dev/azureuser1  True
*  dev/azureuser2  True
```

Utilize `azds list-uris` os URLs para exibir os URLs para a aplicação da amostra no espaço atualmente selecionado que é *dev/azureuser2*.

```cmd
$ azds list-uris
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
```

Confirme se os URLs `azds list-uris` apresentados pelo comando têm o prefixo *azureuser2.s.dev.* Este prefixo confirma que o espaço atual selecionado é *azureuser2*, que é uma criança de *v*.

Navegue para o serviço *bikesharingweb* para o espaço *dev/azureuser2 dev* abrindo o URL público a partir do `azds list-uris` comando. No exemplo acima, o URL público para `http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/`o serviço *bikesharingweb* é . Selecione *Aurelia Briggs (cliente)* como utilizador. Verifique se vê o texto *Hi Aurelia Briggs Assine* no topo.

## <a name="update-code"></a>Atualizar código

Abra *bikeSharingWeb/componentes/Header.js* com um editor de texto e altere o texto no [elemento de extensão com o nome de `userSignOut` classe](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/components/Header.js#L16).

```html
<span className="userSignOut">
    <Link href="/devsignin"><span tabIndex="0">Welcome {props.userName} | Sign out</span></Link>
</span>
```

Guarde as suas alterações e feche o ficheiro.

## <a name="build-and-run-the-updated-bikesharingweb-service-in-the-devazureuser2-dev-space"></a>Construa e corra o serviço atualizado de bikesharingweb no espaço *dev/azureuser2* dev

Navegue para o *bikeSharingWeb/diretório* e execute o `azds up` comando.

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

Este comando constrói e executa o serviço *bikesharingweb* no espaço *dev/azureuser2* dev. Este serviço funciona para além do serviço *bikesharingweb* em *dev* e é usado apenas para pedidos com o prefixo URL *azureuser2.s.* Para obter mais informações sobre como funciona o encaminhamento entre os espaços de dev dos pais e das crianças, consulte [como funciona o Azure Dev Spaces e está configurado](how-dev-spaces-works.md).

Navegue para o serviço *bikesharingweb* para o espaço *dev/azureuser2* dev, `azds up` abrindo o URL público exibido na saída do comando. Selecione *Aurelia Briggs (cliente)* como utilizador. Verifique se vê o texto atualizado no canto superior direito. Poderá ter de atualizar a página ou limpar a cache do seu navegador se não vir imediatamente esta alteração.

![Aplicação de partilha de bicicletas Azure Dev Spaces atualizada](media/quickstart-team-development/bikeshare-update.png)

> [!NOTE]
> Quando navega para o `azds up`seu serviço durante a execução, os vestígios de pedido http também são apresentados na saída do `azds up` comando. Estes vestígios podem ajudá-lo a resolver problemas e a depurar o seu serviço. Pode desativar `--disable-http-traces` estes `azds up`vestígios utilizando durante a execução .

## <a name="verify-other-dev-spaces-are-unchanged"></a>Verifique se outros Espaços Dev estão inalterados

Se `azds up` o comando ainda estiver em funcionamento, prima *Ctrl+c*.

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

Navegue para a versão *dev* da *bikesharingweb* no seu navegador, escolha *Aurelia Briggs (cliente)* como utilizador, e verifique se vê o texto original no canto superior direito. Repita estes passos com o URL *dev/azureuser1.* Note que as alterações só são aplicadas na versão *dev/azureuser2* da *bikesharingweb*. Este isolamento de alterações ao *dev/azureuser2* permite que *o azureuser2* faça alterações sem afetar *o azureuser1*.

Para que estas alterações se reflitam no *dev* e *dev/azureuser1,* deve seguir o fluxo de trabalho existente da sua equipa ou o gasoduto CI/CD existente. Por exemplo, este fluxo de trabalho pode envolver comprometer a sua alteração no seu sistema de controlo de versão e implementar a atualização utilizando um pipeline CI/CD ou ferramentas como o Helm.

## <a name="clean-up-your-azure-resources"></a>Limpe os seus recursos Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passos seguintes

Saiba como os Espaços de Programador do Azure ajudam a desenvolver aplicações mais complexas em vários contentores e como pode simplificar o desenvolvimento de colaboração ao trabalhar com diferentes versões ou ramos do seu código em diferentes espaços.

> [!div class="nextstepaction"]
> [Working with multiple containers and team development](multi-service-nodejs.md) (Trabalhar com vários contentores e o desenvolvimento em equipa)

[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
