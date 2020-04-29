---
title: Como funciona o seu código com a Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descreve os processos de execução do seu código no Serviço Azure Kubernetes com espaços Azure Dev
keywords: azds.yaml, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores
ms.openlocfilehash: 6851c04ac0b72db1bd13c991875c16b0beadc573
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241364"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>Como funciona o seu código com a Azure Dev Spaces

A Azure Dev Spaces fornece-lhe múltiplas formas de iterar e depurar rapidamente aplicações kubernetes e colaborar com a sua equipa num cluster do Serviço Azure Kubernetes (AKS). Uma vez que o seu [projeto esteja preparado para funcionar num espaço de v,][how-it-works-prep]você pode usar Dev Spaces para construir e executar o seu projeto no seu cluster AKS.

Este artigo descreve o que acontece executar o seu código em AKS com Espaços Dev.

## <a name="run-your-code"></a>Executar o seu código

Para executar o seu código num `up` espaço de v, emita o comando no mesmo diretório que o seu `azds.yaml` ficheiro:

```cmd
azds up
```

O `up` comando envia os ficheiros de origem da aplicação e outros artefactos necessários para construir e executar o seu projeto para o espaço de v. A partir daí, o controlador no seu espaço de dev:

1. Cria os objetos Kubernetes para implementar a sua aplicação.
1. Constrói o recipiente para a sua aplicação.
1. Implanta a sua aplicação para o espaço de dev.
1. Cria um nome DNS acessível ao público para o ponto final da sua aplicação se configurado.
1. Utiliza o *port-forward* para fornecer acesso http://localhostao seu ponto final de aplicação utilizando .
1. Para a frente stdout e stderr para a ferramenta do lado do cliente.


## <a name="starting-a-service"></a>Iniciar um serviço

Quando inicia um serviço num espaço de dev, a ferramenta do lado do cliente e o controlador trabalham em coordenação para sincronizar os seus ficheiros de origem, criar o seu recipiente e objetos Kubernetes e executar a sua aplicação.

A um nível mais granular, eis o que acontece quando se corre: `azds up`

1. [Os ficheiros são sincronizados][sync-section] do computador do utilizador para um armazenamento de ficheiros Azure que é exclusivo do cluster AKS do utilizador. O código fonte, o gráfico helm e os ficheiros de configuração são carregados.
1. O controlador cria um pedido para iniciar uma nova sessão. Este pedido contém várias propriedades, incluindo um ID único, nome espacial, caminho para código fonte, e uma bandeira dedepura.
1. O controlador substitui o espaço reservado *$(tag)* na tabela Helm pelo ID de sessão único e instala a tabela Helm para o seu serviço. A adição de uma referência ao ID de sessão único ao gráfico Helm permite que o recipiente implantado no cluster AKS para esta sessão específica seja ligado ao pedido de sessão e informações associadas.
1. Durante a instalação do gráfico Helm, o servidor de admissão web hook Kubernetes adiciona recipientes adicionais ao casulo da sua aplicação para instrumentação e acesso ao código fonte do seu projeto. Os recipientes de devspaces-proxy-proxy-proxy-init são adicionados para fornecer rastreio http e encaminhamento espacial. O recipiente de construção de espaços devespaços é adicionado para fornecer ao casulo acesso à instância Docker e código fonte do projeto para a construção do recipiente da sua aplicação.
1. Quando a cápsula da aplicação é iniciada, o recipiente de construção de espaços devespaços e o recipiente de devspaces-proxy-init são usados para construir o recipiente de aplicação. O contentor de aplicação e os contentores proxy de devspaces são então iniciados.
1. Depois de o recipiente de aplicação ter sido iniciado, a funcionalidade do lado do cliente http://localhostutiliza a *funcionalidade* kubernetes para fornecer acesso HTTP à sua aplicação mais de . Este encaminhamento de porta liga o seu computador de desenvolvimento ao serviço no seu espaço de v.
1. Quando todos os contentores da cápsula já começaram, o serviço está em funcionamento. Neste ponto, a funcionalidade do lado do cliente começa a transmitir os vestígios HTTP, stdout e stderr. Esta informação é apresentada pela funcionalidade do lado do cliente para o desenvolvedor.

## <a name="updating-a-running-service"></a>Atualizar um serviço de funcionamento

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

A forma como o *devhostagent* executa os passos anteriores está [configurada em `azds.yaml` ][azds-yaml-section].

As atualizações a ficheiros de projeto, tais como Ficheiros Docker, ficheiros CSProJ ou qualquer parte do gráfico Helm, exigem que o recipiente da aplicação seja reconstruído e reimplantado. Quando um destes ficheiros é sincronizado com o espaço de v, o controlador executa o comando de [atualização][helm-upgrade] do leme e o recipiente da aplicação é reconstruído e reimplantado.

## <a name="file-synchronization"></a>Sincronização de ficheiros

