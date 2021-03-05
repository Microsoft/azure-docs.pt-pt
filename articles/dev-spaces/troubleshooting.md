---
title: Resolução de problemas
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: Aprenda a resolver problemas e resolva problemas comuns ao ativar e utilizar espaços Azure Dev
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s '
ms.openlocfilehash: 14ced0c66b42b6f18c946d0c75091be1af5598f5
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102197797"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Azure Dev Spaces resolução de problemas

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Este guia contém informações sobre problemas comuns que poderá ter ao utilizar espaços Azure Dev.

Se tiver algum problema ao utilizar o Azure Dev Spaces, crie um [problema no repositório Azure Dev Spaces GitHub](https://github.com/Azure/dev-spaces/issues).

## <a name="before-you-begin"></a>Antes de começar

Para resolver os problemas de forma mais eficaz, pode ajudar a criar registos mais detalhados para revisão.

Para o Visual Studio, deite a `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` variável ambiental para 1. Certifique-se de reiniciar o Visual Studio para que a variável ambiental entre em vigor. Uma vez ativados, os registos detalhados são escritos para o seu `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` diretório.

No CLI, pode obter mais informações durante a execução do comando utilizando o `--verbose` interruptor. Também pode navegar em registos mais detalhados `%TEMP%\Azure Dev Spaces` . Num Mac, o *diretório TEMP* pode ser encontrado correndo `echo $TMPDIR` a partir de uma janela do terminal. Num computador Linux, o *diretório TEMP* é normalmente `/tmp` . Além disso, verifique se a sessão de registo está ativada no seu [ficheiro de configuração Azure CLI](/cli/azure/azure-cli-configuration#cli-configuration-values-and-environment-variables).

Azure Dev Spaces também funciona melhor ao depurar uma única instância, ou pod. O `azds.yaml` ficheiro contém uma definição, *replicaCount,* que indica o número de cápsulas que kubernetes executa para o seu serviço. Se alterar a *réplicaCount* para configurar a sua aplicação para executar várias cápsulas para um determinado serviço, o depurador liga-se à primeira cápsula, quando listado alfabeticamente. O depurar liga-se a uma cápsula diferente quando a cápsula original recicla, possivelmente resultando em comportamento inesperado.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Questões comuns ao permitir espaços Azure Dev

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Erro "Falhou na criação do controlador Azure Dev Spaces"

Pode ver este erro quando algo corre mal com a criação do controlador. Se for um erro transitório, elimine e recrie o controlador para o corrigir.

Também pode tentar eliminar o controlador:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Utilize o CLI dos Espaços Azure Dev para eliminar um controlador. Não é possível apagar um controlador do Visual Studio. Também não é possível instalar o CLI Azure Dev Spaces na Azure Cloud Shell para que não possa eliminar um controlador da Azure Cloud Shell.

Se não tiver o CLI dos Espaços Azure Dev instalado, pode primeiro instalá-lo usando o seguinte comando e depois eliminar o seu controlador:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

### <a name="controller-create-failing-because-of-controller-name-length"></a>Controlador criar falha por causa do comprimento do nome do controlador

O nome de um controlador Azure Dev Spaces não pode ser superior a 31 caracteres. Se o nome do seu controlador exceder 31 caracteres quando ativar Dev Spaces num cluster AKS ou criar um controlador, receberá um erro. Por exemplo:

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

Para corrigir este problema, crie um controlador com um nome alternativo. Por exemplo:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Permitir que os Espaços Dev falhem quando as piscinas de nó do Windows são adicionadas a um cluster AKS

Atualmente, a Azure Dev Spaces destina-se a funcionar apenas em cápsulas e nós Linux. Quando tiver um cluster AKS com uma piscina de nó windows, deve certificar-se de que os pods Azure Dev Spaces estão apenas agendados nos nós Linux. Se um pod Azure Dev Spaces estiver programado para ser executado num nó Windows, esse pod não arrancará e permitirá que o Dev Spaces falhe.

Para corrigir este problema, [adicione uma mancha](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) ao seu cluster AKS para garantir que as cápsulas Linux não estão programadas para funcionar num nó Windows.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Erro "Não encontrei nós Linux não danificados em estado pronto no cluster. Tem de haver pelo menos um nó Linux não incluído em Estado pronto para implantar cápsulas no espaço de nomes de 'azds'.

A Azure Dev Spaces não conseguiu criar um controlador no seu cluster AKS porque não conseguia encontrar um nó imaculado num estado *pronto* para agendar cápsulas. Azure Dev Spaces requer pelo menos um nó Linux num estado *Ready* que permite agendar cápsulas sem especificar tolerações.

Para corrigir este problema, [atualize a sua configuração de mancha](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) no seu cluster AKS para garantir que pelo menos um nó Linux permite agendar cápsulas sem especificar tolerações. Além disso, certifique-se de que pelo menos um nó Linux que permite agendar cápsulas sem especificar tolerações está no estado *Ready.* Se o seu nó estiver a demorar muito tempo a chegar ao estado *Ready,* pode tentar reiniciar o seu nó.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Erro "Azure Dev Spaces CLI não instalado corretamente" ao executar az aks use-dev-spaces

Uma atualização ao Azure Dev Spaces CLI alterou o seu caminho de instalação. Se estiver a utilizar uma versão do CLI Azure antes de 2.0.63, poderá ver este erro. Para exibir a sua versão do Azure CLI, utilize `az --version` .

```azurecli
az --version
```

```output
azure-cli                         2.0.60 *
...
```

Apesar da mensagem de erro ao executar `az aks use-dev-spaces` com uma versão do Azure CLI antes de 2.0.63, a instalação tem sucesso. Pode continuar a usar `azds` sem problemas.

Para corrigir este problema, atualize a sua instalação do [CLI Azure](/cli/azure/install-azure-cli) para 2.0.63 ou mais tarde. Esta atualização resolverá a mensagem de erro que recebe ao executar `az aks use-dev-spaces` . Em alternativa, pode continuar a utilizar a sua versão atual do Azure CLI e do Azure Dev Spaces CLI.

### <a name="error-unable-to-reach-kube-apiserver"></a>Erro "Incapaz de alcançar kube-apiserver"

Pode ver este erro quando o Azure Dev Spaces não conseguir ligar-se ao servidor API do cluster AKS.

Se o acesso ao seu servidor API do cluster AKS estiver bloqueado ou tiver [gamas de endereços IP autorizados](../aks/api-server-authorized-ip-ranges.md) pelo servidor API ativadas para o seu cluster AKS, também deve [criar](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) ou [atualizar](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) o seu cluster para [permitir gamas adicionais baseadas na sua região](configure-networking.md#aks-cluster-network-requirements)

Certifique-se de que o servidor API está disponível executando comandos kubectl. Se o servidor API não estiver disponível, contacte o suporte AKS e tente novamente quando o servidor API estiver a funcionar.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Questões comuns ao preparar o seu projeto para a Azure Dev Spaces

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Aviso "Dockerfile não podia ser gerado devido a linguagem não apoiada"
A Azure Dev Spaces fornece suporte nativo para C# e Node.js. Quando corre `azds prep` num diretório com código escrito numa destas línguas, a Azure Dev Spaces cria automaticamente um Dockerfile apropriado para si.

Ainda pode utilizar espaços Azure Dev com código escrito noutras línguas, mas precisa de criar manualmente o Dockerfile antes de correr `azds up` pela primeira vez.

Se a sua aplicação estiver escrita num idioma que a Azure Dev Spaces não suporta de forma nativa, tem de fornecer um Dockerfile apropriado para construir uma imagem de contentor que executa o seu código. Docker fornece uma [lista de boas práticas para escrever Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) e uma [referência Dockerfile](https://docs.docker.com/engine/reference/builder/) que pode ajudá-lo a escrever um Dockerfile que se adequa às suas necessidades.

Assim que tiver um Dockerfile apropriado no lugar, corre `azds up` para executar a sua aplicação em Azure Dev Spaces.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Questões comuns ao iniciar ou parar serviços com espaços Azure Dev

### <a name="error-config-file-not-found"></a>Erro "Ficheiro Config não encontrado:"

Ao `azds up` correr, poderá ver este erro. Ambos `azds up` e devem ser `azds prep` executados a partir do diretório de raiz do projeto que você quer executar no seu espaço dev.

Para resolver este problema:
1. Altere o seu diretório atual para a pasta raiz que contém o seu código de serviço. 
1. Se não tiver um ficheiro _azds.yaml_ na pasta de código, corra `azds prep` para gerar os ativos de Docker, Kubernetes e Azure Dev Spaces.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Timeout em "Waiting for container image build..." passo com os nódoas virtuais AKS

Este tempo limite ocorre quando tenta usar Dev Spaces para executar um serviço configurado para funcionar num [nó virtual AKS](../aks/virtual-nodes-portal.md). A Dev Spaces não suporta atualmente serviços de construção ou depuração em nós virtuais.

Se correr `azds up` com o interruptor ou ativar o registo `--verbose` verboso no Estúdio Visual, consulte detalhes adicionais:

```cmd
azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

O comando acima mostra que a cápsula do serviço foi atribuída a *virtual-nó-aci-linux*, que é um nó virtual.

Para corrigir este problema, atualize o gráfico Helm para o serviço para remover quaisquer valores *de nodeSelector* ou *tolerações* que permitam que o serviço seja executado num nó virtual. Estes valores são tipicamente definidos no ficheiro do `values.yaml` gráfico.

Ainda pode utilizar um cluster AKS que tenha a funcionalidade de nós virtuais ativada, se o serviço que pretende construir ou depurar através de Dev Spaces funciona num nó VM. Executar um serviço com Dev Spaces num nó VM é a configuração padrão.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Erro "não conseguiu encontrar uma cápsula de leme pronta" ao lançar Dev Spaces

Este erro ocorre se o cliente Helm já não puder falar com a cápsula Tiller em funcionamento no cluster.

Para corrigir este problema, reinicie os nós de agente no seu agrupamento.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Erro "release azds- \<identifier\> - \<spacename\> - \<servicename\> failed: services \<servicename\> ' already exist" ou "Pull access denied for \<servicename\> , repository não existe ou pode exigir 'login de estivador'"

Estes erros podem ocorrer se misturar comandos helm diretos (tais `helm install` `helm upgrade` como, ou `helm delete` ) com comandos Dev Spaces (tais como `azds up` e ) dentro do mesmo espaço `azds down` dev. Eles ocorrem porque Dev Spaces tem o seu próprio caso Tiller, que entra em conflito com o seu próprio caso Tiller correndo no mesmo espaço dev.

É bom usar os comandos Helm e Dev Spaces contra o mesmo cluster AKS, mas cada espaço de nome habilitado para dev Spaces deve usar um ou outro.

Por exemplo, suponha que use um comando Helm para executar toda a sua aplicação em um espaço de dev dos pais. Você pode criar espaços de dev infantil fora desse pai, usar Dev Spaces para executar serviços individuais dentro dos espaços dev infantil, e testar os serviços juntos. Quando estiver pronto para fazer o check-in das suas alterações, utilize um comando Helm para implementar o código atualizado para o espaço dev dos pais. Não utilize `azds up` executar o serviço atualizado no espaço dev dos pais, pois entrará em conflito com o serviço inicialmente executado usando o Helm.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Dockerfile existente não usado para construir um recipiente

A Azure Dev Spaces pode ser configurado para apontar para um _Dockerfile_ específico no seu projeto. Se parece que a Azure Dev Spaces não está a usar o _Dockerfile_ que esperas construir os teus contentores, talvez precises de dizer explicitamente à Azure Dev Spaces que o Dockerfile deve usar. 

Para corrigir este problema, abra o ficheiro _azds.yaml_ que a Azure Dev Spaces gerou no seu projeto. Configurações *de atualização: desenvolver: construir: estivar* para apontar para o Dockerfile que pretende utilizar. Por exemplo:

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Erro "não autorizado: autenticação necessária" ao tentar utilizar uma imagem do Docker a partir de um registo privado

Estás a usar uma imagem do Docker de um registo privado que requer autenticação.

Para corrigir este problema, pode permitir que a Dev Spaces autente e retire imagens deste registo privado utilizando [imagensPullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets). Para utilizar a imagem Suscrets, [crie um segredo de Kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) no espaço de nomes onde está a usar a imagem. Em seguida, forneça o segredo como uma imagemPullSecret em `azds.yaml` .

Abaixo está um exemplo de uma imagem especificadaPullSecrets em `azds.yaml` .

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
> Definição de imagem Os Arquivos Desacristos de `azds.yaml` Imagem irão sobrepor-se à imagemPullSecrets especificados no `values.yaml` .

### <a name="error-service-cannot-be-started"></a>Erro "Serviço não pode ser iniciado."

Pode ver este erro quando o seu código de serviço não for iniciado. A causa está frequentemente no código do utilizador. Para obter mais informações de diagnóstico, ative o registo mais detalhado ao iniciar o seu serviço.

A partir da linha de comando, utilize o `--verbose` para permitir a registo mais detalhado. Também pode especificar um formato de saída utilizando `--output` . Por exemplo:

```cmd
azds up --verbose --output json
```

No Estúdio Visual:

1. **Ferramentas Abertas > Opções** e em **Projetos e Soluções,** escolha **Construir e Executar.**
2. Altere as definições para **o projeto MSBuild construir verbosidade de saída** para **Detalhado** ou **Diagnóstico**.

    ![Screenshot do diálogo de opções de ferramentas](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Reexecução de um serviço após recriação do controlador

Recebe um *Serviço não pode ser iniciado* erro ao tentar refazer um serviço depois de ter removido e, em seguida, recriou o controlador Azure Dev Spaces associado a este cluster. Nesta situação, a saída verbosa contém o seguinte texto:

```output
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Este erro ocorre porque a remoção do controlador Dev Spaces não remove os serviços previamente instalados por esse controlador. Recriar o controlador e, em seguida, tentar executar os serviços utilizando o novo controlador falha porque os serviços antigos ainda estão no lugar.

Para resolver este problema, utilize o `kubectl delete` comando para remover manualmente os serviços antigos do seu cluster e, em seguida, repercuta os Espaços Dev para instalar os novos serviços.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Erro "Serviço não pode ser iniciado." ao usar estivadores de vários estágios

Recebe um *Serviço não pode ser iniciado* erro ao utilizar um Dockerfile em várias fases. Nesta situação, a saída verbosa contém o seguinte texto:

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

Ao utilizar [o Azure Dev Spaces para ligar o seu cluster AKS à sua máquina de desenvolvimento,](https://code.visualstudio.com/docs/containers/bridge-to-kubernetes)poderá encontrar um problema em que o tráfego de rede não seja reencaminhado entre a sua máquina de desenvolvimento e o seu cluster AKS.

Ao ligar a sua máquina de desenvolvimento ao seu cluster AKS, a Azure Dev Spaces encaminha o tráfego de rede entre o seu cluster AKS e a sua máquina de desenvolvimento modificando o ficheiro da sua máquina de `hosts` desenvolvimento. A Azure Dev Spaces cria uma entrada no `hosts` endereço do serviço Kubernetes que está a substituir como nome de anfitrião. Esta entrada é utilizada com o encaminhamento da porta para o tráfego direto da rede entre a sua máquina de desenvolvimento e o cluster AKS. Se um serviço na sua máquina de desenvolvimento entrar em conflito com a porta do serviço Kubernetes que está a substituir, a Azure Dev Spaces não pode encaminhar o tráfego de rede para o serviço Kubernetes. Por exemplo, o serviço *Windows BranchCache* está normalmente ligado a *0.0.0.0.0:80*, que os conflitos causarão um conflito para o porto 80 em todos os IPs locais.

Para corrigir este problema, tem de parar quaisquer serviços ou processos que contraíssem o porto do serviço Kubernetes que está a tentar substituir. Pode utilizar ferramentas, como *o netstat,* para inspecionar que serviços ou processos na sua máquina de desenvolvimento estão em conflito.

Por exemplo, para parar e desativar o serviço *Windows BranchCache:*
* Corra `services.msc` a partir do pedido de comando.
* Clique à direita no *BranchCache* e selecione *Propriedades.*
* Clique *em Paragem*.
* Opcionalmente, pode desativá-lo definindo *o tipo de Arranque* para *Desativado*.
* Clique em *OK*.

### <a name="error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state"></a>Erro "nenhuma AzureAssignedIdin encontrada para pod:azds/azds-webhook-deployment- \<id\> in assigned state"

Ao executar um serviço com Azure Dev Spaces num cluster AKS com identidade [gerida e](../aks/use-managed-identity.md) [identidades geridas por pod instaladas,](../aks/developer-best-practices-pod-security.md#use-pod-managed-identities) o processo pode parar de responder após o passo *de instalação* do gráfico. Se inspecionar o *azds-injector-webhook* no espaço do nome dos *azds,* poderá ver este erro.

Os serviços que a Azure Dev Spaces funciona no seu cluster utilizam a identidade gerida do cluster para falar com os serviços de backend Azure Dev Spaces fora do cluster. Quando a identidade gerida do pod é instalada, as regras de networking são configuradas nos nós do seu cluster para redirecionar todas as chamadas para credenciais de identidade geridas para um [DaemonSet de Identidade Gerida no Nó (NMI) instalado no cluster](https://github.com/Azure/aad-pod-identity#node-managed-identity). Este NMI DaemonSet identifica a cápsula de chamada e garante que o casulo foi rotulado adequadamente para aceder à identidade gerida solicitada. O Azure Dev Spaces não consegue detetar se um cluster tem identidade gerida por pods instalada e não consegue executar a configuração necessária para permitir que os serviços do Azure Dev Spaces acedam à identidade gerida do cluster. Uma vez que os serviços Azure Dev Spaces não foram configurados para aceder à identidade gerida do cluster, o NMI DaemonSet não lhes permitirá obter um token AD Azure para a identidade gerida e não comunicar com os serviços de backend da Azure Dev Spaces.

Para corrigir este problema, aplique uma [AzurePodIdentityExcepção](https://azure.github.io/aad-pod-identity/docs/configure/application_exception) para o *azds-injector-webhook* e cápsulas de atualização instrumentadas pela Azure Dev Spaces para aceder à identidade gerida.

Crie um ficheiro chamado *webhookException.yaml* e copie a seguinte definição YAML:

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

O ficheiro acima cria um objeto *AzurePodIdentityExcepção* para o *azds-injector-webhook*. Para implantar este objeto, `kubectl` utilize:

```cmd
kubectl apply -f webhookException.yaml
```

Para atualizar os pods instrumentados pela Azure Dev Spaces para aceder à identidade gerida, atualize o espaço de *nomes* na definição yaML abaixo e `kubectl` use-o para aplicá-lo para cada espaço dev.

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

Em alternativa, pode criar objetos *AzureIdentity* e *AzureIdentityBining* e atualizar as etiquetas do pod para cargas de trabalho em espaços instrumentados pela Azure Dev Spaces para aceder à identidade gerida criada pelo cluster AKS.

Para listar os detalhes da identidade gerida, execute o seguinte comando para o seu cluster AKS:

```azurecli
az aks show -g <resourcegroup> -n <cluster> -o json --query "{clientId: identityProfile.kubeletidentity.clientId, resourceId: identityProfile.kubeletidentity.resourceId}"
```

O comando acima liberta o *clienteId* e *recursosId* para a identidade gerida. Por exemplo:

```json
{
  "clientId": "<clientId>",
  "resourceId": "/subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>"
}
```

Para criar um objeto *AzureIdentity,* crie um ficheiro denominado *clusteridentity.yaml* e use a seguinte definição YAML atualizada com os detalhes da sua identidade gerida a partir do comando anterior:

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

Para criar um objeto *AzureIdentityBining,* crie um ficheiro denominado *clusteridentitybinding.yaml* e use a seguinte definição YAML:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: my-cluster-mi-binding
spec:
  AzureIdentity: my-cluster-mi
  Selector: my-label-value
```

Para implantar os objetos *AzureIdentity* e *AzureIdentityBinding,* `kubectl` utilize:

```cmd
kubectl apply -f clusteridentity.yaml
kubectl apply -f clusteridentitybinding.yaml
```

Depois de implantar os objetos *AzureIdentity* e *AzureIdentityBinding,* qualquer carga de trabalho com a *aadpodidbining: a* etiqueta de valor do meu rótulo pode aceder à identidade gerida do cluster. Adicione esta etiqueta e reimplante todas as cargas de trabalho em qualquer espaço dev. Por exemplo:

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

### <a name="error-cannot-get-connection-details-for-azure-dev-spaces-controller-abc-because-it-is-in-the-failed-state-something-wrong-might-have-happened-with-your-controller"></a>Erro "Não é possível obter detalhes de ligação para o Controlador de Espaços Azure Dev 'ABC' porque está no estado 'Falhado'. Algo de errado pode ter acontecido com o seu controlador.

Para resolver este problema, tente eliminar o controlador Azure Dev Spaces do cluster e reinstalá-lo:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Além disso, como a Azure Dev Spaces está a ser reformada, por favor considere [migrar para Bridge para Kubernetes,](migrate-to-bridge-to-kubernetes.md) o que proporciona uma melhor experiência.

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Questões comuns usando Visual Studio e Visual Studio Code com Azure Dev Spaces

### <a name="error-required-tools-and-configurations-are-missing"></a>Erro "Faltam ferramentas e configurações necessárias"

Este erro pode ocorrer ao lançar o Código VS: "[Azure Dev Spaces] Faltam ferramentas e configurações necessárias para construir e depurar '[nome do projeto].".
O erro significa que azds.exe não está na variável ambiente PATH, como se vê no Código VS.

Tente lançar o Código VS a partir de uma indicação de comando onde a variável ambiente PATH está corretamente definida.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Erro "As ferramentas necessárias para construir e depurar o 'nome do projecto' estão desatualizadas."

Você vê este erro no Código do Estúdio Visual se tiver uma versão mais recente da extensão do Código VS para Azure Dev Spaces, mas uma versão mais antiga do Azure Dev Spaces CLI.

Tente descarregar e instalar a versão mais recente do Azure Dev Spaces CLI:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Erro: "Não consegui encontrar extensão de depurador para o tipo:coreclr"

Pode ver este erro ao executar o depurar código de estúdio visual. Pode não ter a extensão do Código VS para C# instalada na sua máquina de desenvolvimento. A extensão C# inclui suporte de depurar para .NET Core (CoreCLR).

Para corrigir este problema, instale a [extensão do Código VS para C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Erro "O tipo de depurador configurado 'coreclr' não é suportado"

Pode ver este erro ao executar o depurar código de estúdio visual. Pode não ter a extensão do Código VS para espaços Azure Dev instalados na sua máquina de desenvolvimento.

Para corrigir este problema, instale a extensão do Código VS para espaços Azure Dev.

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Erro "Valor 'cwd' inválido '/src'. O sistema não consegue encontrar o ficheiro especificado." ou "lançamento: programa '/src/[caminho para projetar binário]' não existe"

Pode ver este erro ao executar o depurar código de estúdio visual. Por predefinição, a extensão do Código VS utiliza `src` como diretório de trabalho para o projeto no contentor. Se tiver atualizado o seu `Dockerfile` para especificar um diretório de trabalho diferente, poderá ver este erro.

Para corrigir este problema, atualize o `launch.json` ficheiro sob a `.vscode` subdiretória da sua pasta de projeto. Altere a `configurations->cwd` diretiva para apontar para o mesmo diretório definido no do seu projeto `WORKDIR` `Dockerfile` . Também poderá ser necessário atualizar a `configurations->program` diretiva.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Erro "O programa de tubos 'azds' saiu inesperadamente com o código 126."

Pode ver este erro ao executar o depurar código de estúdio visual.

Para corrigir este problema, feche e reabra o Código do Estúdio Visual. Reinicie o depurador.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Erro "Relógio interno falhou: assista ENOSPC" ao anexar a depuragem a uma aplicação Node.js

Este erro ocorre quando o nó que executa o pod com a aplicação Node.js a que está a tentar anexar com um depurador excedeu o valor *fs.inotify.max_user_watches.* Em alguns casos, [o valor predefinido de *fs.inotify.max_user_watches* pode ser demasiado pequeno para manusear a fixação de um depurador diretamente a uma cápsula](https://github.com/Azure/AKS/issues/772).

Uma solução temporária para esta emissão é aumentar o valor de *fs.inotify.max_user_watches* em cada nó no cluster e reiniciar esse nó para que as alterações produzam efeitos.

## <a name="other-common-issues"></a>Outros problemas comuns

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Erro "azds" não é reconhecido como um comando interno ou externo, programa operável ou ficheiro de lote

Este erro pode ocorrer se `azds.exe` não estiver instalado ou configurado corretamente.

Para resolver este problema:

1. Verifique a localização %ProgramFiles%/Microsoft SDKs\Azure\Azure\Azure Dev Spaces CLI para `azds.exe` . Se estiver lá, adicione esta localização à variável ambiente PATH.
2. Se `azds.exe` não estiver instalado, executar o seguinte comando:

    ```azurecli
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>Erro de autorização "Microsoft.DevSpaces/register/action"

Precisa de acesso *ao Proprietário* ou *Colaborador* na sua assinatura Azure para gerir os Espaços Azure Dev. Se estiver a tentar gerir a Dev Spaces e não tiver acesso *ao Proprietário* ou *Colaborador* à subscrição Azure associada, poderá ver um erro de autorização. Por exemplo:

```output
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

Para corrigir este problema, utilizando uma conta com o Acesso *do Proprietário* ou *contribuinte* à subscrição do Azure, registe manualmente o espaço `Microsoft.DevSpaces` de nome:

```azurecli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>Novas cápsulas não estão a começar

O inicializador Kubernetes não pode aplicar o PodSpec para novas cápsulas devido às alterações da permissão do RBAC de Kubernetes no *cluster-admin.* O novo pod também pode ter um PodSpec inválido, por exemplo, a conta de serviço associada à cápsula já não existe. Para ver as cápsulas que estão em estado *pendente* devido à questão do inicializador, utilize o `kubectl get pods` comando:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Este problema pode impactar cápsulas em *todos os espaços de nomes* do cluster, incluindo espaços de nome onde a Azure Dev Spaces não está ativada.

Para corrigir este problema, [atualize o CLI de Dev Spaces para a versão mais recente](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) e, em seguida, elimine a configuração inicial de *azds* do controlador Azure Dev Spaces:

```azurecli
az aks get-credentials --resource-group <resource group name> --name <cluster name>
```

```bash
kubectl delete InitializerConfiguration azds
```

Depois de ter removido a *configuração inicial dos azds* do controlador Azure Dev Spaces, utilize `kubectl delete` para remover quaisquer cápsulas num estado *pendente.* Depois de todas as cápsulas pendentes terem sido removidas, reposicione as suas cápsulas.

Se as novas cápsulas ainda estiverem presas num estado *pendente* após uma redistribuição, utilize `kubectl delete` para remover quaisquer cápsulas num estado *pendente.* Depois de todas as cápsulas pendentes terem sido removidas, elimine o controlador do cluster e reinstale-o:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Depois de o seu controlador ser reinstalado, recolocar as suas cápsulas.

### <a name="incorrect-azure-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Permissões incorretas do Azure RBAC para chamar controlador de Dev Spaces e APIs

O utilizador que acede ao controlador Azure Dev Spaces deve ter acesso para ler o *conjunto de administradores kubeconfig* no cluster AKS. Por exemplo, esta permissão está disponível no [Azure Kubernetes Service Cluster Admin Role](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). O utilizador que acede ao controlador Azure Dev Spaces também deve ter a função *de Contribuinte* ou *Proprietário* Azure para o controlador. Mais detalhes sobre a atualização das permissões de um utilizador para um cluster AKS estão disponíveis [aqui.](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group)

Para atualizar a função Azure do utilizador para o controlador:

1. Inicie sessão no portal do Azure em https://portal.azure.com.
1. Navegue para o Grupo de Recursos que contém o controlador, que normalmente é o mesmo que o seu cluster AKS.
1. Ativar a caixa de verificação *dos tipos escondidos Do Show.*
1. Clique no controlador.
1. Abra o painel *de controlo de acesso (IAM).*
1. Clique no *separador Atribuições de Funções.*
1. Clique *em Adicionar* e adicione a atribuição de *função*.
    * Para *Função*, selecione *Contribuinte* ou *Proprietário*.
    * Para *atribuir acesso a*, selecione *utilizador, grupo ou principal de serviço Azure.*
    * Para *obter o seguinte,* procure no utilizador que pretenda obter permissões.
1. Clique em *Guardar*.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Resolução de nomes DNS falha para um URL público associado a um serviço Dev Spaces

Pode configurar um ponto final de URL público para o seu serviço especificando o `--enable-ingress` interruptor para o `azds prep` comando, ou selecionando a `Publicly Accessible` caixa de verificação no Estúdio Visual. O nome DNS público é automaticamente registado quando executou o seu serviço em Dev Spaces. Se este nome DNS não estiver registado, vê que uma *Página não pode ser exibida* ou o Site não pode ser atingido *por* erro no seu navegador web ao ligar-se ao URL público.

Para resolver este problema:

* Verifique o estado de todos os URLs associados aos seus serviços Dev Spaces:

  ```console
  azds list-uris
  ```

* Se um URL estiver no estado *pendente,* a Dev Spaces ainda aguarda a conclusão do registo dns. Às vezes, leva alguns minutos para a inscrição ser concluída. Dev Spaces também abre um túnel local para cada serviço, que pode usar enquanto aguarda o registo dns.
* Se um URL permanecer no estado *pendente* por mais de 5 minutos, pode indicar um problema com o pod dNS externo que cria o ponto final público ou a cápsula controladora nginx ingress que adquire o ponto final público. Utilize os seguintes comandos para eliminar estas cápsulas e permitir que a AKS as recrie automaticamente:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Erro "erro de ligação a montante ou desconexão/reset antes dos cabeçalhos"

Pode ver este erro ao tentar aceder ao seu serviço. Por exemplo, quando você vai para o URL do serviço em um navegador. Este erro significa que a porta do contentor não está disponível. Isto pode, pelas seguintes razões:

* O contentor ainda está em fase de construção e implantação. Esta questão pode surgir se executar `azds up` ou iniciar o depurar e, em seguida, tentar aceder ao recipiente antes de ter sido implantado com sucesso.
* A configuração da porta não é consistente em todo o seu _Dockerfile,_ Helm Chart e qualquer código de servidor que abra uma porta.

Para resolver este problema:

1. Se o recipiente estiver em fase de construção/implantação, pode esperar 2-3 segundos e tentar aceder novamente ao serviço. 
1. Verifique a configuração da porta nos seguintes ativos:
    * **[Gráfico de leme](https://docs.helm.sh):** Especificado pelo `service.port` e `deployment.containerPort` em valores.yaml andaimes por `azds prep` comando.
    * Quaisquer portas abertas no código de aplicação, por exemplo em Node.js: `var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>O nome ou nome "MyLibrary" não foi encontrado

Um projeto de biblioteca que está a usar não pode ser encontrado. Com a Dev Spaces, o contexto de construção encontra-se ao nível do projeto/serviço por padrão.  

Para resolver este problema:

1. Modifique o `azds.yaml` ficheiro para definir o contexto de construção ao nível da solução.
2. Modifique os `Dockerfile` ficheiros e `Dockerfile.develop` ficheiros para se referir aos ficheiros do projeto, por `.csproj` exemplo, corretamente em relação ao novo contexto de construção.
3. Adicione um `.dockerignore` no mesmo diretório que o `.sln` ficheiro.
4. Atualize as `.dockerignore` entradas adicionais conforme necessário.

Pode encontrar um exemplo [aqui.](https://github.com/sgreenmsft/buildcontextsample)

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>Autoscalagem de pod horizontal não funcionando em um espaço dev

Quando você executa um serviço em um espaço dev, a vagem desse serviço é [injetada com recipientes adicionais para instrumentação](how-dev-spaces-works-cluster-setup.md#prepare-your-aks-cluster) e todos os recipientes em uma vagem precisam ter limites de recursos e pedidos definidos para Autoscaling de Pod Horizontal.

Para corrigir este problema, aplique um pedido de recurso e limite aos recipientes Dev Spaces injetados. Os pedidos e limites de recursos podem ser aplicados para o recipiente injetado (devspaces-proxy) adicionando a `azds.io/proxy-resources` anotação à especificação do seu pod. O valor deve ser definido para um objeto JSON que representa a secção de recursos da especificação do recipiente para o representante.

Abaixo está um exemplo de uma anotação de recursos de procuração que deve ser aplicada à sua especificação de vagem.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Ativar os espaços Azure Dev num espaço de nome existente com cápsulas de corrida

Você pode ter um cluster AKS existente e espaço de nome com pods de corrida onde você deseja ativar Azure Dev Spaces.

Para ativar os espaços Azure Dev num espaço de nome existente num cluster AKS, corra `use-dev-spaces` e use para reiniciar todas as `kubectl` cápsulas nesse espaço de nome.

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
```

```console
kubectl -n my-namespace delete pod --all
```

Depois de as suas cápsulas terem sido reiniciadas, pode começar a utilizar o seu espaço de nome existente com espaços Azure Dev.

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>Ativar espaços Azure Dev em cluster AKS com tráfego de saída restrito para os nosdes de cluster

Para ativar os espaços Azure Dev num cluster AKS para o qual o tráfego de saída dos nosdes de cluster é restrito, você terá que permitir os seguintes FQDNs:

| FQDN                                    | Porta      | Utilização      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Para puxar o linux alpino e outras imagens do Azure Dev Spaces |
| gcr.io | HTTP:443 | Para puxar imagens de leme/leme|
| storage.googleapis.com | HTTP:443 | Para puxar imagens de leme/leme|

Atualize a sua configuração de firewall ou segurança para permitir o tráfego de rede de e para todos os serviços de infraestrutura de FQDNs e [Azure Dev Spaces](../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations)acima referidos .

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>Erro "Não foi possível encontrar o cluster \<cluster\> na \<subscriptionId\> subscrição"

Pode ver este erro se o seu ficheiro Kubeconfig estiver a direcionar um cluster ou subscrição diferente do que está a tentar utilizar com a ferramenta do lado do cliente Azure Dev Spaces. A ferramenta do cliente Azure Dev Spaces replica o comportamento do *kubectl,* que usa [um ou mais ficheiros kubeconfig](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/) para selecionar e comunicar com o cluster.

Para resolver este problema:

* Utilize `az aks use-dev-spaces -g <resource group name> -n <cluster name>` para atualizar o contexto atual. Este comando também permite espaços Azure Dev no seu cluster AKS se ainda não estiver ativado. Em alternativa, pode utilizar `kubectl config use-context <cluster name>` para atualizar o contexto atual.
* Utilize `az account show` para mostrar a subscrição atual do Azure que está a ser alvo e verifique se está correta. Pode alterar a subscrição que está a ser alvo utilizando `az account set` .

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>Erro utilizando dev Spaces após rotação de certificados AKS

Depois de [rodar os certificados no seu cluster AKS,](../aks/certificate-rotation.md)certas operações, tais como `azds space list` e `azds up` falharão. Também precisa de atualizar os certificados no seu controlador Azure Dev Spaces depois de rodar os certificados no seu cluster.

Para corrigir este problema, certifique-se de que o seu *kubeconfig* tem os certificados atualizados utilizando `az aks get-credentials` e depois executar o `azds controller refresh-credentials` comando. Por exemplo:

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
```

```console
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
