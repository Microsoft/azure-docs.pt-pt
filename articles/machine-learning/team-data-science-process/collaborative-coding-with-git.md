---
title: Codificação colaborativa com Git - Processo de Ciência de Dados de Equipa
description: Como fazer desenvolvimento colaborativo de códigos para projetos de ciência de dados usando Git com planeamento ágil.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ca24a781f4f3ad5c210813dabbb896de35056ed6
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588714"
---
# <a name="collaborative-coding-with-git"></a>Codificação em colaboração com Git

Este artigo descreve como usar o Git como o quadro colaborativo de desenvolvimento de códigos para projetos de ciência de dados. O artigo abrange como ligar o código em Azure Repos a itens de trabalho [de desenvolvimento ágil](agile-development.md) em Azure Boards, como fazer revisões de código, e como criar e fundir pedidos de pull para alterações.

## <a name="link-a-work-item-to-an-azure-repos-branch"></a><a name='Linkaworkitemwithagitbranch-1'></a>Ligue um item de trabalho a um ramo Azure Repos 

A Azure DevOps fornece uma maneira conveniente de ligar um item de trabalho de utilizador ou história de tarefa da Azure Boards com um ramo de repositório Azure Repos Git. Pode ligar a sua História ou Tarefa do Utilizador diretamente ao código que lhe está associado. 

Para ligar um item de trabalho a um novo ramo, selecione a elipse **de Ações** **(...**) ao lado do item de trabalho, e no menu de contexto, percorra e selecione **Novo ramo**.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

No Criar um diálogo **de ramo,** forneça o novo nome do ramo e a base Azure Repos Git repositório e ramo. O repositório de base deve estar no mesmo projeto Azure DevOps que o item de trabalho. O ramo base pode ser qualquer ramo existente. Selecione **Criar ramo**. 

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Também pode criar um novo ramo utilizando o seguinte comando de bash Git no Windows ou Linux:

```bash
git checkout -b <new branch name> <base branch name>

```
Se não especificar \<base branch name> um, o novo ramo baseia-se `main` em . 

Para mudar para o seu ramo de trabalho, executar o seguinte comando: 

```bash
git checkout <working branch name>
```

Depois de mudar para o ramo de trabalho, pode começar a desenvolver artefactos de código ou documentação para completar o artigo de trabalho. Correr `git checkout main` troca-te de volta para o `main` ramo.

É uma boa prática criar um ramo Git para cada item de trabalho de User Story. Em seguida, para cada item de trabalho de tarefa, pode criar um ramo baseado na sucursal de User Story. Organize os balcões numa hierarquia que corresponda à relação Story-Task utilizador quando tem várias pessoas a trabalhar em diferentes Histórias de Utilizador para o mesmo projeto, ou em diferentes Tarefas para a mesma História do Utilizador. Pode minimizar conflitos fazendo com que cada membro da equipa trabalhe num ramo diferente, ou em códigos diferentes ou outros artefactos ao partilhar um ramo. 

O diagrama a seguir mostra a estratégia de ramificação recomendada para a TDSP. Você pode não precisar de tantos ramos como mostrado aqui, especialmente quando apenas uma ou duas pessoas trabalham em um projeto, ou apenas uma pessoa trabalha em todas as Tarefas de uma História de Utilizador. Mas separar o ramo de desenvolvimento do ramo primário é sempre uma boa prática, e pode ajudar a evitar que o ramo de libertação seja interrompido por atividades de desenvolvimento. Para obter uma descrição completa do modelo do ramo Git, consulte [Um Modelo de Ramificação Git Bem sucedido.](https://nvie.com/posts/a-successful-git-branching-model/)

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Também pode ligar um item de trabalho a um ramo existente. Na página **Detalhe** de um item de trabalho, selecione **Adicionar link**. Em seguida, selecione um ramo existente para ligar o item de trabalho e selecione **OK**. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

## <a name="work-on-the-branch-and-commit-changes"></a><a name='WorkonaBranchandCommittheChanges-2'></a>Trabalhar no ramo e cometer alterações 

Depois de fazer uma alteração para o seu item de trabalho, como adicionar um ficheiro de script R à filial da sua máquina `script` local, pode comprometer a mudança da sua filial local para o ramo de trabalho a montante, utilizando os seguintes comandos de bash Git:

```bash
git status
git add .
git commit -m "added an R script file"
git push origin script
```

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## <a name="create-a-pull-request"></a><a name='CreateapullrequestonVSTS-3'></a>Criar um pedido Pull

Depois de um ou mais compromissos e impulsos, quando estiver pronto para fundir o seu atual ramo de trabalho no seu ramo base, pode criar e submeter um *pedido de atração* em Azure Repos. 

A partir da página principal do seu projeto Azure DevOps, aponte para **os pedidos de**  >  **Repos Pull** na navegação à esquerda. Em seguida, selecione qualquer um dos botões de pedido de **puxar novo,** ou o link **de pedido de puxar.**

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

No ecrã **New Pull Request,** se necessário, navegue para o repositório git e ramo em que pretende fundir as suas alterações. Adicione ou altere qualquer outra informação que pretenda. Em **Comentários**, adicione os nomes dos revisores e, em seguida, selecione **Create**. 

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## <a name="review-and-merge"></a><a name='ReviewandMerge-4'></a>Rever e fundir

Assim que criar o pedido de retirada, os seus revisores recebem uma notificação de e-mail para rever o pedido de retirada. Os revisores testam se as alterações funcionam e verificam as alterações com o solicitador, se possível. Os revisores podem fazer comentários, solicitar alterações e aprovar ou rejeitar o pedido de pull com base na sua avaliação. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

Depois de os revisores aprovarem as alterações, você ou outra pessoa com permissões de fusão podem fundir o ramo de trabalho para o seu ramo base. Selecione **Complete** e, em seguida, selecione **Complete merge** in the **Complete pull request request** dialog. Pode optar por eliminar o ramo de trabalho depois de se ter fundido. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Confirme se o pedido está marcado como **CONCLUÍDO**. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Quando voltas para **Repos** na navegação à esquerda, podes ver que foste mudado para o ramo principal desde que o `script` ramo foi apagado.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

Também pode utilizar os seguintes comandos de bash Git para fundir o `script` ramo de trabalho no seu ramo base e eliminar o ramo de trabalho após a fusão:

```bash
git checkout main
git merge script
git branch -d script
```

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)

## <a name="next-steps"></a>Passos seguintes

[Executar tarefas de ciência de dados](execute-data-science-tasks.md) mostra como usar utilitários para completar várias tarefas comuns de ciência de dados, tais como exploração interativa de dados, análise de dados, relatórios e criação de modelos.

[Exemplo de passagem de imagens](walkthroughs.md) lista os passos de cenários específicos, com links e descrições de miniaturas. Os cenários ligados ilustram como combinar ferramentas e serviços de nuvem e no local em fluxos de trabalho ou oleodutos para criar aplicações inteligentes. 

