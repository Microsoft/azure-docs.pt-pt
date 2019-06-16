---
title: Tarefa de DevOps do Azure para o Explorador de dados do Azure
description: Neste tópico, vai aprender a criar um pipeline de lançamento e implementar
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 0628d5c07d7258cc4d68727c364e65bd81c78e8e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388984"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Tarefa de DevOps do Azure para o Explorador de dados do Azure

[Os serviços do Azure DevOps](https://azure.microsoft.com/services/devops/) fornece ferramentas de colaboração como pipelines de alto desempenho, repositórios de Git privados gratuitos, quadros de Kanban configuráveis e extensas capacidades de testes automatizadas e contínuas de desenvolvimento. [Pipelines do Azure](https://azure.microsoft.com/services/devops/pipelines/) é uma capacidade de DevOps do Azure que permite-lhe gerir o CI/CD para implementar o seu código com pipelines de elevado desempenho que funcionam com qualquer linguagem, a plataforma e a cloud.
[Explorador de dados do Azure - os comandos de administrador](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) é a tarefa de Pipelines do Azure permite-lhe criar pipelines de versão e implementar a sua base de dados é alterado para seus bancos de dados do Explorador de dados do Azure. Está disponível gratuitamente no [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

Este documento descreve um exemplo simples sobre o uso do **Explorador de dados do Azure – comandos de administrador** tarefas para implementar o seu esquema é alterado para a base de dados. Para pipelines CI/CD completas, consulte [documentação do Azure DevOps](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts).

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* Configuração de Cluster do Explorador de dados do Azure:
    * Um [clusters do Explorador de dados do Azure e base de dados](/azure/data-explorer/create-cluster-database-portal)
    * Criar aplicação do Azure Active Directory (Azure AD) por [aprovisionamento de uma aplicação do Azure AD](/azure/kusto/management/access-control/how-to-provision-aad-app).
    * Conceder acesso à aplicação do Azure AD na sua base de dados do Explorador de dados do Azure por [gerenciamento de permissões de base de dados do Azure Data Explorer](/azure/data-explorer/manage-database-permissions).
* Configuração de DevOps do Azure:
    * [Inscreva-se uma organização gratuita](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [Criar uma organização](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Criar um projeto no Azure DevOps](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Código com o Git](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>Criar pastas

Criar as seguintes pastas de exemplo (*funções*, *políticas*, *tabelas*) no seu repositório de Git. Copie os ficheiros da [aqui](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) para as respectivas pastas como vista abaixo e confirmar as alterações. Os ficheiros de exemplo são fornecidos para executar o seguinte fluxo de trabalho.

![Criar pastas](media/devops/create-folders.png)

> [!TIP]
> Ao criar seu próprio fluxo de trabalho, recomendamos que faça idempotentes seu código. Por exemplo, usar [tabela de intercalação. Create](/azure/kusto/management/tables#create-merge-tables) em vez de [criar uma tabela](/azure/kusto/management/tables#create-table)e utilizar [. Create ou alter](/azure/kusto/management/functions#create-or-alter-function) funcionar em vez de [. Create](/azure/kusto/management/functions#create-function) função.

## <a name="create-a-release-pipeline"></a>Criar um pipeline de versão

1. Inicie sessão no seu [organização do Azure DevOps](https://dev.azure.com/).
1. Selecione **Pipelines** > **versões** no menu do lado esquerdo e selecione **novo pipeline**.

    ![Novo pipeline](media/devops/new-pipeline.png)

1. O **novo pipeline de versões** é aberta a janela. Na **Pipelines** separador a **selecionar um modelo** painel, selecione **tarefa vazia**.

     ![Selecione um modelo](media/devops/select-template.png)

1. Selecione **estágio** botão. Na **estágio** painel, adicione o **nome da fase**. Selecione **guardar** para guardar o seu pipeline.

    ![Nome da fase](media/devops/stage-name.png)

1. Selecione **adicionar um artefato** botão. Na **adicionar um artefato** painel, selecione o repositório onde existe o seu código, preencha as informações relevantes e clique em **Add**. Selecione **guardar** para guardar o seu pipeline.

    ![Adicione um artefacto](media/devops/add-artifact.png)

1. Na **variáveis** separador, selecione **+ adicionar** para criar uma variável para **URL de ponto final** que será utilizado na tarefa. Escrever a **Name** e o **valor** do ponto de extremidade. Selecione **guardar** para guardar o seu pipeline. 

    ![Criar variável](media/devops/create-variable.png)

    Para localizar seu Endpoint_URL, a página de descrição geral do seu **Cluster de Explorador de dados do Azure** no Azure portal contém o URI de cluster do Explorador de dados do Azure. Construir o URI no seguinte formato `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`.  Por exemplo, https:\//kustodocs.westus.kusto.windows.net?DatabaseName=SampleDB

    ![URI de cluster de Explorador de dados do Azure](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>Criar tarefas para implementar

1. Na **Pipeline** separador, clique em **1 tarefa, tarefa 0** adicionar tarefas. 

    ![Adicionar tarefas](media/devops/add-task.png)

1. Criar três tarefas para implementar **tabelas**, **funções**, e **políticas**, nessa ordem. 

1. Na **tarefas** separador, selecione **+** por **tarefa de agente**. Procure o **Azure Data Explorer**. Na **Marketplace**, instale o **Explorador de dados do Azure – comandos de administrador** extensão. Em seguida, selecione **Add** na **executar comando de Explorador de dados do Azure**.

     ![Adicionar comandos de administrador](media/devops/add-admin-commands.png)

1. Clique em **Kusto comando** à esquerda e atualizar a tarefa com as seguintes informações:
    * **Nome a apresentar**: Nome da tarefa
    * **Caminho do ficheiro**: Na **tabelas** de tarefas, especificar */Tables/* .csl, uma vez que os ficheiros de criação de tabela estão no *tabela* pasta.
    * **URL de ponto final**: introduza o `EndPoint URL`variável criada no passo anterior.
    * Selecione **ponto final de serviço de utilização** e selecione **+ novo**.

    ![Tarefa de atualização de comando de Kusto](media/devops/kusto-command-task.png)

1. Conclua as seguintes informações na **ligação de serviço de adicionar o Azure Data Explorer** janela:

    |Definição  |Valor sugerido  |
    |---------|---------|
    |**Connection name** (Nome da ligação)     |    Introduza um nome para identificar este ponto final de serviço     |
    |**Url do cluster**    |    Valor pode ser encontrado na secção Descrição geral do seu Cluster do Explorador de dados do Azure no portal do Azure | 
    |**Id de Principal de serviço**    |    Introduza o ID de aplicação do AAD (criado como pré-requisito)     |
    |**Chave da aplicação Principal de serviço**     |    Introduza a chave de aplicação do AAD (criado como pré-requisito)    |
    |**Id de inquilino do AAD**    |      Introduza o seu inquilino do AAD (por exemplo, o microsoft.com, contoso.com...)    |

    Selecione **permitir que todos os pipelines utilizar esta ligação** caixa de verificação. Selecione **OK**.

    ![Adicionar ligação de serviço](media/devops/add-service-connection.png)

1. Repita os passos 1 a 5 outro duas vezes para implementar os ficheiros a partir da *funções* e *políticas* pastas. Selecione **Guardar**. Na **tarefas** separador, consulte as três tarefas que criou: **Implementar tabelas**, **implementar as funções**, e **implementar políticas**.

    ![Implementar todas as pastas](media/devops/deploy-all-folders.png)

1. Selecione **+ versão** > **criar versão** para criar uma versão.

    ![Criar uma versão](media/devops/create-release.png)

1. Na **registos** separador, verifique o estado de implementação é efetuada com êxito.

    ![A implantação é concluída com êxito](media/devops/deployment-successful.png)

Já concluiu a criação de um pipeline de lançamento para a implantação de três tarefas para pré-produção.