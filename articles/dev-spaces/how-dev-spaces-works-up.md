---
title: Como funciona o seu código com a Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descreve os processos de execução do seu código no Serviço Azure Kubernetes com Espaços Azure Dev
keywords: azds.yaml, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores
ms.openlocfilehash: 1cace325f9415d46210636e5c04cc2d75589cc11
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014436"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>Como funciona o seu código com a Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

A Azure Dev Spaces fornece-lhe múltiplas formas de iteração rápida e depuração rápida de aplicações kubernetes e colaborar com a sua equipa num cluster Azure Kubernetes Service (AKS). Uma vez que o seu [projeto esteja preparado para funcionar num espaço dev,][how-it-works-prep]você pode usar Dev Spaces para construir e executar o seu projeto no seu cluster AKS.

Este artigo descreve o que acontece executar o seu código em AKS com Dev Spaces.

## <a name="run-your-code"></a>Executar o seu código

Para executar o seu código num espaço dev, emita o `up` comando no mesmo diretório que o seu `azds.yaml` ficheiro:

```cmd
azds up
```

O `up` comando envia os ficheiros de origem da aplicação e outros artefactos necessários para construir e executar o seu projeto no espaço dev. A partir daí, o controlador no seu espaço dev:

1. Cria os objetos Kubernetes para implementar a sua aplicação.
1. Constrói o recipiente para a sua aplicação.
1. Implementa a sua aplicação para o espaço dev.
1. Cria um nome DNS acessível ao público para o seu ponto final de aplicação se configurado.
1. Utiliza *porta-a-frente* para fornecer acesso ao seu ponto final de aplicação utilizando http://localhost .
1. Encaminha a sestramento e stderr para a ferramenta do lado do cliente.


## <a name="starting-a-service"></a>Iniciar um serviço

Quando inicia um serviço num espaço dev, a ferramenta do lado do cliente e o controlador trabalham em coordenação para sincronizar os seus ficheiros de origem, criar o seu contentor e objetos Kubernetes e executar a sua aplicação.

A um nível mais granular, eis o que acontece quando se `azds up` corre:

1. [Os ficheiros são sincronizados][sync-section] do computador do utilizador para um armazenamento de ficheiros Azure exclusivo do cluster AKS do utilizador. O código-fonte, o gráfico helm e os ficheiros de configuração são carregados.
1. O controlador cria um pedido para iniciar uma nova sessão. Este pedido contém várias propriedades, incluindo um ID único, nome espacial, caminho para código fonte, e uma bandeira de depurante.
1. O controlador substitui o espaço reservado *de $(tag)* na tabela Helm pelo ID de sessão único e instala a tabela Helm para o seu serviço. A adição de uma referência ao ID de sessão único ao gráfico Helm permite que o contentor implantado no cluster AKS para esta sessão específica seja ligado ao pedido de sessão e às informações associadas.
1. Durante a instalação do gráfico Helm, o servidor de admissão de Webhook Kubernetes adiciona recipientes adicionais à cápsula da sua aplicação para instrumentação e acesso ao código fonte do seu projeto. Os recipientes devspaces-proxy e devspaces-proxy-init são adicionados para fornecer rastreio HTTP e encaminhamento espacial. O recipiente de construção de devspaces é adicionado para fornecer à cápsula acesso à instância Docker e código fonte de projeto para a construção do recipiente da sua aplicação.
1. Quando a cápsula da aplicação é iniciada, o recipiente de devspaces-build e o recipiente de devspaces-proxy-init são usados para construir o recipiente de aplicação. O recipiente de aplicação e os recipientes de procuração de devspaces são então iniciados.
1. Após o início do recipiente de aplicação, a funcionalidade do lado do cliente utiliza a funcionalidade *porta-a-frente* Kubernetes para fornecer acesso HTTP à sua aplicação ao longo http://localhost de . Esta porta de reencaminhamento liga o seu computador de desenvolvimento ao serviço no seu espaço dev.
1. Quando todos os contentores da cápsula começarem, o serviço está a funcionar. Neste ponto, a funcionalidade do lado do cliente começa a transmitir os vestígios HTTP, stdout e stderr. Esta informação é apresentada pela funcionalidade do lado do cliente para o desenvolvedor.

