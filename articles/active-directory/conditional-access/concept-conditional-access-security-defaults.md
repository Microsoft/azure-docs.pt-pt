---
title: Azure Active Directory padrões de segurança
description: Políticas padrão de segurança que ajudam a proteger as organizações contra ataques comuns
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: bde1741d12cc7ef181fb60bc7eecbec1fed8cbd0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73151214"
---
# <a name="what-are-security-defaults"></a>O que são os padrões de segurança?

O gerenciamento de segurança pode ser difícil quando ataques comuns relacionados à identidade estão se tornando cada vez mais populares. Esses ataques incluem pulverização, reprodução e phishing de senha.

Os padrões de segurança no Azure Active Directory (AD do Azure) facilitam a segurança e ajudam a proteger sua organização. Os padrões de segurança contêm configurações de segurança pré-configuradas para ataques comuns. 

A Microsoft está disponibilizando os padrões de segurança para todos. O objetivo é garantir que todas as organizações tenham um nível básico de segurança habilitado sem nenhum custo adicional. Você ativa os padrões de segurança no portal do Azure.

![Captura de tela da portal do Azure com a alternância para habilitar os padrões de segurança](./media/concept-conditional-access-security-defaults/security-defaults-azure-ad-portal.png)
 
As seguintes configurações de segurança serão ativadas em seu locatário. 

## <a name="unified-multi-factor-authentication-registration"></a>Registro de autenticação multifator unificado

Todos os usuários em seu locatário devem se registrar para a autenticação multifator (MFA) na forma do serviço de autenticação multifator do Azure. Os usuários têm 14 dias para se registrarem na autenticação multifator usando o aplicativo Microsoft Authenticator. Depois que os 14 dias tiverem passado, o usuário não poderá entrar até que o registro da autenticação multifator seja concluído.

Entendemos que alguns usuários podem estar fora do escritório ou não entrarão durante os 14 dias imediatamente após habilitar os padrões de segurança. Para garantir que cada usuário tenha bastante tempo para se registrar na autenticação multifator, o período de 14 dias é exclusivo para cada usuário. O período de 14 dias de um usuário começa após sua primeira entrada interativa bem-sucedida depois de habilitar os padrões de segurança.

## <a name="multi-factor-authentication-enforcement"></a>Imposição da autenticação multifator

### <a name="protecting-administrators"></a>Protegendo administradores

Os usuários com acesso a contas com privilégios aumentaram o acesso ao seu ambiente. Devido à potência que essas contas têm, você deve tratá-las com cuidado especial. Um método comum para melhorar a proteção de contas com privilégios é exigir uma forma mais forte de verificação de conta para entrada. No Azure AD, você pode obter uma verificação de conta mais forte exigindo a autenticação multifator.

Após a conclusão do registro com a autenticação multifator, serão necessárias as nove funções de administrador do Azure AD a seguir para executar autenticação adicional sempre que entrarem:

- Administrador global
- Administrador do SharePoint
- Administrador do Exchange
- Administrador de acesso condicional
- Administrador de segurança
- Administrador de assistência técnica ou administrador de senha
- Administrador de faturação
- Administrador do usuário
- Administrador de autenticação

### <a name="protecting-all-users"></a>Protegendo todos os usuários

Tendemos a imaginar que as contas de administrador são as únicas contas que precisam de camadas extras de autenticação. Os administradores têm acesso amplo a informações confidenciais e podem fazer alterações nas configurações de toda a assinatura. Mas os invasores tendem a direcionar os usuários finais. 

Depois que esses invasores obtiverem acesso, eles poderão solicitar acesso a informações privilegiadas em nome do titular da conta original. Eles podem até mesmo baixar o diretório inteiro para executar um ataque de phishing em toda a sua organização. 

Um método comum para melhorar a proteção para todos os usuários é exigir uma forma mais forte de verificação de conta, como a autenticação multifator, para todos. Depois que os usuários terminarem o registro da autenticação multifator, eles serão solicitados a fornecer autenticação adicional sempre que necessário.

### <a name="blocking-legacy-authentication"></a>Bloqueando a autenticação herdada

Para dar aos usuários acesso fácil aos seus aplicativos de nuvem, o Azure AD dá suporte a uma variedade de protocolos de autenticação, incluindo a autenticação herdada. A *autenticação herdada* é um termo que se refere a uma solicitação de autenticação feita por:

- Clientes do Office mais antigos que não usam autenticação moderna (por exemplo, um cliente do Office 2010).
- Qualquer cliente que usa protocolos de email mais antigos, como IMAP, SMTP ou POP3.

