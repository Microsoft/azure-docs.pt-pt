---
title: Azure Active Directory padrões de segurança
description: Políticas padrão de segurança projetadas para proteger as organizações contra ataques comuns
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4921f0605f16a601f6e2ee9a15b71b50d253201
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72453046"
---
# <a name="what-are-security-defaults"></a>O que são os padrões de segurança?

O gerenciamento de segurança pode ser difícil quando ataques comuns relacionados à identidade, como o spray de senha, reprodução e phishing, estão se tornando cada vez mais populares. Criar uma maneira mais simples de tornar sua organização mais segura contra esses ataques comuns é a meta dos padrões de segurança no Azure Active Directory (AD). Os padrões de segurança facilitam a segurança e ajudam a proteger sua organização contra ataques comuns. Os padrões de segurança contêm configurações de segurança pré-configuradas para esses ataques comuns. A Microsoft está disponibilizando os padrões de segurança para todos. O objetivo é garantir que todas as organizações tenham um nível básico de segurança habilitado sem nenhum custo adicional.

![Captura de tela da nova UX de padrões de segurança](./media/concept-conditional-access-security-defaults/security-defaults-azure-ad-portal.png)
 
As seguintes configurações de segurança serão ativadas em seu locatário. 

## <a name="unified-mfa-registration"></a>Registro de MFA unificado

Todos os usuários em seu locatário serão registrados para a autenticação multifator do Azure (MFA). Os usuários terão 14 dias para se registrar no Azure MFA usando o aplicativo Microsoft Authenticator. Depois que os 14 dias tiverem passado, o usuário não poderá entrar até que o registro de MFA tenha sido concluído.

Entendemos que alguns usuários podem estar fora do escritório e/ou não entrarão em log durante os 14 dias imediatamente após a habilitação dos padrões de segurança. Para garantir que cada usuário receba bastante tempo para se registrar no MFA, o período de 14 dias é exclusivo para cada usuário. O período de 14 dias de um usuário começa após seu primeiro logon interativo bem-sucedido quando os padrões de segurança estiverem habilitados.

## <a name="mfa-enforcement"></a>Imposição de MFA

### <a name="protecting-administrators"></a>Protegendo administradores

Os usuários com acesso a contas com privilégios aumentaram o acesso ao seu ambiente. Devido à potência que essas contas têm, você deve tratá-las com cuidado especial. Um método comum para melhorar a proteção de contas com privilégios é exigir uma forma mais forte de verificação de conta quando eles são usados para entrar. No Azure Active Directory, você pode obter uma verificação de conta mais forte exigindo a autenticação multifator.

Depois que o registro de MFA for concluído, as nove funções de administrador do Azure AD a seguir serão necessárias para executar a MFA toda vez que entrarem.

- Administrador global
- Administrador do SharePoint
- Administrador do Exchange
- Administrador de acesso condicional
- Administrador de segurança
- Administrador de assistência técnica/administrador de senha
- Administrador de faturação
- Administrador do usuário
- Administrador de autenticação

### <a name="protecting-all-users"></a>Protegendo todos os usuários

Tendemos a imaginar que as contas de administrador são as únicas contas que precisam de proteção com a MFA (autenticação multifator). Os administradores têm acesso amplo a informações confidenciais e podem fazer alterações nas configurações de toda a assinatura. No entanto, os atores ruins tendem a direcionar os usuários finais. Depois de obter acesso, esses atores inválidos podem solicitar acesso a informações privilegiadas em nome do titular da conta original ou baixar o diretório inteiro para executar um ataque de phishing em toda a organização. Um método comum para melhorar a proteção para todos os usuários é exigir uma forma mais forte de verificação de conta, como a MFA (autenticação multifator) para todos. Depois que o registro de MFA for concluído, os usuários serão solicitados a receber a MFA sempre que necessário.

### <a name="blocking-legacy-authentication"></a>Bloqueando a autenticação herdada

Para dar aos usuários acesso fácil aos seus aplicativos de nuvem, o Azure Active Directory (Azure AD) dá suporte a uma ampla variedade de protocolos de autenticação, incluindo autenticação herdada. A autenticação herdada é um termo que se refere a uma solicitação de autenticação feita por:

- Clientes mais antigos do Office que não usam autenticação moderna (por exemplo, cliente do Office 2010)
- Qualquer cliente que usa protocolos de email herdados, como IMAP/SMTP/POP3

