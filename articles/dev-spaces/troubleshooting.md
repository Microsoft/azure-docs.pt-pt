---
title: Resolução de problemas
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: Aprenda a resolver problemas comuns ao permitir e utilizar espaços Azure Dev
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s '
ms.openlocfilehash: 1242aa0e6c8255d778da55b0e574f3d12f61c381
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83872020"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Tiroteio em problemas em Azure Dev Spaces

Este guia contém informações sobre problemas comuns que pode ter ao utilizar espaços Azure Dev.

Se tiver algum problema ao utilizar espaços Azure Dev, crie um [problema no repositório Azure Dev Spaces GitHub](https://github.com/Azure/dev-spaces/issues).

## <a name="before-you-begin"></a>Antes de começar

Para resolver problemas de forma mais eficaz, pode ajudar a criar registos mais detalhados para revisão.

Para o Estúdio Visual, detete a `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` variável ambiente para 1. Certifique-se de reiniciar o Visual Studio para que a variável ambiental faça efeito. Uma vez ativados, os registos detalhados são escritos para o seu `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` diretório.

No CLI, pode obter mais informações durante a execução do comando utilizando o `--verbose` interruptor. Também pode procurar registos mais `%TEMP%\Azure Dev Spaces` detalhados. Num Mac, o diretório *TEMP* pode ser encontrado correndo `echo $TMPDIR` de uma janela terminal. Num computador Linux, o diretório *TEMP* é geralmente `/tmp` . Além disso, verifique se a exploração madeireira está ativada no ficheiro de [configuração DoClI Azure](/cli/azure/azure-cli-configuration?view=azure-cli-latest#cli-configuration-values-and-environment-variables).

A Azure Dev Spaces também funciona melhor quando depura uma única instância, ou pod. O `azds.yaml` ficheiro contém uma definição, *réplicaCount,* que indica o número de cápsulas que a Kubernetes executa para o seu serviço. Se alterar a *réplicaCount* para configurar a sua aplicação para executar várias cápsulas para um determinado serviço, o debugger liga-se à primeira cápsula, quando listada alfabeticamente. O debugger liga-se a uma vagem diferente quando a cápsula original recicla, possivelmente resultando em comportamento inesperado.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Questões comuns ao permitir espaços Azure Dev

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Erro "Falhou na criação do controlador Azure Dev Spaces"

Pode ver este erro quando algo corre mal com a criação do controlador. Se for um erro transitório, elimine e recrie o controlador para o corrigir.

Também pode tentar apagar o controlador:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Utilize o Azure Dev Spaces CLI para eliminar um controlador. Não é possível apagar um controlador do Estúdio Visual. Também não é possível instalar o Azure Dev Spaces CLI na Casca de Nuvem Azure para que não possa eliminar um controlador da Casca de Nuvem Azure.

Se não tiver instalado o CLI dos Espaços Azure Dev, pode primeiro instalá-lo utilizando o seguinte comando e, em seguida, eliminar o seu controlador:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

A recriação do controlador pode ser feita a partir do CLI ou do Estúdio Visual. Consulte o desenvolvimento da [equipa](quickstart-team-development.md) ou [desenvolva-se com](quickstart-netcore-visualstudio.md) quickstarts .NET Core, por exemplo.

### <a name="controller-create-failing-because-of-controller-name-length"></a>O controlador cria falhas devido ao comprimento do nome do controlador

O nome de um controlador azure Dev Spaces não pode ter mais de 31 caracteres. Se o nome do seu controlador exceder 31 caracteres quando ativa o Dev Spaces num cluster AKS ou criar um controlador, receberá um erro. Por exemplo:

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

Para corrigir este problema, crie um controlador com um nome alternativo. Por exemplo:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Permitir que os Espaços Dev falhem quando as piscinas de nó do Windows são adicionadas a um cluster AKS

Atualmente, a Azure Dev Spaces destina-se a funcionar apenas em cápsulas linux e nós. Quando tiver um cluster AKS com uma piscina de nó windows, deve garantir que as cápsulas Azure Dev Spaces estão apenas programadas nos nós linux. Se um casulo Azure Dev Spaces estiver programado para ser executado num nó windows, essa cápsula não começará e permitirá que os Espaços Dev falhem.

Para corrigir este problema, [adicione uma mancha](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) ao seu cluster AKS para garantir que as cápsulas Linux não estão programadas para funcionar num nó windows.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Erro "Não encontrei nenhum nó linux imaculado em estado pronto no cluster. Tem de haver pelo menos um nó Linux não contaminado em estado pronto para implantar cápsulas no espaço de nome 'azds'.

A Azure Dev Spaces não conseguiu criar um controlador no seu cluster AKS porque não conseguia encontrar um nó imaculado num estado *ready* para agendar cápsulas. A Azure Dev Spaces requer pelo menos um nó Linux em estado *Ready* que permite agendar cápsulas sem especificar tolerâncias.

Para corrigir este problema, [atualize a sua configuração de mancha](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) no seu cluster AKS para garantir que pelo menos um nó Linux permite agendar cápsulas sem especificar tolerâncias. Além disso, certifique-se de que pelo menos um nó Linux que permite agendar cápsulas sem especificar tolerações está no estado *Ready.* Se o seu nó demorar muito tempo a chegar ao estado *de Ready,* pode tentar reiniciar o seu nó.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Erro "Azure Dev Spaces CLI não instalado sem instalação corretamente" ao executar az aks use-dev-spaces

Uma atualização para o Azure Dev Spaces CLI alterou a sua trajetória de instalação. Se estiver a utilizar uma versão do Azure CLI antes de 2.0.63, poderá ver este erro. Para exibir a sua versão do Azure CLI, utilize `az --version` .

```azurecli
az --version
```

```output
azure-cli                         2.0.60 *
...
```

Apesar da mensagem de erro ao correr `az aks use-dev-spaces` com uma versão do Azure CLI antes de 2.0.63, a instalação tem sucesso. Pode continuar a usar `azds` sem problemas.

Para corrigir este problema, atualize a instalação do [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) para 2.0.63 ou posteriormente. Esta atualização irá resolver a mensagem de erro que recebe quando estiver a correr `az aks use-dev-spaces` . Em alternativa, pode continuar a utilizar a sua versão atual do Azure CLI e do Azure Dev Spaces CLI.

### <a name="error-unable-to-reach-kube-apiserver"></a>Erro "Incapaz de chegar ao kube-apiserver"

Pode ver este erro quando o Azure Dev Spaces não conseguir ligar-se ao servidor API do seu cluster AKS.

Se o acesso ao seu servidor API do cluster AKS estiver bloqueado ou se tiver intervalos de [endereçoIP autorizados](../aks/api-server-authorized-ip-ranges.md) para o seu cluster AKS, também deve [criar](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) ou [atualizar](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) o seu cluster para [permitir intervalos adicionais baseados na sua região](https://github.com/Azure/dev-spaces/tree/master/public-ips).

Certifique-se de que o servidor API está disponível executando comandos kubectl. Se o servidor API estiver indisponível, contacte o suporte AKS e tente novamente quando o servidor API estiver a funcionar.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Questões comuns ao preparar o seu projeto para espaços Azure Dev

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Aviso "Dockerfile não podia ser gerado devido a linguagem não suportada"
A Azure Dev Spaces oferece apoio nativo para C# e Node.js. Quando executa `azds prep` um diretório com código escrito numa destas línguas, o Azure Dev Spaces cria automaticamente um Dockerfile apropriado para si.

Ainda pode utilizar espaços Azure Dev com código escrito noutras línguas, mas precisa de criar manualmente o Dockerfile antes de ser utilizado `azds up` pela primeira vez.

Se a sua aplicação estiver escrita numa linguagem que a Azure Dev Spaces não suporta de forma nativa, precisa de fornecer um Dockerfile apropriado para construir uma imagem de contentor que execute o seu código. O Docker fornece uma [lista das melhores práticas para escrever Ficheiros Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) e uma [referência do Dockerfile](https://docs.docker.com/engine/reference/builder/) que pode ajudá-lo a escrever um Dockerfile que se adequa às suas necessidades.

Uma vez que tenha um Dockerfile apropriado no lugar, você corre `azds up` para executar a sua aplicação em Espaços Azure Dev.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Questões comuns ao iniciar ou parar serviços com espaços Azure Dev

### <a name="error-config-file-not-found"></a>Erro "Ficheiro Config não encontrado:"

Ao `azds up` correr, pode ver este erro. Ambos `azds up` devem `azds prep` ser executados a partir do diretório raiz do projeto que você quer executar no seu espaço de v.

Para resolver este problema:
1. Mude o seu diretório atual para a pasta raiz que contém o seu código de serviço. 
1. Se não tiver um ficheiro _azds.yaml_ na pasta de código, corra para gerar ativos de `azds prep` Docker, Kubernetes e Azure Dev Spaces.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Timeout no "Waiting for container image build..." passo com nódosos virtuais AKS

Este tempo de tempo ocorre quando se tenta usar a Dev Spaces para executar um serviço que está configurado para funcionar num [nó virtual AKS](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). A Tualmente, a Dev Spaces não suporta serviços de construção ou depuração em nódos virtuais.

Se correr com o interruptor ou ativar o `azds up` `--verbose` registo verbose no Estúdio Visual, verá detalhes adicionais:

```cmd
azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

O comando acima mostra que o casulo do serviço foi atribuído ao *virtual-node-aci-linux,* que é um nó virtual.

Para corrigir este problema, atualize o gráfico Helm para o serviço remover quaisquer valores *de nodeSelector* ou *tolerações* que permitam que o serviço seja executado num nó virtual. Estes valores são tipicamente definidos no ficheiro do `values.yaml` gráfico.

Ainda pode utilizar um cluster AKS que tem a funcionalidade de nós virtuais ativada, se o serviço que pretende construir ou depurar através de Dev Spaces corre num nó VM. Executar um serviço com Dev Spaces num nó VM é a configuração padrão.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Erro "não conseguiu encontrar uma cápsula de leme pronta" ao lançar Dev Spaces

Este erro ocorre se o cliente Helm já não puder falar com a cápsula Tiller que corre no cluster.

Para corrigir este problema, reinicie os nós do agente no seu cluster.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Erro "liberte o nome de serviço de identificação do nome de serviço falhado: o \< \> - \< nome de \> - \< \> serviço dos serviços \< ' já existe" ou "Puxe o acesso negado para o nome do serviço , o \> \< \> repositório não existe ou pode exigir 'login de estivador'"

Estes erros podem ocorrer se misturar comandos direct helm (tais `helm install` como, `helm upgrade` ou ) com `helm delete` comandos Dev Spaces (como `azds up` `azds down` e) dentro do mesmo espaço de dev. Ocorrem porque a Dev Spaces tem a sua própria instância Tiller, que entra em conflito com a sua própria instância Tiller que funciona no mesmo espaço de dev.

É bom usar comandos Helm e Dev Spaces contra o mesmo aglomerado AKS, mas cada espaço de nome habilitado para espaços de V deve usar um ou outro.

Por exemplo, suponha que use um comando Helm para executar toda a sua aplicação num espaço de dev dos pais. Você pode criar espaços de dev infantil fora desse progenitor, usar Dev Spaces para executar serviços individuais dentro dos espaços de dev infantil, e testar os serviços em conjunto. Quando estiver pronto para fazer o check-in das suas alterações, utilize um comando Helm para implementar o código atualizado para o espaço de dev dos pais. Não utilize `azds up` para executar o serviço atualizado no espaço de dev dos pais, porque entrará em conflito com o serviço inicialmente executado usando Helm.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Dockerfile existente não usado para construir um recipiente

Os Espaços Azure Dev podem ser configurados para apontar para um _Dockerfile_ específico no seu projeto. Se parece que a Azure Dev Spaces não está a usar o _Dockerfile_ que espera construir os seus contentores, poderá ter de dizer explicitamente aos Espaços Azure Dev que o Dockerfile deve utilizar. 

Para corrigir este problema, abra o ficheiro _azds.yaml_ que a Azure Dev Spaces gerou no seu projeto. Configurações *de atualização: desenvolver: construir: dockerfile* para apontar para o Dockerfile que pretende utilizar. Por exemplo:

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Erro "não autorizado: autenticação necessária" ao tentar utilizar uma imagem do Docker a partir de um registo privado

Estás a usar uma imagem do Docker de um registo privado que requer autenticação.

Para corrigir este problema, pode permitir que a Dev Spaces autenticae e retire imagens deste registo privado utilizando [imagensPullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets). Para utilizar a imagemPullSecrets, [crie um segredo Kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) no espaço de nome onde está a usar a imagem. Em seguida, forneça o segredo como uma imagemPullSecret em `azds.yaml` .

Abaixo está um exemplo de uma imagem especificandoPullSecrets em `azds.yaml` .

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
> A definição de imagemPullSecrets em irá sobrepor a `azds.yaml` imagemPullSecrets especificada na `values.yaml` .

### <a name="error-service-cannot-be-started"></a>Erro "O serviço não pode ser iniciado."

Pode ver este erro quando o seu código de serviço não começar. A causa está frequentemente no código do utilizador. Para obter mais informações de diagnóstico, ative a exploração madeireira mais detalhada ao iniciar o seu serviço.

A partir da linha de comando, utilize o para permitir uma `--verbose` exploração mais detalhada. Também pode especificar um formato de saída utilizando `--output` . Por exemplo:

```cmd
azds up --verbose --output json
```

No Estúdio Visual:

1. **Ferramentas abertas > Opções** e em **Projetos e Soluções,** escolha **Construir e Executar.**
2. Altere as definições para **a verbosidade** de produção de construção de projeto MSBuild para **Detalhado** ou **Diagnóstico**.

    ![Screenshot de ferramentas de diálogo de opções](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Reexecutar um serviço após a recriação do controlador

Recebe um *Serviço não pode ser iniciado* erro ao tentar reexecutar um serviço depois de ter removido e recriado o controlador Azure Dev Spaces associado a este cluster. Nesta situação, a saída verbosa contém o seguinte texto:

```output
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Este erro ocorre porque a remoção do controlador Dev Spaces não remove os serviços previamente instalados por esse controlador. Recriar o controlador e, em seguida, tentar executar os serviços usando o novo controlador falha porque os serviços antigos ainda estão em vigor.

Para resolver este problema, utilize o `kubectl delete` comando para remover manualmente os antigos serviços do seu cluster e, em seguida, reexecutar a Dev Spaces para instalar os novos serviços.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Erro "O serviço não pode ser iniciado." ao utilizar ficheiros Dockerfiles em várias fases

O Serviço *não pode ser iniciado* quando se utiliza um Dockerfile em várias fases. Nesta situação, a saída verbosa contém o seguinte texto:

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

Este erro ocorre porque a Azure Dev Spaces não suporta atualmente construções em várias fases. Para evitar construções em várias fases, reescreva o seu Dockerfile.

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>O tráfego de rede não é encaminhado para o seu cluster AKS ao ligar a sua máquina de desenvolvimento

Ao utilizar o [Azure Dev Spaces para ligar o seu cluster AKS à sua máquina](how-to/connect.md)de desenvolvimento, poderá encontrar um problema em que o tráfego de rede não seja encaminhado entre a sua máquina de desenvolvimento e o seu cluster AKS.

Ao ligar a sua máquina de desenvolvimento ao seu cluster AKS, a Azure Dev Spaces reencaminha o tráfego de rede entre o seu cluster AKS e a sua máquina de desenvolvimento, modificando o ficheiro da sua máquina de `hosts` desenvolvimento. A Azure Dev Spaces cria uma entrada no `hosts` endereço do serviço Kubernetes que está a substituir como nome de anfitrião. Esta entrada é utilizada com o encaminhamento da rede direta para o tráfego de rede direto entre a sua máquina de desenvolvimento e o cluster AKS. Se um serviço na sua máquina de desenvolvimento entrar em conflito com o porto do serviço Kubernetes que está a substituir, o Azure Dev Spaces não pode encaminhar o tráfego de rede para o serviço Kubernetes. Por exemplo, o serviço *Windows BranchCache* está normalmente ligado a *0.0.0.0:80*, o que os conflitos causarão um conflito para a porta 80 em todos os IPs locais.

Para corrigir este problema, é necessário parar quaisquer serviços ou processos que entrem em conflito com o serviço do Porto do Serviço Kubernetes que está a tentar substituir. Pode utilizar ferramentas, como *netstat,* para inspecionar quais os serviços ou processos da sua máquina de desenvolvimento em conflito.

Por exemplo, parar e desativar o serviço *Windows BranchCache:*
* Corra `services.msc` a partir do pedido de comando.
* Clique à direita no *BranchCache* e selecione *Propriedades*.
* Clique em *Parar*.
* Opcionalmente, pode desativá-lo definindo o *tipo de Arranque* para *Desativado*.
* Clique em *OK*.

### <a name="error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state"></a>Erro "no AzureAssignedIdentity encontrado para pod:azds/azds-webhook-deployment-id \< \> em estado designado"

Ao executar um serviço com a Azure Dev Spaces num cluster AKS com uma [identidade gerida](../aks/use-managed-identity.md) e identidades geridas por pod sabotadas, o processo pode ser suspenso após o passo de instalação do *gráfico.* [pod managed identities](../aks/developer-best-practices-pod-security.md#use-pod-managed-identities) Se inspecionar o *webhook azds-injector* no espaço do nome *azds,* poderá ver este erro.

Os serviços que a Azure Dev Spaces executa no seu cluster utilizam a identidade gerida pelo cluster para falar com os serviços de backend da Azure Dev Spaces fora do cluster. Quando a identidade gerida pelo pod é instalada, as regras de rede são configuradas nos nós do seu cluster para redirecionar todas as chamadas para credenciais de identidade geridas para um [DaemonSet de Identidade Gerida de Nó (NMI) instalado no cluster](https://github.com/Azure/aad-pod-identity#node-managed-identity). Este NMI DaemonSet identifica o casulo de chamada e garante que a cápsula foi devidamente rotulada para aceder à identidade gerida solicitada. A Azure Dev Spaces não consegue detetar se um cluster tem uma identidade gerida por pods instalada e não consegue realizar a configuração necessária para permitir aos serviços da Azure Dev Spaces aceder à identidade gerida do cluster. Uma vez que os serviços da Azure Dev Spaces não foram configurados para aceder à identidade gerida do cluster, o NMI DaemonSet não lhes permitirá obter um símbolo AAD para a identidade gerida e não comunicar com os serviços de backend da Azure Dev Spaces.

Para corrigir este problema, aplique uma Exceção de [Identidade AzurePod](https://github.com/Azure/aad-pod-identity/blob/master/docs/readmes/README.app-exception.md) para os *azds-injector-webhook* e cápsulas de atualização instrumentadas pela Azure Dev Spaces para aceder à identidade gerida.

Crie um ficheiro chamado *webhookException.yaml* e copie a seguinte definição yAML:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: azds
spec:
  PodLabels:
    azds.io/uses-cluster-identity: "true"
```

O ficheiro acima cria um objeto *De exceção AzurePodIdentityException* para o *azds-injector-webhook*. Para implantar este objeto, `kubectl` utilize:

```cmd
kubectl apply -f webhookException.yaml
```

Para atualizar as cápsulas instrumentadas pela Azure Dev Spaces para aceder à identidade gerida, atualize o espaço de *nome* na definição yAML abaixo e utilize `kubectl` para aplicá-lo para cada espaço de dev.

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: myNamespace
spec:
  PodLabels:
    azds.io/instrumented: "true"
```

Em alternativa, pode criar objetos *AzureIdentity* e *AzureIdentityBinding* e atualizar as etiquetas de pod para cargas de trabalho em espaços instrumentados pela Azure Dev Spaces para aceder à identidade gerida criada pelo cluster AKS.

Para listar os detalhes da identidade gerida, execute o seguinte comando para o seu cluster AKS:

```azurecli
az aks show -g <resourcegroup> -n <cluster> -o json --query "{clientId: identityProfile.kubeletidentity.clientId, resourceId: identityProfile.kubeletidentity.resourceId}"
```

As saídas de comando acima são o *clienteId* e *recursoId* para a identidade gerida. Por exemplo:

```json
{
  "clientId": "<clientId>",
  "resourceId": "/subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>"
}
```

Para criar um objeto *AzureIdentity, crie* um ficheiro chamado *clusteridentity.yaml* e utilize a seguinte definição YAML atualizada com os detalhes da sua identidade gerida a partir do comando anterior:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentity
metadata:
  name: my-cluster-mi
spec:
  type: 0
  ResourceID: /subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>
  ClientID: <clientId>
```

Para criar um objeto *AzureIdentityBinding, crie* um ficheiro chamado *clusteridentitybinding.yaml* e utilize a seguinte definição YAML:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: my-cluster-mi-binding
spec:
  AzureIdentity: my-cluster-mi
  Selector: my-label-value
```

Para implantar os *objetos AzureIdentity* e *AzureIdentityBinding,* `kubectl` utilize:

```cmd
kubectl apply -f clusteridentity.yaml
kubectl apply -f clusteridentitybinding.yaml
```

Depois de implementar os *objetos AzureIdentity* e *AzureIdentityBinding,* qualquer carga de trabalho com a *aadpodidbinding:* a etiqueta de valor do meu rótulo pode aceder à identidade gerida do cluster. Adicione esta etiqueta e reimplante todas as cargas de trabalho em execução em qualquer espaço de dev. Por exemplo:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: sample
        aadpodidbinding: my-label-value
    spec:
      [...]
```

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Questões comuns usando visual studio e código de estúdio visual com espaços Azure Dev

### <a name="error-required-tools-and-configurations-are-missing"></a>Erro "Faltam ferramentas e configurações necessárias"

Este erro pode ocorrer no lançamento do Código VS: "[Os Espaços Azure Dev] são necessárias ferramentas e configurações para construir e depurar '[nome do projeto]' estão em falta."
O erro significa que azds.exe não está na variável ambiente PATH, como visto no Código VS.

Tente lançar o Código VS a partir de um pedido de comando onde a variável ambiente PATH é definida corretamente.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Erro "As ferramentas necessárias para construir e depurar o 'nome de projeto' estão desatualizadas."

Você vê este erro no Código do Estúdio Visual se tiver uma versão mais recente da extensão do Código VS para Espaços Azure Dev, mas uma versão mais antiga do Azure Dev Spaces CLI.

Tente descarregar e instalar a versão mais recente do Azure Dev Spaces CLI:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Erro: "Falhou em encontrar extensão de debugger para o tipo:coreclr"

Pode ver este erro ao executar o debugger do Código do Estúdio Visual. Pode não ter a extensão do Código VS para C# instalada na sua máquina de desenvolvimento. A extensão C# inclui suporte de depuração para .NET Core (CoreCLR).

Para corrigir este problema, instale a extensão do [Código VS para C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Erro "Não é suportado o tipo de depuração configurado 'coreclr' "

Pode ver este erro ao executar o debugger do Código do Estúdio Visual. Pode não ter a extensão do Código VS para espaços Azure Dev instaladas na sua máquina de desenvolvimento.

Para corrigir este problema, instale a extensão do [Código VS para espaços Azure Dev](get-started-netcore.md).

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Erro "Valor 'cwd' inválido '/src'. O sistema não pode encontrar o ficheiro especificado." ou "lançamento: programa '/src/[caminho para projeto binário]" não existe"

Pode ver este erro ao executar o debugger do Código do Estúdio Visual. Por predefinição, a extensão do Código VS utiliza como diretório de `src` trabalho para o projeto no recipiente. Se atualizou o seu para especificar um diretório de `Dockerfile` trabalho diferente, poderá ver este erro.

Para corrigir este problema, atualize o `launch.json` ficheiro sob o `.vscode` subdiretório da sua pasta de projeto. Altere a `configurations->cwd` diretiva para apontar para o mesmo diretório que o definido no do seu `WORKDIR` `Dockerfile` projeto. Também pode ter de atualizar a `configurations->program` diretiva.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Erro "O programa de tubos 'azds' saiu inesperadamente com o código 126."

Pode ver este erro ao executar o debugger do Código do Estúdio Visual.

Para corrigir este problema, feche e reabra o Código do Estúdio Visual. Reinicie o debugger.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Erro "Relógio interno falhou: assista ENOSPC" ao anexar depuração a uma aplicação Node.js

Este erro ocorre quando o nó que executa a cápsula com a aplicação Node.js a que está a tentar anexar com um debugger excedeu o valor *fs.inotify.max_user_watches.* Em alguns casos, [o valor padrão de *fs.inotify.max_user_watches* pode ser demasiado pequeno para manusear a fixação de um debugger diretamente a uma cápsula](https://github.com/Azure/AKS/issues/772).

Uma suposições temporárias para esta questão é aumentar o valor de *fs.inotify.max_user_watches* em cada nó do cluster e reiniciar o nó para que as alterações entrem em vigor.

## <a name="other-common-issues"></a>Outros problemas comuns

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Erro "azds" não é reconhecido como um comando interno ou externo, programa operável ou ficheiro de lote

Este erro pode acontecer se `azds.exe` não estiver instalado ou configurado corretamente.

Para resolver este problema:

1. Verifique a localização %ProgramFiles%/Microsoft SDKs\Azure\Azure\Azure Dev Spaces CLI para `azds.exe` . Se estiver lá, adicione essa localização à variável ambiental PATH.
2. Se `azds.exe` não estiver instalado, execute o seguinte comando:

    ```azurecli
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>Erro de autorização "Microsoft.DevSpaces/register/action"

Precisa de acesso *do Proprietário* ou *colaborador* na sua subscrição Azure para gerir os Espaços Azure Dev. Se estiver a tentar gerir a Dev Spaces e não tiver acesso ao *Proprietário* ou *Colaborador* à subscrição do Azure associada, poderá ver um erro de autorização. Por exemplo:

```output
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

Para corrigir este problema, utilizando uma conta com acesso ao *Proprietário* ou *Colaborador* à subscrição do Azure, registe manualmente o espaço de `Microsoft.DevSpaces` nome:

```azurecli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>Novas cápsulas não estão a começar

O inicializador Kubernetes não pode aplicar o PodSpec para novas cápsulas devido a alterações de permissão RBAC na função *cluster-admin* no cluster. O novo casulo também pode ter um PodSpec inválido, por exemplo, a conta de serviço associada à cápsula já não existe. Para ver as cápsulas que se encontram em estado *pendente* devido à questão do inicializador, utilize o `kubectl get pods` comando:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Esta edição pode ter impacto em cápsulas em *todos os espaços* de nome no cluster, incluindo espaços de nome onde o Azure Dev Spaces não está ativado.

Para corrigir este problema, [atualize o CLI dos Espaços Dev para a versão mais recente](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) e, em seguida, elimine a *Configuração inicializador azds* do controlador Dev Spaces:

```azurecli
az aks get-credentials --resource-group <resource group name> --name <cluster name>
```

```bash
kubectl delete InitializerConfiguration azds
```

Depois de ter removido a configuração do *inicializador azds* do controlador Azure Dev Spaces, utilize `kubectl delete` para remover quaisquer cápsulas num estado *pendente.* Depois de todas as cápsulas pendentes terem sido removidas, recoloque as suas cápsulas.

Se as novas cápsulas continuarem presas num estado *pendente* após uma recolocação, utilize `kubectl delete` para remover quaisquer cápsulas num estado *pendente.* Depois de todas as cápsulas pendentes terem sido removidas, elimine o controlador do cluster e reinstale-o:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Depois de o seu controlador ser reinstalado, recoloque as suas cápsulas.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Permissões RBAC incorretas para chamar controlador de Dev Spaces e APIs

O utilizador que aceda ao controlador Azure Dev Spaces deve ter acesso a ler o administrador *kubeconfig* no cluster AKS. Por exemplo, esta permissão está disponível no papel de administrador de cluster de [serviço azure Kubernetes incorporado.](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions) O utilizador que aceda ao controlador Azure Dev Spaces deve também ter a função *DeRBAC do Colaborador* ou *Proprietário* para o controlador. Mais detalhes sobre a atualização das permissões de um utilizador para um cluster AKS estão disponíveis [aqui](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Para atualizar a função RBAC do utilizador para o controlador:

1. Inicie sessão no portal do Azure em https://portal.azure.com.
1. Navegue para o Grupo de Recursos que contém o controlador, que é geralmente o mesmo que o seu cluster AKS.
1. Ativar a caixa de verificação *de tipos ocultos Show.*
1. Clique no controlador.
1. Abra o painel de Controlo de *Acesso (IAM).*
1. Clique no separador *Role Assignments.*
1. Clique em *Adicionar* e, em seguida, *adicionar a atribuição de funções*.
    * Para *função*, selecione *Colaborador* ou *Proprietário*.
    * Para *atribuir acesso a*, selecione utilizador, grupo ou diretor de serviço *Azure AD*.
    * Para *Selecionar,* procure o utilizador que pretende dar permissões.
1. Clique em *Guardar*.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Resolução de nome DNS falha para um URL público associado a um serviço Dev Spaces

Pode configurar um ponto final de URL público para o seu serviço, especificando o `--enable-ingress` interruptor para o `azds prep` comando, ou selecionando a caixa de `Publicly Accessible` verificação no Estúdio Visual. O nome DNS público é registado automaticamente quando executa o seu serviço em Espaços Dev. Se este nome DNS não estiver registado, verá que uma *Página não pode ser exibida* ou o Site não pode ser *atingido* erro no seu navegador na web quando se conecta ao URL público.

Para resolver este problema:

* Verifique o estado de todos os URLs associados aos seus serviços Dev Spaces:

  ```console
  azds list-uris
  ```

* Se um URL estiver no estado *pendente,* a Dev Spaces ainda aguarda que o registo do DNS esteja concluído. Às vezes, leva alguns minutos para a inscrição ser completada. A Dev Spaces também abre um túnel de acolhimento local para cada serviço, que pode utilizar enquanto aguarda o registo de DNS.
* Se um URL permanecer no estado *pendente* por mais de 5 minutos, pode indicar um problema com o casulo DNS externo que cria o ponto final público ou o casulo de comando de ingresso nginx que adquire o ponto final público. Utilize os seguintes comandos para eliminar estas cápsulas e permitir que o AKS as recrie automaticamente:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Erro "erro de ligação a montante ou desconexão/reset antes dos cabeçalhos"

Pode ver este erro ao tentar aceder ao seu serviço. Por exemplo, quando você vai ao URL do serviço em um navegador. Este erro significa que a porta do contentor não está disponível. Isto pode pelas seguintes razões:

* O contentor ainda está em fase de construção e implantação. Este problema pode surgir se executar `azds up` ou iniciar o desordeiro, e depois tentar aceder ao recipiente antes de ser implantado com sucesso.
* A configuração da porta não é consistente em todo o seu _Dockerfile,_ Helm Chart, e qualquer código de servidor que abra uma porta.

Para resolver este problema:

1. Se o recipiente estiver em processo de construção/implantação, pode esperar 2-3 segundos e tentar aceder novamente ao serviço. 
1. Verifique a configuração da sua porta nos seguintes ativos:
    * ** [Gráfico de leme:](https://docs.helm.sh)** Especificado pelo `service.port` e `deployment.containerPort` em valores.yaml andaimes por `azds prep` comando.
    * Quaisquer portas abertas no código de aplicação, por exemplo no Nó.js:`var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>O tipo ou nome do espaço de nome "MyLibrary" não foi encontrado

Um projeto da biblioteca que estás a usar não pode ser encontrado. Com a Dev Spaces, o contexto de construção está ao nível do projeto/serviço por padrão.  

Para resolver este problema:

1. Modifique o `azds.yaml` ficheiro para definir o contexto de construção para o nível de solução.
2. Modifique os `Dockerfile` ficheiros e `Dockerfile.develop` ficheiros para se referir aos ficheiros do projeto, por `.csproj` exemplo, corretamente em relação ao novo contexto de construção.
3. Adicione um `.dockerignore` no mesmo diretório que o `.sln` ficheiro.
4. Atualize o `.dockerignore` com entradas adicionais conforme necessário.

Pode encontrar um exemplo [aqui.](https://github.com/sgreenmsft/buildcontextsample)

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>Autoscalcificação horizontal de pod não funcionando em um espaço de v

Quando executa um serviço num espaço de v, a cápsula desse serviço é [injetada com recipientes adicionais para instrumentação](how-dev-spaces-works-cluster-setup.md#prepare-your-aks-cluster) e todos os recipientes de uma cápsula precisam de ter limites de recursos e pedidos definidos para a Autoscalização Horizontal Pod.

Para corrigir este problema, aplique um pedido de recurso e limite aos recipientes de Dev Spaces injetados. Os pedidos e limites de recursos podem ser aplicados para o recipiente injetado (devspaces-proxy) adicionando a `azds.io/proxy-resources` anotação à especificação do seu casulo. O valor deve ser fixado num objeto JSON que represente a secção de recursos da especificação do recipiente para o proxy.

Abaixo está um exemplo de uma anotação de recursos proxy que deve ser aplicada à especificação do seu casulo.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Habilitar os espaços Azure Dev num espaço de nome existente com cápsulas de corrida

Você pode ter um cluster AKS existente e espaço de nome com cápsulas de corrida onde você deseja ativar O Espaço Azure Dev.

Para ativar o Azure Dev Spaces num espaço de nome existente num cluster AKS, corra `use-dev-spaces` e use para reiniciar todas as `kubectl` cápsulas nesse espaço de nome.

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
```

```console
kubectl -n my-namespace delete pod --all
```

Depois de as suas cápsulas terem recomeçado, pode começar a usar o seu espaço de nome existente com o Azure Dev Spaces.

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>Enable Azure Dev Spaces no cluster AKS com tráfego de saída restrita para nós de cluster

Para permitir a Azure Dev Spaces num cluster AKS para o qual o tráfego de saída dos nós do cluster é restrito, terá de permitir seguir FQDNs:

| FQDN                                    | Porta      | Utilizar      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Para puxar imagens alpinas linux e outras imagens do Azure Dev Spaces |
| gcr.io | HTTP:443 | Para puxar imagens de leme/leme|
| storage.googleapis.com | HTTP:443 | Para puxar imagens de leme/leme|
| Azds. <guid> <location> . . azds.io | HTTPS:443 | Para comunicar com os serviços de backend da Azure Dev Spaces para o seu controlador. O FQDN exato pode ser encontrado no "dataplaneFqdn" em %USERPROFILE% \. azds\settings.json|

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>Erro "Não consegui encontrar o \< cluster \> na \< subscrição de subscriçãoId" \>

Pode ver este erro se o seu ficheiro kubeconfig estiver direcionado para um cluster ou subscrição diferente do que está a tentar utilizar com a ferramenta do lado do cliente do Azure Dev Spaces. A ferramenta do lado do cliente Azure Dev Spaces replica o comportamento do *kubectl*, que utiliza [um ou mais ficheiros kubeconfig](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/) para selecionar e comunicar com o cluster.

Para resolver este problema:

* Utilize `az aks use-dev-spaces -g <resource group name> -n <cluster name>` para atualizar o contexto atual. Este comando também permite a Azure Dev Spaces no seu cluster AKS se ainda não estiver ativado. Em alternativa, pode utilizar `kubectl config use-context <cluster name>` para atualizar o contexto atual.
* Utilize `az account show` para mostrar a subscrição atual do Azure que está a visar e verificar se está correta. Pode alterar a subscrição que está a ser alvo usando `az account set` .

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>Erro utilizando Espaços Dev após rotação de certificados AKS

Depois [de rodar os certificados no seu cluster AKS,](../aks/certificate-rotation.md)certas operações, tais como e `azds space list` `azds up` falharão. Também precisa de atualizar os certificados no seu controlador Azure Dev Spaces depois de rodar os certificados no seu cluster.

Para corrigir este problema, certifique-se de que o seu *kubeconfig* tem os certificados atualizados utilizando `az aks get-credentials` e, em seguida, executar o `azds controller refresh-credentials` comando. Por exemplo:

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
```

```console
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
