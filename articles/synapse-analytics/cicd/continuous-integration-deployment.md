---
title: Integração contínua e entrega para espaço de trabalho Synapse
description: Aprenda a usar a integração contínua e a entrega para implementar mudanças no espaço de trabalho de um ambiente (desenvolvimento, teste, produção) para outro.
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 5dbd49312b58dc656e2239e8a0a4acea614023de
ms.sourcegitcommit: e5f9126c1b04ffe55a2e0eb04b043e2c9e895e48
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96317141"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Integração contínua e entrega para o espaço de trabalho Azure Synapse

## <a name="overview"></a>Descrição geral

Integração Contínua (CI) é o processo de automatização da construção e teste do código sempre que um membro da equipa comete alterações ao controlo da versão. A Implementação Contínua (CD) é o processo de construção, teste, configuração e implantação de múltiplos ambientes de teste ou de encenação para um ambiente de produção.

Para o espaço de trabalho Azure Synapse, a integração contínua e a entrega (CI/CD) movem todas as entidades de um ambiente (desenvolvimento, teste, produção) para outro. Para promover o seu espaço de trabalho para outro espaço de trabalho, existem duas partes: utilize [modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview) para criar ou atualizar recursos do espaço de trabalho (piscinas e espaço de trabalho); migrar artefactos (scripts SQL, caderno, definição de trabalho de faísca, oleodutos, conjuntos de dados, fluxos de dados, e assim por diante) com ferramentas Synapse CI/CD em Azure DevOps. 

Este artigo irá delinear usando o gasoduto de libertação Azure para automatizar a implantação de um espaço de trabalho synapse para vários ambientes.

## <a name="prerequisites"></a>Pré-requisitos

-   O espaço de trabalho utilizado para o desenvolvimento foi configurado com um repositório git em Studio, ver [controlo de Origem no Synapse Studio](source-control.md).
-   Um projeto Azure DevOps foi preparado para executar o oleoduto de lançamento.

## <a name="set-up-a-release-pipelines"></a>Criar um oleoduto de libertação