## <a name="updating-a-running-service"></a>Atualizar um serviço de execução

Enquanto um serviço está em execução, a Azure Dev Spaces tem a capacidade de atualizar esse serviço se algum dos ficheiros de origem do projeto mudar. Dev Spaces também lida com a atualização do serviço de forma diferente, dependendo do tipo de ficheiro que é alterado. Existem três formas de a Dev Spaces atualizar um serviço de execução:

* Atualizar diretamente um ficheiro
* Reconstruir e reiniciar o processo da aplicação dentro do contentor da aplicação em execução
* Reconstrução e reafectação do contentor da aplicação

![Sincronização de ficheiros Azure Dev Spaces](media/how-dev-spaces-works/file-sync.svg)

Certos ficheiros de projetos que são ativos estáticos, tais como ficheiros html, css e cshtml, podem ser atualizados diretamente no contentor da aplicação sem reiniciar nada. Se um ativo estático mudar, o novo ficheiro é sincronizado no espaço dev e depois utilizado pelo recipiente de funcionamento.

As alterações a ficheiros como código fonte ou ficheiros de configuração de aplicações podem ser aplicadas reiniciando o processo da aplicação dentro do recipiente de funcionamento. Uma vez sincronizados estes ficheiros, o processo da aplicação é reiniciado dentro do recipiente de funcionamento utilizando o processo *devhostagent.* Ao criar inicialmente o contentor da aplicação, o controlador substitui o comando de arranque para a aplicação por um processo diferente chamado *devhostagent*. O processo real da aplicação é então executado como um processo infantil sob *devhostagent*, e sua saída é canalizada usando a saída *de devhostagent.* O processo *devhostagent* também faz parte da Dev Spaces e pode executar comandos no contentor de funcionamento em nome da Dev Spaces. Ao realizar um reinício, *devhostagent:*

* Para o processo ou processos atuais associados à aplicação
* Reconstrói a aplicação
* Reinicia o processo ou processos associados à aplicação

A forma como *o devhostagent* executa os passos anteriores está [configurada em `azds.yaml`][azds-yaml-section].

As atualizações para ficheiros de projetos como Dockerfiles, ficheiros csproj ou qualquer parte do gráfico Helm requerem que o contentor da aplicação seja reconstruído e redistribuído. Quando um destes ficheiros é sincronizado com o espaço dev, o controlador executa o comando de [atualização][helm-upgrade] do leme e o contentor da aplicação é reconstruído e redistribuído.

## <a name="file-synchronization"></a>Sincronização de ficheiros

A primeira vez que uma aplicação é iniciada num espaço dev, todos os ficheiros de origem da aplicação são carregados. Enquanto a aplicação está em execução e em posteriores reiniciam, apenas os ficheiros alterados são carregados. São utilizados dois ficheiros para coordenar este processo: um ficheiro do lado do cliente e um ficheiro do lado do controlador.

O ficheiro do lado do cliente está armazenado num diretório temporário e é nomeado com base num hash do diretório do projeto que está a executar em Dev Spaces. Por exemplo, no Windows teria um ficheiro como *Utilizadores\USERNAME\AppData\Local\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.sync.* No Linux, o ficheiro do lado do cliente é armazenado no *diretório /tmp.* Você pode encontrar o diretório no macOS executando o `echo $TMPDIR` comando.

Este ficheiro está no formato JSON e contém:

* Uma entrada para cada ficheiro de projeto sincronizado com o espaço dev
* Um ID de sincronização
* O tempotad da última operação de sincronização

Cada entrada de ficheiro do projeto contém um caminho para o ficheiro e a sua hora de jogo.

O ficheiro do lado do controlador é armazenado no cluster AKS. Contém o ID de sincronização e o tempo da última sincronização.

Uma sincronização acontece quando os prazos de sincronização não coincidem entre o lado do cliente e os ficheiros do lado do controlador. Durante uma sincronização, a ferramenta do lado do cliente itera sobre as entradas de ficheiro no ficheiro do lado do cliente. Se a estada de tempo do ficheiro for após a sincronização do tempo, esse ficheiro é sincronizado com o espaço dev. Uma vez concluída a sincronização, os mapas temporais de sincronização são atualizados tanto no lado do cliente como nos ficheiros do lado do controlador.

