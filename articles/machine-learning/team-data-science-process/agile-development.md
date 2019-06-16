---
title: Desenvolvimento ágil de projetos de ciência de dados - Team Data Science Process
description: Como os desenvolvedores podem executar um projeto de ciência de dados numa versão sistemática, controlada e forma colaborativa dentro de uma equipe de projeto usando o processo de ciência de dados de equipa.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: adf713fc3f875168f99b302b0a9affef88e8414f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60327902"
---
# <a name="agile-development-of-data-science-projects"></a>Desenvolvimento ágil de projetos de ciência de dados

Este documento descreve como os desenvolvedores podem executar um projeto de ciência de dados numa sistemática, a versão controlada e a forma de colaboração dentro de uma equipe de projeto, utilizando o [Team Data Science Process](overview.md) (TDSP). O TDSP é uma estrutura desenvolvida pela Microsoft que fornece uma sequência estruturada de atividades para executar soluções de Análise Preditiva com base na cloud de forma eficiente. Para um destaque das funções de equipe e das respetivas tarefas associadas que são processadas por um dados ciência equipe padronização sobre esse processo, consulte [Team Data Science Process funções e tarefas](roles-tasks.md). 

Este artigo inclui instruções sobre como: 

1. fazer **planejamento de sprints** para itens de trabalho envolvidos num projeto.<br> Se não estiver familiarizado com o planejamento de sprints, pode encontrar detalhes e informações gerais [aqui](https://en.wikipedia.org/wiki/Sprint_(software_development) "aqui"). 
2. **Adicionar itens de trabalho** para sprints. 

> [!NOTE]
> Os passos necessários para configurar um ambiente de equipe do TDSP usando os serviços do Azure DevOps descritos na seguinte conjunto de instruções. Eles especificam como realizar estas tarefas com os serviços de DevOps do Azure, uma vez que é como implementar o TDSP na Microsoft.  Se optar por utilizar os serviços do Azure DevOps, itens (3) e (4) na lista anterior são as vantagens que obtém naturalmente. Se outro código de plataforma de alojamento é utilizado para o seu grupo, as tarefas que devem ser concluídas pelo líder da Equipe geralmente não são alterados. Mas a forma de concluir estas tarefas vai ser diferente. Por exemplo, o item na secção seis, **ligar um item de trabalho com um ramo de Git**, poderá não ser tão fácil quanto nos serviços de DevOps do Azure.
>
>

A figura a seguir ilustra um sprint típico, planejamento, codificação e fluxo de trabalho de controlo de origem envolvido na implementação de um projeto de ciência de dados:

![1](./media/agile-development/1-project-execute.png)


##  1. <a name='Terminology-1'></a>Terminologia 

No TDSP framework planejamento de sprint, existem quatro tipos mais usados de **itens de trabalho**: **Funcionalidade**, **história de usuário**, **tarefas**, e **Bug**. Cada projeto mantém um registo de segurança único para todos os itens de trabalho. Não existe nenhum registo de segurança ao nível do repositório de Git num projeto. Aqui estão as respetivas definições:

- **Funcionalidade**: Um recurso corresponde a um compromisso de projeto. Diferentes esferas com um cliente são consideradas diferentes funcionalidades. Da mesma forma, é melhor considerar diferentes fases de um projeto com um cliente como diferentes funcionalidades. Se escolher um esquema, tal como ***ClientName EngagementName*** nomear seus recursos, em seguida, pode facilmente reconhecer o contexto do projeto/compromisso dos nomes próprios.
- **História**: Histórias são itens de trabalho diferentes que são necessárias para concluir uma recurso (projeto)-a-ponto. Exemplos de histórias incluem:
    - Obtenção de dados 
    - Explorar dados 
    - Geração de recursos
    - Criar modelos
    - Operacionalizar modelos 
    - Reparametrizar modelos
- **Tarefa**: As tarefas são atribuível código ou de itens de trabalho de documento ou de outras atividades que precisam ser feito para concluir uma história específica. Por exemplo, tarefas na história *inserindo dados* poderia ser:
    -  Obter as credenciais do SQL Server 
    -  Carregar dados para o SQL Data Warehouse. 
- **Bug**: Bugs normalmente se referem a correções que são necessários para um código existente ou um documento que são realizadas quando concluir uma tarefa. Se o erro é causado por falta fases ou tarefas, respetivamente, ele pode escalar a ser uma história ou uma tarefa. 

> [!NOTE]
> Conceitos emprestados de recursos, histórias, tarefas e bugs de gerenciamento de código de software (SCM) para ser usado em ciência de dados. Eles podem ser ligeiramente diferente das respetivas definições SCM convencionais.
>
>

> [!NOTE]
> Os cientistas de dados podem se sentir mais confortáveis com um modelo agile que especificamente se alinha com as fases do ciclo de vida TDSP. Com isso em mente, tem sido criado um modelo de planejamento de sprints de derivado do Agile, onde Epopéias, etc. de histórias são substituídos por fases do ciclo de vida TDSP ou substages. Para obter instruções sobre como criar um modelo agile, consulte [configurar o processo de ciência de dados ágil no Visual Studio Online](agile-development.md#set-up-agile-dsp-6).
>
>

## 2. <a name='SprintPlanning-2'></a>Planejamento de Sprint 

Planejamento de sprints é útil para atribuição de prioridades do projeto e o planejamento de recursos e a alocação. Muitos cientistas de dados são envolvidos com vários projetos, cada uma delas pode levar meses para ser concluída. Projetos, muitas vezes, continuar em prova diferentes. Nos serviços de DevOps do Azure, pode facilmente criar, gerir e controlar os itens de trabalho no seu projeto e conduzir o planejamento de sprints para garantir que seus projetos avançam conforme esperado. 

Siga [esta ligação](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning) para as instruções passo a passo sobre sprint planejamento nos serviços de DevOps do Azure. 


## 3. <a name='AddFeature-3'></a>Adicionar um recurso  

Depois do repositório de projeto é criado num projeto, vá para a equipe **descrição geral** página e clique em **gerenciar o trabalho**.

![2](./media/agile-development/2-sprint-team-overview.png)

Para incluir um recurso na lista de pendências, clique em **listas de pendências** --> **funcionalidades** --> **New**, tipo no recurso **Title**(normalmente, o nome do projeto) e, em seguida, clique em **adicionar** .

![3](./media/agile-development/3-sprint-team-add-work.png)

Clique duas vezes o recurso que criou. Preencha as descrições e atribuir os membros da Equipe para esta funcionalidade para definir parâmetros para esse recurso de planejamento. 

Também pode ligar esta funcionalidade para o repositório do projeto. Clique em **adicionar ligação** sob a **desenvolvimento** secção. Depois de terminar de editar a funcionalidade, clique em **guardar e fechar** para sair.


## 4. <a name='AddStoryunderfeature-4'></a>Adicionar a história em funcionalidade 

Sob o recurso, histórias podem ser adicionadas para descrever os principais passos necessários para concluir o projeto (recurso). Para adicionar uma nova história, clique a **+** iniciar sessão para a esquerda da funcionalidade na vista de lista de pendências.  

![4](./media/agile-development/4-sprint-add-story.png)

Pode editar os detalhes da história, por exemplo, o estado, descrição, comentários, planejamento e prioridade na janela de pop-up.

![5](./media/agile-development/5-sprint-edit-story.png)

Pode associar essa história para um repositório existente clicando **+ adicionar ligação** sob **desenvolvimento**. 

![6](./media/agile-development/6-sprint-link-existing-branch.png)


## 5. <a name='AddTaskunderstory-5'></a>Adicione uma tarefa a uma história 

As tarefas são os passos detalhados específicos que são necessárias para concluir cada história. Depois de todas as tarefas de uma história são concluídas, a história deve ser concluída demasiado. 

Para adicionar uma tarefa a uma história, clique nas **+** início de sessão junto ao item da história, selecione **tarefas**e, em seguida, preencha as informações detalhadas desta tarefa na janela de pop-up.

![7](./media/agile-development/7-sprint-add-task.png)

Depois dos recursos, histórias e as tarefas são criadas, pode ver-os na **lista de pendências** ou **quadro** vistas para controlar o respetivo estado.

![8](./media/agile-development/8-sprint-backlog-view.png)

![9](./media/agile-development/9-link-to-a-new-branch.png)


## 6. <a name='set-up-agile-dsp-6'></a> Configurar um modelo de trabalho do Agile TDSP no Visual Studio Online

Este artigo explica como configurar um modelo de processo de ciência de dados ágil que utiliza as fases de ciclo de vida de ciência de dados do TDSP e controla os itens de trabalho com o Visual Studio Online (vso). Os passos abaixo o exemplo de como configurar a ciência específicas de dados ágil processam modelo *AgileDataScienceProcess* e mostram como criar itens de trabalho de ciência de dados com base no modelo.

### <a name="agile-data-science-process-template-setup"></a>Configuração de modelo de processo de ciência de dados ágil

1. Navegue para a página inicial do servidor, **configurar** -> **processo**.

    ![10](./media/agile-development/10-settings.png) 

2. Navegue para **todos os processos** -> **processos**, em **Agile** e clique em **criar herdadas processo**. Em seguida, colocar o nome de processo "AgileDataScienceProcess" e clique em **criar processo**.

    ![11](./media/agile-development/11-agileds.png)

3. Sob o **AgileDataScienceProcess** -> **tipos de item de trabalho** separador, desative **Epic**, **funcionalidade**,  **História de usuário**, e **tarefa** tipos por do item de trabalho **configurar -> desativar**

    ![12](./media/agile-development/12-disable.png)

4. Navegue para **AgileDataScienceProcess** -> **níveis de registo de segurança** separador. Mudar o nome "Epopéias" para "TDSP Projects" ao clicar no **configurar** -> **editar/mudança de nome**. Na mesma caixa de diálogo, clique em **+ novo tipo de item de trabalho** em "Projeto de ciência de dados" e defina o valor de **tipo de item de trabalho padrão** para "Projeto de TDSP" 

    ![13](./media/agile-development/13-rename.png)  

5. Da mesma forma, altere o nome de registo de segurança "Recursos" para "TDSP Stages" e adicione o seguinte para o **tipo de item de trabalho nova**:

    - Compreensão Empresarial
    - Aquisição de dados
    - Modelação
    - Implementação

6. Mudar o nome "História de usuário" para "TDSP Substages" com o tipo de item de trabalho padrão definida como "TDSP Substage" recém-criado tipo.

7. Definir as "tarefas" para recentemente criado o tipo de item de trabalho "Tarefa TDSP" 

8. Após estes passos, os níveis de registo de segurança devem ter um aspeto semelhante a esta:

    ![14](./media/agile-development/14-template.png)  

 
### <a name="create-data-science-work-items"></a>Criar itens de trabalho de ciência de dados

Depois de criar o modelo de processo de ciência de dados, pode criar e controlar seus itens de trabalho de ciência de dados que correspondem para o ciclo de vida do TDSP.

1. Quando cria um novo projeto, selecione "Agile\AgileDataScienceProcess" como o **processo do item de trabalho**:

    ![15](./media/agile-development/15-newproject.png)

2. Navegue para o projeto recém-criado e clique em **Work** -> **listas de pendências**.

3. Tornar "TDSP Projects" visível ao clicar em **configurar as definições team** e verifique "TDSP Projects"; em seguida, guarde.

    ![16](./media/agile-development/16-enabledsprojects.png)

4. Agora pode começar a criar os itens de trabalho específicos de ciência de dados.

    ![17](./media/agile-development/17-dsworkitems.png)

5. Eis um exemplo de como os itens de trabalho de projeto de ciência de dados devem aparecer:

    ![18](./media/agile-development/18-workitems.png)


## <a name="next-steps"></a>Passos Seguintes

[Codificação de colaboração com Git](collaborative-coding-with-git.md) descreve como fazer o desenvolvimento de código de colaboração para projetos de ciência de dados com o Git como a estrutura de desenvolvimento de código compartilhado e como ligá-las codificação atividades para o trabalho planejado com os processos "agile".

Seguem-se links adicionais para recursos na processos ágeis.

- Processos "agile"   [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process)
- Tipos de item de trabalho de processos "agile" e o fluxo de trabalho   [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow)


Orientações passo a passo que demonstram todas as etapas do processo para **cenários específicos** também são fornecidas. Se estão listadas e estão associados ao descrições em miniatura da [instruções passo a passo do exemplo](walkthroughs.md) artigo. Eles ilustram como combinar a cloud, ferramentas no local e serviços num fluxo de trabalho ou um pipeline para criar uma aplicação inteligente. 
