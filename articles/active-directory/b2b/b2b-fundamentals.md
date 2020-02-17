---
title: Diretório Ativo Azure B2B boas práticas e recomendações
description: Conheça as melhores práticas e recomendações para o acesso ao utilizador convidado de negócios para empresas (B2B) no Diretório Ativo azure.
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
ms.openlocfilehash: 4e17e0f1f01e836a7a240100c1c0e1f015da5f00
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368145"
---
# <a name="azure-active-directory-b2b-best-practices"></a>Boas práticas do Diretório Ativo Azure B2B
Este artigo contém recomendações e boas práticas para a colaboração entre empresas (B2B) na Azure Ative Directory (Azure AD).

## <a name="b2b-recommendations"></a>Recomendações B2B
| Recomendação | Comentários |
| --- | --- |
| Para uma experiência de inscrição ideal, federado com fornecedores de identidade | Sempre que possível, federadirectamente com fornecedores de identidade para permitir que os utilizadores convidados inscrevam as suas aplicações e recursos partilhados sem terem de criar contas Microsoft Accounts (MSAs) ou Azure AD. Pode utilizar a funcionalidade da [federação da Google](google-federation.md) para permitir que os utilizadores convidados do B2B assinem as suas contas google. Ou, pode utilizar a [funcionalidade Direct federation (pré-visualização)](direct-federation.md) para criar uma federação direta com qualquer organização cujo fornecedor de identidade (IDP) apoie o protocolo SAML 2.0 ou WS-Fed. |
| Utilize a funcionalidade de senha única (pré-visualização) do Email para os hóspedes B2B que não conseguem autenticar por outros meios | A funcionalidade de [senha única (pré-visualização)](one-time-passcode.md) do Email autentica os utilizadores convidados B2B quando não podem ser autenticados através de outros meios como o Azure AD, uma conta Microsoft (MSA) ou a federação da Google. Quando o utilizador hóspede resgata um convite ou acede a um recurso partilhado, pode solicitar um código temporário, que é enviado para o seu endereço de e-mail. Depois introduzem este código para continuar a assinar. |
| Adicionar uma imagem corporativa à sua página de início de sessão | Pode personalizar a sua página de sessão de sessão para que seja mais intuitivo para os seus utilizadores convidados B2B. Veja como [adicionar branding da empresa para iniciar sessão e páginas do Painel](../fundamentals/customize-branding.md)de Acesso . |
| Adicione a sua declaração de privacidade à experiência de redenção de hóspedes B2B | Pode adicionar o URL da declaração de privacidade da sua organização ao primeiro processo de resgate de convites para que um utilizador convidado consinta nos seus termos de privacidade para continuar. Ver Como: Adicione as [informações de privacidade da sua organização no Diretório Ativo Azure](https://aka.ms/adprivacystatement). |
| Utilize a funcionalidade de convite a granel (pré-visualização) para convidar vários utilizadores convidados b2B ao mesmo tempo | Convide vários utilizadores convidados para a sua organização ao mesmo tempo, utilizando a funcionalidade de pré-visualização de convite seleções em massa no portal Azure. Esta funcionalidade permite-lhe fazer o upload de um ficheiro CSV para criar utilizadores convidados B2B e enviar convites a granel. Consulte [o Tutorial para convidar a granel os utilizadores B2B](tutorial-bulk-invite.md). |
| Impor políticas de acesso condicional para autenticação multi-factor (MFA) | Recomendamos a aplicação das políticas de MFA nas aplicações que pretende partilhar com os utilizadores B2B parceiros. Desta forma, o MFA será aplicado de forma consistente nas aplicações do seu inquilino, independentemente de a organização parceira estar a usar MFA. Ver Acesso Condicional para utilizadores de [colaboração B2B.](conditional-access.md) |
| Se estiver a aplicar as políticas de Acesso Condicional baseadas em dispositivos, utilize listas de exclusão para permitir o acesso aos utilizadores B2B | Se as políticas de Acesso Condicional baseadas no dispositivo estiverem ativadas na sua organização, os dispositivos de utilizador convidados B2B serão bloqueados porque não são geridos pela sua organização. Pode criar listas de exclusão que contenham utilizadores parceiros específicos para os excluir da política de acesso condicional baseada no dispositivo. Ver Acesso Condicional para utilizadores de [colaboração B2B.](conditional-access.md) |
| Utilize um URL específico para o inquilino ao fornecer links diretos aos seus utilizadores convidados B2B | Como alternativa ao e-mail de convite, pode dar a um hóspede um link direto para a sua app ou portal. Este link direto deve ser específico do inquilino, o que significa que deve incluir um ID de inquilino ou domínio verificado para que o hóspede possa ser autenticado no seu inquilino, onde a aplicação partilhada está localizada. Consulte [a experiência De Redenção para o utilizador convidado.](redemption-experience.md) |
| Ao desenvolver uma aplicação, utilize o UserType para determinar a experiência do utilizador do hóspede  | Se estiver a desenvolver uma aplicação e pretender proporcionar diferentes experiências para utilizadores de inquilinos e utilizadores convidados, utilize a propriedade UserType. A alegação userType não está atualmente incluída no símbolo. As aplicações devem utilizar a API do Microsoft Graph para consultar o diretório para que o utilizador obtenha o seu UserType. |
| Alterar a propriedade UserType *apenas* se a relação do utilizador com a organização mudar | Embora seja possível utilizar o PowerShell para converter a propriedade UserType para um utilizador de Membro para Hóspede (e vice-versa), deverá alterar esta propriedade apenas se a relação do utilizador com a sua organização mudar. Consulte [propriedades de um utilizador convidado B2B](user-properties.md).|

## <a name="next-steps"></a>Passos seguintes

[Gerir a partilha b2B](delegate-invitations.md)
