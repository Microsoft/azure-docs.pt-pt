---
title: Use o Processo Local com Kubernetes com Visual Studio (pré-visualização)
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Saiba como usar o Processo Local com Kubernetes com o Visual Studio para ligar o seu computador de desenvolvimento a um cluster Kubernetes com a Azure Dev Spaces
keywords: Processo Local com Kubernetes, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores
ms.openlocfilehash: 8e89baeac8d9042a87685ed1268ed694db8d9455
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84316804"
---
# <a name="use-local-process-with-kubernetes-with-visual-studio-preview"></a>Use o Processo Local com Kubernetes com Visual Studio (pré-visualização)

O Processo Local com Kubernetes permite-lhe executar e depurar código no seu computador de desenvolvimento, ainda ligado ao seu cluster Kubernetes com o resto da sua aplicação ou serviços. Por exemplo, se tiver uma grande arquitetura de microserviços com muitos serviços e bases de dados interdependentes, replicar essas dependências no seu computador de desenvolvimento pode ser difícil. Além disso, a construção e implementação do código para o seu cluster Kubernetes para cada alteração de código durante o desenvolvimento do loop interno pode ser lenta, morosa e difícil de usar com um depurador.

O Processo Local com Kubernetes evita ter de construir e implementar o seu código no seu cluster, criando uma ligação diretamente entre o seu computador de desenvolvimento e o seu cluster. Ligar o seu computador de desenvolvimento ao seu cluster enquanto depura permite testar e desenvolver rapidamente o seu serviço no contexto da aplicação completa sem criar nenhuma configuração de Docker ou Kubernetes.

O Processo Local com Kubernetes redireciona o tráfego entre o seu cluster Kubernetes conectado e o seu computador de desenvolvimento. Esta reorientação de tráfego permite que o código no seu computador de desenvolvimento e serviços em execução no seu cluster Kubernetes se comunique como se estivessem no mesmo cluster Kubernetes. O Processo Local com Kubernetes também fornece uma forma de replicar variáveis ambientais e volumes montados disponíveis para cápsulas no seu cluster Kubernetes no seu computador de desenvolvimento. Fornecer acesso a variáveis ambientais e volumes montados no seu computador de desenvolvimento permite-lhe trabalhar rapidamente no seu código sem ter replicado essas dependências manualmente.

Neste guia, você aprenderá a usar o Processo Local com Kubernetes para redirecionar o tráfego entre o seu cluster Kubernetes e o código em execução no seu computador de desenvolvimento. Este guia também fornece um script para implementar uma grande aplicação de amostra com múltiplos microserviços num cluster Kubernetes.

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são disponibilizadas a si na condição de concordar com os [termos suplementares de utilização][preview-terms]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="before-you-begin"></a>Antes de começar

Este guia utiliza a [aplicação de amostra de partilha de bicicletas Azure Dev Spaces][bike-sharing-github] para demonstrar a ligação do seu computador de desenvolvimento a um cluster Kubernetes. Se já tem a sua própria aplicação a decorrer num cluster Kubernetes, ainda pode seguir os passos abaixo e usar os nomes dos seus próprios serviços.

### <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode criar uma [conta gratuita.](https://azure.microsoft.com/free)
* A [CLI do Azure instalada][azure-cli].
* [Visual Studio 2019][visual-studio] versão 16.7 Preview 2 ou maior a correr no Windows 10 com o *ASP.NET e desenvolvimento web* e cargas de trabalho de desenvolvimento *Azure* instaladas.
* [Azure Dev Spaces CLI instalado][azds-cli].

### <a name="enable-the-local-process-with-kubernetes-preview-feature-in-visual-studio"></a>Ativar o Processo Local com a pré-visualização de Kubernetes no Estúdio Visual

Para ativar o processo local com Kubernetes no Estúdio Visual, clique em *Tools*  >  *Opções de Opções de*  >  *Environment*  >  *Opções Ambiente Funcionalidades de Pré-visualização*. *Selecione Ative a depuragem local para serviços Kubernetes*.

Além disso, para aplicações de consola .NET, instale o Pacote NuGet *Microsoft.VisualStudio.Azure.Kubernetes.Tools.Targets* NuGet.

## <a name="create-a-kubernetes-cluster"></a>Criar um cluster do Kubernetes

Criar um cluster AKS numa [região apoiada.][supported-regions] Os comandos abaixo criam um grupo de recursos chamado *MyResourceGroup* e um cluster AKS chamado *MyAKS*.

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az aks create \
    --resource-group MyResourceGroup \
    --name MyAKS \
    --location eastus \
    --node-count 3 \
    --generate-ssh-keys
