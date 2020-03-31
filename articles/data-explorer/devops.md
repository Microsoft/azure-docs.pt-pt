---
title: Tarefa de Azure DevOps para O Explorador de Dados Azure
description: Neste tópico, aprende-se a criar um oleoduto de libertação e a implantar
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 1e44a7e71858f028b798720c5505eacbfe8c2332
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472048"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Tarefa Azure DevOps para o Explorador de Dados Azure

A [Azure DevOps Services](https://azure.microsoft.com/services/devops/) fornece ferramentas de colaboração de desenvolvimento, tais como oleodutos de alto desempenho, repositórios privados gratuitos git, placas Kanban configuráveis e extensas capacidades de teste automatizados e contínuos. [O Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) é uma capacidade Azure DevOps que lhe permite gerir o CI/CD para implementar o seu código com gasodutos de alto desempenho que funcionam com qualquer idioma, plataforma e nuvem.
[Azure Data Explorer - Admin Commands](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) é a tarefa azure Pipelines que lhe permite criar gasodutos de lançamento e implementar as alterações da sua base de dados nas bases de dados do Azure Data Explorer. Está disponível gratuitamente no [Visual Studio Marketplace.](https://marketplace.visualstudio.com/)

Este documento descreve um exemplo simples sobre a utilização da tarefa do **Azure Data Explorer – Admin Commands** para implementar as alterações do seu esquema na sua base de dados. Para obter os gasodutos CI/CD completos, consulte a [documentação azure DevOps](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts).

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* Configuração do Cluster Azure Data Explorer:
    * Um [cluster azure data explorer e base de dados](/azure/data-explorer/create-cluster-database-portal)
    * Create Azure Ative Directory (Azure AD) [aplica-se ao fornecimento de uma aplicação Azure AD](/azure/kusto/management/access-control/how-to-provision-aad-app).
    * Conceda acesso à sua App AD Azure na sua base de dados do Azure Data [Explorer, gerindo permissões](/azure/data-explorer/manage-database-permissions)na base de dados do Azure Data Explorer.
* Configuração Azure DevOps:
    * [Inscreva-se para uma organização gratuita](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [Criar uma nova organização](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Criar um projeto em Azure DevOps](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Código com Git](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>Criar pastas

Crie as seguintes pastas de amostra *(Funções,* *Políticas,* *Tabelas)* no seu repositório Git. Copie os ficheiros [daqui](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) para as respetivas pastas, conforme visto abaixo e comprometa as alterações. Os ficheiros da amostra são fornecidos para executar o seguinte fluxo de trabalho.

![Criar pastas](media/devops/create-folders.png)

> [!TIP]
> Ao criar o seu próprio fluxo de trabalho, recomendamos que o seu código idempotente. Por exemplo, utilize [a tabela .create-merge](/azure/kusto/management/create-table-command#create-merge-table) em vez de [.create table](/azure/kusto/management/create-table-command), e use [função .create-or-alter](/azure/kusto/management/create-alter-function) em vez de [.criar](/azure/kusto/management/create-function) função.

## <a name="create-a-release-pipeline"></a>Criar um pipeline de versão

1. Inscreva-se na sua [organização Azure DevOps.](https://dev.azure.com/)
1. Selecione **Pipelines** > **Desbloqueiodo** do menu à esquerda e selecione **Novo pipeline**.

    ![Novo pipeline](media/devops/new-pipeline.png)

1. A janela do **novo oleoduto** de lançamento abre. No separador **Pipelines,** no painel **de modeloS Selecione,** selecione **trabalho vazio**.

     ![Selecione um modelo](media/devops/select-template.png)

1. Selecione o botão **Stage.** No painel **de palco,** adicione o **nome do palco**. Selecione **Guardar** para salvar o seu oleoduto.

    ![Nomeie o palco](media/devops/stage-name.png)

1. Selecione Adicionar um botão **de artefacto.** No **painel Adicionar um** painel de artefactos, selecione o repositório onde o seu código existe, preencha informações relevantes e clique em **Adicionar**. Selecione **Guardar** para salvar o seu oleoduto.

    ![Adicionar um artefacto](media/devops/add-artifact.png)

1. No separador **Variáveis,** selecione **+ Adicione** para criar uma variável para **ENDPoint URL** que será usada na tarefa. Escreva o **Nome** e o **Valor** do Ponto Final. Selecione **Guardar** para salvar o seu oleoduto. 

    ![Criar variável](media/devops/create-variable.png)

    Para encontrar o seu Endpoint_URL, a página geral do seu **Cluster de Exploradores** de Dados Azure no portal Azure contém o cluster Azure Data Explorer URI. Construa o URI `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`no seguinte formato .  Por exemplo,\/https: /kustodocs.westus.kusto.windows.net?DatabaseName=SampleDB

    ![Cluster Uri do Explorador de Dados Azure](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>Criar tarefas para implementar

1. No separador **Pipeline,** clique em **1 trabalho, 0 tarefa** para adicionar tarefas. 

    ![Adicionar tarefas](media/devops/add-task.png)

1. Criar três tarefas para implementar **Tabelas, Funções,** e **Políticas,** por esta ordem. **Tables** 

1. No separador **Tarefas,** selecione **+** por **função de Agente**. Procure o **Azure Data Explorer**. No **Marketplace,** instale a extensão **Azure Data Explorer – Admin Commands.** Em seguida, **selecione Adicionar** no Comando de Explorador de **Dados Run Azure**.

     ![Adicionar comandos de administração](media/devops/add-admin-commands.png)

1. Clique no **Comando Kusto** à esquerda e atualize a tarefa com as seguintes informações:
    * **Nome**do display : Nome da tarefa
    * **Caminho de ficheiro**: Na tarefa **tabelas,** especifique */Tabelas/*.csl uma vez que os ficheiros de criação de tabela saem na pasta *Tabela.*
    * **URL do ponto** `EndPoint URL`final : introduza a variável criada em passo anterior.
    * Selecione **Use o ponto final do serviço** e selecione + **Novo**.

    ![Atualizar a tarefa de comando kusto](media/devops/kusto-command-task.png)

1. Complete as seguintes informações na janela de **ligação add Azure Data Explorer:**

    |Definição  |Valor sugerido  |
    |---------|---------|
    |**Nome da ligação**     |    Insira um nome para identificar este ponto final do serviço     |
    |**Cluster Url**    |    O valor pode ser encontrado na secção geral do seu Cluster de Exploradores de Dados Azure no portal Azure | 
    |**Id principal de serviço**    |    Introduza o ID da App AAD (criado como pré-requisito)     |
    |**Chave principal de aplicativo de serviço**     |    Introduza a chave da aplicação AAD (criada como pré-requisito)    |
    |**Id inquilino da AAD**    |      Insira o seu inquilino aAD (como microsoft.com, contoso.com...)    |

    Selecione Permitir que todos os gasodutos utilizem esta caixa de verificação de **ligação.** Selecione **OK**.

    ![Adicionar ligação de serviço](media/devops/add-service-connection.png)

1. Repita os passos 1-5 mais duas vezes para implementar ficheiros das pastas *Funções* e *Políticas.* Selecione **Guardar**. No separador **Tarefas,** consulte as três tarefas criadas: **Deploy Tables,** **Deploy Functions,** e **Deploy Policies**.

    ![Implementar todas as pastas](media/devops/deploy-all-folders.png)

1. Selecione **+ Libertar** > **Criar lançamento** para criar uma libertação.

    ![Criar um lançamento](media/devops/create-release.png)

1. No separador **Logs,** verifique se o estado de implementação foi bem sucedido.

    ![A implantação é bem sucedida](media/devops/deployment-successful.png)

Concluiu agora a criação de um oleoduto de libertação para a implantação de três tarefas de pré-produção.