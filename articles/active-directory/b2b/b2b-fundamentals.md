---
title: Práticas recomendadas e recomendações do Azure Active Directory B2B
description: Conheça as práticas recomendadas e recomendações para acesso de usuário convidado entre empresas (B2B) no Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2aa6bc856fc7b7de071a45f3aa11c051e36eca4f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480115"
---
# <a name="azure-active-directory-b2b-best-practices"></a>Práticas recomendadas do Azure Active Directory B2B
Este artigo contém recomendações e práticas recomendadas para colaboração B2B (entre empresas) no Azure Active Directory (AD do Azure).

## <a name="b2b-recommendations"></a>Recomendações B2B
| Recomendação | Comentários |
| --- | --- |
| Para obter uma experiência de entrada ideal, federar com provedores de identidade | Sempre que possível, agrupe-se diretamente com os provedores de identidade para permitir que os usuários convidados façam logon em seus aplicativos e recursos compartilhados sem precisar criar contas da Microsoft (MSAs) ou do Azure AD. Você pode usar o [recurso Google Federation](google-federation.md) para permitir que usuários convidados B2B entrem com suas contas do Google. Ou, você pode usar o [recurso de Federação direta (versão prévia)](direct-federation.md) para configurar a Federação direta com qualquer organização cujo IDP (provedor de identidade) dê suporte ao protocolo SAML 2,0 ou WS-encaminhar. |
| Use o recurso de senha de uso único de email (versão prévia) para convidados B2B que não podem ser autenticados por outros meios | O recurso de [senha de uso único de email (versão prévia)](one-time-passcode.md) autentica os usuários convidados B2B quando eles não podem ser autenticados por outros meios, como o Azure AD, um conta Microsoft (MSA) ou Google Federation. Quando o usuário convidado resgata um convite ou acessa um recurso compartilhado, ele pode solicitar um código temporário, que é enviado para seu endereço de email. Em seguida, eles inserem esse código para continuar a entrar. |
| Adicionar uma imagem corporativa à sua página de início de sessão | Você pode personalizar sua página de entrada para que ela seja mais intuitiva para seus usuários convidados B2B. Veja como [Adicionar identidade visual da empresa às páginas de entrada e do painel de acesso](../fundamentals/customize-branding.md). |
| Adicione sua declaração de privacidade à experiência de resgate do usuário convidado B2B | Você pode adicionar a URL da política de privacidade da sua organização ao processo de resgate de convite pela primeira vez para que um usuário convidado precise consentir seus termos de privacidade para continuar. Consulte [como: adicionar as informações de privacidade de sua organização em Azure Active Directory](https://aka.ms/adprivacystatement). |
| Usar o recurso de convite em massa (versão prévia) para convidar vários usuários convidados B2B ao mesmo tempo | Convide vários usuários convidados para sua organização ao mesmo tempo usando o recurso de visualização de convite em massa no portal do Azure. Esse recurso permite carregar um arquivo CSV para criar usuários de convidado B2B e enviar convites em massa. Consulte o [tutorial para convidar em massa usuários B2B](tutorial-bulk-invite.md). |
| Impor políticas de acesso condicional para a autenticação multifator (MFA) | É recomendável impor políticas de MFA nos aplicativos que você deseja compartilhar com os usuários B2B do parceiro. Dessa forma, a MFA será imposta consistentemente nos aplicativos em seu locatário, independentemente de a organização parceira estar usando MFA. Consulte [acesso condicional para usuários de colaboração B2B](conditional-access.md). |
| Se você estiver impondo políticas de acesso condicional com base no dispositivo, use listas de exclusão para permitir o acesso a usuários B2B | Se as políticas de acesso condicional com base no dispositivo estiverem habilitadas em sua organização, os dispositivos de usuário convidado B2B serão bloqueados porque não são gerenciados pela sua organização. Você pode criar listas de exclusão que contenham usuários de parceiros específicos para excluí-las da política de acesso condicional com base no dispositivo. Consulte [acesso condicional para usuários de colaboração B2B](conditional-access.md). |
| Use uma URL específica do locatário ao fornecer links diretos para seus usuários convidados B2B | Como alternativa ao email de convite, você pode dar a um convidado um link direto para seu aplicativo ou Portal. Esse link direto deve ser específico do locatário, o que significa que ele deve incluir uma ID de locatário ou um domínio verificado para que o convidado possa ser autenticado em seu locatário, onde o aplicativo compartilhado está localizado. Consulte [experiência de resgate para o usuário convidado](redemption-experience.md). |
| Ao desenvolver um aplicativo, use UserType para determinar a experiência do usuário convidado  | Se você estiver desenvolvendo um aplicativo e desejar fornecer experiências diferentes para usuários de locatário e usuários convidados, use a propriedade UserType. A declaração UserType não está incluída no token no momento. Os aplicativos devem usar o API do Graph para consultar o diretório para que o usuário Obtenha seu UserType. |
| Alterar a propriedade UserType *somente* se a relação do usuário com a organização for alterada | Embora seja possível usar o PowerShell para converter a propriedade UserType de um usuário de membro para Guest (e vice-versa), você deve alterar essa propriedade somente se a relação do usuário com a organização for alterada. Consulte [Propriedades de um usuário convidado B2B](user-properties.md).|

## <a name="next-steps"></a>Passos seguintes

[Gerenciar o compartilhamento B2B](delegate-invitations.md)
