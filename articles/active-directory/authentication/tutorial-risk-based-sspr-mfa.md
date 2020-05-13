---
title: Proteção de entrada de utilizador baseada em risco no Diretório Ativo azure
description: Neste tutorial, aprende-se a permitir que a Proteção de Identidade Azure proteja os utilizadores quando o comportamento de entrada de risco é detetado na sua conta.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 05/11/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 718a38f4744b6a1f9b4ebd0112be07b2556f1c39
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83116075"
---
# <a name="tutorial-use-risk-detections-for-user-sign-ins-to-trigger-azure-multi-factor-authentication-or-password-changes"></a>Tutorial: Utilize deteções de risco para iniciar alterações de autenticação de multifactor e de acesso ao Azure para desencadear alterações na autenticação de vários fatores do Azure ou na palavra-passe

Para proteger os seus utilizadores, pode configurar políticas baseadas em risco no Azure Ative Directory (Azure AD) que respondem automaticamente a comportamentos de risco. As políticas de Proteção de Identidade Azure AD podem bloquear automaticamente uma tentativa de entrada ou exigir medidas adicionais, tais como exigir uma alteração de senha ou um pedido para a autenticação de multi-factors Azure. Estas políticas funcionam com as políticas de Acesso Condicional Azure AD existentes como uma camada extra de proteção para a organização org. Os utilizadores podem nunca desencadear um comportamento de risco numa destas políticas, mas a sua organização está protegida se for feita uma tentativa de comprometer a sua segurança.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Compreender as políticas disponíveis para a Proteção de Identidade da AD Azure
> * Ativar o registo de autenticação multi-factor azure
> * Permitir alterações de palavra-passe baseadas em risco
> * Ativar a Multi-Factor Authentication baseada em risco
> * Testar políticas baseadas no risco para tentativas de inscrição no utilizador

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, necessita dos seguintes recursos e privilégios:

* Um inquilino azure working com pelo menos uma licença de teste Azure AD Premium P2 habilitada.
    * Se necessário, [crie um de graça.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Uma conta com privilégios *de Administrador Global.*
* Azure AD configurado para reset de senha de autosserviço e autenticação de multi-factor azure
    * Se necessário, complete o tutorial para ativar o [Azure AD SSPR](tutorial-enable-sspr.md).
    * Se necessário, complete o tutorial para ativar a [autenticação de multifactor estoiro.](tutorial-enable-azure-mfa.md)

## <a name="overview-of-azure-ad-identity-protection"></a>Visão geral da Proteção de Identidade da AD Azure

Todos os dias, a Microsoft recolhe e analisa triliões de sinais anooníferos como parte das tentativas de início de sessão do utilizador. Estes sinais ajudam a construir padrões de bom comportamento de inscrição do utilizador e identificam potenciais tentativas de insessão de risco. A Azure AD Identity Protection pode rever as tentativas de entrada do utilizador e tomar medidas adicionais se houver comportamento suspeito:

Algumas das seguintes ações podem desencadear a deteção de riscos de proteção de identidade da Azure AD:

* Utilizadores com credenciais vazadas.
* Inscrições a partir de endereços IP anónimos.
* Impossível viagem para locais atípicos.
* Inscrições de dispositivos infetados.
* Inscrições em endereços IP com atividade suspeita.
* Inscrições em locais desconhecidos.

As três seguintes políticas estão disponíveis na Azure AD Identity Protection para proteger os utilizadores e responder a atividades suspeitas. Pode optar por ligar ou desligar a aplicação da política, selecionar utilizadores ou grupos para que a apólice se aplique e decidir se pretende bloquear o acesso ao início ou solicitar ações adicionais.

* Política de risco do utilizador
    * Identifica e responde a contas de utilizador que podem ter comprometido credenciais. Pode levar o utilizador a criar uma nova senha.
* Assine na política de risco
    * Identifica e responde a tentativas suspeitas de inscrição. Pode levar o utilizador a fornecer formas adicionais de verificação utilizando a autenticação de multifactor Estoque Azure.
* Política de registo de MFA
    * Certifica-se de que os utilizadores estão registados para autenticação multi-factor Azure. Se uma política de risco de entrada for solicitada para o MFA, o utilizador já deve estar registado para autenticação multi-factor Azure.

Quando ativa um utilizador de política ou assina na política de risco, também pode escolher o limiar para o nível de risco - *baixo e superior,* *médio e superior*, ou *alto*. Esta flexibilidade permite-lhe decidir o quão agressivo pretende ser na aplicação de quaisquer controlos para eventos suspeitos de inscrição.

Para mais informações sobre a Proteção de Identidade Azure AD, consulte o que é a Proteção de [Identidade Azure AD?](../identity-protection/overview-identity-protection.md)

## <a name="enable-mfa-registration-policy"></a>Ativar a política de registo do MFA

A Azure AD Identity Protection inclui uma política padrão que pode ajudar a registar os utilizadores para autenticação multi-factor Azure. Se utilizar políticas adicionais para proteger eventos de inscrição, necessita de utilizadores que já se tenham registado para o MFA. Quando ativa esta política, não é necessário que os utilizadores realizem MFA em cada evento de início de sessão. A apólice verifica apenas o estado de registo de um utilizador e pede-lhes que se registem antes do registo se necessário.

É recomendado ativar a política de registo de MFA para utilizadores que devem ser ativados para políticas adicionais de Proteção de Identidade Azure AD. Para ativar esta política, complete os seguintes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de administrador global.
1. Procure e selecione **Azure Ative Directory**, selecione **Security,** em seguida, sob a rubrica proteger o menu *Protect* **.**
1. Selecione a política de registo de **MFA** no menu do lado esquerdo.
1. Por predefinição, a política aplica-se a *Todos os utilizadores*. Se desejar, selecione **Atribuições,** escolha os utilizadores ou grupos para aplicar a apólice.
1. Sob *controlos,* selecione **Access**. Certifique-se de que a opção para *exigir registo De Tema Azure* está verificada e, em seguida, escolha **Select**.
1. Definir a política de **aplicação** *para on*e, em seguida, selecione **Guardar**.

    ![Screenshot de como exigir que os utilizadores se registem para mFA no portal Azure](./media/tutorial-risk-based-sspr-mfa/enable-mfa-registration.png)

## <a name="enable-user-risk-policy-for-password-change"></a>Ativar a política de risco do utilizador para a alteração de palavra-passe

A Microsoft trabalha com investigadores, entidades responsáveis pela aplicação da lei, várias equipas de segurança da Microsoft e outras origens fidedignas para localizar os pares de nome de utilizador e palavra-passe. Quando um destes pares corresponde a uma conta no seu ambiente, pode ser solicitada uma alteração de senha baseada no risco. Esta política e ação exige que o utilizador atualize a sua palavra-passe antes de poder iniciar sessão para garantir que quaisquer credenciais anteriormente expostas já não funcionam.

Para ativar esta política, complete os seguintes passos:

1. Selecione a política de risco do **Utilizador** a partir do menu do lado esquerdo.
1. Por predefinição, a política aplica-se a *Todos os utilizadores*. Se desejar, selecione **Atribuições,** escolha os utilizadores ou grupos para aplicar a apólice.
1. Em *Condições,* escolha **Selecione condições > Selecione um nível**de risco e, em seguida, escolha *Médio e superior*.
1. Escolha **Selecionar,** depois **feito.**
1. No *acesso,* selecione **Access**. Certifique-se de que a opção de **permitir o acesso** e exigir a alteração da *palavra-passe* é verificada e, em seguida, escolha **Selecionar**.
1. Definir a política de **aplicação** *para on*e, em seguida, selecione **Guardar**.

    ![Screenshot de como ativar a política de risco do utilizador no portal Azure](./media/tutorial-risk-based-sspr-mfa/enable-user-risk-policy.png)

## <a name="enable-sign-in-risk-policy-for-mfa"></a>Ativar a política de risco de inscrição para o MFA

A maioria dos utilizadores tem um comportamento normal que pode ser rastreado. Quando se encontram fora desta norma, pode ser arriscado permitir-lhes assinar com sucesso. Em vez disso, pode querer bloquear esse utilizador ou pedir-lhe para realizar uma autenticação de vários fatores. Se o utilizador completar com sucesso o desafio MFA, pode considerá-lo uma tentativa de inscrição válida e conceder acesso à aplicação ou serviço.

Para ativar esta política, complete os seguintes passos:

1. Selecione a **política de risco de iniciar a sessão** no menu do lado esquerdo.
1. Por predefinição, a política aplica-se a *Todos os utilizadores*. Se desejar, selecione **Atribuições,** escolha os utilizadores ou grupos para aplicar a apólice.
1. Em *Condições,* escolha **Selecione condições > Selecione um nível**de risco e, em seguida, escolha *Médio e superior*.
1. Escolha **Selecionar,** depois **feito.**
1. No *Acesso,* escolha **Selecione um controlo**. Certifique-se de que a opção de **permitir o acesso** e exigir a *autenticação de vários fatores* é verificada e, em seguida, escolha **Selecionar**.
1. Definir a política de **aplicação** *para on*e, em seguida, selecione **Guardar**.

    ![Screenshot de como ativar a política de risco de entrada no portal Azure](./media/tutorial-risk-based-sspr-mfa/enable-sign-in-risk-policy.png)

## <a name="test-risky-sign-events"></a>Teste de eventos de sinais de risco

A maioria dos eventos de inscrição do utilizador não desencadeará as políticas baseadas no risco configuradas nos passos anteriores. Um utilizador nunca poderá ver um pedido de MFA adicional ou para redefinir a sua palavra-passe. Se as suas credenciais se mantiverem seguras e o seu comportamento consistente, os seus eventos de inscrição seriam bem sucedidos.

Para testar as políticas de Proteção de Identidade Azure AD criadas nos passos anteriores, precisa de uma forma de simular comportamentos de risco ou potenciais ataques. As etapas para fazer estes testes variam em função da política de Proteção de Identidade Azure AD que pretende validar. Para obter mais informações sobre cenários e etapas, consulte [Simular deteções de risco na Proteção de Identidade Azure AD](../identity-protection/howto-identity-protection-simulate-risk.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se tiver concluído os testes e já não pretender ter as políticas baseadas no risco ativadas, volte a cada política que deseja desativar e definir a Política de **Aplicação** para *Desligar*.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, permitiu políticas de utilizador baseadas em risco para a Proteção de Identidade Azure AD. Aprendeu a:

> [!div class="checklist"]
> * Compreender as políticas disponíveis para a Proteção de Identidade da AD Azure
> * Ativar o registo de autenticação multi-factor azure
> * Permitir alterações de palavra-passe baseadas em risco
> * Ativar a Multi-Factor Authentication baseada em risco
> * Testar políticas baseadas no risco para tentativas de inscrição no utilizador

> [!div class="nextstepaction"]
> [Saiba mais sobre a Proteção de Identidade da Azure AD](../identity-protection/overview-identity-protection.md)
