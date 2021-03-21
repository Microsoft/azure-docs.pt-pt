---
title: Ativar o registo combinado de informações de segurança - Azure Ative Directory
description: Saiba como simplificar a experiência do utilizador final com o registo combinado de autenticação multi-factor Ad Ad e autosserviço.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 01/27/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcfaef2f518028762958477a5b0d326acc237d1f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98938444"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Permitir o registo combinado de informações de segurança no Azure Ative Directory

Antes do registo combinado, os utilizadores registaram métodos de autenticação para autenticação multi-factor Azure AD e autosserviço de autosserviço reset (SSPR) separadamente. As pessoas confundiram-se com o facto de métodos semelhantes terem sido usados para autenticação multi-factor Azure AD e SSPR, mas tiveram de se registar para ambas as funcionalidades. Agora, com o registo combinado, os utilizadores podem registar-se uma vez e obter os benefícios tanto da Autenticação Multi-Factor AD Azure como da SSPR.

> [!NOTE]
> A partir de 15 de agosto de 2020, todos os novos inquilinos da AD Azure serão automaticamente habilitados para o registo combinado. 

Para se certificar de que compreende a funcionalidade e os efeitos antes de ativar a nova experiência, consulte os [conceitos combinados de registo de informações de segurança.](concept-registration-mfa-sspr-combined.md)

![Experiência reforçada do registo combinado de informações de segurança](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Permitir o registo combinado

Para permitir o registo combinado, complete estes passos:

1. Inscreva-se no portal Azure como administrador de utilizador ou administrador global.
2. Vá às definições do Utilizador **do Diretor Ativo Azure**  >    >  **Gerir as definições de pré-visualização da funcionalidade do utilizador**.
3. No âmbito **do Utilizadores pode utilizar a experiência de registo de informações de segurança combinada,** opte por ativar um grupo **selecionado** de utilizadores ou para **todos os** utilizadores.

   ![Ativar a experiência de informação de segurança combinada para os utilizadores](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info.png)

> [!NOTE]
> Depois de ativar o registo combinado, os utilizadores que registem ou confirmem o seu número de telefone ou aplicação móvel através da nova experiência podem utilizá-los para autenticação multi-factor Ad Azure e SSPR, se esses métodos estiverem ativados nas políticas Azure AD Multi-Factor Authentication e SSPR.
>
> Se então desativar esta experiência, os utilizadores que se difundirem na página de registo SSPR anterior `https://aka.ms/ssprsetup` são obrigados a realizar a autenticação de vários fatores antes de poderem aceder à página.

Se configurar a *Lista de Atribuição de Zonas do Site* no Internet Explorer, os seguintes sites têm de estar na mesma zona:

* *[https://login.microsoftonline.com](https://login.microsoftonline.com)*
* *[https://mysignins.microsoft.com](https://mysignins.microsoft.com)*
* *[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)*

## <a name="conditional-access-policies-for-combined-registration"></a>Políticas de acesso condicional para registo combinado

Para garantir quando e como os utilizadores se registam para autenticação multi-factor Ad Azure e redefinição da palavra-passe de autosserviço, pode utilizar as ações do utilizador na política de Acesso Condicional. Esta funcionalidade pode ser ativada em organizações que pretendam que os utilizadores se registem para autenticação multi-factor Azure e SSPR a partir de uma localização central, como uma localização de rede fidedigna durante o embarque de RH.

> [!NOTE]
> Esta política só se aplica quando um utilizador acede a uma página de registo combinado. Esta política não aplica a inscrição de MFA quando um utilizador acede a outras aplicações.
>
> Pode criar uma política de registo de MFA utilizando [a Azure Identity Protection - Configure MFA Policy](../identity-protection/howto-identity-protection-configure-mfa-policy.md).

Para obter mais informações sobre a criação de localizações fidedignas no Acesso Condicional, consulte [qual é a condição de localização no Azure Ative Directory Conditional Access?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Criar uma política para exigir o registo a partir de um local de confiança

Preencha as seguintes etapas para criar uma política que se aplique a todos os utilizadores selecionados que tentem registar-se utilizando a experiência de registo combinado, e bloqueie o acesso a menos que estejam a ligar-se a partir de um local marcado como rede fidedigna:

1. No **portal Azure,** navegue pelo **Azure Ative Directory**  >    >  **Security Conditional Access**.
1. Selecione **+ Nova política**.
1. Introduza um nome para esta política, como *Registo de Informações de Segurança Combinadas em Redes Fidedignas*.
1. Em **Atribuições**, selecione **Utilizadores e grupos**. Escolha os utilizadores e grupos a que pretende que esta política se aplique e, em seguida, selecione **Fazer**.

   > [!WARNING]
   > Os utilizadores devem estar habilitados para o registo combinado.

1. Em **aplicativos ou ações cloud**, selecione **ações do Utilizador**. Verifique **as informações de segurança do Registo** e, em seguida, selecione 'Fazer' ( **'Fazer' (')**

    ![Criar uma política de acesso condicional para controlar o registo de informações de segurança](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. Em **Locais de**  >  **Condições,** configure as seguintes opções:
   1. Configurar **Sim.**
   1. Incluir **qualquer localização.**
   1. Excluir **todos os locais fidedignos.**
1. Selecione **Feito** na janela *'Localizações'* e, em seguida, selecione **'Fazer'** na janela *Condições.*
1. Sob **controlos de acesso**  >  **Grant,** escolha o acesso ao **Bloco** e, em seguida, **selecione**.
1. Defina **Ativar política** como **Ativado**.
1. Para finalizar a política, **selecione Criar**.

## <a name="next-steps"></a>Passos seguintes

Se precisar de ajuda, consulte o [registo combinado de informações de segurança combinadas](howto-registration-mfa-sspr-combined-troubleshoot.md) ou saiba [qual é a condição de localização no Acesso Condicional AD Azure?](../conditional-access/location-condition.md)

Uma vez que os utilizadores estejam habilitados para o registo combinado, pode então [ativar o reset da palavra-passe de autosserviço](tutorial-enable-sspr.md) e [ativar a autenticação multi-factor AD Azure](tutorial-enable-azure-mfa.md).

Se necessário, aprenda a [forçar os utilizadores a re-registar métodos de autenticação](howto-mfa-userdevicesettings.md#manage-user-authentication-options).
