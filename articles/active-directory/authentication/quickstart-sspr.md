---
title: Início rápido-redefinição de senha de autoatendimento do Azure AD
description: Neste guia de início rápido, você aprende a configurar a redefinição de senha de autoatendimento do Azure AD para permitir que os usuários redefinam suas próprias senhas e reduza a sobrecarga do departamento de ti.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: quickstart
ms.date: 12/10/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd178f52665c77f03a48d87a9e73c9019390bb21
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154861"
---
# <a name="quickstart-configure-azure-active-directory-self-service-password-reset"></a>Início rápido: configurar Azure Active Directory redefinição de senha de autoatendimento

Neste guia de início rápido, você configura o SSPR (redefinição de senha de autoatendimento) do Azure Active Directory (AD) para permitir que os usuários redefinam suas senhas ou desbloqueiem suas contas. Com o SSPR, os usuários podem redefinir suas próprias credenciais sem assistência técnica ou administrador. Essa capacidade permite que os usuários recuperem o acesso à sua conta sem esperar por suporte adicional.

> [!IMPORTANT]
> Este guia de início rápido mostra um administrador como habilitar a redefinição de senha de autoatendimento. Se você for um usuário final já registrado para redefinição de senha de autoatendimento e precisar voltar à sua conta, vá para https://aka.ms/sspr.
>
> Se sua equipe de ti não tiver habilitado a capacidade de redefinir sua própria senha, entre em contato com sua assistência técnica para obter assistência adicional.

## <a name="prerequisites"></a>Pré-requisitos

* Um inquilino do Azure AD em funcionamento com, pelo menos, uma licença de avaliação ativada.
    * Se necessário, [crie um gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Uma conta com privilégios de Administrador Global.
* Um usuário de teste que não seja administrador com uma senha que você conhece, como *testuser*.
    * Se você precisar criar um usuário, consulte [início rápido: adicionar novos usuários a Azure Active Directory](../add-users-azure-active-directory.md).
* Um grupo piloto para testar com o fato de o usuário de teste não administrador ser membro do, como *SSPR-Test-Group*.
    * Se você precisar criar um grupo, consulte como [criar um grupo e adicionar membros em Azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Ativar a reposição de palavras-passe self-service

[Exibir este processo como um vídeo no YouTube](https://youtu.be/Pa0eyqjEjvQ)

1. No menu portal do Azure ou na **Home** Page, selecione **Azure Active Directory** e, em seguida, escolha **redefinição de senha**.

1. Na página **Propriedades** , na opção para **redefinição de senha de autoatendimento habilitada**, escolha **selecionado**.
1. Escolha **selecionar grupo**e, em seguida, selecione o grupo piloto criado como parte da seção pré-requisitos deste artigo, como *SSPR-Test-Group*. Quando estiver pronto, selecione **salvar**.
1. Na página **métodos de autenticação** , faça as seguintes escolhas e, em seguida, escolha **salvar**:
    * Número de métodos necessários para a reposição: **1**
    * Métodos disponíveis para os utilizadores:
        * **Código do aplicativo móvel**
        * **E-mail**

    > [!div class="mx-imgBorder"]
    > ![Escolhendo métodos de autenticação para SSPR][Authentication]

4. Na página de **registro** , faça as seguintes escolhas e, em seguida, escolha **salvar**:
   * Exigir que os utilizadores se registem quando iniciam sessão: **Sim**
   * Definir o número de dias antes de ser pedido aos utilizadores que voltem a confirmar as informações de autenticação: **365**

## <a name="test-self-service-password-reset"></a>Testar a reposição personalizada de palavra-passe

Agora, vamos testar a configuração do SSPR com um usuário de teste que faz parte do grupo selecionado na seção anterior, como *testuser*. Desde que a Microsoft aplica requisitos de autenticação fortes às contas de administrador do Azure, um teste efetuado com uma conta de administrador pode alterar o resultado. Para obter mais informações sobre a política de palavras-passe de administrador, veja o nosso artigo sobre a [política de palavras-passe](concept-sspr-policy.md).

1. Abra uma nova janela de browser no modo InPrivate ou incógnito e navegue para [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
2. Entre com um usuário de teste que não seja administrador, como *testuser*e Registre seu telefone de autenticação.
3. Depois de concluído, selecione o botão marcado **parece bom** e feche a janela do navegador.
4. Abra uma nova janela de browser no modo InPrivate ou incógnito e navegue para [https://aka.ms/sspr](https://aka.ms/sspr).
5. Insira a ID de usuário dos seus usuários de teste que não são administradores, como *testuser*, os caracteres do captcha e, em seguida, selecione **Avançar**.
6. Siga as etapas de verificação para redefinir sua senha.

## <a name="clean-up-resources"></a>Limpar recursos

Para desabilitar a redefinição de senha de autoatendimento, procure e selecione **Azure Active Directory** na portal do Azure. Selecione **redefinição de senha**e escolha **nenhuma** em **redefinição de senha de autoatendimento habilitada**. Quando estiver pronto, selecione **salvar**.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a configurar a redefinição de senha de autoatendimento para seus usuários somente de nuvem. Para obter informações sobre como concluir uma implementação mais detalhada, avance para o nosso guia de implementação.

> [!div class="nextstepaction"]
> [Implementar a reposição personalizada de palavra-passe](howto-sspr-deployment.md)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "Métodos de autenticação do Azure AD disponíveis e a quantidade necessária"

<!-- INTERNAL LINKS -->
[register-sspr]: ../user-help/active-directory-passwords-reset-register.md
[reset-password]: ../user-help/active-directory-passwords-update-your-own-password.md
