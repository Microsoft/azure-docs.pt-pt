---
title: Começar com inscrição combinada - Diretório Ativo Azure
description: Ativar a autenticação combinada de Multi-Factor e o registo de palavra-passe de autosserviço Azure AD
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
ms.openlocfilehash: 466b063253ee49ab58c2685f359b4bb8a4079532
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81639686"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Permitir o registo combinado de informações de segurança no Diretório Ativo do Azure

Antes do registo combinado, os utilizadores registaram métodos de autenticação para a autenticação de multi-factores Azure e reset de palavra-passe self-service (SSPR) separadamente. As pessoas estavam confusas de que métodos semelhantes eram usados para a autenticação multi-factor e SSPR, mas tinham de se registar para ambas as funcionalidades. Agora, com o registo combinado, os utilizadores podem registar-se uma vez e obter os benefícios tanto da Autenticação Multi-Factor como do SSPR.

Antes de permitir a nova experiência, reveja o artigo Registo combinado de [informações](concept-registration-mfa-sspr-combined.md) de segurança para garantir que compreende a funcionalidade e os efeitos desta funcionalidade.

![Experiência melhorada do registo combinado de informações de segurança](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Permitir o registo combinado

Complete estas etapas para permitir o registo combinado:

1. Inscreva-se no portal Azure como administrador de utilizador ou administrador global.
2. Vá para as > **definições** > de utilizador do **Diretório Ativo do Azure****Gerencie as definições de pré-visualização**do utilizador .
3. No âmbito do Utilizador pode utilizar funcionalidades de **pré-visualização para registar e gerir informações**de segurança, opte por ativar para um grupo de utilizadores **Selecionados** ou para **Todos os** utilizadores.

   ![Ativar a experiência combinada de pré-visualização de informações de segurança para todos os utilizadores](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!NOTE]
> Depois de ativar o registo combinado, os utilizadores que se registem ou confirmem o seu número de telefone ou aplicação móvel através da nova experiência podem utilizá-los para autenticação multi-factor e SSPR, se esses métodos estiverem ativados nas políticas de Autenticação multi-Factor e SSPR. Se, em seguida, desativar esta experiência, os `https://aka.ms/ssprsetup` utilizadores que acederem à página de registo sSPR anterior serão obrigados a efetuar a autenticação de vários fatores antes de poderem aceder à página.

Se configurar a Lista de Atribuição de Zonas no Internet Explorer, os seguintes sites têm de estar na mesma zona:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Políticas de acesso condicional para registo combinado

A segurança quando e como os utilizadores se registam para a Autenticação multi-factor Do Azure e o reset de palavra-passe self-service é agora possível com as ações do utilizador na política de Acesso Condicional. Esta funcionalidade está disponível para organizações que permitiram a funcionalidade de [registo combinado.](../authentication/concept-registration-mfa-sspr-combined.md) Esta funcionalidade pode ser ativada em organizações onde pretendem que os utilizadores se registem para a Autenticação Multi-Factor E SSPR azure a partir de um local central, como uma localização de rede confiável durante o embarque em RH.

Para obter mais informações sobre a criação de localizações fidedignas no Acesso Condicional, consulte o artigo Qual é a condição de localização no Acesso Condicional do [Diretório Ativo azure?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Criar uma política para exigir o registo a partir de um local de confiança

A seguinte política aplica-se a todos os utilizadores selecionados que tentem registar-se utilizando a experiência de registo combinado e bloqueia o acesso a menos que estejam a ligar-se a partir de um local marcado como rede fidedigna.

1. No **portal Azure,** navegue até ao**Acesso Condicional** de**Segurança** >  **do Diretório** > Ativo azure
1. Selecione **+ Nova política**
1. Insira um nome para esta política, como registo combinado de informações de *segurança em redes fidedignas*.
1. Em **Atribuições**, selecione **Utilizadores e grupos**. Escolha os utilizadores e grupos a que pretende que esta política se aplique e, em seguida, selecione **Done**.

   > [!WARNING]
   > Os utilizadores devem estar habilitados para o registo combinado.

1. Em **aplicações ou ações cloud,** selecione **as ações do Utilizador.** Verifique **registar informações**de segurança e, em seguida, selecione **Done**.

    ![Criar uma política de acesso condicional para controlar o registo de informações de segurança](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. Em **Condições** > **Locais,** configure as seguintes opções:
   1. Configurar **Sim**
   1. Incluir **Qualquer local**
   1. Excluir **todos os locais confiáveis**
1. Selecione **Feito** na janela *Locais* e, em seguida, selecione **Feito** na janela *Condições.*
1. Sob **controlode** > **acesso,** escolha o acesso ao **bloco**e, em seguida, **selecione**
1. Definir **ativar a política** para **on**
1. Para finalizar a política, selecione **Criar**

## <a name="next-steps"></a>Passos seguintes

Se precisar de ajuda, veja como resolver o registo combinado de [informações](howto-registration-mfa-sspr-combined-troubleshoot.md) de segurança ou saiba Qual é a condição de localização no Acesso Condicional do [Diretório Ativo Azure?](../conditional-access/location-condition.md)

Para ativar as funcionalidades do seu inquilino Azure AD, consulte os tutoriais para permitir a [redefinição da palavra-passe de self-service](tutorial-enable-sspr.md) e ativar a [autenticação de multi-factores Azure](tutorial-enable-azure-mfa.md).

Saiba como permitir o [registo combinado no seu inquilino](howto-registration-mfa-sspr-combined.md) ou force os utilizadores a [reregistar métodos de autenticação](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

Também pode rever os [métodos disponíveis para autenticação de multi-factores Azure e SSPR](concept-authentication-methods.md).
