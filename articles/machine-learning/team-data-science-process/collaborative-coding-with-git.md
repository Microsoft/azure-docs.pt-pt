---
title: Codificação em colaboração com Git - Team Data Science Process
description: Como fazer o desenvolvimento de código de colaboração para projetos de ciência de dados com o Git com o planejamento ágil.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 08/23/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 3b57621fcec654f11c8e9a68e4568f332dbf9ac6
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195361"
---
# <a name="collaborative-coding-with-git"></a>Codificação em colaboração com Git

Este artigo descreve como usar o Git como a estrutura de desenvolvimento de código colaborativa para projetos de ciência de dados. O artigo aborda como vincular código em Azure Repos aos itens de trabalho de [desenvolvimento Agile](agile-development.md) no Azure boards, como fazer revisões de código e como criar e mesclar solicitações pull para alterações.

## <a name='Linkaworkitemwithagitbranch-1'></a>Vincular um item de trabalho a uma ramificação Azure Repos 

O Azure DevOps fornece uma maneira conveniente de conectar um Azure Boards uma história de usuário ou um item de trabalho de tarefa com uma ramificação de repositório git Azure Repos. Você pode vincular sua história de usuário ou tarefa diretamente ao código associado a ela. 

Para conectar um item de trabalho a uma nova ramificação, selecione as reticências de **ações** ( **...** ) ao lado do item de trabalho e, no menu de contexto, role para e selecione **novo Branch**.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

Na caixa de diálogo **criar uma ramificação** , forneça o nome da nova ramificação e a base Azure Repos repositório e ramificação git. O repositório base deve estar no mesmo projeto DevOps do Azure que o item de trabalho. A ramificação base pode ser a ramificação mestre ou outra ramificação existente. Selecione **criar ramificação**. 

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Você também pode criar uma nova ramificação usando o seguinte comando git bash no Windows ou Linux:

```bash
git checkout -b <new branch name> <base branch name>

```
Se você não especificar um \<nome de Branch base >, a nova ramificação será `master`baseada em. 

Para alternar para o Branch de trabalho, execute o seguinte comando: 

```bash
git checkout <working branch name>
```

Depois de alternar para o Branch de trabalho, você pode começar a desenvolver o código ou artefatos de documentação para concluir o item de trabalho. Executar `git checkout master` o comutador de volta `master` para a ramificação.

É uma boa prática criar uma ramificação git para cada item de trabalho de história de usuário. Em seguida, para cada item de trabalho de tarefa, você pode criar uma ramificação com base na ramificação de história de usuário. Organize as ramificações em uma hierarquia que corresponde à relação de tarefa de história de usuário quando você tem várias pessoas trabalhando em histórias de usuário diferentes para o mesmo projeto ou em tarefas diferentes para a mesma história de usuário. Você pode minimizar conflitos fazendo com que cada membro da equipe trabalhe em uma ramificação diferente ou em código diferente ou em outros artefatos ao compartilhar uma ramificação. 

O diagrama a seguir mostra a estratégia de ramificação recomendada para TDSP. Talvez você não precise de tantas ramificações quantas forem mostradas aqui, especialmente quando apenas uma ou duas pessoas trabalham em um projeto ou apenas uma pessoa trabalha em todas as tarefas de uma história de usuário. Mas separar a ramificação de desenvolvimento da ramificação mestre é sempre uma boa prática e pode ajudar a impedir que o Branch de lançamento seja interrompido pelas atividades de desenvolvimento. Para obter uma descrição completa do modelo de ramificação git, consulte [um modelo de ramificação git bem-sucedido](https://nvie.com/posts/a-successful-git-branching-model/).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Também pode associar um item de trabalho a uma ramificação existente. Na página de **detalhes** de um item de trabalho, selecione **Adicionar link**. Em seguida, selecione um Branch existente para vincular o item de trabalho e selecione **OK**. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

## <a name='WorkonaBranchandCommittheChanges-2'></a>Trabalhar no Branch e confirmar as alterações 

Depois de fazer uma alteração para seu item de trabalho, como adicionar um arquivo de script R à ramificação do `script` computador local, você pode confirmar a alteração do Branch local para o Branch de trabalho upstream usando os seguintes comandos do git bash:

```bash
git status
git add .
git commit -m "added an R script file"
git push origin script
```

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## <a name='CreateapullrequestonVSTS-3'></a>Criar uma solicitação de pull

Após uma ou mais confirmações e envios por push, quando estiver pronto para mesclar seu Branch de trabalho atual em sua ramificação base, você poderá criar e enviar uma *solicitação de pull* no Azure repos. 

Na página principal do seu projeto DevOps do Azure, aponte para**solicitações de pull** do **repositórios** > no painel de navegação esquerdo. Em seguida, selecione um dos novos botões de **solicitação de pull** ou o link **criar uma solicitação de pull** .

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

Na tela **nova solicitação de pull** , se necessário, navegue até o repositório git e Branch para os quais você deseja mesclar suas alterações. Adicione ou altere outras informações que desejar. Emrevisores, adicione os nomes dos que você precisa para revisar suas alterações e, em seguida, selecione **criar**. 

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## <a name='ReviewandMerge-4'></a>Revisão e intercalação

Depois de criar a solicitação de pull, seus revisores receberão uma notificação por email para examinar a solicitação de pull. Os revisores testam se as alterações funcionam e verificam as alterações com o solicitante, se possível. Os revisores podem fazer comentários, solicitar alterações e aprovar ou rejeitar a solicitação de pull com base em sua avaliação. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

Depois que os revisores aprovarem as alterações, você ou outra pessoa com permissões de mesclagem podem mesclar a ramificação de trabalho com sua ramificação base. Selecione **concluir**e, em seguida, selecione **mesclagem completa** na caixa de diálogo **concluir solicitação de pull** . Você pode optar por excluir o Branch de trabalho depois que ele tiver sido mesclado. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Confirme se a solicitação está marcada como **concluída**. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Ao voltar para **repositórios** no painel de navegação esquerdo, você pode ver que foi alternado para o Branch mestre desde que a `script` ramificação foi excluída.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

Você também pode usar os seguintes comandos do git bash para mesclar o Branch de `script` trabalho para sua ramificação base e excluir o Branch de trabalho após a mesclagem:

```bash
git checkout master
git merge script
git branch -d script
```

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)

## <a name="next-steps"></a>Passos seguintes

[Executar tarefas de ciência de dados](execute-data-science-tasks.md) mostra como usar utilitários para concluir várias tarefas comuns de ciência de dados, como exploração interativa de dados, análise de dados, relatórios e criação de modelo.

[Exemplos de orientações passo](walkthroughs.md) a passo de cenários específicos, com descrições de miniaturas e links. Os cenários vinculados ilustram como combinar ferramentas e serviços de nuvem e locais em fluxos de trabalho ou pipelines para criar aplicativos inteligentes. 

