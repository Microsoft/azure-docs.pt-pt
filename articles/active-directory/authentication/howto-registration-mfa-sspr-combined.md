---
title: Introdução ao registro combinado-Azure Active Directory
description: Habilitar a autenticação multifator do Azure AD combinada e o registro de redefinição de senha de autoatendimento (versão prévia)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2bd3f61ffc07881ed8e502788b11fc0f435735b
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847359"
---
# <a name="enable-combined-security-information-registration-preview"></a>Habilitar o registro de informações de segurança combinadas (versão prévia)

Antes de habilitar a nova experiência, examine o artigo [registro combinado de informações de segurança (versão prévia)](concept-registration-mfa-sspr-combined.md) para garantir que você compreenda a funcionalidade e os efeitos desse recurso.

![Experiência avançada de registro de informações de segurança combinada](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| O registro de informações de segurança combinadas para a autenticação multifator do Azure e a redefinição de senha de autoatendimento do Azure AD (Azure Active Directory) é um recurso de visualização pública do Azure AD. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!NOTE]
> As organizações que habilitaram a visualização anterior para registrar e gerenciar informações de segurança devem concluir as etapas abaixo para habilitar a experiência de visualização avançada. Para organizações que não fazem a comutação, em 8 de outubro de 2019, a Microsoft mudará os usuários da versão prévia anterior para registrar e gerenciar informações de segurança para a experiência aprimorada. 
> 
> Se você não tiver habilitado nenhuma versão da visualização, sua organização não será afetada.

## <a name="enable-combined-registration"></a>Permitir o registo combinado

Conclua estas etapas para habilitar o registro combinado:

1. Entre no portal do Azure como um administrador de usuário ou administrador global.
2. Vá para **Azure Active Directory** > **configurações de usuário** > **gerenciar configurações para recursos de visualização do painel de acesso**.
3. Em **usuários podem usar recursos de visualização para registrar e gerenciar informações de segurança – atualização**, escolha habilitar para um grupo de usuários **selecionado** ou para **todos** os usuários.

   ![Habilitar a experiência de visualização de informações de segurança combinadas para todos os usuários](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> A partir de março de 2019, as opções de chamada telefônica não estarão disponíveis para autenticação multifator e usuários SSPR em locatários gratuitos/de avaliação do Azure AD. As mensagens SMS não são afetadas por essa alteração. As opções de chamada telefônica ainda estarão disponíveis para os usuários em locatários pagos do Azure AD.

> [!NOTE]
> Depois de habilitar o registro combinado, os usuários que registrarem ou confirmarem seu número de telefone ou aplicativo móvel por meio da nova experiência poderão usá-los para autenticação multifator e SSPR, se esses métodos estiverem habilitados na autenticação multifator e SSPR Policie. Se você desabilitar essa experiência, os usuários que vão para a página de registro SSPR anterior em `https://aka.ms/ssprsetup` serão solicitados a executar a autenticação multifator antes que possam acessar a página.

Se você tiver configurado a lista de atribuição de site a zona no Internet Explorer, os seguintes sites devem estar na mesma zona:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Políticas de acesso condicional para registro combinado

A proteção de quando e como os usuários se registram para a autenticação multifator do Azure e a redefinição de senha de autoatendimento agora são possíveis com as ações do usuário na política de acesso condicional. Esse recurso de visualização está disponível para organizações que habilitaram a [visualização de registro combinado](../authentication/concept-registration-mfa-sspr-combined.md). Essa funcionalidade pode ser habilitada em organizações em que eles desejam que os usuários se registrem para a autenticação multifator do Azure e SSPR de um local central, como um local de rede confiável durante a integração de RH. Para obter mais informações sobre como criar locais confiáveis no acesso condicional, consulte o artigo [qual é a condição de local em Azure Active Directory acesso condicional?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Criar uma política para exigir o registro de um local confiável

A política a seguir se aplica a todos os usuários selecionados, que tentam se registrar usando a experiência de registro combinada e bloqueia o acesso, a menos que eles estejam se conectando de um local marcado como rede confiável.

![Criar uma política de autoridade de certificação para controlar o registro de informações de segurança](media/howto-registration-mfa-sspr-combined/conditional-access-register-security-info.png)

1. Na **portal do Azure**, navegue até **Azure Active Directory** > **acesso condicional**
1. Selecione **Nova política**
1. Em nome, insira um nome para essa política. Por exemplo, o **registro de informações de segurança combinadas em redes confiáveis**
1. Em **atribuições**, clique em **usuários e grupos**e selecione os usuários e grupos aos quais você deseja que essa política se aplique

   > [!WARNING]
   > Os usuários devem estar habilitados para a [visualização de registro combinado](../authentication/howto-registration-mfa-sspr-combined.md).

1. Em **aplicativos de nuvem ou ações**, selecione **ações do usuário**, marque **registrar informações de segurança (versão prévia)**
1. Em **condições** > **locais**
   1. Configurar **Sim**
   1. Incluir **qualquer local**
   1. Excluir **todos os locais confiáveis**
   1. Clique em **concluído** na folha locais
   1. Clique em **concluído** na folha condições
1. Em **controles de acesso** > **concessão**
   1. Clique em **bloquear acesso**
   1. Em seguida, clique em **selecionar**
1. Definir **habilitar política** como **ativado**
1. Em seguida, clique em **criar**

## <a name="next-steps"></a>Passos seguintes

[Forçar os usuários a registrar novamente os métodos de autenticação](howto-mfa-userdevicesettings.md#manage-authentication-methods)

[Métodos disponíveis para autenticação multifator e SSPR](concept-authentication-methods.md)

[Configurar a redefinição de senha de autoatendimento](howto-sspr-deployment.md)

[Configurar a autenticação multifator do Azure](howto-mfa-getstarted.md)

[Solucionando problemas de registro de informações de segurança combinadas](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Qual é a condição de local em Azure Active Directory acesso condicional?](../conditional-access/location-condition.md)
