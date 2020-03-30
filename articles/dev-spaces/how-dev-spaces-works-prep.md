---
title: Como funciona a preparação de um projeto para a Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descreve como funciona a preparação do seu projeto com a Azure Dev Spaces
keywords: azds.yaml, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores
ms.openlocfilehash: 24a54fffdc8e94493d2a4a9aeb1c5f02dcd192b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241637"
---
# <a name="how-preparing-a-project-for-azure-dev-spaces-works"></a>Como funciona a preparação de um projeto para a Azure Dev Spaces

A Azure Dev Spaces fornece-lhe múltiplas formas de iterar e depurar rapidamente aplicações kubernetes e colaborar com a sua equipa num cluster do Serviço Azure Kubernetes (AKS). A Dev Spaces pode gerar Dockerfiles e gráficos Helm para o seu projeto. A Dev Spaces também cria e utiliza um ficheiro de configuração para implementar, executar e depurar as suas aplicações Kubernetes no AKS. Todos estes ficheiros residem com o código da sua aplicação e podem ser adicionados ao seu sistema de controlo de versão.

Este artigo descreve o que acontece que prepara o seu projeto para correr em AKS com Dev Spaces.

## <a name="prepare-your-code"></a>Prepare o seu código

Para executar a sua aplicação num espaço de v, ela precisa de ser contentorizada, e você precisa definir como deve ser implantado em Kubernetes. Para contentorizar a sua aplicação, precisa de um Dockerfile. Para definir como a sua aplicação é implantada para Kubernetes, você precisa de um [gráfico Helm](https://docs.helm.sh/). Para ajudar na criação do gráfico Dockerfile e Helm para `prep` a sua aplicação, as ferramentas do lado do cliente fornecem o comando:

```cmd
azds prep --enable-ingress
```

O `prep` comando irá analisar os ficheiros do seu projeto e tentar criar o gráfico Dockerfile e Helm para executar a sua aplicação em Kubernetes. Atualmente, `prep` o comando gerará um gráfico de Dockerfile e Helm com as seguintes línguas:

* Java
* Node.js
* .NET Core

Tem *de* `prep` executar o comando a partir de um diretório que contenha código fonte. Executar `prep` o comando a partir do diretório correto permite que a ferramenta do lado do cliente identifique o idioma e crie um Dockerfile apropriado para containerizar a sua aplicação. Você também pode `prep` executar o comando a partir de um diretório que contém um arquivo *pom.xml* para projetos Java.

Se executar `prep` o comando a partir do diretório que não contenha código fonte, a ferramenta do lado do cliente não gerará um Dockerfile. Também apresentará um erro dizendo: *O Dockerfile não pôde ser gerado devido a uma linguagem não suportada*. Este erro também ocorre se a ferramenta do lado do cliente não reconhecer o tipo de projeto.

Quando dirige `prep` o comando, tem a opção de especificar a `--enable-ingress` bandeira. Esta bandeira diz ao controlador para criar um ponto final acessível à Internet para este serviço. Se não especificar esta bandeira, o serviço só é acessível a partir do interior do cluster ou utilizando o túnel local de acolhimento criado pela ferramenta do lado do cliente. Pode ativar ou desativar `prep` este comportamento depois de executar o comando atualizando a tabela Helm gerada.

O `prep` comando não substituirá quaisquer ficheiros Docker existentes ou gráficos Helm existentes no seu projeto. Se um dockerfile ou gráfico Helm existente sustiver a `prep` mesma convenção de nomeação que os ficheiros gerados pelo comando, o `prep` comando saltará gerando esses ficheiros. Caso contrário, `prep` o comando gerará o seu próprio dockerfile ou gráfico Helm juntamente com os ficheiros existentes.

> [!IMPORTANT]
> A Azure Dev Spaces utiliza o gráfico Dockerfile e Helm para o seu projeto para construir e executar o seu código, mas pode modificar estes ficheiros se quiser alterar a forma como o projeto é construído e executado.

O `prep` comando também `azds.yaml` gerará um ficheiro na raiz do seu projeto. A Azure Dev Spaces utiliza este ficheiro para construir, instalar, configurar e executar a sua aplicação. Este ficheiro de configuração lista a localização do seu dockerfile e gráfico Helm e também fornece configuração adicional em cima desses artefactos.

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

O `azds.yaml` ficheiro gerado `prep` pelo comando destina-se a funcionar para um cenário simples e único de desenvolvimento do projeto. Se o seu projeto específico tiver uma maior complexidade, poderá `prep` ter de atualizar este ficheiro depois de executar o comando. Por exemplo, o seu projeto pode exigir algumas alterações no seu processo de construção ou lançamento com base nas suas necessidades de desenvolvimento ou depuração. Você também pode ter várias aplicações no seu projeto, que requerem vários processos de construção ou um conteúdo de construção diferente.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre executar o seu código no seu espaço de v, veja [como funciona o seu código com o Azure Dev Spaces.][how-it-works-up]

Para começar a usar o Azure Dev Spaces para preparar o seu projeto para o Espaço Azure Dev, consulte os seguintes quickstarts:

* [Iterado e depurado rapidamente com Código de Estúdio Visual e Java][quickstart-java]
* [Iterado e depurado rapidamente com Código de Estúdio Visual e .NET][quickstart-netcore]
* [Iterado e depurado rapidamente com Código de Estúdio Visual e Node.js][quickstart-node]
* [Iterado e depurado rapidamente com Visual Studio e .NET Core][quickstart-vs]
* [Usando o CLI para desenvolver uma aplicação em Kubernetes][quickstart-cli]

[how-it-works-up]: how-dev-spaces-works-up.md
[quickstart-cli]: quickstart-cli.md
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md