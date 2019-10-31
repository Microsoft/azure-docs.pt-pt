---
title: Azure Dev Spaces conectar
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 10/24/2019
ms.topic: conceptual
description: Saiba como usar o Azure Dev Spaces Connect
keywords: Azure Dev Spaces, espaços de desenvolvimento, Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, conectar
ms.openlocfilehash: 2e1984b838e961239e0533179c34eccb118abd8e
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73065876"
---
# <a name="azure-dev-spaces-connect-preview"></a>Azure Dev Spaces conectar (versão prévia)

O Azure Dev Spaces Connect permite que você execute e depure código com ou sem um contêiner em seu computador de desenvolvimento, enquanto ainda está conectado ao cluster kubernetes com o restante do seu aplicativo ou serviços. Conectar seu computador de desenvolvimento ao seu cluster ajuda a desenvolver rapidamente seu aplicativo e realizar testes de ponta a ponta sem precisar criar nenhuma configuração de Docker ou kubernetes. Você também pode se conectar ao cluster do AKS sem afetar outras cargas de trabalho ou usuários que podem estar usando o mesmo cluster.

O Azure Dev Spaces Connect redireciona o tráfego entre o cluster AKS conectado e o computador de desenvolvimento. Esse redirecionamento de tráfego permite que o código em seu computador de desenvolvimento e serviços em execução no cluster AKS se comuniquem como se estivessem no mesmo cluster AKS. Como seu código está em execução no computador de desenvolvimento, você também tem flexibilidade nas ferramentas de desenvolvimento que está usando para executar e depurar esse código. O Azure Dev Spaces Connect também fornece uma maneira de replicar variáveis de ambiente e arquivos montados disponíveis para pods em seu cluster AKS em seu computador de desenvolvimento.

Neste guia, vai aprender a:

