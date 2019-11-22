---
title: Ligar o seu computador de desenvolvimento a um cluster do AKS
services: azure-dev-spaces
ms.date: 11/04/2019
ms.topic: conceptual
description: Saiba como conectar seu computador de desenvolvimento a um cluster AKS com Azure Dev Spaces
keywords: Azure Dev Spaces, espaços de desenvolvimento, Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres
ms.openlocfilehash: 1b65721b67ff63525adfe5d2061f22f359c02bde
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280124"
---
# <a name="connect-your-development-machine-to-an-aks-cluster-preview"></a>Ligar o seu computador de desenvolvimento a um cluster do AKS (pré-visualização)

Azure Dev Spaces permite que você execute e depure código com ou sem um contêiner em seu computador de desenvolvimento, enquanto ainda está conectado ao cluster kubernetes com o restante do seu aplicativo ou serviços. Conectar seu computador de desenvolvimento ao seu cluster ajuda a desenvolver rapidamente seu aplicativo e realizar testes de ponta a ponta sem precisar criar nenhuma configuração de Docker ou kubernetes. Você também pode se conectar ao cluster do AKS sem afetar outras cargas de trabalho ou usuários que podem estar usando o mesmo cluster.

Azure Dev Spaces redireciona o tráfego entre o cluster AKS conectado e o computador de desenvolvimento. Esse redirecionamento de tráfego permite que o código em seu computador de desenvolvimento e serviços em execução no cluster AKS se comuniquem como se estivessem no mesmo cluster AKS. Como seu código está em execução no computador de desenvolvimento, você também tem flexibilidade nas ferramentas de desenvolvimento que está usando para executar e depurar esse código. Azure Dev Spaces também fornece uma maneira de replicar as variáveis de ambiente e os arquivos montados disponíveis para pods no seu cluster AKS em seu computador de desenvolvimento.

Neste guia, vai aprender a:

* Configure Azure Dev Spaces em um cluster kubernetes gerenciado no Azure.
* Implante um aplicativo grande com vários microserviços em um espaço de desenvolvimento.
* Use Azure Dev Spaces para redirecionar o tráfego entre o cluster AKS e o código em execução no computador de desenvolvimento.

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são tornadas disponíveis para si na condição de concordar com os [termos suplementares de utilização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="before-you-begin"></a>Antes de começar

Este guia usa o [aplicativo de exemplo de compartilhamento de bicicletas Azure dev Spaces](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) para demonstrar como conectar seu computador de desenvolvimento a um cluster AKs. Siga as instruções no [Leiame do aplicativo de exemplo compartilhamento de bicicletas de Azure dev Spaces](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/README.md) para executar o aplicativo de exemplo. Como alternativa, se você tiver seu próprio aplicativo em um cluster AKS, ainda poderá seguir as etapas abaixo e usar os nomes dos seus próprios serviços e pods.

### <a name="limitations"></a>Limitações

* Não há suporte para UDP no momento.

### <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free).
* A [CLI do Azure instalada][azure-cli].
* [Visual Studio Code][vs-code] com a extensão [Azure dev Spaces][azds-vs-code] instalada e em execução no MacOS ou no Windows 10.
* O [aplicativo de exemplo de compartilhamento de bicicletas de Azure dev Spaces](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) ou seu próprio aplicativo em execução em um cluster AKs.

## <a name="connect-your-development-machine"></a>Conectar seu computador de desenvolvimento

Abra *dev-Spaces/Samples/BikeSharingApp/bicicletas* em Visual Studio Code e use a extensão Azure dev Spaces para conectar seu computador de desenvolvimento ao cluster AKs.

Para usar a extensão de Azure Dev Spaces, abra a paleta de comandos em Visual Studio Code clicando em *Exibir* na *paleta de comandos*. Comece a digitar `Azure Dev Spaces: Redirect` e clique em `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`, `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`ou `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`.

![Comandos](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>Selecione uma opção de redirecionamento

Se você executar `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`, será solicitado que você escolha um serviço kubernetes existente:

![Escolher serviço](../media/how-to-connect/connect-choose-service.png)

Esta opção redireciona todo o tráfego no cluster AKS para esse serviço para a versão do aplicativo em execução no computador de desenvolvimento. Se esse serviço tiver vários pods em execução no cluster AKS, todo o tráfego para esse serviço será roteado apenas para seu computador de desenvolvimento. Azure Dev Spaces também roteia todo o tráfego de saída do aplicativo de volta para o cluster AKS.

Se você executar `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`, será solicitado que você escolha um pod específico:

![Escolher Pod](../media/how-to-connect/connect-choose-pod.png)

Essa opção se conecta a um pod específico. Essa opção é útil para interagir com pods que não enviam ou recebem tráfego e replicam pods encerrados. Se o Pod enviar e receber tráfego, essa opção se comparecerá de uma maneira semelhante para `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` e redirecionará todo o tráfego no cluster AKS para todos os pods relacionados ao serviço do pod selecionado.

Se você executar `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`, não será solicitado que você selecione um pod ou serviço existente. Essa opção redireciona todo o tráfego de saída do aplicativo em execução no computador de desenvolvimento para o cluster AKS.

Para este exemplo, escolha `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` e selecione o serviço *bicicletas* .

### <a name="select-a-connection-mode"></a>Selecione um modo de conexão

Depois de selecionar a opção de redirecionamento, você será solicitado a escolher o modo de conexão de *substituição* ou *clonagem* .

![Substituir ou clonar](../media/how-to-connect/connect-replace-clone.png)

A opção *replace* substitui o Pod ou serviço atual no cluster AKs e redireciona todo o tráfego para esse serviço para seu computador de desenvolvimento. Essa opção pode causar interrupções em outros serviços em seu cluster AKS que interagem com o serviço que você está redirecionando pode não funcionar até que você inicie o aplicativo em seu computador de desenvolvimento. A opção de *clonagem* permite que você escolha um espaço de desenvolvimento filho existente ou crie um novo espaço de desenvolvimento filho para redirecionar o tráfego de um pod ou serviço para seu computador de desenvolvimento. Essa opção permite que você trabalhe em isolamento e não interrompa outros serviços, pois apenas o tráfego para esse espaço de desenvolvimento filho será redirecionado para seu computador de desenvolvimento. A opção de *clonagem* requer que o cluster AKS tenha Azure dev Spaces habilitado.

Para este exemplo, escolha *substituir*.

> [!NOTE]
> Se o Pod do seu serviço existente tiver vários contêineres, você também será solicitado a escolher o contêiner do aplicativo.

### <a name="select-a-port-for-your-application"></a>Selecione uma porta para seu aplicativo

Depois de selecionar o modo de conexão, você será solicitado a inserir a porta TCP em seu aplicativo local. Se seu aplicativo abrir várias portas, separe-as por uma vírgula, por exemplo *, 80, 81*. Se seu aplicativo não aceitar nenhuma solicitação de rede, digite *0*. Para este exemplo, digite *3000*.

![Conectar escolher porta](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>Confirme que você está conectado

Depois de selecionar a porta TCP do aplicativo, Azure Dev Spaces estabelecerá uma conexão com o cluster AKS. Azure Dev Spaces injeta um agente em seu cluster AKS para redirecionar o tráfego entre o cluster AKS e o computador de desenvolvimento. O estabelecimento dessa conexão pode levar alguns minutos. Azure Dev Spaces também solicitará acesso de administrador para modificar o arquivo de *hosts* em seu computador de desenvolvimento.

> [!IMPORTANT]
> Uma vez que Azure Dev Spaces estabelece uma conexão com o cluster AKS, os outros serviços em seu cluster AKS podem não funcionar corretamente até que você inicie o serviço em seu computador de desenvolvimento se você escolher o modo de *substituição* de conexão. Você pode escolher o modo de conexão de *clonagem* em vez de criar um espaço de desenvolvimento filho para seu redirecionamento e evitar qualquer interrupção no espaço pai. Além disso, se o serviço tiver uma dependência que não está disponível em seu computador de desenvolvimento, talvez seja necessário modificar seu aplicativo ou fornecer [configuração adicional](#additional-configuration)

Azure Dev Spaces abre uma janela de terminal intitulada *AZDS Connect-Bikes* depois de estabelecer uma conexão com o cluster AKs. Essa janela de terminal tem todas as variáveis de ambiente e entradas DNS configuradas do seu cluster AKS. Qualquer código executado nesta janela de terminal ou usando o depurador de Visual Studio Code está conectado ao cluster AKS.

![Componentes](../media/how-to-connect/connect-terminal.png)

Além disso, Azure Dev Spaces cria uma janela chamada *espaços de desenvolvimento se conecta* com toda a sua saída.

![Saída](../media/how-to-connect/connect-output.png)

Azure Dev Spaces também tem um item da barra de status mostrando o status da conexão.

![Estado](../media/how-to-connect/connect-status.png)

Verifique se a barra de status mostra *espaços de desenvolvimento: conectado a dev/Bikes na porta local 3000*.

### <a name="configure-your-application-on-your-development-machine"></a>Configurar seu aplicativo no computador de desenvolvimento

Abra a janela do terminal *AZDS Connect-Bikes* e execute `npm install`:

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```


Clique em *depurar* e *abra configurações*. Se for solicitado a selecionar um ambiente, escolha *node. js*. Isso cria um arquivo de `.vscode/launch.json`. Substitua o conteúdo desse arquivo pelo seguinte:

```json
{
    "configurations": [
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

Seu aplicativo será iniciado e Azure Dev Spaces redirecionará o tráfego entre o cluster AKS e o computador de desenvolvimento. Você verá mensagens semelhantes à mostrada abaixo no *console de depuração*:

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 3000
```

Navegue até o serviço *bikesharingweb* clicando na barra de status Azure dev Spaces e escolhendo a URL pública do seu aplicativo. Você também pode encontrar a URL pública do comando `azds list-uris` executado anteriormente. Se você não estiver usando Azure Dev Spaces em seu cluster, use o IP ou a URL para o aplicativo para o namespace que você está usando. No exemplo acima, a URL pública para o serviço *bikesharingweb* é `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Selecione *Aurelia Briggs (cliente)* como o usuário e, em seguida, selecione uma bicicleta para alugar.

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

Clique em *depurar* e em *parar depuração* para interromper o depurador. Clique na barra de status Azure Dev Spaces para desconectar-se do cluster AKS.

## <a name="additional-configuration"></a>Configuração adicional

Azure Dev Spaces pode manipular o tráfego de roteamento e replicar variáveis de ambiente sem nenhuma configuração adicional. Se precisar baixar todos os arquivos que são montados no contêiner em seu cluster AKS, como um arquivo ConfigMap, você pode criar um `azds-local.env` para baixar esses arquivos em seu computador de desenvolvimento.

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