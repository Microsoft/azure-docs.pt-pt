---
title: Proteção de inscrição de utilizadores baseada em risco no Azure Ative Directory
description: Neste tutorial, aprende-se a permitir a Proteção de Identidade Azure para proteger os utilizadores quando for detetado um comportamento de acesso de risco na sua conta.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97aabfa39954aa6ba937166eb54c05ac4874ea7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96741343"
---
# <a name="tutorial-use-risk-detections-for-user-sign-ins-to-trigger-azure-ad-multi-factor-authentication-or-password-changes"></a>Tutorial: Utilize deteções de risco para iniciar alterações de autenticação ou palavra-passe Azure AD

Para proteger os seus utilizadores, pode configurar políticas baseadas no risco no Azure Ative Directory (Azure AD) que respondem automaticamente a comportamentos de risco. As políticas de Proteção de Identidade Azure AD podem bloquear automaticamente uma tentativa de entrada ou exigir ações adicionais, tais como exigir uma alteração de palavra-passe ou um pedido de autenticação multi-factor Azure. Estas políticas funcionam com as políticas de acesso condicionado Azure AD existentes como uma camada extra de proteção para a sua organização. Os utilizadores podem nunca desencadear um comportamento de risco numa destas políticas, mas a sua organização está protegida se for feita uma tentativa de comprometer a sua segurança.

> [!IMPORTANT]
> Este tutorial mostra a um administrador como ativar a autenticação multi-factor Azure AD baseada no risco.
>
> Se a sua equipa de TI não tiver ativado a capacidade de utilizar a Autenticação Multi-Factor AD Azure ou tiver problemas durante a sação, contacte o seu helpdesk para obter assistência adicional.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Compreender as políticas disponíveis para a Azure AD Identity Protection
> * Ativar o registo de autenticação multi-factor Azure AD
> * Permitir alterações de palavra-passe baseadas em risco
> * Ativar a Multi-Factor Authentication baseada em risco
> * Testar políticas baseadas no risco para tentativas de inscrição do utilizador

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa dos seguintes recursos e privilégios:

* Um inquilino Azure AD em funcionamento com pelo menos um Azure AD Premium P2 ou uma licença de julgamento habilitada.
    * Se necessário, [crie um de graça.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Uma conta com privilégios *de Administrador Global.*
* Azure AD configurado para reset de senha de autosserviço e autenticação multi-factor AD Azure
    * Se necessário, [complete o tutorial para ativar a Azure AD SSPR](tutorial-enable-sspr.md).
    * Se necessário, [complete o tutorial para ativar a autenticação multi-factor Azure AD](tutorial-enable-azure-mfa.md).

## <a name="overview-of-azure-ad-identity-protection"></a>Visão geral da Proteção de Identidade AD AZure

Todos os dias, a Microsoft recolhe e analisa triliões de sinais anonimizados como parte das tentativas de inscrição do utilizador. Estes sinais ajudam a construir padrões de bom comportamento de inscrição do utilizador e a identificar potenciais tentativas de inserção de risco. A Azure AD Identity Protection pode rever as tentativas de inscrição do utilizador e tomar medidas adicionais se houver comportamento suspeito:

Algumas das seguintes ações podem desencadear a deteção de riscos de proteção de identidade Azure AD:

* Utilizadores com credenciais vazadas.
* Insuposições de endereços IP anónimos.
* Viagem impossível para locais atípicos.
* Entradas de dispositivos infetados.
* Insuposições de endereços IP com atividade suspeita.
* Entradas de locais desconhecidos.

As três políticas seguintes estão disponíveis na Azure AD Identity Protection para proteger os utilizadores e responder a atividades suspeitas. Pode optar por ligar ou desligar a aplicação da política, selecionar utilizadores ou grupos para a política a aplicar e decidir se pretende bloquear o acesso no início de sação ou solicitar uma ação adicional.

* Política de risco do utilizador
    * Identifica e responde a contas de utilizador que podem ter credenciais comprometidas. Pode solicitar ao utilizador que crie uma nova palavra-passe.
* Assine na política de risco
    * Identifica e responde a tentativas suspeitas de inscrição. Pode solicitar ao utilizador que forneça formas adicionais de verificação utilizando a autenticação multi-factor Azure AD.
* Política de registo na MFA
    * Certifique-se de que os utilizadores estão registados para autenticação multi-factor Azure AD. Se uma política de risco de inscrição for solicitada para MFA, o utilizador já deve estar registado para autenticação multi-factor Azure AD.

Quando ativar um utilizador de política ou assinar na política de risco, também pode escolher o limiar para o nível de risco - *baixo e superior*, médio e *superior*, ou *alto*. Esta flexibilidade permite-lhe decidir o quão agressivo quer ser na aplicação de quaisquer controlos para eventos suspeitos de inscrição.

Para obter mais informações sobre a Proteção de Identidade AZure AD, veja [o que é a Proteção de Identidade AZure AD?](../identity-protection/overview-identity-protection.md)

## <a name="enable-mfa-registration-policy"></a>Ativar a política de registo do MFA

A Azure AD Identity Protection inclui uma política predefinida que pode ajudar a obter os utilizadores registados para a autenticação multi-factor AD Azure. Se utilizar políticas adicionais para proteger eventos de inscrição, precisaria que os utilizadores já se tivessem registado para MFA. Quando ativa esta política, não requer que os utilizadores realizem MFA em cada evento de inscrição. A apólice apenas verifica o estado de registo de um utilizador e pede-lhes que se registem se necessário.

Recomenda-se que a política de registo do MFA seja ativada para políticas adicionais de Proteção de Identidade Azure AD. Para ativar esta política, complete os seguintes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de administrador global.
1. Procure e selecione **Azure Ative Directory**, selecione **Segurança**, em seguida, no título do menu *Protect* escolha **Proteção de Identidade**.
1. Selecione a política de **registo de MFA** a partir do menu do lado esquerdo.
1. Por predefinição, a política aplica-se a *Todos os utilizadores*. Se desejar, selecione **Atribuições,** em seguida, escolha os utilizadores ou grupos para aplicar a política.
1. Em *Controlos*, selecione **Access**. Certifique-se de que a opção para *o registo de MFA AD AD do Azure* É verificada e, em seguida, escolha **Select**.
1. **Descoda a política de execução** para on e, *em* seguida, selecione **Guardar**.

    ![Screenshot de como exigir que os utilizadores se registem para MFA no portal Azure](./media/tutorial-risk-based-sspr-mfa/enable-mfa-registration.png)

## <a name="enable-user-risk-policy-for-password-change"></a>Ativar a política de risco do utilizador para a mudança de palavra-passe

A Microsoft trabalha com investigadores, entidades responsáveis pela aplicação da lei, várias equipas de segurança da Microsoft e outras origens fidedignas para localizar os pares de nome de utilizador e palavra-passe. Quando um destes pares corresponde a uma conta no seu ambiente, pode ser solicitada uma alteração de senha baseada no risco. Esta política e ação requer que o utilizador atualize a sua palavra-passe antes de poder iniciar sação para garantir que quaisquer credenciais previamente expostas deixem de funcionar.

Para ativar esta política, complete os seguintes passos:

1. Selecione a política de risco do **Utilizador** a partir do menu do lado esquerdo.
1. Por predefinição, a política aplica-se a *Todos os utilizadores*. Se desejar, selecione **Atribuições,** em seguida, escolha os utilizadores ou grupos para aplicar a política.
1. *Em Condições*, escolha **Selecionar as condições > Selecionar um nível de risco,** em seguida, escolher *Médio e superior*.
1. Escolha **Selecione** e, em **seguida, Feito**.
1. No *Acesso*, selecione **Access**. Certifique-se de que a opção para **Permitir o acesso** e exigir a alteração da *palavra-passe* é verificada e, em seguida, escolha **Selecione**.
1. **Descoda a política de execução** para on e, *em* seguida, selecione **Guardar**.

    ![Screenshot de como ativar a política de risco do utilizador no portal Azure](./media/tutorial-risk-based-sspr-mfa/enable-user-risk-policy.png)

## <a name="enable-sign-in-risk-policy-for-mfa"></a>Permitir a política de risco de inscrição para o MFA

A maioria dos utilizadores tem um comportamento normal que pode ser rastreado. Quando caem fora desta norma, pode ser arriscado permitir que assinem com sucesso. Em vez disso, pode querer bloquear esse utilizador ou pedir-lhe para realizar uma autenticação multi-factor. Se o utilizador completar com sucesso o desafio MFA, pode considerá-lo uma tentativa válida de iniciar sedudas e conceder acesso à aplicação ou serviço.

Para ativar esta política, complete os seguintes passos:

1. Selecione a **política de risco de inscrição** a partir do menu do lado esquerdo.
1. Por predefinição, a política aplica-se a *Todos os utilizadores*. Se desejar, selecione **Atribuições,** em seguida, escolha os utilizadores ou grupos para aplicar a política.
1. *Em Condições*, escolha **Selecionar as condições > Selecionar um nível de risco,** em seguida, escolher *Médio e superior*.
1. Escolha **Selecione** e, em **seguida, Feito**.
1. No *Acesso*, escolha **Selecionar um controlo**. Certifique-se de que a opção para **Permitir o acesso** e exigir a *autenticação de vários fatores* é verificada e, em seguida, escolha **Selecione**.
1. **Descoda a política de execução** para on e, *em* seguida, selecione **Guardar**.

    ![Screenshot de como ativar a política de risco de inscrição no portal Azure](./media/tutorial-risk-based-sspr-mfa/enable-sign-in-risk-policy.png)

## <a name="test-risky-sign-events"></a>Teste eventos de sinais de risco

A maioria dos eventos de inscrição do utilizador não desencadeará as políticas baseadas no risco configuradas nos passos anteriores. Um utilizador pode nunca ver uma solicitação para um MFA adicional ou para redefinir a sua palavra-passe. Se as suas credenciais permanecerem seguras e o seu comportamento consistente, os seus eventos de entrada serão bem sucedidos.

Para testar as políticas de Proteção de Identidade Azure AD criadas nos passos anteriores, precisa de uma forma de simular comportamentos de risco ou potenciais ataques. Os passos para fazer estes testes variam em função da política de Proteção de Identidade Azure AD que pretende validar. Para obter mais informações sobre cenários e etapas, consulte [Simular deteções de riscos na Proteção de Identidade AZure AD](../identity-protection/howto-identity-protection-simulate-risk.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Se tiver concluído os testes e deixar de querer ter as políticas baseadas no risco ativadas, volte a cada política que pretende desativar e desative a Política de **Execução** para *desligar*.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, permitiu políticas de utilizador baseadas em riscos para a Proteção de Identidade Azure AD. Aprendeu a:

> [!div class="checklist"]
> * Compreender as políticas disponíveis para a Azure AD Identity Protection
> * Ativar o registo de autenticação multi-factor Azure AD
> * Permitir alterações de palavra-passe baseadas em risco
> * Ativar a Multi-Factor Authentication baseada em risco
> * Testar políticas baseadas no risco para tentativas de inscrição do utilizador

> [!div class="nextstepaction"]
> [Saiba mais sobre a Azure AD Identity Protection](../identity-protection/overview-identity-protection.md)