Hoje, a maior parte do comprometimento de tentativas de entrada é proveniente da autenticação herdada. A autenticação herdada não dá suporte à autenticação multifator. Mesmo que você tenha uma política de autenticação multifator habilitada em seu diretório, um invasor pode se autenticar usando um protocolo mais antigo e ignorar a autenticação multifator. 

Depois que os padrões de segurança são habilitados em seu locatário, todas as solicitações de autenticação feitas por um protocolo mais antigo serão bloqueadas. Os padrões de segurança não bloqueiam o Exchange ActiveSync.

### <a name="protecting-privileged-actions"></a>Protegendo ações privilegiadas

As organizações usam uma variedade de serviços do Azure gerenciados por meio da API Azure Resource Manager, incluindo:

- Portal do Azure 
- Azure PowerShell 
- CLI do Azure

O uso de Azure Resource Manager para gerenciar seus serviços é uma ação altamente privilegiada. Azure Resource Manager pode alterar configurações de todo o locatário, como configurações de serviço e cobrança de assinatura. A autenticação de fator único é vulnerável a uma variedade de ataques, como phishing e spray de senha. 

É importante verificar a identidade dos usuários que desejam acessar Azure Resource Manager e atualizar as configurações. Você verifica sua identidade exigindo autenticação adicional antes de permitir o acesso.

Depois de habilitar os padrões de segurança em seu locatário, qualquer usuário que estiver acessando o portal do Azure, Azure PowerShell ou o CLI do Azure precisará concluir a autenticação adicional. Essa política se aplica a todos os usuários que estão acessando Azure Resource Manager, seja um administrador ou um usuário. 

Se o usuário não estiver registrado para autenticação multifator, o usuário será solicitado a se registrar usando o aplicativo Microsoft Authenticator para continuar. Nenhum período de registro de autenticação multifator de 14 dias será fornecido.

## <a name="deployment-considerations"></a>Considerações sobre implementação

As considerações adicionais a seguir estão relacionadas à implantação de padrões de segurança para seu locatário.

### <a name="older-protocols"></a>Protocolos mais antigos

Os clientes de email usam protocolos de autenticação mais antigos (como IMAP, SMTP e POP3) para fazer solicitações de autenticação. Esses protocolos não dão suporte à autenticação multifator. A maioria dos comprometimentos de conta que a Microsoft vê é de ataques contra protocolos mais antigos que estão tentando ignorar a autenticação multifator. 

Para garantir que a autenticação multifator seja necessária para entrar em uma conta administrativa e que os invasores não possam ignorá-la, os padrões de segurança bloqueiam todas as solicitações de autenticação feitas para contas de administrador de protocolos mais antigos.

> [!WARNING]
> Antes de habilitar essa configuração, verifique se os administradores não estão usando protocolos de autenticação mais antigos. Para obter mais informações, consulte [como sair da autenticação herdada](concept-conditional-access-block-legacy-authentication.md).

### <a name="conditional-access"></a>Acesso Condicional

Você pode usar o acesso condicional para configurar políticas que fornecem o mesmo comportamento habilitado por padrões de segurança. Se você estiver usando o acesso condicional e tiver políticas de acesso condicional habilitadas em seu ambiente, os padrões de segurança não estarão disponíveis para você. Se você tiver uma licença que forneça acesso condicional, mas não tiver políticas de acesso condicional habilitadas em seu ambiente, você poderá usar os padrões de segurança até habilitar as políticas de acesso condicional.

![Mensagem de aviso de que você pode ter padrões de segurança ou acesso condicional não ambos](./media/concept-conditional-access-security-defaults/security-defaults-conditional-access.png)

Aqui estão os guias passo a passo sobre como você pode usar o acesso condicional para configurar políticas equivalentes:

- [Exigir MFA para administradores](howto-conditional-access-policy-admin-mfa.md)
- [Exigir MFA para gerenciamento do Azure](howto-conditional-access-policy-azure-management.md)
- [Bloquear autenticação herdada](howto-conditional-access-policy-block-legacy.md)
- [Exigir MFA para todos os usuários](howto-conditional-access-policy-all-users-mfa.md)

## <a name="enabling-security-defaults"></a>Habilitando padrões de segurança

Para habilitar os padrões de segurança em seu diretório:

1. Entre no [portal do Azure](https://portal.azure.com) As um administrador de segurança, administrador de acesso condicional ou administrador global.
1. Navegue até **Azure Active Directory**  **Propriedades**de > de  .
1. Selecione **gerenciar padrões de segurança**.
1. Defina a alternância **habilitar padrões de segurança** para **Sim**.
1. Selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

[Políticas de acesso condicional comum](concept-conditional-access-policy-common.md)

[O que é o acesso condicional?](overview.md)
