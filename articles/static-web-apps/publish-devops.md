---
title: 'Tutorial: Publicar Azure Static Web Apps com Azure DevOps'
description: Aprenda a usar Azure DevOps para publicar Azure Static Web Apps.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: af359734ff5bfe90dedbb7f8389aecdc6e056654
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543568"
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

1. Utilize um repositório existente ou _importe um repositório,_ como indicado abaixo.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="DevOps Repo":::

1. Crie um novo ficheiro para a sua aplicação web frontal.

1. Copie e cole a seguinte marcação HTML no seu novo ficheiro:

    ```html
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

1. Guarde o ficheiro.

## <a name="create-a-static-web-app"></a>Criar uma aplicação Web estática

1. Navegue até ao [portal Azure.](https://portal.azure.com)

1. Selecione **Criar um Recurso**.

1. Procure por **Aplicações Web Estáticas.**

1. Selecione **Aplicativos Web Estáticos (Pré-visualização)**.

1. Selecione **Criar**.

1. Nos _detalhes da Implementação,_ certifique-se de que seleciona **Outros**. Isto permite-lhe utilizar o código no seu repositório Azure DevOps.

    > [!NOTE]
    > A funcionalidade para selecionar _Other_ está neste momento a ser lançada e pode ainda não estar disponível em todas as subscrições do Azure.

    :::image type="content" source="media/publish-devops/create-resource.png" alt-text="Detalhes da implementação - outros":::

1. Uma vez que a implementação seja bem sucedida, **selecione Gerir o token de implementação**.

1. Copie o **token de implementação** e cole-o num editor de texto para utilização noutro ecrã.

    > [!NOTE]
    > Este valor é reservado por enquanto porque vai copiar e colar mais valores nos próximos passos.

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Ficha de implantação":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>Criar a Tarefa pipeline em Azure DevOps

1. Navegue para o projeto Azure DevOps que foi criado anteriormente.

2. Crie um novo **Pipeline build** e selecione **Configurar construir.**

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Pipeline de compilação":::

3. Copie e cole o seguinte YAML no seu oleoduto.

    > [!NOTE]
    > Os valores introduzidos _para app_location_,_api_location_, e _output_location_ terão de ser modificados para a sua aplicação.  

    ```yaml
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

    Configure as entradas da App Web Estática Azure de acordo com a estrutura da pasta da sua aplicação.

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

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
