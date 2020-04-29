---
title: Ligar o computador de desenvolvimento a um cluster do AKS (pré-visualização)
services: azure-dev-spaces
ms.date: 11/04/2019
ms.topic: conceptual
description: Saiba como ligar o seu computador de desenvolvimento a um cluster AKS com o Azure Dev Spaces
keywords: Espaços Azure Dev, Espaços Dev, Docker, Kubernetes, Azure, AKS, Serviço Azure Kubernetes, contentores
ms.openlocfilehash: 772f221a8047a71902f36fa98ded6c24b5e02d27
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80235008"
---
# <a name="connect-your-development-computer-to-an-aks-cluster-preview"></a>Ligar o computador de desenvolvimento a um cluster do AKS (pré-visualização)

A Azure Dev Spaces permite-lhe executar e depurar código com ou sem recipiente no seu computador de desenvolvimento, enquanto ainda está ligado ao seu cluster Kubernetes com o resto da sua aplicação ou serviços. Ligar o computador de desenvolvimento ao seu cluster ajuda-o a desenvolver rapidamente a sua aplicação e a realizar testes de ponta a ponta sem ter de criar qualquer configuração de Docker ou Kubernetes. Também pode ligar-se ao seu cluster AKS sem afetar outras cargas de trabalho ou utilizadores que possam estar a utilizar o mesmo cluster.

A Azure Dev Spaces redireciona o tráfego entre o seu cluster AKS conectado e o seu computador de desenvolvimento. Esta redirecionamento de tráfego permite que o código no seu computador de desenvolvimento e serviços em execução no seu cluster AKS se comuniquem como se estivessem no mesmo cluster AKS. Uma vez que o seu código está a ser executado no seu computador de desenvolvimento, também tem flexibilidade nas ferramentas de desenvolvimento que está a usar para executar e depurar esse código. A Azure Dev Spaces também fornece uma forma de replicar variáveis ambientais e ficheiros montados disponíveis para pods no seu cluster AKS no seu computador de desenvolvimento.

Neste guia, vai aprender a:

