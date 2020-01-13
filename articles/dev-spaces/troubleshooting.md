---
title: Resolução de problemas
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: conceptual
description: Saiba como solucionar problemas comuns ao habilitar e usar o Azure Dev Spaces
keywords: 'Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, K8S '
ms.openlocfilehash: a52d27733168c55f9e34d15f6675dd7bce0f8aad
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438113"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Solução de problemas Azure Dev Spaces

Este guia contém informações sobre problemas comuns que poderá ter quando utilizar espaços de desenvolvimento do Azure.

Se você tiver um problema ao usar Azure Dev Spaces, crie um [problema no repositório GitHub Azure dev Spaces](https://github.com/Azure/dev-spaces/issues).

## <a name="before-you-begin"></a>Antes de começar

Para solucionar problemas com mais eficiência, pode ajudar a criar logs mais detalhados para análise.

Para a extensão do Visual Studio, defina o `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` variável de ambiente para 1. Certifique-se de que reinicie o Visual Studio para a variável de ambiente para entrar em vigor. Uma vez habilitado, os logs detalhados são gravados no diretório `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools`.

Na CLI, o utilizador pode apresentar mais informações durante a execução do comando utilizando o `--verbose` mudar. Também pode procurar registos mais detalhados no `%TEMP%\Azure Dev Spaces`. Num Mac, o diretório TEMP do diretório pode ser encontrado ao executar `echo $TMPDIR` de uma janela de terminal. Num computador Linux, o diretório TEMP do diretório é normalmente `/tmp`.

Azure Dev Spaces também funciona melhor ao depurar uma única instância ou Pod. O arquivo de `azds.yaml` contém uma configuração, *replicaCount*, que indica o número de pods que o kubernetes executa para o serviço. Se você alterar o *replicaCount* para configurar seu aplicativo para executar vários pods para um determinado serviço, o depurador será anexado ao primeiro Pod, quando listado em ordem alfabética. O depurador é anexado a um pod diferente quando o Pod original é reciclado, possivelmente resultando em um comportamento inesperado.

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

Recriar o controlador pode ser feito na CLI ou do Visual Studio. Consulte os guias de início rápido de [desenvolvimento da equipe](quickstart-team-development.md) ou [desenvolver com o .NET Core](quickstart-netcore-visualstudio.md) para obter exemplos.

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

Para corrigir esse problema, [adicione um](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) seu cluster AKs para garantir que os pods do Linux não estejam agendados para execução em um nó do Windows.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Erro "não foi encontrado nenhum nó Linux não reconsiderado no estado pronto no cluster. Deve haver pelo menos um nó Linux não comparável no estado pronto para implantar pods no namespace ' azds '. "

Azure Dev Spaces não foi possível criar um controlador no cluster AKS porque ele não encontrou um nó não indesejado em um estado *pronto* para agendar pods. Azure Dev Spaces requer pelo menos um nó do Linux em um estado *pronto* que permita o agendamento de pods sem especificar Tolerations.

Para corrigir esse problema, [Atualize sua configuração](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) do seu cluster AKs para garantir que pelo menos um nó Linux permita o agendamento de pods sem especificar Tolerations. Além disso, verifique se pelo menos um nó Linux que permite o agendamento de pods sem especificar Tolerations está no estado *pronto* . Se o seu nó estiver demorando muito tempo para chegar ao estado *pronto* , você poderá tentar reiniciar o nó.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Erro "a CLI do Azure Dev Spaces não foi instalada corretamente" durante a execução de `az aks use-dev-spaces`

Uma atualização para a CLI do Azure Dev Spaces alterou seu caminho de instalação. Se você estiver usando uma versão do CLI do Azure anterior a 2.0.63, poderá ver esse erro. Para exibir sua versão do CLI do Azure, use `az --version`.

```bash
$ az --version
azure-cli                         2.0.60 *
...
```

Apesar da mensagem de erro ao executar `az aks use-dev-spaces` com uma versão do CLI do Azure antes de 2.0.63, a instalação é realizada com sucesso. Você pode continuar a usar `azds` sem problemas.

Para corrigir esse problema, atualize a instalação do [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) para o 2.0.63 ou posterior. Esta atualização resolverá a mensagem de erro que você recebe ao executar `az aks use-dev-spaces`. Como alternativa, você pode continuar a usar sua versão atual do CLI do Azure e a CLI do Azure Dev Spaces.

### <a name="error-unable-to-reach-kube-apiserver"></a>Erro "não é possível alcançar Kube-apiserver"

Você poderá ver esse erro quando Azure Dev Spaces não puder se conectar ao servidor de API do cluster AKS. 

Se o acesso ao servidor de API do cluster do AKS estiver bloqueado ou se você tiver [intervalos de endereços IP autorizados do servidor de API](../aks/api-server-authorized-ip-ranges.md) habilitados para o cluster do AKs, você também deverá [criar](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) ou [Atualizar](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) o cluster para [permitir intervalos adicionais com base em sua região](https://github.com/Azure/dev-spaces/tree/master/public-ips).

Verifique se o servidor de API está disponível executando comandos kubectl. Se o servidor de API não estiver disponível, entre em contato com o suporte do AKS e tente novamente quando o servidor de API estiver funcionando.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Problemas comuns ao preparar seu projeto para Azure Dev Spaces

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Aviso "Dockerfile não pôde ser gerado devido a um idioma sem suporte"
Espaços de desenvolvimento do Azure fornece suporte nativo para c# e node. js. Quando você executa `azds prep` em um diretório com código escrito em uma dessas linguagens, Azure Dev Spaces cria automaticamente um Dockerfile apropriado para você.

Você ainda pode usar Azure Dev Spaces com código escrito em outras linguagens, mas precisa criar manualmente o Dockerfile antes de executar `azds up` pela primeira vez.

Se seu aplicativo for escrito em uma linguagem que Azure Dev Spaces não dá suporte nativo, você precisará fornecer um Dockerfile apropriado para criar uma imagem de contêiner executando seu código. O Docker fornece uma [lista de práticas recomendadas para escrever Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) e uma [referência Dockerfile](https://docs.docker.com/engine/reference/builder/) que pode ajudá-lo a escrever um Dockerfile que atenda às suas necessidades.

Quando tiver um Dockerfile apropriado em vigor, execute `azds up` para executar seu aplicativo no Azure Dev Spaces.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Problemas comuns ao iniciar ou parar serviços com Azure Dev Spaces

### <a name="error-config-file-not-found"></a>Erro "arquivo de configuração não encontrado:"

Ao executar `azds up`, você poderá ver esse erro. Tanto `azds up` quanto `azds prep` devem ser executadas no diretório raiz do projeto que você deseja executar no espaço de desenvolvimento.

Para resolver este problema:
1. Altere o diretório atual para a pasta de raiz que contém o código de serviço. 
1. Se você não tiver um arquivo _azds. YAML_ na pasta de código, execute `azds prep` para gerar os ativos Docker, Kubernetes e Azure dev Spaces.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Tempo limite em "aguardando compilação da imagem de contêiner..." etapa com nós virtuais AKS

Esse tempo limite ocorre quando você tenta usar espaços de desenvolvimento para executar um serviço que está configurado para ser executado em um [nó virtual AKs](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Os espaços de desenvolvimento atualmente não dão suporte à criação ou depuração de serviços em nós virtuais.

Se você executar `azds up` com a opção `--verbose` ou habilitar o log detalhado no Visual Studio, verá detalhes adicionais:

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

O comando acima mostra que o Pod do serviço foi atribuído a *virtual-node-ACI-Linux*, que é um nó virtual.

Para corrigir esse problema, atualize o gráfico Helm para que o serviço remova os valores *nodeSelector* ou *Tolerations* que permitem que o serviço seja executado em um nó virtual. Esses valores normalmente são definidos no arquivo de `values.yaml` do gráfico.

Você ainda pode usar um cluster AKS que tenha o recurso de nós virtuais habilitado, se o serviço que você deseja compilar ou depurar por meio de espaços de desenvolvimento for executado em um nó de VM. A execução de um serviço com espaços de desenvolvimento em um nó de VM é a configuração padrão.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Erro "não foi possível encontrar um pod de gaveta pronta" ao iniciar espaços de desenvolvimento

Este erro ocorre se o cliente do Helm já não pode comunicar com o pod Tiller em execução no cluster.

Para corrigir esse problema, reinicie os nós de agente no cluster.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Erro "Release azds-\<identificador\>-\<spacename\>-\<ServiceName\> falhou: os serviços '\<ServiceName\>' já existe" ou "acesso de pull negado para \<ServiceName\>, o repositório não existe ou pode exigir ' Docker login"

Esses erros podem ocorrer se você misturar comandos Helm diretos em execução (como `helm install`, `helm upgrade`ou `helm delete`) com comandos de espaços de desenvolvimento (como `azds up` e `azds down`) dentro do mesmo espaço de desenvolvimento. Elas ocorrem porque os espaços de desenvolvimento têm sua própria instância de gaveta, que está em conflito com a instância de seu próprio gaveta em execução no mesmo espaço de desenvolvimento.

É bom usar comandos Helm e comandos de espaços de desenvolvimento no mesmo cluster AKS, mas cada namespace habilitado para espaços de desenvolvimento deve usar um ou outro.

Por exemplo, suponha que você use um comando Helm para executar o aplicativo inteiro em um espaço de desenvolvimento pai. Você pode criar espaços de desenvolvimento filho fora desse pai, usar espaços de desenvolvimento para executar serviços individuais dentro dos espaços de desenvolvimento filho e testar os serviços juntos. Quando estiver pronto para fazer check-in das alterações, use um comando Helm para implantar o código atualizado no espaço de desenvolvimento pai. Não use `azds up` para executar o serviço atualizado no espaço de desenvolvimento pai, pois ele entrará em conflito com o serviço executado inicialmente usando Helm.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Dockerfile existentes não usados para criar um contêiner

Espaços de desenvolvimento do Azure pode ser configurados para apontar para um específico _Dockerfile_ no seu projeto. Se aparecer Azure Dev Spaces não estiver usando o _Dockerfile_ de que você espera criar seus contêineres, talvez seja necessário informar explicitamente Azure dev Spaces qual Dockerfile usar. 

Para corrigir esse problema, abra o arquivo _azds. YAML_ que Azure dev Spaces gerado em seu projeto. Configurações de atualização *: desenvolver: Build: dockerfile* para apontar para o dockerfile que você deseja usar. Por exemplo:

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Erro "não autorizado: autenticação necessária" ao tentar usar uma imagem do Docker de um registro privado

Você está usando uma imagem do Docker de um registro privado que requer autenticação.

Para corrigir esse problema, você pode permitir que os espaços de desenvolvimento autentiquem e recebam imagens desse registro privado usando [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets). Para usar o imagePullSecrets, [crie um segredo kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) no namespace em que você está usando a imagem. Em seguida, forneça o segredo como um imagePullSecret no `azds.yaml`.

Abaixo está um exemplo de uma especificação de imagePullSecrets no `azds.yaml`.

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
> A definição de imagePullSecrets em `azds.yaml` substituirá imagePullSecrets especificado na `values.yaml`.

### <a name="error-service-cannot-be-started"></a>Erro "o serviço não pode ser iniciado".

Pode ver este erro quando o seu código de serviço não iniciar. A causa é, muitas vezes, no código do usuário. Para obter mais informações de diagnóstico, habilite o log mais detalhado ao iniciar o serviço.

Na linha de comando, use o `--verbose` para habilitar o log mais detalhado. Você também pode especificar um formato de saída usando `--output`. Por exemplo:

```cmd
azds up --verbose --output json
```

No Visual Studio:

1. Open **ferramentas > opções** e, em **projetos e soluções**, escolha **compilar e executar**.
2. Alterar as definições de **verbosidade de saída da compilação de projeto do MSBuild** ao **Detailed** ou **diagnóstico**.

    ![Caixa de diálogo Opções de captura de ecrã de ferramentas](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Executando novamente um serviço após a recriação do controlador

Você recebe um erro *serviço não pode ser iniciado* ao tentar executar novamente um serviço depois de ter removido e recriado o controlador de Azure dev Spaces associado a esse cluster. Nessa situação, a saída detalhada contém o seguinte texto:

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Esse erro ocorre porque a remoção do controlador de espaços de desenvolvimento não remove os serviços instalados anteriormente pelo controlador. Recriar o controlador e, em seguida, tentar executar os serviços usando o novo controlador falhar porque os serviços antigos ainda estão no local.

Para resolver esse problema, use o comando `kubectl delete` para remover manualmente os serviços antigos do cluster e, em seguida, execute novamente os espaços de desenvolvimento para instalar os novos serviços.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Erro "o serviço não pode ser iniciado". ao usar o Dockerfiles de várias etapas

Você recebe um erro de *serviço não pode ser iniciado* ao usar um Dockerfile de vários estágios. Nessa situação, a saída detalhada contém o seguinte texto:

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

Esse erro ocorre porque os nós AKS executam uma versão mais antiga do Docker que não dá suporte a compilações de vários estágios. Para evitar compilações de vários estágios, reescreva seu Dockerfile.

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>O tráfego de rede não é encaminhado para o cluster AKS ao conectar seu computador de desenvolvimento

Ao usar [Azure dev Spaces para conectar o cluster AKs ao seu computador de desenvolvimento](how-to/connect.md), você pode encontrar um problema em que o tráfego de rede não é encaminhado entre o computador de desenvolvimento e o cluster AKs.

Ao conectar seu computador de desenvolvimento ao cluster AKS, o Azure Dev Spaces encaminha o tráfego de rede entre o cluster AKS e o computador de desenvolvimento modificando o arquivo de `hosts` do seu computador de desenvolvimento. Azure Dev Spaces cria uma entrada no `hosts` com o endereço do serviço kubernetes que você está substituindo como um nome de host. Essa entrada é usada com o encaminhamento de porta para direcionar o tráfego de rede entre o computador de desenvolvimento e o cluster AKS. Se um serviço em seu computador de desenvolvimento estiver em conflito com a porta do serviço kubernetes que você está substituindo, Azure Dev Spaces não poderá encaminhar o tráfego de rede para o serviço kubernetes. Por exemplo, o serviço do *Windows BranchCache* é geralmente associado a *0.0.0.0:80*, que entrará em conflito com a porta 80 em todos os IPs locais.

Para corrigir esse problema, você precisa interromper todos os serviços ou processos que entram em conflito com a porta do serviço kubernetes que você está tentando substituir. Você pode usar ferramentas, como *netstat*, para inspecionar quais serviços ou processos em seu computador de desenvolvimento estão em conflito.

Por exemplo, para parar e desabilitar o serviço *Windows BranchCache* :
* Execute `services.msc` no prompt de comando.
* Clique com o botão direito do mouse em *BranchCache* e selecione *Propriedades*.
* Clique em *parar*.
* Opcionalmente, você pode desabilitá-lo definindo o *tipo de inicialização* como *desabilitado*.
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

Para corrigir esse problema, instale a [extensão de vs Code C#para ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)o.

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Erro "não há suporte para o tipo de depuração configurado ' CoreCLR '"

Você pode ver esse erro ao executar o depurador de Visual Studio Code. Talvez você não tenha a extensão de VS Code para Azure Dev Spaces instalada em seu computador de desenvolvimento.

Para corrigir esse problema, instale a [extensão de vs Code para Azure dev Spaces](get-started-netcore.md).

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Erro "valor de ' CWD ' inválido '/src '. O sistema não é possível localizar o ficheiro especificado." ou "Iniciar: programa '/ src / [caminho para o binário do projeto]' não existe"

Você pode ver esse erro ao executar o depurador de Visual Studio Code. Por predefinição, utiliza a extensão do VS Code `src` como o diretório de trabalho para o projeto no contentor. Se atualizou seu `Dockerfile` para especificar um diretório de trabalho diferentes, pode ver este erro.

Para corrigir esse problema, atualize o arquivo de `launch.json` no subdiretório `.vscode` da pasta do projeto. Alterar o `configurations->cwd` diretiva para apontar para o mesmo diretório que o `WORKDIR` definidos no seu projeto `Dockerfile`. Também poderá ter de atualizar o `configurations->program` diretiva também.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Erro "o programa de pipe ' azds ' saiu inesperadamente com o código 126."

Você pode ver esse erro ao executar o depurador de Visual Studio Code.

Para corrigir esse problema, feche e reabra Visual Studio Code. Reinicie o depurador.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Erro "falha na inspeção interna: Watch ENOSPC" ao anexar a depuração a um aplicativo node. js

Esse erro ocorre quando o nó que executa o Pod com o aplicativo node. js ao qual você está tentando se anexar com um depurador excedeu o valor *FS. INotifyPropertyChanged. max_user_watches* . Em alguns casos, [o valor padrão de *FS. inotifypropertychanged. max_user_watches* pode ser muito pequeno para lidar com a anexação de um depurador diretamente a um pod](https://github.com/Azure/AKS/issues/772).

Uma solução alternativa temporária para esse problema é aumentar o valor de *FS. INotifyPropertyChanged. max_user_watches* em cada nó no cluster e reiniciar esse nó para que as alterações entrem em vigor.

## <a name="other-common-issues"></a>Outros problemas comuns

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>O erro "azds" não é reconhecido como um comando interno ou externo, um programa operável ou um arquivo em lotes

Esse erro pode ocorrer se `azds.exe` não estiver instalado ou configurado corretamente.

Para resolver este problema:

1. Verifique o local% ProgramFiles%/Microsoft SDKs\Azure\Azure dev Spaces CLI para `azds.exe`. Se existir, adicione essa localização à variável de ambiente PATH.
2. Se `azds.exe` não estiver instalado, execute o seguinte comando:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>Erro de autorização "Microsoft. DevSpaces/Register/Action"

Você precisa de acesso de *proprietário* ou *colaborador* em sua assinatura do Azure para gerenciar Azure dev Spaces. Se você estiver tentando gerenciar espaços de desenvolvimento e não tiver acesso de *proprietário* ou *colaborador* à assinatura do Azure associada, você poderá ver um erro de autorização. Por exemplo:

```console
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

Para corrigir esse problema, usando uma conta com acesso de *proprietário* ou *colaborador* à assinatura do Azure, Registre manualmente o namespace de `Microsoft.DevSpaces`:

```console
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>Novos pods não estão sendo iniciados

O inicializador kubernetes não pode aplicar o PodSpec para o novo pods devido a alterações de permissão de RBAC para a função de *administrador de cluster* no cluster. O novo pod também pode ter um PodSpec inválido, por exemplo, a conta de serviço associada ao Pod não existe mais. Para ver os pods que estão em um estado *pendente* devido ao problema do inicializador, use o comando `kubectl get pods`:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Esse problema pode afetar os pods em *todos os namespaces* no cluster, incluindo namespaces em que o Azure dev Spaces não está habilitado.

Para corrigir esse problema, [atualize a CLI de espaços de desenvolvimento para a versão mais recente](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) e, em seguida, exclua o *azds InitializerConfiguration* do controlador de Azure dev Spaces:

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

Depois de remover o *Azds InitializerConfiguration* do controlador de Azure dev Spaces, use `kubectl delete` para remover os pods em um estado *pendente* . Depois que todos os pods pendentes tiverem sido removidos, reimplante o pods.

Se novos pods ainda estiverem presos em um estado *pendente* após uma reimplantação, use `kubectl delete` para remover os pods em um estado *pendente* . Depois que todos os pods pendentes tiverem sido removidos, exclua o controlador do cluster e reinstale-o:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Depois que o controlador for reinstalado, reimplante o pods.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Permissões RBAC incorretas para chamar controlador e APIs de espaços de desenvolvimento

O usuário que acessa o controlador de Azure Dev Spaces deve ter acesso para ler o *kubeconfig* do administrador no cluster AKs. Por exemplo, essa permissão está disponível na [função de administrador de cluster do serviço kubernetes do Azure interna](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). O usuário que acessa o controlador de Azure Dev Spaces também deve ter a função de RBAC de *proprietário* ou *colaborador* para o controlador. Mais detalhes sobre como atualizar as permissões de um usuário para um cluster AKS estão disponíveis [aqui](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Para atualizar a função RBAC do usuário para o controlador:

1. Inicie sessão no portal do Azure em https://portal.azure.com.
1. Navegue até o grupo de recursos que contém o controlador, que geralmente é o mesmo que o cluster AKS.
1. Habilite a caixa de seleção *Mostrar tipos ocultos* .
1. Clique no controlador.
1. Abra o painel *controle de acesso (iam)* .
1. Clique na guia *atribuições de função* .
1. Clique em *Adicionar* e em *Adicionar atribuição de função*.
    * Para *função*, selecione *colaborador* ou *proprietário*.
    * Para *atribuir acesso a*, selecione *usuário, grupo ou entidade de serviço do Azure ad*.
    * Para *selecionar*, pesquise o usuário que você deseja conceder permissões.
1. Clique em *Guardar*.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Resolução de nomes DNS falha por um URL público associado a um serviço de espaços de desenvolvimento

Você pode configurar um ponto de extremidade de URL pública para seu serviço especificando o `--public` alternar para o comando `azds prep` ou marcando a caixa de seleção `Publicly Accessible` no Visual Studio. O nome DNS público é registrado automaticamente quando você executa o serviço em espaços de desenvolvimento. Se esse nome DNS não estiver registrado, você verá que uma *página não pode ser exibida* ou o erro *site não pode ser acessado* no navegador da Web ao se conectar à URL pública.

Para resolver este problema:

* Verifique o status de todas as URLs associadas aos seus serviços de espaços de desenvolvimento:

  ```console
  azds list-uris
  ```

* Se uma URL estiver no estado *pendente* , os espaços de desenvolvimento ainda aguardarão a conclusão do registro de DNS. Às vezes, demora alguns minutos para o registo concluir. Espaços de desenvolvimento também é aberto um túnel de localhost para cada serviço, que pode utilizar enquanto estiver aguardando o registo de DNS.
* Se uma URL permanecer no estado *pendente* por mais de 5 minutos, isso poderá indicar um problema com o Pod DNS externo que cria o ponto de extremidade público ou o Pod do controlador de entrada Nginx que adquire o ponto de extremidade público. Use os comandos a seguir para excluir esses pods e permitir que o AKS os recrie automaticamente:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Erro "erro de conexão upstream ou desconectar/redefinir antes dos cabeçalhos"

Poderá ver este erro ao tentar aceder ao seu serviço. Por exemplo, quando passa para o URL do serviço num navegador. Esse erro significa que a porta do contêiner não está disponível. Isso pode ser pelos seguintes motivos:

* O contentor está ainda estiver no processo que está a ser compilado e implantado. Este problema pode surgir se executar `azds up` ou iniciar o depurador e, em seguida, tente aceder ao contentor antes de ser implementada com êxito.
* Configuração da porta não é consistente em toda sua _Dockerfile_, gráfico Helm e qualquer código de servidor que se abre uma porta.

Para resolver este problema:

1. Se o contentor está no processo a ser criado/implementado, pode aguardar 2 a 3 segundos e tente acessar o serviço novamente. 
1. Verifique a configuração de porta. Os números de porta especificados devem ser **idênticos** em todos os seguintes ativos:
    * **O Dockerfile:** especificado pelo `EXPOSE` instrução.
    * **[Gráfico do Helm](https://docs.helm.sh):** especificado pela `externalPort` e `internalPort` valores para um serviço (muitas vezes, localizados num `values.yml` ficheiro),
    * Nenhuma porta de que está a ser aberta no código da aplicação, por exemplo, no node. js: `var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>Não foi possível encontrar o nome do namespace ou tipo "MyLibrary"

Um projeto de biblioteca que você está usando não pode ser encontrado. Com espaços de desenvolvimento, o contexto de compilação está no nível de projeto/serviço por padrão.  

Para resolver este problema:

1. Modifique o arquivo de `azds.yaml` para definir o contexto de compilação para o nível da solução.
2. Modifique os arquivos de `Dockerfile` e `Dockerfile.develop` para referir-se aos arquivos de projeto, por exemplo `.csproj`, corretamente relativos ao novo contexto de compilação.
3. Adicione um `.dockerignore` no mesmo diretório que o arquivo de `.sln`.
4. Atualize o `.dockerignore` com entradas adicionais, conforme necessário.

Você pode encontrar um exemplo [aqui](https://github.com/sgreenmsft/buildcontextsample).

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>O dimensionamento automático do pod horizontal não está funcionando em um espaço de desenvolvimento

Quando você executa um serviço em um espaço de desenvolvimento, o Pod do serviço é [injetado com contêineres adicionais para instrumentação](how-dev-spaces-works.md#prepare-your-aks-cluster) e todos os contêineres em um pod precisam ter limites de recursos e solicitações definidas para dimensionamento automático de Pod horizontal.

Para corrigir esse problema, aplique uma solicitação de recurso e limite aos contêineres de espaços de desenvolvimento injetados. As solicitações de recursos e os limites podem ser aplicados ao contêiner injetado (devspaces-proxy) adicionando a anotação de `azds.io/proxy-resources` à sua especificação de Pod. O valor deve ser definido como um objeto JSON que representa a seção de recursos da especificação do contêiner para o proxy.

Abaixo está um exemplo de uma anotação de recursos de proxy que deve ser aplicada à sua especificação de Pod.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Habilitar Azure Dev Spaces em um namespace existente com pods em execução

Você pode ter um cluster AKS existente e um namespace com pods em execução onde você deseja habilitar Azure Dev Spaces.

Para habilitar Azure Dev Spaces em um namespace existente em um cluster AKS, execute `use-dev-spaces` e use `kubectl` para reiniciar todos os pods nesse namespace.

```console
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
kubectl -n my-namespace delete pod --all
```

Depois que o pods for reiniciado, você poderá começar a usar seu namespace existente com Azure Dev Spaces.

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>Habilitar Azure Dev Spaces no cluster AKS com tráfego de saída restrito para nós de cluster

Para habilitar Azure Dev Spaces em um cluster AKS para o qual o tráfego de saída de nós de cluster é restrito, você precisará permitir os seguintes FQDNs:

| FQDN                                    | Porta      | Usar      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Para efetuar pull de imagens do Linux Alpine e outras Azure Dev Spaces |
| gcr.io | HTTP: 443 | Para efetuar pull de imagens do Helm/gaveta|
| storage.googleapis.com | HTTP: 443 | Para efetuar pull de imagens do Helm/gaveta|
| azds-<guid>.<location>. azds.io | HTTPS:443 | Para se comunicar com os serviços de back-end Azure Dev Spaces para seu controlador. O FQDN exato pode ser encontrado em "dataplaneFqdn" em% USERPROFILE%\.azds\settings.JSON|