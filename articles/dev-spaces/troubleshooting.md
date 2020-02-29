---
title: Resolução de problemas
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: Saiba como solucionar problemas comuns ao habilitar e usar o Azure Dev Spaces
keywords: 'Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, K8S '
ms.openlocfilehash: 2b5a6f14899ec41b1740563f4e8174f65aa679c7
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198002"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Solução de problemas Azure Dev Spaces

Este guia contém informações sobre problemas comuns que poderá ter quando utilizar espaços de desenvolvimento do Azure.

Se tiver algum problema ao utilizar espaços Azure Dev, crie um [problema no repositório Azure Dev Spaces GitHub](https://github.com/Azure/dev-spaces/issues).

## <a name="before-you-begin"></a>Antes de começar

Para solucionar problemas com mais eficiência, pode ajudar a criar logs mais detalhados para análise.

Para a extensão do Estúdio Visual, detete a variável ambiente `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` para 1. Certifique-se de que reinicie o Visual Studio para a variável de ambiente para entrar em vigor. Uma vez ativados, os registos detalhados são escritos ao seu diretório `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools`.

No CLI, pode obter mais informações durante a execução do comando utilizando o interruptor `--verbose`. Também pode navegar em registos mais detalhados em `%TEMP%\Azure Dev Spaces`. Num Mac, o diretório *TEMP* pode ser encontrado executando `echo $TMPDIR` de uma janela terminal. Num computador Linux, o diretório *TEMP* costuma `/tmp`. Além disso, verifique se a exploração madeireira está ativada no ficheiro de [configuração DoClI Azure](/cli/azure/azure-cli-configuration?view=azure-cli-latest#cli-configuration-values-and-environment-variables).

Azure Dev Spaces também funciona melhor ao depurar uma única instância ou Pod. O ficheiro `azds.yaml` contém uma definição, *réplicaCount,* que indica o número de cápsulas que a Kubernetes executa para o seu serviço. Se alterar a *réplicaCount* para configurar a sua aplicação para executar várias cápsulas para um determinado serviço, o debugger liga-se à primeira cápsula, quando listada alfabeticamente. O depurador é anexado a um pod diferente quando o Pod original é reciclado, possivelmente resultando em um comportamento inesperado.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Problemas comuns ao habilitar o Azure Dev Spaces

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Erro "falha ao criar o controlador de Azure Dev Spaces"

Poderá ver este erro quando algo dá errado com a criação do controlador. Se for um erro transitório, exclua e recrie o controlador para corrigi-lo.

Você também pode tentar excluir o controlador:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Use a CLI do Azure Dev Spaces para excluir um controlador. Não é possível excluir um controlador do Visual Studio. Você também não pode instalar a CLI do Azure Dev Spaces no Azure Cloud Shell para que não possa excluir um controlador do Azure Cloud Shell.

Se você não tiver a CLI do Azure Dev Spaces instalada, você poderá primeiro instalá-la usando o comando a seguir e excluir o controlador:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Recriar o controlador pode ser feito na CLI ou do Visual Studio. Consulte o desenvolvimento da [equipa](quickstart-team-development.md) ou [desenvolva-se com](quickstart-netcore-visualstudio.md) quickstarts .NET Core, por exemplo.

### <a name="controller-create-failing-because-of-controller-name-length"></a>Falha na criação do controlador devido a comprimento do nome do controlador

O nome de um controlador de Azure Dev Spaces não pode ter mais de 31 caracteres. Se o nome do controlador exceder 31 caracteres ao habilitar espaços de desenvolvimento em um cluster AKS ou criar um controlador, você receberá um erro. Por exemplo:

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

Para corrigir esse problema, crie um controlador com um nome alternativo. Por exemplo:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Falha ao habilitar espaços de desenvolvimento quando os pools de nós do Windows são adicionados a um cluster AKS

Atualmente, o Azure Dev Spaces destina-se a ser executado somente em pods e nós do Linux. Quando você tem um cluster AKS com um pool de nós do Windows, deve garantir que Azure Dev Spaces pods sejam agendadas apenas em nós do Linux. Se um pod Azure Dev Spaces estiver agendado para ser executado em um nó do Windows, esse Pod não será iniciado e a habilitação de espaços de desenvolvimento falhará.

Para corrigir este problema, [adicione uma mancha](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) ao seu cluster AKS para garantir que as cápsulas Linux não estão programadas para funcionar num nó windows.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Erro "não foi encontrado nenhum nó Linux não reconsiderado no estado pronto no cluster. Deve haver pelo menos um nó Linux não comparável no estado pronto para implantar pods no namespace ' azds '. "

A Azure Dev Spaces não conseguiu criar um controlador no seu cluster AKS porque não conseguia encontrar um nó imaculado num estado *ready* para agendar cápsulas. A Azure Dev Spaces requer pelo menos um nó Linux em estado *Ready* que permite agendar cápsulas sem especificar tolerâncias.

Para corrigir este problema, [atualize a sua configuração de mancha](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) no seu cluster AKS para garantir que pelo menos um nó Linux permite agendar cápsulas sem especificar tolerâncias. Além disso, certifique-se de que pelo menos um nó Linux que permite agendar cápsulas sem especificar tolerações está no estado *Ready.* Se o seu nó demorar muito tempo a chegar ao estado *de Ready,* pode tentar reiniciar o seu nó.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Erro "Azure Dev Spaces CLI não instalado corretamente" ao executar `az aks use-dev-spaces`

Uma atualização para a CLI do Azure Dev Spaces alterou seu caminho de instalação. Se você estiver usando uma versão do CLI do Azure anterior a 2.0.63, poderá ver esse erro. Para exibir a sua versão do Azure CLI, utilize `az --version`.

```bash
$ az --version
azure-cli                         2.0.60 *
...
```

Apesar da mensagem de erro ao executar `az aks use-dev-spaces` com uma versão do Azure CLI antes de 2.0.63, a instalação tem sucesso. Pode continuar a usá`azds` sem problemas.

Para corrigir este problema, atualize a instalação do [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) para 2.0.63 ou posteriormente. Esta atualização irá resolver a mensagem de erro que recebe ao executar `az aks use-dev-spaces`. Como alternativa, você pode continuar a usar sua versão atual do CLI do Azure e a CLI do Azure Dev Spaces.

### <a name="error-unable-to-reach-kube-apiserver"></a>Erro "não é possível alcançar Kube-apiserver"

Você poderá ver esse erro quando Azure Dev Spaces não puder se conectar ao servidor de API do cluster AKS. 

Se o acesso ao seu servidor API do cluster AKS estiver bloqueado ou se tiver intervalos de [endereçoIP autorizados](../aks/api-server-authorized-ip-ranges.md) para o seu cluster AKS, também deve [criar](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) ou [atualizar](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) o seu cluster para [permitir intervalos adicionais baseados na sua região](https://github.com/Azure/dev-spaces/tree/master/public-ips).

Verifique se o servidor de API está disponível executando comandos kubectl. Se o servidor de API não estiver disponível, entre em contato com o suporte do AKS e tente novamente quando o servidor de API estiver funcionando.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Problemas comuns ao preparar seu projeto para Azure Dev Spaces

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Aviso "Dockerfile não pôde ser gerado devido a um idioma sem suporte"
Espaços de desenvolvimento do Azure fornece suporte nativo para c# e node. js. Quando executa `azds prep` num diretório com código escrito numa destas línguas, o Azure Dev Spaces cria automaticamente um Dockerfile apropriado para si.

Ainda pode utilizar espaços Azure Dev com código escrito noutras línguas, mas precisa de criar manualmente o Dockerfile antes de executar `azds up` pela primeira vez.

Se seu aplicativo for escrito em uma linguagem que Azure Dev Spaces não dá suporte nativo, você precisará fornecer um Dockerfile apropriado para criar uma imagem de contêiner executando seu código. O Docker fornece uma [lista das melhores práticas para escrever Ficheiros Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) e uma [referência do Dockerfile](https://docs.docker.com/engine/reference/builder/) que pode ajudá-lo a escrever um Dockerfile que se adequa às suas necessidades.

Uma vez que tenha um Dockerfile apropriado no lugar, executa `azds up` para executar a sua aplicação em Espaços Azure Dev.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Problemas comuns ao iniciar ou parar serviços com Azure Dev Spaces

### <a name="error-config-file-not-found"></a>Erro "arquivo de configuração não encontrado:"

Ao correr `azds up`, pode ver este erro. Tanto `azds up` como `azds prep` devem ser executados a partir do diretório raiz do projeto que pretende executar no seu espaço de dev.

Para resolver este problema:
1. Altere o diretório atual para a pasta de raiz que contém o código de serviço. 
1. Se não tiver um ficheiro _azds.yaml_ na pasta de código, faça `azds prep` para gerar ativos do Docker, Kubernetes e Azure Dev Spaces.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Tempo limite em "aguardando compilação da imagem de contêiner..." etapa com nós virtuais AKS

Este tempo de tempo ocorre quando se tenta usar a Dev Spaces para executar um serviço que está configurado para funcionar num [nó virtual AKS](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Os espaços de desenvolvimento atualmente não dão suporte à criação ou depuração de serviços em nós virtuais.

Se executar `azds up` com o interruptor `--verbose`, ou ativar o registo verbose no Estúdio Visual, verá detalhes adicionais:

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

O comando acima mostra que o casulo do serviço foi atribuído ao *virtual-node-aci-linux,* que é um nó virtual.

Para corrigir este problema, atualize o gráfico Helm para o serviço remover quaisquer valores *de nodeSelector* ou *tolerações* que permitam que o serviço seja executado num nó virtual. Estes valores são tipicamente definidos no ficheiro `values.yaml` do gráfico.

Você ainda pode usar um cluster AKS que tenha o recurso de nós virtuais habilitado, se o serviço que você deseja compilar ou depurar por meio de espaços de desenvolvimento for executado em um nó de VM. A execução de um serviço com espaços de desenvolvimento em um nó de VM é a configuração padrão.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Erro "não foi possível encontrar um pod de gaveta pronta" ao iniciar espaços de desenvolvimento

Este erro ocorre se o cliente do Helm já não pode comunicar com o pod Tiller em execução no cluster.

Para corrigir esse problema, reinicie os nós de agente no cluster.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Erro "libertar azds-\<identificador\>-\<nome espacial\>-\<nome de serviço\> falhou: o nome de serviço\<\>já existe" ou "Puxe o acesso negado para \<nome de serviço\>, o repositório não existe ou pode exigir 'login de estivador'".

Estes erros podem ocorrer se misturar comandos direct helm (como `helm install`, `helm upgrade`ou `helm delete`) com comandos Dev Spaces (como `azds up` e `azds down`) dentro do mesmo espaço de dev. Elas ocorrem porque os espaços de desenvolvimento têm sua própria instância de gaveta, que está em conflito com a instância de seu próprio gaveta em execução no mesmo espaço de desenvolvimento.

É bom usar comandos Helm e comandos de espaços de desenvolvimento no mesmo cluster AKS, mas cada namespace habilitado para espaços de desenvolvimento deve usar um ou outro.

Por exemplo, suponha que você use um comando Helm para executar o aplicativo inteiro em um espaço de desenvolvimento pai. Você pode criar espaços de desenvolvimento filho fora desse pai, usar espaços de desenvolvimento para executar serviços individuais dentro dos espaços de desenvolvimento filho e testar os serviços juntos. Quando estiver pronto para fazer check-in das alterações, use um comando Helm para implantar o código atualizado no espaço de desenvolvimento pai. Não utilize `azds up` para executar o serviço atualizado no espaço de dev dos pais, porque entrará em conflito com o serviço inicialmente executado usando Helm.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Dockerfile existentes não usados para criar um contêiner

Os Espaços Azure Dev podem ser configurados para apontar para um _Dockerfile_ específico no seu projeto. Se parece que a Azure Dev Spaces não está a usar o _Dockerfile_ que espera construir os seus contentores, poderá ter de dizer explicitamente aos Espaços Azure Dev que o Dockerfile deve utilizar. 

Para corrigir este problema, abra o ficheiro _azds.yaml_ que a Azure Dev Spaces gerou no seu projeto. Configurações *de atualização: desenvolver: construir: dockerfile* para apontar para o Dockerfile que pretende utilizar. Por exemplo:

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Erro "não autorizado: autenticação necessária" ao tentar usar uma imagem do Docker de um registro privado

Você está usando uma imagem do Docker de um registro privado que requer autenticação.

Para corrigir este problema, pode permitir que a Dev Spaces autenticae e retire imagens deste registo privado utilizando [imagensPullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets). Para utilizar a imagemPullSecrets, [crie um segredo Kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) no espaço de nome onde está a usar a imagem. Em seguida, forneça o segredo como uma imagemPullSecret em `azds.yaml`.

Abaixo está um exemplo de uma imagem especificandoPullSecrets em `azds.yaml`.

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: $BUILD_CONTEXT$
  dockerfile: Dockerfile
install:
  chart: $CHART_DIR$
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    # Optional, specify an array of imagePullSecrets. These secrets must be manually created in the namespace.
    # This will override the imagePullSecrets array in values.yaml file.
    # If the dockerfile specifies any private registry, the imagePullSecret for the registry must be added here.
    # ref: https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod
    #
    # This uses credentials from secret "myRegistryKeySecretName".
    imagePullSecrets:
      - name: myRegistryKeySecretName
```

> [!IMPORTANT]
> A definição de imagemPullSecrets em `azds.yaml` sobrepor-se-á à imagemPullSecrets especificada no `values.yaml`.

### <a name="error-service-cannot-be-started"></a>Erro "o serviço não pode ser iniciado".

Pode ver este erro quando o seu código de serviço não iniciar. A causa é, muitas vezes, no código do usuário. Para obter mais informações de diagnóstico, habilite o log mais detalhado ao iniciar o serviço.

A partir da linha de comando, utilize o `--verbose` para permitir uma exploração mais detalhada. Também pode especificar um formato de saída utilizando `--output`. Por exemplo:

```cmd
azds up --verbose --output json
```

No Visual Studio:

1. Ferramentas **Abertas > Opções** e em **Projetos e Soluções,** escolha **Construir e Executar.**
2. Altere as definições para **a verbosidade** de produção de construção de projeto MSBuild para **Detalhado** ou **Diagnóstico**.

    ![Caixa de diálogo Opções de captura de ecrã de ferramentas](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Executando novamente um serviço após a recriação do controlador

Recebe um *Serviço não pode ser iniciado* erro ao tentar reexecutar um serviço depois de ter removido e recriado o controlador Azure Dev Spaces associado a este cluster. Nessa situação, a saída detalhada contém o seguinte texto:

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Esse erro ocorre porque a remoção do controlador de espaços de desenvolvimento não remove os serviços instalados anteriormente pelo controlador. Recriar o controlador e, em seguida, tentar executar os serviços usando o novo controlador falhar porque os serviços antigos ainda estão no local.

Para resolver este problema, utilize o comando `kubectl delete` para remover manualmente os antigos serviços do seu cluster e, em seguida, reexecutar a Dev Spaces para instalar os novos serviços.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Erro "o serviço não pode ser iniciado". ao usar o Dockerfiles de várias etapas

O Serviço *não pode ser iniciado* quando se utiliza um Dockerfile em várias fases. Nessa situação, a saída detalhada contém o seguinte texto:

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

Esse erro ocorre porque Azure Dev Spaces atualmente não oferece suporte a compilações de vários estágios. Para evitar compilações de vários estágios, reescreva seu Dockerfile.

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>O tráfego de rede não é encaminhado para o cluster AKS ao conectar seu computador de desenvolvimento

Ao utilizar o [Azure Dev Spaces para ligar o seu cluster AKS à sua máquina](how-to/connect.md)de desenvolvimento, poderá encontrar um problema em que o tráfego de rede não seja encaminhado entre a sua máquina de desenvolvimento e o seu cluster AKS.

Ao ligar a sua máquina de desenvolvimento ao seu cluster AKS, a Azure Dev Spaces reencaminha o tráfego de rede entre o seu cluster AKS e a sua máquina de desenvolvimento, modificando o ficheiro `hosts` da sua máquina de desenvolvimento. A Azure Dev Spaces cria uma entrada no `hosts` com o endereço do serviço Kubernetes que está a substituir como nome de anfitrião. Essa entrada é usada com o encaminhamento de porta para direcionar o tráfego de rede entre o computador de desenvolvimento e o cluster AKS. Se um serviço em seu computador de desenvolvimento estiver em conflito com a porta do serviço kubernetes que você está substituindo, Azure Dev Spaces não poderá encaminhar o tráfego de rede para o serviço kubernetes. Por exemplo, o serviço *Windows BranchCache* está normalmente ligado a *0.0.0.0:80*, o que os conflitos causarão um conflito para a porta 80 em todos os IPs locais.

Para corrigir esse problema, você precisa interromper todos os serviços ou processos que entram em conflito com a porta do serviço kubernetes que você está tentando substituir. Pode utilizar ferramentas, como *netstat,* para inspecionar quais os serviços ou processos da sua máquina de desenvolvimento em conflito.

Por exemplo, parar e desativar o serviço *Windows BranchCache:*
* Executar `services.msc` a partir do pedido de comando.
* Clique à direita no *BranchCache* e selecione *Propriedades*.
* Clique em *Parar*.
* Opcionalmente, pode desativá-lo definindo o *tipo de Arranque* para *Desativado*.
* Clique em *OK*.

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Problemas comuns ao usar o Visual Studio e o Visual Studio Code com Azure Dev Spaces

### <a name="error-required-tools-and-configurations-are-missing"></a>Erro "as ferramentas e configurações necessárias estão ausentes"

Este erro poderá ocorrer ao iniciar o VS Code: "[espaços de desenvolvimento do Azure] necessário a ferramentas e configurações para compilar e depurar"[nome do projeto]"estão em falta."
O erro significa que azds.exe não está na variável de ambiente PATH, como visto no VS Code.

Tente iniciar o VS Code em um prompt de comando em que a variável de ambiente PATH esteja definida corretamente.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Erro "as ferramentas necessárias para compilar e depurar ' ProjectName ' estão desatualizadas."

Você verá esse erro em Visual Studio Code se tiver uma versão mais recente da extensão de VS Code para Azure Dev Spaces, mas uma versão mais antiga da CLI do Azure Dev Spaces.

Tente baixar e instalar a versão mais recente da CLI do Azure Dev Spaces:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Erro: "falha ao localizar a extensão do depurador para o tipo: CoreCLR"

Você pode ver esse erro ao executar o depurador de Visual Studio Code. Talvez você não tenha a extensão de VS Code C# para instalada em seu computador de desenvolvimento. A C# extensão inclui suporte de depuração para o CoreCLR (.NET Core).

Para corrigir este problema, instale a [extensão C# ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)do Código VS para .

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Erro "não há suporte para o tipo de depuração configurado ' CoreCLR '"

Você pode ver esse erro ao executar o depurador de Visual Studio Code. Talvez você não tenha a extensão de VS Code para Azure Dev Spaces instalada em seu computador de desenvolvimento.

Para corrigir este problema, instale a extensão do [Código VS para espaços Azure Dev](get-started-netcore.md).

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Erro "valor de ' CWD ' inválido '/src '. O sistema não é possível localizar o ficheiro especificado." ou "Iniciar: programa '/ src / [caminho para o binário do projeto]' não existe"

Você pode ver esse erro ao executar o depurador de Visual Studio Code. Por predefinição, a extensão do Código VS utiliza `src` como diretório de trabalho para o projeto no recipiente. Se atualizou o seu `Dockerfile` para especificar um diretório de trabalho diferente, poderá ver este erro.

Para corrigir este problema, atualize o ficheiro `launch.json` sob o subdiretório `.vscode` da sua pasta de projeto. Altere a diretiva `configurations->cwd` para apontar para o mesmo diretório que o `WORKDIR` definido no `Dockerfile`do seu projeto. Pode também ter de atualizar a diretiva `configurations->program`.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Erro "o programa de pipe ' azds ' saiu inesperadamente com o código 126."

Você pode ver esse erro ao executar o depurador de Visual Studio Code.

Para corrigir esse problema, feche e reabra Visual Studio Code. Reinicie o depurador.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Erro "falha na inspeção interna: Watch ENOSPC" ao anexar a depuração a um aplicativo node. js

Este erro ocorre quando o nó que executa a cápsula com a aplicação Node.js a que está a tentar anexar com um debugger excedeu o valor *fs.inotify.max_user_watches.* Em alguns casos, [o valor padrão de *fs.inotify.max_user_watches* pode ser demasiado pequeno para manusear a fixação de um debugger diretamente a uma cápsula](https://github.com/Azure/AKS/issues/772).

Uma suposições temporárias para esta questão é aumentar o valor de *fs.inotify.max_user_watches* em cada nó do cluster e reiniciar o nó para que as alterações entrem em vigor.

## <a name="other-common-issues"></a>Outros problemas comuns

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>O erro "azds" não é reconhecido como um comando interno ou externo, um programa operável ou um arquivo em lotes

Este erro pode acontecer se `azds.exe` não for instalado ou configurado corretamente.

Para resolver este problema:

1. Verifique a localização %ProgramFiles%/Microsoft SDKs\Azure\Azure\Azure Dev Spaces CLI para `azds.exe`. Se existir, adicione essa localização à variável de ambiente PATH.
2. Se `azds.exe` não estiver instalado, execute o seguinte comando:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>Erro de autorização "Microsoft. DevSpaces/Register/Action"

Precisa de acesso *do Proprietário* ou *colaborador* na sua subscrição Azure para gerir os Espaços Azure Dev. Se estiver a tentar gerir a Dev Spaces e não tiver acesso ao *Proprietário* ou *Colaborador* à subscrição do Azure associada, poderá ver um erro de autorização. Por exemplo:

```console
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

Para corrigir este problema, utilizando uma conta com acesso ao *Proprietário* ou *Colaborador* à subscrição do Azure, registe manualmente o espaço de nome `Microsoft.DevSpaces`:

```console
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>Novos pods não estão sendo iniciados

O inicializador Kubernetes não pode aplicar o PodSpec para novas cápsulas devido a alterações de permissão RBAC na função *cluster-admin* no cluster. O novo pod também pode ter um PodSpec inválido, por exemplo, a conta de serviço associada ao Pod não existe mais. Para ver as cápsulas que se encontram em estado *pendente* devido à questão do inicializador, utilize o comando `kubectl get pods`:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Esta edição pode ter impacto em cápsulas em *todos os espaços* de nome no cluster, incluindo espaços de nome onde o Azure Dev Spaces não está ativado.

Para corrigir este problema, [atualize o CLI dos Espaços Dev para a versão mais recente](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) e, em seguida, apagando a *Configuração inicializador azds* do controlador Dev Spaces:

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

Depois de ter removido a configuração do *inicializador azds* do controlador Azure Dev Spaces, utilize `kubectl delete` para remover quaisquer cápsulas num estado *pendente.* Depois que todos os pods pendentes tiverem sido removidos, reimplante o pods.

Se as novas cápsulas continuarem presas num estado *pendente* após uma recolocação, utilize `kubectl delete` para remover quaisquer cápsulas em estado *pendente.* Depois que todos os pods pendentes tiverem sido removidos, exclua o controlador do cluster e reinstale-o:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Depois que o controlador for reinstalado, reimplante o pods.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Permissões RBAC incorretas para chamar controlador e APIs de espaços de desenvolvimento

O utilizador que aceda ao controlador Azure Dev Spaces deve ter acesso a ler o administrador *kubeconfig* no cluster AKS. Por exemplo, esta permissão está disponível no papel de administrador de cluster de [serviço azure Kubernetes incorporado.](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions) O utilizador que aceda ao controlador Azure Dev Spaces deve também ter a função *DeRBAC do Colaborador* ou *Proprietário* para o controlador. Mais detalhes sobre a atualização das permissões de um utilizador para um cluster AKS estão disponíveis [aqui](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Para atualizar a função RBAC do usuário para o controlador:

1. Inicie sessão no portal do Azure em https://portal.azure.com.
1. Navegue até o grupo de recursos que contém o controlador, que geralmente é o mesmo que o cluster AKS.
1. Ativar a caixa de verificação *de tipos ocultos Show.*
1. Clique no controlador.
1. Abra o painel de Controlo de *Acesso (IAM).*
1. Clique no separador *Role Assignments.*
1. Clique em *Adicionar* e, em seguida, *adicionar a atribuição de funções*.
    * Para *função*, selecione *Colaborador* ou *Proprietário*.
    * Para *atribuir acesso a*, selecione utilizador, grupo ou diretor de serviço *Azure AD*.
    * Para *Selecionar,* procure o utilizador que pretende dar permissões.
1. Clique em *Guardar*.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Resolução de nomes DNS falha por um URL público associado a um serviço de espaços de desenvolvimento

Pode configurar um ponto final de URL público para o seu serviço, especificando o `--enable-ingress` mudar para o comando `azds prep` ou selecionando a caixa de verificação `Publicly Accessible` no Estúdio Visual. O nome DNS público é registrado automaticamente quando você executa o serviço em espaços de desenvolvimento. Se este nome DNS não estiver registado, verá que uma *Página não pode ser exibida* ou o Site não pode ser *atingido* erro no seu navegador na web quando se conecta ao URL público.

Para resolver este problema:

* Verifique o status de todas as URLs associadas aos seus serviços de espaços de desenvolvimento:

  ```console
  azds list-uris
  ```

* Se um URL estiver no estado *pendente,* a Dev Spaces ainda aguarda que o registo do DNS esteja concluído. Às vezes, demora alguns minutos para o registo concluir. Espaços de desenvolvimento também é aberto um túnel de localhost para cada serviço, que pode utilizar enquanto estiver aguardando o registo de DNS.
* Se um URL permanecer no estado *pendente* por mais de 5 minutos, pode indicar um problema com o casulo DNS externo que cria o ponto final público ou o casulo de comando de ingresso nginx que adquire o ponto final público. Use os comandos a seguir para excluir esses pods e permitir que o AKS os recrie automaticamente:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Erro "erro de conexão upstream ou desconectar/redefinir antes dos cabeçalhos"

Poderá ver este erro ao tentar aceder ao seu serviço. Por exemplo, quando passa para o URL do serviço num navegador. Esse erro significa que a porta do contêiner não está disponível. Isso pode ser pelos seguintes motivos:

* O contentor está ainda estiver no processo que está a ser compilado e implantado. Esta questão pode surgir se executar `azds up` ou iniciar o desordeiro, e depois tentar aceder ao recipiente antes de ser implantado com sucesso.
* A configuração da porta não é consistente em todo o seu _Dockerfile,_ Helm Chart, e qualquer código de servidor que abra uma porta.

Para resolver este problema:

1. Se o contentor está no processo a ser criado/implementado, pode aguardar 2 a 3 segundos e tente acessar o serviço novamente. 
1. Verifique a configuração da sua porta nos seguintes ativos:
    * **[Gráfico de leme:](https://docs.helm.sh)** Especificado pelo `service.port` e `deployment.containerPort` em valores.yaml andaimes por `azds prep` comando.
    * Quaisquer portas abertas no código de aplicação, por exemplo no Nó.js: `var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>Não foi possível encontrar o nome do namespace ou tipo "MyLibrary"

Um projeto de biblioteca que você está usando não pode ser encontrado. Com espaços de desenvolvimento, o contexto de compilação está no nível de projeto/serviço por padrão.  

Para resolver este problema:

1. Modifique o ficheiro `azds.yaml` para definir o contexto de construção para o nível de solução.
2. Modifique os ficheiros `Dockerfile` e `Dockerfile.develop` para se referir aos ficheiros do projeto, por exemplo, `.csproj`, corretamente em relação ao novo contexto de construção.
3. Adicione um `.dockerignore` no mesmo diretório que o ficheiro `.sln`.
4. Atualize o `.dockerignore` com entradas adicionais, se necessário.

Pode encontrar um exemplo [aqui.](https://github.com/sgreenmsft/buildcontextsample)

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>O dimensionamento automático do pod horizontal não está funcionando em um espaço de desenvolvimento

Quando executa um serviço num espaço de v, a cápsula desse serviço é [injetada com recipientes adicionais para instrumentação](how-dev-spaces-works.md#prepare-your-aks-cluster) e todos os recipientes de uma cápsula precisam de ter limites de recursos e pedidos definidos para a Autoscalização Horizontal Pod.

Para corrigir esse problema, aplique uma solicitação de recurso e limite aos contêineres de espaços de desenvolvimento injetados. Os pedidos e limites de recursos podem ser aplicados para o recipiente injetado (devspaces-proxy) adicionando o `azds.io/proxy-resources` anotação à especificação do seu casulo. O valor deve ser fixado num objeto JSON que represente a secção de recursos da especificação do recipiente para o proxy.

Abaixo está um exemplo de uma anotação de recursos de proxy que deve ser aplicada à sua especificação de Pod.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Habilitar Azure Dev Spaces em um namespace existente com pods em execução

Você pode ter um cluster AKS existente e um namespace com pods em execução onde você deseja habilitar Azure Dev Spaces.

Para ativar o Azure Dev Spaces num espaço de nome existente num cluster AKS, executar `use-dev-spaces` e utilizar `kubectl` para reiniciar todas as cápsulas nesse espaço de nome.

```console
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
kubectl -n my-namespace delete pod --all
```

Depois que o pods for reiniciado, você poderá começar a usar seu namespace existente com Azure Dev Spaces.

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>Habilitar Azure Dev Spaces no cluster AKS com tráfego de saída restrito para nós de cluster

Para habilitar Azure Dev Spaces em um cluster AKS para o qual o tráfego de saída de nós de cluster é restrito, você precisará permitir os seguintes FQDNs:

| FQDN                                    | Porta      | Utilização      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Para efetuar pull de imagens do Linux Alpine e outras Azure Dev Spaces |
| gcr.io | HTTP: 443 | Para efetuar pull de imagens do Helm/gaveta|
| storage.googleapis.com | HTTP: 443 | Para efetuar pull de imagens do Helm/gaveta|
| azds-<guid>.<location>.azds.io | HTTPS:443 | Para se comunicar com os serviços de back-end Azure Dev Spaces para seu controlador. O FQDN exato pode ser encontrado no "dataplaneFqdn" em %USERPROFILE%\.azds\settings.json|

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>Erro "Não consegui encontrar o cluster \<cluster\> na subscrição \<subscrição Id\>"

Você poderá ver esse erro se o arquivo kubeconfig estiver direcionado a um cluster ou assinatura diferente do que você está tentando usar com as ferramentas do lado do cliente Azure Dev Spaces. A ferramenta do lado do cliente Azure Dev Spaces replica o comportamento do *kubectl*, que utiliza [um ou mais ficheiros kubeconfig](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/) para selecionar e comunicar com o cluster.

Para resolver este problema:

* Use `az aks use-dev-spaces -g <resource group name> -n <cluster name>` para atualizar o contexto atual. Esse comando também habilita Azure Dev Spaces no cluster AKS se o ainda não estiver habilitado. Em alternativa, pode utilizar `kubectl config use-context <cluster name>` para atualizar o contexto atual.
* Use `az account show` para mostrar a subscrição atual do Azure que está a visar e verificar se está correta. Pode alterar a subscrição que está a ser alvo usando `az account set`.

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>Erro utilizando Espaços Dev após rotação de certificados AKS

Depois [de rodar os certificados no seu cluster AKS,](../aks/certificate-rotation.md)certas operações, como `azds space list` e `azds up` falharão. Também precisa de atualizar os certificados no seu controlador Azure Dev Spaces depois de rodar os certificados no seu cluster.

Para corrigir este problema, certifique-se de que o seu *kubeconfig* tem os certificados atualizados utilizando `az aks get-credentials` em seguida, executar o comando `azds controller refresh-credentials`. Por exemplo:

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
