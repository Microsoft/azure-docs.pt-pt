---
title: Utilizar o DevTest Labs nos pipelines de compilação e versão dos Pipelines do Azure
description: Aprenda a usar a Azure DevTest Labs em Azure Pipelines constroem e libertam oleodutos.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d04ed5dd7bebac0c8f24deb9145c3d2e4b77122e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88080339"
---
# <a name="use-devtest-labs-in-azure-pipelines-build-and-release-pipelines"></a>Utilizar o DevTest Labs nos pipelines de compilação e versão dos Pipelines do Azure
Este artigo fornece informações sobre como a DevTest Labs pode ser usada em gasodutos Azure. 

## <a name="overall-flow"></a>Fluxo global
O fluxo básico é ter um **gasoduto de construção** que faça as seguintes tarefas:

1. Construa o código de aplicação.
1. Crie o ambiente base nos Laboratórios DevTest.
1. Atualize o ambiente com informações personalizadas.
1. Implementar a aplicação para o ambiente DevTest Labs
1. Teste o código. 

Uma vez concluída a construção com sucesso, o **gasoduto de desbloqueio** utilizará os artefactos de construção para implantar a encenação ou a produção. 

Uma das premissas necessárias é que toda a informação necessária para recriar o ecossistema testado está disponível dentro dos artefactos de construção, incluindo a configuração dos recursos Azure. À medida que os recursos da Azure incorrem num custo quando utilizados, as empresas querem controlar ou acompanhar a utilização destes recursos. Em algumas situações, os modelos do Azure Resource Manager que são usados para criar e configurar os recursos podem ser geridos por outro departamento como o TI. E estes modelos podem ser armazenados num repositório diferente. Conduz a uma situação interessante em que uma construção será criada e testada, e tanto o código como a configuração terão de ser armazenados dentro dos artefactos de construção para recriar corretamente o sistema em produção. 

Utilizando os Laboratórios DevTest durante a fase de construção/teste, pode adicionar modelos do Azure Resource Manager e ficheiros de suporte às fontes de construção, de modo a que durante a fase de libertação a configuração exata utilizada para testar seja implantada na produção. A tarefa **Create Azure DevTest Labs Environment** com a configuração adequada salvará os modelos do Gestor de Recursos dentro dos artefactos de construção. Para este exemplo, você estará usando o código a partir do [Tutorial: Construa uma aplicação web .NET Core e SQL Database no Azure App Service](../app-service/tutorial-dotnetcore-sqldb-app.md), para implementar e testar a aplicação web em Azure.

![Fluxo global](./media/use-devtest-labs-build-release-pipelines/overall-flow.png)

## <a name="set-up-azure-resources"></a>Configurar recursos do Azure
Há um par de itens que precisam ser criados previamente:

- Dois repositórios. O primeiro com o código do tutorial e um modelo de Gestor de Recursos com dois VMs adicionais. O segundo conterá o modelo base Azure Resource Manager (configuração existente).
- Um Grupo de Recursos para a implantação do código de produção e configuração.
- Um laboratório precisa de ser criado com uma [ligação ao repositório de configuração](devtest-lab-create-environment-from-arm.md) para o oleoduto de construção. O modelo de Gestor de Recursos precisa de ser verificado no repositório de configuração à medida que azuredeploy.jscom o metadata.jspara permitir que a DevTest Labs reconheça e implemente o modelo.

O oleoduto de construção criará um ambiente DevTest Labs e implementará o código para testes.

## <a name="set-up-a-build-pipeline"></a>Criar um oleoduto de construção
Em Azure Pipelines, crie um pipeline de construção utilizando o código a partir do [Tutorial: Construa uma aplicação web .NET Core e SQL Database no Azure App Service](../app-service/tutorial-dotnetcore-sqldb-app.md). Utilize o modelo **core ASP.NET,** que preencherá a tarefa necessária para construir, testar e publicar o código.

![Selecione o modelo de ASP.NET](./media/use-devtest-labs-build-release-pipelines/select-asp-net.png)

