---
title: Resolução de problemas
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/11/2018
ms.topic: conceptual
description: Desenvolvimento rápido da Kubernetes com contentores e microsserviços no Azure
keywords: 'Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, K8S '
ms.openlocfilehash: b16a7d874f15747c14df1d728be824fac76de2be
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70993952"
---
# <a name="troubleshooting-guide"></a>Guia de resolução de problemas

Este guia contém informações sobre problemas comuns que poderá ter quando utilizar espaços de desenvolvimento do Azure.

Se você tiver um problema ao usar Azure Dev Spaces, crie um [problema no repositório GitHub Azure dev Spaces](https://github.com/Azure/dev-spaces/issues).

## <a name="enabling-detailed-logging"></a>Ativar o registo detalhado

Para solucionar problemas com mais eficiência, pode ajudar a criar logs mais detalhados para análise.

Para a extensão do Visual Studio, defina o `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` variável de ambiente para 1. Certifique-se de que reinicie o Visual Studio para a variável de ambiente para entrar em vigor. Uma vez habilitado, os logs detalhados são `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` gravados em seu diretório.

Na CLI, o utilizador pode apresentar mais informações durante a execução do comando utilizando o `--verbose` mudar. Também pode procurar registos mais detalhados no `%TEMP%\Azure Dev Spaces`. Num Mac, o diretório TEMP do diretório pode ser encontrado ao executar `echo $TMPDIR` de uma janela de terminal. Num computador Linux, o diretório TEMP do diretório é normalmente `/tmp`.

## <a name="debugging-services-with-multiple-instances"></a>Depuração de serviços com várias instâncias

Atualmente, Azure Dev Spaces funciona melhor ao depurar uma única instância ou Pod. O arquivo azds. YAML contém uma configuração, *replicaCount*, que indica o número de pods que o kubernetes executa para o serviço. Se você alterar o replicaCount para configurar seu aplicativo para executar vários pods para um determinado serviço, o depurador será anexado ao primeiro Pod, quando listado em ordem alfabética. O depurador é anexado a um pod diferente quando o Pod original é reciclado, possivelmente resultando em um comportamento inesperado.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Erro "Falha ao criar o controlador de espaços de desenvolvimento do Azure"

### <a name="reason"></a>Reason
Poderá ver este erro quando algo dá errado com a criação do controlador. Se for um erro transitório, exclua e recrie o controlador para corrigi-lo.

### <a name="try"></a>Experimente

Exclua o controlador:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Você deve usar a CLI do Azure Dev Spaces para excluir um controlador. Não é possível excluir um controlador do Visual Studio. Você também não pode instalar o Azure Dev Spaces CLI no Azure Cloud Shell para que não possa excluir um controlador do Azure Cloud Shell.

Se você não tiver a CLI do Azure Dev Spaces instalada, você poderá primeiro instalá-la usando o comando a seguir e excluir o controlador:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Recriar o controlador pode ser feito na CLI ou do Visual Studio. Consulte os guias de início rápido de [desenvolvimento da equipe](quickstart-team-development.md) ou [desenvolver com o .NET Core](quickstart-netcore-visualstudio.md) para obter exemplos.

## <a name="error-service-cannot-be-started"></a>Erro "serviço não pode ser iniciado."

Pode ver este erro quando o seu código de serviço não iniciar. A causa é, muitas vezes, no código do usuário. Para obter mais informações de diagnóstico, efetue as seguintes alterações aos seus comandos e as definições:

### <a name="try"></a>Experimente:

Na linha de comando:

Ao usar _azds.exe_, utilize a opção-- verbose da linha de comandos e utilizar a opção-- saída da linha de comandos para especificar o formato de saída.
 
```cmd
azds up --verbose --output json
```

No Visual Studio:

1. Open **ferramentas > opções** e, em **projetos e soluções**, escolha **compilar e executar**.
2. Alterar as definições de **verbosidade de saída da compilação de projeto do MSBuild** ao **Detailed** ou **diagnóstico**.

    ![Caixa de diálogo Opções de captura de ecrã de ferramentas](media/common/VerbositySetting.PNG)
    
### <a name="multi-stage-dockerfiles"></a>Vários estágios Dockerfiles:
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

Esse erro ocorre porque os nós AKS executam uma versão mais antiga do Docker que não oferece suporte a compilações de vários estágios. Para evitar compilações de vários estágios, reescreva seu Dockerfile.

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

Para resolver esse problema, use o `kubectl delete` comando para remover manualmente os serviços antigos do cluster e, em seguida, execute novamente os espaços de desenvolvimento para instalar os novos serviços.

## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Resolução de nomes DNS falha por um URL público associado a um serviço de espaços de desenvolvimento

Você pode configurar um ponto de extremidade de URL pública para seu serviço `--public` especificando a opção `azds prep` para o comando ou marcando `Publicly Accessible` a caixa de seleção no Visual Studio. O nome DNS público é registrado automaticamente quando você executa o serviço em espaços de desenvolvimento. Se esse nome DNS não estiver registrado, você verá que uma *página não pode ser exibida* ou o erro *site não pode ser* acessado no navegador da Web ao se conectar à URL pública.

### <a name="try"></a>Experimente:

Pode utilizar o seguinte comando para listar todas as URLs associadas com os serviços de espaços de desenvolvimento:

```cmd
azds list-uris
```

Se um URL no *pendente* Estado, o que significa que os espaços de desenvolvimento ainda está a aguardar concluir o registo de DNS. Às vezes, demora alguns minutos para o registo concluir. Espaços de desenvolvimento também é aberto um túnel de localhost para cada serviço, que pode utilizar enquanto estiver aguardando o registo de DNS.

Se uma URL permanecer no estado *pendente* por mais de 5 minutos, isso poderá indicar um problema com o Pod DNS externo que cria o ponto de extremidade público ou o Pod do controlador de entrada Nginx que adquire o ponto de extremidade público. Pode utilizar os comandos seguintes para eliminar estas pods. O AKS recria automaticamente o pods excluído.

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>Erro "Necessário ferramentas e configurações estão em falta"

Este erro poderá ocorrer ao iniciar o VS Code: "[espaços de desenvolvimento do Azure] necessário a ferramentas e configurações para compilar e depurar"[nome do projeto]"estão em falta."
O erro significa que azds.exe não está na variável de ambiente PATH, como visto no VS Code.

### <a name="try"></a>Experimente:

Inicie o VS Code a partir de um prompt de comando em que a variável de ambiente PATH está definida corretamente.

## <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Erro "as ferramentas necessárias para compilar e depurar ' ProjectName ' estão desatualizadas."

Você verá esse erro em Visual Studio Code se tiver uma versão mais recente da extensão de VS Code para Azure Dev Spaces, mas uma versão mais antiga da CLI do Azure Dev Spaces.

### <a name="try"></a>Experimente

Baixe e instale a versão mais recente da CLI do Azure Dev Spaces:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Erro 'azds' não é reconhecido como um comando interno ou externo, programa operável ou arquivo em lotes
 
Poderá ver este erro se azds.exe não está instalado ou configurado corretamente.

### <a name="try"></a>Experimente:

1. Verifique o local% ProgramFiles%/Microsoft SDKs\Azure\Azure dev Spaces CLI para azds. exe. Se existir, adicione essa localização à variável de ambiente PATH.
2. Se azds.exe não estiver instalado, execute o seguinte comando:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Aviso 'Dockerfile não foi possível gerar devido a linguagem não suportada"
Espaços de desenvolvimento do Azure fornece suporte nativo para c# e node. js. Quando executa *azds prep* num diretório que contém o código escrito em uma dessas linguagens, espaços de desenvolvimento do Azure irá criar automaticamente um Dockerfile apropriado para.

Você ainda pode usar Azure Dev Spaces com código escrito em outras linguagens, mas você precisa criar manualmente o Dockerfile antes de executar o *azds* pela primeira vez.

### <a name="try"></a>Experimente:
Se seu aplicativo for escrito em uma linguagem que Azure Dev Spaces não dá suporte nativo, você precisará fornecer um Dockerfile apropriado para criar uma imagem de contêiner executando seu código. O Docker fornece uma [lista de práticas recomendadas para escrever Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) e uma [referência Dockerfile](https://docs.docker.com/engine/reference/builder/) que pode ajudá-lo a escrever um Dockerfile que atenda às suas necessidades.

Depois de ter um Dockerfile adequada em vigor, pode continuar com a execução *azds se* para executar a aplicação nos espaços de desenvolvimento do Azure.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Erro 'a montante erro de ligar ou desligar/reset antes de cabeçalhos'
Poderá ver este erro ao tentar aceder ao seu serviço. Por exemplo, quando passa para o URL do serviço num navegador. 

### <a name="reason"></a>Razão 
A porta do contentor não está disponível. Esse problema pode ocorrer porque: 
* O contentor está ainda estiver no processo que está a ser compilado e implantado. Este problema pode surgir se executar `azds up` ou iniciar o depurador e, em seguida, tente aceder ao contentor antes de ser implementada com êxito.
* Configuração da porta não é consistente em toda sua _Dockerfile_, gráfico Helm e qualquer código de servidor que se abre uma porta.

### <a name="try"></a>Experimente:
1. Se o contentor está no processo a ser criado/implementado, pode aguardar 2 a 3 segundos e tente acessar o serviço novamente. 
1. Verifique a configuração de porta. Os números de porta especificados devem ser idênticos em todos os seguintes ativos:
    * **Dockerfile** Especificado pela `EXPOSE` instrução.
    * **[Gráfico de Helm](https://docs.helm.sh):** Especificado pelos `externalPort` valores e `internalPort` para um serviço (geralmente localizado em um `values.yml` arquivo),
    * Nenhuma porta de que está a ser aberta no código da aplicação, por exemplo, no node. js: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Ficheiro de configuração não encontrado
Executar `azds up` e receber o erro seguinte: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Razão
Tem de executar `azds up` do diretório de raiz do código que pretende executar e, é preciso inicializar a pasta de código para executar com espaços de desenvolvimento do Azure.

### <a name="try"></a>Experimente:
1. Altere o diretório atual para a pasta de raiz que contém o código de serviço. 
1. Se não tiver uma _azds.yaml_ ficheiro na pasta do código, executar `azds prep` para gerar os ativos de Docker, Kubernetes e espaços de desenvolvimento do Azure.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Erro: ' O programa de pipe ' azds ' saiu inesperadamente com o código 126. '
A iniciar o depurador do VS Code, às vezes, pode resultar em erro.

### <a name="try"></a>Experimente:
1. Feche e reabra o VS Code.
2. Aperte F5 novamente.

## <a name="debugging-error-failed-to-find-debugger-extension-for-typecoreclr"></a>Depuração de erro 'Falha ao localizar a extensão de depurador para o tipo: coreclr'
Executar o depurador do VS Code reporta o erro: `Failed to find debugger extension for type:coreclr.`

### <a name="reason"></a>Razão
Não tem a extensão do VS Code para c# instalado no computador de desenvolvimento. A C# extensão inclui suporte de depuração para o CoreCLR (.NET Core).

### <a name="try"></a>Experimente:
Instalar o [extensão do VS Code para c#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>Depuração de erro 'o tipo de depuração de configurado 'coreclr' não é suportado'
Executar o depurador do VS Code reporta o erro: `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Razão
Não tem a extensão do VS Code para espaços de desenvolvimento do Azure instalado no computador de desenvolvimento.

### <a name="try"></a>Experimente:
Instalar o [extensão do VS Code para espaços de desenvolvimento do Azure](get-started-netcore.md).

## <a name="debugging-error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Depuração de erro "valor inválido 'cwd' ' / src'. O sistema não é possível localizar o ficheiro especificado." ou "Iniciar: programa '/ src / [caminho para o binário do projeto]' não existe"
Executar o depurador do VS Code reporta o erro `Invalid 'cwd' value '/src'. The system cannot find the file specified.` e/ou `launch: program '/src/[path to project executable]' does not exist`

### <a name="reason"></a>Razão
Por predefinição, utiliza a extensão do VS Code `src` como o diretório de trabalho para o projeto no contentor. Se atualizou seu `Dockerfile` para especificar um diretório de trabalho diferentes, pode ver este erro.

### <a name="try"></a>Experimente:
Atualização do `launch.json` de ficheiros sob o `.vscode` subdiretório da pasta do projeto. Alterar o `configurations->cwd` diretiva para apontar para o mesmo diretório que o `WORKDIR` definidos no seu projeto `Dockerfile`. Também poderá ter de atualizar o `configurations->program` diretiva também.

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>Não foi possível encontrar o nome do tipo ou espaço de nomes "MyLibrary."

### <a name="reason"></a>Reason 
O contexto de compilação está no nível de projeto/serviço por padrão, portanto, um projeto de biblioteca que você está usando não pode ser encontrado.

### <a name="try"></a>Experimente:
O que precisa ser feito:
1. Modificar a _azds.yaml_ ficheiro para definir o contexto de compilação para o nível de solução.
2. Modificar a _Dockerfile_ e _Dockerfile.develop_ arquivos para fazer referência ao projeto ( _. csproj_) ficheiros corretamente, em relação ao novo criar contexto.
3. Lugar uma _.dockerignore_ arquivo ao lado do ficheiro. sln e modifique conforme necessário.

Pode encontrar um exemplo em https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>Erro de autorização 'Microsoft.DevSpaces/register/action'
Você precisa de acesso de *proprietário* ou *colaborador* em sua assinatura do Azure para gerenciar Azure dev Spaces. Você poderá ver esse erro se estiver tentando gerenciar espaços de desenvolvimento e não tiver acesso de *proprietário* ou *colaborador* à assinatura do Azure associada.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Reason
A subscrição do Azure selecionada não se registou o `Microsoft.DevSpaces` espaço de nomes.

### <a name="try"></a>Experimente:
Qualquer pessoa com acesso de proprietário ou contribuinte à subscrição do Azure pode executar o seguinte comando da CLI do Azure para registar manualmente o `Microsoft.DevSpaces` espaço de nomes:

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="dev-spaces-times-out-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Os espaços de desenvolvimento atingem o tempo limite *para aguardar a compilação da imagem de contêiner...* etapa com nós virtuais AKs

### <a name="reason"></a>Reason
Esse tempo limite ocorre quando você tenta usar espaços de desenvolvimento para executar um serviço que está configurado para ser executado em um [nó virtual AKs](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Atualmente, os espaços de desenvolvimento não dão suporte à criação ou depuração de serviços em nós virtuais.

Se você executar `azds up` com a `--verbose` opção ou habilitar o log detalhado no Visual Studio, verá detalhes adicionais:

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

O comando acima mostra que o Pod do serviço foi atribuído a *virtual-node-ACI-Linux*, que é um nó virtual.

### <a name="try"></a>Experimente:
Atualize o gráfico Helm para que o serviço remova os valores *nodeSelector* e/ou *Tolerations* que permitem que o serviço seja executado em um nó virtual. Esses valores normalmente são definidos no arquivo do `values.yaml` gráfico.

Você ainda pode usar um cluster AKS que tenha o recurso de nós virtuais habilitado, se o serviço que você deseja compilar/depurar por meio de espaços de desenvolvimento for executado em um nó de VM. Essa é a configuração padrão.

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>"Erro: não foi possível encontrar um pod tiller pronto" ao iniciar o desenvolvimento espaços

### <a name="reason"></a>Razão
Este erro ocorre se o cliente do Helm já não pode comunicar com o pod Tiller em execução no cluster.

### <a name="try"></a>Experimente:
Reiniciar os nós de agente no seu cluster normalmente resolve este problema.

## <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>"Erro: versão azds do\<identificador\>\>-denome\<deNamespaceNamefalhou: ServiceName dos serviços\<\> -\<\>' já existe "ou" acesso de pull negado \<para\>ServiceName, o repositório não existe ou pode exigir ' Docker login "

### <a name="reason"></a>Reason
Esses erros podem ocorrer se você misturar comandos Helm diretos em execução ( `helm install`como `helm upgrade`, ou `helm delete` `azds up` ) com os comandos de espaços de desenvolvimento ( `azds down`como e) dentro do mesmo espaço de desenvolvimento. Elas ocorrem porque os espaços de desenvolvimento têm sua própria instância de gaveta, que está em conflito com a instância de seu próprio gaveta em execução no mesmo espaço de desenvolvimento.

### <a name="try"></a>Experimente:
É bom usar comandos Helm e comandos de espaços de desenvolvimento no mesmo cluster AKS, mas cada namespace habilitado para espaços de desenvolvimento deve usar um ou outro.

Por exemplo, suponha que você use um comando Helm para executar o aplicativo inteiro em um espaço de desenvolvimento pai. Você pode criar espaços de desenvolvimento filho fora desse pai, usar espaços de desenvolvimento para executar serviços individuais dentro dos espaços de desenvolvimento filho e testar os serviços juntos. Quando estiver pronto para fazer check-in das alterações, use um comando Helm para implantar o código atualizado no espaço de desenvolvimento pai. Não use `azds up` para executar o serviço atualizado no espaço de desenvolvimento pai, pois ele entrará em conflito com o serviço executado inicialmente usando Helm.

## <a name="azure-dev-spaces-proxy-can-interfere-with-other-pods-running-in-a-dev-space"></a>Proxy de espaços de desenvolvimento do Azure pode interferir com outras pods em execução num espaço de desenvolvimento

### <a name="reason"></a>Razão
Quando ativar espaços de desenvolvimento num espaço de nomes no seu cluster do AKS, um contentor adicional chamado _mindaro proxy_ está instalado em cada um dos pods em execução dentro desse espaço de nomes. Esse contêiner intercepta as chamadas para os serviços no pod, que é integral para os recursos de desenvolvimento de equipe dos espaços de desenvolvimento; no entanto, ele pode interferir em determinados serviços em execução nesses pods. É conhecido por interferir no pods executando o cache do Azure para Redis, causando erros de conexão e falhas na comunicação primária/secundária.

### <a name="try"></a>Experimente:
Você pode mover os pods afetados para um namespace dentro do cluster que _não_ tem espaços de desenvolvimento habilitados. O restante do seu aplicativo pode continuar a ser executado dentro de um namespace habilitado para espaços de desenvolvimento. Espaços de desenvolvimento não irá instalar o _mindaro proxy_ espaços de nomes do contentor dentro de espaços não Dev ativados.

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Espaços de desenvolvimento do Azure parece que não usar meu Dockerfile existente para criar um contentor

### <a name="reason"></a>Razão
Espaços de desenvolvimento do Azure pode ser configurados para apontar para um específico _Dockerfile_ no seu projeto. Se aparecer Azure Dev Spaces não estiver usando o _Dockerfile_ de que você espera criar seus contêineres, talvez seja necessário informar explicitamente Azure dev Spaces qual Dockerfile usar. 

### <a name="try"></a>Experimente:
Abra o arquivo _azds. YAML_ que Azure dev Spaces gerado em seu projeto. Use a diretiva *Configurações-> desenvolver-> Build-> dockerfile* para apontar para o dockerfile que você deseja usar:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

## <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Erro "falha na inspeção interna: Watch ENOSPC" ao anexar a depuração a um aplicativo node. js

### <a name="reason"></a>Reason

O nó que executa o Pod com o aplicativo node. js ao qual você está tentando se anexar com um depurador excedeu o valor de *_user_watches FS. INotifyPropertyChanged. Max* . Em alguns casos, [o valor padrão de *FS. INotifyPropertyChanged. Max _user_watches* pode ser muito pequeno para lidar com a anexação de um depurador diretamente a um pod](https://github.com/Azure/AKS/issues/772).

### <a name="try"></a>Experimente
Uma solução alternativa temporária para esse problema é aumentar o valor de *FS. INotifyPropertyChanged. Max _user_watches* em cada nó no cluster e reiniciar esse nó para que as alterações entrem em vigor.

## <a name="new-pods-are-not-starting"></a>Novos pods não estão iniciando

### <a name="reason"></a>Reason

O inicializador kubernetes não pode aplicar o PodSpec para o novo pods devido a alterações de permissão de RBAC para a função de *administrador de cluster* no cluster. O novo pod também pode ter um PodSpec inválido, por exemplo, a conta de serviço associada ao Pod não existe mais. Para ver os pods que estão em um estado *pendente* devido ao problema do inicializador, use `kubectl get pods` o comando:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Esse problema pode afetar os pods em *todos os namespaces* no cluster, incluindo namespaces em que o Azure dev Spaces não está habilitado.

### <a name="try"></a>Experimente

[Atualizando a CLI de espaços de desenvolvimento para a versão mais recente](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) e, em seguida, excluindo o *azds InitializerConfiguration* do controlador de Azure dev Spaces:

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

Depois de remover o *azds InitializerConfiguration* do controlador de Azure dev Spaces, use `kubectl delete` para remover qualquer pods em um estado *pendente* . Depois que todos os pods pendentes tiverem sido removidos, reimplante o pods.

Se novos pods ainda estiverem presos em um estado *pendente* após uma reimplantação, use `kubectl delete` para remover todos os pods em um estado *pendente* . Depois que todos os pods pendentes tiverem sido removidos, exclua o controlador do cluster e reinstale-o:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Depois que o controlador for reinstalado, reimplante o pods.

## <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Permissões RBAC incorretas para chamar controlador e APIs de espaços de desenvolvimento

### <a name="reason"></a>Reason
O usuário que acessa o controlador de Azure Dev Spaces deve ter acesso para ler o *kubeconfig* do administrador no cluster AKs. Por exemplo, essa permissão está disponível na [função de administrador de cluster do serviço kubernetes do Azure interna](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). O usuário que acessa o controlador de Azure Dev Spaces também deve ter a função de RBAC de *proprietário* ou *colaborador* para o controlador.

### <a name="try"></a>Experimente
Mais detalhes sobre como atualizar as permissões de um usuário para um cluster AKS estão disponíveis [aqui](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Para atualizar a função RBAC do usuário para o controlador:

1. Inicie sessão no portal do Azure em https://portal.azure.com.
1. Navegue até o grupo de recursos que contém o controlador, que geralmente é o mesmo que o cluster AKS.
1. Habilite a caixa de seleção *Mostrar tipos ocultos* .
1. Clique no controlador.
1. Abra o painel *controle de acesso (iam)* .
1. Clique na guia *atribuições de função* .
1. Clique em *Adicionar* e em *Adicionar atribuição de função*.
    * Para *função* , selecione *colaborador* ou *proprietário*.
    * Para *atribuir acesso para* selecionar *usuário, grupo ou entidade de serviço do Azure ad*.
    * Para *selecionar* pesquisa para o usuário que você deseja conceder permissões.
1. Clique em *Guardar*.

## <a name="controller-create-failing-due-to-controller-name-length"></a>Falha na criação do controlador devido a comprimento do nome do controlador

### <a name="reason"></a>Reason
O nome de um controlador de Azure Dev Spaces não pode ter mais de 31 caracteres. Se o nome do controlador exceder 31 caracteres ao habilitar espaços de desenvolvimento em um cluster AKS ou criar um controlador, você receberá um erro como:

*Falha ao criar um controlador de espaços de desenvolvimento para o cluster ' a-Controller-Name-que-is-Way-The-Long-AKs-leste-US ': O nome do controlador de Azure Dev Spaces ' a-Controller-Name-que-is-Way-time-Long-AKs-leste-US ' é inválido. Restrição (ões) violada (s): Azure Dev Spaces nomes de controlador só podem ter no máximo 31 caracteres de comprimento*

### <a name="try"></a>Experimente

Crie um controlador com um nome alternativo:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

## <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Falha ao habilitar espaços de desenvolvimento quando os pools de nós do Windows são adicionados a um cluster AKS

### <a name="reason"></a>Reason
Atualmente, o Azure Dev Spaces destina-se a ser executado somente em pods e nós do Linux. Quando você tem um cluster AKS com um pool de nós do Windows, deve garantir que Azure Dev Spaces pods sejam agendadas apenas em nós do Linux. Se um pod Azure Dev Spaces estiver agendado para ser executado em um nó do Windows, esse Pod não será iniciado e a habilitação dos espaços de desenvolvimento falhará.

### <a name="try"></a>Experimente
[Adicione um](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) seu cluster AKs para garantir que os pods do Linux não estejam agendados para execução em um nó do Windows.

## <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Erro "não foi encontrado nenhum nó Linux não reconsiderado no estado pronto no cluster. Deve haver pelo menos um nó Linux não comparável no estado pronto para implantar pods no namespace ' azds '. "

### <a name="reason"></a>Reason

Azure Dev Spaces não pôde criar um controlador em seu cluster AKS porque não pôde encontrar um nó não comparável em um estado *pronto* para agendar pods. Azure Dev Spaces requer pelo menos um nó do Linux em um estado *pronto* que permita o agendamento de pods sem especificar Tolerations.

### <a name="try"></a>Experimente
[Atualize sua configuração](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) do seu AKs no cluster para garantir que pelo menos um nó do Linux permita o planejamento de pods sem especificar Tolerations. Além disso, verifique se pelo menos um nó Linux que permite o agendamento de pods sem especificar Tolerations está no estado *pronto* . Se o seu nó estiver demorando muito tempo para chegar ao estado *pronto* , você poderá tentar reiniciar o nó.

## <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Erro "a CLI do Azure Dev Spaces não foi instalada corretamente" durante a execução`az aks use-dev-spaces`

### <a name="reason"></a>Reason
Uma atualização para a CLI do Azure Dev Spaces alterou seu caminho de instalação. Se você estiver usando uma versão do CLI do Azure anterior a 2.0.63, poderá ver esse erro. Para exibir sua versão do CLI do Azure, use `az --version`.

```bash
$ az --version
azure-cli                         2.0.60 *
...
```

Apesar da mensagem de erro ao `az aks use-dev-spaces` ser executada com uma versão do CLI do Azure antes de 2.0.63, a instalação é realizada com sucesso. Você pode continuar a usar `azds` sem problemas.

### <a name="try"></a>Experimente
Atualize a instalação do [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) para o 2.0.63 ou posterior. Isso resolverá a mensagem de erro que você receberá ao executar `az aks use-dev-spaces`. Como alternativa, você pode continuar a usar sua versão atual do CLI do Azure e a CLI do Azure Dev Spaces.


## <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>O dimensionamento automático do pod horizontal não está funcionando em um espaço de desenvolvimento

### <a name="reason"></a>Reason

Quando você executa um serviço em um espaço de desenvolvimento, o Pod do serviço é [injetado com contêineres adicionais para instrumentação](how-dev-spaces-works.md#prepare-your-aks-cluster) e todos os contêineres em um pod precisam ter limites de recursos e solicitações definidas para dimensionamento automático de Pod horizontal. 


As solicitações de recursos e os limites podem ser aplicados ao contêiner injetado (devspaces-proxy) adicionando `azds.io/proxy-resources` a anotação à especificação do pod. O valor deve ser definido como um objeto JSON que representa a seção de recursos da especificação do contêiner para o proxy.

### <a name="try"></a>Experimente

Abaixo está um exemplo de uma anotação de recursos de proxy que deve ser aplicada à sua especificação de Pod.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

## <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Erro "não autorizado: autenticação necessária" ao tentar usar uma imagem do Docker de um registro privado

### <a name="reason"></a>Reason

Você está usando uma imagem do Docker de um registro privado que requer autenticação. Você pode permitir que os espaços de desenvolvimento autentiquem e recebam imagens desse registro privado usando [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets).

### <a name="try"></a>Experimente

Para usar o imagePullSecrets, [crie um segredo kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) no namespace em que você está usando a imagem. Em seguida, forneça o segredo como um `azds.yaml`imagePullSecret no.

Abaixo está um exemplo de uma especificação de imagePullSecrets `azds.yaml`no.

```
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
> A configuração de `azds.yaml` imagePullSecrets no substituirá imagePullSecrets especificado `values.yaml`no.
