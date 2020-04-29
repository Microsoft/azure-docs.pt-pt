---
title: Desenvolvimento ágil de projetos de ciência de dados - Team Data Science Process
description: Execute um projeto de ciência de dados de forma sistemática, controlada e colaborativa dentro de uma equipa de projeto, utilizando o Processo de Ciência de Dados da Equipa.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c097c14406349d973e905fadb806cc159d9b16d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76722106"
---
# <a name="agile-development-of-data-science-projects"></a>Desenvolvimento ágil de projetos de ciência de dados

Este documento descreve como os desenvolvedores podem executar um projeto de ciência de dados de forma sistemática, controlada e colaborativa dentro de uma equipa de projeto, utilizando o Processo de Ciência de Dados da [Equipa](overview.md) (TDSP). O TDSP é um quadro desenvolvido pela Microsoft que fornece uma sequência estruturada de atividades para executar eficientemente soluções de análise preditivas baseadas na nuvem. Para um esboço das funções e tarefas que são tratadas por uma equipa de ciência de dados padronizada no TDSP, consulte as [funções e tarefas](roles-tasks.md)do Processo de Ciência de Dados da Equipa. 

Este artigo inclui instruções sobre como: 

- Faça *o planeamento de sprint* para itens de trabalho envolvidos num projeto.
- Adicione itens de *trabalho* aos sprints.
- Crie e use um *modelo de item de trabalho de deriva de ágeis* que se alinha especificamente com estágios de ciclo de vida TDSP.

As seguintes instruções descrevem os passos necessários para criar um ambiente de equipa TDSP utilizando placas Azure e Azure Repos em Azure DevOps. As instruções utilizam O Azure DevOps porque é assim que se implementa o TDSP na Microsoft. Se o seu grupo usa uma plataforma de hospedagem de código diferente, as tarefas de liderança da equipa geralmente não mudam, mas a forma de completar as tarefas é diferente. Por exemplo, ligar um item de trabalho a uma sucursal git pode não ser o mesmo com o GitHub como é com o Azure Repos.

A figura que se segue ilustra um típico fluxo de trabalho de planeamento, codificação e controlo de fontes para um projeto de ciência de dados:

![Processo de Ciência de Dados de Equipa](./media/agile-development/1-project-execute.png)

##  <a name="work-item-types"></a><a name='Terminology-1'></a>Tipos de artigos de trabalho

No quadro de planeamento de sprint TDSP, existem quatro tipos de *itens* de trabalho frequentemente utilizados: *Funcionalidades*, Histórias de *Utilizador,* *Tarefas*e *Bugs.* O atraso para todos os itens de trabalho está ao nível do projeto, não ao nível do repositório Git. 

Aqui estão as definições para os tipos de artigos de trabalho:

- **Recurso**: Uma Funcionalidade corresponde a um envolvimento do projeto. Diferentes compromissos com um cliente são diferentes Funcionalidades, e o melhor é considerar diferentes fases de um projeto como diferentes Funcionalidades. Se escolher um esquema como * \<o Nome\<* de Cliente>- Nome de Noivado>para nomear as suas Funcionalidades, pode facilmente reconhecer o contexto do projeto e o envolvimento dos próprios nomes.
  
- **História**do utilizador : As Histórias de Utilizador são itens de trabalho necessários para completar uma funcionalidade de ponta a ponta. Exemplos de Histórias de Utilizadores incluem:
  - Obter dados 
  - Explorar dados 
  - Gerar características
  - Criar modelos
  - Operacionalizar modelos 
  - Reenviar modelos
  
- **Tarefa**: As tarefas são itens de trabalho atribuíveis que precisam de ser feitos para completar uma história específica do utilizador. Por exemplo, as tarefas no User Story *Get podem* ser:
  - Obtenha credenciais de Servidor SQL
  - Enviar dados para SQL Data Warehouse
  
- **Bug**: Bugs são problemas no código ou documentos existentes que devem ser corrigidos para completar uma Tarefa. Se os Bugs forem causados por itens de trabalho em falta, podem escalar para serem User Stories ou Tasks. 

