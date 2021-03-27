---
title: 'Tutorial: Publicar Azure Static Web Apps com Azure DevOps'
description: Aprenda a usar Azure DevOps para publicar Azure Static Web Apps.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: 701f999427d743c18f5dbcadb00cf303f97a8f53
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627336"
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

1. **Selecione Import** para começar a importar um pedido de amostra.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="DevOps Repo":::

1. In **Clone URL**, insira `https://github.com/staticwebdev/vanilla-api.git` .

1. Selecione **Import** (Importar).

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

1. Assim que a implementação for bem sucedida, navegue para o novo recurso Static Web Apps.

1. **Selecione Gerir o token de implementação**.

1. Copie o **token de implementação** e cole-o num editor de texto para utilização noutro ecrã.

    > [!NOTE]
    > Este valor é reservado por enquanto porque vai copiar e colar mais valores nos próximos passos.

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Ficha de implantação":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>Criar a Tarefa pipeline em Azure DevOps

1. Navegue para o repositório Azure DevOps que foi criado anteriormente.

1. Selecione **Configurar a construção**.

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Pipeline de compilação":::

1. No ecrã de configuração do *seu pipeline,* selecione **Starter pipeline**.

    :::image type="content" source="media/publish-devops/configure-pipeline.png" alt-text="Gasoduto de configuração":::

1. Copie e cole o seguinte YAML no seu oleoduto.

    ```yaml
    trigger:
      - main
    
    pool:
      vmImage: ubuntu-latest
    
    steps:
      - task: AzureStaticWebApp@0
        inputs:
          app_location: "/" 
          api_location: "api"
          output_location: ""
        env:
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    > [!NOTE]
    > Se não estiver a utilizar a aplicação da amostra, os valores para `app_location` , e precisa de alterar para corresponder aos `api_location` `output_location` valores da sua aplicação.

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    O `azure_static_web_apps_api_token` valor é auto-gerido e configurado manualmente.

1. Selecione **Variáveis**.

1. Criar uma nova variável.

1. Nomeie a variável **deployment_token** (correspondente ao nome no fluxo de trabalho).

1. Copie o símbolo de implantação que colou anteriormente num editor de texto.

1. Cole na ficha de implantação na caixa _Valor._

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="Ficha variável":::

1. **Selecione Mantenha este valor em segredo.**

1. Selecione **OK**.

1. **Selecione Guardar** para voltar ao seu oleoduto YAML.

1. **Selecione Guardar e correr** para abrir o Save e _executar_ o diálogo.

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="Pipeline":::

1. **Selecione Guardar e correr** para executar o gasoduto.

1. Assim que a implementação for bem sucedida, navegue para a **Visão Geral** das Aplicações Web Estáticas Azure, que inclui links para a configuração de implementação. Note como a ligação _Source_ agora aponta para o ramo e localização do repositório Azure DevOps.

1. Selecione o **URL** para ver o seu site recém-implementado.

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="Localização de implantação":::

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configurar aplicativos web estáticos Azure](./configuration.md)
