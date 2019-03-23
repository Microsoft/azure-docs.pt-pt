---
title: 'Início Rápido: Reposição personalizada de palavra-passe do Azure AD'
description: Neste início rápido, vai configurar rapidamente a reposição personalizada de palavra-passe do Azure AD para permitir aos utilizadores repor as respetivas palavras-passe
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58e3254d499e013dc686bf6b7d53f919a457c901
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371289"
---
# <a name="quickstart-self-service-password-reset"></a>Início rápido: Reposição personalizada de palavra-passe

Neste início rápido, vai percorrer a configuração da reposição personalizada de palavra-passe (SSPR) como uma forma simples de os administradores de TI permitirem aos utilizadores repor as palavras-passe ou desbloquear as contas.

## <a name="prerequisites"></a>Pré-requisitos

* Um inquilino do Azure AD em funcionamento com, pelo menos, uma licença de avaliação ativada.
* Uma conta com privilégios de Administrador Global.
* Um utilizador de teste não-administrador com uma palavra-passe, sabe, se precisar de criar uma utilizador, consulte o artigo [início rápido: Adicionar novos utilizadores ao Azure Active Directory](../add-users-azure-active-directory.md).
* Um grupo piloto para testar do qual o utilizador de teste não administrador seja membro. Se precisar de criar um grupo, veja o artigo [Criar um grupo e adicionar membros no Azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Ativar a reposição de palavras-passe self-service

> [!VIDEO https://www.youtube.com/embed/Pa0eyqjEjvQ]

1. No seu inquilino existente do Azure AD, no **portal do Azure**, em **Azure Active Directory**, selecione **Reposição de palavra-passe**.

2. Na página **Propriedades**, na opção **Reposição Personalizada de Palavra-passe Ativada**, selecione **Selecionado**.
    * Em **Grupo**, selecione o grupo piloto criado como parte da secção de pré-requisitos deste artigo.
    * Clique em **Guardar**.

3. Na página **Métodos de autenticação**, selecione o seguinte:
   * Número de métodos necessários para repor: **1**
   * Métodos disponíveis para os utilizadores:
      * **E-mail**
      * **Código de aplicação móvel (pré-visualização)**
   * Clique em **Guardar**.

     ![Escolher métodos de autenticação para SSPR][Authentication]

4. Na página **Registo**, selecione as seguintes opções:
   * Exigir que os utilizadores se registem quando iniciam sessão: **Sim**
   * Defina o número de dias antes de ser pedido aos utilizadores que voltem as informações de autenticação a: **365**

## <a name="test-self-service-password-reset"></a>Testar a reposição personalizada de palavra-passe

Agora, vamos testar a configuração SSPR com um utilizador de teste. Desde que a Microsoft aplica requisitos de autenticação fortes às contas de administrador do Azure, um teste efetuado com uma conta de administrador pode alterar o resultado. Para obter mais informações sobre a política de palavras-passe de administrador, veja o nosso artigo sobre a [política de palavras-passe](concept-sspr-policy.md).

1. Abra uma nova janela de browser no modo InPrivate ou incógnito e navegue para [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
2. Inicie sessão com um utilizador de teste não administrador e registe o seu telefone de autenticação.
3. Quando tiver terminado, clique no botão marcado com **parece bem** e feche a janela do browser.
4. Abra uma nova janela de browser no modo InPrivate ou incógnito e navegue para [https://aka.ms/sspr](https://aka.ms/sspr).
5. Introduza o ID de Utilizador dos utilizadores de teste não administradores, os carateres do CAPTCHA e, em seguida, clique em **Seguinte**.
6. Siga os passos de verificação para repor a palavra-passe

## <a name="clean-up-resources"></a>Limpar recursos

É fácil desativar a reposição de palavras-passe self-service. Abra o seu inquilino do Azure AD e aceda à **propriedades** > **reposição de palavra-passe**e, em seguida, selecione **None** em **reposição personalizada de palavra-passe Ativado**.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu a configurar a reposição personalizada de palavra-passe para os utilizadores apenas da cloud. Para obter informações sobre como concluir uma implementação mais detalhada, avance para o nosso guia de implementação.

> [!div class="nextstepaction"]
> [Implementar a reposição personalizada de palavra-passe](howto-sspr-deployment.md)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "Métodos de autenticação do Azure AD disponíveis e a quantidade necessária"
