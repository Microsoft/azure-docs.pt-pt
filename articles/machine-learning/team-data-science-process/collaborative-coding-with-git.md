---
title: Codificação em colaboração com Git - Team Data Science Process
description: Como fazer o desenvolvimento de código de colaboração para projetos de ciência de dados com o Git com o planejamento ágil.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 551d0cd149c4d1555a40ccf0d7baeff97c6809c2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863309"
---
# <a name="collaborative-coding-with-git"></a>Codificação em colaboração com Git

Neste artigo, descrevemos como fazer o desenvolvimento de código de colaboração para projetos de ciência de dados com o Git como a estrutura de desenvolvimento de código compartilhado. Ele aborda como ligá-las atividades para o trabalho planejado de codificação [Desenvolvimento ágil](agile-development.md) e como revisões de código.


## 1. <a name='Linkaworkitemwithagitbranch-1'></a>Ligar um item de trabalho com ramos do Git 

Serviços de DevOps do Azure fornece uma maneira conveniente de conectar um item de trabalho (uma história ou tarefas) com um ramo de Git. Isto permite-lhe ligar a sua história ou a tarefa diretamente para o código associado ele. 

Para ligar um item de trabalho para um novo ramo, faça duplo clique num item de trabalho e, na janela de pop-up, clique em **criar um novo ramo** sob **+ adicionar ligação**.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

Forneça as informações para essa nova ramificação, como o nome do ramo, o repositório de Git base e o ramo. O repositório de Git escolhido tem de ser repositório ao abrigo do mesmo projeto que o item de trabalho pertence. O ramo base pode ser o ramo principal ou alguma outra ramificação existente.

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

É uma boa prática criar um ramo de Git para cada item de trabalho da história. Em seguida, para cada item de trabalho de tarefas, crie um ramo com base no ramo história. Organizar os ramos desta forma hierárquica que corresponde às relações de tarefa de história é útil quando tem várias pessoas trabalhando em histórias diferentes do mesmo projeto ou ter várias pessoas trabalhando em diferentes tarefas da mesma história. Conflitos podem ser minimizados quando cada membro da Equipe trabalha numa ramificação diferente e quando cada membro funciona em diferentes códigos ou outros artefactos ao partilhar um ramo. 

A imagem seguinte ilustra a estratégia de ramificação recomendada para o TDSP. Poderá não ser necessário à medida que muitas ramos que são mostrados aqui, especialmente quando tiver apenas uma ou duas pessoas trabalhando no mesmo projeto, ou apenas uma pessoa funciona em todas as tarefas de uma história. Mas separar a ramificação de desenvolvimento do ramo principal é sempre uma boa prática. Isso pode ajudar a impedir que o ramo de lançamento a ser interrompido por atividades de desenvolvimento. Uma descrição mais completa do modelo de ramificação Git pode ser encontrada na [um êxito Git ramificação modelo](https://nvie.com/posts/a-successful-git-branching-model/).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Para mudar para o ramo que deseja trabalhar, execute o seguinte comando num comando de shell (Windows ou Linux). 

    git checkout <branch name>

Alterar o *< nome do ramo\>*  para **mestre** comutadores de volta para o **mestre** ramo. Depois de mudar para o ramo de trabalho, pode começar a trabalhar esse item de trabalho, os artefatos de código ou a documentação necessários para concluir o item de desenvolvimento. 

Também pode associar um item de trabalho a uma ramificação existente. Na **detalhe** página de um item de trabalho, em vez de clicar **criar um novo ramo**, clicar em **+ adicionar ligação**. Em seguida, selecione o ramo que pretende associar o item de trabalho para. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

Também pode criar um novo ramo no Git Bash comandos. Se < nome do ramo de base\> está em falta, o < nome do novo ramo\> baseia-se no _mestre_ ramo. 
    
    git checkout -b <new branch name> <base branch name>


## 2. <a name='WorkonaBranchandCommittheChanges-2'></a>Trabalhe num ramo e confirmar as alterações 

Agora suponha que fizer alguma alteração para o *dados\_ingestão* ramo para o item de trabalho, como adicionar um ficheiro de R no ramo no seu computador local. Pode consolidar o ficheiro de R adicionadas para o ramo para este item de trabalho, desde que estiver nessa sucursal na sua shell do Git, através dos seguintes comandos do Git:

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## 3. <a name='CreateapullrequestonVSTS-3'></a>Crie um pedido pull nos serviços de DevOps do Azure 

Quando estiver pronto, após alguns consolidações e de push, para unir o ramo atual no seu ramo de base, pode submeter um **pedido pull** nos serviços de DevOps do Azure. 

Aceda à página principal do seu projeto e clique em **código**. Selecione o ramo a serem intercalados e o nome do repositório de Git que deseja mesclar a ramificação em. Em seguida, clique em **pedidos Pull**, clique em **novo pedido pull** para criar uma revisão de pedidos pull antes do trabalho no ramo é mesclado para o seu ramo de base.

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

Preencha algumas descrição sobre este pedido pull, adicione os revisores e enviá-lo.

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## 4. <a name='ReviewandMerge-4'></a>Revisão e intercalação 

Quando o pedido pull é criado, os revisores obtém uma notificação por e-mail para rever os pedidos pull. Os revisores necessário verificar se as alterações estão a funcionar ou não e testar as alterações com o solicitante se possível. Com base na sua avaliação, os revisores, podem aprovar ou rejeitar o pedido pull. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

![9](./media/collaborative-coding-with-git/9-spring-approve-pullrequest.png)

Depois da revisão terminar, o ramo de trabalho é mesclado para o seu ramo de base ao clicar o **Complete** botão. Pode optar por eliminar o ramo de trabalho depois foi intercalado. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Confirme no canto superior esquerdo que o pedido é marcado como **CONCLUÍDO**. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Quando voltar para o repositório ao abrigo **código**, é solicitado que tiver sido mudado para o ramo principal.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

Também pode utilizar os seguintes comandos do Git para intercalar o seu ramo de trabalho para o seu ramo de base e eliminar o ramo de trabalho após a mesclagem:

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)


 
## <a name="next-steps"></a>Passos Seguintes

[Executar tarefas de ciência de dados](execute-data-science-tasks.md) mostra como utilizar utilitários para concluir várias tarefas de ciência de dados comuns, como a exploração interativa de dados, análise de dados, relatórios e criação de um modelo.

Orientações passo a passo que demonstram todas as etapas do processo para **cenários específicos** também são fornecidas. Se estão listadas e estão associados ao descrições em miniatura da [instruções passo a passo do exemplo](walkthroughs.md) artigo. Eles ilustram como combinar a cloud, ferramentas no local e serviços num fluxo de trabalho ou um pipeline para criar uma aplicação inteligente. 

