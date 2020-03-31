---
title: Utilizar o DevTest Labs nos pipelines de compilação e versão dos Pipelines do Azure
description: Aprenda a utilizar os Azure DevTest Labs em Pipelines Azure construir e lançar oleodutos.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: e16f3c5a0c0b2b86d6a893f541cefb275a8e7d07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169228"
---
# <a name="use-devtest-labs-in-azure-pipelines-build-and-release-pipelines"></a>Utilizar o DevTest Labs nos pipelines de compilação e versão dos Pipelines do Azure
Este artigo fornece informações sobre como os Laboratórios DevTest podem ser usados em oleodutos de construção e lançamento de oleodutos da Azure Pipelines. 

## <a name="overall-flow"></a>Fluxo global
O fluxo básico é ter um pipeline de **construção** que faça as seguintes tarefas:

1. Construa o código de aplicação.
1. Crie o ambiente base em DevTest Labs.
1. Atualize o ambiente com informações personalizadas.
1. Implementar a aplicação para o ambiente DevTest Labs
1. Teste o código. 

Uma vez concluída a construção com sucesso, o gasoduto de **libertação** utilizará os artefactos de construção para implantar encenações ou produção. 

Uma das premissas necessárias é que toda a informação necessária para recriar o ecossistema testado está disponível dentro dos artefactos de construção, incluindo a configuração dos recursos Azure. Como os recursos do Azure incorrem num custo quando utilizados, as empresas querem controlar ou acompanhar a utilização destes recursos. Em algumas situações, os modelos do Gestor de Recursos Azure que são usados para criar e configurar os recursos podem ser geridos por outro departamento como o IT. E estes modelos podem ser armazenados num repositório diferente. Conduz a uma situação interessante em que uma construção será criada e testada, e tanto o código como a configuração terão de ser armazenados dentro dos artefactos de construção para recriar adequadamente o sistema em produção. 

Utilizando o DevTest Labs durante a fase de construção/teste, pode adicionar modelos do Gestor de Recursos Azure e ficheiros de suporte às fontes de construção, de modo a que durante a fase de lançamento a configuração exata usada para testar seja implantada para produção. A tarefa **create Azure DevTest Labs Ambiente** com a configuração adequada salvará os modelos do Gestor de Recursos dentro dos artefactos de construção. Para este exemplo, estará a utilizar o código do [Tutorial: Construa uma aplicação web .NET Core e SQL Database no Azure App Service,](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md)para implementar e testar a aplicação web em Azure.

![Fluxo global](./media/use-devtest-labs-build-release-pipelines/overall-flow.png)

## <a name="set-up-azure-resources"></a>Configurar recursos do Azure
Há um par de itens que precisam ser criados previamente:

- Dois repositórios. O primeiro com o código do tutorial e um modelo de Gestor de Recursos com dois VMs adicionais. O segundo conterá o modelo base do Gestor de Recursos Azure (configuração existente).
- Um Grupo de Recursos para a implementação do código de produção e configuração.
- Um laboratório precisa de ser montado com uma [ligação ao repositório](devtest-lab-create-environment-from-arm.md) de configuração para o oleoduto de construção. O modelo do Gestor de Recursos precisa de ser verificado no repositório de configuração como azuredeploy.json com os metadados.json para permitir que os Laboratórios DevTest reconheçam e implementem o modelo.

O oleoduto de construção criará um ambiente DevTest Labs e implementará o código para testes.

## <a name="set-up-a-build-pipeline"></a>Criar um oleoduto de construção
Nos Oleodutos Azure, crie um pipeline de construção utilizando o código do [Tutorial: Construa uma aplicação web .NET Core e SQL Database no Azure App Service](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md). Utilize o modelo **ASP.NET Core,** que preencherá a tarefa necessária para construir, testar e publicar o código.

![Selecione o modelo de ASP.NET](./media/use-devtest-labs-build-release-pipelines/select-asp-net.png)

Você precisa adicionar três tarefas adicionais para criar o ambiente em DevTest Labs e implantar para o ambiente.

