---
title: Desenvolvimento ágil de projetos de ciência de dados - Processo de Ciência de Dados de Equipa
description: Execute um projeto de ciência de dados de forma sistemática, controlada pela versão e colaborativa dentro de uma equipa de projeto, utilizando o Processo de Ciência de Dados de Equipa.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 113c11b749b42bbaf9e5e40cdf63623288997a92
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89440291"
---
# <a name="agile-development-of-data-science-projects"></a>Desenvolvimento ágil de projetos de ciência de dados

Este documento descreve como os desenvolvedores podem executar um projeto de ciência de dados de forma sistemática, controlada pela versão e colaborativa dentro de uma equipa de projeto, utilizando o Processo de Ciência de [Dados de Equipa](overview.md) (TDSP). O TDSP é uma estrutura desenvolvida pela Microsoft que fornece uma sequência estruturada de atividades para executar eficientemente soluções de análise preditiva baseadas na nuvem. Para um esboço das funções e tarefas que são tratadas por uma equipa de ciência de dados que normaliza no TDSP, consulte [as funções e tarefas do Processo de Ciência de Dados de Equipa.](roles-tasks.md) 

Este artigo inclui instruções sobre como: 

- Faça *o planeamento do sprint* para artigos de trabalho envolvidos num projeto.
- Adicione *itens de trabalho* a sprints.
- Crie e use um *modelo de item de trabalho derivado ágil* que se alinha especificamente com as fases do ciclo de vida TDSP.

As seguintes instruções descrevem as etapas necessárias para configurar um ambiente de equipa TDSP utilizando placas Azure e Azure Repos em Azure DevOps. As instruções utilizam o Azure DevOps porque é assim que se implementa o TDSP na Microsoft. Se o seu grupo utilizar uma plataforma de hospedagem de código diferente, as tarefas de liderança da equipa geralmente não mudam, mas a forma de completar as tarefas é diferente. Por exemplo, ligar um item de trabalho com um ramo Git pode não ser o mesmo com o GitHub como é com a Azure Repos.

A seguinte figura ilustra um típico planeamento de sprint, codificação e fluxo de trabalho de controlo de fontes para um projeto de ciência de dados:

![Processo de Ciência de Dados de Equipa](./media/agile-development/1-project-execute.png)

##  <a name="work-item-types"></a><a name='Terminology-1'></a>Tipos de artigos de trabalho

No quadro de planeamento de sprint TDSP, existem quatro tipos de *artigos* de trabalho frequentemente *utilizados: Funcionalidades, Histórias de Utilizador,* *Tarefas* e *Bugs.*  O atraso para todos os itens de trabalho está ao nível do projeto, não ao nível do repositório de Git. 

Aqui estão as definições para os tipos de artigos de trabalho:

- **Recurso**: Um recurso corresponde a um envolvimento do projeto. Diferentes compromissos com um cliente são características diferentes, e é melhor considerar diferentes fases de um projeto como diferentes Características. Se escolher um esquema como *\<ClientName>-\<EngagementName>* o nome das suas Funcionalidades, poderá facilmente reconhecer o contexto do projeto e o envolvimento dos próprios nomes.
  
- **História do utilizador**: As Histórias de Utilizador são itens de trabalho necessários para completar uma funcionalidade de ponta a ponta. Exemplos de Histórias de Utilizadores incluem:
  - Obter dados 
  - Explorar dados 
  - Gerar funcionalidades
  - Criar modelos
  - Operacionalizar modelos 
  - Reenviar modelos
  
- **Tarefa**: As tarefas são itens de trabalho atribuíveis que precisam de ser feitos para completar uma história específica do utilizador. Por exemplo, as tarefas no User Story *Obter dados* podem ser:
  - Obtenha credenciais de servidor SQL
  - Faça upload de dados para a Azure Synapse Analytics
  
- **Bug**: Bugs são problemas no código ou documentos existentes que devem ser corrigidos para completar uma Tarefa. Se os Bugs forem causados por itens de trabalho em falta, podem escalar para serem Histórias de Utilizador ou Tarefas. 

