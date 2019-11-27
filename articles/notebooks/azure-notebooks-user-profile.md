---
title: Perfil de usuário e ID para utilização com blocos de notas do Azure
description: Como criar e gerenciar seu perfil de usuário e a ID de usuário com Azure Notebooks, que se torna parte da URL de blocos de anotações compartilhados.
ms.topic: article
ms.date: 02/25/2019
ms.openlocfilehash: 0874fe8223ae9c49ccfe4e8efedf2620117a0f8c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277614"
---
# <a name="your-profile-and-user-id-for-azure-notebooks"></a>O ID de utilizador e de perfil para blocos de notas do Azure

Dentro do espaço de poderoso e colaboração de blocos de notas do Azure, o seu perfil de utilizador apresenta sua imagem pública a outras pessoas:

[![uma página de perfil de Azure Notebooks](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

O ID de utilizador faz parte dos URLs de usar para compartilhar projetos e blocos de notas. A lista seguinte descreve os diferentes padrões de URL:

- `https://notebooks.azure.com/<user_id>`: sua página de perfil.
- `https://notebooks.azure.com/<user_id>/projects`: seus projetos. Verá todos os projetos; outros utilizadores ver apenas os seus projetos públicos.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: Arquivos de projeto.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: clones de projetos específicos.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: a visualização em HTML de um arquivo ou bloco de anotações específico.

## <a name="your-user-id"></a>O ID de utilizador

Quando iniciar sessão em blocos de notas do Azure pela primeira vez, a sua conta é atribuída automaticamente um ID de utilizador temporários, como "anon-idr3ca". Desde que tenha um ID de utilizador que começa com "anon-", blocos de notas do Azure pede-lhe para alterá-la sempre que iniciar sessão:

![Linha de comandos para criar um ID de utilizador ao iniciar sessão no Azure blocos de notas](media/accounts/create-user-id.png)

Um comando **Configurar ID de usuário** também aparece ao lado do nome de usuário temporário:

![Configurar comando de ID de utilizador que aparece quando estiver usando um ID de temporário](media/accounts/configure-user-id-command.png)

Também pode alterar o seu ID de utilizador em qualquer altura na página de perfil.

Uma ID de usuário deve ser composta entre quatro e dezesseis letras, números e hifens. Não existem outros carateres são permitidos, e o ID de utilizador não pode começar ou terminar com um hífen ou utilizar vários hífens seguidos. Como as IDs de usuário são exclusivas em todas as contas de Azure Notebooks, você pode ver a mensagem "a ID de usuário já está em uso". (A mensagem também será exibida se você tentar usar uma marca registrada da Microsoft como uma ID de usuário.) Nesses casos, escolha uma ID de usuário diferente.

> [!Important]
> Alterar o seu ID invalida todos os URLs talvez tenha partilhado usando sua ID anterior. Pode alterar o seu ID de volta para o seu ID anterior para revalide as ligações. No entanto, é possível que outro utilizador solicitar um não for utilizado o ID de nesse meio tempo.

## <a name="your-profile"></a>Seu perfil

Seu perfil é composto por informações publicamente visíveis na URL, `https://notebooks.azure.com/<user_id>`. Sua página de perfil também mostra seus projetos utilizados recentemente e todos os projetos marcados com estrela.

Para editar seu perfil, use o comando **Editar informações de perfil** em sua página de perfil. As secções do seu perfil, são os seguintes:

| Section | Conteúdos |
| --- | --- |
| Fotografia de perfil | Uma imagem que é apresentada na página de perfil. |
| Informações sobre a Conta | O nome a apresentar, o ID de utilizador e a conta de e-mail pública. A conta de email aqui fornece a outros usuários uma média para contatá-lo e pode ser diferente da [conta](azure-notebooks-user-account.md) usada para entrar Azure notebooks si mesmo. |
| Informações do Perfil | Sua localização, empresa, cargo, site da web e uma breve descrição por conta própria. |
| Perfis de redes sociais | O GItHub, Twitter e identificações no Facebook, caso queira partilhá-los. |
| Definições de Privacidade | Fornece dois comandos:<ul><li>**Exportar meu perfil**: cria e baixa um arquivo *. zip* contendo todas as informações que Azure notebooks salva em seu perfil, incluindo sua fotografia, informações de perfil e logs de segurança.</li><li>**Excluir minha conta**: exclui permanentemente todas as suas informações pessoais armazenadas no Azure notebooks.</li></ul> |
| Ativar funcionalidades do Site | Permite-lhe controlar os aspectos do comportamento de blocos de notas do Azure:<ul><li>**Front-end unificado para blocos de anotações**: permite uma inicialização mais rápida do notebook e melhor persistência.</li><li>**Executar no JupyterLab por padrão**: por padrão, o Azure notebooks fornece uma interface de usuário simples que é adequada para a maioria dos usuários. JupyterLab fornece uma interface mais rica, mais complicada, mas para usuários experientes.</li><li>**Site VNext**: habilita o layout da Web modernizado mostrado nesta documentação.</li></ul> |

## <a name="next-steps"></a>Passos seguintes  

> [!div class="nextstepaction"]
> [Tutorial: criar uma execução de um bloco de anotações Jupyter para fazer a regressão linear](tutorial-create-run-jupyter-notebook.md)
