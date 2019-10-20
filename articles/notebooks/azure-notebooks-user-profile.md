---
title: Perfil de usuário e ID para uso com Azure Notebooks
description: Como criar e gerenciar seu perfil de usuário e a ID de usuário com Azure Notebooks, que se torna parte da URL de blocos de anotações compartilhados.
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
ms.openlocfilehash: 3985e3d5fbe6e24014694665557f13bcc9a35a25
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598215"
---
# <a name="your-profile-and-user-id-for-azure-notebooks"></a>Seu perfil e ID de usuário para Azure Notebooks

No espaço eficiente e colaborativo de Azure Notebooks, seu perfil de usuário apresenta sua imagem pública para outras pessoas:

[página de perfil do ![An Azure Notebooks](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

Sua ID de usuário faz parte das URLs que você usa para compartilhar projetos e notebooks. A lista a seguir descreve os diferentes padrões de URL:

- `https://notebooks.azure.com/<user_id>`: sua página de perfil.
- `https://notebooks.azure.com/<user_id>/projects`: seus projetos. Você vê todos os projetos; outros usuários veem apenas seus projetos públicos.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: Arquivos de projeto.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: clones de projetos específicos.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: a visualização em HTML de um arquivo ou bloco de anotações específico.

## <a name="your-user-id"></a>Sua ID de usuário

Ao entrar Azure Notebooks pela primeira vez, sua conta recebe automaticamente uma ID de usuário temporária, como "Anon-idr3ca". Contanto que você tenha uma ID de usuário que comece com "Anon-", Azure Notebooks solicita que você a altere sempre que entrar:

![Solicitar a criação de uma ID de usuário ao fazer logon no Azure Notebooks](media/accounts/create-user-id.png)

Um comando **Configurar ID de usuário** também aparece ao lado do nome de usuário temporário:

![Configurar o comando de ID de usuário que aparece quando você está usando uma ID temporária](media/accounts/configure-user-id-command.png)

Você também pode alterar sua ID de usuário a qualquer momento em sua página de perfil.

Uma ID de usuário deve ser composta entre quatro e dezesseis letras, números e hifens. Nenhum outro caractere é permitido e a ID de usuário não pode começar ou terminar com um hífen ou usar vários hifens em uma linha. Como as IDs de usuário são exclusivas em todas as contas de Azure Notebooks, você pode ver a mensagem "a ID de usuário já está em uso". (A mensagem também será exibida se você tentar usar uma marca registrada da Microsoft como uma ID de usuário.) Nesses casos, escolha uma ID de usuário diferente.

> [!Important]
> Alterar sua ID invalida as URLs que você pode ter compartilhado usando sua ID anterior. Você pode alterar sua ID de volta para a ID anterior para revalidar os links. No entanto, é possível que outro usuário solicite uma ID não usada enquanto isso.

## <a name="your-profile"></a>Seu perfil

Seu perfil é composto por informações publicamente visíveis na URL, `https://notebooks.azure.com/<user_id>`. Sua página de perfil também mostra seus projetos usados recentemente e todos os projetos estrelado.

Para editar seu perfil, use o comando **Editar informações de perfil** em sua página de perfil. As seções do seu perfil são as seguintes:

| Section | Conteúdos |
| --- | --- |
| Foto do perfil | Uma imagem que é mostrada em sua página de perfil. |
| Informações da conta | Seu nome de exibição, ID de usuário e conta de email pública. A conta de email aqui fornece a outros usuários uma média para contatá-lo e pode ser diferente da [conta](azure-notebooks-user-account.md) usada para entrar Azure notebooks si mesmo. |
| Informações de perfil | Seu local, empresa, cargo, site e uma breve descrição de você mesmo. |
| Perfis sociais | Suas IDs do GItHub, do Twitter e do Facebook, se você quiser compartilhá-las. |
| Configurações de privacidade | Fornece dois comandos:<ul><li>**Exportar meu perfil**: cria e baixa um arquivo *. zip* contendo todas as informações que Azure notebooks salva em seu perfil, incluindo sua fotografia, informações de perfil e logs de segurança.</li><li>**Excluir minha conta**: exclui permanentemente todas as suas informações pessoais armazenadas no Azure notebooks.</li></ul> |
| Habilitar recursos do site | Permite que você controle aspectos do comportamento do Azure Notebooks:<ul><li>**Front-end unificado para blocos de anotações**: permite uma inicialização mais rápida do notebook e melhor persistência.</li><li>**Executar no JupyterLab por padrão**: por padrão, o Azure notebooks fornece uma interface de usuário simples que é adequada para a maioria dos usuários. O JupyterLab fornece uma interface mais sofisticada, mas mais complicada para usuários experientes.</li><li>**Site VNext**: habilita o layout da Web modernizado mostrado nesta documentação.</li></ul> |

## <a name="next-steps"></a>Passos seguintes  

> [!div class="nextstepaction"]
> [Tutorial: criar uma execução de um bloco de anotações Jupyter para fazer a regressão linear](tutorial-create-run-jupyter-notebook.md)