Os cientistas de dados podem sentir-se mais confortáveis usando um modelo ágil que substitui funcionalidades, histórias de utilizador e tarefas por fases e substatos de ciclo de vida TDSP. Para criar um modelo derivado ágil que se alinha especificamente com as fases do ciclo de vida TDSP, consulte [Use um modelo de trabalho ágil de TDSP](#set-up-agile-dsp-6).

> [!NOTE]
> A TDSP empresta os conceitos de Funcionalidades, Histórias de Utilizador, Tarefas e Bugs da gestão de códigos de software (SCM). Os conceitos TDSP podem diferir ligeiramente das suas definições convencionais de SCM.

## <a name="plan-sprints"></a><a name='SprintPlanning-2'></a>Planeie sprints

Muitos cientistas de dados estão envolvidos com vários projetos, que podem levar meses para completar e prosseguir a diferentes ritmos. O planeamento do sprint é útil para a priorização do projeto, e para o planeamento e alocação de recursos. Em Azure Boards, você pode facilmente criar, gerir e rastrear itens de trabalho para seus projetos, e realizar planeamento de sprint para garantir que os projetos estão avançando como esperado.

Para obter mais informações sobre o planeamento do sprint, consulte [os sprints scrum.](https://en.wikipedia.org/wiki/Scrum_(software_development)#Sprint) 

Para obter mais informações sobre o planeamento do sprint em Azure Boards, consulte [atribuir itens de atraso para um sprint](/azure/devops/boards/sprints/assign-work-sprint). 

## <a name="add-a-feature-to-the-backlog"></a><a name='AddFeature-3'></a>Adicione uma funcionalidade ao atraso 

Após a criação do seu projeto e do repositório de código de projeto, pode adicionar uma Funcionalidade ao atraso para representar o trabalho do seu projeto.

1. Na sua página do projeto, selecione **Boards**  >  **Backlogs** na navegação à esquerda. 
   
1. No **separador Backlog,** se o tipo de artigo de trabalho na barra superior for **Stories,** desça e selecione **Funcionalidades**. Em seguida, selecione **New Work Item.**
   
   ![Selecione novo item de trabalho](./media/agile-development/2-sprint-team-overview.png)
   
1. Introduza um título para a Funcionalidade, normalmente o nome do seu projeto e, em seguida, **selecione Adicionar ao topo**. 
   
   ![Insira um título e selecione Adicionar ao topo](./media/agile-development/3-sprint-team-add-work.png)
   
1. Na lista **de Backlog,** selecione e abra a nova Funcionalidade. Preencha a descrição, atribua um membro da equipa e estabeleça parâmetros de planeamento. 
   
   Também pode ligar a Funcionalidade ao repositório de códigoS Azure Repos do projeto selecionando **link Add** na secção **Desenvolvimento.** 
   
   Depois de editar a funcionalidade, **selecione Save & Close**.
   
   ![Editar Recurso e selecionar Guardar & Fechar](./media/agile-development/3a-add-link-repo.png)

## <a name="add-a-user-story-to-the-feature"></a><a name='AddStoryunderfeature-4'></a>Adicione uma história de utilizador à funcionalidade 

No âmbito da Funcionalidade, pode adicionar Histórias de Utilizador para descrever os principais passos necessários para completar o projeto. 

Para adicionar uma nova História do Utilizador a uma Funcionalidade:

1. No **separador Backlog,** selecione **+** o para o lado esquerdo da Funcionalidade. 
   
   ![Adicione uma nova História do Utilizador sob a Funcionalidade](./media/agile-development/4-sprint-add-story.png)
   
1. Dê ao User Story um título e edite detalhes como atribuição, estado, descrição, comentários, planeamento e prioridade. 
   
   Também pode ligar a História do Utilizador a um ramo do repositório de códigoS Azure Repos do projeto, selecionando **link Add** na secção **Desenvolvimento.** Selecione o repositório e o ramo a que pretende ligar o item de trabalho e, em seguida, selecione **OK**.
   
   ![Adicionar link](./media/agile-development/5-sprint-edit-story.png)
   
1. Quando terminar de editar a História do Utilizador, **selecione Save & Close**. 

## <a name="add-a-task-to-a-user-story"></a><a name='AddTaskunderstory-5'></a>Adicione uma tarefa a uma história de utilizador 

As tarefas são passos detalhados específicos que são necessários para completar cada História do Utilizador. Depois de todas as tarefas de uma História do Utilizador estarem concluídas, a História do Utilizador também deve ser concluída. 

Para adicionar uma Tarefa a uma História do Utilizador, selecione **+** o seguinte ao item História do Utilizador e selecione **Tarefa**. Preencha o título e outras informações na Tarefa.

![Adicione uma tarefa a uma história de utilizador](./media/agile-development/7-sprint-add-task.png)

Depois de criar Funcionalidades, Histórias de Utilizador e Tarefas, pode vê-las nas **vistas backlogs** ou **Boards** para acompanhar o seu estado.

![Vista de atrasos](./media/agile-development/8-sprint-backlog-view.png)

![Vista de placas](./media/agile-development/8a-sprint-board-view.png)

## <a name="use-an-agile-tdsp-work-template"></a><a name='set-up-agile-dsp-6'></a>Use um modelo de trabalho ágil TDSP

Os cientistas de dados podem sentir-se mais confortáveis usando um modelo ágil que substitui funcionalidades, histórias de utilizador e tarefas por fases e substatos de ciclo de vida TDSP. Em Azure Boards, você pode criar um modelo derivado ágil que usa fases de ciclo de vida TDSP para criar e rastrear itens de trabalho. Os passos seguintes passam pela criação de um modelo de processo ágil específico da ciência dos dados e na criação de itens de trabalho de ciência de dados com base no modelo.

### <a name="set-up-an-agile-data-science-process-template"></a>Configurar um modelo de processo de ciência de dados ágeis

1. Na página principal da organização Azure DevOps, selecione **as definições** da Organização a partir da navegação à esquerda. 
   
1. Nas **Definições** de Organização deixou a navegação, sob **Placas,** selecione **Processo**. 
   
1. No painel **de todos os processos,** selecione o **...** ao lado do **Agile,** e, em seguida, selecione **Criar processo herdado**.
   
   ![Criar processo herdado a partir de Ágil](./media/agile-development/10-settings.png) 
   
1. No **processo De criar herdado a partir do diálogo Agile,** insira o nome *AgileDataScienceProcess* e selecione **Criar o processo**.
   
   ![Criar processo agileDataScienceProcess](./media/agile-development/11-agileds.png)
   
1. Em **todos os processos,** selecione o novo **AgileDataScienceProcess**. 
   
1. No separador **'Tipos de artigos** de trabalho', desative **a Epic**, **Feature**, **User Story** e **Task** selecionando o **...** ao lado de cada item e, em seguida, selecionando **Desativar**. 
   
   ![Desativar tipos de artigos de trabalho](./media/agile-development/12-disable.png)
   
1. Em **todos os processos,** selecione o **separador níveis de backlog.** Em **Portfólios,** selecione o **...** ao lado da **Epic (desativada)** e, em seguida, selecione **Edit/Rename**. 
   
1. Na caixa de diálogo **de nível de atraso editar:**
   1. Under **Name**, substitua **a Epic** por *Projetos TDSP.* 
   1. Nos **tipos de artigos de trabalho neste nível de atraso**, selecione Novo tipo de item de **trabalho,** introduza *o Projeto TDSP* e selecione **Adicionar**. 
   1. Sob **o tipo de artigo de trabalho predefinido,** desça e selecione O Projeto **TDSP**. 
   1. Selecione **Guardar**.
   
   ![Definir nível de atraso do portfólio](./media/agile-development/13-rename.png)  
   
1. Siga os mesmos passos para renomear **funcionalidades** para *TDSP Stages*, e adicione os seguintes novos tipos de artigos de trabalho:
   
   - *Compreensão empresarial*
   - *Aquisição de Dados*
   - *Modelação*
   - *Implementação*
   
1. Em **substatos de requisitos**, rebatize **Stories** to *TDSP Substages*, adicione o novo item de trabalho do *tipo TDSP Substage*, e deslo deixe o tipo de artigo de trabalho predefinido no **Substage TDSP**.
   
1. No **atraso de iteração**, adicione um novo item de trabalho tipo *TDSP Task*, e desateia-o como o tipo de produto de trabalho predefinido. 
   
Depois de completar os passos, os níveis de atraso devem ficar assim:
   
 ![Níveis de atraso do modelo TDSP](./media/agile-development/14-template.png)  

### <a name="create-agile-data-science-process-work-items"></a>Criar itens de trabalho do Processo de Ciência de Dados Ágil

Pode utilizar o modelo de processo de ciência de dados para criar projetos TDSP e rastrear itens de trabalho que correspondam às fases do ciclo de vida da TDSP.

1. A partir da sua página principal da organização Azure DevOps, selecione **Novo projeto**. 
   
1. No novo diálogo do **projeto,** dê um nome ao seu projeto e, em seguida, selecione **Advanced**. 
   
1. No **processo de item do trabalho,** desça e selecione **AgileDataScienceProcess** e, em seguida, selecione **Criar**.
   
   ![Criar um projeto TDSP](./media/agile-development/15-newproject.png)
   
1. No projeto recém-criado, selecione **Boards**  >  **Backlogs** na navegação à esquerda.
   
1. Para tornar os Projetos TDSP visíveis, selecione o ícone **de configuração da equipa Configure.** No ecrã **Definições,** selecione a caixa de verificação **dos Projetos TDSP** e, em seguida, selecione **Guardar e fechar**.
   
   ![Selecione caixa de verificação de projetos TDSP](./media/agile-development/16-enabledsprojects1.png)
   
1. Para criar um Projeto TDSP específico para a ciência dos dados, selecione **projetos TDSP** na barra superior e, em seguida, selecione **Novo item de trabalho**. 
   
1. No popup, dê um nome ao item de trabalho do Projeto TDSP e selecione **Adicionar ao topo**.
   
   ![Criar item de trabalho de projeto de ciência de dados](./media/agile-development/17-dsworkitems0.png)
   
1. Para adicionar um item de trabalho no âmbito do Projeto TDSP, selecione o **+** próximo ao projeto e, em seguida, selecione o tipo de item de trabalho para criar. 
   
   ![Selecione o tipo de item de trabalho de ciência de dados](./media/agile-development/17-dsworkitems1.png)
   
1. Preencha os detalhes do novo item de trabalho e **selecione Save & Close**.
   
1. Continue a selecionar os **+** símbolos ao lado de itens de trabalho para adicionar novos Estágios, Substatos e Tarefas TDSP. 
   
Aqui está um exemplo de como os itens de trabalho do projeto de ciência de dados devem aparecer na visão **de Backlogs:**

![18](./media/agile-development/18-workitems1.png)


## <a name="next-steps"></a>Passos seguintes

[A codificação colaborativa com o Git](collaborative-coding-with-git.md) descreve como fazer o desenvolvimento colaborativo de códigos para projetos de ciência de dados usando Git como o quadro de desenvolvimento de código compartilhado, e como ligar essas atividades de codificação ao trabalho planeado com o processo ágil.

[Exemplo de passagem de imagens](walkthroughs.md) lista os passos de cenários específicos, com links e descrições de miniaturas. Os cenários ligados ilustram como combinar ferramentas e serviços de nuvem e no local em fluxos de trabalho ou oleodutos para criar aplicações inteligentes.
  
Recursos adicionais em processos ágeis:

- [Processo Ágil](/azure/devops/boards/work-items/guidance/agile-process)
  
- [Tipos de artigos de trabalho de processo ágil e fluxo de trabalho](/azure/devops/boards/work-items/guidance/agile-process-workflow)

