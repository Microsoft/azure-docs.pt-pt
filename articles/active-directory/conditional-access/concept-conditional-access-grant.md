---
title: Controlos de subvenções na política de acesso condicional - Diretório Ativo Azure
description: O que são controlos de subvenções numa política de acesso condicional da AD Azure
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89063cc8131c28f20153c6fe9b4c71b58794e609
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192119"
---
# <a name="conditional-access-grant"></a>Acesso Condicional: Subvenção

No âmbito de uma política de Acesso Condicional, um administrador pode utilizar os controlos de acesso para conceder ou bloquear o acesso aos recursos.

![Política de Acesso Condicional com controlo de subvenções que requer a autenticação de vários fatores](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Acesso ao bloco

O Bloco tem em conta quaisquer atribuições e impede o acesso com base na configuração da política de Acesso Condicional.

O bloco é um controlo poderoso que deve ser controlado com conhecimento apropriado. É algo que os administradores devem utilizar o [modo apenas para](concept-conditional-access-report-only.md) testar antes de ativar.

## <a name="grant-access"></a>Conceder acesso

Os administradores podem optar por impor um ou mais controlos ao conceder acesso. Estes controlos incluem as seguintes opções: 

- [Exigir autenticação multi-factor (autenticação azure multi-factor)](../authentication/concept-mfa-howitworks.md)
- [Exigir que o dispositivo seja marcado como conforme (Microsoft Intune)](https://docs.microsoft.com/intune/protect/device-compliance-get-started)
- [Requerer dispositivo híbrido Azure AD](../devices/concept-azure-ad-join-hybrid.md)
- [Exigir aplicação de cliente aprovada](app-based-conditional-access.md)
- [Exigir política de proteção de aplicativos](app-protection-based-conditional-access.md)

Quando os administradores optarem por combinar estas opções, podem escolher os seguintes métodos:

- Exigir todos os comandos selecionados (controlo **e** controlo)
- Exigir um dos comandos selecionados (controlo **OU** controlo)

Por predefinição, o Acesso Condicional requer todos os controlos selecionados.

### <a name="require-multi-factor-authentication"></a>Exigir autenticação de vários fatores

A seleção desta caixa de verificação exigirá que os utilizadores realizem a autenticação de vários fatores Do Azure. Mais informações sobre a implementação da autenticação de multi-factores azure podem ser encontradas no artigo [Planejando uma implementação de autenticação azure multi-factor baseada na nuvem.](../authentication/howto-mfa-getstarted.md)

### <a name="require-device-to-be-marked-as-compliant"></a>Exigir que o dispositivo seja marcado como conforme

As organizações que implementaram o Microsoft Intune podem utilizar as informações devolvidas dos seus dispositivos para identificar dispositivos que satisfaçam os requisitos específicos de conformidade. Esta informação de conformidade política é transmitida de Intune para AD Azure, onde o Acesso Condicional pode tomar decisões para conceder ou bloquear o acesso aos recursos. Para obter mais informações sobre as políticas de conformidade, consulte o artigo [Definir regras sobre dispositivos para permitir o acesso aos recursos na sua organização usando o Intune](https://docs.microsoft.com/intune/protect/device-compliance-get-started).

### <a name="require-hybrid-azure-ad-joined-device"></a>Requerer dispositivo híbrido Azure AD

As organizações podem optar por utilizar a identidade do dispositivo como parte da sua política de Acesso Condicional. As organizações podem exigir que os dispositivos sejam híbridos Azure AD unidos usando esta caixa de verificação. Para obter mais informações sobre identidades do dispositivo, consulte o artigo O que é uma identidade de [dispositivo?](../devices/overview.md)

### <a name="require-approved-client-app"></a>Requer aplicação aprovada do cliente

As organizações podem exigir que uma tentativa de acesso às aplicações de nuvem selecionadas seja feita a partir de uma aplicação de cliente aprovada.

Esta definição aplica-se às seguintes aplicações de clientes:

- Proteção de Informação do Microsoft Azure
- Reservas da Microsoft
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Navegador Gerido Intune microsoft
- Faturação da Microsoft
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype para negócios
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer

**Observações**

- As aplicações de clientes aprovadas suportam a funcionalidade de gestão de aplicações móveis Intune.
- O requisito da **aplicação de cliente aprovado Requer:**
   - Apenas suporta o iOS e Android para o estado da plataforma do dispositivo.
- O Acesso Condicional não pode considerar o Microsoft Edge no modo InPrivate uma aplicação de cliente aprovada.

### <a name="require-app-protection-policy"></a>Pedir uma política de proteção de aplicações

Na sua política de Acesso Condicional, pode exigir que esteja presente uma política de proteção de aplicações na aplicação do cliente antes de o acesso estar disponível para as aplicações na nuvem selecionadas. 

![Controlar o acesso com a política de proteção de aplicações](./media/technical-reference/22.png)

Esta definição aplica-se às seguintes aplicações de clientes:

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**Observações**

- Aplicativos para política de proteção de aplicações suportam a funcionalidade de gestão de aplicações móveis Intune com proteção de políticas.
- Os requisitos da política de **proteção de aplicações Exigem:**
    - Apenas suporta o iOS e Android para o estado da plataforma do dispositivo.

## <a name="next-steps"></a>Passos seguintes

- [Acesso Condicional: Controlos de sessão](concept-conditional-access-session.md)

- [Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

- [Modo apenas de relatório](concept-conditional-access-report-only.md)