1.  Em [Azure DevOps,](https://dev.azure.com/)abra o projeto criado para o lançamento.

1.  No lado esquerdo da página, selecione **Pipelines** e, em seguida, selecione **Versões**.

    ![Selecione Pipelines, Lançamentos](media/create-release-1.png)

1.  Selecione **Novo oleoduto**, ou, se tiver os oleodutos existentes, selecione **Novo** e, em seguida, Novo oleoduto **de lançamento**.

1.  Selecione o modelo **de trabalho vazio.**

    ![Selecione trabalho vazio](media/create-release-select-empty.png)

1.  Na **caixa de nomes** de palco, insira o nome do seu ambiente.

1.  **Selecione Adicionar artefacto** e, em seguida, selecione o repositório de git configurado com o seu desenvolvimento Synapse Studio. Selecione o repositório de git que usou para gerir o modelo ARM de piscinas e espaço de trabalho. Se utilizar o GitHub como fonte, tem de criar uma ligação de serviço para a sua conta GitHub e retirar repositórios. Para mais informações sobre [a ligação de serviço](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints) 

    ![Adicionar ramo de publicação](media/release-creation-github.png)

1.  Selecione o ramo do modelo ARM para atualização de recursos. Para a **versão Predefinitiva**, selecione **mais recente do ramo predefinido**.

    ![Adicionar modelo ARM](media/release-creation-arm-branch.png)

1.  Selecione o ramo de [publicação](source-control.md#configure-publishing-settings) do repositório para o **ramo Predefinido**. Por defeito, esta sucursal de publicação é `workspace_publish` . Para a **versão Predefinitiva**, selecione **mais recente do ramo predefinido**.

    ![Adicionar um artefacto](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-arm-resource-create-and-update"></a>Configurar uma tarefa de fase para criar e atualizar recursos ARM 

Adicione uma tarefa de implementação do Gestor de Recursos Azure para criar ou atualizar recursos, incluindo espaço de trabalho e piscinas:

1. Na vista do palco, **selecione Ver as tarefas do palco**.

    ![Vista de palco](media/release-creation-stage-view.png)

1. Criar uma nova tarefa. Procure a **implementação do modelo ARM** e, em seguida, selecione **Adicionar**.

1. Na tarefa De implementação, selecione a subscrição, o grupo de recursos e a localização para o espaço de trabalho alvo. Fornecer credenciais, se necessário.

1. Na lista **de Ação,** selecione **Criar ou atualizar o grupo de recursos**.

1. Selecione o botão elipse **(...**) ao lado da caixa **Modelo.** Navegue para o modelo do Gestor de Recursos Azure do seu espaço de trabalho alvo

1. Selecione **...** ao lado da caixa **de parâmetros do modelo** para escolher o ficheiro de parâmetros.

1. Selecione **...** ao lado da caixa de **parâmetros do modelo de substituição** e introduza os valores de parâmetros desejados para o espaço de trabalho alvo. 

1. Selecione **Incremental** para o **modo de implementação**.
    
    ![espaço de trabalho e piscinas implantam](media/pools-resource-deploy.png)

1. (Opcional) Adicione **a Azure PowerShell** para a atribuição de funções de espaço de trabalho de concessão e atualização. Se utilizar o gasoduto de desbloqueio para criar um espaço de trabalho synapse, o principal de serviço do oleoduto é adicionado como administrador de espaço de trabalho predefinido. Pode executar o PowerShell para conceder a outras contas acesso ao espaço de trabalho. 
    
    ![conceder permissão](media/release-creation-grant-permission.png)

 > [!WARNING]
> No modo de implementação completo, os recursos que existem no grupo de recursos mas que não estão especificados no novo modelo de Gestor de Recursos serão **eliminados**. Para mais informações, consulte os [modos de implementação do Gestor de Recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes)

## <a name="set-up-a-stage-task-for-artifacts-deployment"></a>Configurar uma tarefa de palco para a implantação de artefactos 

Utilize a extensão [de implantação do espaço de trabalho synapse](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy) para implantar outros itens no espaço de trabalho synapse, como conjunto de dados, script SQL, caderno, definição de trabalho de faísca, fluxo de dados, serviço ligado, serviço ligado e IR (Integration Runtime).  

1. Pesquisa e obtenha a extensão do **mercado Azure DevOps**(https://marketplace.visualstudio.com/azuredevops) 

     ![Obter extensão](media/get-extension-from-market.png)

1. Selecione uma organização para instalar extensão. 

     ![Instalar a extensão](media/install-extension.png)

1. Certifique-se de que o princípio de serviço do gasoduto Azure DevOps foi autorizado a ser atribuído à subscrição e também atribuído como administrador do espaço de trabalho para o espaço de trabalho alvo. 

1. Criar uma nova tarefa. Procure a **implementação do espaço de trabalho synapse** e, em seguida, selecione **Adicionar**.

     ![Adicionar extensão](media/add-extension-task.png)

1.  Na tarefa, forneça informações relacionadas com o repositório de **git de workspace_publish** , e selecione o grupo de recursos, região, nome e ambiente cloud para o espaço de trabalho alvo. Forneça parâmetros e valores se precisar.

    ![Implantação do espaço de trabalho da Sinapse](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> Nos cenários ci/CD, o tipo de execução de integração (IR) em diferentes ambientes deve ser o mesmo. Por exemplo, se tiver um IR auto-hospedado no ambiente de desenvolvimento, o mesmo IR também deve ser do tipo auto-hospedado em outros ambientes, como o teste e a produção. Da mesma forma, se estiver a partilhar tempos de integração em várias fases, tem de configurar os tempos de integração como auto-hospedados em todos os ambientes, tais como desenvolvimento, teste e produção.

## <a name="create-release-for-deployment"></a>Criar lançamento para implantação 

Depois de guardar todas as alterações, pode selecionar **Criar desbloqueio** para criar manualmente um desbloqueio. Para automatizar a criação de lançamentos, veja [os gatilhos de lançamento do Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/release/triggers)

   ![Selecione Criar ver lançamento](media/release-creation-manually.png)

## <a name="best-practices-for-cicd"></a>Melhores práticas para CI/CD

Se estiver a utilizar a integração do Git com o seu espaço de trabalho synapse e tiver um pipeline CI/CD que move as suas alterações do desenvolvimento para teste e depois para a produção, recomendamos estas boas práticas:

-   **Integração de Git**. Configure apenas o seu espaço de trabalho sinapse de desenvolvimento com integração git. As alterações aos espaços de trabalho de teste e produção são implementadas através de CI/CD e não necessitam de integração do Git.
-   **Prepare piscinas antes da migração de artefactos.** Se você tem script SQL ou caderno anexado a piscinas no espaço de trabalho de desenvolvimento, o mesmo nome de piscinas em diferentes ambientes são esperados. 
-   **Infraestrutura como Código (IAC)**. Gestão de infraestruturas (redes, máquinas virtuais, equilibradores de carga e topologia de ligação) num modelo descritivo, utilize a mesma versão que a equipa de DevOps utiliza para código fonte. 
-   **Outros.** Ver [as melhores práticas para artefactos ADF](/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)


