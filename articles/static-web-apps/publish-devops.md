---
title: 'Tutorial: Publicar Azure Static Web Apps com Azure DevOps'
description: Aprenda a usar Azure DevOps para publicar Azure Static Web Apps.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: 4745cf9d60b58e01beb29a640b1282265c23b13c
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105051127"
---
# <a name="tutorial-publish-azure-static-web-apps-with-azure-devops"></a>Tutorial: Publicar Azure Static Web Apps com Azure DevOps

Este artigo demonstra como implementar para [Azure Static Web Apps](./overview.md) usando [Azure DevOps](https://dev.azure.com/).

Neste tutorial, irá aprender a:

- Crie um site de aplicações web estáticas Azure
- Criar um Pipeline Azure DevOps para construir e publicar uma aplicação web estática 

## <a name="prerequisites"></a>Pré-requisitos

- **Conta Ative Azure:** Se não tiver uma, pode [criar uma conta gratuitamente.](https://azure.microsoft.com/free/)
- **Projeto Azure DevOps:** Se não tiver um, pode [criar um projeto de graça.](https://azure.microsoft.com/pricing/details/devops/azure-devops-services/)
- **Gasoduto Azure DevOps:** Se precisar de ajuda para começar, consulte [criar o seu primeiro oleoduto.](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline?view=azure-devops&preserve-view=true)


## <a name="create-a-static-web-app-in-an-azure-devops-repository"></a>Crie uma aplicação web estática num repositório Azure DevOps
  > [!NOTE]
  > Se tiver uma aplicação existente no seu repositório, poderá saltar para a secção seguinte.

1. Navegue para o seu repositório Azure DevOps.
   
2. Utilize um repositório existente ou _importe um repositório,_ como indicado abaixo.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="DevOps Repo":::

3. Crie um novo ficheiro para a sua aplicação web frontal.
   
4. Copie e cole a seguinte marcação HTML no seu novo ficheiro:

  ```HTML
  <!DOCTYPE html>
  <html lang="en">

  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="styles.css">
    <title>Hello World!</title>
  </head>

  <body>
    <main>
      <h1>Hello World!</h1>
    </main>
  </body>

  </html>
  ```

5. Guarde o ficheiro.

## <a name="create-a-static-web-app"></a>Criar uma aplicação Web estática

1. Navegue até ao [portal Azure.](https://portal.azure.com)
   
2. Selecione **Criar um Recurso**.
   
3. Procure por **Aplicações Web Estáticas.**
   
4. Selecione **Aplicativos Web Estáticos (Pré-visualização)**.
   
5. Selecione **Criar**.
   
6. Nos _detalhes da Implementação,_ certifique-se de que seleciona **Outros**. Isto permite-lhe utilizar o código no seu repositório Azure DevOps.
  > [!NOTE]
  > A funcionalidade para selecionar _outras_ encontra-se atualmente a ser lançada e pode ainda não estar disponível em todas as subscrições do Azure.

  :::image type="content" source="media/publish-devops/create-resource.png" alt-text="Detalhes da implementação - outros":::

7. Uma vez que a implementação seja bem sucedida, **selecione Gerir o token de implementação**.

8. Copie o **token de implementação** e cole-o num editor de texto para utilização noutro ecrã.

  > [!NOTE]
  > Este valor é reservado por enquanto porque vai copiar e colar mais valores nos próximos passos.

  :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Ficha de implantação"::: 

## <a name="create-the-pipeline-task-in-azure-devops"></a>Criar a Tarefa pipeline em Azure DevOps

1. Navegue para o projeto Azure DevOps que foi criado anteriormente.

2. Crie um novo **Pipeline build** e selecione **Configurar construir.**

  :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Pipeline de compilação"::: 

3. Copie e cole o seguinte YAML no seu oleoduto.
> [!NOTE]
> Os valores introduzidos _para app_location_, _api_location_, e _output_location_ terão de ser modificados para a sua aplicação.  

```YAML
trigger:
  - main

pool:
  vmImage: ubuntu-latest

steps:
  - task: AzureStaticWebApp@0
    inputs:
      app_location: frontend 
      api_location: api
      output_location: build
    env:
      azure_static_web_apps_api_token: $(deployment_token)
   ```
Configure as entradas da _App Web Estática Azure_ de acordo com a estrutura da pasta da sua aplicação.

| Propriedade | Descrição | Obrigatório |
|---|---|---|
| `app_location` | Localização do seu código de inscrição.<br><br>Por exemplo, insira `/` se o código fonte da sua aplicação estiver na raiz do repositório, ou se o seu código de `/app` aplicação estiver num diretório chamado `app` . | Yes |
| `api_location` | Localização do seu código Azure Functions.<br><br>Por exemplo, insira `/api` se o seu código de aplicação estiver numa pasta chamada `api` . Se nenhuma aplicação Azure Functions for detetada na pasta, a construção não falha, o fluxo de trabalho assume que não quer uma API. | No |
| `output_location` | Localização do diretório de saída de construção relativamente ao `app_location` .<br><br>Por exemplo, se o código fonte da sua aplicação estiver localizado em `/app` , e o script de construção descosões de saídas para a `/app/build` pasta, então definido `build` como o `output_location` valor. | No |

O `azure_static_web_apps_api_token` valor é auto-gerido e configurado manualmente.

4. Selecione **Variáveis**.

5. Criar uma nova variável.

6. Nomeie a variável **deployment_token** (correspondente ao nome no fluxo de trabalho).

7. Copie o símbolo de implantação que colou anteriormente num editor de texto.

8. Cole na ficha de implantação na caixa _Valor._

  :::image type="content" source="media/publish-devops/variable-token.png" alt-text="Ficha variável":::

9. Selecione **OK**.

10. **Selecione Guardar e executar** o gasoduto.

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="Pipeline":::
   
11. Assim que a implementação for bem sucedida, navegue para a **Visão Geral** das Aplicações Web Estáticas Azure, que inclui links para a configuração de implementação.

12. Selecione o **URL** para ver o seu site recém-implementado. Note como a ligação _Source_ agora aponta para o ramo e localização do repositório Azure DevOps.
   
    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="Localização de implantação":::


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configurar aplicativos web estáticos Azure](./configuration.md)