* Instale o Azure Dev Spaces num aglomerado de Kubernetes gerido em Azure.
* Implemente uma grande aplicação com vários microserviços para um espaço de v.
* Utilize os Espaços Azure Dev para redirecionar o tráfego entre o seu cluster AKS e o código em execução no seu computador de desenvolvimento.

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são disponibilizadas a si na condição de concordar com os [termos suplementares de utilização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="before-you-begin"></a>Antes de começar

Este guia utiliza a aplicação de partilha de [bicicletas Azure Dev Spaces](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) para demonstrar a ligação do seu computador de desenvolvimento a um cluster AKS. Siga as instruções na aplicação de partilha de [bicicletas Azure Dev Spaces README](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/README.md) para executar a aplicação da amostra. Em alternativa, se tiver a sua própria aplicação num cluster AKS, ainda pode seguir os passos abaixo e utilizar os nomes dos seus próprios serviços e casulos.

### <a name="limitations"></a>Limitações

* A UDP não é apoiada neste momento.

### <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição Azure, pode criar uma [conta gratuita.](https://azure.microsoft.com/free)
* A [CLI do Azure instalada][azure-cli].
* [Código de estúdio visual][vs-code] com a extensão [Azure Dev Spaces][azds-vs-code] instalada e em execução no MacOS ou windows 10.
* A aplicação de [partilha de bicicletas Azure Dev Spaces](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) ou a sua própria aplicação em execução num cluster AKS.

## <a name="connect-your-development-computer"></a>Ligue o seu computador de desenvolvimento

Abra *os espaços/amostras/BikeSharingApp/Bikes* no Código do Estúdio Visual e utilize a extensão Azure Dev Spaces para ligar o seu computador de desenvolvimento ao seu cluster AKS.

Para utilizar a extensão Dos Espaços Azure Dev, abra a Paleta de Comando no Código do Estúdio Visual clicando em *Ver* e ntão *Paleta de Comando*. Comece `Azure Dev Spaces: Redirect` a escrever `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`e `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`clique `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`em qualquer um, ou .

![Comandos](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>Selecione uma opção de reorientação

Se correr, `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`é-lhe pedido que escolha um serviço Kubernetes existente:

![Escolha o Serviço](../media/how-to-connect/connect-choose-service.png)

Esta opção redireciona todo o tráfego do cluster AKS para este serviço para a versão da sua aplicação em execução no seu computador de desenvolvimento. Se este serviço tiver várias cápsulas em execução no cluster AKS, todo o tráfego para este serviço é apenas encaminhado para o seu computador de desenvolvimento. A Azure Dev Spaces também encaminha todo o tráfego de saída desde a aplicação até ao seu cluster AKS.

Se correr, `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`é-lhe pedido que escolha uma cápsula específica:

![Escolha Pod](../media/how-to-connect/connect-choose-pod.png)

Esta opção liga-se a uma cápsula específica. Esta opção é útil para interagir com cápsulas que não enviam ou recebem tráfego e replicam cápsulas terminadas. Se a cápsula enviar e receber tráfego, esta opção `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` comporta-se de forma semelhante e redirecionará todo o tráfego do cluster AKS para todas as cápsulas relacionadas com o serviço da cápsula selecionada.

Se correr, `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`não é solicitado a selecionar um casulo ou serviço existente. Esta opção redireciona todo o tráfego de saída da aplicação que está a funcionar no seu computador de desenvolvimento para o cluster AKS.

Para este exemplo, escolha `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` e selecione o serviço de *bicicletas.*

### <a name="select-a-connection-mode"></a>Selecione um modo de ligação

Depois de selecionar a sua opção de reorientação, é-lhe pedido que escolha o modo de ligação *Replace* ou *Clone.*

![Substituir ou Clonar](../media/how-to-connect/connect-replace-clone.png)

A opção *Substituir* substitui o pod ou serviço atual no cluster AKS e redireciona todo o tráfego desse serviço para o seu computador de desenvolvimento. Esta opção pode ser disruptiva para outros serviços do seu cluster AKS que interagem com o serviço que está a redirecionar pode não funcionar até iniciar a aplicação no seu computador de desenvolvimento. A opção *Clone* permite-lhe escolher um espaço de dev infantil existente ou criar um novo espaço de dev para crianças para redirecionar o tráfego para um casulo ou serviço para o seu computador de desenvolvimento. Esta opção permite-lhe trabalhar isoladamente e não perturbar outros serviços, uma vez que apenas o tráfego para aquele espaço de dev infantil será redirecionado para o seu computador de desenvolvimento. A opção *Clone* requer que o seu cluster AKS tenha espaços Azure Dev ativados.

Para este exemplo, escolha *Substituir*.

> [!NOTE]
> Se o casulo do seu serviço existente tiver vários contentores, também é solicitado a escolher o recipiente da aplicação.

### <a name="select-a-port-for-your-application"></a>Selecione uma porta para a sua aplicação

Depois de selecionar o seu modo de ligação, é-lhe pedido que entre na porta TCP a sua aplicação local. Se a sua aplicação abrir várias portas, separe-as por uma vírmula, por exemplo, *80,81*. Se o seu pedido não aceitar quaisquer pedidos de rede, insira *0*. Para este exemplo, insira *3000*.

![Ligar escolha porta](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>Confirme que está ligado

Depois de selecionar a porta TCP da sua aplicação, a Azure Dev Spaces estabelecerá uma ligação ao cluster AKS. A Azure Dev Spaces injeta um agente no seu cluster AKS para redirecionar o tráfego entre o cluster AKS e o seu computador de desenvolvimento. Estabelecer esta ligação pode demorar alguns minutos. A Azure Dev Spaces também solicitará o acesso ao administrador de forma a modificar o ficheiro dos *anfitriões* no seu computador de desenvolvimento.

> [!IMPORTANT]
> Assim que a Azure Dev Spaces estabelecer uma ligação ao seu cluster AKS, os outros serviços do seu cluster AKS podem não funcionar corretamente até iniciar o serviço no seu computador de desenvolvimento se escolher o modo de ligação *Substituir.* Pode escolher o modo de ligação *Clone* em vez de criar um espaço de dev para crianças para a sua reorientação e evitar qualquer perturbação no espaço dos pais. Além disso, se o seu serviço tiver uma dependência que não esteja disponível no seu computador de desenvolvimento, poderá ter de modificar a sua aplicação ou fornecer [configuração adicional](#additional-configuration)

A Azure Dev Spaces abre uma janela terminal intitulada *AZDS Connect - Bikes* depois de estabelecer uma ligação ao seu cluster AKS. Esta janela terminal tem todas as variáveis ambientais e entradas de DNS configuradas a partir do seu cluster AKS. Qualquer código que execute nesta janela de terminal ou usando o debugger visual studio code está ligado ao cluster AKS.

![Terminal](../media/how-to-connect/connect-terminal.png)

Além disso, a Azure Dev Spaces cria uma janela intitulada *Dev Spaces Connect* com toda a sua saída.

![Saída](../media/how-to-connect/connect-output.png)

A Azure Dev Spaces também tem um item de barra de estado que mostra o estado de ligação.

![Estado](../media/how-to-connect/connect-status.png)

Verifique se a barra de estado mostra *Espaços Dev: Ligados a dev/bicicletas na porta local 3000*.

### <a name="configure-your-application-on-your-development-computer"></a>Configure a sua aplicação no seu computador de desenvolvimento

Abra a Janela de terminais *AZDS Connect - Bicicletas* e executar: `npm install`

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```

Clique em *Debug* e *abra configurações*. Se solicitado a selecionar um ambiente, escolha *Node.js*. Isto cria `.vscode/launch.json` um ficheiro. Substitua o conteúdo desse ficheiro pelo seguinte:

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

Abra [o pacote.json](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/package.json) e adicione um script de depuração:

```json
  "devDependencies": {
    "nodemon": "^1.18.10"
  },
  "scripts": {
    "debug": "node --nolazy --inspect-brk=9229 server.js"
  }
```

### <a name="start-your-application-on-your-development-computer"></a>Inicie a sua aplicação no seu computador de desenvolvimento

Clique no ícone *Debug* à esquerda e clique no botão de arranque ao lado do *Lançamento via NPM* na parte superior.

![Lançamento via NPM](../media/how-to-connect/launch-npm.png)

A sua aplicação iniciará e a Azure Dev Spaces redireciona o tráfego entre o seu cluster AKS e o seu computador de desenvolvimento. Verá mensagens semelhantes às seguintes na *Consola Debug:*

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 3000
```

Navegue para o serviço *bikesharingweb* clicando na barra de status Dov Spaces do Azure E escolhendo o URL público da sua aplicação. Também pode encontrar a URL `azds list-uris` pública do comando que dirigiu anteriormente. Se não estiver a utilizar espaços Azure Dev no seu cluster, utilize o IP ou o URL para a aplicação para o espaço de nome que está a utilizar. No exemplo acima, o URL público para `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`o serviço *bikesharingweb* é . Selecione *Aurelia Briggs (cliente)* como utilizador e, em seguida, selecione uma bicicleta para alugar.

### <a name="set-a-break-point"></a>Definir um ponto de rutura

Abra [o servidor.js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233) e clique em algum lugar na linha 233 para colocar o cursor lá. Desloque um ponto de rutura batendo em *F9* ou clicando em *Debug* e *toggle Breakpoint*.

Navegue para o serviço *bikesharingweb* abrindo o URL público. Selecione *Aurelia Briggs (cliente)* como utilizador e, em seguida, selecione uma bicicleta para alugar. Note que a imagem da bicicleta não carrega. O Regresso ao Código do Estúdio Visual e a linha 233 de observação estão em destaque. O ponto de rutura que definiu fez uma pausa no serviço na linha 233. Para retomar o serviço, clique em *F5* ou clique em *Debug* *e*continue . Volte ao seu navegador e verifique se vê uma imagem de espaço reservado para a bicicleta.

Retire o ponto de rutura colocando o cursor na linha 233 `server.js` e batendo em *F9*.

### <a name="update-your-application"></a>Atualize a sua aplicação

Editar `server.js` para remover as linhas 232 e 233:

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

Guarde as suas alterações e clique em *Debug* e *reinicie a depuração*. Refresque o seu navegador e verifique se já não vê uma imagem de espaço reservado para a bicicleta.

Clique em *Debug* e *pare de depuração* para parar o debugger. Clique na barra de estado do Azure Dev Spaces para desligar do cluster AKS.

## <a name="additional-configuration"></a>Configuração adicional

A Azure Dev Spaces pode lidar com o tráfego de encaminhamento e replicando variáveis ambientais sem qualquer configuração adicional. Se precisar de descarregar quaisquer ficheiros que estejam montados no contentor do seu cluster AKS, como um ficheiro ConfigMap, pode criar um `azds-local.env` para descarregar esses ficheiros para o seu computador de desenvolvimento.

Aqui está `azds-local.env`um exemplo:

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}


# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

## <a name="using-logging-and-diagnostics"></a>Utilização de exploração madeireira e diagnóstico

A saída de registo está escrita na janela *Dev Spaces Connect* depois de ligar o computador de desenvolvimento ao seu cluster AKS.

![Saída](../media/how-to-connect/connect-output.png)

Clique na barra de estado do Azure Dev Spaces e escolha *mostrar informações*de diagnóstico . Este comando imprime as variáveis ambientais atuais e os dNS inteiros na saída de exploração madeireira.

![Saída com diagnósticos](../media/how-to-connect/connect-output-diagnostics.png)

Além disso, pode encontrar os `Azure Dev Spaces` registos de diagnóstico no diretório no [diretório *TEMP* do][azds-tmp-dir]seu computador de desenvolvimento .

## <a name="next-steps"></a>Passos seguintes

Aprenda a utilizar a Azure Dev Spaces e a GitHub Actions para testar alterações a partir de um pedido de puxão diretamente no AKS antes de o pedido de puxão ser fundido no ramo principal do seu repositório.

> [!div class="nextstepaction"]
> [Ações gitHub & Serviço Azure Kubernetes][gh-actions]

[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download