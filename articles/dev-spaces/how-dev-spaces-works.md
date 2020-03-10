---
title: Como funciona o Azure Dev Spaces e está configurado
services: azure-dev-spaces
ms.date: 03/04/2019
ms.topic: conceptual
description: Descreve os processos que alimentam os Espaços Azure Dev e como são configurados no ficheiro de configuração azds.yaml
keywords: azds.yaml, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores
ms.openlocfilehash: e96541b0008dca9cbaeda92152f835c188036971
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375253"
---
# <a name="how-azure-dev-spaces-works-and-is-configured"></a>Como funciona o Azure Dev Spaces e está configurado

Desenvolver uma aplicação Kubernetes pode ser um desafio. Precisas de ficheiros de configuração do Docker e da Kubernetes. Você precisa descobrir como testar a sua aplicação localmente e interagir com outros serviços dependentes. Você pode precisar lidar com o desenvolvimento e teste em vários serviços ao mesmo tempo e com uma equipe de desenvolvedores.

A Azure Dev Spaces ajuda-o a desenvolver, implementar e depurar aplicações kubernetes diretamente no Serviço Azure Kubernetes (AKS). A Azure Dev Spaces também permite que uma equipa partilhe um espaço de v. Partilhar um espaço de dev através de uma equipa permite que membros da equipa individuais se desenvolvam isoladamente sem terem de replicar ou simular dependências ou outras aplicações no cluster.

A Azure Dev Spaces cria e utiliza um ficheiro de configuração para implementar, executar e depurar as suas aplicações Kubernetes no AKS. Este ficheiro de configuração reside com o código da sua aplicação e pode ser adicionado ao seu sistema de controlo de versão.

Este artigo descreve os processos que alimentam os Espaços Azure Dev e como esses processos estão configurados no ficheiro de configuração do Azure Dev Spaces. Para que os Espaços Azure Dev funcionassem rapidamente e o veja na prática, complete um dos quickstarts:

* [Java com CLI e Código de Estúdio Visual](quickstart-java.md)
* [Núcleo .NET com CLI e Código de Estúdio Visual](quickstart-netcore.md)
* [.NET Core com Estúdio Visual](quickstart-netcore-visualstudio.md)
* [Node.js com CLI e Código de Estúdio Visual](quickstart-nodejs.md)

## <a name="how-azure-dev-spaces-works"></a>Como funciona o Azure Dev Spaces

A Azure Dev Spaces tem dois componentes distintos com os quais interage: o controlador e a ferramenta do lado do cliente.

![Componentes dos Espaços Azure Dev](media/how-dev-spaces-works/components.svg)

O controlador executa as seguintes ações:

* Gere a criação e seleção de espaço de v.
* Instala o gráfico Helm da sua aplicação e cria objetos Kubernetes.
* Constrói a imagem do contentor da sua aplicação.
* Implementa a sua aplicação para a AKS.
* As construções incrementais e reiniciaquando o código de origem muda.
* Gere registos e vestígios HTTP.
* Para a frente stdout e stderr para a ferramenta do lado do cliente.
* Permite que os membros da equipa criem espaços de dev infantis derivados de um espaço de dev dos pais.
* Configura o encaminhamento para aplicações dentro de um espaço, bem como em espaços de pais e filhos.

O controlador reside fora da AKS. Impulsiona o comportamento e a comunicação entre a ferramenta do lado do cliente e o cluster AKS. O controlador está ativado utilizando o Azure CLI quando preparar o seu cluster para utilizar espaços Azure Dev. Uma vez ativado, pode interagir com ele utilizando a ferramenta do lado do cliente.

A ferramenta do lado do cliente permite ao utilizador:
* Gere um ficheiro de configuração dockerfile, Helm e Azure Dev Spaces para a aplicação.
* Crie espaços de dev para pais e filhos.
* Diga ao controlador para construir e iniciar a sua aplicação.

Enquanto a sua aplicação está em execução, a ferramenta do lado do cliente também:
* Recebe e exibe stdout e stderr da sua aplicação em execução no AKS.
* Utiliza o [port-forward](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) para permitir o acesso à web à sua aplicação usando http:\//localhost.
* Anexa um desbugger à sua aplicação de execução no AKS.
* Sincroniza o código fonte no seu espaço de dev quando é detetada uma alteração para construções incrementais, permitindo uma rápida iteração.

Pode utilizar a ferramenta do lado do cliente a partir da linha de comando como parte do comando `azds`. Também pode utilizar a ferramenta do lado do cliente com:

