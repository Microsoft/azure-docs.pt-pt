---
title: Azure Active Directory Identity Protection | Microsoft Docs
description: Saiba como Azure AD Identity Protection permite limitar a capacidade de um invasor explorar uma identidade ou um dispositivo comprometido e proteger uma identidade ou um dispositivo que foi suspeito ou comprometido anteriormente.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 01/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b89cab41061376fc1d8b4cbffc8fe87b9677688
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125681"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>O que é Azure Active Directory Identity Protection?

O Azure Active Directory [Identity](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis#terminology) Protection permite que as organizações configurem respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuário.

## <a name="get-started"></a>Introdução

A Microsoft protege identidades baseadas em nuvem por mais de uma década. Com Azure Active Directory Identity Protection, em seu ambiente, você pode usar os mesmos sistemas de proteção que a Microsoft usa para proteger identidades.

A grande maioria das violações de segurança ocorre quando os invasores têm acesso a um ambiente, roubando a identidade de um usuário. Ao longo dos anos, os invasores se tornaram cada vez mais eficazes em aproveitar as violações de terceiros e usar ataques de phishing sofisticados. Assim que um invasor obtiver acesso a contas de usuário com privilégios baixos, será relativamente fácil obter acesso a recursos importantes da empresa por meio da movimentação lateral.

Como consequência disso, você precisa:

- Proteger todas as identidades, independentemente do nível de privilégio
- Impedir proativamente que identidades comprometidas sejam abusos

Descobrir identidades comprometidas não é uma tarefa fácil. O Azure Active Directory usa algoritmos de aprendizado de máquina adaptáveis e heurística para detectar anomalias e incidentes suspeitos que indicam identidades potencialmente comprometidas. Usando esses dados, a proteção de identidade gera relatórios e alertas que permitem avaliar os problemas detectados e tomar as ações apropriadas de mitigação ou correção.

Azure Active Directory Identity Protection é mais do que uma ferramenta de monitoramento e relatório. Para proteger as identidades de sua organização, você pode configurar políticas baseadas em risco que respondem automaticamente a problemas detectados quando um nível de risco especificado é atingido. Essas políticas, além de outros controles de acesso condicional fornecidos pelo Azure Active Directory e pelo [Enterprise Mobility + Security](https://docs.microsoft.com/enterprise-mobility-security/) (EMS), podem bloquear ou iniciar automaticamente as ações de correção adaptável, incluindo redefinições de senha e imposição da autenticação multifator.

### <a name="identity-protection-capabilities"></a>Recursos de proteção de identidade

**Detectando vulnerabilidades e contas arriscadas:**  

- Fornecendo recomendações personalizadas para melhorar a postura geral de segurança realçando vulnerabilidades
- Calculando níveis de risco de entrada
- Calculando os níveis de risco do usuário

**Investigando as detecções de risco:**

- Enviando notificações para detecções de risco
- Investigando as detecções de risco usando informações relevantes e contextuais
- Fornecendo fluxos de trabalho básicos para controlar investigações
- Fornecendo acesso fácil a ações de correção, como redefinição de senha

**Políticas de acesso condicional com base em risco:**

- Política para mitigar entradas arriscadas bloqueando entradas ou exigindo desafios de autenticação multifator
- Política para bloquear ou proteger contas de usuário arriscadas
- Política para exigir que os usuários se registrem para autenticação multifator

## <a name="identity-protection-roles"></a>Funções de proteção de identidade

Para balancear a carga das atividades de gerenciamento em relação à implementação da proteção de identidade, você pode atribuir várias funções. O Azure AD Identity Protection dá suporte a três funções de diretório:

| Role | Pode fazer | Não é possível fazer |
| :-- | --- | --- |
| Administrador Global | Acesso completo à proteção de identidade, proteção de identidade integrada| |
| Administrador de segurança | Acesso completo à proteção de identidade | Integração da proteção de identidade, redefinição de senhas para um usuário |
| Leitor de segurança | Acesso somente leitura à proteção de identidade | Integração da proteção de identidade, correção de usuários, configuração de políticas, redefinição de senhas |

Para obter mais detalhes, consulte [atribuindo funções de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)

## <a name="detection"></a>Deteção

### <a name="vulnerabilities"></a>Vulnerabilidades

Azure Active Directory Identity Protection analisa sua configuração e detecta vulnerabilidades que podem afetar as identidades do usuário. Para obter mais detalhes, consulte [vulnerabilidades detectadas por Azure Active Directory Identity Protection](vulnerabilities.md).

### <a name="risk-detections"></a>Detecções de risco

O Azure Active Directory usa algoritmos de aprendizado de máquina adaptáveis e heurística para detectar ações suspeitas relacionadas às identidades do usuário. O sistema cria um registro para cada ação suspeita detectada. Esses registros também são conhecidos como detecções de risco.  
Para obter mais detalhes, consulte [Azure Active Directory as detecções de risco](../active-directory-identity-protection-risk-events.md).

## <a name="investigation"></a>Investigação

Sua jornada por meio da proteção de identidade normalmente começa com o painel de proteção de identidade.

![Correção](./media/overview/1000.png "Correção")

O dashboard dá-lhe acesso a:

- Relatórios como **usuários sinalizados para risco**, **detecções de risco** e **vulnerabilidades**
- Configurações como a configuração de suas **políticas de segurança**, **notificações** e **registro de autenticação** multifator

Normalmente é seu ponto de partida para investigação, que é o processo de revisar as atividades, os logs e outras informações relevantes relacionadas a uma detecção de risco para decidir se as etapas de correção ou mitigação são necessárias e como a identidade foi comprometido e entender como a identidade comprometida foi usada.

Você pode vincular suas atividades de investigação às [notificações](notifications.md) Azure Active Directory proteção de envios por email.

## <a name="policies"></a>Políticas

Para implementar respostas automatizadas, Azure Active Directory Identity Protection fornece três políticas:

- [Política de registro da autenticação multifator](howto-mfa-policy.md)
- [Política de risco do usuário](howto-user-risk-policy.md)
- [Política de risco de entrada](howto-sign-in-risk-policy.md)

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="next-steps"></a>Passos Seguintes

- [Canal 9: Azure AD e identidade mostram: Versão prévia da proteção de identidade](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
- [Habilitando Azure Active Directory Identity Protection](enable.md)
