---
title: Começar com inscrição combinada - Azure Ative Directory
description: Ativar o registo combinado de autenticação multi-factor Ad E autosserviço
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7feb69b2ea53794b780a983ed8ab4ba5874ac022
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85260853"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Permitir o registo combinado de informações de segurança no Azure Ative Directory

Antes do registo combinado, os utilizadores registaram métodos de autenticação para autenticação multi-factor Azure e redefinição de senha de autosserviço (SSPR) separadamente. As pessoas confundiram-se com o facto de métodos semelhantes terem sido usados para autenticação multi-factor Azure e SSPR, mas tiveram de se registar para ambas as funcionalidades. Agora, com o registo combinado, os utilizadores podem registar-se uma vez e obter os benefícios tanto da Autenticação Multi-Factor Azure como da SSPR.

Antes de ativar a nova experiência, reveja o artigo [Registo de informações de segurança combinadas](concept-registration-mfa-sspr-combined.md) para garantir que compreende a funcionalidade e os efeitos desta funcionalidade.

![Experiência reforçada do registo combinado de informações de segurança](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Permitir o registo combinado

Complete estes passos para permitir o registo combinado:

1. Inscreva-se no portal Azure como administrador de utilizador ou administrador global.
2. Vá às definições do Utilizador **do Diretor Ativo Azure**  >  **User settings**  >  **Gerir as definições de pré-visualização da funcionalidade do utilizador**.
3. No âmbito **do Utilizadores pode utilizar a experiência de registo de informações de segurança combinada,** opte por ativar um grupo **selecionado** de utilizadores ou para **todos os** utilizadores.

   ![Ativar a experiência de informação de segurança combinada para os utilizadores](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info.png)

> [!NOTE]
> Depois de ativar o registo combinado, os utilizadores que registem ou confirmem o seu número de telefone ou aplicação móvel através da nova experiência podem utilizá-los para autenticação multi-factor Azure e SSPR, se esses métodos estiverem ativados nas políticas Azure Multi-Factor Authentication e SSPR. Se então desativar esta experiência, os utilizadores que forem à página de registo SSPR anterior `https://aka.ms/ssprsetup` serão obrigados a realizar a autenticação de vários fatores antes de poderem aceder à página.

Se configurar a Lista de Atribuição de Zonas do Site no Internet Explorer, os seguintes sites têm de estar na mesma zona:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Políticas de acesso condicional para registo combinado

Garantir quando e como os utilizadores se registam para autenticação multi-factor Azure e redefinição de senha de autosserviço é agora possível com as ações do utilizador na política de Acesso Condicional. Esta funcionalidade está disponível para organizações que tenham ativado a [funcionalidade de registo combinado.](../authentication/concept-registration-mfa-sspr-combined.md) Esta funcionalidade pode ser ativada em organizações onde pretendem que os utilizadores se registem para autenticação multi-factor Azure e SSPR a partir de uma localização central, como uma localização de rede fidedigna durante o embarque de RH.

> [!NOTE]
> Esta política só se aplica quando um utilizador acede a uma página de registo combinado. Esta política não aplica a inscrição de MFA quando um utilizador acede a outras aplicações. Pode criar uma política de registo de MFA utilizando [a Azure Identity Protection - Configure MFA Policy](../identity-protection/howto-identity-protection-configure-mfa-policy.md).

Para obter mais informações sobre a criação de localizações fidedignas no Acesso Condicional, consulte o artigo [Qual é a condição de localização no Azure Ative Directory Conditional Access?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Criar uma política para exigir o registo a partir de um local de confiança

A seguinte política aplica-se a todos os utilizadores selecionados que tentam registar-se utilizando a experiência de registo combinado, e bloqueiam o acesso a menos que estejam a ligar-se a partir de um local marcado como rede fidedigna.

1. No **portal Azure,** navegue pelo **Azure Ative Directory**  >  **Security**  >  **Security Conditional Access**.
1. Selecione **+ Nova política**.
1. Introduza um nome para esta política, como *Registo de Informações de Segurança Combinadas em Redes Fidedignas*.
1. Em **Atribuições**, selecione **Utilizadores e grupos**. Escolha os utilizadores e grupos a que pretende que esta política se aplique e, em seguida, selecione **Fazer**.

   > [!WARNING]
   > Os utilizadores devem estar habilitados para o registo combinado.

1. Em **aplicativos ou ações cloud**, selecione **ações do Utilizador**. Verifique **as informações de segurança do Registo**e, em seguida, selecione 'Fazer' ( **'Fazer' (')**

    ![Criar uma política de acesso condicional para controlar o registo de informações de segurança](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. Em **Locais de**  >  **Condições,** configure as seguintes opções:
   1. Configurar **Sim.**
   1. Incluir **qualquer localização.**
   1. Excluir **todos os locais fidedignos.**
1. Selecione **Feito** na janela *'Localizações'* e, em seguida, selecione **'Fazer'** na janela *Condições.*
1. Sob **controlos de acesso**  >  **Grant,** escolha o acesso ao **Bloco**e, em seguida, **selecione**.
1. Defina **Permitir política** como **Ativado**.
1. Para finalizar a política, **selecione Criar**.

## <a name="next-steps"></a>Passos seguintes

Se precisar de ajuda, veja como resolver o registo combinado de [informações de segurança](howto-registration-mfa-sspr-combined-troubleshoot.md) ou saiba [qual é a condição de localização no Azure Ative Directory Conditional Access?](../conditional-access/location-condition.md)

Para ativar as funcionalidades no seu inquilino AZure AD, consulte os tutoriais para permitir o [reset da palavra-passe de autosserviço](tutorial-enable-sspr.md) e [ativar a autenticação multi-factor Azure](tutorial-enable-azure-mfa.md).

Saiba como [forçar os utilizadores a re-registar métodos de autenticação](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

Também pode rever os [métodos disponíveis para autenticação multi-factor Azure e SSPR.](concept-authentication-methods.md)