```

## <a name="install-the-sample-application"></a>Instalar a aplicação da amostra

Instale a aplicação da amostra no seu cluster utilizando o script fornecido. Pode executar este script no seu computador de desenvolvimento ou utilizando a [Azure Cloud Shell][azure-cloud-shell].

```azurecli-interactive
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/
chmod +x ./local-process-quickstart.sh
./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
```

Navegue para a aplicação da amostra que executa o seu cluster abrindo o seu URL público, que é apresentado na saída do script de instalação.

```console
$ ./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
Defaulting Dev spaces repository root to current directory : ~/dev-spaces
Setting the Kube context
...
To try out the app, open the url:
dev.bikesharingweb.EXTERNAL_IP.nip.io
```

Na amostra acima, a URL pública é `dev.bikesharingweb.EXTERNAL_IP.nip.io` .

## <a name="connect-to-your-cluster-and-debug-a-service"></a>Conecte-se ao seu cluster e depure um serviço

No seu computador de desenvolvimento, descarregue e configuure o CLI de Kubernetes para ligar ao seu cluster Kubernetes usando [credenciais de obter az aks][az-aks-get-credentials].

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

Open *dev-spaces/samples/BikeSharingApp/ReservationEngine/app.csproj* da [aplicação de partilha de bicicletas Azure Dev Spaces Bike Sharing][bike-sharing-github] no Estúdio Visual.

No seu projeto, selecione *Processo Local com Kubernetes* a partir das definições de lançamento dropdown como mostrado abaixo.

![Escolha o Processo Local com Kubernetes](../media/local-process-kubernetes-visual-studio/choose-local-process.png)

Clique no botão de partida ao lado *do Processo Local com Kubernetes*. No Processo Local com diálogo *Kubernetes:*

* Selecione a sua subscrição.
* Selecione *MyAKS* para o seu cluster.
* Selecione *dev* para o seu espaço de nome.
* Selecione *o motor de reservas* para o serviço para redirecionar.
* Selecione *aplicativo* para o perfil de lançamento.
* Selecione `http://dev.bikesharingweb.EXTERNAL_IP.nip.io` para o URL para lançar o seu navegador.

![Escolha o processo local com o cluster Kubernetes](../media/local-process-kubernetes-visual-studio/choose-local-process-cluster.png)

> [!IMPORTANT]
> Só é possível redirecionar serviços que tenham uma única cápsula.

Clique *em Guardar e comece a depurar.*

Todo o tráfego no cluster Kubernetes é redirecionado para o serviço *de criação* de reservas para a versão da sua aplicação em execução no seu computador de desenvolvimento. O Processo Local com Kubernetes também encaminha todo o tráfego de saída da aplicação de volta para o seu cluster Kubernetes.

> [!NOTE]
> Será solicitado para permitir que o *KubernetesDNSManager* corra elevado e modifique o ficheiro dos anfitriões.

O seu computador de desenvolvimento está ligado quando a barra de estado mostra que está ligado ao serviço *de preparação de reservas.*

![Computador de desenvolvimento conectado](../media/local-process-kubernetes-visual-studio/development-computer-connected.png)

> [!NOTE]
> Nos lançamentos subesquentes, não será solicitado com o diálogo Processo Local com o diálogo *Kubernetes.* Atualize estas definições no *painel Debug* nas propriedades do projeto.

Uma vez ligado o seu computador de desenvolvimento, o tráfego começa a ser redirecionado para o seu computador de desenvolvimento para o serviço que está a substituir.

## <a name="set-a-break-point"></a>Definir um ponto de rutura

Abra [BikesHelper.cs][bikeshelper-cs-breakpoint] e clique em algum lugar na linha 26 para colocar o seu cursor lá. Desaponte um ponto de rutura atingindo *F9* ou clicando em *Debug* e, em seguida, *Toggle Breakpoint*.

Navegue para a aplicação da amostra abrindo o URL público. Selecione *Aurelia Briggs (cliente)* como utilizador e, em seguida, selecione uma bicicleta para alugar. Clique *em Alugar Bicicleta.* O Regresso ao Visual Studio e a linha de observação 26 está em destaque. O ponto de rutura que definiu fez uma pausa no serviço na linha 26. Para retomar o serviço, clique em *F5* ou clique em *Debug* *e*continue . Volte ao seu navegador e verifique se a página mostra que alugou a bicicleta.

Retire o ponto de rutura colocando o cursor na linha 26 `BikesHelper.cs` e atingindo *F9*.

> [!NOTE]
> Por predefinição, parar a tarefa de depuração também desliga o seu computador de desenvolvimento do seu cluster Kubernetes. Pode alterar este comportamento alterando *a Disconnect depois de depurar* para *falso* na secção *Ferramentas de Depurar Kubernetes* das opções de depurar. Depois de atualizar esta definição, o seu computador de desenvolvimento permanecerá ligado quando parar e começar a depurar. Para desligar o computador de desenvolvimento do seu cluster, clique no botão *Desligar* na barra de ferramentas.
>
> Se o Visual Studio terminar abruptamente a ligação ao cluster ou termina, o serviço que está a redirecionar pode não ser restaurado para o seu estado original antes de se ligar ao Processo Local com kubernetes. Para corrigir este problema, consulte o [guia de resolução de problemas][troubleshooting].

## <a name="using-logging-and-diagnostics"></a>Utilização de registos e diagnósticos

Pode encontrar os registos de diagnóstico `Azure Dev Spaces` no diretório do seu [computador de desenvolvimento *TEMP.* ][azds-tmp-dir]

## <a name="remove-the-sample-application-from-your-cluster"></a>Remova a aplicação da amostra do seu cluster

Utilize o script fornecido para remover a aplicação da amostra do seu cluster.

```azurecli-interactive
./local-process-quickstart.sh -c -g MyResourceGroup -n MyAKS
```

## <a name="next-steps"></a>Passos seguintes

Saiba como usar a Azure Dev Spaces e GitHub Actions para testar alterações a partir de um pedido de puxar diretamente na AKS antes que o pedido de puxar seja fundido no ramo principal do seu repositório.

> [!div class="nextstepaction"]
> [GitHub Actions & Serviço Azure Kubernetes][gh-actions]

[azds-cli]: install-dev-spaces.md#install-the-client-side-tools
[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[azure-cloud-shell]: ../../cloud-shell/overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-vs-code]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-aks-tools
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[bikeshelper-cs-breakpoint]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/ReservationEngine/BikesHelper.cs#L26
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[troubleshooting]: ../troubleshooting.md#fail-to-restore-original-configuration-of-deployment-on-cluster
[visual-studio]: https://www.visualstudio.com/vs/