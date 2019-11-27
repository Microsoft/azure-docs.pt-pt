---
title: Entrar para blocos de notas do Azure
description: Iniciar sessão em blocos de notas do Azure e defina um ID de utilizador, o que lhe dá a capacidade de acessar projetos guardados e partilhar os blocos de notas com outras pessoas rapidamente.
ms.topic: quickstart
ms.date: 04/15/2019
ms.openlocfilehash: 234703da9662380a59c7673ca3b34a1b01b7dbef
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277446"
---
# <a name="quickstart-sign-in-and-set-a-user-id"></a>Início rápido: Iniciar sessão e defina um ID de utilizador

Embora possa exibir sempre o blocos de notas do Azure sem iniciar sessão, tem de iniciar sessão executar blocos de notas, acessar projetos guardados e blocos de notas e partilhar os seus blocos de notas com outras pessoas.

## <a name="sign-in"></a>Iniciar sessão

1. Selecione **entrar** no canto superior direito de [notebooks.Azure.com](https://notebooks.azure.com/).

    ![Localização do comando de início de sessão em blocos de notas do Azure](media/accounts/sign-in-command.png)

1. Quando solicitado, insira o endereço de email de uma conta da Microsoft ou uma conta corporativa ou de estudante e selecione **Avançar**. Os tipos de conta são descritos em [sua conta de usuário para Azure notebooks](azure-notebooks-user-account.md). Se você não tiver uma conta da Microsoft ou desejar fazer uma para uso especificamente com Azure Notebooks, selecione **criar uma**:

    ![Criar novo comando de conta da Microsoft no início de sessão de linha de comandos](media/accounts/create-new-microsoft-account.png)

    > [!Tip]
    > Se você tentar criar uma nova conta com um endereço de email que já tenha uma conta associada a ela, você poderá ver a mensagem "não é possível inscrever-se aqui com um endereço de email corporativo ou de estudante. Use um email pessoal, como Gmail ou Yahoo!, ou obtenha um novo email do Outlook. Nesse caso, tente entrar com o endereço de email de trabalho sem criar uma nova conta.

1. Introduza a palavra-passe quando lhe for pedido.

1. Se estiver a iniciar sessão pela primeira vez, os blocos de notas do Azure pede permissão aceder à sua conta. Selecione **Sim** para continuar:

    ![Linha de comandos de permissões de conta](media/accounts/account-permission-prompt.png)

## <a name="set-a-user-id"></a>Defina um ID de utilizador

1. Após o primeiro início de sessão, estiver atribuído um ID de utilizador temporários, como "anon-idrca3". Sempre que tem um ID de utilizador que começa com "anon-", blocos de notas do Azure pede-lhe para criar um ID de seus próprios. O ID de utilizador é utilizado em qualquer URL que obtém para partilhar os seus projetos e blocos de notas, por isso, escolha algo que seja exclusivo e significativo para.

    ![Pedido para introduzir um ID de utilizador para blocos de notas do Azure](media/accounts/create-user-id.png)

    Se você selecionar **não, obrigado**, Azure notebooks continuará solicitando uma ID de usuário cada vez que você entrar. Sua ID de usuário também pode ser definida a qualquer momento no seu [perfil de usuário](azure-notebooks-user-profile.md).

1. Depois de entrar com êxito, Azure Notebooks navega para sua página de perfil público, na qual você pode selecionar **Editar informações de perfil** para preencher o restante de suas informações (para obter mais informações, consulte [seu perfil e ID de usuário](azure-notebooks-user-profile.md)):

    ![Vista inicial de uma página de perfil de blocos de notas do Azure](media/accounts/profile-page-new.png)

> [!NOTE]
> Se você vir a mensagem "a ID de usuário já está em uso", tente uma ID diferente. As IDs de usuário são exclusivas em todas as contas de Azure Notebooks e Azure Notebooks também reserva determinadas IDs de usuário, como nomes de marca da Microsoft.

## <a name="sign-out"></a>Terminar sessão

Para sair, selecione seu nome de usuário no canto superior direito da página e, em seguida, selecione **sair**:

![Localização do comando de fim de sessão em blocos de notas do Azure](media/accounts/sign-out-command.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Início rápido: criar e compartilhar um bloco de anotações](quickstart-create-share-jupyter-notebook.md)
