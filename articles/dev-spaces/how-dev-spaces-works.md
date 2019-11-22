---
title: Como Azure Dev Spaces funciona e está configurado
services: azure-dev-spaces
ms.date: 03/04/2019
ms.topic: conceptual
description: Descreve os processos que o Power Azure Dev Spaces e como eles são configurados no arquivo de configuração azds. YAML
keywords: azds. YAML, Azure Dev Spaces, espaços de desenvolvimento, Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres
ms.openlocfilehash: 9efae0e9d6bc53e08dce604fa79aa29e158ecabd
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280140"
---
# <a name="how-azure-dev-spaces-works-and-is-configured"></a>Como Azure Dev Spaces funciona e está configurado

O desenvolvimento de um aplicativo kubernetes pode ser desafiador. Você precisa de arquivos de configuração do Docker e do kubernetes. Você precisa descobrir como testar seu aplicativo localmente e interagir com outros serviços dependentes. Talvez seja necessário manipular o desenvolvimento e os testes em vários serviços de uma só vez e com uma equipe de desenvolvedores.

Azure Dev Spaces ajuda a desenvolver, implantar e depurar aplicativos kubernetes diretamente no AKS (serviço kubernetes do Azure). Azure Dev Spaces também permite que uma equipe Compartilhe um espaço de desenvolvimento. Compartilhar um espaço de desenvolvimento em uma equipe permite que os membros individuais da equipe desenvolvam isoladamente sem a necessidade de replicar ou simular dependências ou outros aplicativos no cluster.

O Azure Dev Spaces cria e usa um arquivo de configuração para implantar, executar e depurar seus aplicativos kubernetes no AKS. Esse arquivo de configuração reside com o código do aplicativo e pode ser adicionado ao seu sistema de controle de versão.

Este artigo descreve os processos que o Power Azure Dev Spaces e como esses processos são configurados no arquivo de configuração do Azure Dev Spaces. Para obter Azure Dev Spaces em execução rapidamente e vê-lo na prática, conclua um dos guias de início rápido:

* [Java com CLI e Visual Studio Code](quickstart-java.md)
* [.NET Core com CLI e Visual Studio Code](quickstart-netcore.md)
* [.NET Core com Visual Studio](quickstart-netcore-visualstudio.md)
* [Node. js com CLI e Visual Studio Code](quickstart-nodejs.md)

## <a name="how-azure-dev-spaces-works"></a>Como funciona o Azure Dev Spaces

Azure Dev Spaces tem dois componentes distintos com os quais você interage: o controlador e as ferramentas do lado do cliente.

![Componentes do Azure Dev Spaces](media/how-dev-spaces-works/components.svg)

O controlador executa as seguintes ações:

* Gerencia a criação e a seleção de espaço de desenvolvimento.
* Instala o gráfico Helm do seu aplicativo e cria objetos kubernetes.
* Compila a imagem de contêiner do aplicativo.
* Implanta seu aplicativo no AKS.
* O incrementais cria e reinicia quando o código-fonte é alterado.
* Gerencia logs e rastreamentos HTTP.
* Encaminha stdout e stderr para as ferramentas do lado do cliente.
* Permite que os membros da equipe criem espaços de desenvolvimento filhos derivados de um espaço de desenvolvimento pai.
* Configura o roteamento de aplicativos dentro de um espaço, bem como entre os espaços pai e filho.

O controlador reside fora do AKS. Ele orienta o comportamento e a comunicação entre as ferramentas do lado do cliente e o cluster AKS. O controlador é habilitado usando o CLI do Azure quando você prepara o cluster para usar Azure Dev Spaces. Quando ele estiver habilitado, você poderá interagir com ele usando as ferramentas do lado do cliente.

As ferramentas do lado do cliente permitem que o usuário:
* Gere um Dockerfile, um gráfico do Helm e um arquivo de configuração Azure Dev Spaces para o aplicativo.
* Crie espaços de desenvolvimento pai e filho.
* Peça ao controlador para criar e iniciar seu aplicativo.

Enquanto seu aplicativo está em execução, as ferramentas do lado do cliente também:
* Recebe e exibe stdout e stderr de seu aplicativo em execução no AKS.
* Usa [porta-encaminhar](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) para permitir acesso à Web ao seu aplicativo usando http:\//localhost.
* Anexa um depurador ao seu aplicativo em execução no AKS.
* Sincroniza o código-fonte para o espaço de desenvolvimento quando uma alteração é detectada para compilações incrementais, permitindo iteração rápida.

Você pode usar as ferramentas do lado do cliente na linha de comando como parte do comando `azds`. Você também pode usar as ferramentas do lado do cliente com:

