---
title: Controlos de subvenções na política de acesso condicional - Diretório Ativo Azure
description: O que são controlos de subvenções numa política de acesso condicional da AD Azure
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02ec8dace971cd4dc1407c9e8d20839504c9ecc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331845"
---
# <a name="conditional-access-grant"></a>Acesso Condicional: Subvenção

No âmbito de uma política de Acesso Condicional, um administrador pode utilizar os controlos de acesso para conceder ou bloquear o acesso aos recursos.

![Política de Acesso Condicional com controlo de subvenções que requer a autenticação de vários fatores](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Bloquear acesso

O Bloco tem em conta quaisquer atribuições e impede o acesso com base na configuração da política de Acesso Condicional.

O bloco é um controlo poderoso que deve ser controlado com conhecimento apropriado. É algo que os administradores devem utilizar o [modo apenas para](concept-conditional-access-report-only.md) testar antes de ativar.

## <a name="grant-access"></a>Conceder acesso

Os administradores podem optar por impor um ou mais controlos ao conceder acesso. Estes controlos incluem as seguintes opções: 

- [Exigir autenticação multi-factor (autenticação azure multi-factor)](../authentication/concept-mfa-howitworks.md)
- [Exigir que o dispositivo seja marcado como conforme (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [Requerer dispositivo híbrido Azure AD](../devices/concept-azure-ad-join-hybrid.md)
- [Exigir aplicação de cliente aprovada](app-based-conditional-access.md)
- [Pedir uma política de proteção de aplicações](app-protection-based-conditional-access.md)

Quando os administradores optarem por combinar estas opções, podem escolher os seguintes métodos:

- Exigir todos os comandos selecionados (controlo **e** controlo)
- Exigir um dos comandos selecionados (controlo **OU** controlo)

Por predefinição, o Acesso Condicional requer todos os controlos selecionados.

### <a name="require-multi-factor-authentication"></a>Exigir autenticação de vários fatores

A seleção desta caixa de verificação exigirá que os utilizadores realizem a autenticação de vários fatores Do Azure. Mais informações sobre a implementação da autenticação de multi-factores azure podem ser encontradas no artigo [Planejando uma implementação de autenticação azure multi-factor baseada na nuvem.](../authentication/howto-mfa-getstarted.md)

### <a name="require-device-to-be-marked-as-compliant"></a>Exigir que o dispositivo seja marcado como conforme

As organizações que implementaram o Microsoft Intune podem utilizar as informações devolvidas dos seus dispositivos para identificar dispositivos que satisfaçam os requisitos específicos de conformidade. Esta informação de conformidade política é transmitida de Intune para AD Azure, onde o Acesso Condicional pode tomar decisões para conceder ou bloquear o acesso aos recursos. Para obter mais informações sobre as políticas de conformidade, consulte o artigo [Definir regras sobre dispositivos para permitir o acesso aos recursos na sua organização usando o Intune](/intune/protect/device-compliance-get-started).

Um dispositivo pode ser marcado como conforme por Intune (para qualquer dispositivo OS) ou por um sistema DEM de terceiros para dispositivos Windows 10. O Jamf Pro é o único sistema MDM suportado por terceiros. Mais informações sobre integração podem ser encontradas no artigo, [Integrao Jamf Pro com Intune para o cumprimento.](/intune/protect/conditional-access-integrate-jamf)

Os dispositivos devem ser registados em Azure AD antes de poderem ser marcados como conformes. Mais informações sobre o registo do dispositivo podem ser encontradas no artigo, O que é uma identidade do [dispositivo.](../devices/overview.md)

### <a name="require-hybrid-azure-ad-joined-device"></a>Requerer dispositivo híbrido Azure AD

As organizações podem optar por utilizar a identidade do dispositivo como parte da sua política de Acesso Condicional. As organizações podem exigir que os dispositivos sejam híbridos Azure AD unidos usando esta caixa de verificação. Para obter mais informações sobre identidades do dispositivo, consulte o artigo O que é uma identidade de [dispositivo?](../devices/overview.md)

### <a name="require-approved-client-app"></a>Exigir aplicação de cliente aprovada

As organizações podem exigir que uma tentativa de acesso às aplicações de nuvem selecionadas seja feita a partir de uma aplicação de cliente aprovada. Estas aplicações de clientes aprovadas suportam políticas de proteção de [aplicações Intune](/intune/app-protection-policy) independentes de qualquer solução de gestão de dispositivos móveis (MDM).

Para alavancar este controlo de subvenções, o Acesso Condicional exige que o dispositivo seja registado no Diretório Ativo do Azure, que requer a utilização de uma aplicação de corretor. A aplicação de mediador pode ser o Microsoft Authenticator para iOS ou o Portal da Empresa da Microsoft para dispositivos Android. Se uma aplicação de corretor não for instalada no dispositivo quando o utilizador tenta autenticar, o utilizador é redirecionado para a loja de aplicações para instalar a aplicação de corretagem.

Esta definição aplica-se às seguintes aplicações iOS e Android:

- Proteção de Informação do Microsoft Azure
- Reservas da Microsoft
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Browser Gerido do Microsoft Intune
- Faturação da Microsoft
- Microsoft Kaizala
- Lançador microsoft
- Microsoft Office
- Microsoft Office Hub
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Planejador da Microsoft
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype para Empresas
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer
- Placa branca da Microsoft

**Observações**

- As aplicações de clientes aprovadas suportam a funcionalidade de gestão de aplicações móveis Intune.
- O requisito da **aplicação de cliente aprovado Requer:**
   - Apenas suporta o iOS e Android para o estado da plataforma do dispositivo.
   - É necessária uma aplicação de corretor para registar o dispositivo. No iOS, a aplicação de corretor é o Microsoft Authenticator e no Android, é a aplicação Intune Company Portal.
- O Acesso Condicional não pode considerar o Microsoft Edge no modo InPrivate uma aplicação de cliente aprovada.

Consulte o artigo, [Como: Exigir aplicações de clientes aprovadas para acesso](app-based-conditional-access.md) a aplicações na nuvem com Acesso Condicional para exemplos de configuração.

### <a name="require-app-protection-policy"></a>Pedir uma política de proteção de aplicações

Na sua política de Acesso Condicional, pode exigir que esteja presente uma política de proteção de [aplicações Intune](/intune/app-protection-policy) na aplicação do cliente antes de o acesso estar disponível para as aplicações na nuvem selecionadas. 

Para alavancar este controlo de subvenções, o Acesso Condicional exige que o dispositivo seja registado no Diretório Ativo do Azure, que requer a utilização de uma aplicação de corretor. A aplicação de mediador pode ser o Microsoft Authenticator para iOS ou o Portal da Empresa da Microsoft para dispositivos Android. Se uma aplicação de corretor não for instalada no dispositivo quando o utilizador tenta autenticar, o utilizador é redirecionado para a loja de aplicações para instalar a aplicação de corretagem.

Esta definição aplica-se às seguintes aplicações de clientes:

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Planejador da Microsoft

**Observações**

- Aplicativos para política de proteção de aplicações suportam a funcionalidade de gestão de aplicações móveis Intune com proteção de políticas.
- Os requisitos da política de **proteção de aplicações Exigem:**
    - Apenas suporta o iOS e Android para o estado da plataforma do dispositivo.
    - É necessária uma aplicação de corretor para registar o dispositivo. No iOS, a aplicação de corretor é o Microsoft Authenticator e no Android, é a aplicação Intune Company Portal.

Consulte o artigo, Como: Exigir a política de [proteção de aplicações e uma aplicação de cliente aprovada para acesso](app-protection-based-conditional-access.md) a aplicações na nuvem com Acesso Condicional para exemplos de configuração.

### <a name="terms-of-use"></a>Termos de utilização

Se a sua organização tiver criado termos de utilização, opções adicionais podem ser visíveis sob controlo de subvenções. Estas opções permitem que os administradores exijam o reconhecimento dos termos de utilização como condição de acesso aos recursos protegidos pela política. Mais informações sobre os termos de utilização podem ser encontradas no artigo, [Azure Ative Diretório termos de utilização.](terms-of-use.md)

## <a name="next-steps"></a>Passos seguintes

- [Acesso Condicional: Controlos de sessão](concept-conditional-access-session.md)

- [Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

- [Modo só de relatório](concept-conditional-access-report-only.md)