Precisa adicionar três tarefas adicionais para criar o ambiente nos Laboratórios DevTest e implementar para o ambiente.

![Pipeline com três tarefas](./media/use-devtest-labs-build-release-pipelines/pipeline-tasks.png)

### <a name="create-environment-task"></a>Criar tarefa ambiental
Na tarefa de criar ambiente **(Azure DevTest Labs Create Environment** task) utilize as listas de drop-down para selecionar os seguintes valores:

- Subscrição do Azure
- nome do laboratório
- nome do repositório
- nome do modelo (que mostra a pasta onde o ambiente é armazenado). 

Recomendamos que utilize listas de drop-down na página em vez de introduzir a informação manualmente. Se introduzir manualmente as informações, insira ids de recursos Azure totalmente qualificados. A tarefa apresenta os nomes amigáveis em vez de identificações de recursos. 

O nome ambiente é o nome apresentado mostrado dentro da DevTest Labs. Deve ser um nome único para cada construção. Por exemplo: **TestEnv$(Build.BuildId)**. 

Pode especificar o ficheiro de parâmetros ou parâmetros para passar informações no modelo de Gestor de Recursos. 

Selecione as **variáveis de saída Criar com base na opção de saída do modelo de ambiente** e introduza um nome de referência. Para este exemplo, insira **o BaseEnv** para obter o nome de referência. Utilizará este BaseEnv ao configurar a próxima tarefa. 

![Criar tarefa ambiente Azure DevTest Labs](./media/use-devtest-labs-build-release-pipelines/create-environment.png)

### <a name="populate-environment-task"></a>Tarefa do ambiente de povoar
A segunda tarefa **(Azure DevTest Labs Populate Environment)** é atualizar o ambiente existente da DevTest Labs. A tarefa de criar saídas de tarefas **baseEnv.environmentResourceId** que é usada para configurar o nome do ambiente para esta tarefa. O modelo de Gestor de Recursos para este exemplo tem dois parâmetros - **adminUserName** e **adminPassword**. 

![Tarefa de Ambiente DevTest Labs de Populate Azure](./media/use-devtest-labs-build-release-pipelines/populate-environment.png)

## <a name="app-service-deploy-task"></a>Tarefa de implementação do Serviço de Aplicações
A terceira tarefa é a tarefa de implementação do **Serviço de Aplicações Azure.** O tipo de aplicação está definido para **Web App** e o nome do Serviço de Aplicações está definido para **$(WebSite)**.

![Tarefa de implementação do serviço de aplicações](./media/use-devtest-labs-build-release-pipelines/app-service-deploy.png)

## <a name="set-up-release-pipeline"></a>Configurar o gasoduto de libertação
Cria um pipeline de desbloqueio com duas tarefas: **Implementação Azure: Criar ou atualizar o Grupo de Recursos** e implementar o Serviço de **Aplicações Azure**. 

Para a primeira tarefa, especifique o nome e a localização do grupo de recursos. A localização do modelo é um artefacto ligado. Se o modelo do Gestor de Recursos incluir modelos ligados, uma implementação de grupo de recursos personalizados tem de ser implementada. O modelo está no artefacto da gota publicado. Substitua os parâmetros do modelo para o modelo de Gestor de Recursos. Pode deixar as definições restantes com valores predefinidos. 

Para a segunda tarefa Implementar o **Serviço de Aplicações Azure**, especifique a subscrição do Azure, selecione **Web App** para o **tipo app**, e **$(WebSite)** para o **nome do Serviço de Aplicações**. Pode deixar as definições restantes com valores predefinidos. 

## <a name="test-run"></a>Teste de Corrida
Agora que ambos os oleodutos estão montados, faça fila manualmente para uma construção e veja-a funcionar. O próximo passo é definir o gatilho apropriado para a construção e ligar a construção ao gasoduto de libertação.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Integre a Azure DevTest Labs no seu pipelineS Azure e gasoduto de entrega contínua](devtest-lab-integrate-ci-cd.md)
- [Integrar ambientes nos seus gasodutos Azure Pipelines CI/CD](integrate-environments-devops-pipeline.md)
