---
title: Codificação colaborativa com git - Processo de Ciência de Dados da Equipa
description: Como fazer o desenvolvimento colaborativo de códigos para projetos de ciência de dados usando Git com planeamento ágil.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 0708e395eff90ff5b889c05f0fd5e7a98205c5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721902"
---
# <a name="collaborative-coding-with-git"></a>Codificação em colaboração com Git

Este artigo descreve como usar Git como o quadro colaborativo de desenvolvimento de códigos para projetos de ciência de dados. O artigo abrange como ligar código em Azure Repos a itens de trabalho de [desenvolvimento ágeis](agile-development.md) em Placas Azure, como fazer revisões de códigos e como criar e fundir pedidos de alterações.

## <a name="link-a-work-item-to-an-azure-repos-branch"></a><a name='Linkaworkitemwithagitbranch-1'></a>Ligue um item de trabalho a uma sucursal de Azure Repos 

A Azure DevOps fornece uma forma conveniente de ligar um artigo de trabalho de utilizador ou tarefa de Placas Azure com uma filial de repositório Azure Repos Git. Pode ligar a sua História de Utilizador ou Tarefa diretamente ao código associado ao mesmo. 

Para ligar um item de trabalho a um novo ramo, selecione as **Ações** elipsis (**...**) ao lado do item de trabalho, e no menu de contexto, percorra e selecione **Nova filial.**  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

No diálogo **Criar um ramo,** forneça o novo nome do ramo e o repositório e ramo de base Azure Repos Git. O repositório base deve estar no mesmo projeto Azure DevOps que o item de trabalho. O ramo base pode ser o ramo principal ou outro ramo existente. Selecione **Criar ramo**. 

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Também pode criar um novo ramo utilizando o seguinte comando git bash no Windows ou Linux:

```bash
git checkout -b <new branch name> <base branch name>

```
Se não especificar um \<nome base>, o novo `master`ramo baseia-se em . 

Para mudar para o seu ramo de trabalho, execute o seguinte comando: 

```bash
git checkout <working branch name>
```

Depois de mudar para o ramo de trabalho, pode começar a desenvolver artefactos de código ou documentação para completar o item de trabalho. Correr `git checkout master` muda-te de `master` volta para o ramo.

É uma boa prática criar uma filial Git para cada item de trabalho da User Story. Em seguida, para cada item de trabalho de Tarefa, pode criar um ramo baseado no ramo User Story. Organize os ramos numa hierarquia que corresponda à relação User Story-Task quando tiver várias pessoas a trabalhar em diferentes Histórias de Utilizadores para o mesmo projeto, ou em diferentes Tarefas para a mesma História do Utilizador. Você pode minimizar conflitos fazendo com que cada membro da equipa trabalhe em um ramo diferente, ou em código diferente ou outros artefactos ao partilhar um ramo. 

O diagrama seguinte mostra a estratégia de ramificação recomendada para a TDSP. Você pode não precisar de tantos ramos como mostrado aqui, especialmente quando apenas uma ou duas pessoas trabalham num projeto, ou apenas uma pessoa trabalha em todas as Tarefas de uma História de Utilizador. Mas separar o ramo de desenvolvimento do ramo principal é sempre uma boa prática, e pode ajudar a evitar que o ramo de libertação seja interrompido por atividades de desenvolvimento. Para obter uma descrição completa do modelo git branch, consulte Um modelo de [ramificação Git bem sucedido](https://nvie.com/posts/a-successful-git-branching-model/).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Também pode ligar um item de trabalho a um ramo existente. Na página **de Detalhes** de um item de trabalho, selecione **Adicionar link**. Em seguida, selecione um ramo existente para ligar o item de trabalho e selecione **OK**. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

## <a name="work-on-the-branch-and-commit-changes"></a><a name='WorkonaBranchandCommittheChanges-2'></a>Trabalhar no ramo e cometer alterações 

Depois de fazer uma alteração para o seu item de trabalho, `script` como adicionar um ficheiro de script R à filial da sua máquina local, pode comprometer a mudança da sua filial local para o ramo de trabalho a montante utilizando os seguintes comandos git bash:

```bash
git status
git add .
git commit -m "added an R script file"
git push origin script
```

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## <a name="create-a-pull-request"></a><a name='CreateapullrequestonVSTS-3'></a>Criar um pedido Pull

Depois de um ou mais compromissos e empurrões, quando estiver pronto para fundir o seu atual ramo de trabalho no seu ramo base, pode criar e submeter um pedido de *pull* em Azure Repos. 

A partir da página principal do seu projeto Azure DevOps, aponte para pedidos **de Repos** > **Pull** na navegação à esquerda. Em seguida, selecione um dos botões de pedido de **puxar novo,** ou o link Criar um pedido de **puxar.**

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

No ecrã **New Pull Request,** se necessário, navegue para o repositório Git e branch em que pretende fundir as suas alterações. Adicione ou altere qualquer outra informação que desejar. Em **Revisores,** adicione os nomes dos revisores e, em seguida, selecione **Criar**. 

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## <a name="review-and-merge"></a><a name='ReviewandMerge-4'></a>Rever e fundir

Assim que criar o pedido de pull, os seus revisores recebem uma notificação de e-mail para rever o pedido de pull. Os revisores testam se as alterações funcionam e verificam as alterações com o solicitador, se possível. Os revisores podem fazer comentários, solicitar alterações e aprovar ou rejeitar o pedido de retirada com base na sua avaliação. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

Depois de os revisores aprovarem as alterações, você ou outra pessoa com permissões de fusão pode fundir o ramo de trabalho com o seu ramo base. Selecione **Complete**, e, em seguida, selecione **Complete fundir** no diálogo de pedido de **puxar completo.** Pode optar por eliminar o ramo de trabalho depois de se ter fundido. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Confirme que o pedido está marcado como **CONCLUÍDO**. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Quando voltar a **Repos** na navegação esquerda, pode ver que foi mudado para `script` o ramo principal desde que o ramo foi apagado.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

Também pode utilizar os seguintes comandos `script` git bash para fundir o ramo de trabalho no seu ramo base e eliminar o ramo de trabalho após a fusão:

```bash
git checkout master
git merge script
git branch -d script
```

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)

## <a name="next-steps"></a>Passos seguintes

[Executar tarefas de ciência](execute-data-science-tasks.md) de dados mostra como usar utilitários para completar várias tarefas comuns de ciência de dados, tais como a exploração interativa de dados, análise de dados, relatórios e criação de modelos.

[Os walkthroughs de exemplo](walkthroughs.md) listam caminhadas de cenários específicos, com links e descrições de miniaturas. Os cenários ligados ilustram como combinar ferramentas e serviços em fluxos de trabalho ou oleodutos para criar aplicações inteligentes. 

