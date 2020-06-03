---
title: Use o processo local com Kubernetes com Código de Estúdio Visual (pré-visualização)
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Saiba como utilizar o Processo Local com kubernetes para ligar o seu computador de desenvolvimento a um cluster Kubernetes com espaços Azure Dev
keywords: Processo Local com Kubernetes, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores
ms.openlocfilehash: 23a94528ffa4e9e412f472349ea26d1a14003616
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84316741"
---
# <a name="use-local-process-with-kubernetes-with-visual-studio-code-preview"></a>Use o processo local com Kubernetes com Código de Estúdio Visual (pré-visualização)

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
* [Código de estúdio visual][vs-code] em execução no macOS ou Windows 10.
* A versão de extensão [Azure Dev Spaces][azds-vs-code] 2.0.220200601 ou maior instalada no Código do Estúdio Visual.
* [Azure Dev Spaces CLI instalado][azds-cli].

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

> [!IMPORTANT]
> Tem de ter acesso *ao Proprietário* ou *Colaborador* ao seu cluster para executar o script.

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

Abra *os espaços/amostras/BikeSharingApp/Bikes* da [aplicação de partilha de bicicletas Azure Dev Spaces Bike Sharing][bike-sharing-github] no Código do Estúdio Visual. Abra a extensão do Serviço Azure Kubernetes e selecione o espaço de nome *dev* no cluster *MyAKS.*

![Selecione Espaço de Nome](../media/local-process-kubernetes-vs-code/select-namespace.png)

Utilize o `npm install` comando para instalar as dependências da aplicação.

```console
npm install
```

Selecione o ícone *Debug* à esquerda e selecione *Processo Local com Kubernetes (Pré-visualização)* na parte superior.

![Escolha o Processo Local com Kubernetes](../media/local-process-kubernetes-vs-code/choose-local-process.png)

Clique no botão de partida ao lado *do Processo Local com Kubernetes (Pré-visualização)*. A primeira vez que executar esta configuração de lançamento é solicitada a configurar o serviço que pretende substituir, a porta para encaminhar a partir do seu computador de desenvolvimento e a tarefa de lançamento a utilizar.

Escolha o serviço *de bicicletas.*

![Escolha o Serviço](../media/local-process-kubernetes-vs-code/choose-service.png)

Todo o tráfego no cluster Kubernetes é redirecionado para o serviço de *bicicletas* para a versão da sua aplicação em execução no seu computador de desenvolvimento. O Processo Local com Kubernetes também encaminha todo o tráfego de saída da aplicação de volta para o seu cluster Kubernetes.

> [!IMPORTANT]
> Só é possível redirecionar serviços que tenham uma única cápsula.

Depois de selecionar o seu serviço, é-lhe pedido que entre na porta TCP para a sua aplicação local. Para este exemplo, insira *3000*.

![Conecte a porta de escolha](../media/local-process-kubernetes-vs-code/choose-port.png)

Escolha *o Lançamento via NPM* como tarefa de lançamento.

![Conecte a tarefa de lançamento de escolha](../media/local-process-kubernetes-vs-code/choose-launch.png)

> [!NOTE]
> Será solicitado para permitir que o *KubernetesDNSManager* corra elevado e modifique o ficheiro dos anfitriões.

O seu computador de desenvolvimento fica ligado quando a barra de estado fica laranja e a extensão Dev Spaces mostra que está ligado.

![Computador de desenvolvimento conectado](../media/local-process-kubernetes-vs-code/development-computer-connected.png)

> [!NOTE]
> Nos lançamentos subesquentes, não será solicitado o nome de serviço, porta ou tarefa de lançamento. Estes valores são guardados em *.vscode/tasks.json*.

Uma vez ligado o seu computador de desenvolvimento, o tráfego começa a ser redirecionado para o seu computador de desenvolvimento para o serviço que está a substituir.

## <a name="set-a-break-point"></a>Definir um ponto de rutura

Abra [o server.js][server-js-breakpoint] e clique em algum lugar na linha 233 para colocar o cursor lá. Desaponte um ponto de rutura atingindo *F9* ou clicando *em Run* e depois *Toggle Breakpoint*.

Navegue para a aplicação da amostra abrindo o URL público. Selecione *Aurelia Briggs (cliente)* como utilizador e, em seguida, selecione uma bicicleta para alugar. Note que a imagem da bicicleta não carrega. Destaque para o Código do Estúdio Visual e observa a linha 233. O ponto de rutura que definiu fez uma pausa no serviço na linha 233. Para retomar o serviço, clique em *F5* ou clique em *Executar* *e*continue . Volte ao seu navegador e verifique se vê uma imagem de espaço reservado para a bicicleta.

Retire o ponto de rutura colocando o cursor na linha 233 `server.js` e atingindo *F9*.

### <a name="update-your-application"></a>Atualize a sua aplicação

Editar `server.js` para remover as linhas 234 e 235:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

A secção deve agora parecer:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Guarde as alterações e clique em *Executar* e *reinicie a depuragem*. Depois de reconectar o seu navegador e verificar se já não vê uma imagem de espaço reservado para a bicicleta.

Clique *em Executar* e, em *seguida, pare de depurar* para parar o depurante.

> [!NOTE]
> Por predefinição, parar a tarefa de depuração também desliga o seu computador de desenvolvimento do seu cluster Kubernetes. Pode alterar este comportamento procurando por *Processo Local Com Kubernetes: Desconecte-se depois de depurar* as definições do Código do Estúdio Visual e remover a verificação ao lado *da Disconnect automaticamente quando a depuração termina.* Depois de atualizar esta definição, o seu computador de desenvolvimento permanecerá ligado quando parar e começar a depurar. Para desligar o computador de desenvolvimento do seu cluster clique na extensão Azure Dev Spaces na barra de estado e, em seguida, escolha *Desligar a sessão atual*.
>
> Se o Código do Estúdio Visual terminar abruptamente a ligação ao cluster ou termina, o serviço que está a redirecionar pode não ser restaurado para o seu estado original antes de se ligar ao Processo Local com kubernetes. Para corrigir este problema, consulte o [guia de resolução de problemas][troubleshooting].

## <a name="using-logging-and-diagnostics"></a>Utilização de registos e diagnósticos

A saída de registo é escrita na janela *Dev Spaces* depois do seu computador de desenvolvimento estar ligado ao seu cluster Kubernetes.

![Saída](../media/local-process-kubernetes-vs-code/output.png)

Clique na barra de estado Azure Dev Spaces e escolha *informações de diagnóstico de conexão de ligação*. Este comando imprime as variáveis ambientais atuais e os inteiros dns na saída de registo.

![Saída com diagnósticos](../media/local-process-kubernetes-vs-code/output-diagnostics.png)

Além disso, pode encontrar os registos de diagnóstico `Azure Dev Spaces` no diretório do seu [computador de desenvolvimento *TEMP.* ][azds-tmp-dir]

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
[server-js-breakpoint]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[troubleshooting]: ../troubleshooting.md#fail-to-restore-original-configuration-of-deployment-on-cluster
[vs-code]: https://code.visualstudio.com/download