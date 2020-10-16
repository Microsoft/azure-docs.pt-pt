---
title: Perfil do utilizador e ID para uso com pré-visualização de cadernos Azure
description: Como criar e gerir o seu perfil de utilizador e iD do utilizador com os Azure Notebooks, que se torna parte do URL dos cadernos partilhados.
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: 9a1ff7f92faec21f537f068f0a33473700ddfed8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85831357"
---
# <a name="your-profile-and-user-id-for-azure-notebooks-preview"></a>O seu perfil e iD de utilizador para pré-visualização de cadernos Azure

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Dentro do poderoso espaço colaborativo dos Cadernos Azure, o seu perfil de utilizador apresenta a sua imagem pública a outros:

[![Uma página de perfil de Azure Notebooks](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

O seu ID de utilizador faz parte dos URLs que utiliza para partilhar projetos e cadernos. A lista a seguir descreve os diferentes padrões de URL:

- `https://notebooks.azure.com/<user_id>`: A sua página de perfil.
- `https://notebooks.azure.com/<user_id>/projects`: Os seus projetos. Vê todos os projetos; outros utilizadores vêem apenas os seus projetos públicos.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: Ficheiros de projeto.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: Clones de projetos específicos.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: A pré-visualização HTML de um caderno ou ficheiro específico.

## <a name="your-user-id"></a>O seu ID de utilizador

Ao iniciar sessão em Azure Notebooks pela primeira vez, a sua conta é automaticamente atribuída a um ID de utilizador temporário, como "anon-idr3ca". Desde que tenha um ID do utilizador que comece com "anon-", os Cadernos Azure solicitam-lhe que o altere sempre que iniciar sedumento:

![Solicitação para criar um ID do utilizador ao iniciar sessão em Azure Notebooks](media/accounts/create-user-id.png)

Um comando **de identificação do utilizador configurado** também aparece ao lado do nome de utilizador temporário:

![Configure o comando de ID do utilizador que aparece quando está a usar um ID temporário](media/accounts/configure-user-id-command.png)

Também pode alterar o seu ID do utilizador a qualquer momento na sua página de perfil.

Um ID do utilizador deve ser composto entre quatro a dezasseis letras, números e hífenes. Não são permitidos outros caracteres e o ID do utilizador não pode começar ou terminar com um hífen ou usar vários hífenes seguidos. Como os IDs dos utilizadores são únicos em todas as contas de Azure Notebooks, pode ver a mensagem: "O ID do utilizador já está a ser utilizado". (A mensagem também aparece se tentar utilizar uma marca da Microsoft como ID do utilizador.) Nestes casos, escolha um ID de utilizador diferente.

> [!Important]
> Alterar o seu ID invalida quaisquer URLs que possa ter partilhado usando o seu ID anterior. Pode alterar o seu ID de volta para o seu ID anterior para revalidar os links. No entanto, é possível que outro utilizador reclame um ID não uusado entretanto.

## <a name="your-profile"></a>O seu perfil

O seu perfil é composto por informações publicamente visualizais no URL, `https://notebooks.azure.com/<user_id>` . A sua página de perfil também mostra os seus projetos recentemente utilizados e quaisquer projetos estrelados.

Para editar o seu perfil, utilize o comando **de Informação de Perfil de Edição** na sua página de perfil. As secções do seu perfil são as seguintes:

| Section | Conteúdos |
| --- | --- |
| Foto de perfil | Uma imagem que é mostrada na sua página de perfil. |
| Informações da Conta | O seu nome de exibição, ID do utilizador e conta de e-mail pública. A conta de e-mail aqui fornece a outros utilizadores uma média para entrar em contato com você e pode ser diferente da [conta](azure-notebooks-user-account.md) que você usa para assinar em Azure Notebooks em si. |
| Informação de Perfil | A sua localização, empresa, título de emprego, site e uma breve descrição de si mesmo. |
| Perfis Sociais | Os seus IDs GItHub, Twitter e Facebook, se quiser partilhá-los. |
| Definições de Privacidade | Fornece dois comandos:<ul><li>**Export My Profile**: cria e descarrega um ficheiro *.zip* contendo todas as informações que o Azure Notebooks guarda no seu perfil, incluindo a sua fotografia, informações de perfil e registos de segurança.</li><li>**Delete My Account**: Elimina permanentemente todas as suas informações pessoais armazenadas nos Cadernos Azure.</li></ul> |
| Ativar funcionalidades do site | Permite-lhe controlar aspetos do comportamento dos Cadernos Azure:<ul><li>**Frontend Unificado para Cadernos**: permite uma startup de caderno mais rápida e uma melhor persistência.</li><li>**Executado no JupyterLab por padrão**: Por padrão, os Blocos de Notas Azure fornecem uma interface de utilizador simples que é adequada para a maioria dos utilizadores. O JupyterLab oferece uma interface mais rica mas complicada para utilizadores experientes.</li><li>**Website VNext**: permite o layout web modernizado mostrado nesta documentação.</li></ul> |

## <a name="next-steps"></a>Passos seguintes  

> [!div class="nextstepaction"]
> [Tutorial: criar e executar um caderno Jupyter para fazer regressão linear](tutorial-create-run-jupyter-notebook.md)
