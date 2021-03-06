---
title: GitHub Actions fluxos de trabalho para Azure Static Web Apps
description: Aprenda a usar repositórios GitHub para configurar a implementação contínua para Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: b20a1670c13a272ed48088567a205d854ac99179
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791252"
---
# <a name="github-actions-workflows-for-azure-static-web-apps-preview"></a>GitHub Actions fluxos de trabalho para Azure Static Web Apps Preview

Quando cria um novo recurso Azure Static Web Apps, o Azure gera um fluxo de trabalho GitHub Actions para controlar a implementação contínua da aplicação. O fluxo de trabalho é impulsionado por um ficheiro YAML. Este artigo detalha a estrutura e as opções do ficheiro de fluxo de trabalho.

As implementações são iniciadas por [gatilhos,](#triggers)que executam [trabalhos](#jobs) que são definidos por [passos](#steps)individuais.

> [!NOTE]
> A azure Static Web Apps também suporta Azure DevOps. Consulte [a Publicação com a Azure DevOps](publish-devops.md) para obter informações sobre a criação de um oleoduto.

## <a name="file-location"></a>Localização do arquivo

Quando liga o seu repositório GitHub a Azure Static Web Apps, um ficheiro de fluxo de trabalho é adicionado ao repositório.

Siga estes passos para ver o ficheiro de fluxo de trabalho gerado.

1. Abra o repositório da aplicação no GitHub.
1. A partir do separador _Código,_ clique na `.github/workflows` pasta.
1. Clique no ficheiro com um nome que se pareça `azure-static-web-apps-<RANDOM_NAME>.yml` com .

O ficheiro YAML no seu repositório será semelhante ao seguinte exemplo:

```yml
name: Azure Static Web Apps CI/CD

on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main

jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
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
          output_location: 'dist' # Built app content directory - optional
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

Um [gatilho](https://help.github.com/actions/reference/events-that-trigger-workflows) de ações gitHub notifica um fluxo de trabalho de GitHub Actions para executar um trabalho baseado em gatilhos de eventos. Os gatilhos estão listados utilizando a `on` propriedade no ficheiro workflow.

```yml
on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
```

Através de configurações associadas à `on` propriedade, pode definir quais os ramos que desencadeiam um trabalho, e definir gatilhos para disparar para diferentes estados de pedido de pull.

Neste exemplo, um fluxo de trabalho é iniciado à medida que o ramo _principal_ muda. As alterações que iniciam o fluxo de trabalho incluem a flexidção de compromissos e a abertura de pedidos contra o ramo escolhido.

## <a name="jobs"></a>Tarefas

Cada gatilho do evento requer um manipulador de eventos. [Os](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs) empregos definem o que acontece quando um evento é desencadeado.

No ficheiro de fluxo de trabalho static Web Apps, existem dois empregos disponíveis.

| Nome                     | Descrição                                                                                                    |
| ------------------------ | -------------------------------------------------------------------------------------------------------------- |
| `build_and_deploy_job`   | Executa quando empurra comete ou abre um pedido de puxão contra a sucursal listada na `on` propriedade.          |
| `close_pull_request_job` | Executa apenas quando fecha um pedido de puxar, que remove o ambiente de encenação criado a partir de pedidos de puxar. |

## <a name="steps"></a>Passos

Passos são tarefas sequenciais para um trabalho. Um passo realiza ações como instalar dependências, fazer testes e implementar a sua aplicação para a produção.

Um ficheiro de fluxo de trabalho define os seguintes passos.

| Tarefa                      | Passos                                                                                                                              |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------- |
| `build_and_deploy_job`   | <ol><li>Verifica o repositório no ambiente da Ação.<li>Constrói e implementa o repositório para a Azure Static Web Apps.</ol> |
| `close_pull_request_job` | <ol><li>Notifica a Azure Static Web Apps que um pedido de pull foi fechado.</ol>                                                        |

## <a name="build-and-deploy"></a>Criar e implementar

O passo nomeado `Build and Deploy` constrói e implementa para o seu exemplo de Aplicações Web Estáticas Azure. Na `with` secção, pode personalizar os seguintes valores para a sua implantação.

```yml
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: '/' # App source code path
  api_location: 'api' # Api source code path - optional
  output_location: 'dist' # Built app content directory - optional
  ###### End of Repository/Build Configurations ######
```

[!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

Os `repo_token` `action` valores , e `azure_static_web_apps_api_token` valores são definidos para si por Azure Static Web Apps não devem ser alterados manualmente.

## <a name="custom-build-commands"></a>Comandos de construção personalizados

Pode ter um controlo fino sobre o que os comandos funcionam durante uma implantação. Os seguintes comandos podem ser definidos sob a secção de um `with` trabalho.

A implantação chama sempre `npm install` antes de qualquer comando personalizado.

| Comando             | Descrição                                                                                                                                                                                                                                                                                                                                                                                |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `app_build_command` | Define um comando personalizado para executar durante a implementação da aplicação de conteúdo estático.<br><br>Por exemplo, configurar uma construção de produção para uma aplicação angular criar um script npm nomeado `build-prod` para executar e entrar como o comando `ng build --prod` `npm run build-prod` personalizado. Se ficar em branco, o fluxo de trabalho tenta executar os `npm run build` comandos ou `npm run build:azure` comandos. |
| `api_build_command` | Define um comando personalizado para executar durante a implementação da aplicação API de Funções Azure.                                                                                                                                                                                                                                                                                                  |

## <a name="skip-app-build"></a>Saltar construção de aplicativos

Se precisar de controlo total sobre a forma como a sua aplicação frontal é construída, pode adicionar passos de construção personalizados no seu fluxo de trabalho. Em seguida, pode configurar a ação Static Web Apps para contornar o processo de construção automática e apenas implementar a app que foi construída num passo anterior.

Para saltar a construção da app, desaguise `skip_app_build` para e para a `true` `app_location` localização da pasta a implementar.

```yml
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: 'dist' # Application build output generated by a previous step
  api_location: 'api' # Api source code path - optional
  output_location: '' # Leave this empty
  skip_app_build: true
  ###### End of Repository/Build Configurations ######
```

| Propriedade         | Descrição                                                 |
| ---------------- | ----------------------------------------------------------- |
| `skip_app_build` | Desaprova o valor `true` para saltar a construção da aplicação frontal. |

> [!NOTE]
> Só pode saltar a construção para a aplicação frontal. Se a sua aplicação tiver uma API, ainda será construída pela Static Web Apps GitHub Action.

## <a name="route-file-location"></a>Localização do ficheiro de rota

Pode personalizar o fluxo de trabalho para procurar o [routes.jsem](routes.md) qualquer pasta do seu repositório. A seguinte propriedade pode ser definida sob a secção de um `with` trabalho.

| Propriedade          | Descrição                                                                                                                                 |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| `routes_location` | Define o local do diretório onde se encontra a _routes.jsno_ ficheiro. Esta localização é relativa à raiz do repositório. |

Ser explícito sobre a localização do seu _routes.jsem_ ficheiro é particularmente importante se o seu passo de construção de quadro frontal não mover este ficheiro para o `output_location` por padrão.

> [!IMPORTANT]
> A funcionalidade definida no _routes.jsem_ ficheiro é agora depreciada. Consulte o ficheiro de [configuração](./configuration.md) de Aplicações Web Estáticas Azure para obter informações sobre _staticwebapp.config.jsem_.

## <a name="environment-variables"></a>Variáveis de ambiente

Pode definir variáveis ambientais para a sua construção através `env` da secção da configuração de um trabalho.

```yaml
jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }}
          action: 'upload'
          ###### Repository/Build Configurations
          app_location: '/'
          api_location: 'api'
          output_location: 'public'
          ###### End of Repository/Build Configurations ######
        env: # Add environment variables here
          HUGO_VERSION: 0.58.0