A primeira vez que uma aplicação é iniciada num espaço de v, todos os ficheiros de origem da aplicação são carregados. Enquanto a aplicação estiver em execução e posteriormente reinicia, apenas os ficheiros alterados são carregados. Dois ficheiros são usados para coordenar este processo: um ficheiro do lado do cliente e um ficheiro do lado do controlador.

O ficheiro do lado do cliente é armazenado num diretório temporário e é nomeado com base num hash do diretório do projeto que está a executar em Espaços Dev. Por exemplo, no Windows teria um ficheiro como *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef123456767890abcdef1234567890abcdef.synclog* para o seu projeto. No Linux, o ficheiro do lado do cliente é armazenado no diretório */tmp.* Pode encontrar o diretório no macOS executando o `echo $TMPDIR` comando.

Este ficheiro encontra-se em formato JSON e contém:

* Uma entrada para cada ficheiro de projeto que é sincronizado com o espaço dev
* Um ID de sincronização
* O carimbo temporal da última operação de sincronização

Cada entrada de ficheiro sinuoso contém um caminho para o ficheiro e a sua marca de tempo.

O ficheiro do lado do controlador está armazenado no cluster AKS. Contém o ID de sincronização e o carimbo temporal da última sincronização.

Uma sincronização acontece quando os selos de tempo de sincronização não correspondem entre o lado do cliente e os ficheiros do lado do controlador. Durante uma sincronização, o lado cliente que ferramenta itera sobre as entradas de ficheiros no ficheiro do lado do cliente. Se o carimbo de tempo do ficheiro for após a marca ção, o ficheiro está sincronizado com o espaço de dev. Uma vez concluída a sincronização, os selos de tempo sincronizados são atualizados tanto no lado do cliente como no lado do controlador.

Todos os ficheiros do projeto estão sincronizados se o ficheiro do lado do cliente não estiver presente. Este comportamento permite-lhe forçar uma sincronização completa, apagando o ficheiro do lado do cliente.

## <a name="how-running-your-code-is-configured"></a>Como executar o seu código é configurado

A Azure Dev `azds.yaml` Spaces utiliza o ficheiro para instalar e configurar o seu serviço. O controlador `install` utiliza a `azds.yaml` propriedade no ficheiro para instalar o gráfico Helm e criar os objetos Kubernetes:

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

Por padrão, `prep` o comando gerará a tabela Helm. Também define a propriedade *de instalação.chart* para o diretório da tabela Helm. Se quiser usar um gráfico Helm num local diferente, pode atualizar esta propriedade para usar essa localização.

Ao instalar as tabelas Helm, a Azure Dev Spaces fornece uma forma de sobrepor valores na tabela Helm. Os valores predefinidos `charts/APP_NAME/values.yaml`para o gráfico Helm estão dentro .

Utilizando a propriedade *install.values,* pode listar um ou mais ficheiros que definam valores que pretende ser substituídos na tabela Helm. Por exemplo, se quiser um nome de anfitrião ou configuração de base de dados especificamente ao executar a sua aplicação num espaço de v, pode utilizar esta funcionalidade de substituição. Também pode adicionar *um?* no final de qualquer um dos nomes de ficheiros para defini-lo como opcional.

A propriedade *install.set* permite-lhe configurar um ou mais valores que pretende ser substituído na tabela Helm. Quaisquer valores configurados na *instalação.set* sobrepor-se-ão aos valores configurados em ficheiros listados na *instalação.valores*. As propriedades em *instalação.set* dependem dos valores na tabela Helm e podem ser diferentes dependendo do gráfico helm gerado.

No exemplo acima, a propriedade *install.set.replicaCount* diz ao controlador quantas instâncias da sua aplicação para executar no seu espaço de dev. Dependendo do seu cenário, pode aumentar este valor, mas terá um impacto na anexação de um desordeiro à cápsula da sua aplicação. Para mais informações, consulte o artigo de resolução de [problemas.][troubleshooting]

