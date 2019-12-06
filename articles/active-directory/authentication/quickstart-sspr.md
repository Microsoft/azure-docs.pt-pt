---
title: 'Início Rápido: Reposição personalizada de palavra-passe do Azure AD'
description: Neste início rápido, vai configurar rapidamente a reposição personalizada de palavra-passe do Azure AD para permitir aos utilizadores repor as respetivas palavras-passe
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 582a6a32aa4c34b2e6fef37f3de5b5414de16cf3
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846662"
---
# <a name="quickstart-self-service-password-reset"></a>Início Rápido: Reposição personalizada de palavra-passe

Neste início rápido, vai percorrer a configuração da reposição personalizada de palavra-passe (SSPR) como uma forma simples de os administradores de TI permitirem aos utilizadores repor as palavras-passe ou desbloquear as contas.

## <a name="prerequisites"></a>Pré-requisitos

* Um inquilino do Azure AD em funcionamento com, pelo menos, uma licença de avaliação ativada.
* Uma conta com privilégios de Administrador Global.
* Um utilizador de teste não administrador com uma palavra-passe que conheça. Se precisar de criar um utilizador, veja o artigo [Início Rápido: Adicionar novos utilizadores ao Azure Active Directory](../add-users-azure-active-directory.md).
* Um grupo piloto para testar do qual o utilizador de teste não administrador seja membro. Se precisar de criar um grupo, veja o artigo [Criar um grupo e adicionar membros no Azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Ativar a reposição de palavras-passe self-service

[Exibir este processo como um vídeo no YouTube](https://youtu.be/Pa0eyqjEjvQ)

1. No seu locatário existente do Azure AD, no menu portal do Azure ou na **Home** Page, selecione **Azure Active Directory**. Em seguida, selecione **redefinição de senha**.

2. Na página **Propriedades**, na opção **Reposição Personalizada de Palavra-passe Ativada**, selecione **Selecionado**.
    * Em **Grupo**, selecione o grupo piloto criado como parte da secção de pré-requisitos deste artigo.
    * Clique em **Guardar**.

3. Na página **Métodos de autenticação**, selecione o seguinte:
   * Número de métodos necessários para a reposição: **1**
   * Métodos disponíveis para os utilizadores:
      * **E-mail**
      * **Código do aplicativo móvel (visualização)**
   * Clique em **Guardar**.

     ![Escolhendo métodos de autenticação para SSPR][Authentication]

4. Na página **Registo**, selecione as seguintes opções:
   * Exigir que os utilizadores se registem quando iniciam sessão: **Sim**
   * Definir o número de dias antes de ser pedido aos utilizadores que voltem a confirmar as informações de autenticação: **365**

## <a name="test-self-service-password-reset"></a>Testar a reposição personalizada de palavra-passe

Agora, vamos testar a configuração SSPR com um utilizador de teste. Desde que a Microsoft aplica requisitos de autenticação fortes às contas de administrador do Azure, um teste efetuado com uma conta de administrador pode alterar o resultado. Para obter mais informações sobre a política de palavras-passe de administrador, veja o nosso artigo sobre a [política de palavras-passe](concept-sspr-policy.md).

1. Abra uma nova janela de browser no modo InPrivate ou incógnito e navegue para [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
2. Inicie sessão com um utilizador de teste não administrador e registe o seu telefone de autenticação.
3. Quando tiver terminado, clique no botão marcado com **parece bem** e feche a janela do browser.
4. Abra uma nova janela de browser no modo InPrivate ou incógnito e navegue para [https://aka.ms/sspr](https://aka.ms/sspr).
5. Introduza o ID de Utilizador dos utilizadores de teste não administradores, os carateres do CAPTCHA e, em seguida, clique em **Seguinte**.
6. Siga os passos de verificação para repor a palavra-passe

## <a name="clean-up-resources"></a>Limpar recursos

É fácil desativar a reposição de palavras-passe self-service. Abra seu locatário do Azure AD e vá para **propriedades** > **redefinição de senha**e selecione **nenhum** na **redefinição de senha de autoatendimento habilitada**.

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a configurar a reposição personalizada de palavra-passe para os utilizadores apenas da cloud. Para obter informações sobre como concluir uma implementação mais detalhada, avance para o nosso guia de implementação.

> [!div class="nextstepaction"]
> [Implementar a reposição personalizada de palavra-passe](howto-sspr-deployment.md)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "Métodos de autenticação do Azure AD disponíveis e a quantidade necessária"
