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
ms.openlocfilehash: 7a944b87192236ba7401fe62d52275bea8be5076
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57769800"
---
# <a name="troubleshooting-guide"></a>Guia de resolução de problemas

Este guia contém informações sobre problemas comuns que poderá ter quando utilizar espaços de desenvolvimento do Azure.

## <a name="enabling-detailed-logging"></a>Ativar o registo detalhado

Para resolver problemas com mais eficiência, ele pode ajudar a criar registos mais detalhados para revisão.

Para a extensão do Visual Studio, defina o `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` variável de ambiente para 1. Certifique-se de que reinicie o Visual Studio para a variável de ambiente para entrar em vigor. Uma vez ativada, os registos detalhados são escritos para sua `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` diretório.

Na CLI, o utilizador pode apresentar mais informações durante a execução do comando utilizando o `--verbose` mudar. Também pode procurar registos mais detalhados no `%TEMP%\Azure Dev Spaces`. Num Mac, o diretório TEMP do diretório pode ser encontrado ao executar `echo $TMPDIR` de uma janela de terminal. Num computador Linux, o diretório TEMP do diretório é normalmente `/tmp`.

## <a name="debugging-services-with-multiple-instances"></a>Depuração de serviços com várias instâncias

Atualmente, os espaços de desenvolvimento do Azure works melhores ao depurar uma única instância ou o pod. O ficheiro de azds.yaml contém uma definição *replicaCount*, que indica o número de pods que executa o Kubernetes para o seu serviço. Se alterar o replicaCount para configurar a aplicação seja executada vários pods para um determinado serviço, o depurador anexa o pod primeiro, quando listados por ordem alfabética. O depurador anexa a um pod diferente quando recicla o pod original, resultando possivelmente num comportamento inesperado.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Erro "Falha ao criar o controlador de espaços de desenvolvimento do Azure"

Poderá ver este erro quando algo dá errado com a criação do controlador. Se for um erro transitório, elimine e recrie o controlador para corrigi-lo.

### <a name="try"></a>Experimente:

Para eliminar o controlador, utilize a CLI do Azure Dev espaços. Não é possível fazê-lo no Visual Studio ou no Cloud Shell. Para instalar a CLI AZDS, primeiro de instalar a CLI do Azure e, em seguida, execute este comando:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

E, em seguida, execute este comando para eliminar o controlador:

```cmd
azds remove -g <resource group name> -n <cluster name>
```

Recriar o controlador pode ser feito na CLI ou do Visual Studio. Siga as instruções nos tutoriais como se a iniciar pela primeira vez.


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

### <a name="try"></a>Experimentar

Transfira e instale a versão mais recente da CLI de espaços de desenvolvimento do Azure:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Erro 'azds' não é reconhecido como um comando interno ou externo, programa operável ou arquivo em lotes
 
Poderá ver este erro se azds.exe não está instalado ou configurado corretamente.

### <a name="try"></a>Experimente:

1. Verifique a localização %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev espaços CLI (pré-visualização) para azds.exe. Se existir, adicione essa localização à variável de ambiente PATH.
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
Não tem a extensão do VS Code para c# instalado no computador de desenvolvimento. A extensão c# inclui a depuração de suporte para .net Core (CoreCLR).

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

### <a name="reason"></a>Razão 
O contexto de compilação é no nível de projeto/serviço por predefinição, portanto um projeto de biblioteca que está a utilizar não é possível encontrar.

### <a name="try"></a>Experimente:
O que precisa ser feito:
1. Modificar a _azds.yaml_ ficheiro para definir o contexto de compilação para o nível de solução.
2. Modificar a _Dockerfile_ e _Dockerfile.develop_ arquivos para fazer referência ao projeto (_. csproj_) ficheiros corretamente, em relação ao novo criar contexto.
3. Lugar uma _.dockerignore_ arquivo ao lado do ficheiro. sln e modifique conforme necessário.

Pode encontrar um exemplo em https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>Erro de autorização 'Microsoft.DevSpaces/register/action'
Precisa *proprietário* ou *contribuinte* acesso na sua subscrição do Azure para gerir espaços de desenvolvimento do Azure. Poderá ver este erro se estiver a tentar gerir espaços de desenvolvimento e não tiver *proprietário* ou *contribuinte* acesso à subscrição do Azure associada.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Razão
A subscrição do Azure selecionada não se registou o `Microsoft.DevSpaces` espaço de nomes.

### <a name="try"></a>Experimente:
Qualquer pessoa com acesso de proprietário ou contribuinte à subscrição do Azure pode executar o seguinte comando da CLI do Azure para registar manualmente o `Microsoft.DevSpaces` espaço de nomes:

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="dev-spaces-times-out-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Espaços de desenvolvimento exceder o tempo limite em *a aguardar a criação da imagem do contentor...*  passo connosco virtuais de AKS

### <a name="reason"></a>Razão
Isto ocorre quando está tentando utilizar espaços de desenvolvimento para executar um serviço que está configurado para ser executado [nó virtual de AKS](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Espaços de desenvolvimento não suporta atualmente a criar ou a depuração de serviços em nós virtuais.

Se executar `azds up` com o `--verbose` comutador ou ativar o registo verboso no Visual Studio, verá detalhes adicionais:

```cmd
Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

Isto mostra que o pod do serviço foi atribuído a *virtual-nó-aci-linux*, que é um nó virtual.

### <a name="try"></a>Experimente:
Atualizar o gráfico Helm para o serviço de remover quaisquer *nodeSelector* e/ou *tolerations* valores que permitem que o serviço para executar num nó virtual. Estes valores normalmente são definidos do gráfico `values.yaml` ficheiro.

Pode continuar a utilizar um cluster do AKS que tenha a funcionalidade de nós virtuais ativada, se o serviço que pretende depuração/compilação por meio de espaços de desenvolvimento é executado num nó VM. Esta é a configuração predefinida.

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>"Erro: não foi possível encontrar um pod tiller pronto" ao iniciar o desenvolvimento espaços

### <a name="reason"></a>Razão
Este erro ocorre se o cliente do Helm já não pode comunicar com o pod Tiller em execução no cluster.

### <a name="try"></a>Experimente:
Reiniciar os nós de agente no seu cluster normalmente resolve este problema.

## <a name="azure-dev-spaces-proxy-can-interfere-with-other-pods-running-in-a-dev-space"></a>Proxy de espaços de desenvolvimento do Azure pode interferir com outras pods em execução num espaço de desenvolvimento

### <a name="reason"></a>Razão
Quando ativar espaços de desenvolvimento num espaço de nomes no seu cluster do AKS, um contentor adicional chamado _mindaro proxy_ está instalado em cada um dos pods em execução dentro desse espaço de nomes. Este contentor intercepta as chamadas para os serviços no pod, que é integral para recursos de desenvolvimento de equipe dos espaços de desenvolvimento; No entanto, ele pode interferir com determinados serviços em execução nos pods. Ele é conhecido interfira com pods em execução Azure Cache de Redis, fazendo com que os erros de ligação e falhas de comunicação de mestre/subordinado.

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
