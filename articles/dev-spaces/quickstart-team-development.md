---
title: Desenvolvimento em equipe no kubernetes
services: azure-dev-spaces
ms.date: 01/22/2020
ms.topic: quickstart
description: Este guia de início rápido mostra como fazer o Team kubernetes Development com contêineres e microservices com Azure Dev Spaces
keywords: Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, K8S
manager: gwallace
ms.openlocfilehash: d2b31cce6604cef31de6f034566ebd46a4e92750
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721596"
---
# <a name="quickstart-team-development-on-kubernetes---azure-dev-spaces"></a>Início rápido: desenvolvimento de equipe no kubernetes-Azure Dev Spaces

Neste guia, vai aprender a:

- Configure Azure Dev Spaces em um cluster kubernetes gerenciado no Azure.
- Implante um aplicativo grande com vários microserviços em um espaço de desenvolvimento.
- Teste um único microserviço em um espaço de desenvolvimento isolado dentro do contexto do aplicativo completo.

![Desenvolvimento de equipe Azure Dev Spaces](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free).
- A [CLI do Azure instalada](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Leme 3 instalado.][helm-installed]

## <a name="create-an-azure-kubernetes-service-cluster"></a>Criar um cluster do serviço kubernetes do Azure

É preciso criar um aglomerado AKS numa [região apoiada.][supported-regions] Os comandos abaixo criam um grupo de recursos chamado *MyResourceGroup* e um cluster AKS chamado *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Habilitar Azure Dev Spaces em seu cluster AKS

Utilize o comando `use-dev-spaces` para ativar os Espaços Dev no seu cluster AKS e seguir as instruções. O comando abaixo permite a Dev Spaces no cluster *MyAKS* no grupo *MyResourceGroup* e cria um espaço dev chamado *dev*.

> [!NOTE]
> O comando `use-dev-spaces` também instalará o Azure Dev Spaces CLI se ainda não estiver instalado. Não é possível instalar a CLI do Azure Dev Spaces no Azure Cloud Shell.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Obter código do aplicativo de exemplo

Neste artigo, utiliza a aplicação de partilha de [bicicletas Azure Dev Spaces](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) para demonstrar a utilização dos Espaços Azure Dev.

Clone o aplicativo do GitHub e navegue até seu diretório:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>Recupere o HostSuffix para *dev*

Utilize o comando `azds show-context` para mostrar o HostSuffix para *dev*.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>Atualize o gráfico Helm com seu HostSuffix

Abra [gráficos/valores.yaml](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml) e substitua todas as instâncias de `<REPLACE_ME_WITH_HOST_SUFFIX>` pelo valor hostSufix que recuperou anteriormente. Salve as alterações e feche o arquivo.

## <a name="run-the-sample-application-in-kubernetes"></a>Executar o aplicativo de exemplo no kubernetes

Os comandos para executar o aplicativo de exemplo no kubernetes fazem parte de um processo existente e não têm dependência de Azure Dev Spaces ferramentas. Nesse caso, Helm é a ferramenta usada para executar esse aplicativo de exemplo, mas outras ferramentas podem ser usadas para executar todo o aplicativo em um namespace dentro de um cluster. Os comandos Helm estão a visar o espaço dev chamado *dev* que criaste anteriormente, mas este espaço de v também é um espaço de nome Kubernetes. Como resultado, os espaços de desenvolvimento podem ser direcionados por outras ferramentas da mesma forma que outros namespaces.

Você pode usar Azure Dev Spaces para desenvolvimento de equipe depois que um aplicativo é executado em um cluster, independentemente das ferramentas usadas para implantá-lo.

Utilize o comando `helm install` para configurar e instalar a aplicação da amostra no seu cluster.

```cmd
cd charts/
helm install bikesharing . --dependency-update --namespace dev --atomic
```