Os cientistas de dados podem sentir-se mais confortáveis usando um modelo ágil que substitui funcionalidades, histórias de utilizadores e tarefas por estágios e subestágios do ciclo de vida TDSP. Para criar um modelo ágil derivado que se alinhe especificamente com as fases do ciclo de vida da TDSP, consulte Utilize um modelo de [trabalho ágil tDSP](#set-up-agile-dsp-6).

> [!NOTE]
> A TDSP empresta os conceitos de Funcionalidades, Histórias de Utilizadores, Tarefas e Bugs da gestão de códigos de software (SCM). Os conceitos TDSP podem diferir ligeiramente das suas definições convencionais de SCM.

## <a name="plan-sprints"></a><a name='SprintPlanning-2'></a>Plano sprints

Muitos cientistas de dados estão envolvidos com vários projetos, que podem levar meses para completar e prosseguir a diferentes ritmos. O planeamento do sprint é útil para a priorização de projetos, e planeamento e alocação de recursos. Nas Placas Azure, pode facilmente criar, gerir e rastrear itens de trabalho para os seus projetos, e realizar o planeamento do sprint para garantir que os projetos estão a avançar como esperado.

Para obter mais informações sobre o planeamento do sprint, consulte [os sprints de Scrum.](https://en.wikipedia.org/wiki/Scrum_(software_development)#Sprint) 

Para obter mais informações sobre o planeamento do sprint em Placas Azure, consulte itens de atraso de [atribuição para um sprint](/azure/devops/boards/sprints/assign-work-sprint). 

## <a name="add-a-feature-to-the-backlog"></a><a name='AddFeature-3'></a>Adicione uma Funcionalidade ao atraso 

Após a criação do seu repositório de código de projeto e projeto, pode adicionar uma Funcionalidade ao atraso para representar o trabalho para o seu projeto.

1. Na sua página do projeto, selecione > **'Backlogs'** de **Placas**na navegação à esquerda. 
   
1. No **separador Backlog,** se o tipo de artigo de trabalho na barra superior for **Stories,** desça e selecione **Funcionalidades**. Em seguida, selecione **New Work Item.**
   
   ![Selecione Novo Item de Trabalho](./media/agile-development/2-sprint-team-overview.png)
   
1. Introduza um título para a Funcionalidade, normalmente o seu nome de projeto, e, em seguida, selecione **Adicionar para topo**. 
   
   ![Insira um título e selecione Adicionar ao topo](./media/agile-development/3-sprint-team-add-work.png)
   
1. A partir da lista **de atrasos,** selecione e abra a nova Funcionalidade. Preencha a descrição, designe um membro da equipa e defina os parâmetros de planeamento. 
   
   Também pode ligar a Funcionalidade ao repositório de código Azure Repos do projeto, selecionando o **link Adicionar** na secção **Desenvolvimento.** 
   
   Depois de editar a funcionalidade, selecione **Guardar & Fechar**.
   
   ![Editar funcionalidade e selecionar Guardar & Fechar](./media/agile-development/3a-add-link-repo.png)

## <a name="add-a-user-story-to-the-feature"></a><a name='AddStoryunderfeature-4'></a>Adicione uma história de utilizador à funcionalidade 

No âmbito da Funcionalidade, pode adicionar User Stories para descrever os principais passos necessários para completar o projeto. 

Para adicionar uma nova História de Utilizador a uma Funcionalidade:

1. No **separador Backlog,** selecione o **+** à esquerda da Funcionalidade. 
   
   ![Adicione uma nova História de Utilizador sob a Funcionalidade](./media/agile-development/4-sprint-add-story.png)
   
1. Dê um título ao User Story e edite detalhes como atribuição, estado, descrição, comentários, planeamento e prioridade. 
   
   Também pode ligar a História do Utilizador a um ramo do repositório de código Azure Repos do projeto, selecionando o **link Add** na secção **Desenvolvimento.** Selecione o repositório e o ramo a que pretende ligar o item de trabalho e, em seguida, selecione **OK**.
   
   ![Adicionar link](./media/agile-development/5-sprint-edit-story.png)
   
1. Quando terminar de editar a História do Utilizador, selecione **Guardar & Fechar**. 

## <a name="add-a-task-to-a-user-story"></a><a name='AddTaskunderstory-5'></a>Adicione uma tarefa a uma história de utilizador 

As tarefas são passos detalhados específicos que são necessários para completar cada História do Utilizador. Depois de todas as Tarefas de uma História de Utilizador estarem concluídas, a História do Utilizador também deve estar concluída. 

Para adicionar uma Tarefa a uma **+** História de Utilizador, selecione o próximo ao item user Story e selecione **Task**. Preencha o título e outras informações na Tarefa.

![Adicione uma tarefa a uma história de utilizador](./media/agile-development/7-sprint-add-task.png)

Depois de criar Funcionalidades, Histórias de Utilizadores e Tarefas, pode vê-las nas vistas de **Atrasos** ou **Placas** para acompanhar o seu estado.

![Vista de atrasos](./media/agile-development/8-sprint-backlog-view.png)

![Vista de pranchas](./media/agile-development/8a-sprint-board-view.png)

## <a name="use-an-agile-tdsp-work-template"></a><a name='set-up-agile-dsp-6'></a>Use um modelo de trabalho ágil TDSP

Os cientistas de dados podem sentir-se mais confortáveis usando um modelo ágil que substitui funcionalidades, histórias de utilizadores e tarefas por estágios e subestágios do ciclo de vida TDSP. Em Placas Azure, você pode criar um modelo de deriva de ágeis que usa estágios de ciclo de vida TDSP para criar e rastrear itens de trabalho. Os passos seguintes passam pela criação de um modelo de processo ágil específico da ciência dos dados e criação de itens de trabalho de ciência de dados com base no modelo.

### <a name="set-up-an-agile-data-science-process-template"></a>Configurar um modelo de processo de ciência de dados ágil

1. A partir da página principal da organização Azure DevOps, selecione **as definições** da Organização a partir da navegação à esquerda. 
   
1. Nas **Definições** de Organização deixou a navegação, sob **as Placas,** selecione **Processo**. 
   
1. No painel **de todos os processos,** selecione o **...** ao lado de **Agile**, e, em seguida, selecione **Criar processo herdado**.
   
   ![Criar processo herdado de Ágile](./media/agile-development/10-settings.png) 
   
1. No **processo Create herdado a partir do diálogo Ágile,** introduza o nome *AgileDataScienceProcess*, e selecione **O processo Criar**.
   
   ![Criar processo AgileDataScienceProcess](./media/agile-development/11-agileds.png)
   
1. Em **todos os processos,** selecione o novo **AgileDataScienceProcess**. 
   
1. No separador de tipos de **itens de trabalho,** desative **o Épico,** **funcionalidade,** História do **Utilizador**e **Tarefa** selecionando o **...** ao lado de cada item e, em seguida, selecionando **Desativar**. 
   
   ![Desativar tipos de artigos de trabalho](./media/agile-development/12-disable.png)
   
1. Em **todos os processos,** selecione o separador **de níveis de atraso.** Em **'Carteiras' de atrasos,** selecione o **...** ao lado da **Epic (desativada)** e, em **seguida, selecione Editar/Rename**. 
   
1. Na caixa de diálogo **de nível de atraso editar:**
   1. Em **nome**, substitua **a Epic** por *Projetos TDSP*. 
   1. Sob os tipos de **itens de trabalho neste nível de atraso,** selecione Novo tipo de artigo de **trabalho,** insira *o Projeto TDSP,* e selecione **Adicionar**. 
   1. No tipo de artigo de **trabalho Predefinido,** desça e selecione **Projeto TDSP**. 
   1. Selecione **Guardar**.
   
   ![Definir nível de atraso do portfólio](./media/agile-development/13-rename.png)  
   
1. Siga os mesmos passos para renomear **funcionalidades** para *estágios TDSP,* e adicione os seguintes novos tipos de itens de trabalho:
   
   - *Compreensão de Negócios*
   - *Aquisição de Dados*
   - *Modelação*
   - *Implementação*
   
1. Sob **atraso de requisitos**, rebatiza **histórias** para *subpalcos TDSP,* adicione o novo item de trabalho tipo *TDSP Substage,* e detete o tipo de artigo de trabalho padrão para **substagee TDSP**.
   
1. Em atraso de **Iteração,** adicione um novo item de trabalho tipo *TDSP Task*, e detetete-o como o tipo de item de trabalho padrão. 
   
Depois de completar os passos, os níveis de atraso devem ser assim:
   
 ![Níveis de atraso do modelo TDSP](./media/agile-development/14-template.png)  

### <a name="create-agile-data-science-process-work-items"></a>Criar itens de trabalho do Processo de Ciência de Dados Ágeis

Você pode usar o modelo de processo de ciência de dados para criar projetos TDSP e rastrear itens de trabalho que correspondem às fases do ciclo de vida TDSP.

1. A partir da sua página principal da organização Azure DevOps, selecione **Novo projeto.** 
   
1. No diálogo **criar novo projeto,** dar um nome ao seu projeto e, em seguida, selecionar **Avançado**. 
   
1. No **processo de artigo de trabalho,** desça e selecione **AgileDataScienceProcess**, e depois selecione **Criar**.
   
   ![Criar um projeto TDSP](./media/agile-development/15-newproject.png)
   
1. No projeto recém-criado, selecione **Boards** > **Backlogs** na navegação à esquerda.
   
1. Para tornar os Projetos TDSP visíveis, selecione o ícone de definições da **equipa Configure.** No ecrã **Definições,** selecione a caixa de verificação de **projetos TDSP** e, em seguida, selecione **Guardar e fechar**.
   
   ![Selecione TDSP Projects check box](./media/agile-development/16-enabledsprojects1.png)
   
1. Para criar um Projeto TDSP específico para a ciência dos dados, selecione **Projetos TDSP** na barra superior e, em seguida, selecione **Novo item**de trabalho . 
   
1. No popup, dê um nome ao artigo de trabalho do Projeto TDSP e selecione **Adicionar ao topo**.
   
   ![Criar itens de trabalho de projeto de ciência de dados](./media/agile-development/17-dsworkitems0.png)
   
1. Para adicionar um item de trabalho no âmbito **+** do Projeto TDSP, selecione o próximo ao projeto e, em seguida, selecione o tipo de item de trabalho para criar. 
   
   ![Selecione o tipo de trabalho de ciência de dados](./media/agile-development/17-dsworkitems1.png)
   
1. Preencha os detalhes no novo artigo de trabalho e selecione **Guardar & Fechar**.
   
1. Continue a **+** selecionar os símbolos ao lado de itens de trabalho para adicionar novos Estágios TDSP, Subestágios e Tarefas. 
   
Aqui está um exemplo de como os itens de trabalho do projeto de ciência de dados devem aparecer na visão **de Backlogs:**

![18](./media/agile-development/18-workitems1.png)


## <a name="next-steps"></a>Passos seguintes

[A codificação colaborativa com git](collaborative-coding-with-git.md) descreve como fazer o desenvolvimento colaborativo de códigos para projetos de ciência de dados usando Git como o quadro de desenvolvimento de código compartilhado, e como ligar essas atividades de codificação ao trabalho planeado com o processo ágil.

[Os walkthroughs de exemplo](walkthroughs.md) listam caminhadas de cenários específicos, com links e descrições de miniaturas. Os cenários ligados ilustram como combinar ferramentas e serviços em fluxos de trabalho ou oleodutos para criar aplicações inteligentes.
  
Recursos adicionais em processos ágeis:

- [Processo Ágil](/azure/devops/boards/work-items/guidance/agile-process)
  
- [Tipos de artigos de trabalho de processo ágil e fluxo de trabalho](/azure/devops/boards/work-items/guidance/agile-process-workflow)

