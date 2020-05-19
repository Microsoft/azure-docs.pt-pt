---
title: GitHub Actions fluxos de trabalho para web apps estáticas azure
description: Aprenda a usar os repositórios GitHub para configurar a implementação contínua para aplicações web estáticas azure.
services: static-web-apps
author: christiannwamba
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: chnwamba
ms.openlocfilehash: c7180be2afa541f743d7bd81627b93f34d9bb4eb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597843"
---
# <a name="github-actions-workflows-for-azure-static-web-apps-preview"></a>GitHub Actions fluxos de trabalho para pré-visualização de aplicações web estáticas azure

Ao criar um novo recurso Azure Static Web App, o Azure gera um fluxo de trabalho gitHub Actions para controlar a implementação contínua da app. O fluxo de trabalho é conduzido por um ficheiro YAML. Este artigo detalha a estrutura e as opções do ficheiro workflow.

As implantações são [iniciadas](#triggers)por gatilhos , que executam [trabalhos](#jobs) definidos por [passos](#steps)individuais .

## <a name="file-location"></a>Localização do arquivo

Quando liga o seu repositório GitHub a Aplicações Web Estáticas Azure, um ficheiro de fluxo de trabalho é adicionado ao repositório.

Siga estes passos para visualizar o ficheiro de fluxo de trabalho gerado.

1. Abra o repositório da aplicação no GitHub.
1. A partir do separador _Código,_ clique na `.github/workflows` pasta.
1. Clique no ficheiro com um nome que se pareça `azure-static-web-apps-<RANDOM_NAME>.yml` com .

O ficheiro YAML no seu repositório será semelhante ao seguinte exemplo:

```yml
name: Azure Static Web Apps CI/CD

on:
  push:
    branches:
    - master
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
    - master

jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
    - uses: actions/checkout@v1
    - name: Build And Deploy
      id: builddeploy
      uses: Azure/static-web-apps-deploy@v0.0.1-preview
      with:
        azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
        repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
        action: 'upload'
        ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
        app_location: '/' # App source code path
        api_location: 'api' # Api source code path - optional
        app_artifact_location: 'dist' # Built app content directory - optional
        ###### End of Repository/Build Configurations ######

  close_pull_request_job:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Job
    steps:
    - name: Close Pull Request
      id: closepullrequest
      uses: Azure/static-web-apps-deploy@v0.0.1-preview
      with:
        azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
        action: 'close'
```

## <a name="triggers"></a>Acionadores

Um [gatilho](https://help.github.com/actions/reference/events-that-trigger-workflows) gitHub Actions identifica um fluxo de trabalho gitHub Actions para executar um trabalho baseado em gatilhos de eventos. Os gatilhos estão listados usando a `on` propriedade no ficheiro de fluxo de trabalho.

```yml
on:
  push:
    branches:
    - master
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
    - master
```

Através de configurações associadas à `on` propriedade, pode definir quais os ramos que desencadeiam um trabalho, e acionar gatilhos para disparar para diferentes estados de pedido de puxar.

Neste exemplo, um fluxo de trabalho é iniciado à medida que o ramo _principal_ muda. As alterações que iniciam o fluxo de trabalho incluem empurrar os compromissos e abrir pedidos de puxar contra o ramo escolhido.

## <a name="jobs"></a>Tarefas

Cada gatilho de evento requer um manipulador de eventos. [Os empregos](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs) definem o que acontece quando um evento é desencadeado.

No ficheiro de fluxo de trabalho de Aplicações Web Estáticas, existem dois postos de trabalho disponíveis.

| Name  | Descrição |
|---------|---------|
|`build_and_deploy_job` | Executa quando empurra compromete ou abre um pedido de puxão contra a sucursal listada na `on` propriedade. |
|`close_pull_request_job` | Executa apenas quando fecha um pedido de puxão. |

## <a name="steps"></a>Passos

Os passos são tarefas sequenciais para um trabalho. Um passo realiza ações como instalar dependências, fazer testes e implementar a sua aplicação para a produção.

Um ficheiro de fluxo de trabalho define os seguintes passos.

| Tarefa  | Passos  |
|---------|---------|
| `build_and_deploy_job` |<ol><li>Verifica o repositório no ambiente da Ação.<li>Constrói e implementa o repositório para Aplicações Web Estáticas Azure.</ol>|
| `close_pull_request_job` | <ol><li>Notifica as Aplicações Web Estáticas Azure que um pedido de pull foi fechado.</ol>|

## <a name="build-and-deploy"></a>Criar e implementar

O passo nomeado constrói e desdobra para a sua instância de `Build and Deploy` Aplicações Web Estáticas Azure. Por baixo da `with` secção, pode personalizar os seguintes valores para a sua implementação.

```yml
with:
    azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
    repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
    action: 'upload'
    ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
    app_location: '/' # App source code path
    api_location: 'api' # Api source code path - optional
    app_artifact_location: 'dist' # Built app content directory - optional
    ###### End of Repository/Build Configurations ######
```

| Propriedade | Descrição | Necessário |
|---|---|---|
| `app_location` | Localização do seu código de candidatura.<br><br>Por exemplo, introduza `/` se o código fonte da sua aplicação estiver na raiz do repositório, ou se o seu código de `/app` aplicação estiver num diretório chamado `app` . | Sim |
| `api_location` | Localização do seu código funções Azure.<br><br>Por exemplo, introduza se o seu código de `/api` aplicação estiver numa pasta chamada `api` . Se não for detetada nenhuma aplicação De Funções Azure na pasta, a construção não falha, o fluxo de trabalho assume que não quer uma API. | Não |
| `app_artifact_location` | Localização do diretório de saída de construção em relação ao `app_location` .<br><br>Por exemplo, se o código fonte da aplicação estiver localizado em `/app` , e o script de construção for ficheiros para a `/app/build` pasta, então definido `build` como o `app_artifact_location` valor. | Não |

As `repo_token` , e os `action` `azure_static_web_apps_api_token` valores são definidos para si por Aplicações Web Estáticas Do Azure não devem ser alterados manualmente.

## <a name="custom-build-commands"></a>Comandos de construção personalizados

Pode ter um controlo fino sobre os comandos executados durante uma implantação. Os seguintes comandos podem ser definidos sob a secção de um `with` trabalho.

A implantação liga sempre antes de `npm install` qualquer comando personalizado.

| Comando            | Descrição |
|---------------------|-------------|
| `app_build_command` | Define um comando personalizado a executar durante a implementação da aplicação de conteúdo estático.<br><br>Por exemplo, configurar uma construção de produção para uma aplicação Angular entrar `ng build -prod` . Se ficar em branco, o fluxo de trabalho tenta executar os `npm run build` comandos ou `npm run build:Azure` comandos.  |
| `api_build_command` | Define um comando personalizado a executar durante a implementação da aplicação API funções Azure. |

## <a name="route-file-location"></a>Localização de ficheiros de rota

Pode personalizar o fluxo de trabalho para procurar as [rotas.json](routes.md) em qualquer pasta do seu repositório. A seguinte propriedade pode ser definida sob a secção de um `with` trabalho.

| Propriedade            | Descrição |
|---------------------|-------------|
| `routes_location` | Define a localização do diretório onde o ficheiro _routes.json_ é encontrado. Esta localização é relativa à raiz do repositório. |

 Ser explícito sobre a localização do ficheiro _routes.json_ é particularmente importante se o seu passo de construção de estrutura frontal não mover este ficheiro para o `app_artifact_location` padrão.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Reveja os pedidos de retirada em ambientes de pré-produção](review-publish-pull-requests.md)
