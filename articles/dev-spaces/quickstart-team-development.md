---
title: Desenvolvimento de equipas em Kubernetes
services: azure-dev-spaces
ms.date: 01/22/2020
ms.topic: quickstart
description: Este quickstart mostra-lhe como fazer o desenvolvimento da equipa Kubernetes com contentores e microserviços com Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 3b4aae5c249e4dcbc4365c98791415e9fdc55c0c
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080441"
---
# <a name="quickstart-team-development-on-kubernetes---azure-dev-spaces"></a>Quickstart: Desenvolvimento de equipas em Kubernetes - Azure Dev Spaces

Neste guia, vai aprender a:

- Instale Azure Dev Spaces num cluster gerido de Kubernetes em Azure.
- Implemente uma grande aplicação com múltiplos microserviços para um espaço dev.
- Teste um único microserviço num espaço dev isolado no contexto da aplicação completa.

![Desenvolvimento da equipa Azure Dev Spaces](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode criar uma [conta gratuita.](https://azure.microsoft.com/free)
- A [CLI do Azure instalada](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Leme 3 instalado][helm-installed].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Criar um cluster do Azure Kubernetes Service

Você deve criar um cluster AKS em uma [região apoiada][supported-regions]. Os comandos abaixo criam um grupo de recursos chamado *MyResourceGroup* e um cluster AKS chamado *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Ativar espaços Azure Dev no seu cluster AKS

Utilize o `use-dev-spaces` comando para ativar os Espaços Dev no seu cluster AKS e siga as indicações. O comando abaixo permite espaços Dev no cluster *MyAKS* no grupo *MyResourceGroup* e cria um espaço dev chamado *dev*.

> [!NOTE]
> O `use-dev-spaces` comando também instalará o CLI dos Espaços Azure Dev se ainda não estiver instalado. Não é possível instalar o Azure Dev Spaces CLI na Concha da Nuvem Azure.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Obtenha o código de aplicação da amostra

Neste artigo, você usa a [aplicação de bicicleta Azure Dev Spaces Partilhar](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) para demonstrar usando Azure Dev Spaces.

Clone a aplicação do GitHub e navegue no seu diretório:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>Recupere o hostS sulé para *dev*

Utilize o `azds show-context` comando para mostrar o hostS sulé para *dev*.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>Atualize o gráfico Helm com o seu HostS sulé

Abra [gráficos/valores.yaml](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml) e substitua todas as instâncias `<REPLACE_ME_WITH_HOST_SUFFIX>` do valor HostS suix que recuperou anteriormente. Guarde as suas alterações e feche o ficheiro.

## <a name="run-the-sample-application-in-kubernetes"></a>Executar o pedido de amostra em Kubernetes

Os comandos para executar a aplicação da amostra em Kubernetes fazem parte de um processo existente e não têm dependência da ferramenta Azure Dev Spaces. Neste caso, helm é a ferramenta usada para executar esta aplicação de amostra, mas outras ferramentas poderiam ser usadas para executar toda a sua aplicação em um espaço de nome dentro de um cluster. Os comandos Helm estão direcionados para o espaço dev chamado *dev* que você criou anteriormente, mas este espaço dev também é um espaço de nome Kubernetes. Como resultado, os espaços dev podem ser alvo de outras ferramentas como outros espaços de nome.

Você pode usar Azure Dev Spaces para o desenvolvimento de equipe depois de uma aplicação estar em execução em um cluster, independentemente da ferramenta usada para implantá-lo.

Utilize o `helm install` comando para configurar e instalar a aplicação da amostra no seu cluster.

```cmd
cd charts/
helm install bikesharingsampleappsampleapp . --dependency-update --namespace dev --atomic
```

O `helm install` comando pode demorar vários minutos a ser concluído. Depois de instalar a aplicação da amostra no seu cluster e uma vez que tem Dev Spaces ativado no seu cluster, utilize o `azds list-uris` comando para exibir os URLs para a aplicação da amostra em *dev* que está atualmente selecionado.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Navegue para o serviço *de bikesharingweb* abrindo o URL público a partir do `azds list-uris` comando. No exemplo acima, o URL público para o serviço *de partilha de bicicletas* é `http://dev.bikesharingweb.fedcab0987.eus.azds.io/` . Selecione *Aurelia Briggs (cliente)* como utilizadora. Verifique se vê o texto *Hi Aurelia Briggs Assine* no topo.

![Aplicação de amostra de partilha de bicicletas Azure Dev Spaces](media/quickstart-team-development/bikeshare.png)

## <a name="create-child-dev-spaces"></a>Criar espaços dev para crianças

Utilize o `azds space select` comando para criar dois espaços para crianças sob *dev:*

```cmd
azds space select -n dev/azureuser1 -y
azds space select -n dev/azureuser2 -y
```

Os comandos acima criam dois espaços para crianças sob *o nome de dev* *azureuser1* e *azureuser2*. Estes dois espaços infantis representam espaços dev distintos para os desenvolvedores *azureuser1* e *azureuser2* para usar para fazer alterações na aplicação da amostra.

Utilize o `azds space list` comando para listar todos os espaços dev e *confirme que o dev/azureuser2* está selecionado.

```cmd
$ azds space list
   Name            DevSpacesEnabled
-  --------------  ----------------
   default         False
   dev             True
   dev/azureuser1  True
*  dev/azureuser2  True
```

Utilize o `azds list-uris` para exibir os URLs para a aplicação da amostra no espaço atualmente selecionado que é *dev/azureuser2*.

```cmd
$ azds list-uris
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
```

Confirme se os URLs apresentados pelo `azds list-uris` comando têm o prefixo *azureuser2.s.dev.* Este prefixo confirma que o espaço atual selecionado é *azureuser2,* que é filho *de dev*.

Navegue para o serviço *de partilha de bicicletas* para o espaço *dev/azureuser2* dev, abrindo o URL público a partir do `azds list-uris` comando. No exemplo acima, o URL público para o serviço *de partilha de bicicletas* é `http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/` . Selecione *Aurelia Briggs (cliente)* como utilizadora. Verifique se vê o texto *Hi Aurelia Briggs Assine* no topo.

## <a name="update-code"></a>Atualizar código

Abrir *BikeSharingWeb/componentes/Header.js* com um editor de texto e alterar o texto no [elemento de envergadura com o nome de `userSignOut` classeName](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/components/Header.js#L16).

```html
<span className="userSignOut">
    <Link href="/devsignin"><span tabIndex="0">Welcome {props.userName} | Sign out</span></Link>
</span>
```

Guarde as suas alterações e feche o ficheiro.

## <a name="build-and-run-the-updated-bikesharingweb-service-in-the-devazureuser2-dev-space"></a>Construa e execute o serviço de bikesharingweb atualizado no espaço *dev/azureuser2* dev

Navegue para o *BikeSharingWeb/diretório* e executar o `azds up` comando.

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

Este comando constrói e executa o serviço *de bikesharingweb* no espaço *dev/azureuser2.* Este serviço é executado para além do serviço *de bikesharingweb* que funciona em *dev* e é utilizado apenas para pedidos com o prefixo URL *azureuser2.s.* Para obter mais informações sobre como funciona o encaminhamento entre os espaços de dev dos pais e das [crianças, consulte como funciona a Azure Dev Spaces e está configurado.](how-dev-spaces-works.md)

Navegue para o serviço *de partilha de bicicletas* para o espaço *dev/azureuser2* dev, abrindo o URL público exibido na saída do `azds up` comando. Selecione *Aurelia Briggs (cliente)* como utilizadora. Verifique se vê o texto atualizado no canto superior direito. Poderá ter de refrescar a página ou limpar a cache do seu navegador se não vir imediatamente esta alteração.

![Aplicação de amostra de partilha de bicicletas Azure Dev Spaces](media/quickstart-team-development/bikeshare-update.png)

> [!NOTE]
> Quando navega para o seu serviço durante `azds up` a execução, os vestígios de pedido HTTP também são apresentados na saída do `azds up` comando. Estes vestígios podem ajudá-lo a resolver problemas e depurar o seu serviço. Pode desativar estes vestígios `--disable-http-traces` utilizando-os durante a execução `azds up` .

## <a name="verify-other-dev-spaces-are-unchanged"></a>Verifique se outros Espaços Dev estão inalterados

Se o `azds up` comando ainda estiver em funcionamento, prima *Ctrl+c*.

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

Navegue para a versão *dev* da *bikesharingweb* no seu navegador, escolha *Aurelia Briggs (cliente)* como utilizadora e verifique se vê o texto original no canto superior direito. Repita estes passos com o *URL dev/azureuser1.* Note que as alterações são aplicadas apenas na versão *dev/azureuser2* da *bikesharingweb*. Este isolamento das alterações ao *dev/azureuser2* permite que *o azureuser2* faça alterações sem afetar *o azureuser1*.

Para que estas alterações sejam refletidas em *dev* e *dev/azureuser1,* deve seguir o fluxo de trabalho ou o pipeline CI/CD existente da sua equipa. Por exemplo, este fluxo de trabalho pode envolver a alteração do seu sistema de controlo de versão e a implementação da atualização utilizando um pipeline CI/CD ou ferramentas como o Helm.

## <a name="clean-up-your-azure-resources"></a>Limpe os seus recursos Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o funcionamento da Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Como funciona o Azure Dev Spaces](how-dev-spaces-works.md)

[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
