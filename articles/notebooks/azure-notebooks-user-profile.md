---
title: Perfil de usuário e ID para utilização com blocos de notas do Azure
description: Como criar e gerir o perfil de utilizador e o ID de utilizador com blocos de notas do Azure.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 7d069d86-660f-4c94-b6e3-0c0f38c52d0e
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: 1fddefeb2a54ae775a9016799ffff1963eab247e
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970153"
---
# <a name="your-profile-and-user-id-for-azure-notebooks"></a>O ID de utilizador e de perfil para blocos de notas do Azure

Dentro do espaço de poderoso e colaboração de blocos de notas do Azure, o seu perfil de utilizador apresenta sua imagem pública a outras pessoas:

[página de perfil do ![An Azure Notebooks](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

O ID de utilizador faz parte dos URLs de usar para compartilhar projetos e blocos de notas. A lista seguinte descreve os diferentes padrões de URL:

- `https://notebooks.azure.com/<user_id>`: Sua página de perfil.
- `https://notebooks.azure.com/<user_id>/projects`: Seus projetos. Verá todos os projetos; outros utilizadores ver apenas os seus projetos públicos.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: Arquivos de projeto.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: Clones de projetos específicos.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: A visualização em HTML de um arquivo ou bloco de anotações específico.

## <a name="your-user-id"></a>O ID de utilizador

Quando iniciar sessão em blocos de notas do Azure pela primeira vez, a sua conta é atribuída automaticamente um ID de utilizador temporários, como "anon-idr3ca". Desde que tenha um ID de utilizador que começa com "anon-", blocos de notas do Azure pede-lhe para alterá-la sempre que iniciar sessão:

![Linha de comandos para criar um ID de utilizador ao iniciar sessão no Azure blocos de notas](media/accounts/create-user-id.png)

R **configurar o ID de utilizador** comando também é apresentado junto ao nome do utilizador temporário:

![Configurar comando de ID de utilizador que aparece quando estiver usando um ID de temporário](media/accounts/configure-user-id-command.png)

Também pode alterar o seu ID de utilizador em qualquer altura na página de perfil.

Uma ID de usuário deve ser composta entre quatro e dezesseis letras, números e hifens. Não existem outros carateres são permitidos, e o ID de utilizador não pode começar ou terminar com um hífen ou utilizar vários hífens seguidos. Como as IDs de usuário são exclusivas em todas as contas de Azure Notebooks, você pode ver a mensagem "a ID de usuário já está em uso". (A mensagem também será exibida se você tentar usar uma marca registrada da Microsoft como uma ID de usuário.) Nesses casos, escolha uma ID de usuário diferente.

> [!Important]
> Alterar o seu ID invalida todos os URLs talvez tenha partilhado usando sua ID anterior. Pode alterar o seu ID de volta para o seu ID anterior para revalide as ligações. No entanto, é possível que outro utilizador solicitar um não for utilizado o ID de nesse meio tempo.

## <a name="your-profile"></a>Seu perfil

Seu perfil é composto de informações podem ser exibidas publicamente no URL, `https://notebooks.azure.com/<user_id>`. Sua página de perfil também mostra seus projetos utilizados recentemente e todos os projetos marcados com estrela.

Para editar seu perfil, utilize o **editar informações de perfil** comando na sua página de perfil. As secções do seu perfil, são os seguintes:

| Section | Conteúdos |
| --- | --- |
| Fotografia de perfil | Uma imagem que é apresentada na página de perfil. |
| Informações sobre a Conta | O nome a apresentar, o ID de utilizador e a conta de e-mail pública. A conta de e-mail aqui fornece uma média para contactá-lo de outros utilizadores e pode ser diferente do [conta](azure-notebooks-user-account.md) pode iniciar sessão em blocos de notas do Azure em si. |
| Informações do Perfil | Sua localização, empresa, cargo, site da web e uma breve descrição por conta própria. |
| Perfis de redes sociais | O GItHub, Twitter e identificações no Facebook, caso queira partilhá-los. |
| Definições de Privacidade | Fornece dois comandos:<ul><li>**Exportar o meu perfil**: cria e transfere um *. zip* ficheiro que contém todas as informações que guarda de blocos de notas do Azure no seu perfil, incluindo a sua fotografia, informações de perfil e registos de segurança.</li><li>**Excluir minha conta**: Exclui permanentemente todas as suas informações pessoais armazenadas no Azure Notebooks.</li></ul> |
| Ativar funcionalidades do Site | Permite-lhe controlar os aspectos do comportamento de blocos de notas do Azure:<ul><li>**Unificação de front-end para blocos de notas**: permite a inicialização mais rápida do bloco de notas e persistência melhor.</li><li>**Executar no JupyterLab por padrão**: Por padrão, o Azure Notebooks fornece uma interface de usuário simples que é adequada para a maioria dos usuários. JupyterLab fornece uma interface mais rica, mais complicada, mas para usuários experientes.</li><li>**Web site de VNext**: permite que o layout de reestruturação web mostrado nesta documentação.</li></ul> |

## <a name="next-steps"></a>Passos Seguintes  

> [!div class="nextstepaction"]
> [Tutorial: criar uma execução de um bloco de notas do Jupyter para fazer a regressão linear](tutorial-create-run-jupyter-notebook.md)
