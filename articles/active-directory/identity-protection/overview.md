---
title: O Azure Active Directory Identity Protection | Documentos da Microsoft
description: Saiba como o Azure AD Identity Protection permite-lhe limitar a capacidade de um atacante explore uma identidade comprometida ou dispositivo e para proteger uma identidade ou um dispositivo que foi anteriormente suspeita ou conhecido por ser comprometido.
services: active-directory
keywords: proteção de identidade do Azure Active Directory, descoberta de aplicações na cloud, gestão de aplicações, a segurança, a risco, a nível de risco, a vulnerabilidade, a política de segurança
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d9b0c80522a988447b2753953c0c51b058fdd43
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "66015218"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>O que é o Azure Active Directory Identity Protection?

O Azure Active Directory Identity Protection permite às organizações configurar respostas automáticas para detetado ações suspeitas relacionadas com identidades de utilizador.

## <a name="get-started"></a>Introdução

Microsoft tem protegido identidades baseadas na cloud há mais de uma década. Com o Azure Active Directory Identity Protection, no seu ambiente, pode utilizar os mesmos sistemas de proteção, a Microsoft utiliza para proteger as identidades.

A grande maioria das falhas de segurança ocorrem quando os atacantes obtêm acesso a um ambiente, por roubo de identidade de um utilizador. Ao longo dos anos, os atacantes têm se tornado cada vez mais eficazes em tirar partido das violações de terceiros e utilize ataques de phishing sofisticado. Assim que um atacante obtiver acesso a contas de utilizador com privilégios baixos, é relativamente fácil para obter acesso aos recursos de importantes da empresa através de movimento lateral.

Como conseqüência disso, terá de:

- Proteger as identidades de todos, independentemente de seu nível de privilégio

- Proativamente impedir que as identidades comprometidas a ser utilizada

Detetar identidades comprometidas não é tarefa fácil. O Azure Active Directory utiliza algoritmos de aprendizagem automática adaptáveis e heurística para detetar anomalias e incidentes suspeitos que indiquem potencialmente comprometidos identidades. Utilizando estes dados, o Identity Protection gera relatórios e alertas que lhe permite avaliar os problemas detetados e tomar apropriado de atenuação ou ações de remediação.

O Azure Active Directory Identity Protection é mais do que uma monitorização e a ferramenta de relatório. Para proteger as identidades da sua organização, pode configurar as políticas baseadas em risco, que respondam automaticamente aos problemas detetados quando for atingido um nível de risco especificado. Estas políticas, além de outros Controles de acesso condicional fornecidos pelo Azure Active Directory e [Enterprise Mobility + Security](https://docs.microsoft.com/enterprise-mobility-security/) (EMS), pode automaticamente bloquear ou iniciar ações de remediação adaptável, incluindo reposições de palavra-passe e a imposição de multi-factor authentication.

### <a name="identity-protection-capabilities"></a>Capacidades de proteção de identidade

**Detetar vulnerabilidades e contas de risco:**  

- Fornecendo recomendações personalizadas para melhorar a postura de segurança geral, realce de vulnerabilidades
- Calcular os níveis de risco de início de sessão
- Calcular os níveis de risco de utilizador

**Investigar eventos de risco:**

- Enviar notificações para eventos de risco
- Investigar eventos de risco com informações relevantes e contextuais
- Fornecimento de fluxos de trabalho básicos para controlar as investigações
- Proporciona acesso fácil para ações de remediação, tais como a reposição de palavra-passe

**Políticas de acesso condicional baseado no risco:**

- Política para mitigar o risco de inícios de sessão ao inícios de sessão de bloquear ou exigir que os desafios de autenticação multifator
- Política para bloquear ou contas de utilizador de risco de seguro
- Política para exigir que os utilizadores se registarem na autenticação multifator

## <a name="identity-protection-roles"></a>Funções de proteção de identidade

O balanceamento de carga as atividades de gestão em torno de sua implementação do Identity Protection, pode atribuir várias funções. O Azure AD Identity Protection suporta 3 funções de diretório:

| Função | Pode fazer | Não é possível fazer |
| :-- | --- | --- |
| Administrador Global | Acesso total ao Identity Protection, carregar Identity Protection| |
| Administrador de segurança | Acesso total ao Identity Protection | Carregar Identity Protection, repor palavras-passe para um utilizador |
| Leitor de segurança | Acesso só de leitura ao Identity Protection | Carregar Identity Protection, remediar utilizadores, configurar políticas, repor palavras-passe |

Para obter mais detalhes, consulte [atribuir funções de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)

## <a name="detection"></a>Deteção

### <a name="vulnerabilities"></a>Vulnerabilidades

O Azure Active Directory Identity Protection analisa a configuração e detecta vulnerabilidades que podem ter um impacto sobre as identidades dos utilizadores. Para obter mais detalhes, consulte [vulnerabilidades detetadas pelo Azure Active Directory Identity Protection](vulnerabilities.md).

### <a name="risk-events"></a>Eventos de risco

O Azure Active Directory utiliza algoritmos de aprendizagem automática adaptáveis e heurística para detetar as ações suspeitas relacionadas com as identidades dos utilizadores. O sistema cria um registo para cada ação suspeita detetada. Estes registos também são conhecidos como eventos de risco.  
Para obter mais detalhes, veja [Eventos de risco do Azure Active Directory](../active-directory-identity-protection-risk-events.md).

## <a name="investigation"></a>Investigação

Sua viagem por meio de proteção de identidade normalmente começa com o dashboard do Identity Protection.

![Remediação](./media/overview/1000.png "remediação")

O dashboard dá-lhe acesso a:

- Por exemplo, os relatórios **utilizadores sinalizados para risco**, **eventos de risco** e **vulnerabilidades**
- Definições, tais como a configuração da sua **políticas de segurança**, **notificações** e **registo de autenticação multifator**

Normalmente é seu ponto de partida para a investigação, o que é o processo de revisão das atividades, registos e outras informações relevantes relacionados com um evento de risco para decidir se os passos de remediação ou atenuação são necessários, e como foi a identidade comprometido e compreender como a identidade comprometida foi utilizada.

Pode vincular suas atividades de investigação para o [notificações](notifications.md) Azure Active Directory Protection envia por e-mail.

## <a name="policies"></a>Políticas

Para implementar respostas automáticas, Azure Active Directory Identity Protection fornece três diretivas:

- [Política de registo de autenticação multifator](howto-mfa-policy.md)
- [Política de risco do utilizador](howto-user-risk-policy.md)
- [Política de risco de início de sessão](howto-sign-in-risk-policy.md)

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="next-steps"></a>Passos Seguintes

- [Channel 9: O Azure AD e mostrar de identidade: Pré-visualização de proteção de identidade](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
- [Ativar o Azure Active Directory Identity Protection](enable.md)
