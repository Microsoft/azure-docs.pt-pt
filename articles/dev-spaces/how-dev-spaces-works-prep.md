---
title: Funciona a preparação de um projeto para a Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descreve como funciona a preparação do seu projeto com a Azure Dev Spaces
keywords: azds.yaml, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores
ms.openlocfilehash: 61351072494b51d02a1d6c31399208b9e9b54fce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88213427"
---
# <a name="how-preparing-a-project-for-azure-dev-spaces-works"></a>Funciona a preparação de um projeto para a Azure Dev Spaces

A Azure Dev Spaces fornece-lhe múltiplas formas de iteração rápida e depuração rápida de aplicações kubernetes e colaborar com a sua equipa num cluster Azure Kubernetes Service (AKS). Dev Spaces pode gerar gráficos dockerfiles e Helm para o seu projeto. Dev Spaces também cria e utiliza um ficheiro de configuração para implementar, executar e depurar as suas aplicações Kubernetes em AKS. Todos estes ficheiros residem no código da sua aplicação e podem ser adicionados ao sistema de controlo da sua versão.

Este artigo descreve o que acontece para preparar o seu projeto para correr em AKS com Dev Spaces.

## <a name="prepare-your-code"></a>Prepare o seu código

Para executar a sua aplicação num espaço dev, ele precisa de ser contentorizado, e você precisa definir como deve ser implantado em Kubernetes. Para fazer a sua aplicação, precisa de um Dockerfile. Para definir como a sua aplicação é implantada em Kubernetes, precisa de um [gráfico Helm](https://docs.helm.sh/). Para ajudar na criação tanto do gráfico Dockerfile como do Helm para a sua aplicação, as ferramentas do lado do cliente fornecem o `prep` comando:

```cmd
azds prep --enable-ingress
```

O `prep` comando irá analisar os ficheiros do seu projeto e tentar criar o gráfico Dockerfile e Helm para executar a sua aplicação em Kubernetes. Atualmente, o `prep` comando gerará um gráfico de Dockerfile e Helm com as seguintes línguas:

* Java
* Node.js
* .NET Core

*Tem de* executar o comando a partir de `prep` um diretório que contenha código fonte. Executar o `prep` comando a partir do diretório correto permite que a ferramenta do lado do cliente identifique o idioma e crie um Dockerfile apropriado para contentorizar a sua aplicação. Você também pode executar o `prep` comando a partir de um diretório que contém um arquivo *pom.xml* para projetos java.

Se executar o `prep` comando a partir de diretório que não contenha código fonte, a ferramenta do lado do cliente não gerará um Dockerfile. Também apresentará um erro dizendo: *O Dockerfile não pôde ser gerado devido a uma linguagem não apoiada*. Este erro também ocorre se a ferramenta do lado do cliente não reconhecer o tipo de projeto.

Quando executar o `prep` comando, tem a opção de especificar a `--enable-ingress` bandeira. Esta bandeira diz ao controlador para criar um ponto final acessível à Internet para este serviço. Se não especificar esta bandeira, o serviço só é acessível a partir do cluster ou utilizando o túnel local criado pela ferramenta do lado do cliente. Pode ativar ou desativar este comportamento depois de executar o `prep` comando atualizando o gráfico Helm gerado.

O `prep` comando não substituirá quaisquer dockerfiles existentes ou gráficos helm que tenha no seu projeto. Se um gráfico de Dockerfile ou Helm existente utilizar a mesma convenção de nomeação que os ficheiros gerados pelo `prep` comando, o `prep` comando saltará para gerar esses ficheiros. Caso contrário, o `prep` comando gerará o seu próprio gráfico Dockerfile ou Helm juntamente com os ficheiros existentes.

> [!IMPORTANT]
> A Azure Dev Spaces usa o gráfico Dockerfile e Helm para o seu projeto para construir e executar o seu código, mas pode modificar estes ficheiros se quiser alterar a forma como o projeto é construído e executado.

O `prep` comando também gerará um ficheiro na raiz `azds.yaml` do seu projeto. A Azure Dev Spaces utiliza este ficheiro para construir, instalar, configurar e executar a sua aplicação. Este ficheiro de configuração lista a localização do seu gráfico Dockerfile e Helm e também fornece uma configuração adicional em cima desses artefactos.

Aqui está um ficheiro azds.yaml de exemplo criado usando [a aplicação de amostra .NET Core](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend):

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

O `azds.yaml` ficheiro gerado pelo comando `prep` destina-se a trabalhar para um cenário simples e único de desenvolvimento de projetos. Se o seu projeto específico tiver uma complexidade aumentada, poderá ter de atualizar este ficheiro depois de executar o `prep` comando. Por exemplo, o seu projeto pode exigir algumas alterações no seu processo de construção ou lançamento com base nas suas necessidades de desenvolvimento ou depuração. Também pode ter várias aplicações no seu projeto, que requerem múltiplos processos de construção ou um conteúdo de construção diferente.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como executar o seu código no seu espaço dev, veja [como funciona o seu código com a Azure Dev Spaces][how-it-works-up].

[how-it-works-up]: how-dev-spaces-works-up.md