```

## <a name="monorepo-support"></a>Suporte monorepo

Um monorepo é um repositório que contém código para mais de uma aplicação. Por predefinição, um ficheiro de fluxo de trabalho estático web apps rastreia todos os ficheiros num repositório, mas pode ajustá-lo para direcionar uma única aplicação. Portanto, para os monorepos, cada aplicação estática tem o seu próprio ficheiro de configuração que vive lado a lado na pasta _.github/workflows_ do repositório.

```files
├── .github
│   └── workflows
│       ├── azure-static-web-apps-purple-pond.yml
│       └── azure-static-web-apps-yellow-shoe.yml
│
├── app1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── app2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
├── api1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── api2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
└── README.md
```

Para direcionar um ficheiro de fluxo de trabalho para uma única aplicação, especifica caminhos nas `push` secções e `pull_request` secções.

O exemplo a seguir demonstra como adicionar um `paths` nó às `push` secções e `pull_request` secções de um ficheiro denominado _azure-static-web-apps-purple-pond.yml_.

```yml
on:
  push:
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
```

Neste caso, apenas as alterações feitas a seguir aos ficheiros desencadeiam uma nova construção:

- Quaisquer ficheiros dentro da pasta _da app1_
- Quaisquer ficheiros dentro da pasta _api1_
- Alterações no ficheiro de fluxo de trabalho _azure-static-web-apps-purple-pond.yml_

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Reveja os pedidos pull em ambientes de pré-produção](review-publish-pull-requests.md)