Hoje, a maioria de todas as tentativas de entrada comprometentes vêm da autenticação herdada. A autenticação herdada não dá suporte à autenticação multifator (MFA). Mesmo que você tenha uma política de MFA habilitada em seu diretório, um ator inadequado pode autenticar usando um protocolo herdado e ignorar MFA. Depois que os padrões de segurança são habilitados em seu locatário, todas as solicitações de autenticação feitas por um protocolo herdado para qualquer serão bloqueadas. Os padrões de segurança não bloqueiam o Exchange ActiveSync.

### <a name="protecting-privileged-actions"></a>Protegendo ações privilegiadas

As organizações usam uma variedade de serviços do Azure gerenciados por meio da API de Azure Resource Manager, incluindo:

- Portal do Azure 
- Azure PowerShell 
- CLI do Azure

O uso de Azure Resource Manager para gerenciar seus serviços é uma ação altamente privilegiada. Azure Resource Manager pode alterar configurações de todo o locatário, como configurações de serviço e cobrança de assinatura. A autenticação de fator único é vulnerável a uma variedade de ataques, como phishing e spray de senha. Portanto, é importante verificar a identidade dos usuários que desejam acessar Azure Resource Manager e atualizar as configurações, exigindo a autenticação multifator antes de permitir o acesso.

Depois que os padrões de segurança estiverem habilitados em seu locatário, qualquer usuário que acessar o portal do Azure, Azure PowerShell ou CLI do Azure será solicitado a concluir a autenticação multifator. Essa política se aplica a todos os usuários que acessam Azure Resource Manager, independentemente de se eles forem um administrador ou um usuário. Se o usuário não estiver registrado para MFA, o usuário será solicitado a se registrar usando o aplicativo Microsoft Authenticator para continuar. Nenhum período de registro de MFA de 14 dias será fornecido.

## <a name="deployment-considerations"></a>Considerações sobre implementação

A seguir estão algumas considerações adicionais relacionadas à implantação de padrões de segurança para seu locatário.

### <a name="legacy-protocols"></a>Protocolos herdados

Os protocolos de autenticação herdados (IMAP, SMTP, POP3, etc.) são usados por clientes de email para fazer solicitações de autenticação. Esses protocolos não dão suporte a MFA. A maioria dos comprometimentos de conta vistos pela Microsoft são causadas por atores ruins que realizam ataques contra protocolos herdados tentando ignorar MFA. Para garantir que a MFA seja necessária ao fazer logon em uma conta administrativa e os atores inválidos não possam ignorar MFA, os padrões de segurança bloqueiam todas as solicitações de autenticação feitas para contas de administrador de protocolos herdados.

> [!WARNING]
> Antes de habilitar essa configuração, verifique se os administradores não estão usando protocolos de autenticação herdados. Para obter mais informações, consulte o artigo [como sair da autenticação herdada](concept-conditional-access-block-legacy-authentication.md).

### <a name="conditional-access"></a>Acesso Condicional

O acesso condicional pode ser usado para configurar políticas que fornecem o mesmo comportamento habilitado por padrões de segurança. Se você estiver usando o acesso condicional e tiver políticas de acesso condicional habilitadas em seu ambiente, os padrões de segurança não estarão disponíveis para você. Se você tiver uma licença que fornece acesso condicional, mas não tiver políticas de acesso condicional habilitadas em seu ambiente, você poderá usar os padrões de segurança até habilitar as políticas de autoridade de certificação.

![Padrões de segurança ou acesso condicional não ambos](./media/concept-conditional-access-security-defaults/security-defaults-conditional-access.png)

Aqui estão os guias passo a passo sobre como o acesso condicional pode ser usado para configurar políticas equivalentes:

- [Exigir MFA para administradores](howto-conditional-access-policy-admin-mfa.md)
- [Exigir MFA para gerenciamento do Azure](howto-conditional-access-policy-azure-management.md)
- [Bloquear autenticação herdada](howto-conditional-access-policy-block-legacy.md)

## <a name="enabling-security-defaults"></a>Habilitando padrões de segurança

Para habilitar os padrões de segurança em seu diretório:

1. Entre no [portal do Azure](https://portal.azure.com) As um administrador de segurança, administrador de acesso condicional ou administrador global.
1. Navegue até **Azure Active Directory** **Propriedades**   @ no__t-2
1. Selecione **gerenciar padrões de segurança**
1. Defina a alternância **habilitar padrões de segurança** para **Sim**.
1. Clique em salvar.

## <a name="next-steps"></a>Passos seguintes

[Políticas de acesso condicional comum](concept-conditional-access-policy-common.md)

[O que é o acesso condicional?](overview.md)