* Código de estúdio visual utilizando a [extensão Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).
* Estúdio Visual com [Ferramentas de Estúdio Visual para Kubernetes.](https://aka.ms/get-vsk8stools)

Aqui está o fluxo básico para a configuração e utilização de espaços Azure Dev:
1. Prepare o seu cluster AKS para espaços Azure Dev
1. Prepare o seu código para correr em Espaços Azure Dev
1. Execute o seu código num espaço de dev
1. Desinime o seu código num espaço de v
1. Partilhar um espaço de dev

Cobriremos mais detalhes de como os Espaços Azure Dev funcionam em cada uma das secções abaixo.

## <a name="prepare-your-aks-cluster"></a>Prepare o seu cluster AKS

Preparar o seu cluster AKS envolve:
* A verificação do seu cluster AKS está numa região [apoiada pela Azure Dev Spaces.][supported-regions]
* Verificando se está a executar Kubernetes 1.10.3 ou mais tarde.
* Habilitar os espaços Azure Dev no seu cluster utilizando `az aks use-dev-spaces`

Para obter mais informações sobre como criar e configurar um cluster AKS para espaços Azure Dev, consulte um dos guias iniciados:
* [Começar em Espaços Azure Dev com Java](get-started-java.md)
* [Começar em Espaços Azure Dev com .NET Core e Visual Studio](get-started-netcore-visualstudio.md)
* [Começar em Espaços Azure Dev com .NET Core](get-started-netcore.md)
* [Começar em Espaços Azure Dev com Node.js](get-started-nodejs.md)

Quando o Azure Dev Spaces está ativado no seu cluster AKS, instala o controlador para o seu cluster. O controlador é um recurso Azure separado fora do seu cluster e faz o seguinte aos recursos do seu cluster:

* Cria ou designa um espaço de nome Kubernetes para usar como espaço de v.
* Remove qualquer espaço de nome Kubernetes chamado *azds,* se existir, e cria um novo.
* Implementa uma configuração de webhook Kubernetes.
* Implementa um servidor de admissão webhook.
    

Também usa o mesmo diretor de serviço que o seu cluster AKS usa para fazer chamadas de serviço para outros componentes do Azure Dev Spaces.

![Espaços Azure Dev preparam aglomerado](media/how-dev-spaces-works/prepare-cluster.svg)

Para utilizar os Espaços Azure Dev, deve haver pelo menos um espaço de v. A Azure Dev Spaces utiliza espaços de nome Kubernetes dentro do seu cluster AKS para espaços de v. Quando um controlador está a ser instalado, ele pede-lhe para criar um novo espaço de nome Kubernetes ou escolher um espaço de nome existente para usar como o seu primeiro espaço de dev. Quando um espaço de nome é designado como um espaço de v, o controlador adiciona a etiqueta *azds.io/space=true* a esse espaço de nome para identificá-lo como um espaço de v. O espaço de v inicial que cria ou designa é selecionado por padrão depois de preparar o seu cluster. Quando um espaço é selecionado, é utilizado pela Azure Dev Spaces para criar novas cargas de trabalho.

Por padrão, o controlador cria um espaço de v chamado *padrão,* atualizando o espaço de nome *padrão* kubernetes existente. Pode utilizar a ferramenta do lado do cliente para criar novos espaços de dev e remover os espaços de dev existentes. Devido a uma limitação em Kubernetes, o espaço de v *padrão* não pode ser removido. O controlador também remove quaisquer espaços de nome kubernetes *existentes chamados azds* para evitar conflitos com o comando `azds` usado pela ferramenta do lado do cliente.

O servidor de admissão de webhook Kubernetes é utilizado para injetar cápsulas com três contentores durante a implementação para instrumentação: um recipiente de procuração de espaços devespaços, um recipiente de devspaces-proxy-init e um recipiente de construção de espaços devespaços. **Todos estes três contentores funcionam com acesso de raiz no seu cluster AKS.** Também usam o mesmo diretor de serviço que o seu cluster AKS usa para fazer chamadas de serviço para outros componentes do Azure Dev Spaces.

![Servidor de admissão webhook Azure Dev Spaces Kubernetes](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

O contentor de procuração de espaços devespaços é um recipiente para carros laterais que transporta todo o tráfego de TCP para dentro e para fora do recipiente de aplicação e ajuda a realizar o encaminhamento. O contentor de procuração de espaços devespaços redireciona mensagens HTTP se determinados espaços estiverem a ser utilizados. Por exemplo, pode ajudar a encaminhar mensagens HTTP entre aplicações em espaços para pais e filhos. Todo o tráfego não HTTP passa por devspaces-proxy não modificado. O recipiente de procuração de espaços devespaços também regista todas as mensagens HTTP de entrada e saída e envia-as para a ferramenta do lado do cliente como vestígios. Estes vestígios podem então ser vistos pelo desenvolvedor para inspecionar o comportamento da aplicação.

O recipiente de devspaces-proxy-init é um [recipiente init](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) que adiciona regras adicionais de encaminhamento com base na hierarquia espacial ao recipiente da sua aplicação. Adiciona regras de encaminhamento atualizando o ficheiro */etc/resolve.conf* do recipiente de aplicação e a configuração dos iptables antes de começar. As atualizações para */etc/resolve.conf* permitem a resolução de Serviços DNS nos espaços-mãe. As atualizações de configuração iptables garantem que todo o tráfego de TCP dentro e fora do contentor da aplicação são encaminhados embora devspaces-proxy. Todas as atualizações do devspaces-proxy-init acontecem além das regras que a Kubernetes adiciona.

O recipiente de construção de espaços devespaços é um recipiente init e tem o código fonte do projeto e a tomada Docker montada. O código fonte do projeto e o acesso ao Docker permitem que o recipiente de aplicação seja construído diretamente pela cápsula.

> [!NOTE]
> A Azure Dev Spaces usa o mesmo nó para construir o contentor da sua aplicação e executá-lo. Como resultado, a Azure Dev Spaces não necessita de um registo externo de contentores para a construção e execução da sua aplicação.

O servidor de admissão de webhook kubernetes ouve qualquer novo casulo criado no cluster AKS. Se a cápsula for implantada para qualquer espaço de nome com a etiqueta *azds.io/space=true,* injeta essa cápsula com os recipientes adicionais. O recipiente de construção de espaços de vespaços só é injetado se o recipiente da aplicação for executado utilizando a ferramenta do lado do cliente.

Assim que tiver preparado o seu cluster AKS, pode utilizar a ferramenta do lado do cliente para preparar e executar o seu código no seu espaço de dev.

## <a name="prepare-your-code"></a>Prepare o seu código

Para executar a sua aplicação num espaço de v, ela precisa de ser contentorizada, e você precisa definir como deve ser implantado em Kubernetes. Para contentorizar a sua aplicação, precisa de um Dockerfile. Para definir como a sua aplicação é implantada para Kubernetes, você precisa de um [gráfico Helm](https://docs.helm.sh/). Para ajudar na criação do gráfico Dockerfile e Helm para a sua aplicação, as ferramentas do lado do cliente fornecem o comando `prep`:

```cmd
azds prep --public
```

O comando `prep` irá analisar os ficheiros do seu projeto e tentar criar o gráfico Dockerfile e Helm para executar a sua aplicação em Kubernetes. Atualmente, o comando `prep` gerará um gráfico de Dockerfile e Helm com as seguintes línguas:

* Java
* Node.js
* .NET Core

Tem *de* executar o comando `prep` a partir de um diretório que contenha código fonte. Executar o comando `prep` a partir do diretório correto permite que a ferramenta do lado do cliente identifique o idioma e crie um Dockerfile apropriado para containerizar a sua aplicação. Você também pode executar o comando `prep` de um diretório que contém um arquivo *pom.xml* para projetos Java.

Se executar o comando `prep` a partir do diretório que não contenha código fonte, a ferramenta do lado do cliente não gerará um Dockerfile. Também apresentará um erro dizendo: *O Dockerfile não pôde ser gerado devido a uma linguagem não suportada*. Este erro também ocorre se a ferramenta do lado do cliente não reconhecer o tipo de projeto.

Quando executar o comando `prep`, tem a opção de especificar a bandeira `--public`. Esta bandeira diz ao controlador para criar um ponto final acessível à Internet para este serviço. Se não especificar esta bandeira, o serviço só é acessível a partir do interior do cluster ou utilizando o túnel local de acolhimento criado pela ferramenta do lado do cliente. Pode ativar ou desativar este comportamento depois de executar o comando `prep` atualizando o gráfico helm gerado.

O comando `prep` não substituirá quaisquer ficheiros Docker existentes ou gráficos Helm que tenha no seu projeto. Se um dockerfile ou gráfico Helm existente sustiver a mesma convenção de nomeação que os ficheiros gerados pelo comando `prep`, o comando `prep` saltará para a geração desses ficheiros. Caso contrário, o comando `prep` gerará o seu próprio dockerfile ou gráfico Helm juntamente com os ficheiros existentes.

O comando `prep` também gerará um ficheiro `azds.yaml` na raiz do seu projeto. A Azure Dev Spaces utiliza este ficheiro para construir, instalar, configurar e executar a sua aplicação. Este ficheiro de configuração lista a localização do seu dockerfile e gráfico Helm e também fornece configuração adicional em cima desses artefactos.

Aqui está um exemplo de ficheiro azds.yaml criado com [a aplicação da amostra .NET Core:](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend)

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

O ficheiro `azds.yaml` gerado pelo comando `prep` deve funcionar bem para um cenário simples e único de desenvolvimento do projeto. Se o seu projeto específico tiver uma maior complexidade, poderá ter de atualizar este ficheiro depois de executar o comando `prep`. Por exemplo, o seu projeto pode exigir alguns ajustes no seu processo de construção ou lançamento com base nas suas necessidades de desenvolvimento ou depuração. Você também pode ter várias aplicações no seu projeto, que requerem vários processos de construção ou um conteúdo de construção diferente.

## <a name="run-your-code"></a>Executar o seu código

Para executar o seu código num espaço de v, emita o comando `up` no mesmo diretório que o seu ficheiro `azds.yaml`:

```cmd
azds up
```

O comando `up` envia os ficheiros de origem da aplicação e outros artefactos necessários para construir e executar o seu projeto para o espaço de v. A partir daí, o controlador no seu espaço de dev:

1. Cria os objetos Kubernetes para implementar a sua aplicação.
1. Constrói o recipiente para a sua aplicação.
1. Implanta a sua aplicação para o espaço de dev.
1. Cria um nome DNS acessível ao público para o ponto final da sua aplicação se configurado.
1. Utiliza o *port-forward* para fornecer acesso ao seu ponto final de aplicação utilizando http://localhost.
1. Para a frente stdout e stderr para a ferramenta do lado do cliente.


### <a name="starting-a-service"></a>Iniciar um serviço

Quando inicia um serviço num espaço de dev, a ferramenta do lado do cliente e o controlador trabalham em coordenação para sincronizar os seus ficheiros de origem, criar o seu recipiente e objetos Kubernetes e executar a sua aplicação.

A um nível mais granular, eis o que acontece quando se corre `azds up`:

1. Os ficheiros são sincronizados da máquina do utilizador para um armazenamento de ficheiros Azure que é exclusivo do cluster AKS do utilizador. O código fonte, o gráfico helm e os ficheiros de configuração são carregados. Mais detalhes sobre o processo de sincronização estão disponíveis na secção seguinte.
1. O controlador cria um pedido para iniciar uma nova sessão. Este pedido contém várias propriedades, incluindo um ID único, nome espacial, caminho para código fonte, e uma bandeira dedepura.
1. O controlador substitui o espaço reservado *$(tag)* na tabela Helm pelo ID de sessão único e instala a tabela Helm para o seu serviço. A adição de uma referência ao ID de sessão único ao gráfico Helm permite que o recipiente implantado no cluster AKS para esta sessão específica seja ligado ao pedido de sessão e informações associadas.
1. Durante a instalação do gráfico Helm, o servidor de admissão web hook Kubernetes adiciona recipientes adicionais ao casulo da sua aplicação para instrumentação e acesso ao código fonte do seu projeto. Os recipientes de devspaces-proxy-proxy-proxy-init são adicionados para fornecer rastreio http e encaminhamento espacial. O recipiente de construção de espaços devespaços é adicionado para fornecer ao casulo acesso à instância Docker e código fonte do projeto para a construção do recipiente da sua aplicação.
1. Quando a cápsula da aplicação é iniciada, o recipiente de construção de espaços devespaços e o recipiente de devspaces-proxy-init são usados para construir o recipiente de aplicação. O contentor de aplicação e os contentores proxy de devspaces são então iniciados.
1. Depois de o recipiente de aplicação ter sido iniciado, a funcionalidade do lado do cliente utiliza a *funcionalidade* kubernetes para fornecer acesso HTTP à sua aplicação ao longo de http://localhost. Este encaminhamento de porta liga a sua máquina de desenvolvimento ao serviço no seu espaço de v.
1. Quando todos os contentores da cápsula já começaram, o serviço está em funcionamento. Neste ponto, a funcionalidade do lado do cliente começa a transmitir os vestígios HTTP, stdout e stderr. Esta informação é apresentada pela funcionalidade do lado do cliente para o desenvolvedor.

### <a name="updating-a-running-service"></a>Atualizar um serviço de funcionamento

Enquanto um serviço está em execução, a Azure Dev Spaces tem a capacidade de atualizar esse serviço se algum dos ficheiros de origem do projeto mudar. A Dev Spaces também lida com a atualização do serviço de forma diferente, dependendo do tipo de ficheiro que é alterado. Existem três formas de a Dev Spaces atualizar um serviço de funcionamento:

* Atualizar diretamente um ficheiro
* Reconstrução e reinício do processo da aplicação no interior do contentor da aplicação em execução
* Reconstrução e reafectação do contentor da aplicação

![Sincronização de ficheiros Azure Dev Spaces](media/how-dev-spaces-works/file-sync.svg)

Certos ficheiros do projeto que são ativos estáticos, tais como ficheiros html, css e cshtml, podem ser atualizados diretamente no recipiente da aplicação sem reiniciar nada. Se um ativo estático mudar, o novo ficheiro é sincronizado com o espaço de v e, em seguida, utilizado pelo recipiente de funcionamento.

Alterações a ficheiros como código fonte ou ficheiros de configuração de aplicações podem ser aplicadas reiniciando o processo da aplicação dentro do recipiente de execução. Uma vez sincronizados estes ficheiros, o processo da aplicação é reiniciado dentro do recipiente de execução utilizando o processo *de devhostagent.* Ao criar inicialmente o recipiente da aplicação, o controlador substitui o comando de arranque da aplicação por um processo diferente chamado *devhostagent*. O processo real da aplicação é então executado como um processo infantil sob *o devhostagent*, e a sua saída é canalizada usando a saída do *devhostagent.* O processo *de devhostagent* também faz parte da Dev Spaces e pode executar comandos no contentor em nome da Dev Spaces. Ao efetuar um reinício, *devhostagent:*

* Interrompe o processo atual ou os processos associados à aplicação
* Reconstrói a aplicação
* Reinicia o processo ou os processos associados à aplicação

A forma como o *devhostagent* executa os passos anteriores está configurada no ficheiro de configuração `azds.yaml`. Esta configuração é detalhada numa secção posterior.

As atualizações a ficheiros de projeto, tais como Ficheiros Docker, ficheiros CSProJ ou qualquer parte do gráfico Helm, exigem que o recipiente da aplicação seja reconstruído e reimplantado. Quando um destes ficheiros é sincronizado com o espaço de v, o controlador executa o comando de [atualização](https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure) do leme e o recipiente da aplicação é reconstruído e reimplantado.

### <a name="file-synchronization"></a>Sincronização de ficheiros

A primeira vez que uma aplicação é iniciada num espaço de v, todos os ficheiros de origem da aplicação são carregados. Enquanto a aplicação estiver em execução e posteriormente reinicia, apenas os ficheiros alterados são carregados. Dois ficheiros são usados para coordenar este processo: um ficheiro do lado do cliente e um ficheiro do lado do controlador.

O ficheiro do lado do cliente é armazenado num diretório temporário e é nomeado com base num hash do diretório do projeto que está a executar em Espaços Dev. Por exemplo, no Windows teria um ficheiro como *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef123456767890abcdef1234567890abcdef.synclog* para o seu projeto. No Linux, o ficheiro do lado do cliente é armazenado no diretório */tmp.* Pode encontrar o diretório no macOS executando o comando `echo $TMPDIR`.

Este ficheiro encontra-se em formato JSON e contém:

* Uma entrada para cada ficheiro de projeto que é sincronizado com o espaço dev
* Um ID de sincronização
* O carimbo temporal da última operação de sincronização

Cada entrada de ficheiro sinuoso contém um caminho para o ficheiro e a sua marca de tempo.

O ficheiro do lado do controlador está armazenado no cluster AKS. Contém o ID de sincronização e o carimbo temporal da última sincronização.

Uma sincronização acontece quando os selos de tempo de sincronização não correspondem entre o lado do cliente e os ficheiros do lado do controlador. Durante uma sincronização, o lado cliente que ferramenta itera sobre as entradas de ficheiros no ficheiro do lado do cliente. Se o carimbo de tempo do ficheiro for após a marca ção, o ficheiro está sincronizado com o espaço de dev. Uma vez concluída a sincronização, os selos de tempo sincronizados são atualizados tanto no lado do cliente como no lado do controlador.

Todos os ficheiros do projeto estão sincronizados se o ficheiro do lado do cliente não estiver presente. Este comportamento permite-lhe forçar uma sincronização completa, apagando o ficheiro do lado do cliente.

### <a name="how-routing-works"></a>Como funciona o encaminhamento

Um espaço de v é construído em cima de AKS e usa os [mesmos conceitos de networking.](../aks/concepts-network.md) A Azure Dev Spaces também tem um serviço centralizado de *ingressmanager* e implanta o seu próprio Controlador De Ingress para o cluster AKS. O serviço *ingressmanager* monitoriza os clusters AKS com espaços de dev e aumenta o Controlador de Ingress os Espaços Azure Dev no cluster com objetos Ingress para encaminhamento para cápsulas de aplicação. O recipiente de procuração de espaços devespaços em cada cápsula adiciona um cabeçalho `azds-route-as` HTTP para o tráfego HTTP a um espaço de v baseado no URL. Por exemplo, um pedido para o URL *http://azureuser.s.default.serviceA.fedcba09...azds.io* obteria um cabeçalho HTTP com `azds-route-as: azureuser`. O recipiente de procuração de espaços devespaços não adicionará um cabeçalho `azds-route-as` se um já estiver presente.

Quando um pedido HTTP é feito a um serviço de fora do cluster, o pedido vai para o controlador Ingress. O controlador Ingress encaminha o pedido diretamente para a cápsula apropriada com base nos seus objetos e regras Ingress. O recipiente de procuração de espaços devespaços na cápsula recebe o pedido, adiciona o cabeçalho `azds-route-as` com base no URL e, em seguida, encaminha o pedido para o recipiente de aplicação.

Quando um pedido HTTP é feito a um serviço de outro serviço dentro do cluster, o pedido passa primeiro pelo recipiente de procuração de espaços de vespaços do serviço de chamada. O recipiente de procuração de espaços devespaços olha para o pedido http e verifica o cabeçalho `azds-route-as`. Com base no cabeçalho, o recipiente de procuração de espaços devespaços procurará o endereço IP do serviço associado ao valor do cabeçalho. Se for encontrado um endereço IP, o contentor proxy devspaces redireciona o pedido para esse endereço IP. Se não for encontrado um endereço IP, o contentor proxy devspaces encaminha o pedido para o recipiente de aplicação dos pais.

Por exemplo, o serviço de aplicações *A* e *o serviçoB* são implantados para um espaço de dev dos pais chamado *padrão*. *serviceA* depende do *serviçoB* e faz chamadas HTTP para ele. O Utilizador Azure cria um espaço de dev para crianças baseado no espaço *padrão* chamado *azureuser*. O Utilizador Azure também implementa a sua própria versão de *serviçoA* para o seu espaço infantil. Quando for feito um pedido para *http://azureuser.s.default.serviceA.fedcba09...azds.io:*

![Encaminhamento de Espaços Azure Dev](media/how-dev-spaces-works/routing.svg)

1. O controlador Ingress procura o IP para o casulo associado ao URL, que é *serviceA.azureuser*.
1. O controlador Ingress encontra o IP para o casulo no espaço de v do Utilizador Azure e encaminha o pedido para o *casulo serviceA.azureuser.*
1. O recipiente de procuração de espaços devespaços no *vagem serviceA.azureuser* recebe o pedido e adiciona `azds-route-as: azureuser` como um cabeçalho HTTP.
1. O contentor de procuração de espaços devespaços no *vagem serviceA.azureuser* encaminha o pedido para o recipiente de aplicação *serviceA* no *vagem serviceA.azureuser.*
1. A aplicação *serviceA* no *serviçoA.azureuser* pod faz uma chamada para *o serviçoB*. A aplicação *serviceA* também contém código para preservar o cabeçalho `azds-route-as` existente, que neste caso é `azds-route-as: azureuser`.
1. O recipiente de procuração de espaços devespaços no *casulo serviceA.azureuser* recebe o pedido e analisa o IP do *serviçoB* com base no valor do cabeçalho `azds-route-as`.
1. O recipiente de procuração de espaços devespaços no *vagem serviceA.azureuser* não encontra um IP para *serviçoB.azureuser*.
1. O recipiente de procuração de espaços devespaços no *vagem serviceA.azureuser* procura o IP para *o serviçoB* no espaço principal, que é *serviceB.default*.
1. O recipiente de procuração de espaços devespaços no *vagem serviceA.azureuser* encontra o IP para *serviceB.default* e encaminha o pedido para o *serviçoB.default* pod.
1. O contentor de procuração de espaços devespaços no *vagem serviceB.default* recebe o pedido e encaminha o pedido para o recipiente de aplicação *serviceB* no *vagem padrão serviceB.predefinido.*
1. A aplicação *serviceB* no *serviçoB.default* pod devolve uma resposta ao *pod serviceA.azureuser.*
1. O contentor de procuração de espaços devespaços no *vagão serviceA.azureuser* recebe a resposta e encaminha a resposta para o recipiente de aplicação *serviceA* no *vagem serviceA.azureuser.*
1. A aplicação *serviceA* recebe a resposta e, em seguida, devolve a sua própria resposta.
1. O recipiente de procuração de espaços devespaços no *vagão serviceA.azureuser* recebe a resposta do recipiente de aplicação *serviceA* e encaminha a resposta para o chamador original fora do cluster.

Todo o tráfego de TCP que não seja HTTP passa pelo controlador Ingress e contentores de procuração de espaços devespaços não modificados.

### <a name="how-running-your-code-is-configured"></a>Como executar o seu código é configurado

A Azure Dev Spaces utiliza o ficheiro `azds.yaml` para instalar e configurar o seu serviço. O controlador utiliza a propriedade `install` no ficheiro `azds.yaml` para instalar o gráfico Helm e criar os objetos Kubernetes:

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

Por padrão, o comando `prep` gerará o gráfico Helm. Também define a propriedade *de instalação.chart* para o diretório da tabela Helm. Se quiser usar um gráfico Helm num local diferente, pode atualizar esta propriedade para usar essa localização.

Ao instalar as tabelas Helm, a Azure Dev Spaces fornece uma forma de sobrepor valores na tabela Helm. Os valores predefinidos para o gráfico Helm estão em `charts/APP_NAME/values.yaml`.

Utilizando a propriedade *install.values,* pode listar um ou mais ficheiros que definam valores que pretende ser substituídos na tabela Helm. Por exemplo, se quiser um nome de anfitrião ou configuração de base de dados especificamente ao executar a sua aplicação num espaço de v, pode utilizar esta funcionalidade de substituição. Também pode adicionar *um?* no final de qualquer um dos nomes de ficheiros para defini-lo como opcional.

A propriedade *install.set* permite-lhe configurar um ou mais valores que pretende ser substituído na tabela Helm. Quaisquer valores configurados na *instalação.set* sobrepor-se-ão aos valores configurados em ficheiros listados na *instalação.valores*. As propriedades em *instalação.set* dependem dos valores na tabela Helm e podem ser diferentes dependendo do gráfico helm gerado.

No exemplo acima, a propriedade *install.set.replicaCount* diz ao controlador quantas instâncias da sua aplicação para executar no seu espaço de dev. Dependendo do seu cenário, pode aumentar este valor, mas terá um impacto na anexação de um desordeiro à cápsula da sua aplicação. Para mais informações, consulte o artigo de resolução de [problemas.](troubleshooting.md)

No gráfico helm gerado, a imagem do recipiente está definida para *{{ . Valores.image.repositório }}:{{{ Valores.image.tag }}* . O ficheiro `azds.yaml` define a propriedade *install.set.image.tag* como *$(tag)* por padrão, que é usado como o valor para *{{ . Valores.image.tag }}* . Ao configurar a propriedade *install.set.image.tag* desta forma, permite que a imagem do recipiente para a sua aplicação seja marcada de forma distinta ao executar os Espaços Azure Dev. Neste caso específico, a imagem é marcada como *\<valor a partir de image.repository>:$(tag)* . Deve utilizar a variável *$(tag)* como valor de *instalação.set.image.tag* para que os Espaços Dev reconheçam e localizem o recipiente no cluster AKS.

No exemplo acima, `azds.yaml` define *instalar.set.ingress.hosts*. A *propriedade install.set.ingress.hosts* define um formato de nome de anfitrião para pontos finais públicos. Esta propriedade também utiliza *$(spacePrefix),* *$(rootSpacePrefix)* e *$(hostSuffix)* , que são valores fornecidos pelo controlador. 

O *$(spacePrefix)* é o nome do espaço de v infantil, que assume a forma de *SPACENAME.s*. O *$(rootSpacePrefix)* é o nome do espaço dos pais. Por exemplo, se *o azureuser* é um espaço infantil de *padrão*, o valor por *$(rootSpacePrefix)* é *padrão* e o valor de *$(spacePrefix)* é *azureuser.s*. Se o espaço não for um espaço para crianças, *$(spacePrefix)* está em branco. Por exemplo, se o espaço *predefinido* não tiver espaço para pais, o valor para *$(rootSpacePrefix)* é *padrão* e o valor de *$(spacePrefix)* está em branco. O *$(hostSuffix)* é um sufixo DNS que aponta para o Controlador de Ingress os Espaços Azure Dev que funciona no seu cluster AKS. Este sufixo DNS corresponde a uma entrada dNS wildcard, por exemplo *\*. RANDOM_VALUE.eus.azds.io,* que foi criado quando o controlador Azure Dev Spaces foi adicionado ao seu cluster AKS.

No ficheiro `azds.yaml` acima, também pode atualizar *instalar.set.ingress.hosts* para alterar o nome de anfitrião da sua aplicação. Por exemplo, se quiser simplificar o nome de anfitrião da sua aplicação a partir de *$(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)* para *$(spacePrefix)$(rootSpacePrefix)web$(hostSuffix)* .

Para construir o recipiente para a sua aplicação, o controlador utiliza as secções abaixo do ficheiro de configuração `azds.yaml`:

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

O controlador usa um Dockerfile para construir e executar a sua aplicação.

A propriedade *build.context* lista o diretório onde os Dockerfiles existem. A propriedade *build.dockerfile* define o nome do Dockerfile para a construção da versão de produção da aplicação. As *configurações.develop.build.dockerfile* property configura o nome do Dockerfile para a versão de desenvolvimento da aplicação.

Ter diferentes Dockerfiles para desenvolvimento e produção permite-lhe ativar certas coisas durante o desenvolvimento e desativar esses itens para implementações de produção. Por exemplo, pode permitir a depuração ou mais exploração madeireira verbosa durante o desenvolvimento e desativar num ambiente de produção. Também pode atualizar estas propriedades se os seus Ficheiros Docker forem nomeados de forma diferente ou se encontrarem num local diferente.

Para ajudá-lo a itear rapidamente durante o desenvolvimento, o Azure Dev Spaces sincronizará alterações do seu projeto local e atualizará incrementalmente a sua aplicação. A secção abaixo no ficheiro de configuração `azds.yaml` é utilizada para configurar a sincronização e a atualização:

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

Os ficheiros e diretórios que sincronizarão as alterações estão listados na *propriedade configurações.develop.container.sync.* Estes diretórios são sincronizados inicialmente quando executa o comando `up`, bem como quando são detetadas alterações. Se houver diretórios adicionais ou diferentes que você gostaria de sincronizar com o seu espaço de dev, você pode mudar esta propriedade.

As *configurações.develop.container.iterate.buildCommands* property especifica como construir a aplicação em um cenário de desenvolvimento. A *propriedade configurações.develop.container.command* fornece o comando para executar a aplicação num cenário de desenvolvimento. Você pode querer atualizar qualquer uma destas propriedades se houver bandeiras ou parâmetros adicionais de construção ou tempo de execução que você gostaria de usar durante o desenvolvimento.

As *configurações.develop.container.iterate.processesToKill* lista os processos a matar para parar a aplicação. Pode querer atualizar esta propriedade se quiser alterar o comportamento de reinício da sua aplicação durante o desenvolvimento. Por exemplo, se atualizou as *configurações.develop.container.iterate.buildCommands* ou *configurations.develop.container.command* properties to change how application is built or started, you may need to change what processs are stop.

Ao preparar o seu código utilizando o comando `azds prep`, tem a opção de adicionar a bandeira `--public`. Adicionar a bandeira `--public` cria um URL acessível ao público para a sua aplicação. Se omite esta bandeira, a aplicação só é acessível dentro do aglomerado ou utilizando o túnel de acolhimento local. Depois de executar o comando `azds prep`, pode alterar esta definição modificando a propriedade *ativada* pela entrada em `charts/APPNAME/values.yaml`:

```yaml
ingress:
  enabled: true
```

## <a name="debug-your-code"></a>Desinime o seu código

Para aplicações Java, .NET e Node.js, pode desinviar a sua aplicação a funcionar diretamente no seu espaço de dev utilizando o Visual Studio Code ou o Visual Studio. O Visual Studio Code e o Visual Studio fornecem ferramentas para se conectarem ao seu espaço de dev, lançarem a sua aplicação e anexarem um debugger. Depois de executar `azds prep`, pode abrir o seu projeto no Visual Studio Code ou no Visual Studio. O Visual Studio Code ou o Visual Studio gerarão os seus próprios ficheiros de configuração para a ligação, que é separado de executar `azds prep`. A partir de visual studio code ou estúdio visual, você pode definir breakpoints e lançar a sua aplicação para o seu espaço de v.

![Depurando o seu código](media/get-started-node/debug-configuration-nodejs2.png)

Quando lança a sua aplicação utilizando o Visual Studio Code ou o Visual Studio para depuração, eles lidam com o lançamento e a ligação ao seu espaço de dev da mesma forma que executar `azds up`. A ferramenta do lado do cliente no Visual Studio Code e Visual Studio também fornece um parâmetro adicional com informações específicas para depuração. O parâmetro contém o nome da imagem de debugger, a localização do desbugger dentro da imagem do depurador, e a localização de destino dentro do recipiente da aplicação para montar a pasta de debugger.

A imagem de debugger é automaticamente determinada pela ferramenta do lado do cliente. Utiliza um método semelhante ao utilizado durante o Dockerfile e o gráfico Helm geram quando se executa `azds prep`. Depois de o desbugger ser montado na imagem da aplicação, é executado utilizando `azds exec`.

## <a name="sharing-a-dev-space"></a>Partilhar um espaço de dev

Ao trabalhar com uma equipa, você pode [compartilhar um espaço de v em toda uma equipe](how-to/share-dev-spaces.md) e criar espaços de v derivados. Um espaço de v pode ser usado por qualquer pessoa com acesso ao grupo de recursos do espaço dev.

Você também pode criar um novo espaço de dev que é derivado de outro espaço de v. Quando se cria um espaço de v derivado, o rótulo *azds.io/parent-space=PARENT-SPACE-NAME* é adicionado ao espaço de nome do espaço de v derivado. Além disso, todas as aplicações do espaço de v dos pais são partilhadas com o espaço de v derivado. Se implementar uma versão atualizada de uma aplicação para o espaço derivado, ela só existirá no espaço de v derivado e o espaço de v dos pais permanecerá inalterado. Você pode ter um máximo de três níveis de espaços derivados de v ou espaços *de avós.*

O espaço derivado de v também irá direcionar inteligentemente os pedidos entre as suas próprias aplicações e as aplicações partilhadas com o seu progenitor. O encaminhamento funciona tentando encaminhar o pedido para uma aplicação no espaço de v derivado e recuando para a aplicação partilhada do espaço de v-mãe. O encaminhamento recairá para a aplicação partilhada no espaço dos avós se a aplicação não estiver no espaço dos pais.

Por exemplo:
* O *padrão* de espaço de v tem aplicações *serviçoA* e *serviçoB* .
* O *azureuser* espaço dev é derivado do *padrão*.
* Uma versão atualizada do *serviço A* é implantada para *azureuser*.

Ao utilizar *o azureuser,* todos os pedidos de *serviço A* serão encaminhados para a versão atualizada em *azureuser*. Um pedido de *serviçoB* tentará primeiro ser encaminhado para a versão *azureuser* do *serviçoB*. Uma vez que não existe, será encaminhado para a versão *padrão* do *serviçoB*. Se a versão *azureuser* do *serviço A* for removida, todos os pedidos de *serviçoA* voltarão a utilizar a versão *padrão* do *serviço A*.

## <a name="next-steps"></a>Passos Seguintes

Para começar a usar os Espaços Azure Dev, consulte os seguintes quickstarts:

* [Java com CLI e Código de Estúdio Visual](quickstart-java.md)
* [Núcleo .NET com CLI e Código de Estúdio Visual](quickstart-netcore.md)
* [.NET Core com Estúdio Visual](quickstart-netcore-visualstudio.md)
* [Node.js com CLI e Código de Estúdio Visual](quickstart-nodejs.md)

Para começar com o desenvolvimento da equipa, consulte os seguintes artigos:

* [Team Development - Java com CLI e Código de Estúdio Visual](team-development-java.md)
* [Team Development - .NET Core com CLI e Visual Studio Code](team-development-netcore.md)
* [Team Development - .NET Core com Estúdio Visual](team-development-netcore-visualstudio.md)
* [Team Development - Node.js com CLI e Visual Studio Code](team-development-nodejs.md)



[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
