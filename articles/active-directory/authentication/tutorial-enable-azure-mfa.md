---
title: Ativar a autenticação de vários fatores Azure Ad
description: Neste tutorial, aprende-se a ativar a autenticação multi-factor Azure AD para um grupo de utilizadores e testar o pedido de fator secundário durante um evento de entrada.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62818ae5be079dc154e6d6faef4a8ebaae8fcd9d
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837877"
---
# <a name="tutorial-secure-user-sign-in-events-with-azure-ad-multi-factor-authentication"></a>Tutorial: Garantir eventos de entrada de utilizadores com autenticação multi-factor Azure AD

A autenticação multi-factor (MFA) é um processo em que um utilizador é solicitado durante um evento de inscrição para formas adicionais de identificação. Esta solicitação pode ser introduzir um código no telemóvel ou fornecer uma digitalização de impressões digitais. Quando se precisa de uma segunda forma de autenticação, a segurança é aumentada, uma vez que este fator adicional não é algo que seja fácil para um intruso obter ou duplicar.

As políticas de autenticação multi-factor AD Ad e acesso condicional conferem a flexibilidade para permitir mFA para os utilizadores durante eventos específicos de entrada.

> [!IMPORTANT]
> Este tutorial mostra a um administrador como ativar a autenticação multi-factor Azure AD.
>
> Se a sua equipa de TI não tiver ativado a capacidade de utilizar a Autenticação Multi-Factor AD Azure ou tiver problemas durante a sação, contacte o seu helpdesk para obter assistência adicional.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma política de acesso condicional para permitir a autenticação multi-factor Azure AD para um grupo de utilizadores
> * Configure as condições de política que solicitam para o MFA
> * Teste o processo de MFA como utilizador

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa dos seguintes recursos e privilégios:

* Um inquilino Azure AD em funcionamento com pelo menos um Azure AD Premium P1 ou uma licença de julgamento habilitada.
    * Se necessário, [crie um de graça.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Uma conta com privilégios *de administrador global.*
* Um utilizador não administrador com uma palavra-passe que conhece, como *testuser.* Você testa a experiência de autenticação multi-factor Azure AD do utilizador final usando esta conta neste tutorial.
    * Se precisar de criar um utilizador, consulte [Quickstart: Adicione novos utilizadores ao Azure Ative Directory](../fundamentals/add-users-azure-active-directory.md).
* Um grupo do qual o utilizador não administrador é membro, como o *MFA-Test-Group*. Você ativa a autenticação multi-factor Azure AD para este grupo neste tutorial.
    * Se precisar de criar um grupo, veja como [criar um grupo e adicione membros no Azure Ative Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="create-a-conditional-access-policy"></a>Criar uma política de acesso condicional

A forma recomendada de ativar e utilizar a autenticação multi-factor Azure AD é com políticas de Acesso Condicional. O Acesso Condicional permite criar e definir políticas que reagem a assinar em eventos e solicitar ações adicionais antes de um utilizador ter acesso a uma aplicação ou serviço.

![Resumo do diagrama de como o Acesso Condicional funciona para garantir o processo de inscrição](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

As políticas de Acesso Condicional podem ser granulares e específicas, com o objetivo de capacitar os utilizadores a serem produtivos onde e quando, mas também proteger a sua organização. Neste tutorial, vamos criar uma política básica de Acesso Condicional para solicitar para MFA quando um utilizador se inscreve no portal Azure. Num tutorial posterior nesta série, configura a autenticação multi-factor Azure AD utilizando uma política de acesso condicional baseada no risco.

Em primeiro lugar, crie uma política de acesso condicional e atribua o seu grupo de teste de utilizadores da seguinte forma:

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta com permissões *de administrador global.*
1. Procure e selecione **Azure Ative Directory,** em seguida, escolha **Segurança** no menu do lado esquerdo.
1. Selecione **Acesso Condicional,** em seguida, escolha **+ Nova política**.
1. Insira um nome para a apólice, como *MFA Pilot*.
1. Em **Atribuições**, escolha **Utilizadores e grupos,** em seguida, o **botão Select e grupos** de rádio.
1. Verifique a caixa para **Utilizadores e grupos,** em **seguida, selecione** para navegar nos utilizadores e grupos Azure AD disponíveis.
1. Navegue e selecione o seu grupo AD Azure, como *MFA-Test-Group,* em seguida, escolha **Select**.

    [![Selecione o seu grupo AD Azure para usar com a política ](media/tutorial-enable-azure-mfa/select-group-for-conditional-access-cropped.png) de acesso condicional](media/tutorial-enable-azure-mfa/select-group-for-conditional-access.png#lightbox)

1. Para aplicar a política de acesso condicional para o grupo, selecione **'Fazer'.**

## <a name="configure-the-conditions-for-multi-factor-authentication"></a>Configure as condições para a autenticação de vários fatores

Com a política de Acesso Condicional criada e um grupo de teste de utilizadores atribuídos, agora defina as aplicações ou ações na nuvem que desencadeiam a política. Estas aplicações ou ações em nuvem são os cenários que você decide requerem processamento adicional, como para solicitar para MFA. Por exemplo, pode decidir que o acesso a uma aplicação financeira ou a utilização de ferramentas de gestão requer como uma medida de verificação adicional.

Para este tutorial, configurar a política de Acesso Condicional para exigir MFA quando um utilizador se inscreve no portal Azure.

1. Selecione **Aplicações na cloud ou ações**. Pode optar por aplicar a política de acesso condicional a *todas as aplicações na nuvem* ou a escolher *aplicações.* Para proporcionar flexibilidade, também pode excluir certas aplicações da política.

    Para este tutorial, na página *Incluir,* escolha o botão de rádio **Select apps.**

1. Escolha **Selecione** e, em seguida, navegue na lista de eventos de inscrição disponíveis que podem ser utilizados.

    Para este tutorial, escolha **a Microsoft Azure Management** para que a política se aplique a eventos de inscrição no portal Azure.

1. Para aplicar as aplicações selecionadas, escolha **Selecione** e, em seguida, **Concluído**.

    ![Selecione a app Microsoft Azure Management para incluir na política de acesso condicional](media/tutorial-enable-azure-mfa/select-azure-management-app.png)

Os controlos de acesso permitem definir os requisitos para que um utilizador tenha acesso, como por exemplo, necessitar de uma aplicação de cliente aprovada ou usar um dispositivo que seja o Hybrid Azure AD. Neste tutorial, configurar os controlos de acesso para exigir MFA durante um evento de inscrição no portal Azure.

1. Nos *controlos de acesso,* escolha **Grant,** certifique-se de que o botão de rádio **de acesso Grant** está selecionado.
1. Verifique se a caixa é **autenticação de vários fatores** e, em seguida, escolha **Selecione**.

As políticas de Acesso Condicional só podem ser *definidas* para Reportar se quiser ver como a configuração impactaria os utilizadores, ou *Off* se não quiser a política de utilização agora. Como um grupo de teste de utilizadores foi direcionado para este tutorial, permite ativar a política e, em seguida, testar Azure AD Multi-Factor Authentication.

1. Desace a *política de ativação* para **on**.
1. Para aplicar a política de acesso condicional, selecione **Criar**.

## <a name="test-azure-ad-multi-factor-authentication"></a>Teste Azure Ad Multi-Factor Autenticação

Vamos ver a sua política de acesso condicional e autenticação multi-factor Azure AD em ação. Primeiro, inscreva-se num recurso que não requer MFA da seguinte forma:

1. Abra uma nova janela de navegador em modo InPrivate ou incógnito e navegue para [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)
1. Inscreva-se com o utilizador de teste não administrador, como *o testuser*. Não há nenhuma solicitação para completar a MFA.
1. Feche a janela do browser.

Agora inscreva-se no portal Azure. Como o portal Azure foi configurado na política de Acesso Condicional para exigir uma verificação adicional, obtém-se uma solicitação de autenticação multi-factor Azure AD.

1. Abra uma nova janela de navegador no modo InPrivate ou incógnito e navegue para [https://portal.azure.com](https://portal.azure.com) .
1. Inscreva-se com o utilizador de teste não administrador, como *o testuser*. É-lhe exigido que se registe e utilize a Autenticação Multi-Factor Azure AD. Siga as instruções para concluir o processo e verifique se insinou-se com sucesso no portal Azure.

    ![Siga as solicitações do navegador e, em seguida, na sua solicitação de autenticação multi-factor registada para iniciar sação](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

1. Feche a janela do browser.

## <a name="clean-up-resources"></a>Limpar os recursos

Se já não pretender utilizar a política de Acesso Condicional para ativar a autenticação multi-factor Ad Azure configurada como parte deste tutorial, elimine a política utilizando os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure e selecione **Azure Ative Directory,** em seguida, escolha **Segurança** no menu do lado esquerdo.
1. Selecione **o acesso condicional,** em seguida, escolha a política que criou, como *O Piloto de MFA*
1. Escolha **Eliminar** e, em seguida, confirme que deseja eliminar a apólice.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, permitiu a autenticação multi-factor Azure AD utilizando políticas de Acesso Condicional para um grupo selecionado de utilizadores. Aprendeu a:

> [!div class="checklist"]
> * Criar uma política de acesso condicional para permitir a autenticação multi-factor Azure AD para um grupo de utilizadores AD Azure
> * Configure as condições de política que solicitam para o MFA
> * Teste o processo de MFA como utilizador

> [!div class="nextstepaction"]
> [Ativar a gravação de palavra-passe para reset da palavra-passe de autosserviço (SSPR)](./tutorial-enable-sspr-writeback.md)