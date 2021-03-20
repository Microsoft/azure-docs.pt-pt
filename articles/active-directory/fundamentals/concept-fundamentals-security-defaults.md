---
title: Incumprimentos de segurança do Azure Ative Directory
description: Políticas de incumprimento de segurança que ajudam a proteger organizações de ataques comuns em Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: efaf6060c0b09e071546038d9e30f2c8065059e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98600127"
---
# <a name="what-are-security-defaults"></a>O que são falhas de segurança?

Gerir a segurança pode ser difícil com ataques comuns relacionados com a identidade, como spray de palavras-passe, repetição e phishing tornando-se cada vez mais popular. As falhas de segurança facilitam a proteção da sua organização contra estes ataques com definições de segurança pré-configuradas:

- Exigindo que todos os utilizadores se registem para autenticação multi-factor Azure AD.
- Exigindo que os administradores realizem a autenticação de vários fatores.
- Bloqueando protocolos de autenticação de legados.
- Exigir que os utilizadores realizem a autenticação de vários fatores quando necessário.
- Proteger atividades privilegiadas como o acesso ao portal Azure.

![Screenshot do portal Azure com o toggle para permitir incumprimentos de segurança](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
Mais detalhes sobre o porquê de estarem a ser disponibilizados incumprimentos de segurança podem ser encontrados no post de blogue de Alex Weinert, [introduzindo falhas de segurança](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414).

## <a name="availability"></a>Disponibilidade

A Microsoft está a disponibilizar falhas de segurança a todos. O objetivo é garantir que todas as organizações tenham um nível básico de segurança, sem custos adicionais. Liga-se as falhas de segurança no portal Azure. Se o seu inquilino foi criado em ou depois de 22 de outubro de 2019, é possível que os incumprimentos de segurança já estejam habilitados no seu inquilino. Num esforço para proteger todos os nossos utilizadores, estão a ser lançados incumprimentos de segurança a todos os novos inquilinos criados.

### <a name="whos-it-for"></a>Para quem é?

- Se é uma organização que quer aumentar a sua postura de segurança mas não sabe como ou por onde começar, os incumprimentos de segurança são para si.
- Se você é uma organização que utiliza o nível gratuito de licenciamento do Azure Ative Directory, os incumprimentos de segurança são para si.

### <a name="who-should-use-conditional-access"></a>Quem deve usar acesso condicional?

- Se é uma organização que atualmente utiliza políticas de Acesso Condicional para reunir sinais, tomar decisões e impor políticas organizacionais, os incumprimentos de segurança provavelmente não são adequados para si. 
- Se você é uma organização com licenças Azure Ative Directory Premium, os incumprimentos de segurança provavelmente não são adequados para si.
- Se a sua organização tiver requisitos de segurança complexos, deve considerar o Acesso Condicional.

## <a name="policies-enforced"></a>Políticas aplicadas

### <a name="unified-multi-factor-authentication-registration"></a>Registo unificado de autenticação multi-factor

Todos os utilizadores do seu inquilino devem inscrever-se para autenticação multi-factor (MFA) sob a forma da Autenticação Multi-Factor Azure AD. Os utilizadores têm 14 dias para se inscreverem na Autenticação Multi-Factor AZure AD utilizando a aplicação Microsoft Authenticator. Após os 14 dias que passaram, o utilizador não poderá fazer o seu registo até que o registo esteja concluído. O período de 14 dias de um utilizador começa após o seu primeiro início de sposição interativo bem-sucedido após permitir falhas de segurança.

### <a name="protecting-administrators"></a>Proteger administradores

Os utilizadores com acesso privilegiado têm acesso acrescido ao seu ambiente. Devido ao poder que estas contas têm, deve tratá-las com especial cuidado. Um método comum para melhorar a proteção das contas privilegiadas consiste em exigir uma verificação mais forte da conta para a inscrição. No Azure AD, você pode obter uma verificação de conta mais forte, exigindo a autenticação de vários fatores.

Após o registo com a autenticação multi-factor Azure AD, serão necessárias as seguintes nove funções de administrador da AD Azure para a realização de autenticação adicional sempre que assinarem:

- Administrador global
- Administrador do SharePoint
- Administrador do Exchange
- Administrador de acesso condicional
- Administrador de segurança
- Administrador da Helpdesk
- Administrador de faturação
- Administrador de utilizadores
- Administrador de autenticação

### <a name="protecting-all-users"></a>Proteger todos os utilizadores

Tendemos a pensar que as contas do administrador são as únicas contas que precisam de camadas extras de autenticação. Os administradores têm um amplo acesso a informações sensíveis e podem fazer alterações nas definições de subscrição. Mas os atacantes frequentemente visam os utilizadores finais. 

Após estes agressores terem acesso, podem solicitar acesso a informações privilegiadas em nome do titular da conta original. Podem até descarregar todo o diretório para realizar um ataque de phishing a toda a sua organização. 

Um método comum para melhorar a proteção para todos os utilizadores é exigir uma forma mais forte de verificação de conta, como a Autenticação Multi-Factor, para todos. Depois de os utilizadores completarem o registo de autenticação multi-factor, serão solicitados para autenticação adicional sempre que necessário. Os utilizadores serão solicitados principalmente quando autenticarem usando um novo dispositivo ou aplicação, ou quando realizam funções e tarefas críticas. Esta funcionalidade protege todas as aplicações registadas com Azure AD, incluindo aplicações SaaS.

### <a name="blocking-legacy-authentication"></a>Bloquear a autenticação do legado

Para facilitar o acesso dos seus utilizadores às suas aplicações na nuvem, o Azure AD suporta uma variedade de protocolos de autenticação, incluindo a autenticação antiga. *Autenticação de legado* é um termo que se refere a um pedido de autenticação feito por:

- Clientes que não utilizam a autenticação moderna (por exemplo, um cliente do Office 2010).
- Qualquer cliente que utilize protocolos de correio antigos como IMAP, SMTP ou POP3.

Hoje, a maioria das tentativas comprometedoras de inscrição provém da autenticação do legado. A autenticação do legado não suporta a autenticação multi-factor. Mesmo que tenha uma política de autenticação multi-factor ativada no seu diretório, um intruso pode autenticar utilizando um protocolo mais antigo e contornar a Autenticação Multi-Factor. 

Após o incumprimento de segurança ser ativado no seu inquilino, todos os pedidos de autenticação feitos por um protocolo mais antigo serão bloqueados. As falhas de segurança bloqueiam a autenticação básica do Exchange Ative Sync.

> [!WARNING]
> Antes de ativar as falhas de segurança, certifique-se de que os seus administradores não estão a utilizar protocolos de autenticação mais antigos. Para mais informações, consulte [Como afastar-se da autenticação do legado.](concept-fundamentals-block-legacy-authentication.md)

- [Como configurar um dispositivo ou aplicação multifunções para enviar e-mail usando o Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)

### <a name="protecting-privileged-actions"></a>Proteger ações privilegiadas

As organizações utilizam uma variedade de serviços Azure geridos através da API gestora de recursos Azure, incluindo:

- Portal do Azure 
- Azure PowerShell 
- CLI do Azure

Utilizar o Azure Resource Manager para gerir os seus serviços é uma ação altamente privilegiada. O Azure Resource Manager pode alterar configurações em todo o inquilino, tais como definições de serviço e faturação de subscrição. A autenticação de um único fator é vulnerável a uma variedade de ataques como phishing e spray de senha. 

É importante verificar a identidade dos utilizadores que pretendem aceder ao Azure Resource Manager e atualizar as configurações. Verifique a sua identidade exigindo autenticação adicional antes de permitir o acesso.

Depois de ativar falhas de segurança no seu inquilino, qualquer utilizador que aceda ao portal Azure, Azure PowerShell ou ao CLI Azure terá de completar a autenticação adicional. Esta política aplica-se a todos os utilizadores que acedam ao Azure Resource Manager, quer sejam administradores ou utilizadores. 

> [!NOTE]
> Os inquilinos online do Exchange Online antes de 2017 têm a autenticação moderna desativada por padrão. Para evitar a possibilidade de um ciclo de login durante a autenticação através destes inquilinos, deve [permitir a autenticação moderna.](/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)

> [!NOTE]
> A conta de sincronização Azure AD Connect está excluída de incumprimentos de segurança e não será solicitada a registar-se ou realizar a autenticação de vários fatores. As organizações não devem utilizar esta conta para outros fins.

## <a name="deployment-considerations"></a>Considerações sobre implementação

As seguintes considerações adicionais estão relacionadas com a implementação de incumprimentos de segurança.

### <a name="authentication-methods"></a>Métodos de autenticação

Estes predefinidos de segurança gratuitos permitem o registo e utilização da Autenticação Multi-Factor AZure AD **utilizando apenas a aplicação Microsoft Authenticator utilizando notificações.** O Acesso Condicional permite a utilização de qualquer método de autenticação que o administrador opte por ativar.

| Método | Predefinições de segurança | Acesso Condicional |
| --- | --- | --- |
| Notificação através de aplicação móvel | X | X |
| Código de verificação a partir de aplicativo móvel ou token de hardware | X** | X |
| Mensagem de texto para telefone |   | X |
| Chamada para telefone |   | X |
| Palavras-passe da aplicação |   | X*** |

- ** Os utilizadores podem utilizar códigos de verificação a partir da aplicação Microsoft Authenticator, mas só podem registar-se através da opção de notificação.
- As palavras-passe da aplicação só estão disponíveis em MFA por utilizador com cenários de autenticação legado apenas se forem ativadas por administradores.

### <a name="disabled-mfa-status"></a>Estado de MFA desativado

Se a sua organização for um utilizador anterior da autenticação multi-factor Azure AD baseada no utilizador, não fique alarmado por não ver os utilizadores num estado **ativado** ou **forçado** se olhar para a página de estado multi-factor Auth. **Desativado** é o estado adequado para os utilizadores que estão a utilizar falhas de segurança ou autenticação multi-factor Azure AD baseada em acesso condicional.

### <a name="conditional-access"></a>Acesso Condicional

Pode utilizar o Acesso Condicional a políticas de configuração semelhantes a falhas de segurança, mas com mais granularidade, incluindo exclusões de utilizadores, que não estão disponíveis em incumprimentos de segurança. Se estiver a utilizar o Acesso Condicional e tiver políticas de Acesso Condicional ativadas no seu ambiente, os padrãos de segurança não estarão disponíveis para si. Se tiver uma licença que forneça acesso condicional mas não tenha nenhuma política de Acesso Condicional ativada no seu ambiente, é bem-vindo a utilizar padrão de segurança até ativar políticas de Acesso Condicional. Mais informações sobre o licenciamento Azure AD podem ser encontradas na página de preços da [AD Azure](https://azure.microsoft.com/pricing/details/active-directory/).

![Mensagem de aviso de que pode ter incumprimentos de segurança ou acesso condicional não tanto](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Aqui estão guias passo a passo sobre como pode utilizar o Acesso Condicional a políticas equivalentes de configuração às políticas ativadas por incumprimentos de segurança:

- [Exigir MFA para administradores](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [Exigir MFA para a gestão do Azure](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Bloquear a autenticação do legado](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [Pedir MFA para todos os utilizadores](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Requer registo MFA Azure AD](../identity-protection/howto-identity-protection-configure-mfa-policy.md) - Requer a azure AD Identity Protection parte do Azure AD Premium P2.

## <a name="enabling-security-defaults"></a>Permitir incumprimentos de segurança

Para ativar falhas de segurança no seu diretório:

1. Inscreva-se no [portal Azure](https://portal.azure.com)   como administrador de segurança, administrador de acesso condicional ou administrador global.
1. Navegue pela **Azure Ative Directory**   >  **Properties**.
1. **Selecione Gerir as falhas de segurança**.
1. Desafine o **alternância de segurança ativa** para **Sim**.
1. Selecione **Guardar**.

## <a name="disabling-security-defaults"></a>Desativar incumprimentos de segurança

As organizações que optem por implementar políticas de Acesso Condicional que substituam os incumprimentos de segurança devem desativar os incumprimentos de segurança. 

![A mensagem de aviso desativa os incumprimentos de segurança para permitir o acesso condicional](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

Para desativar as falhas de segurança no seu diretório:

1. Inscreva-se no [portal Azure](https://portal.azure.com)   como administrador de segurança, administrador de acesso condicional ou administrador global.
1. Navegue pela **Azure Ative Directory**   >  **Properties**.
1. **Selecione Gerir as falhas de segurança**.
1. Desafine o **alternador de segurança** de Ativação para **Nº**.
1. Selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

[Políticas comuns de acesso condicional](../conditional-access/concept-conditional-access-policy-common.md)
