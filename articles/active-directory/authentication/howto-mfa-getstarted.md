---
title: Introdução ao MFA do Azure na cloud
description: Introdução ao Microsoft Azure multi-factor Authentication acesso condicional
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07e98d5a93b8576c06e9be8274a974b1b574da4b
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57217752"
---
# <a name="deploy-cloud-based-azure-multi-factor-authentication"></a>Implementar o Multi-Factor Authentication do Azure baseado em cloud

Introdução ao multi-factor Authentication (MFA do Azure) é um processo simples.

Antes de começar, certifique-se de que tem os seguintes pré-requisitos:

* Uma conta de administrador global no inquilino do Azure AD. Se precisar de ajuda para concluir este passo, consulte nosso artigo [começar com o Azure AD](../get-started-azure-ad.md).
* Corretas licenças atribuídas a utilizadores. Se precisar de mais informações, veja o artigo [como obter o Azure multi-factor Authentication](concept-mfa-licensing.md).

## <a name="choose-how-to-enable"></a>Escolha como pretende ativar

**Ativado pela política de acesso condicional** -este método é discutido neste artigo. É a maneira mais flexível para ativar a verificação de dois passos para os seus utilizadores. Ativar através da política de acesso condicional só funciona para o Azure MFA na cloud e é uma funcionalidade premium do Azure AD.

**Ativada pelo Azure AD Identity Protection** -este método utiliza a política de risco do Azure AD Identity Protection, para exigir verificação de dois passos com base apenas em risco de início de sessão para todas as aplicações na cloud. Este método requer licenciamento do Azure Active Directory P2. Obter mais informações sobre este método podem ser encontradas na [como configurar a política de risco do utilizador](../identity-protection/howto-user-risk-policy.md).

**Ativado, alterando o estado do utilizador** -este é o método tradicional para exigir a verificação de dois passos. Ele funciona com ambos os Azure MFA na cloud e o servidor MFA do Azure. Ao utilizar este método requer que os usuários realizem a verificação de dois passos **sempre que** que iniciar sessão e substitui as políticas de acesso condicional. Obter mais informações sobre este método podem ser encontradas na [como requerer verificação de dois passos para um utilizador](howto-mfa-userstates.md).

> [!Note]
> Podem encontrar mais informações sobre preços e licenças no [do Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) e [multi-factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) páginas de preços.

## <a name="choose-authentication-methods"></a>Escolher métodos de autenticação

Ative, pelo menos, um método de autenticação para os seus utilizadores com base nos requisitos da sua organização. Achamos que, quando estiver ativado para os utilizadores da aplicação Microsoft Authenticator fornece a melhor experiência de utilizador. Se precisa entender quais métodos estão disponíveis e como configurá-los, consulte o artigo [quais são os métodos de autenticação](concept-authentication-methods.md).

> [!IMPORTANT]
> A partir de Março de 2019 as opções de chamada telefónica não estarão disponível para os utilizadores do MFA e o SSPR em inquilinos gratuitos/avaliação do Azure AD. Mensagens SMS não são afetadas por esta alteração. Chamada telefónica continuará a estar disponíveis para os utilizadores na pago inquilinos do Azure AD. Esta alteração só afeta o inquilinos gratuitos/avaliação do Azure AD.

## <a name="get-users-to-enroll"></a>Introdução aos utilizadores a inscrição

Depois de ativar a política de acesso condicional, os utilizadores serão forçados a inscrição da próxima vez que utilizarem uma aplicação protegida com a política. Se habilitar uma política que exija a MFA para todos os utilizadores em todas as aplicações na cloud, esta ação pode causar dores de cabeça para os seus utilizadores e o suporte técnico. Recomenda-se solicitar que os utilizadores registem os métodos de autenticação com antecedência com o portal de registo em [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup). Muitas organizações consideram que criar cartazes, cartões de tabela e mensagens de e-mail ajuda a promover a adoção.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Ativar a multi-factor Authentication com o acesso condicional

