---
title: Ativar a Multi-Factor Authentication do Azure
description: Neste tutorial, aprende-se a ativar a Autenticação Multi-Factor Azure para um grupo de utilizadores e a testar o pedido de fator secundário durante um evento de início de sessão.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/11/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 253eb23be03c1cc0f2abf4ad1fed734426dc287d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77154824"
---
# <a name="tutorial-secure-user-sign-in-events-with-azure-multi-factor-authentication"></a>Tutorial: Eventos de inscrição de utilizador seguros com autenticação de multi-factor Azure

A autenticação multifactor (MFA) é um processo em que um utilizador é solicitado durante um evento de inscrição para formas adicionais de identificação. Esta solicitação pode ser introduzir um código no telemóvel ou fornecer uma digitalização de impressões digitais. Quando se exige uma segunda forma de autenticação, a segurança é aumentada, uma vez que este fator adicional não é algo que seja fácil para um intruso obter ou duplicar.

As políticas de autenticação e acesso condicional do Azure Multi-Factor dão flexibilidade para permitir o MFA aos utilizadores durante eventos específicos de inscrição.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma política de acesso condicional para permitir a autenticação multi-factor do Azure para um grupo de utilizadores
> * Configure as condições de política que solicitam o MFA
> * Testar o processo de MFA como utilizador

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, necessita dos seguintes recursos e privilégios:

* Um inquilino Azure AD em funcionamento com Azure AD Premium ou licença de teste habilitada.
    * Se necessário, [crie um de graça.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Uma conta com privilégios *de administrador global.*
* Um utilizador não administrador com uma palavra-passe que conheça, como *testuser*. Testa a experiência de autenticação multifactor do utilizador final Azure utilizando esta conta neste tutorial.
    * Se precisar de criar um utilizador, consulte [Quickstart: Adicione novos utilizadores ao Diretório Ativo Do Azure](../add-users-azure-active-directory.md).
* Um grupo do que o utilizador não administrador é membro, como o *MFA-Test-Group*. Ativa a Autenticação Multi-Factor Azure para este grupo neste tutorial.
    * Se precisa de criar um grupo, veja como [criar um grupo e adicione membros no Azure Ative Directory](../active-directory-groups-create-azure-portal.md).

## <a name="create-a-conditional-access-policy"></a>Criar uma política de acesso condicional

A forma recomendada de ativar e utilizar a autenticação de multi-factores Azure é com políticas de acesso condicional. O Acesso Condicional permite-lhe criar e definir políticas que reagem a assinar eventos e solicitar ações adicionais antes de um utilizador ter acesso a uma aplicação ou serviço.

![Diagrama de como o Acesso Condicional funciona para garantir o processo de inscrição](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

As políticas de Acesso Condicional podem ser granulares e específicas, com o objetivo de capacitar os utilizadores a serem produtivos onde e quando quiserem, mas também proteger a sua organização. Neste tutorial, vamos criar uma política básica de Acesso Condicional para solicitar o MFA quando um utilizador se inscreve no portal Azure. Num tutorial posterior desta série, configura a Autenticação Multi-Factor Azure utilizando uma política de acesso condicional baseada no risco.

Em primeiro lugar, crie uma política de Acesso Condicional e atribua o seu grupo de teste de utilizadores da seguinte forma:

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta com permissões *de administrador global.*
1. Procure e selecione **Azure Ative Directory,** em seguida, escolha **segurança** do menu do lado esquerdo.
1. Selecione **Acesso Condicional**e, em seguida, escolha **+ Nova política**.
1. Insira um nome para a apólice, como *MFA Pilot*.
1. Em **Atribuições,** escolha **Utilizadores e grupos,** em seguida, os utilizadores **Selecionados e agrupa** o botão de rádio.
1. Verifique a caixa para **Utilizadores e grupos,** em **seguida, Selecione** para navegar nos utilizadores e grupos de Anúncios Azure disponíveis.
1. Procure e selecione o seu grupo Azure AD, como o *MFA-Test-Group,* e depois escolha **Select**.

    [![](media/tutorial-enable-azure-mfa/select-group-for-conditional-access-cropped.png "Select your Azure AD group to use with the Conditional Access policy")](media/tutorial-enable-azure-mfa/select-group-for-conditional-access.png#lightbox)

1. Para aplicar a política de Acesso Condicional para o grupo, selecione **Done**.

## <a name="configure-the-conditions-for-multi-factor-authentication"></a>Configure as condições para a autenticação de vários fatores

Com a política de Acesso Condicional criada e um grupo de teste de utilizadores atribuídos, agora defina as aplicações ou ações na nuvem que desencadeiam a política. Estas aplicações ou ações na nuvem são os cenários que decide requerem processamento adicional, como para solicitar o MFA. Por exemplo, pode decidir que o acesso a uma aplicação financeira ou a utilização de ferramentas de gestão requer como um pedido de verificação adicional.

Para este tutorial, configure a política de Acesso Condicional para exigir MFA quando um utilizador entrar no portal Azure.

1. Selecione **Aplicações na cloud ou ações**. Pode optar por aplicar a política de Acesso Condicional a *todas as aplicações na nuvem* ou selecionar *aplicações.* Para proporcionar flexibilidade, também pode excluir certas aplicações da política.

    Para este tutorial, na página *Incluir,* escolha o botão de rádio **Select apps.**

1. Escolha **Selecionar**e, em seguida, navegue na lista de eventos de entrada disponíveis que possam ser utilizados.

    Para este tutorial, escolha a **Microsoft Azure Management** para que a política se aplique a eventos de inscrição no portal Azure.

1. Para aplicar as aplicações selecionadas, escolha **Select**, em **seguida, Feito**.

    ![Selecione a app Microsoft Azure Management para incluir na política de acesso condicional](media/tutorial-enable-azure-mfa/select-azure-management-app.png)

Os controlos de acesso permitem definir os requisitos para que um utilizador tenha acesso, como a necessidade de uma aplicação de cliente aprovada ou a utilização de um dispositivo que seja a AD Hybrid Azure. Neste tutorial, configure os controlos de acesso para exigir MFA durante um evento de entrada no portal Azure.

1. Sob *os controlos de acesso,* escolha **Grant,** em seguida, certifique-se de que o botão de rádio **de acesso Grant** é selecionado.
1. Verifique se a caixa **requer a autenticação de vários fatores**e, em seguida, escolha **Selecionar**.

As políticas de Acesso Condicional só podem ser definidas para *reportar* se quiser ver como a configuração afetaria os utilizadores, ou *Off* se não quiser a política de utilização neste momento. Como um grupo de teste de utilizadores foi direcionado para este tutorial, permite ativar a política e, em seguida, testar a autenticação azure multi-factor.

1. Desative a *política ativa* para **o toggle**on . .
1. Para aplicar a política de Acesso Condicional, selecione **Criar**.

## <a name="test-azure-multi-factor-authentication"></a>Testar Multi-Factor Authentication do Azure

Vamos ver a sua política de acesso condicional e autenticação multi-factor Azure em ação. Primeiro, inscreva-se num recurso que não requer MFA da seguinte forma:

1. Abra uma nova janela de navegador em modo InPrivate ou incógnito e navegue para[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)
1. Inscreva-se com o utilizador do teste não administrador, como *testuser*. Não há nenhuma solicitação para completar a MFA.
1. Feche a janela do browser.

Agora inscreva-se no portal Azure. Como o portal Azure foi configurado na política de Acesso Condicional para exigir uma verificação adicional, obtém-se um pedido de autenticação Multi-Factor Azure.

1. Abra uma nova janela de navegador no modo InPrivate ou incógnito e navegue para [https://portal.azure.com](https://portal.azure.com).
1. Inscreva-se com o utilizador do teste não administrador, como *testuser*. É-lhe exigido que se registe e utilize a Autenticação Multi-Factor Azure. Siga as instruções para completar o processo e verifique se insere com sucesso no portal Azure.

    ![Siga as solicitações do navegador e, em seguida, no seu pedido de autenticação de vários fatores registado para iniciar sessão](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

1. Feche a janela do browser.

## <a name="clean-up-resources"></a>Limpar recursos

Se já não pretender utilizar a política de acesso condicional para permitir a autenticação multi-factor Azure configurada como parte deste tutorial, elimine a política utilizando os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure e selecione **Azure Ative Directory,** em seguida, escolha **segurança** do menu do lado esquerdo.
1. Selecione **acesso Condicional**e, em seguida, escolha a política que criou, como *mfa pilot*
1. Escolha **Apagar**e, em seguida, confirme que pretende eliminar a política.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, permitiu a autenticação multi-factor azure utilizando políticas de acesso condicional para um grupo selecionado de utilizadores. Aprendeu a:

> [!div class="checklist"]
> * Criar uma política de acesso condicional para permitir a autenticação de multi-factores azure para um grupo de utilizadores de Anúncios Azure
> * Configure as condições de política que solicitam o MFA
> * Testar o processo de MFA como utilizador

> [!div class="nextstepaction"]
> [Ativar a reescrita da palavra-passe para redefinir palavra-passe self-service (SSPR)](tutorial-enable-writeback.md)