- Configure Azure Dev Spaces em um cluster kubernetes gerenciado no Azure.
- Implante um aplicativo grande com vários microserviços em um espaço de desenvolvimento.
- Use Azure Dev Spaces conectar para redirecionar o tráfego entre o cluster AKS e o código em execução no computador de desenvolvimento.

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são tornadas disponíveis para si na condição de concordar com os [termos suplementares de utilização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="before-you-begin"></a>Antes de começar

Este guia usa o [aplicativo de exemplo de compartilhamento de bicicletas Azure dev Spaces](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) para demonstrar Azure dev Spaces se conectar. Se você tiver seu próprio aplicativo no cluster AKS que gostaria de usar, poderá começar [aqui](#use-azure-dev-spaces-connect).

### <a name="limitations"></a>Limitações

* Não há suporte para UDP com Azure Dev Spaces Connect no momento.

### <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free).
* A [CLI do Azure instalada][azure-cli].
* [Helm 2,13 ou superior instalado][helm-installed].
* [Visual Studio Code][vs-code] com a extensão [Azure dev Spaces][azds-vs-code] instalada e em execução no MacOS ou no Windows 10.

## <a name="create-an-azure-kubernetes-service-cluster"></a>Criar um cluster do serviço kubernetes do Azure

Você deve criar um cluster AKS em uma [região com suporte][supported-regions]. Os comandos abaixo criam um grupo de recursos chamado *MyResource* Group e um cluster AKs chamado *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Habilitar Azure Dev Spaces em seu cluster AKS

Use o comando `use-dev-spaces` para habilitar espaços de desenvolvimento em seu cluster AKS e siga os prompts. O comando abaixo habilita espaços de desenvolvimento no cluster *MyAKS* no grupo *MyResource* Group e cria um espaço de desenvolvimento chamado *dev*.

> [!NOTE]
> O comando `use-dev-spaces` também instalará a CLI do Azure Dev Spaces, se ainda não estiver instalada. Não é possível instalar a CLI do Azure Dev Spaces no Azure Cloud Shell.

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

Use o comando `azds show-context` para mostrar o HostSuffix para *desenvolvimento*.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>Atualize o gráfico Helm com seu HostSuffix

Abra [Charts/Values. YAML](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml) e substitua todas as instâncias de `<REPLACE_ME_WITH_HOST_SUFFIX>` pelo valor de HostSuffix recuperado anteriormente. Salve as alterações e feche o arquivo.

## <a name="run-the-sample-application-in-kubernetes"></a>Executar o aplicativo de exemplo no kubernetes

Os comandos para executar o aplicativo de exemplo no kubernetes fazem parte de um processo existente e não têm dependência de Azure Dev Spaces ferramentas. Nesse caso, Helm é a ferramenta usada para executar esse aplicativo de exemplo, mas outras ferramentas podem ser usadas para executar todo o aplicativo em um namespace dentro de um cluster. Os comandos Helm são direcionados para o espaço de desenvolvimento chamado *dev* criado anteriormente, mas esse espaço de desenvolvimento também é um namespace kubernetes. Como resultado, os espaços de desenvolvimento podem ser direcionados por outras ferramentas da mesma forma que outros namespaces.

Você pode usar Azure Dev Spaces para desenvolvimento depois que um aplicativo é executado em um cluster, independentemente das ferramentas usadas para implantá-lo.

### <a name="use-helm-to-install-the-sample-application"></a>Usar o Helm para instalar o aplicativo de exemplo

Use os comandos `helm init` e `helm install` para configurar e instalar o aplicativo de exemplo no cluster.

```cmd
cd charts/
helm init --wait
helm install -n bikesharing . --dep-up --namespace dev --atomic 
```

> [!Note]
> **Se você estiver usando um cluster habilitado para RBAC**, certifique-se de configurar [uma conta de serviço para o gaveta](https://helm.sh/docs/using_helm/#role-based-access-control). Caso contrário, `helm` comandos falharão.

O comando `helm install` pode levar vários minutos para ser concluído. A saída do comando mostra o status de todos os serviços implantados no cluster quando concluído:

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

### <a name="navigate-to-your-sample-application"></a>Navegue até o aplicativo de exemplo

Depois que o aplicativo de exemplo é instalado no cluster e, como você tem espaços de desenvolvimento habilitados no cluster, use o comando `azds list-uris` para exibir as URLs do aplicativo de exemplo no *dev* selecionado no momento.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Navegue até o serviço *bikesharingweb* abrindo a URL pública do comando `azds list-uris`. No exemplo acima, a URL pública para o serviço *bikesharingweb* é `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Selecione *Aurelia Briggs (cliente)* como o usuário e, em seguida, selecione uma bicicleta para alugar. Verifique se você vê uma imagem de espaço reservado para a bicicleta.

## <a name="use-azure-dev-spaces-connect"></a>Usar Azure Dev Spaces conectar

Abra *dev-Spaces/Samples/BikeSharingApp/bicicletas* em Visual Studio Code e use a extensão Azure dev Spaces Connect para conectar seu computador de desenvolvimento ao seu cluster AKs.

Para usar a extensão do Azure Dev Spaces Connect, abra a paleta de comandos em Visual Studio Code clicando em *Exibir* e na *paleta de comandos*. Comece a digitar `Azure Dev Spaces: Redirect` e clique em `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`, `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`ou `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`.

![Conectar comandos](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>Selecione uma opção de redirecionamento

Se você executar `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`, será solicitado que você escolha um serviço kubernetes existente:

![Conectar escolher serviço](../media/how-to-connect/connect-choose-service.png)

Esta opção redireciona todo o tráfego no cluster AKS para esse serviço para a versão do aplicativo em execução no computador de desenvolvimento. Se esse serviço tiver vários pods em execução no cluster AKS, todo o tráfego para esse serviço será roteado apenas para seu computador de desenvolvimento. O Azure Dev Spaces Connect também roteia todo o tráfego de saída do aplicativo de volta para o cluster AKS.

Se você executar `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`, será solicitado que você escolha um pod específico:

![Conectar escolher Pod](../media/how-to-connect/connect-choose-pod.png)

Essa opção se conecta a um pod específico. Essa opção é útil para interagir com pods que não enviam ou recebem tráfego e replicam pods encerrados. Se o Pod enviar e receber tráfego, essa opção se comparecerá de uma maneira semelhante para `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` e redirecionará todo o tráfego no cluster AKS para todos os pods relacionados ao serviço do pod selecionado.

Se você executar `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`, não será solicitado que você selecione um pod ou serviço existente. Essa opção redireciona todo o tráfego de saída do aplicativo em execução no computador de desenvolvimento para o cluster AKS.

Para este exemplo, escolha `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` e selecione o serviço *bicicletas* .

### <a name="select-a-connection-mode"></a>Selecione um modo de conexão

Depois de selecionar a opção de redirecionamento, você será solicitado a escolher o modo de conexão de *substituição* ou *clonagem* .

![Conectar substituir ou clonar](../media/how-to-connect/connect-replace-clone.png)

A opção *replace* substitui o Pod ou serviço atual no cluster AKs e redireciona todo o tráfego para esse serviço para seu computador de desenvolvimento. Essa opção pode causar interrupções em outros serviços em seu cluster AKS que interagem com o serviço que você está redirecionando pode não funcionar até que você inicie o aplicativo em seu computador de desenvolvimento. A opção de *clonagem* permite que você escolha um espaço de desenvolvimento filho existente ou crie um novo espaço de desenvolvimento filho para redirecionar o tráfego de um pod ou serviço para seu computador de desenvolvimento. Essa opção permite que você trabalhe em isolamento e não interrompa outros serviços, pois apenas o tráfego para esse espaço de desenvolvimento filho será redirecionado para seu computador de desenvolvimento. A opção de *clonagem* requer que o cluster AKS tenha Azure dev Spaces habilitado.

Para este exemplo, escolha *substituir*.

> [!NOTE]
> Se o Pod do seu serviço existente tiver vários contêineres, você também será solicitado a escolher o contêiner do aplicativo.

### <a name="select-a-port-for-your-application"></a>Selecione uma porta para seu aplicativo

Depois de selecionar o modo de conexão, você será solicitado a inserir a porta TCP em seu aplicativo local. Se seu aplicativo abrir várias portas, separe-as por uma vírgula, por exemplo *, 80, 81*. Se seu aplicativo não aceitar nenhuma solicitação de rede, digite *0*. Para este exemplo, digite *8080*.

![Conectar escolher porta](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>Confirme que você está conectado

Depois de selecionar a porta TCP do aplicativo, o Azure Dev Spaces Connect estabelecerá uma conexão com o cluster AKS. Azure Dev Spaces conexão injeta um agente em seu cluster AKS para redirecionar o tráfego entre o cluster AKS e o computador de desenvolvimento. O estabelecimento dessa conexão pode levar alguns minutos. O Azure Dev Spaces Connect também solicitará acesso de administrador para modificar o arquivo de *hosts* em seu computador de desenvolvimento.

> [!IMPORTANT]
> Depois que o Azure Dev Spaces Connect estabelecer uma conexão com o cluster do AKS, os outros serviços no cluster do AKS poderão não funcionar corretamente até que você inicie o serviço em seu computador de desenvolvimento. Além disso, se o serviço tiver uma dependência que não está disponível em seu computador de desenvolvimento, talvez seja necessário modificar seu aplicativo ou fornecer [configuração adicional](#additional-configuration)

Azure Dev Spaces Connect abre uma janela de terminal intitulada *AZDS Connect-Bikes* depois de estabelecer uma conexão com o cluster AKs. Essa janela de terminal tem todas as variáveis de ambiente e entradas DNS configuradas do seu cluster AKS. Qualquer código executado nesta janela de terminal ou usando o depurador de Visual Studio Code está conectado ao cluster AKS.

![Conectar terminal](../media/how-to-connect/connect-terminal.png)

Além disso, Azure Dev Spaces Connect cria uma janela chamada *espaços de desenvolvimento se conecta* com toda a sua saída.

![Saída de conexão](../media/how-to-connect/connect-output.png)

O Azure Dev Spaces Connect também tem um item da barra de status mostrando o status da conexão.

![Status de conexão](../media/how-to-connect/connect-status.png)

Verifique se a barra de status mostra *espaços de desenvolvimento: conectado a dev/Bikes na porta local 8080*.

### <a name="configure-your-application-on-your-development-machine"></a>Configurar seu aplicativo no computador de desenvolvimento

Abra a janela do terminal *AZDS Connect-Bikes* e execute `npm install`:

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```

Abra [Server. js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L352) e na linha 352 atualizar *porta* para *8080*:

```javascript
var port = 8080;
var server = null;
```

Clique em *depurar* e *abra configurações*. Isso cria um arquivo de `.vscode/launch.json`. Substitua o conteúdo desse arquivo pelo seguinte:

```json
{
    "configurations": [
        ...
        {
            "type": "node",
            "request": "launch",
            "name": "Launch via NPM",
            "runtimeExecutable": "npm",
            "runtimeArgs": [
                "run-script",
                "debug"
            ],
            "port": 9229
        }
    ]
}
```

Abra [Package. JSON](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/package.json) e adicione um script de depuração:

```json
  "devDependencies": {
    "nodemon": "^1.18.10"
  },
  "scripts": {
    "debug": "node --nolazy --inspect-brk=9229 server.js"
  }
```

### <a name="start-your-application-on-your-development-machine"></a>Inicie seu aplicativo no computador de desenvolvimento

Clique no ícone de *depuração* à esquerda e clique no botão iniciar ao lado de *iniciar via NPM* na parte superior.

![Iniciar via NPM](../media/how-to-connect/launch-npm.png)

Seu aplicativo será iniciado e Azure Dev Spaces conexão redirecionará o tráfego entre o cluster AKS e o computador de desenvolvimento. Você verá mensagens semelhantes à mostrada abaixo no *console de depuração*:

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 8080
```

Navegue até o serviço *bikesharingweb* clicando na barra de status do Azure dev Spaces Connect e escolhendo a URL pública do seu aplicativo. Você também pode encontrar a URL pública do comando `azds list-uris` executado anteriormente. Se você não estiver usando Azure Dev Spaces em seu cluster, use o IP ou a URL para o aplicativo para o namespace que você está usando. No exemplo acima, a URL pública para o serviço *bikesharingweb* é `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Selecione *Aurelia Briggs (cliente)* como o usuário e, em seguida, selecione uma bicicleta para alugar.

### <a name="set-a-break-point"></a>Definir um ponto de interrupção

Abra [Server. js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233) e clique em algum lugar na linha 233 para colocar o cursor lá. Defina um ponto de interrupção pressionando *F9* ou clicando em *depurar* e, em seguida, *alternar ponto de interrupção*.

Navegue até o serviço *bikesharingweb* abrindo a URL pública. Selecione *Aurelia Briggs (cliente)* como o usuário e, em seguida, selecione uma bicicleta para alugar. Observe que a imagem da bicicleta não é carregada. Retorne para Visual Studio Code e observe que a linha 233 está realçada. O ponto de interrupção que você definiu pausou o serviço na linha 233. Para retomar o serviço, pressione *F5* ou clique em *depurar* e *continuar*. Retorne ao seu navegador e verifique se você vê uma imagem de espaço reservado para a bicicleta.

Remova o ponto de interrupção colocando o cursor na linha 233 em `server.js` e pressionando *F9*.

### <a name="update-your-application"></a>Atualizar seu aplicativo

Edite `server.js` para remover as linhas 232 e 233:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

A seção agora deve ser semelhante a:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Salve as alterações e clique em *depurar* e *reinicie a depuração*. Atualize o navegador e verifique se você não vê mais uma imagem de espaço reservado para a bicicleta.

Clique em *depurar* e em *parar depuração* para interromper o depurador. Clique na barra de status do Azure Dev Spaces Connect para se desconectar do cluster AKS.

## <a name="additional-configuration"></a>Configuração adicional

O Azure Dev Spaces Connect pode manipular o tráfego de roteamento e replicar variáveis de ambiente sem nenhuma configuração adicional. Se precisar baixar todos os arquivos que são montados no contêiner em seu cluster AKS, como um arquivo ConfigMap, você pode criar um `azds-local.env` para baixar esses arquivos em seu computador de desenvolvimento.

Aqui está um exemplo `azds-local.env`:

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development machine,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development machine.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}


# This makes the myapp1 service available to your development machine
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

MYAPP1_SERVICE_HOST=${services.myapp1}

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.
```

## <a name="next-steps"></a>Passos seguintes

Saiba como usar as ações Azure Dev Spaces e GitHub para testar alterações de uma solicitação pull diretamente no AKS antes que a solicitação pull seja mesclada no Branch principal do repositório.

> [!div class="nextstepaction"]
> [Ações do GitHub & serviço kubernetes do Azure][gh-actions]

[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[supported-regions]: ../about.md#supported-regions-and-configurations
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download