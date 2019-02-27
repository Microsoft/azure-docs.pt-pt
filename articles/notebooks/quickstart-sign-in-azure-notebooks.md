---
title: Entrar para blocos de notas do Azure
description: Iniciar sessão em blocos de notas do Azure e defina um ID de utilizador, o que lhe dá a capacidade de acessar projetos guardados e partilhar os blocos de notas com outras pessoas rapidamente.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: fb8c94b1-6d0a-4b77-8d14-ae6efcdd99f4
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: 4daae4357a7a9577c48edc8643355d319b701944
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56881571"
---
# <a name="quickstart-sign-in-and-set-a-user-id"></a>Início rápido: Iniciar sessão e definir um ID de utilizador

Embora possa exibir sempre o blocos de notas do Azure sem iniciar sessão, tem de iniciar sessão executar blocos de notas, acessar projetos guardados e blocos de notas e partilhar os seus blocos de notas com outras pessoas.

## <a name="sign-in"></a>Iniciar sessão

1. Selecione **iniciar sessão** no canto superior direito da [notebooks.azure.com](https://notebooks.azure.com/).

    ![Localização do comando de início de sessão em blocos de notas do Azure](media/accounts/sign-in-command.png)

1. Quando lhe for pedido, introduza o endereço de e-mail de uma Account Microsoft ou um trabalho ou conta da instituição de ensino e selecione **seguinte**. Tipos de conta estão descritos no [sua conta de utilizador para blocos de notas do Azure](azure-notebooks-user-account.md). Se não tiver uma Account Microsoft ou pretende efetuar um para utilização especificamente com blocos de notas do Azure, selecione **criá-lo**:

    ![Criar novo comando de conta da Microsoft no início de sessão de linha de comandos](media/accounts/create-new-microsoft-account.png)

1. Introduza a palavra-passe quando lhe for pedido.

1. Se estiver a iniciar sessão pela primeira vez, os blocos de notas do Azure pede permissão aceder à sua conta. Selecione **Sim** para continuar:

    ![Linha de comandos de permissões de conta](media/accounts/account-permission-prompt.png)

## <a name="set-a-user-id"></a>Defina um ID de utilizador

1. Após o primeiro início de sessão, estiver atribuído um ID de utilizador temporários, como "anon-idrca3". Sempre que tem um ID de utilizador que começa com "anon-", blocos de notas do Azure pede-lhe para criar um ID de seus próprios. O ID de utilizador é utilizado em qualquer URL que obtém para partilhar os seus projetos e blocos de notas, por isso, escolha algo que seja exclusivo e significativo para.

    ![Pedido para introduzir um ID de utilizador para blocos de notas do Azure](media/accounts/create-user-id.png)

    Se selecionou **obrigado não**, blocos de notas do Azure continua a solicitar-lhe um ID de utilizador cada vez que iniciar sessão. O utilizador ID também pode ser definido em qualquer altura no seu [perfil de utilizador](azure-notebooks-user-profile.md).

1. Depois de iniciar sessão com êxito na, blocos de notas do Azure, navega para sua página de perfil público, no qual pode selecionar **editar informações de perfil** para preencher o restante das informações do utilizador (para obter mais informações, consulte [seu perfil ID de utilizador e](azure-notebooks-user-profile.md)):

    ![Vista inicial de uma página de perfil de blocos de notas do Azure](media/accounts/profile-page-new.png)

> [!NOTE]
> Se vir a mensagem, "ID de utilizador já está em utilização," tente outro ID. IDs de utilizador são exclusivos em todas as contas de blocos de notas do Azure e blocos de notas do Azure também se reserva determinados IDs de utilizador, tais como nomes de marca de Microsoft.

## <a name="sign-out"></a>Terminar sessão

Para terminar sessão, selecione o seu nome de utilizador no canto superior direito da página, em seguida, selecione **terminar sessão**:

![Localização do comando de fim de sessão em blocos de notas do Azure](media/accounts/sign-out-command.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Quickstart: Criar e partilhar um bloco de notas](quickstart-create-share-jupyter-notebook.md)
