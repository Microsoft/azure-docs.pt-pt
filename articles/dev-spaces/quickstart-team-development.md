---
title: Desenvolvimento em equipe no kubernetes usando o Azure Dev Spaces
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 04/25/2019
ms.topic: quickstart
description: Team kubernetes Development com contêineres e microservices no Azure
keywords: Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, K8S
manager: gwallace
ms.openlocfilehash: f4a0a4858c890c94e3983c3dc9b10d739dc1352d
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772572"
---
# <a name="quickstart-team-development-on-kubernetes-using-azure-dev-spaces"></a>Início rápido: Desenvolvimento em equipe no kubernetes usando o Azure Dev Spaces

Neste guia, vai aprender a:

- Configure Azure Dev Spaces em um cluster kubernetes gerenciado no Azure.
- Implante um aplicativo grande com vários microserviços em um espaço de desenvolvimento.
- Teste um único microserviço em um espaço de desenvolvimento isolado dentro do contexto do aplicativo completo.

![Desenvolvimento de equipe Azure Dev Spaces](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free).
- A [CLI do Azure instalada](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Helm 2,13 ou superior instalado](https://github.com/helm/helm/blob/master/docs/install.md).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Criar um cluster do serviço kubernetes do Azure

Você deve criar um cluster AKS em uma [região com suporte][supported-regions]. Os comandos abaixo criam um grupo de recursos chamado *MyResource* Group e um cluster AKs chamado *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

O cluster *MyAKS* também é criado com um nó, usando o tamanho de *Standard_DS2_v2* e com o RBAC desabilitado.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Habilitar Azure Dev Spaces em seu cluster AKS

Use o `use-dev-spaces` comando para habilitar espaços de desenvolvimento em seu cluster AKs e siga os prompts. O comando abaixo habilita espaços de desenvolvimento no cluster *MyAKS* no grupo *MyResource* Group e cria um espaço de desenvolvimento chamado *dev*.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Obter código do aplicativo de exemplo

Neste artigo, você usa o [aplicativo de exemplo de compartilhamento de bicicletas Azure dev Spaces](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) para demonstrar o uso de Azure dev Spaces.

Clone o aplicativo do GitHub e navegue até seu diretório:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>Recuperar o HostSuffix para *desenvolvimento*

Use o `azds show-context` comando para mostrar o HostSuffix para *desenvolvimento*.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>Atualize o gráfico Helm com seu HostSuffix

Abra [Charts/Values. YAML](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml) e substitua todas `<REPLACE_ME_WITH_HOST_SUFFIX>` as instâncias de pelo valor HostSuffix recuperado anteriormente. Salve as alterações e feche o arquivo.

## <a name="run-the-sample-application-in-kubernetes"></a>Executar o aplicativo de exemplo no kubernetes

Os comandos para executar o aplicativo de exemplo no kubernetes fazem parte de um processo existente e não têm dependência de Azure Dev Spaces ferramentas. Nesse caso, Helm é a ferramenta usada para executar esse aplicativo de exemplo, mas outras ferramentas podem ser usadas para executar todo o aplicativo em um namespace dentro de um cluster. Os comandos Helm são direcionados para o espaço de desenvolvimento chamado *dev* criado anteriormente, mas esse espaço de desenvolvimento também é um namespace kubernetes. Como resultado, os espaços de desenvolvimento podem ser direcionados por outras ferramentas da mesma forma que outros namespaces.

Você pode usar Azure Dev Spaces para desenvolvimento de equipe depois que um aplicativo é executado em um cluster, independentemente das ferramentas usadas para implantá-lo.

Use os `helm init` comandos `helm install` e para configurar e instalar o aplicativo de exemplo no cluster.

```cmd
cd charts/
helm init --wait
helm install -n bikesharing . --dep-up --namespace dev --atomic 
```
> [!Note]
> **Se você estiver usando um cluster habilitado para RBAC**, certifique-se de configurar [uma conta de serviço para o gaveta](https://helm.sh/docs/using_helm/#role-based-access-control). Caso contrário `helm` , ocorrerá falha nos comandos.

O `helm install` comando pode levar vários minutos para ser concluído. A saída do comando mostra o status de todos os serviços implantados no cluster quando concluído:

```cmd
$ cd charts/
$ helm init --wait
...
Happy Helming!

$ helm install -n bikesharing . --dep-up --namespace dev --atomic

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

Depois que o aplicativo de exemplo é instalado no cluster e, como você tem espaços de desenvolvimento habilitados no cluster `azds list-uris` , use o comando para exibir as URLs do aplicativo de exemplo no *dev* selecionado no momento.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Navegue até o serviço *bikesharingweb* abrindo a URL pública do `azds list-uris` comando. No exemplo acima, a URL pública para o serviço *bikesharingweb* é `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Selecione *Aurelia Briggs (cliente)* como o usuário. Verifique se você vê o texto *Hi Aurelia Briggs | Saia* na parte superior.

![Aplicativo de exemplo de compartilhamento de bicicletas Azure Dev Spaces](media/quickstart-team-development/bikeshare.png)

## <a name="create-child-dev-spaces"></a>Criar espaços de desenvolvimento filho

Use o `azds space select` comando para criar dois espaços filho em *desenvolvimento*:

```cmd
azds space select -n dev/azureuser1 -y
azds space select -n dev/azureuser2 -y
```

Os comandos acima criam dois espaços filho em *dev* chamado *azureuser1* e *azureuser2*. Esses dois espaços filho representam espaços de desenvolvimento distintos para que os desenvolvedores *azureuser1* e *azureuser2* usem para fazer alterações no aplicativo de exemplo.

Use o `azds space list` comando para listar todos os espaços de desenvolvimento e confirmar se o *dev/azureuser2* está selecionado.

```cmd
$ azds space list
Name            Selected
--------------  --------
default         False
dev             False
dev/azureuser1  False
dev/azureuser2  True
```

Use o `azds list-uris` para exibir as URLs para o aplicativo de exemplo no espaço atualmente selecionado que é *dev/azureuser2*.

```cmd
$ azds list-uris
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
```

Confirme se as URLs exibidas pelo `azds list-uris` comando têm o prefixo *azureuser2. s. dev* . Esse prefixo confirma que o espaço atual selecionado é *azureuser2*, que é um filho de *dev*.

Navegue até o serviço *bikesharingweb* para o espaço de desenvolvimento de *dev/azureuser2* abrindo a `azds list-uris` URL pública do comando. No exemplo acima, a URL pública para o serviço *bikesharingweb* é `http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/`. Selecione *Aurelia Briggs (cliente)* como o usuário. Verifique se você vê o texto *Hi Aurelia Briggs | Saia* na parte superior.

## <a name="update-code"></a>Atualizar código

Abra *BikeSharingWeb/Components/header. js* com um editor de texto e altere o texto no [elemento span com `userSignOut` o ClassName](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/components/Header.js#L16).

```html
<span className="userSignOut">
    <Link href="/devsignin"><span tabIndex="0">Welcome {props.userName} | Sign out</span></Link>
</span>
```

Salve as alterações e feche o arquivo.

## <a name="build-and-run-the-updated-bikesharingweb-service-in-the-devazureuser2-dev-space"></a>Compilar e executar o serviço bikesharingweb atualizado no espaço de desenvolvimento do *dev/azureuser2*

Navegue até o diretório *BikeSharingWeb/* e execute o `azds up` comando.

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

Esse comando cria e executa o serviço *bikesharingweb* no espaço de desenvolvimento */desenvolvimento de azureuser2* . Esse serviço é executado além do serviço *bikesharingweb* em execução no *dev* e é usado somente para solicitações com o prefixo de URL *azureuser2. s* . Para obter mais informações sobre como o roteamento funciona entre espaços de desenvolvimento pai e filho, consulte [como Azure dev Spaces funciona e está configurado](how-dev-spaces-works.md).

Navegue até o serviço *bikesharingweb* para o espaço de desenvolvimento de *dev/azureuser2* abrindo a URL pública exibida na `azds up` saída do comando. Selecione *Aurelia Briggs (cliente)* como o usuário. Verifique se você vê o texto atualizado no canto superior direito. Talvez seja necessário atualizar a página ou limpar o cache do navegador se você não vir essa alteração imediatamente.

![Aplicativo de exemplo de compartilhamento de bicicletas de Azure Dev Spaces atualizado](media/quickstart-team-development/bikeshare-update.png)

## <a name="verify-other-dev-spaces-are-unchanged"></a>Verificar se outros espaços de desenvolvimento estão inalterados

Se o `azds up` comando ainda estiver em execução, pressione *Ctrl + c*.

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

Navegue até a versão de *desenvolvimento* do *bikesharingweb* no navegador, escolha *Aurelia Briggs (cliente)* como o usuário e verifique se você vê o texto original no canto superior direito. Repita essas etapas com a URL *dev/azureuser1* . Observe que as alterações são aplicadas somente à versão *dev/azureuser2* do *bikesharingweb*. Esse isolamento de alterações em *dev/azureuser2* permite que o *azureuser2* faça alterações sem afetar o *azureuser1*.

Para que essas alterações sejam refletidas em *dev* e *dev/azureuser1*, você deve seguir o pipeline de fluxo de trabalho ou CI/CD existente da sua equipe. Por exemplo, esse fluxo de trabalho pode envolver a confirmação de sua alteração para o seu sistema de controle de versão e a implantação da atualização usando um pipeline de CI/CD ou ferramentas como Helm.

## <a name="clean-up-your-azure-resources"></a>Limpar os recursos do Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passos Seguintes

Saiba como os Espaços de Programador do Azure ajudam a desenvolver aplicações mais complexas em vários contentores e como pode simplificar o desenvolvimento de colaboração ao trabalhar com diferentes versões ou ramos do seu código em diferentes espaços.

> [!div class="nextstepaction"]
> [Working with multiple containers and team development](multi-service-nodejs.md) (Trabalhar com vários contentores e o desenvolvimento em equipa)


[supported-regions]: about.md#supported-regions-and-configurations