Inicie sessão para o [portal do Azure](https://portal.azure.com) através de uma conta de administrador global.

### <a name="choose-verification-options"></a>Escolher opções de verificação

Antes de ativar a multi-factor Authentication, sua organização tem de determinar quais opções de verificação permitem. Com o objetivo deste exercício, habilitar a chamada telefónica e mensagem de texto para telefone, pois são opções genéricas que a maioria é capaz de usar. Obter mais informações sobre métodos de autenticação e seu uso, podem ser encontradas no artigo, [quais são os métodos de autenticação?](concept-authentication-methods.md)

1. Navegue até **do Azure Active Directory**, **utilizadores**, **multi-factor Authentication**.

   ![Aceder ao portal do multi-factor Authentication do painel de utilizadores do Azure AD no portal do Azure](media/howto-mfa-getstarted/users-mfa.png)

1. O novo separador que se abre, navegue até **definições do serviço**.
1. Sob **opções de verificação**, verificar todas as caixas para os métodos disponíveis para os utilizadores.

   ![Configurar métodos de verificação no separador de definições do serviço de multi-factor Authentication](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

4. Clique em **Guardar**.
5. Fechar o **definições do serviço** separador.

### <a name="create-conditional-access-policy"></a>Criar política de acesso condicional

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) através de uma conta de administrador global.
1. Procure **Azure Active Directory**, **Acesso condicional**
1. Selecione **nova política**.
1. Forneça um nome significativo para a sua política.
1. Sob **utilizadores e grupos**:
   * Sobre o **inclusão** separador, selecione a **todos os utilizadores** botão de opção
   * RECOMENDADO: Sobre o **excluir** separador, marque a caixa **utilizadores e grupos** e escolha um grupo para ser utilizado para exclusões quando os utilizadores não têm acesso aos seus métodos de autenticação.
   * Clique em **Concluído**.
1. Sob **aplicações na Cloud**, selecione a **todas as aplicações na cloud** botão de opção.
   * OPCIONALMENTE: Sobre o **excluir** separador, selecione aplicações na cloud que sua organização não exija a MFA para.
   * Clique em **Concluído**.
1. Sob **condições** secção:
   * OPCIONALMENTE: Se tiver ativado o Azure Identity Protection, pode optar por avaliar o risco de início de sessão como parte da política.
   * OPCIONALMENTE: Se tiver configurado a locais confiáveis ou localizações com nome, poderá especificar a incluir ou excluir esses locais da política.
1. Sob **concessão**, certifique-se a **conceder acesso** botão de opção está selecionada.
    * Marque a caixa **exigir autenticação multifator**.
    * Clique em **Selecionar**.
1. Ignorar a **sessão** secção.
1. Definir o **ativar política** alternar para **no**.
1. Clique em **Criar**.

![Criar uma política de acesso condicional para ativar a MFA para os utilizadores do portal do Azure no grupo piloto](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)

### <a name="test-azure-multi-factor-authentication"></a>Testar Multi-Factor Authentication do Azure

Para confirmar que a sua política de acesso condicional funciona, teste a iniciar sessão a um recurso que não deve exigir a MFA e, em seguida, no portal do Azure que exija a MFA.

1. Abra uma nova janela de browser no modo InPrivate ou incógnito e navegue para [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com).
   * Inicie sessão com o utilizador de teste criado como parte da secção pré-requisitos deste artigo e tenha em atenção que ele deve lhe pede para concluir a MFA.
   * Feche a janela do browser.
2. Abra uma nova janela de browser no modo InPrivate ou incógnito e navegue para [https://portal.azure.com](https://portal.azure.com).
   * Inicie sessão com o teste de utilizador criadas como parte da secção de pré-requisitos deste artigo e tenha em atenção que agora deve ser necessário para se registar e utilizar o Azure multi-factor Authentication.
   * Feche a janela do browser.

## <a name="next-steps"></a>Passos Seguintes

Parabéns, tiver configurado o multi-factor Authentication na cloud.

Por que foi que o usuário é solicitado ou não lhe for pedido para executar a MFA? Consulte a secção [relatório de inícios de sessão do Azure AD nos relatórios de documento de multi-factor Authentication](howto-mfa-reporting.md#azure-ad-sign-ins-report).

Para configurar definições adicionais, como os IPs fidedignos, mensagens de voz personalizada e alertas de fraude, consulte o artigo [definições de configurar o Azure multi-factor Authentication](howto-mfa-mfasettings.md).

Informações sobre como gerir as definições de utilizador para o Azure multi-factor Authentication podem ser encontrado no artigo [gerir definições de utilizador com o Azure multi-factor Authentication na cloud](howto-mfa-userdevicesettings.md).

[Ativar o registo convergido para a reposição de palavra-passe self-service do Azure multi-factor Authentication e o Azure AD](concept-registration-mfa-sspr-converged.md).
