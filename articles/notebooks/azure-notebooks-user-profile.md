---
title: Perfil do utilizador e ID para uso com pré-visualização de cadernos Azure
description: Como criar e gerir o seu perfil de utilizador e ID do utilizador com os Cadernos Azure, que se torna parte do URL dos cadernos partilhados.
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: d90eebf1b7b463e038bc5e54f51df0eb6ca746c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646284"
---
# <a name="your-profile-and-user-id-for-azure-notebooks-preview"></a>O seu perfil e ID do utilizador para pré-visualização de cadernos Azure

Dentro do poderoso espaço colaborativo dos Cadernos Azure, o seu perfil de utilizador apresenta a sua imagem pública a outros:

[![Uma página de perfil de Cadernos Azure](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

O seu ID de utilizador faz parte dos URLs que utiliza para partilhar projetos e cadernos. A seguinte lista descreve os diferentes padrões de URL:

- `https://notebooks.azure.com/<user_id>`: A sua página de perfil.
- `https://notebooks.azure.com/<user_id>/projects`: Os seus projetos. Você vê todos os projetos; outros utilizadores vêem apenas os seus projetos públicos.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: Ficheiros do projeto.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: Clones de um projeto específico.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: A pré-visualização HTML de um caderno ou ficheiro específico.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="your-user-id"></a>O seu ID de utilizador

Ao assinar pela primeira vez nos Cadernos Azure, a sua conta é automaticamente atribuída a um ID de utilizador temporário, como "anon-idr3ca". Desde que tenha uma identificação de utilizador que comece com "anon-", os Cadernos Azure solicitam-lhe que o altere sempre que iniciar a sua inscrição:

![Solicita-me que crie um ID do utilizador ao iniciar sessão nos Cadernos Azure](media/accounts/create-user-id.png)

Um comando de ID do **utilizador configurado** também aparece ao lado do nome de utilizador temporário:

![Configure o comando id do utilizador que aparece quando estiver a usar um ID temporário](media/accounts/configure-user-id-command.png)

Também pode alterar o seu ID de utilizador a qualquer momento na sua página de perfil.

O ID do utilizador deve ser composto por entre quatro a dezasseis letras, números e hífens. Nenhum outro caracteres é permitido, e o ID do utilizador não pode começar ou terminar com um hífen ou usar vários hífenes seguidos. Como as iDs dos utilizadores são únicas em todas as contas do Azure Notebooks, pode ver a mensagem: "O ID do utilizador já está a ser utilizado." (A mensagem também aparece se tentar utilizar uma marca microsoft como id de utilizador.) Nestes casos, escolha uma identificação de utilizador diferente.

> [!Important]
> Alterar o seu ID invalida quaisquer URLs que possa ter partilhado usando o seu ID anterior. Pode alterar o seu ID de volta para o seu ID anterior para revalidar os links. No entanto, é possível que outro utilizador reclame um ID não utilizado entretanto.

## <a name="your-profile"></a>O seu perfil

O seu perfil é composto por informações publicamente visualizadas no URL, `https://notebooks.azure.com/<user_id>`. A sua página de perfil também mostra os seus projetos recentemente utilizados e quaisquer projetos estrelados.

Para editar o seu perfil, utilize o comando de **Informação** de Perfil editar na sua página de perfil. As secções do seu perfil são as seguintes:

| Section | Conteúdos |
| --- | --- |
| Foto de perfil | Uma imagem que é mostrada na sua página de perfil. |
| Informações da Conta | O nome do seu display, identificação do utilizador e conta de e-mail pública. A conta de e-mail aqui fornece a outros utilizadores uma forma de contactá-lo e pode ser diferente da [conta](azure-notebooks-user-account.md) que usa para assinar nos próprios Cadernos Azure. |
| Informação de Perfil | A sua localização, empresa, cargo, site e uma breve descrição de si mesmo. |
| Perfis Sociais | O seu GItHub, Twitter e IDs do Facebook, se quiser partilhá-los. |
| Definições de Privacidade | Fornece dois comandos:<ul><li>**Export My Profile**: cria e descarrega um ficheiro *.zip* contendo todas as informações que os Cadernos Azure guardam no seu perfil, incluindo a sua fotografia, informações de perfil e registos de segurança.</li><li>**Delete My Account**: Elimina permanentemente todas as suas informações pessoais armazenadas em Cadernos Azure.</li></ul> |
| Ativar as funcionalidades do site | Permite-lhe controlar aspetos do comportamento dos Cadernos Azure:<ul><li>**Frontend unificado para cadernos**: permite uma startup de cadernomais rápida e uma maior persistência.</li><li>**Executado no JupyterLab por padrão**: Por padrão, os Notebooks Azure fornecem uma interface de utilizador simples que é adequada para a maioria dos utilizadores. O JupyterLab fornece uma interface mais rica, mas mais complicada para utilizadores experientes.</li><li>**VNext Website**: permite o layout web modernizado mostrado nesta documentação.</li></ul> |

## <a name="next-steps"></a>Passos seguintes  

> [!div class="nextstepaction"]
> [Tutorial: criar um caderno Jupyter para fazer regressão linear](tutorial-create-run-jupyter-notebook.md)
