---
title: Incumprimentos de segurança do Diretório Ativo Azure
description: Políticas de incumprimento de segurança que ajudam a proteger as organizações de ataques comuns
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 023a52f373844e026de0e588e9cd46323abdcf34
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149835"
---
# <a name="what-are-security-defaults"></a>O que são incumprimentos de segurança?

Gerir a segurança pode ser difícil quando os ataques comuns relacionados com a identidade estão a tornar-se cada vez mais populares. Estes ataques incluem spray de senha, repetição e phishing.

Os incumprimentos de segurança no Azure Ative Directory (Azure AD) facilitam a segurança e ajudam a proteger a sua organização. Os incumprimentos de segurança contêm definições de segurança pré-configuradas para ataques comuns. 

A Microsoft está a disponibilizar falhas de segurança a todos. O objetivo é garantir que todas as organizações tenham um nível básico de segurança habilitado sem custos adicionais. Ligas falhas de segurança no portal Azure.

![Screenshot do portal Azure com o toggle para permitir falhas de segurança](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
> [!TIP]
> Se seu locatário foi criado em ou após 22 de outubro de 2019, é possível que você esteja experimentando o novo comportamento seguro por padrão e já tenha os padrões de segurança habilitados em seu locatário. Em um esforço para proteger todos os nossos usuários, os padrões de segurança estão sendo distribuídos para todos os novos locatários criados.

Mais detalhes sobre o porquê de falhas de segurança estarem a ser disponibilizadas podem ser encontrados no post de blog de Alex Weinert, [introduzindo incumprimentos](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414)de segurança .

## <a name="unified-multi-factor-authentication-registration"></a>Registo unificado de autenticação multi-factor

Todos os utilizadores do seu inquilino devem inscrever-se para autenticação multifactor (MFA) sob a forma do serviço de autenticação Multifactor Azure. Os utilizadores têm 14 dias para se registarem para autenticação multi-factor utilizando a aplicação Autenticadora Microsoft. Após os 14 dias passados, o utilizador não poderá iniciar o seu início até que o registo de autenticação multifactor esteja concluído.

Sabemos que alguns utilizadores podem estar fora do escritório ou não assinar durante os 14 dias imediatamente após permitir em preversões de segurança. Para garantir que cada utilizador tem tempo suficiente para se registar para autenticação multi-factor, o período de 14 dias é único para cada utilizador. O período de 14 dias de um utilizador começa após o seu primeiro início de sessão interativa bem sucedida depois de ativar as falhas de segurança.

## <a name="multi-factor-authentication-enforcement"></a>Aplicação da autenticação de vários fatores

### <a name="protecting-administrators"></a>Proteger administradores

Os utilizadores com acesso a contas privilegiadas têm um acesso acrescido ao seu ambiente. Devido ao poder que estas contas têm, deve tratá-las com especial cuidado. Um método comum para melhorar a proteção das contas privilegiadas consiste em exigir uma forma mais forte de verificação da conta para o início do processo de inscrição. Em Azure AD, pode obter uma verificação de conta mais forte, exigindo autenticação multi-factor.

Após a inscrição com A autenticação multi-factor, serão necessárias as seguintes nove funções de administrador da AD Azure para realizar a autenticação adicional sempre que iniciarem o seu início:

- Administrador global
- Administrador do SharePoint
- Administrador de intercâmbio
- Administrador de Acesso Condicional
- Administrador de segurança
- Administrador de helpdesk ou administrador de palavra-passe
- Administrador de faturação
- Administrador de utilizadores
- Administrador de autenticação

### <a name="protecting-all-users"></a>Proteger todos os utilizadores

Tendemos a pensar que as contas de administrador são as únicas contas que precisam de camadas extra de autenticação. Os administradores têm um amplo acesso a informações sensíveis e podem fazer alterações às definições em toda a subscrição. Mas os atacantes tendem a visar os utilizadores finais. 

Após o acesso destes agressores, podem solicitar acesso a informações privilegiadas em nome do titular da conta original. Eles podem até baixar todo o diretório para realizar um ataque de phishing a toda a sua organização. 

Um método comum para melhorar a proteção de todos os utilizadores é exigir uma forma mais forte de verificação de conta, como a Autenticação Multi-Factor, para todos. Após os utilizadores completarem o registo de autenticação multi-factor, serão solicitados para autenticação adicional sempre que necessário.

### <a name="blocking-legacy-authentication"></a>Bloquear a autenticação do legado

Para dar aos seus utilizadores um acesso fácil às suas aplicações na nuvem, o Azure AD suporta uma variedade de protocolos de autenticação, incluindo a autenticação do legado. *A autenticação do legado* é um termo que se refere a um pedido de autenticação feito por:

- Clientes mais velhos do Office que não usam autenticação moderna (por exemplo, um cliente do Office 2010).
- Qualquer cliente que utilize protocolos de correio mais antigos, como iMAP, SMTP ou POP3.

Hoje, a maioria das tentativas comprometedoras de inscrição provém da autenticação do legado. A autenticação do legado não suporta a autenticação multi-factor. Mesmo que tenha uma política de autenticação multi-factor ativada no seu diretório, um intruso pode autenticar usando um protocolo mais antigo e contornar a autenticação multi-factor. 

Após os incumprimentos de segurança estarem ativados no seu inquilino, todos os pedidos de autenticação feitos por um protocolo mais antigo serão bloqueados. Falhas de segurança bloqueiam a autenticação básica do Exchange Ative Sync.

> [!WARNING]
> Antes de ativar os incumprimentos de segurança, certifique-se de que os seus administradores não estão a utilizar protocolos de autenticação mais antigos. Para mais informações, consulte [Como afastar-se da autenticação do legado.](concept-fundamentals-block-legacy-authentication.md)

### <a name="protecting-privileged-actions"></a>Proteger ações privilegiadas

As organizações utilizam uma variedade de serviços Azure geridos através da API do Gestor de Recursos Azure, incluindo:

- Portal do Azure 
- Azure PowerShell 
- CLI do Azure

Usar o Gestor de Recursos Azure para gerir os seus serviços é uma ação altamente privilegiada. O Azure Resource Manager pode alterar as configurações em todo o arrendamento, tais como configurações de serviço e faturação de subscrição. A autenticação de um único fator é vulnerável a uma variedade de ataques como phishing e spray de senha. 

É importante verificar a identidade dos utilizadores que pretendam aceder ao Azure Resource Manager e configurações de atualizações. Verifica a sua identidade exigindo autenticação adicional antes de permitir o acesso.

Depois de ativar falhas de segurança no seu inquilino, qualquer utilizador que aceda ao portal Azure, Azure PowerShell ou o Azure CLI terá de concluir a autenticação adicional. Esta política aplica-se a todos os utilizadores que acedem ao Gestor de Recursos Do Azure, seja um administrador ou um utilizador. 

Se o utilizador não estiver registado para autenticação multi-factor, o utilizador será obrigado a registar-se utilizando a aplicação Autenticadora Microsoft para proceder. Será fornecido o período de registo de autenticação multifactor de 14 dias.

Os inquilinos do Exchange Online pré-2017 têm a autenticação moderna desativada por defeito. Para evitar a possibilidade de um laço de login ao autenticar através destes inquilinos, deve permitir a [autenticação moderna.](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)

> [!NOTE]
> A conta de sincronização Azure AD Connect está excluída de incumprimentos de segurança e não será solicitada a registar-se ou a realizar a autenticação de vários fatores. As organizações não devem utilizar esta conta para outros fins.

## <a name="deployment-considerations"></a>Considerações sobre implementação

As seguintes considerações adicionais estão relacionadas com a implementação de incumprimentos de segurança para o seu inquilino.

### <a name="authentication-methods"></a>Métodos de autenticação

As falhas de segurança permitem o registo e utilização da Autenticação Multi-Factor Azure **utilizando apenas a aplicação Do Autenticador Microsoft utilizando notificações**. O Acesso Condicional permite a utilização de qualquer método de autenticação que o administrador opte por ativar.

|   | Predefinições de segurança | Acesso Condicional |
| --- | --- | --- |
| Notificação através de aplicação móvel | X | X |
| Código de verificação de aplicativo móvel ou ficha de hardware |   | X |
| Mensagem de texto para telefone |   | X |
| Chamada para telefone |   | X |
| Palavras-passe de aplicativos |   | X** |

** As palavras-passe da aplicação só estão disponíveis em MFA por utilizador com cenários de autenticação legado apenas se ativadas pelos administradores.

### <a name="conditional-access"></a>Acesso Condicional

Pode utilizar o Acesso Condicional para configurar políticas semelhantes às falhas de segurança, mas com mais granularidade, incluindo exclusões de utilizadores, que não estão disponíveis em incumprimentos de segurança. Se estiver a utilizar o Acesso Condicional e tiver políticas de Acesso Condicional ativadas no seu ambiente, os incumprimentos de segurança não estarão disponíveis para si. Se tiver uma licença que forneça acesso condicional mas não tenha nenhuma política de Acesso Condicional ativada no seu ambiente, é bem-vindo a utilizar falhas de segurança até ativar as políticas de Acesso Condicional. Mais informações sobre o licenciamento da AD Azure podem ser encontradas na página de preços da [AD Azure](https://azure.microsoft.com/pricing/details/active-directory/).

![Mensagem de aviso de que pode ter incumprimentos de segurança ou acesso condicional não ambos](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Aqui estão guias passo a passo sobre como pode utilizar o Acesso Condicional para configurar políticas equivalentes:

- [Exigir MFA para administradores](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [Exigir MFA para a gestão do Azure](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Autenticação do legado do bloco](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [Exigir MFA para todos os utilizadores](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Exigir registo De MFA Azure](../identity-protection/howto-identity-protection-configure-mfa-policy.md) - Requer proteção de identidade Azure AD

## <a name="enabling-security-defaults"></a>Ativar incumprimentos de segurança

Para ativar os incumprimentos de segurança no seu diretório:

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador de segurança, administrador de Acesso Condicional ou administrador global.
1. Navegue pelo **Diretório Ativo da Azure**  **propriedades**>.
1. Selecione **Gerir os predefinições de segurança**.
1. Desative as falhas de **segurança ativar** para **Sim**.
1. Selecione **Guardar**.

## <a name="disabling-security-defaults"></a>Desativar os incumprimentos de segurança

As organizações que optem por implementar políticas de Acesso Condicional que substituam os incumprimentos de segurança devem desativar os incumprimentos de segurança. 

![Mensagem de aviso desativa falhas de segurança para ativar o Acesso Condicional](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

Para desativar os incumprimentos de segurança no seu diretório:

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador de segurança, administrador de Acesso Condicional ou administrador global.
1. Navegue pelo **Diretório Ativo da Azure**  **propriedades**>.
1. Selecione **Gerir os predefinições de segurança**.
1. Desative as **predefinições** de segurança Ativar para **O**.
1. Selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

[Políticas comuns de acesso condicional](../conditional-access/concept-conditional-access-policy-common.md)