No gráfico helm gerado, a imagem do recipiente está definida para *{{ . Valores.image.repositório }}:{{{ Valores.image.tag }}*. O `azds.yaml` ficheiro define a propriedade *install.set.image.tag* como *$(tag)* por padrão, que é usado como o valor para *{{ . Valores.image.tag }}*. Ao configurar a propriedade *install.set.image.tag* desta forma, permite que a imagem do recipiente para a sua aplicação seja marcada de forma distinta ao executar os Espaços Azure Dev. Neste caso específico, a imagem é marcada como * \<valor a partir de imagem.repositório>:$(tag)*. Deve utilizar a variável *$(tag)* como valor de *instalação.set.image.tag* para que os Espaços Dev reconheçam e localizem o recipiente no cluster AKS.

No exemplo acima, `azds.yaml` define *instalar.set.ingress.hosts*. A *propriedade install.set.ingress.hosts* define um formato de nome de anfitrião para pontos finais públicos. Esta propriedade também utiliza *$(spacePrefix),* *$(rootSpacePrefix)* e *$(hostSuffix)*, que são valores fornecidos pelo controlador.

O *$(spacePrefix)* é o nome do espaço de v infantil, que assume a forma de *SPACENAME.s*. O *$(rootSpacePrefix)* é o nome do espaço dos pais. Por exemplo, se *o azureuser* é um espaço infantil de *padrão*, o valor por *$(rootSpacePrefix)* é *padrão* e o valor de *$(spacePrefix)* é *azureuser.s*. Se o espaço não for um espaço para crianças, *$(spacePrefix)* está em branco. Por exemplo, se o espaço *predefinido* não tiver espaço para pais, o valor para *$(rootSpacePrefix)* é *padrão* e o valor de *$(spacePrefix)* está em branco. O *$(hostSuffix)* é um sufixo DNS que aponta para o Controlador de Ingress os Espaços Azure Dev que funciona no seu cluster AKS. Este sufixo DNS corresponde a uma entrada dNS wildcard, por * \*exemplo. RANDOM_VALUE.eus.azds.io,* que foi criado quando o controlador Azure Dev Spaces foi adicionado ao seu cluster AKS.

No ficheiro `azds.yaml` acima, também pode atualizar *instalar.set.ingress.hosts* para alterar o nome de anfitrião da sua aplicação. Por exemplo, se quiser simplificar o nome de anfitrião da sua aplicação a partir de *$(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)* para *$(spacePrefix)$(rootSpacePrefix)web$(hostSuffix)*.

Para construir o recipiente para a sua aplicação, `azds.yaml` o controlador utiliza as secções abaixo do ficheiro de configuração:

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

Para ajudá-lo a itear rapidamente durante o desenvolvimento, o Azure Dev Spaces sincronizará alterações do seu projeto local e atualizará incrementalmente a sua aplicação. A secção abaixo `azds.yaml` no ficheiro de configuração é utilizada para configurar o sincronizacro e a atualização:

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

Os ficheiros e diretórios que sincronizarão as alterações estão listados na *propriedade configurações.develop.container.sync.* Estes diretórios são sincronizados inicialmente quando executa o `up` comando, bem como quando são detetadas alterações. Se houver diretórios adicionais ou diferentes que você gostaria de sincronizar com o seu espaço de dev, você pode mudar esta propriedade.

As *configurações.develop.container.iterate.buildCommands* property especifica como construir a aplicação em um cenário de desenvolvimento. A *propriedade configurações.develop.container.command* fornece o comando para executar a aplicação num cenário de desenvolvimento. Você pode querer atualizar qualquer uma destas propriedades se houver bandeiras ou parâmetros adicionais de construção ou tempo de execução que você gostaria de usar durante o desenvolvimento.

As *configurações.develop.container.iterate.processesToKill* lista os processos a matar para parar a aplicação. Pode querer atualizar esta propriedade se quiser alterar o comportamento de reinício da sua aplicação durante o desenvolvimento. Por exemplo, se atualizou as *configurações.develop.container.iterate.buildCommands* ou *configurations.develop.container.command* properties to change how application is built or started, you may need to change what processs are stop.

Ao preparar o `azds prep` seu código utilizando o comando, tem a opção de adicionar a `--enable-ingress` bandeira. Adicionar `--enable-ingress` a bandeira cria um URL acessível ao público para a sua aplicação. Se omite esta bandeira, a aplicação só é acessível dentro do aglomerado ou utilizando o túnel de acolhimento local. Depois de `azds prep` executar o comando, pode alterar esta definição `charts/APPNAME/values.yaml`modificando a propriedade ativada para *ingresso* em:

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o networking e como os pedidos são encaminhados em Espaços Azure Dev ver [como o encaminhamento funciona com os Espaços Azure Dev.][how-it-works-routing]

Para saber mais sobre a utilização de Espaços Azure Dev para iterar e desenvolver rapidamente, veja como funciona o seu computador de [desenvolvimento com o seu espaço de v][how-it-works-connect] e como funciona a [depuração remota do seu código com a Azure Dev Spaces.][how-it-works-remote-debugging]

Para começar a usar o Azure Dev Spaces para executar o seu projeto, veja os seguintes quickstarts:

* [Iterado e depurado rapidamente com Código de Estúdio Visual e Java][quickstart-java]
* [Iterado e depurado rapidamente com Código de Estúdio Visual e .NET][quickstart-netcore]
* [Iterado e depurado rapidamente com Código de Estúdio Visual e Node.js][quickstart-node]
* [Iterado e depurado rapidamente com Visual Studio e .NET Core][quickstart-vs]
* [Usando o CLI para desenvolver uma aplicação em Kubernetes][quickstart-cli]


[azds-yaml-section]: #how-running-your-code-is-configured
[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[quickstart-cli]: quickstart-cli.md
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md
[sync-section]: #file-synchronization
[troubleshooting]: troubleshooting.md