Todos os ficheiros do projeto são sincronizados se o ficheiro do lado do cliente não estiver presente. Este comportamento permite-lhe forçar uma sincronização completa eliminando o ficheiro do lado do cliente.

## <a name="how-running-your-code-is-configured"></a>Como executar o seu código é configurado

A Azure Dev Spaces utiliza o `azds.yaml` ficheiro para instalar e configurar o seu serviço. O controlador utiliza a `install` propriedade no ficheiro para instalar o gráfico Helm e criar os `azds.yaml` objetos Kubernetes:

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

Por predefinição, o `prep` comando gerará o gráfico Helm. Também define a propriedade *install.chart* para o diretório da tabela Helm. Se quiser usar um gráfico Helm em um local diferente, pode atualizar esta propriedade para usar essa localização.

Ao instalar as tabelas Helm, a Azure Dev Spaces fornece uma forma de anular valores na tabela Helm. Os valores predefinidos para o gráfico Helm estão em `charts/APP_NAME/values.yaml` .

Utilizando a propriedade *install.values,* pode listar um ou mais ficheiros que definem os valores que pretende ser substituídos na tabela Helm. Por exemplo, se você quiser um nome de anfitrião ou configuração de base de dados especificamente ao executar a sua aplicação em um espaço dev, você pode usar esta funcionalidade de substituição. Também pode adicionar *um?* no final de qualquer um dos nomes de ficheiros para defini-lo como opcional.

A propriedade *install.set* permite-lhe configurar um ou mais valores que pretende substituir na tabela Helm. Quaisquer valores configurados no *install.set* sobrepõem-se aos valores configurados nos ficheiros listados em *valores de instalação.* As propriedades em *instalação.conjunto* dependem dos valores na tabela Helm e podem ser diferentes dependendo do gráfico helm gerado.

No exemplo acima, a propriedade *install.set.replicaCount* diz ao controlador quantas instâncias da sua aplicação devem ser executadas no seu espaço dev. Dependendo do seu cenário, pode aumentar este valor, mas terá um impacto na anexação de um depurante à cápsula da sua aplicação. Para mais informações, consulte o [artigo de resolução de problemas.][troubleshooting]

