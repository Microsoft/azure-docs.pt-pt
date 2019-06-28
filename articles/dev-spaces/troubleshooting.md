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
keywords: 'Docker, o Kubernetes, o Azure, o AKS, o serviço Kubernetes do Azure, contentores, Helm, a malha de serviço, roteamento de malha do serviço, kubectl, k8s '
ms.openlocfilehash: e0379bbc7f26ea30f65c5eac73633ca0371aa283
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331313"
---
# <a name="troubleshooting-guide"></a>Guia de resolução de problemas

Este guia contém informações sobre problemas comuns que poderá ter quando utilizar espaços de desenvolvimento do Azure.

Se tiver um problema quando utilizar espaços de desenvolvimento do Azure, crie uma [problema no repositório do GitHub do Azure Dev espaços](https://github.com/Azure/dev-spaces/issues).

## <a name="enabling-detailed-logging"></a>Ativar o registo detalhado

Para resolver problemas com mais eficiência, ele pode ajudar a criar registos mais detalhados para revisão.

Para a extensão do Visual Studio, defina o `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` variável de ambiente para 1. Certifique-se de que reinicie o Visual Studio para a variável de ambiente para entrar em vigor. Uma vez ativada, os registos detalhados são escritos para sua `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` diretório.

Na CLI, o utilizador pode apresentar mais informações durante a execução do comando utilizando o `--verbose` mudar. Também pode procurar registos mais detalhados no `%TEMP%\Azure Dev Spaces`. Num Mac, o diretório TEMP do diretório pode ser encontrado ao executar `echo $TMPDIR` de uma janela de terminal. Num computador Linux, o diretório TEMP do diretório é normalmente `/tmp`.

## <a name="debugging-services-with-multiple-instances"></a>Depuração de serviços com várias instâncias

Atualmente, os espaços de desenvolvimento do Azure works melhores ao depurar uma única instância ou o pod. O ficheiro de azds.yaml contém uma definição *replicaCount*, que indica o número de pods que executa o Kubernetes para o seu serviço. Se alterar o replicaCount para configurar a aplicação seja executada vários pods para um determinado serviço, o depurador anexa o pod primeiro, quando listados por ordem alfabética. O depurador anexa a um pod diferente quando recicla o pod original, resultando possivelmente num comportamento inesperado.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Erro "Falha ao criar o controlador de espaços de desenvolvimento do Azure"

### <a name="reason"></a>Reason
Poderá ver este erro quando algo dá errado com a criação do controlador. Se for um erro transitório, elimine e recrie o controlador para corrigi-lo.

### <a name="try"></a>Experimente

Elimine o controlador:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Tem de utilizar a CLI do Azure Dev espaços para eliminar um controlador. Não é possível eliminar um controlador a partir do Visual Studio. Também não é possível instalar a CLI do Azure Dev espaços no Azure Cloud Shell para que não é possível eliminar um controlador a partir do Azure Cloud Shell.

Se não tiver a CLI de espaços de desenvolvimento do Azure instalada, pode primeiro instalá-lo com o seguinte comando, em seguida, eliminar seu controlador:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Recriar o controlador pode ser feito na CLI ou do Visual Studio. Consulte a [da equipe de desenvolvimento](quickstart-team-development.md) ou [desenvolver com .NET Core](quickstart-netcore-visualstudio.md) inícios rápidos para obter exemplos.

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
Receberá um *não é possível iniciar o serviço* erro quando utiliza um Dockerfile vários estágio. Nesta situação, a saída detalhada contém o seguinte texto:

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

Este erro ocorre porque nós do AKS executados uma versão mais antiga do Docker não suporta vários estágios baseia-se. Para evitar vários estágios compilações, reescreva seu Dockerfile.

### <a name="rerunning-a-service-after-controller-re-creation"></a>Executar novamente um serviço após a criação de reavaliação de controlador
Receberá um *não é possível iniciar o serviço* Ocorreu um erro ao tentar voltar a executar um serviço, depois de ter removido e recriado, em seguida, o controlador de espaços de desenvolvimento do Azure associado a este cluster. Nesta situação, a saída detalhada contém o seguinte texto:

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Este erro ocorre porque a remover o controlador de espaços de desenvolvimento não remove serviços anteriormente instalados por esse controlador. Recriar o controlador e, em seguida, tentar executar os serviços usando o novo controlador falhar porque os serviços antigos ainda estão no local.

Para resolver esse problema, utilize o `kubectl delete` comando manualmente remover os serviços antigos do seu cluster, em seguida, volte a executar os espaços de desenvolvimento para instalar os novos serviços.

## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Resolução de nomes DNS falha por um URL público associado a um serviço de espaços de desenvolvimento

Pode configurar um ponto de final do URL público para o seu serviço, especificando o `--public` mude para o `azds prep` comando ou por selecionar o `Publicly Accessible` caixa de verificação no Visual Studio. O nome DNS público é registrado automaticamente ao executar o seu serviço nos espaços de desenvolvimento. Se este nome DNS não está registado, verá uma *não é possível apresentar a página* ou *não é possível contactar o Site* erro no seu browser ao estabelecer ligação com o URL público.

### <a name="try"></a>Experimente:

Pode utilizar o seguinte comando para listar todas as URLs associadas com os serviços de espaços de desenvolvimento:

```cmd
azds list-uris
```

Se um URL no *pendente* Estado, o que significa que os espaços de desenvolvimento ainda está a aguardar concluir o registo de DNS. Às vezes, demora alguns minutos para o registo concluir. Espaços de desenvolvimento também é aberto um túnel de localhost para cada serviço, que pode utilizar enquanto estiver aguardando o registo de DNS.

Se um URL permanece no *pendente* de estado durante mais de 5 minutos, tal poderá indicar um problema com o pod DNS externo que cria o ponto final público ou o pod de controlador de entrada nginx adquire o ponto final público. Pode utilizar os comandos seguintes para eliminar estas pods. AKS recria automaticamente os pods eliminados.

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>Erro "Necessário ferramentas e configurações estão em falta"

Este erro poderá ocorrer ao iniciar o VS Code: "[espaços de desenvolvimento do Azure] necessário a ferramentas e configurações para compilar e depurar"[nome do projeto]"estão em falta."
O erro significa que azds.exe não está na variável de ambiente PATH, como visto no VS Code.

### <a name="try"></a>Experimente:

Inicie o VS Code a partir de um prompt de comando em que a variável de ambiente PATH está definida corretamente.

## <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Erro "as ferramentas necessárias para compilar e depurar 'projectname' estão Desatualizadas."

Verá este erro no Visual Studio Code, se tiver uma versão mais recente da extensão do VS Code para espaços de desenvolvimento do Azure, mas uma versão mais antiga da CLI de espaços de desenvolvimento do Azure.

### <a name="try"></a>Experimente

Transfira e instale a versão mais recente da CLI de espaços de desenvolvimento do Azure:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Erro 'azds' não é reconhecido como um comando interno ou externo, programa operável ou arquivo em lotes
 
Poderá ver este erro se azds.exe não está instalado ou configurado corretamente.

### <a name="try"></a>Experimente:

1. Verifique a localização %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev espaços CLI para azds.exe. Se existir, adicione essa localização à variável de ambiente PATH.
2. Se azds.exe não estiver instalado, execute o seguinte comando:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Aviso 'Dockerfile não foi possível gerar devido a linguagem não suportada"
Espaços de desenvolvimento do Azure fornece suporte nativo para c# e node. js. Quando executa *azds prep* num diretório que contém o código escrito em uma dessas linguagens, espaços de desenvolvimento do Azure irá criar automaticamente um Dockerfile apropriado para.

Pode continuar a utilizar espaços de desenvolvimento do Azure com código escrito em outras linguagens, mas tem de criar manualmente o Dockerfile antes de executar *azds se* pela primeira vez.

### <a name="try"></a>Experimente:
Se o aplicativo foi escrito numa linguagem que os espaços de desenvolvimento do Azure não suporta nativamente, terá de fornecer um Dockerfile adequada para criar uma imagem de contentor em execução do seu código. Docker disponibiliza um [lista de práticas recomendadas para escrever Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) e uma [referência do Dockerfile](https://docs.docker.com/engine/reference/builder/) que pode ajudá-lo a se escreve um Dockerfile que atenda às suas necessidades.

Depois de ter um Dockerfile adequada em vigor, pode continuar com a execução *azds se* para executar a aplicação nos espaços de desenvolvimento do Azure.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Erro 'a montante erro de ligar ou desligar/reset antes de cabeçalhos'
Poderá ver este erro ao tentar aceder ao seu serviço. Por exemplo, quando passa para o URL do serviço num navegador. 

### <a name="reason"></a>Razão 
A porta do contentor não está disponível. Esse problema pode ocorrer porque: 
* O contentor está ainda estiver no processo que está a ser compilado e implantado. Este problema pode surgir se executar `azds up` ou iniciar o depurador e, em seguida, tente aceder ao contentor antes de ser implementada com êxito.
* Configuração da porta não é consistente em toda sua _Dockerfile_, gráfico Helm e qualquer código de servidor que se abre uma porta.

### <a name="try"></a>Experimente:
1. Se o contentor está no processo a ser criado/implementado, pode aguardar 2 a 3 segundos e tente acessar o serviço novamente. 
1. Verifique a configuração de porta. Os números de porta especificado devem estar **idênticos** em todos os recursos seguintes:
    * **Dockerfile:** Especificado pelo `EXPOSE` instrução.
    * **[Gráfico do Helm](https://docs.helm.sh):** Especificado pela `externalPort` e `internalPort` valores para um serviço (muitas vezes, localizados num `values.yml` ficheiro),
    * Nenhuma porta de que está a ser aberta no código da aplicação, por exemplo, no node. js: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Ficheiro de configuração não encontrado
Executar `azds up` e receber o erro seguinte: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Razão
Tem de executar `azds up` do diretório de raiz do código que pretende executar e, é preciso inicializar a pasta de código para executar com espaços de desenvolvimento do Azure.

### <a name="try"></a>Experimente:
1. Altere o diretório atual para a pasta de raiz que contém o código de serviço. 
1. Se não tiver uma _azds.yaml_ ficheiro na pasta do código, executar `azds prep` para gerar os ativos de Docker, Kubernetes e espaços de desenvolvimento do Azure.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Erro: "O programa de pipe 'azds' terminou inesperadamente com o código 126."
A iniciar o depurador do VS Code, às vezes, pode resultar em erro.

### <a name="try"></a>Experimente:
1. Feche e reabra o VS Code.
2. Aperte F5 novamente.

## <a name="debugging-error-failed-to-find-debugger-extension-for-typecoreclr"></a>Depuração de erro 'Falha ao localizar a extensão de depurador para o tipo: coreclr'
Executar o depurador do VS Code reporta o erro: `Failed to find debugger extension for type:coreclr.`

### <a name="reason"></a>Razão
Não tem a extensão do VS Code para c# instalado no computador de desenvolvimento. O C# extensão inclui suporte para .NET Core (CoreCLR) de depuração.

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
O contexto de compilação é no nível de projeto/serviço por predefinição, portanto um projeto de biblioteca que está a utilizar não é possível encontrar.

### <a name="try"></a>Experimente:
O que precisa ser feito:
1. Modificar a _azds.yaml_ ficheiro para definir o contexto de compilação para o nível de solução.
2. Modificar a _Dockerfile_ e _Dockerfile.develop_ arquivos para fazer referência ao projeto ( _. csproj_) ficheiros corretamente, em relação ao novo criar contexto.
3. Lugar uma _.dockerignore_ arquivo ao lado do ficheiro. sln e modifique conforme necessário.

Pode encontrar um exemplo em https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>Erro de autorização 'Microsoft.DevSpaces/register/action'
Precisa *proprietário* ou *contribuinte* acesso na sua subscrição do Azure para gerir espaços de desenvolvimento do Azure. Poderá ver este erro se estiver a tentar gerir espaços de desenvolvimento e não tiver *proprietário* ou *contribuinte* acesso à subscrição do Azure associada.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Reason
A subscrição do Azure selecionada não se registou o `Microsoft.DevSpaces` espaço de nomes.

### <a name="try"></a>Experimente:
Qualquer pessoa com acesso de proprietário ou contribuinte à subscrição do Azure pode executar o seguinte comando da CLI do Azure para registar manualmente o `Microsoft.DevSpaces` espaço de nomes:

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="dev-spaces-times-out-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Espaços de desenvolvimento exceder o tempo limite em *a aguardar a criação da imagem do contentor...*  passo connosco virtuais de AKS

### <a name="reason"></a>Reason
Este tempo limite ocorre quando tenta utilizar espaços de desenvolvimento para executar um serviço que está configurado para ser executado [nó virtual de AKS](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Espaços de desenvolvimento não suporta atualmente a criar ou a depuração de serviços em nós virtuais.

Se executar `azds up` com o `--verbose` comutador ou ativar o registo verboso no Visual Studio, verá detalhes adicionais:

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

O comando acima mostra que o pod do serviço foi atribuído a *virtual-nó-aci-linux*, que é um nó virtual.

### <a name="try"></a>Experimente:
Atualizar o gráfico Helm para o serviço de remover quaisquer *nodeSelector* e/ou *tolerations* valores que permitem que o serviço para executar num nó virtual. Estes valores normalmente são definidos do gráfico `values.yaml` ficheiro.

Pode continuar a utilizar um cluster do AKS que tenha a funcionalidade de nós virtuais ativada, se o serviço que pretende depuração/compilação por meio de espaços de desenvolvimento é executado num nó VM. Esta é a configuração predefinida.

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>"Erro: não foi possível encontrar um pod tiller pronto" ao iniciar o desenvolvimento espaços

### <a name="reason"></a>Razão
Este erro ocorre se o cliente do Helm já não pode comunicar com o pod Tiller em execução no cluster.

### <a name="try"></a>Experimente:
Reiniciar os nós de agente no seu cluster normalmente resolve este problema.

## <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>"Erro: versão azds -\<identificador\>-\<spacename\>-\<servicename\> falhou: dos serviços\<servicename\>' já existe "ou" solicitar acesso negado para \<servicename\>, repositório não existe ou poderá exigir "início de sessão do docker" "

### <a name="reason"></a>Reason
Estes erros podem ocorrer se combinar o executar comandos de Helm diretos (como `helm install`, `helm upgrade`, ou `helm delete`) com os comandos de espaços de desenvolvimento (como `azds up` e `azds down`) dentro do mesmo espaço de desenvolvimento. Que ocorrem porque os espaços de desenvolvimento tem sua própria instância do Tiller, que está em conflito com a sua própria instância do Tiller em execução no mesmo espaço de desenvolvimento.

### <a name="try"></a>Experimente:
Não há problema em utilizar comandos do Helm e comandos de espaços de desenvolvimento no mesmo cluster do AKS, mas cada espaço de nomes de ativado o espaços de desenvolvimento deve utilizar um ou outro.

Por exemplo, suponha que usar um comando Helm para executar todo o seu aplicativo num espaço de desenvolvimento principal. Pode criar subordinado espaços de desenvolvimento desativar pai, utilizar espaços de desenvolvimento para executar serviços individuais dentro do filho dev espaços e testar os serviços em conjunto. Quando estiver pronto para verificar as suas alterações, utilize um comando Helm para implementar o código atualizado para o espaço de desenvolvimento principal. Não utilize `azds up` para executar o serviço atualizado na pai de espaço de desenvolvimento, uma vez que este irá entrar em conflito com o serviço executar inicialmente a utilizar o Helm.

## <a name="azure-dev-spaces-proxy-can-interfere-with-other-pods-running-in-a-dev-space"></a>Proxy de espaços de desenvolvimento do Azure pode interferir com outras pods em execução num espaço de desenvolvimento

### <a name="reason"></a>Razão
Quando ativar espaços de desenvolvimento num espaço de nomes no seu cluster do AKS, um contentor adicional chamado _mindaro proxy_ está instalado em cada um dos pods em execução dentro desse espaço de nomes. Este contentor intercepta as chamadas para os serviços no pod, que é integral para recursos de desenvolvimento de equipe dos espaços de desenvolvimento; No entanto, ele pode interferir com determinados serviços em execução nos pods. Ele é conhecido interfira com pods em execução Azure Cache de Redis, causando erros de ligação e falhas de comunicação de primária/secundária.

### <a name="try"></a>Experimente:
Pode mover os pods afetados para um espaço de nomes dentro do cluster que faz _não_ ter espaços de desenvolvimento ativada. O resto da sua aplicação pode continuar a ser executado dentro de um espaço de nomes de ativado o espaços de desenvolvimento. Espaços de desenvolvimento não irá instalar o _mindaro proxy_ espaços de nomes do contentor dentro de espaços não Dev ativados.

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Espaços de desenvolvimento do Azure parece que não usar meu Dockerfile existente para criar um contentor

### <a name="reason"></a>Razão
Espaços de desenvolvimento do Azure pode ser configurados para apontar para um específico _Dockerfile_ no seu projeto. Se for apresentada a espaços de desenvolvimento do Azure não está a utilizar o _Dockerfile_ que espera criar os seus contentores, poderá ser necessário informar explicitamente qual Dockerfile para utilizar espaços de desenvolvimento do Azure. 

### <a name="try"></a>Experimente:
Abra o _azds.yaml_ esse espaços de desenvolvimento do Azure gerado no seu projeto de ficheiros. Utilizar o *configurações -> desenvolver -> compilação -> dockerfile* diretiva para apontar para o Dockerfile que pretende utilizar:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

## <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Erro de "watch interno falhou: Assista ENOSPC" ao anexar a depuração para uma aplicação node. js

### <a name="reason"></a>Reason

O nó a executar o pod com a aplicação node. js que está a tentar anexar a, com um depurador foi excedido o *fs.inotify.max_user_watches* valor. Em alguns casos, [o valor predefinido *fs.inotify.max_user_watches* pode ser demasiado pequena para lidar com conectar um depurador diretamente a um pod](https://github.com/Azure/AKS/issues/772).

### <a name="try"></a>Experimente
Uma solução temporária para este problema é aumentar o valor de *fs.inotify.max_user_watches* em cada nó do cluster e reiniciar esse nó para que as alterações entrem em vigor.

## <a name="new-pods-are-not-starting"></a>Novos pods não estão a iniciar

### <a name="reason"></a>Reason

O inicializador de Kubernetes não é possível aplicar o PodSpec para novos pods devido a alterações de permissão RBAC para o *administrador de cluster* função no cluster. O novo pod também pode ter um PodSpec inválido, por exemplo a conta de serviço associada com o pod já não existe. Para ver os pods que estão num *pendente* Estado devido a problema o inicializador, utilize o `kubectl get pods` comando:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Este problema pode ser afetado pods na *todos os espaços de nomes* do cluster, incluindo espaços de nomes onde os espaços de desenvolvimento do Azure não está ativado.

### <a name="try"></a>Experimente

[Atualizar a CLI de espaços de desenvolvimento para a versão mais recente](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) e, em seguida, eliminar a *azds InitializerConfiguration* do controlador de espaços de desenvolvimento do Azure:

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

Depois de ter removido a *azds InitializerConfiguration* a partir do controlador de espaços de desenvolvimento do Azure, utilize `kubectl delete` para remover qualquer pods numa *pendente* estado. Afinal de contas pendentes pods ter sido removido, volte a implementar seus pods.

Se novos pods ainda estão com dificuldades num *pendente* Estado após uma nova implementação, utilize `kubectl delete` para remover qualquer pods numa *pendente* estado. Afinal de contas pendentes pods foram removidos, elimine o controlador do cluster e reinstalá-lo:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Depois do controlador é reinstalado, volte a implementar seus pods.

## <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Permissões RBAC incorretas para chamar o controlador de espaços de programação e APIs

### <a name="reason"></a>Reason
O utilizador aceder ao controlador de espaços de desenvolvimento do Azure tem de ter acesso para ler o administrador *kubeconfig* no cluster do AKS. Por exemplo, esta permissão está disponível na [incorporadas do Azure Kubernetes Service administrador de função de Cluster](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). O utilizador aceder ao controlador de espaços de desenvolvimento do Azure também tem de ter o *contribuinte* ou *proprietário* função RBAC para o controlador.

### <a name="try"></a>Experimente
Estão disponíveis mais detalhes sobre como atualizar permissões de um utilizador para um cluster do AKS [aqui](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Para atualizar a função do utilizador RBAC para o controlador:

1. Inicie sessão no portal do Azure em https://portal.azure.com.
1. Navegue para o grupo de recursos que contém o controlador, que normalmente é o mesmo que o cluster do AKS.
1. Ativar a *mostrar tipos ocultos* caixa de verificação.
1. Clique no controlador.
1. Abra o *controlo de acesso (IAM)* painel.
1. Clique nas *atribuições de funções* separador.
1. Clique em *Add* , em seguida, *adicionar atribuição de função*.
    * Para *função* selecione *contribuinte* ou *proprietário*.
    * Para *atribuir acesso aos* selecionar *utilizador, grupo ou principal de serviço do Azure AD*.
    * Para *selecione* pesquisa para o qual pretende conceder permissões de utilizador.
1. Clique em *Guardar*.

## <a name="controller-create-failing-due-to-controller-name-length"></a>Criar o controlador a falhar devido ao comprimento do nome de controlador

### <a name="reason"></a>Reason
Nome do controlador de espaços de desenvolvimento do Azure não pode ter mais de 31 carateres. Se o nome do seu controlador excede a 31 carateres quando ativar os espaços de desenvolvimento num cluster do AKS ou criar um controlador, receberá um erro, como:

*Falha ao criar um controlador de espaços de desenvolvimento para o cluster "a-controller-name-that-is-way-too-long-aks-east-us": Nome de controlador de espaços de desenvolvimento do Azure "a-controller-name-that-is-way-too-long-aks-east-us" é inválido. CONSTRAINT(s) violada: Nomes de controladores de espaços de desenvolvimento do Azure só podem ser no máximo 31 carateres de comprimento*

### <a name="try"></a>Experimente

Crie um controlador com um nome alternativo:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

## <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Ativar espaços de desenvolvimento a falhar quando conjuntos de nós do Windows são adicionados a um cluster do AKS

### <a name="reason"></a>Reason
Atualmente, os espaços de desenvolvimento do Azure destina-se para execução no Linux pods e nós apenas. Quando tiver um cluster do AKS com um conjunto de nós do Windows, certifique-se de que os pods de espaços de desenvolvimento do Azure estão agendadas de apenas em nós do Linux. Se um pod de espaços de desenvolvimento do Azure está agendado para ser executada num nó do Windows, esse pod não será iniciado e ativar os espaços de desenvolvimento irá falhar.

### <a name="try"></a>Experimente
[Adicionar um taint](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) ao seu cluster do AKS para garantir que o Linux pods não estão agendados para ser executada num nó do Windows.

## <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Erro "foi encontrado nenhuma inequívocas nós do Linux no estado pronto no cluster. Deve haver pelo menos um nó de Linux inequívocas no estado pronto para implementar os pods no espaço de nomes "azds"."

### <a name="reason"></a>Reason

Espaços de desenvolvimento do Azure não foi possível criar um controlador do seu cluster do AKS porque ele não foi possível encontrar um nó inequívocas uma *pronto* estado para agendar pods na. Espaços de desenvolvimento do Azure requer pelo menos um nó de Linux numa *pronto* Estado que permita para agendar pods sem especificar tolerations.

### <a name="try"></a>Experimente
[Atualizar a configuração de taint](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) no seu cluster do AKS para garantir que o Linux, pelo menos, um nó permite agendamento pods sem especificar tolerations. Além disso, certifique-se de que, pelo menos, um nó de Linux que permite o agendamento de pods sem especificar tolerations é no *pronto* estado. Se o nó está a demorar muito tempo a chegar a *pronto* de estado, pode tentar reiniciar o nó.