* Visual Studio Code usando a [extensão Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).
* Visual Studio com [Ferramentas do Visual Studio para kubernetes](https://aka.ms/get-vsk8stools).

Este é o fluxo básico para configurar e usar Azure Dev Spaces:
1. Preparar o cluster AKS para Azure Dev Spaces
1. Prepare seu código para execução em Azure Dev Spaces
1. Executar seu código em um espaço de desenvolvimento
1. Depurar seu código em um espaço de desenvolvimento
1. Compartilhar um espaço de desenvolvimento

Abordaremos mais detalhes sobre como Azure Dev Spaces funciona em cada uma das seções abaixo.

## <a name="prepare-your-aks-cluster"></a>Preparar o cluster AKS

Preparar o cluster AKS envolve:
* Verificar se o cluster AKS está em uma região [com suporte pelo Azure dev Spaces][supported-regions].
* Verificando se você está executando o kubernetes 1.10.3 ou posterior.
* Habilitando Azure Dev Spaces no cluster usando `az aks use-dev-spaces`

Para obter mais informações sobre como criar e configurar um cluster AKS para Azure Dev Spaces, consulte um dos guias de introdução:
* [Introdução ao Azure Dev Spaces com Java](get-started-java.md)
* [Introdução ao Azure Dev Spaces com o .NET Core e o Visual Studio](get-started-netcore-visualstudio.md)
* [Introdução ao Azure Dev Spaces com o .NET Core](get-started-netcore.md)
* [Introdução ao Azure Dev Spaces com node. js](get-started-nodejs.md)

Quando Azure Dev Spaces está habilitado no cluster AKS, ele instala o controlador para o cluster. O controlador é um recurso do Azure separado fora do seu cluster e faz o seguinte para os recursos em seu cluster:

* Cria ou designa um namespace kubernetes para usar como um espaço de desenvolvimento.
* Remove qualquer namespace kubernetes chamado *azds*, se ele existir, e cria um novo.
* Implanta uma configuração de webhook kubernetes.
* Implanta um servidor de admissão de webhook.
    

Ele também usa a mesma entidade de serviço que seu cluster AKS usa para fazer chamadas de serviço para outros componentes de Azure Dev Spaces.

![Azure Dev Spaces preparar o cluster](media/how-dev-spaces-works/prepare-cluster.svg)

Para usar Azure Dev Spaces, deve haver pelo menos um espaço de desenvolvimento. Azure Dev Spaces usa namespaces kubernetes no cluster AKS para espaços de desenvolvimento. Quando um controlador está sendo instalado, ele solicita que você crie um novo namespace kubernetes ou escolha um namespace existente para usar como seu primeiro espaço de desenvolvimento. Quando um namespace é designado como um espaço de desenvolvimento, o controlador adiciona o rótulo *azds.Io/Space=true* a esse namespace para identificá-lo como um espaço de desenvolvimento. O espaço de desenvolvimento inicial que você criar ou designar será selecionado por padrão depois que você preparar o cluster. Quando um espaço é selecionado, ele é usado pelo Azure Dev Spaces para criar novas cargas de trabalho.

Por padrão, o controlador cria um espaço de desenvolvimento denominado *padrão* atualizando o namespace kubernetes *padrão* existente. Você pode usar as ferramentas do lado do cliente para criar novos espaços de desenvolvimento e remover os espaços de desenvolvimento existentes. Devido a uma limitação no kubernetes, o espaço de desenvolvimento *padrão* não pode ser removido. O controlador também remove os namespaces kubernetes existentes chamados *azds* para evitar conflitos com o comando `azds` usado pelas ferramentas do lado do cliente.

O servidor de admissão do webhook kubernetes é usado para injetar pods com três contêineres durante a implantação para instrumentação: um contêiner devspaces-proxy, um contêiner devspaces-proxy-init e um contêiner devspaces-Build. **Todos esses três contêineres são executados com acesso raiz no cluster AKS.** Eles também usam a mesma entidade de serviço que seu cluster AKS usa para fazer chamadas de serviço para outros componentes de Azure Dev Spaces.

![Azure Dev Spaces servidor de admissão do webhook do kubernetes](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

O contêiner devspaces-proxy é um contêiner sidecar que manipula todo o tráfego TCP para dentro e fora do contêiner do aplicativo e ajuda a executar o roteamento. O contêiner devspaces-proxy redireciona mensagens HTTP se determinados espaços estiverem sendo usados. Por exemplo, ele pode ajudar a rotear mensagens HTTP entre aplicativos em espaços pai e filho. Todo o tráfego não HTTP passa pelo devspaces-proxy sem modificações. O contêiner devspaces-proxy também registra todas as mensagens HTTP de entrada e saída e as envia para as ferramentas do lado do cliente como rastreamentos. Esses rastreamentos podem ser exibidos pelo desenvolvedor para inspecionar o comportamento do aplicativo.

O contêiner devspaces-proxy-init é um [contêiner de inicialização](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) que adiciona mais regras de roteamento com base na hierarquia de espaço ao contêiner do seu aplicativo. Ele adiciona regras de roteamento atualizando o arquivo */etc/resolv.conf* do contêiner do aplicativo e a configuração do iptables antes de iniciar. As atualizações para */etc/resolv.conf* permitem a resolução de DNS de serviços em espaços pai. As atualizações de configuração do iptables garantem que todo o tráfego TCP dentro e fora do contêiner do aplicativo seja roteado, por meio do devspaces-proxy. Todas as atualizações de devspaces-proxy-init acontecem além das regras que o kubernetes adiciona.

O contêiner devspaces-Build é um contêiner init e tem o código-fonte do projeto e o soquete do Docker montado. O código-fonte do projeto e o acesso ao Docker permitem que o contêiner de aplicativo seja criado diretamente pelo Pod.

> [!NOTE]
> Azure Dev Spaces usa o mesmo nó para compilar o contêiner do aplicativo e executá-lo. Como resultado, Azure Dev Spaces não precisa de um registro de contêiner externo para compilar e executar seu aplicativo.

O servidor de admissão do webhook kubernetes escuta qualquer novo pod criado no cluster AKS. Se esse Pod for implantado em qualquer namespace com o rótulo *azds.Io/Space=true* , ele injetará esse Pod com os contêineres adicionais. O contêiner devspaces-Build será injetado apenas se o contêiner do aplicativo for executado usando as ferramentas do lado do cliente.

Depois de preparar o cluster do AKS, você poderá usar as ferramentas do lado do cliente para preparar e executar seu código no espaço de desenvolvimento.

## <a name="prepare-your-code"></a>Preparar seu código

Para executar seu aplicativo em um espaço de desenvolvimento, ele precisa estar em contêiner e você precisa definir como ele deve ser implantado no kubernetes. Para colocar seu aplicativo em contêiner, você precisa de um Dockerfile. Para definir como seu aplicativo é implantado no kubernetes, você precisa de um [gráfico do Helm](https://docs.helm.sh/). Para auxiliar na criação do gráfico Dockerfile e Helm para seu aplicativo, as ferramentas do lado do cliente fornecem o comando `prep`:

```cmd
azds prep --public
```

O comando `prep` examinará os arquivos em seu projeto e tentará criar o gráfico Dockerfile e Helm para executar seu aplicativo no kubernetes. Atualmente, o comando `prep` irá gerar um gráfico Dockerfile e Helm com os seguintes idiomas:

* Java
* Node.js
* .NET Core

Você *deve* executar o comando `prep` de um diretório que contém o código-fonte. A execução do comando `prep` do diretório correto permite que as ferramentas do lado do cliente identifiquem o idioma e criem um Dockerfile apropriado para colocar seu aplicativo em contêiner. Você também pode executar o comando `prep` de um diretório que contém um arquivo *pom. xml* para projetos Java.

Se você executar o comando `prep` do diretório que não contém o código-fonte, as ferramentas do lado do cliente não gerarão um Dockerfile. Ele também exibirá um erro dizendo: *Dockerfile não pôde ser gerado devido a um idioma sem suporte*. Esse erro também ocorrerá se as ferramentas do lado do cliente não reconhecerem o tipo de projeto.

Ao executar o comando `prep`, você tem a opção de especificar o sinalizador `--public`. Esse sinalizador informa o controlador para criar um ponto de extremidade acessível pela Internet para esse serviço. Se você não especificar esse sinalizador, o serviço só poderá ser acessado de dentro do cluster ou usando o túnel localhost criado pelas ferramentas do lado do cliente. Você pode habilitar ou desabilitar esse comportamento depois de executar o comando `prep` atualizando o gráfico Helm gerado.

O comando `prep` não substituirá nenhum gráfico Dockerfiles ou Helm existente que você tenha em seu projeto. Se um gráfico Dockerfile ou Helm existente usar a mesma convenção de nomenclatura que os arquivos gerados pelo comando `prep`, o comando `prep` ignorará a geração desses arquivos. Caso contrário, o comando `prep` irá gerar seu próprio gráfico Dockerfile ou Helm ao lado dos arquivos existentes.

O comando `prep` também irá gerar um arquivo de `azds.yaml` na raiz do seu projeto. Azure Dev Spaces usa esse arquivo para compilar, instalar, configurar e executar seu aplicativo. Esse arquivo de configuração lista o local do seu gráfico Dockerfile e Helm e também fornece configuração adicional sobre esses artefatos.

Aqui está um exemplo de arquivo azds. YAML criado usando o [aplicativo de exemplo .NET Core](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend):

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: .
  dockerfile: Dockerfile
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
        # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
        # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
        # For more information see https://aka.ms/devspaces/routing
        - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
```

O arquivo de `azds.yaml` gerado pelo comando `prep` deve funcionar bem para um único cenário de desenvolvimento de projeto simples. Se o seu projeto específico tiver aumentado a complexidade, talvez seja necessário atualizar esse arquivo depois de executar o comando `prep`. Por exemplo, seu projeto pode exigir algum ajuste para seu processo de compilação ou inicialização com base nas suas necessidades de desenvolvimento ou de depuração. Você também pode ter vários aplicativos em seu projeto, que exigem vários processos de compilação ou um conteúdo de compilação diferente.

## <a name="run-your-code"></a>Executar seu código

Para executar seu código em um espaço de desenvolvimento, emita o comando `up` no mesmo diretório que o arquivo de `azds.yaml`:

```cmd
azds up
```

O comando `up` carrega os arquivos de origem do aplicativo e outros artefatos necessários para compilar e executar seu projeto no espaço de desenvolvimento. A partir daí, o controlador em seu espaço de desenvolvimento:

1. Cria os objetos kubernetes para implantar seu aplicativo.
1. Cria o contêiner para seu aplicativo.
1. Implanta seu aplicativo no espaço do dev.
1. Cria um nome DNS acessível publicamente para o ponto de extremidade do aplicativo, se configurado.
1. Usa o *encaminhamento de porta* para fornecer acesso ao ponto de extremidade do aplicativo usando http://localhost.
1. Encaminha stdout e stderr para as ferramentas do lado do cliente.


### <a name="starting-a-service"></a>Iniciando um serviço

Quando você inicia um serviço em um espaço de desenvolvimento, o controlador e as ferramentas do lado do cliente funcionam em coordenação para sincronizar os arquivos de origem, criar seus objetos de contêiner e kubernetes e executar seu aplicativo.

Em um nível mais granular, aqui está o que acontece quando você executa `azds up`:

1. Os arquivos são sincronizados da máquina do usuário para um armazenamento de arquivos do Azure que é exclusivo para o cluster AKS do usuário. O código-fonte, o gráfico Helm e os arquivos de configuração são carregados. Mais detalhes sobre o processo de sincronização estão disponíveis na próxima seção.
1. O controlador cria uma solicitação para iniciar uma nova sessão. Essa solicitação contém várias propriedades, incluindo uma ID exclusiva, o nome do espaço, o caminho para o código-fonte e um sinalizador de depuração.
1. O controlador substitui o espaço reservado *$ (tag)* no gráfico Helm com a ID de sessão exclusiva e instala o gráfico Helm para o serviço. A adição de uma referência à ID de sessão exclusiva ao gráfico Helm permite que o contêiner implantado no cluster AKS para essa sessão específica seja vinculado à solicitação de sessão e às informações associadas.
1. Durante a instalação do gráfico do Helm, o servidor de admissão do webhook do kubernetes adiciona contêineres adicionais ao Pod do seu aplicativo para instrumentação e acesso ao código-fonte do seu projeto. Os contêineres devspaces-proxy e devspaces-proxy-init são adicionados para fornecer roteamento de HTTP e rastreamento de espaço. O contêiner devspaces-Build é adicionado para fornecer o Pod com acesso à instância do Docker e ao código-fonte do projeto para criar o contêiner do aplicativo.
1. Quando o Pod do aplicativo é iniciado, o contêiner devspaces-Build e o contêiner devspaces-proxy-init são usados para criar o contêiner do aplicativo. O contêiner de aplicativo e os contêineres devspaces-proxy são iniciados.
1. Depois que o contêiner de aplicativo é iniciado, a funcionalidade do lado do cliente usa a funcionalidade de *encaminhamento de porta* kubernetes para fornecer acesso http ao seu aplicativo em http://localhost. Esse encaminhamento de porta conecta seu computador de desenvolvimento ao serviço em seu espaço de desenvolvimento.
1. Quando todos os contêineres no pod começaram, o serviço está em execução. Neste ponto, a funcionalidade do lado do cliente começa a transmitir os rastreamentos HTTP, stdout e stderr. Essas informações são exibidas pela funcionalidade do lado do cliente para o desenvolvedor.

### <a name="updating-a-running-service"></a>Atualizando um serviço em execução

Enquanto um serviço estiver em execução, Azure Dev Spaces terá a capacidade de atualizar esse serviço se qualquer um dos arquivos de origem do projeto for alterado. Os espaços de desenvolvimento também lidam com a atualização do serviço de forma diferente, dependendo do tipo de arquivo alterado. Há três maneiras de os espaços de desenvolvimento podem atualizar um serviço em execução:

* Atualizando um arquivo diretamente
* Recompilando e reiniciando o processo do aplicativo dentro do contêiner do aplicativo em execução
* Recompilando e reimplantando o contêiner do aplicativo

![Sincronização de arquivos de Azure Dev Spaces](media/how-dev-spaces-works/file-sync.svg)

Determinados arquivos de projeto que são ativos estáticos, como arquivos HTML, CSS e cshtml, podem ser atualizados diretamente no contêiner do aplicativo sem reiniciar nada. Se um ativo estático for alterado, o novo arquivo será sincronizado com o espaço de desenvolvimento e, em seguida, usado pelo contêiner em execução.

Alterações em arquivos como código-fonte ou arquivos de configuração do aplicativo podem ser aplicadas reiniciando o processo do aplicativo no contêiner em execução. Depois que esses arquivos são sincronizados, o processo do aplicativo é reiniciado dentro do contêiner em execução usando o processo *devhostagent* . Ao criar inicialmente o contêiner do aplicativo, o controlador substitui o comando de inicialização do aplicativo por um processo diferente chamado *devhostagent*. Em seguida, o processo real do aplicativo é executado como um processo filho em *devhostagent*, e sua saída é canalizada usando a saída de *devhostagent*. O processo *devhostagent* também faz parte dos espaços de desenvolvimento e pode executar comandos no contêiner em execução em nome dos espaços de desenvolvimento. Ao executar uma reinicialização, *devhostagent*:

* Interrompe o processo atual ou os processos associados ao aplicativo
* Recria o aplicativo
* Reinicia o processo ou os processos associados ao aplicativo

A maneira como o *devhostagent* executa as etapas anteriores é configurado no arquivo de configuração `azds.yaml`. Essa configuração é detalhada em uma seção posterior.

As atualizações para arquivos de projeto como Dockerfiles, arquivos csproj ou qualquer parte do gráfico Helm exigem que o contêiner do aplicativo seja recriado e reimplantado. Quando um desses arquivos é sincronizado com o espaço de desenvolvimento, o controlador executa o comando de [atualização Helm](https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure) e o contêiner do aplicativo é recriado e reimplantado.

### <a name="file-synchronization"></a>Sincronização de arquivos

Na primeira vez que um aplicativo é iniciado em um espaço de desenvolvimento, todos os arquivos de origem do aplicativo são carregados. Enquanto o aplicativo está em execução e depois é reiniciado, somente os arquivos alterados são carregados. Dois arquivos são usados para coordenar esse processo: um arquivo do lado do cliente e um arquivo do lado do controlador.

O arquivo do lado do cliente é armazenado em um diretório temporário e é nomeado com base em um hash do diretório do projeto que você está executando em espaços de desenvolvimento. Por exemplo, no Windows, você teria um arquivo como *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* para seu projeto. No Linux, o arquivo do lado do cliente é armazenado no diretório */tmp* . Você pode encontrar o diretório no macOS executando o comando `echo $TMPDIR`.

Este arquivo está no formato JSON e contém:

* Uma entrada para cada arquivo de projeto que é sincronizado com o espaço de desenvolvimento
* Uma ID de sincronização
* O carimbo de data/hora da última operação de sincronização

Cada entrada de arquivo de projeto contém um caminho para o arquivo e seu carimbo de data/hora.

O arquivo do lado do controlador é armazenado no cluster AKS. Ele contém a ID de sincronização e o carimbo de data/hora da última sincronização.

Uma sincronização ocorre quando os carimbos de data/hora de sincronização não coincidem entre os arquivos do lado do cliente e do controlador. Durante uma sincronização, as ferramentas do lado do cliente são iteradas sobre as entradas de arquivo no arquivo do lado do cliente. Se o carimbo de data/hora do arquivo for posterior ao carimbo de data/hora de sincronização, esse arquivo será sincronizado com o espaço de desenvolvimento. Depois que a sincronização for concluída, os carimbos de data/hora de sincronização serão atualizados nos arquivos do lado do cliente e do controlador.

Todos os arquivos de projeto serão sincronizados se o arquivo do lado do cliente não estiver presente. Esse comportamento permite forçar uma sincronização completa excluindo o arquivo do lado do cliente.

### <a name="how-routing-works"></a>Como funciona o roteamento

Um espaço de desenvolvimento é criado sobre o AKS e usa os mesmos [conceitos de rede](../aks/concepts-network.md). Azure Dev Spaces também tem um serviço de *ingressmanager* centralizado e implanta seu próprio controlador de entrada no cluster AKs. O serviço *ingressmanager* monitora clusters AKs com espaços de desenvolvimento e aumenta o controlador de entrada Azure dev Spaces no cluster com objetos de entrada para roteamento para pods de aplicativo. O contêiner devspaces-proxy em cada pod adiciona um `azds-route-as` cabeçalho HTTP para o tráfego HTTP para um espaço de desenvolvimento com base na URL. Por exemplo, uma solicitação para a URL *http://azureuser.s.default.serviceA.fedcba09...azds.io* obteria um cabeçalho HTTP com `azds-route-as: azureuser`. O contêiner devspaces-proxy não adicionará um cabeçalho de `azds-route-as`, se já houver um.

Quando uma solicitação HTTP é feita a um serviço de fora do cluster, a solicitação vai para o controlador de entrada. O controlador de entrada roteia a solicitação diretamente para o Pod apropriado com base em seus objetos de entrada e regras. O contêiner devspaces-proxy no pod recebe a solicitação, adiciona o cabeçalho `azds-route-as` com base na URL e, em seguida, roteia a solicitação para o contêiner do aplicativo.

Quando uma solicitação HTTP é feita a um serviço de outro serviço dentro do cluster, a solicitação primeiro passa pelo contêiner devspaces-proxy do serviço de chamada. O contêiner devspaces-proxy examina a solicitação HTTP e verifica o cabeçalho `azds-route-as`. Com base no cabeçalho, o contêiner devspaces-proxy pesquisará o endereço IP do serviço associado ao valor do cabeçalho. Se um endereço IP for encontrado, o contêiner devspaces-proxy redirecionará a solicitação para esse endereço IP. Se um endereço IP não for encontrado, o contêiner devspaces-proxy roteará a solicitação para o contêiner do aplicativo pai.

Por exemplo, os aplicativos *servicea* e *serviceB* são implantados em um espaço de desenvolvimento pai chamado *padrão*. o *servicea* se baseia em *serviceB* e faz chamadas http para ele. O usuário do Azure cria um espaço de desenvolvimento filho com base no espaço *padrão* chamado *azureuser*. O usuário do Azure também implanta sua própria versão do *servicea* em seu espaço filho. Quando uma solicitação é feita para *http://azureuser.s.default.serviceA.fedcba09...azds.io* :

![Roteamento de Azure Dev Spaces](media/how-dev-spaces-works/routing.svg)

1. O controlador de entrada procura o IP para o Pod associado à URL, que é *servicea. azureuser*.
1. O controlador de entrada localiza o IP para o pod no espaço de desenvolvimento do usuário do Azure e roteia a solicitação para o Pod *servicea. azureuser* .
1. O contêiner devspaces-proxy no pod *servicea. azureuser* recebe a solicitação e adiciona `azds-route-as: azureuser` como um cabeçalho http.
1. O contêiner devspaces-proxy no pod *servicea. azureuser* roteia a solicitação para o contêiner do aplicativo *servicea* no pod *servicea. azureuser* .
1. O aplicativo *servicea* no pod *servicea. azureuser* faz uma chamada para *serviceB*. O aplicativo *servicea* também contém código para preservar o cabeçalho de `azds-route-as` existente, que nesse caso é `azds-route-as: azureuser`.
1. O contêiner devspaces-proxy no pod *servicea. azureuser* recebe a solicitação e pesquisa o IP de *serviceB* com base no valor do cabeçalho `azds-route-as`.
1. O contêiner devspaces-proxy no pod *servicea. azureuser* não encontra um IP para *serviceB. azureuser*.
1. O contêiner devspaces-proxy no pod *servicea. azureuser* procura o IP para *serviceB* no espaço pai, que é *serviceB. Default*.
1. O contêiner devspaces-proxy no pod *servicea. azureuser* localiza o IP de *serviceB. Default* e roteia a solicitação para o Pod *serviceB. Default* .
1. O contêiner devspaces-proxy no *serviceB.* o Pod padrão recebe a solicitação e roteia a solicitação para o contêiner do aplicativo *ServiceB* no pod *serviceB. Default* .
1. O aplicativo *serviceB* no *serviceB. Pod padrão* retorna uma resposta para o Pod *servicea. azureuser* .
1. O contêiner devspaces-proxy no pod *servicea. azureuser* recebe a resposta e roteia a resposta para o contêiner do aplicativo *servicea* no pod *servicea. azureuser* .
1. O aplicativo *servicea* recebe a resposta e, em seguida, retorna sua própria resposta.
1. O contêiner devspaces-proxy no pod *servicea. azureuser* recebe a resposta do contêiner de aplicativos *servicea* e roteia a resposta para o chamador original fora do cluster.

Todos os outros tráfegos TCP que não são HTTP passam pelo controlador de entrada e contêineres devspaces-proxy não modificados.

### <a name="how-running-your-code-is-configured"></a>Como a execução do código é configurada

Azure Dev Spaces usa o arquivo `azds.yaml` para instalar e configurar seu serviço. O controlador usa a propriedade `install` no arquivo `azds.yaml` para instalar o gráfico Helm e criar os objetos kubernetes:

```yaml
...
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
      # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
      # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
      # For more information see https://aka.ms/devspaces/routing
      - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
...
```

Por padrão, o comando `prep` irá gerar o gráfico Helm. Ele também define a propriedade *install. Chart* como o diretório do gráfico Helm. Se você quisesse usar um gráfico de Helm em um local diferente, você pode atualizar essa propriedade para usar esse local.

Ao instalar os gráficos do Helm, Azure Dev Spaces fornece uma maneira de substituir valores no gráfico do Helm. Os valores padrão para o gráfico Helm estão em `charts/APP_NAME/values.yaml`.

Usando a propriedade *install. Values* , é possível listar um ou mais arquivos que definem os valores que você deseja substituir no gráfico Helm. Por exemplo, se você quisesse uma configuração de nome de host ou de banco de dados especificamente ao executar seu aplicativo em um espaço de desenvolvimento, você pode usar essa funcionalidade de substituição. Você também pode adicionar um *?* no final de qualquer um dos nomes de arquivo para defini-lo como opcional.

A propriedade *install. Set* permite configurar um ou mais valores que você deseja substituir no gráfico Helm. Quaisquer valores configurados em *install. Set* substituirão os valores configurados em arquivos listados em *install. Values*. As propriedades em *install. Set* dependem dos valores no gráfico Helm e podem ser diferentes dependendo do gráfico Helm gerado.

No exemplo acima, a propriedade *install. set. replicaCount* informa ao controlador quantas instâncias do seu aplicativo executar em seu espaço de desenvolvimento. Dependendo do seu cenário, você pode aumentar esse valor, mas terá um impacto na anexação de um depurador ao Pod do seu aplicativo. Para obter mais informações, consulte o [artigo solução de problemas](troubleshooting.md).

No gráfico Helm gerado, a imagem de contêiner é definida como *{{. Values. Image. Repository}}: {{. Values. Image. Tag}}* . O arquivo `azds.yaml` define a propriedade *install. set. Image. Tag* como *$ (tag)* por padrão, que é usada como o valor de *{{. Values. Image. Tag}}* . Ao definir a propriedade *install. set. Image. Tag* dessa forma, ela permite que a imagem de contêiner do seu aplicativo seja marcada de forma distinta durante a execução de Azure dev Spaces. Nesse caso específico, a imagem é marcada como *\<valor de Image. repository >: $ (tag)* . Você deve usar a variável *$ (tag)* como o valor de *install. set. Image. Tag* para que os espaços de desenvolvimento reconheçam e localizem o contêiner no cluster AKs.

No exemplo acima, `azds.yaml` define *install. set. ingress. hosts*. A propriedade *install. set. ingresso. hosts* define um formato de nome de host para pontos de extremidade públicos. Essa propriedade também usa *$ (spacePrefix)* , *$ (rootSpacePrefix)* e *$ (hostSuffix)* , que são os valores fornecidos pelo controlador. 

*$ (SpacePrefix)* é o nome do espaço de desenvolvimento filho, que assume a forma de *spacename. s*. O *$ (rootSpacePrefix)* é o nome do espaço pai. Por exemplo, se *azureuser* for um espaço filho de *padrão*, o valor de *$ (rootSpacePrefix)* será *padrão* e o valor de *$ (spacePrefix)* será *azureuser. s*. Se o espaço não for um espaço filho, *$ (spacePrefix)* ficará em branco. Por exemplo, se o espaço *padrão* não tiver espaço pai, o valor de *$ (rootSpacePrefix)* será *padrão* e o valor de *$ (spacePrefix)* ficará em branco. *$ (HostSuffix)* é um sufixo DNS que aponta para o controlador de entrada Azure dev Spaces que é executado em seu cluster AKs. Esse sufixo DNS corresponde a uma entrada DNS de caractere curinga, por exemplo *\*. RANDOM_VALUE. eus. azds. Io*, que foi criado quando o controlador de Azure dev Spaces foi adicionado ao cluster AKs.

No arquivo de `azds.yaml` acima, você também pode atualizar *install. set. ingress. hosts* para alterar o nome do host do seu aplicativo. Por exemplo, se você quisesse simplificar o nome do host do seu aplicativo do *$ (spacePrefix) $ (rootSpacePrefix) WebFrontEnd $ (hostSuffix)* para *$ (spacePrefix) $ (rootSpacePrefix) Web $ (hostSuffix*).

Para criar o contêiner para seu aplicativo, o controlador usa as seções abaixo do arquivo de configuração `azds.yaml`:

```yaml
build:
  context: .
  dockerfile: Dockerfile
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
...
```

O controlador usa um Dockerfile para compilar e executar seu aplicativo.

A propriedade *Build. Context* lista o diretório onde o Dockerfiles existe. A propriedade *Build. dockerfile* define o nome do dockerfile para criar a versão de produção do aplicativo. A propriedade *configurações. desenvolver. Build. dockerfile* configura o nome do dockerfile para a versão de desenvolvimento do aplicativo.

Ter diferentes Dockerfiles para desenvolvimento e produção permite que você habilite determinadas coisas durante o desenvolvimento e desabilite esses itens para implantações de produção. Por exemplo, você pode habilitar a depuração ou o log mais detalhado durante o desenvolvimento e a desabilitação em um ambiente de produção. Você também poderá atualizar essas propriedades se suas Dockerfiles forem nomeadas de forma diferente ou estiverem em um local diferente.

Para ajudá-lo a iterar rapidamente durante o desenvolvimento, Azure Dev Spaces sincronizará as alterações de seu projeto local e atualizará o aplicativo de forma incremental. A seção abaixo no arquivo de configuração `azds.yaml` é usada para configurar a sincronização e a atualização:

```yaml
...
configurations:
  develop:
    ...
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
...
```

Os arquivos e diretórios que sincronizarão as alterações serão listados na propriedade *configurações. desenvolver. Container. Sync* . Esses diretórios são sincronizados inicialmente quando você executa o comando `up`, bem como quando as alterações são detectadas. Se houver diretórios adicionais ou diferentes que você gostaria de sincronizar com o espaço de desenvolvimento, você poderá alterar essa propriedade.

A propriedade *configurações. desenvolver. Container. iterate. buildCommands* especifica como compilar o aplicativo em um cenário de desenvolvimento. A propriedade *Configurations. desenvolver. Container. Command* fornece o comando para executar o aplicativo em um cenário de desenvolvimento. Talvez você queira atualizar qualquer uma dessas propriedades se houver sinalizadores ou parâmetros de tempo de execução ou de compilação adicionais que deseja usar durante o desenvolvimento.

As *configurações. desenvolver. Container. iterate. processesToKill* lista os processos a serem eliminados para interromper o aplicativo. Talvez você queira atualizar essa propriedade se desejar alterar o comportamento de reinicialização do seu aplicativo durante o desenvolvimento. Por exemplo, se você atualizou as propriedades *configurações. desenvolver. Container. iterate. buildCommands* ou *configurações. desenvolver. Container. Command* para alterar a forma como o aplicativo é criado ou iniciado, talvez seja necessário alterar quais processos são interrompidos.

Ao preparar seu código usando o comando `azds prep`, você tem a opção de adicionar o sinalizador `--public`. Adicionar o sinalizador `--public` cria uma URL publicamente acessível para seu aplicativo. Se você omitir esse sinalizador, o aplicativo só estará acessível no cluster ou usando o túnel localhost. Depois de executar o comando `azds prep`, você pode alterar essa configuração, modificando a propriedade *entrada. Enabled* no `charts/APPNAME/values.yaml`:

```yaml
ingress:
  enabled: true
```

## <a name="debug-your-code"></a>Depurar seu código

Para aplicativos Java, .NET e node. js, você pode depurar seu aplicativo em execução diretamente em seu espaço de desenvolvimento usando o Visual Studio Code ou o Visual Studio. Visual Studio Code e o Visual Studio fornecem ferramentas para se conectar ao seu espaço de desenvolvimento, iniciar o aplicativo e anexar um depurador. Depois de executar `azds prep`, você pode abrir seu projeto no Visual Studio Code ou no Visual Studio. Visual Studio Code ou o Visual Studio gerará seus próprios arquivos de configuração para conexão, que é separado da execução de `azds prep`. De dentro do Visual Studio Code ou do Visual Studio, você pode definir pontos de interrupção e iniciar seu aplicativo para o espaço de desenvolvimento.

![Depurando seu código](media/get-started-node/debug-configuration-nodejs2.png)

Quando você inicia seu aplicativo usando Visual Studio Code ou o Visual Studio para depuração, eles lidam com a inicialização e a conexão ao seu espaço de desenvolvimento da mesma maneira que a execução de `azds up`. As ferramentas do lado do cliente no Visual Studio Code e no Visual Studio também fornecem um parâmetro adicional com informações específicas para depuração. O parâmetro contém o nome da imagem do depurador, o local do depurador dentro da imagem do depurador e o local de destino dentro do contêiner do aplicativo para montar a pasta do depurador.

A imagem do depurador é determinada automaticamente pelas ferramentas do lado do cliente. Ele usa um método semelhante ao usado durante a geração do gráfico Dockerfile e Helm ao executar `azds prep`. Depois que o depurador é montado na imagem do aplicativo, ele é executado usando `azds exec`.

## <a name="sharing-a-dev-space"></a>Compartilhando um espaço de desenvolvimento

Ao trabalhar com uma equipe, você pode [compartilhar um espaço de desenvolvimento em toda a equipe](how-to/share-dev-spaces.md) e criar espaços de desenvolvimento derivados. Um espaço de desenvolvimento pode ser usado por qualquer pessoa com acesso de colaborador ao grupo de recursos do espaço de desenvolvimento.

Você também pode criar um novo espaço de desenvolvimento que é derivado de outro espaço de desenvolvimento. Quando você cria um espaço de desenvolvimento derivado, o rótulo *azds.Io/Parent-Space=Parent-Space-Name* é adicionado ao namespace do espaço de desenvolvimento derivado. Além disso, todos os aplicativos do espaço de desenvolvimento pai são compartilhados com o espaço de desenvolvimento derivado. Se você implantar uma versão atualizada de um aplicativo no espaço de desenvolvimento derivado, ele só existirá no espaço de desenvolvimento derivado e o espaço de desenvolvimento pai permanecerá inalterado. Você pode ter um máximo de três níveis de espaços de desenvolvimento derivados ou espaços *avôs* .

O espaço de desenvolvimento derivado também roteará de forma inteligente as solicitações entre seus próprios aplicativos e os aplicativos compartilhados de seu pai. O roteamento funciona ao tentar rotear a solicitação para um aplicativo no espaço de desenvolvimento derivado e retornar ao aplicativo compartilhado do espaço de desenvolvimento pai. O roteamento fará o fallback para o aplicativo compartilhado no espaço do avô se o aplicativo não estiver no espaço pai.

Por exemplo:
* O *padrão* de espaço de desenvolvimento tem os aplicativos *servicea* e *serviceB* .
* O *azureuser* de espaço de desenvolvimento é derivado do *padrão*.
* Uma versão atualizada do *servicea* é implantada no *azureuser*.

Ao usar *azureuser*, todas as solicitações para *servicea* serão roteadas para a versão atualizada em *azureuser*. Uma solicitação para *serviceB* primeiro tentará ser roteada para a versão *azureuser* do *serviceB*. Como ele não existe, ele será roteado para a versão *padrão* do *serviceB*. Se a versão *azureuser* do *servicea* for removida, todas as solicitações para *servicea* voltarão a usar a versão *padrão* do *servicea*.

## <a name="next-steps"></a>Passos seguintes

Para começar a usar o Azure Dev Spaces, consulte os seguintes guias de início rápido:

* [Java com CLI e Visual Studio Code](quickstart-java.md)
* [.NET Core com CLI e Visual Studio Code](quickstart-netcore.md)
* [.NET Core com Visual Studio](quickstart-netcore-visualstudio.md)
* [Node. js com CLI e Visual Studio Code](quickstart-nodejs.md)

Para começar a usar o desenvolvimento de equipe, consulte os seguintes artigos de instruções:

* [Desenvolvimento em equipe-Java com CLI e Visual Studio Code](team-development-java.md)
* [Desenvolvimento em equipe-.NET Core com CLI e Visual Studio Code](team-development-netcore.md)
* [Desenvolvimento em equipe-.NET Core com Visual Studio](team-development-netcore-visualstudio.md)
* [Desenvolvimento de equipe-node. js com CLI e Visual Studio Code](team-development-nodejs.md)



[supported-regions]: about.md#supported-regions-and-configurations