Na tabela Helm gerada, a imagem do contentor é definida para *{{{. . Values.image.repositório }}:{{{ . Values.image.tag }}*. O `azds.yaml` ficheiro define *instalar.set.image.tag* propriedade como *$(tag)* por padrão, que é usado como o valor para *{{ . Values.image.tag }}*. Ao definir a propriedade *install.set.image.tag* desta forma, permite que a imagem do recipiente para a sua aplicação seja marcada de forma distinta ao executar espaços Azure Dev. Neste caso específico, a imagem é marcada como *\<value from image.repository> :$(tag)*. Deve utilizar a variável *$(tag)* como o valor da   *instalação.set.image.tag* para que os Espaços Dev reconheçam e localizem o recipiente no cluster AKS.

No exemplo acima, `azds.yaml` define *instalar.set.ingress.hosts*. A *propriedade install.set.ingress.hosts* define um formato de nome anfitrião para pontos finais públicos. Esta propriedade também usa *$(spacePrefix)*, *$(rootSpacePrefix)* e *$(hostSuffix)*, que são valores fornecidos pelo controlador.

O *$(spacePrefix)* é o nome do espaço dev infantil, que assume a forma de *SPACENAME.s*. *O $(rootSpacePrefix)* é o nome do espaço dos pais. Por exemplo, se *o azureuser* for um espaço de *padrão* infantil, o valor para *$(rootSpacePrefix)* é *padrão* e o valor de *$(spacePrefix)* é *azureuser.s*. Se o espaço não for um espaço para crianças, *$(spacePrefix)* está em branco. Por exemplo, se o espaço *predefinido* não tiver espaço para pais, o valor para *$(rootSpacePrefix)* é *padrão* e o valor de *$(spacePrefix)* está em branco. O *$(hostS suix)* é um sufixo DNS que aponta para o Controlador de Ingress Azure Dev Spaces que funciona no seu cluster AKS. Este sufixo DNS corresponde a uma entrada de DNS wildcard, por exemplo *\* . RANDOM_VALUE.eus.azds.io,* que foi criado quando o controlador Azure Dev Spaces foi adicionado ao seu cluster AKS.

No `azds.yaml` ficheiro acima, também pode atualizar *instalar.set.ingress.hosts* para alterar o nome de anfitrião da sua aplicação. Por exemplo, se quiser simplificar o nome de anfitrião da sua aplicação de *$(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)* para *$(spacePrefix)$(rootSpacePrefix)web$(hostSuffix)*.

Para construir o recipiente para a sua aplicação, o controlador utiliza as secções abaixo do ficheiro de `azds.yaml` configuração:

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

A *propriedade build.context* lista o diretório onde existem os Dockerfiles. A *propriedadebuild.dockerfile* define o nome do Dockerfile para a construção da versão de produção da aplicação. A *propriedadeconfigurations.develop.build.dockerfile* configura o nome do Dockerfile para a versão de desenvolvimento da aplicação.

Ter diferentes Dockerfiles para desenvolvimento e produção permite-lhe permitir certas coisas durante o desenvolvimento e desativar esses itens para implementações de produção. Por exemplo, pode permitir a depuração ou mais registo verboso durante o desenvolvimento e desativar num ambiente de produção. Também pode atualizar estas propriedades se os seus Dockerfiles forem nomeados de forma diferente ou se estiverem num local diferente.

Para ajudá-lo a iterar rapidamente durante o desenvolvimento, o Azure Dev Spaces sincronizará as alterações do seu projeto local e atualizará gradualmente a sua aplicação. A secção abaixo no `azds.yaml` ficheiro de configuração é utilizada para configurar a sincronização e a atualização:

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

Os ficheiros e diretórios que sincronizarão alterações estão listados na propriedade *configurations.develop.container.sync.* Estes diretórios são sincronizados inicialmente quando executam o `up` comando, bem como quando são detetadas alterações. Se houver diretórios adicionais ou diferentes que você gostaria de sincronizado com o seu espaço dev, você pode mudar esta propriedade.

As *configurações.develop.container.iterate.buildCommands* propriedade especifica como construir a aplicação em um cenário de desenvolvimento. A *propriedade configurações.develop.container.command* fornece o comando para executar a aplicação em um cenário de desenvolvimento. Pode querer atualizar qualquer uma destas propriedades se houver bandeiras ou parâmetros adicionais de construção ou de execução que gostaria de utilizar durante o desenvolvimento.

As *configurações.develop.container.iterate.processesToKill* lista os processos a matar para parar a aplicação. Pode querer atualizar esta propriedade se quiser alterar o comportamento de reinício da sua aplicação durante o desenvolvimento. Por exemplo, se atualizar as *configurações.develop.container.iterate.buildCommands* ou *configurações.develop.container.command* properties para alterar a forma como a aplicação é construída ou iniciada, poderá ter de alterar os processos que estão parados.

Ao preparar o seu código utilizando o `azds prep` comando, tem a opção de adicionar a `--enable-ingress` bandeira. A adição da `--enable-ingress` bandeira cria um URL acessível ao público para a sua aplicação. Se omitir esta bandeira, a aplicação só é acessível dentro do aglomerado ou utilizando o túnel local. Depois de executar o `azds prep` comando, pode alterar esta definição modificando a propriedade *ingress.ativada* em `charts/APPNAME/values.yaml` :

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre networking e como os pedidos são encaminhados em Azure Dev Spaces veja [como o encaminhamento funciona com a Azure Dev Spaces][how-it-works-routing].

Para saber mais sobre a rápida iterização e desenvolvimento com kubernetes, veja [como funciona a Ponte para Kubernetes][how-it-works-bridge-to-kubernetes] e como funciona a [depuração remota do seu código com a Azure Dev Spaces.][how-it-works-remote-debugging]


[azds-yaml-section]: #how-running-your-code-is-configured
[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[sync-section]: #file-synchronization
[troubleshooting]: troubleshooting.md