O comando `helm install` pode levar vários minutos para ser concluído. Depois de a aplicação da amostra ser instalada no seu cluster e uma vez que tem Espaços Dev ativados no seu cluster, utilize o comando `azds list-uris` para exibir os URLs para a aplicação da amostra em *dev* que está atualmente selecionada.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Navegue para o serviço *bikesharingweb* abrindo o URL público a partir do comando `azds list-uris`. No exemplo acima, o URL público para o serviço *bikesharingweb* é `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Selecione *Aurelia Briggs (cliente)* como utilizador. Verifique se vê o texto *Hi Aurelia Briggs Assine* no topo.

![Aplicativo de exemplo de compartilhamento de bicicletas Azure Dev Spaces](media/quickstart-team-development/bikeshare.png)

## <a name="create-child-dev-spaces"></a>Criar espaços de desenvolvimento filho

Utilize o comando `azds space select` para criar dois espaços infantis sob *dev:*

```cmd
azds space select -n dev/azureuser1 -y
azds space select -n dev/azureuser2 -y
```

Os comandos acima criam dois espaços infantis sob *o* nome de *azureuser1* e *azureuser2*. Estes dois espaços infantis representam espaços de v distintos para os desenvolvedores *azureuser1* e *azureuser2* para fazer alterações na aplicação da amostra.

Utilize o comando `azds space list` para listar todos os espaços de v e confirmar se foi selecionado *dev/azureuser2.*

```cmd
$ azds space list
   Name            DevSpacesEnabled
-  --------------  ----------------
   default         False
   dev             True
   dev/azureuser1  True
*  dev/azureuser2  True
```

Utilize o `azds list-uris` para visualizar os URLs para a aplicação da amostra no espaço atualmente selecionado que é *dev/azureuser2*.

```cmd
$ azds list-uris
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
```

Confirme que os URLs exibidos pelo comando `azds list-uris` têm o prefixo *azureuser2.s.dev.* Este prefixo confirma que o espaço atual selecionado é *azureuser2*, que é uma criança de *v*.

Navegue para o serviço *bikesharingweb* para o espaço *dev/azureuser2 dev,* abrindo o URL público a partir do comando `azds list-uris`. No exemplo acima, o URL público para o serviço *bikesharingweb* é `http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/`. Selecione *Aurelia Briggs (cliente)* como utilizador. Verifique se vê o texto *Hi Aurelia Briggs Assine* no topo.

## <a name="update-code"></a>Atualizar código

Abra *bikeSharingWeb/componentes/Header.js* com um editor de texto e altere o texto no [elemento de extensão com o `userSignOut` nome de classe](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/components/Header.js#L16).

```html
<span className="userSignOut">
    <Link href="/devsignin"><span tabIndex="0">Welcome {props.userName} | Sign out</span></Link>
</span>
```

Salve as alterações e feche o arquivo.

## <a name="build-and-run-the-updated-bikesharingweb-service-in-the-devazureuser2-dev-space"></a>Construa e corra o serviço atualizado de bikesharingweb no espaço *dev/azureuser2* dev

Navegue para o *bikeSharingWeb/diretório* e execute o comando `azds up`.

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

Navegue para o serviço *bikesharingweb* para o espaço *dev/azureuser2 dev,* abrindo o URL público exibido na saída do comando `azds up`. Selecione *Aurelia Briggs (cliente)* como utilizador. Verifique se você vê o texto atualizado no canto superior direito. Talvez seja necessário atualizar a página ou limpar o cache do navegador se você não vir essa alteração imediatamente.

![Aplicativo de exemplo de compartilhamento de bicicletas de Azure Dev Spaces atualizado](media/quickstart-team-development/bikeshare-update.png)

> [!NOTE]
> Quando navega ao seu serviço durante a execução `azds up`, os vestígios de pedido http também são apresentados na saída do comando `azds up`. Esses rastreamentos podem ajudá-lo a solucionar problemas e depurar seu serviço. Pode desativar estes vestígios utilizando `--disable-http-traces` ao ser `azds up`.

## <a name="verify-other-dev-spaces-are-unchanged"></a>Verificar se outros espaços de desenvolvimento estão inalterados

Se o comando `azds up` ainda estiver em funcionamento, prima *Ctrl+c*.

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

Para que estas alterações se reflitam no *dev* e *dev/azureuser1,* deve seguir o fluxo de trabalho existente da sua equipa ou o gasoduto CI/CD existente. Por exemplo, esse fluxo de trabalho pode envolver a confirmação de sua alteração para o seu sistema de controle de versão e a implantação da atualização usando um pipeline de CI/CD ou ferramentas como Helm.

## <a name="clean-up-your-azure-resources"></a>Limpar os recursos do Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passos Seguintes

Saiba como os Espaços de Programador do Azure ajudam a desenvolver aplicações mais complexas em vários contentores e como pode simplificar o desenvolvimento de colaboração ao trabalhar com diferentes versões ou ramos do seu código em diferentes espaços.

> [!div class="nextstepaction"]
> [Working with multiple containers and team development](multi-service-nodejs.md) (Trabalhar com vários contentores e o desenvolvimento em equipa)

[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
