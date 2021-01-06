---
title: Controlos de subvenções na política de acesso condicional - Diretório Ativo Azure
description: O que são controlos de concessão numa política de acesso condicional AD Azure
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 536a9641b68ccd2510a7891b46483a322fb6ecb7
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900931"
---
# <a name="conditional-access-grant"></a>Acesso Condicional: Concessão

Dentro de uma política de Acesso Condicional, um administrador pode recorrer aos controlos de acesso para conceder ou bloquear o acesso aos recursos.

![Política de acesso condicional com controlo de subvenções que requer autenticação de vários fatores](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Bloquear o acesso

O Bloco tem em conta quaisquer atribuições e impede o acesso com base na configuração da política de Acesso Condicional.

O bloco é um controlo poderoso que deve ser exercido com conhecimentos apropriados. As políticas com declarações de blocos podem ter efeitos colaterais não intencionais. Os testes e validações adequados são vitais antes de permitir em escala. Os administradores devem utilizar ferramentas como [o modo de relatório de acesso condicional](concept-conditional-access-report-only.md) e a ferramenta What If in [Conditional Access](what-if-tool.md) ao eseme fazer alterações.

## <a name="grant-access"></a>Conceder acesso

Os administradores podem optar por impor um ou mais controlos ao conceder o acesso. Estes controlos incluem as seguintes opções: 

- [Requerem autenticação multi-factor (autenticação multi-factor Azure AD)](../authentication/concept-mfa-howitworks.md)
- [Exigir que o dispositivo seja marcado como conforme (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [Requerem o dispositivo híbrido Azure AD](../devices/concept-azure-ad-join-hybrid.md)
- [Requera uma aplicação de cliente aprovada](app-based-conditional-access.md)
- [Pedir uma política de proteção de aplicações](app-protection-based-conditional-access.md)
- [Requerem alteração da palavra-passe](#require-password-change)

Quando os administradores optam por combinar estas opções, podem escolher os seguintes métodos:

- Exigir todos os controlos selecionados (controlo **E** controlo)
- Exigir um dos controlos selecionados (controlo **DE CONTROLO)**

Por predefinição, o Acesso Condicional requer todos os controlos selecionados.

### <a name="require-multi-factor-authentication"></a>Requerem autenticação de vários fatores

A seleção desta caixa de verificação exigirá que os utilizadores realizem a autenticação multi-factor Azure AD. Mais informações sobre a implementação da autenticação multi-factor Azure AD podem ser encontradas no artigo [Planejando uma implementação de autenticação multi-factor Azure AD baseada](../authentication/howto-mfa-getstarted.md)na nuvem .

### <a name="require-device-to-be-marked-as-compliant"></a>Exigir que o dispositivo seja marcado como conforme

As organizações que implementaram o Microsoft Intune podem utilizar as informações devolvidas dos seus dispositivos para identificar dispositivos que satisfaçam requisitos específicos de conformidade. Esta informação de conformidade de política é reencaminhada de Intune para Azure AD onde o Acesso Condicional pode tomar decisões para conceder ou bloquear o acesso aos recursos. Para obter mais informações sobre as políticas de conformidade, consulte o artigo [Definir regras sobre dispositivos para permitir o acesso aos recursos na sua organização utilizando o Intune.](/intune/protect/device-compliance-get-started)

Um dispositivo pode ser marcado como conforme pelo Intune (para qualquer dispositivo OS) ou por um sistema MDM de terceiros para dispositivos Windows 10. Jamf pro é o único sistema de MDM suportado por terceiros. Mais informações sobre a integração podem ser encontradas no artigo, Integre o [Jamf Pro com o Intune para o cumprimento.](/intune/protect/conditional-access-integrate-jamf)

Os dispositivos devem ser registados em Azure AD antes de poderem ser marcados como conformes. Mais informações sobre o registo do dispositivo podem ser encontradas no artigo, [O que é uma identidade do dispositivo.](../devices/overview.md)

### <a name="require-hybrid-azure-ad-joined-device"></a>Requerem o dispositivo híbrido Azure AD

As organizações podem optar por usar a identidade do dispositivo como parte da sua política de Acesso Condicional. As organizações podem exigir que os dispositivos sejam híbridos Azure AD unidos através desta caixa de verificação. Para obter mais informações sobre identidades do dispositivo, veja o artigo [O que é uma identidade do dispositivo?](../devices/overview.md)

Ao utilizar o [fluxo OAuth do código do dispositivo,](../develop/v2-oauth2-device-code.md)o controlo de concessão do dispositivo gerido requerido ou a condição do estado do dispositivo não é suportado. Isto porque o dispositivo que executa a autenticação não pode fornecer o estado do seu dispositivo ao dispositivo que fornece um código e o estado do dispositivo no token está bloqueado ao dispositivo que executa a autenticação. Utilize o controlo de concessão de autenticação multi-factor requerendo.

### <a name="require-approved-client-app"></a>Requera uma aplicação de cliente aprovada

As organizações podem exigir que uma tentativa de acesso às aplicações de nuvem selecionadas tenha de ser feita a partir de uma aplicação de cliente aprovada. Estas aplicações de clientes aprovadas suportam políticas de proteção de [aplicações Intune](/intune/app-protection-policy) independentes de qualquer solução de gestão de dispositivos móveis (MDM).

Para alavancar este controlo de subvenção, o Conditional Access exige que o dispositivo seja registado no Azure Ative Directory, o que requer a utilização de uma aplicação de corretor. A aplicação de corretor pode ser o Microsoft Authenticator para iOS, ou o portal Microsoft Authenticator ou Microsoft Company para dispositivos Android. Se uma aplicação de corretor não for instalada no dispositivo quando o utilizador tentar autenticar, o utilizador é redirecionado para a loja de aplicações apropriada para instalar a aplicação de corretagem necessária.

Esta definição aplica-se às seguintes aplicações iOS e Android:

- Microsoft Azure Information Protection
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Power Automate
- Faturação da Microsoft
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype para Empresas
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Stream
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer
- Microsoft Whiteboard
- Microsoft 365 Administrador

**Observações**

- As aplicações de clientes aprovadas suportam a funcionalidade de gestão de aplicações móveis Intune.
- O requisito **da aplicação do cliente aprovado requerido:**
   - Suporta apenas o iOS e Android para o sistema de plataforma do dispositivo.
   - É necessária uma aplicação de corretor para registar o dispositivo. A aplicação de corretor pode ser o Microsoft Authenticator para iOS, ou o portal Microsoft Authenticator ou Microsoft Company para dispositivos Android.
- O Acesso Condicional não pode considerar o Microsoft Edge no modo InPrivate uma aplicação de cliente aprovada.
- A utilização do Azure AD Application Proxy para permitir que a aplicação móvel Power BI se conecte nas instalações Power BI Report Server não é suportado com políticas de acesso condicional que requerem a aplicação do Microsoft Power BI como uma aplicação de cliente aprovada.

Consulte o artigo, [Como: Exigir aplicações de clientes aprovadas para acesso a aplicações na nuvem com Acesso Condicional](app-based-conditional-access.md) para exemplos de configuração.

### <a name="require-app-protection-policy"></a>Pedir uma política de proteção de aplicações

Na sua política de Acesso Condicional, pode exigir que uma [política de proteção de aplicações Intune](/intune/app-protection-policy) esteja presente na aplicação do cliente antes de o acesso estar disponível para as aplicações de nuvem selecionadas. 

Para alavancar este controlo de subvenção, o Conditional Access exige que o dispositivo seja registado no Azure Ative Directory, o que requer a utilização de uma aplicação de corretor. A aplicação de mediador pode ser o Microsoft Authenticator para iOS ou o Portal da Empresa da Microsoft para dispositivos Android. Se uma aplicação de corretor não for instalada no dispositivo quando o utilizador tentar autenticar, o utilizador é redirecionado para a loja de aplicações para instalar a aplicação do corretor.

Esta definição aplica-se às seguintes aplicações de clientes:

- Microsoft Cortana
- Microsoft Edge
- Microsoft Excel
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Word
- MultiLine para Intune
- Nove e-mail - E-mail & Calendário

> [!NOTE]
> Microsoft Kaizala, Microsoft Skype for Business e Microsoft Visio não suportam a concessão **de política de proteção de aplicações Require.** Se necessitar que estas aplicações funcionem, por favor utilize a concessão **de aplicações aprovadas requerendo** exclusivamente. A utilização da ou cláusula entre as duas subvenções não funcionará para estas três candidaturas.

**Observações**

- As aplicações para a política de proteção de aplicações suportam a funcionalidade de gestão de aplicações móveis Intune com proteção de políticas.
- Os requisitos **de política de proteção de aplicações requerem:**
    - Suporta apenas o iOS e Android para o sistema de plataforma do dispositivo.
    - É necessária uma aplicação de corretor para registar o dispositivo. No iOS, a aplicação de corretor é Microsoft Authenticator e no Android, é a aplicação Intune Company Portal.

Consulte o artigo, [Como: Requer a política de proteção de aplicações e uma aplicação de cliente aprovada para acesso a aplicações na nuvem com Acesso Condicional](app-protection-based-conditional-access.md) para exemplos de configuração.

### <a name="require-password-change"></a>Requerem alteração da palavra-passe 

Quando o risco do utilizador é detetado, utilizando as condições de política de risco do utilizador, os administradores podem optar por alterar de forma segura a palavra-passe utilizando a palavra-passe de autosserviço AZure AD. Se o risco do utilizador for detetado, os utilizadores podem efetuar uma palavra-passe de autosserviço reposta para autorreparação, isto irá encerrar o evento de risco do utilizador para evitar ruídos desnecessários para os administradores. 

Quando um utilizador é solicitado a alterar a sua palavra-passe, primeiro será necessário para completar a autenticação de vários fatores. Deverá certificar-se de que todos os seus utilizadores se registaram para autenticação de vários fatores, para que estejam preparados caso o risco seja detetado para a sua conta.  

> [!WARNING]
> Os utilizadores devem ter-se registado previamente para reiniciar a palavra-passe de autosserviço antes de desencadear a política de risco do utilizador. 

Existe uma restrição de casal quando configura uma política usando o controlo de mudança de senha.  

1. A política deve ser atribuída a "todas as aplicações em nuvem". Isto impede que um intruso utilize uma aplicação diferente para alterar a palavra-passe do utilizador e redefinir o risco de conta, simplesmente assinando numa aplicação diferente. 
1. A alteração da palavra-passe não pode ser utilizada com outros controlos, como exigir um dispositivo compatível.  
1. O controlo de alteração de palavra-passe só pode ser utilizado com a condição de atribuição de utilizadores e grupos, a condição de atribuição de aplicações em nuvem (que deve ser definida para todos) e as condições de risco do utilizador. 

### <a name="terms-of-use"></a>Termos de utilização

Se a sua organização criou termos de utilização, opções adicionais podem ser visíveis sob controlo de subvenção. Estas opções permitem que os administradores exijam o reconhecimento dos termos de utilização como condição de acesso aos recursos protegidos pela política. Mais informações sobre os termos de utilização podem ser encontradas no artigo, [termos de utilização do Azure Ative Directory](terms-of-use.md).

## <a name="next-steps"></a>Passos seguintes

- [Acesso Condicional: Controlos de sessão](concept-conditional-access-session.md)

- [Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

- [Modo só de relatório](concept-conditional-access-report-only.md)