![Gasoduto com três tarefas](./media/use-devtest-labs-build-release-pipelines/pipeline-tasks.png)

### <a name="create-environment-task"></a>Criar tarefa ambiental
Na tarefa de criar ambiente (tarefa**Azure DevTest Labs Create Environment)** utilize as listas de drop-down para selecionar os seguintes valores:

- Subscrição do Azure
- nome do laboratório
- nome do repositório
- nome do modelo (que mostra a pasta onde o ambiente é armazenado). 

Recomendamos que utilize listas de drop-down na página em vez de introduzir a informação manualmente. Se introduzir manualmente a informação, introduza ids de recursos azure totalmente qualificados. A tarefa exibe os nomes amigáveis em vez de ids de recursos. 

O nome do ambiente é o nome apresentado no DevTest Labs. Deve ser um nome único para cada construção. Por exemplo: **TestEnv$(Build.BuildId)**. 

Pode especificar ficheiros de parâmetros ou parâmetros para passar informações no modelo do Gestor de Recursos. 

Selecione as **variáveis** de saída Criar com base na opção de saída do modelo de ambiente e introduza um nome de referência. Para este exemplo, insira **o BaseEnv** para o nome de referência. Utilizará este BaseEnv ao configurar a próxima tarefa. 

![Criar a tarefa de ambiente de laboratórios Azure DevTest](./media/use-devtest-labs-build-release-pipelines/create-environment.png)

### <a name="populate-environment-task"></a>Tarefa de ambiente de povoar
A segunda tarefa (Tarefa de**Popuinos Laboratórios DevTest Do Azure)** é atualizar o ambiente existente de Laboratórios DevTest. As saídas de tarefa sonorizadoras de ambiente **BaseEnv.environmentResourceId** que é usada para configurar o nome do ambiente para esta tarefa. O modelo de Gestor de Recursos para este exemplo tem dois parâmetros - **adminUserName** e **adminPassword**. 

![Tarefa ambiente de Laboratórios DevTest DevTest Da Popoazur](./media/use-devtest-labs-build-release-pipelines/populate-environment.png)

## <a name="app-service-deploy-task"></a>Tarefa de implementação do Serviço de Aplicações
A terceira tarefa é a tarefa de implantação do serviço de **aplicações Azure.** O tipo de aplicação está definido para **web app** e o nome do Serviço de Aplicações está definido para **$(WebSite)**.

![Tarefa de implementação de serviço de aplicação](./media/use-devtest-labs-build-release-pipelines/app-service-deploy.png)

## <a name="set-up-release-pipeline"></a>Configurar o gasoduto de libertação
Cria um pipeline de lançamento com duas tarefas: **Implantação Azure: Criar ou atualizar o Grupo de Recursos** e Implementar o Serviço de **Aplicações Azure**. 

Para a primeira tarefa, especifique o nome e a localização do grupo de recursos. A localização do modelo é um artefacto ligado. Se o modelo do Gestor de Recursos incluir modelos ligados, uma implementação de grupo de recursos personalizados precisa de ser implementada. O modelo está no artefacto de gota publicado. Sobrepor os parâmetros do modelo para o modelo de Gestor de Recursos. Pode deixar as definições restantes com valores predefinidos. 

Para a segunda tarefa, implemente o Serviço de **Aplicações Azure,** especifique a subscrição do Azure, selecione **Web App** para o tipo de **aplicação**e **$(WebSite)** para o nome do Serviço de **Aplicações**. Pode deixar as definições restantes com valores predefinidos. 

## <a name="test-run"></a>Teste
Agora que ambos os oleodutos estão montados, faça uma fila manual e veja-a funcionar. O próximo passo é definir o gatilho apropriado para a construção e ligar a construção ao gasoduto de libertação.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Integre o Azure DevTest Labs no seu pipelines Azure de integração contínua e gasoduto de entrega](devtest-lab-integrate-ci-cd-vsts.md)
- [Integre os ambientes nos seus oleodutos CI/CD de Pipelines Azure](integrate-environments-devops-pipeline.md)
