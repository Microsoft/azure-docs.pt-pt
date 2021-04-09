---
title: Azure Ative Directory B2B boas práticas e recomendações
description: Saiba as melhores práticas e recomendações para o acesso do utilizador de negócios-a-empresa (B2B) no Azure Ative Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94fd488ceb7ddb3724dd576c97c9070481e95147
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100365638"
---
# <a name="azure-active-directory-b2b-best-practices"></a>Azure Ative Directy B2B boas práticas
Este artigo contém recomendações e boas práticas para a colaboração entre empresas (B2B) no Azure Ative Directory (Azure AD).

   > [!IMPORTANT]
   > **A partir de outubro de 2021, a** Microsoft deixará de apoiar o resgate de convites através da criação de contas de AD não geridas ("virais" ou "just-in-time") e inquilinos para cenários de colaboração B2B. Nessa altura, a funcionalidade de código de acesso de e-mail será ativada para todos os inquilinos existentes e ativada por padrão para novos inquilinos. Estamos a ativar a funcionalidade de código de acesso de e-mail uma vez porque fornece um método de autenticação de recuo sem emenda para os utilizadores convidados. No entanto, tem a opção de desativar esta funcionalidade se optar por não a utilizar. Para mais detalhes, consulte [a autenticação de código de acesso](one-time-passcode.md) de email uma vez


## <a name="b2b-recommendations"></a>Recomendações B2B
| Recomendação | Comentários |
| --- | --- |
| Para uma experiência de inscrição ideal, federa com fornecedores de identidade | Sempre que possível, federa diretamente com fornecedores de identidade para permitir que os utilizadores convidados inscrevam-se nas suas apps e recursos partilhados sem terem de criar contas microsoft accounts (MSAs) ou Azure AD. Pode utilizar a funcionalidade da [federação](google-federation.md) do Google para permitir que os utilizadores de hóspedes B2B assinem o seu contrato com as suas contas google. Ou, pode utilizar a [funcionalidade Direct federation (pré-visualização)](direct-federation.md) para configurar uma federação direta com qualquer organização cujo fornecedor de identidade (IdP) suporte o protocolo SAML 2.0 ou WS-Fed. |
| Utilize a funcionalidade de código de acesso único do Email para hóspedes B2B que não podem autenticar por outros meios | A funcionalidade [de código de acesso de email](one-time-passcode.md) autentica os utilizadores convidados B2B quando não podem ser autenticados através de outros meios, como o Azure AD, uma conta Microsoft (MSA) ou a federação do Google. Quando o utilizador convidado resgatar um convite ou aceder a um recurso partilhado, pode solicitar um código temporário, que é enviado para o seu endereço de e-mail. Depois introduzem este código para continuarem a iniciar sessão. |
| Adicionar uma imagem corporativa à sua página de início de sessão | Pode personalizar a sua página de sôr-in para que seja mais intuitiva para os seus utilizadores convidados B2B. Veja como [adicionar marca da empresa para iniciar sing e páginas do Painel de Acesso](../fundamentals/customize-branding.md). |
| Adicione a sua declaração de privacidade à experiência de resgate de utilizadores de hóspedes B2B | Pode adicionar o URL da declaração de privacidade da sua organização ao processo de resgate de convites pela primeira vez para que um utilizador convidado consinta nos seus termos de privacidade para continuar. Ver [Como fazer: Adicione as informações de privacidade da sua organização no Diretório Ativo Azure](../fundamentals/active-directory-properties-area.md). |
| Utilize a função de convite a granel (pré-visualização) para convidar vários utilizadores convidados B2B ao mesmo tempo | Convide vários utilizadores convidados para a sua organização ao mesmo tempo, utilizando a funcionalidade de pré-visualização de convites a granel no portal Azure. Esta funcionalidade permite-lhe fazer o upload de um ficheiro CSV para criar utilizadores convidados B2B e enviar convites a granel. Consulte [tutorial para utilizadores B2B convidativos a granel.](tutorial-bulk-invite.md) |
| Impor políticas de acesso condicional para autenticação multi-factor (MFA) | Recomendamos a aplicação das políticas de MFA nas aplicações que pretende partilhar com os utilizadores B2B parceiros. Desta forma, o MFA será constantemente aplicado nas aplicações do seu inquilino, independentemente de a organização parceira estar a usar MFA. Ver [Acesso Condicional para utilizadores de colaboração B2B](conditional-access.md). |
| Se estiver a aplicar políticas de acesso condicional baseadas em dispositivos, utilize listas de exclusão para permitir o acesso aos utilizadores B2B | Se as políticas de Acesso Condicional baseadas no dispositivo estiverem ativadas na sua organização, os dispositivos de utilizador convidado B2B serão bloqueados porque não são geridos pela sua organização. Pode criar listas de exclusão que contenham utilizadores parceiros específicos para os excluir da política de Acesso Condicional baseada no dispositivo. Ver [Acesso Condicional para utilizadores de colaboração B2B](conditional-access.md). |
| Utilize um URL específico do inquilino ao fornecer links diretos para os seus utilizadores de hóspedes B2B | Como alternativa ao e-mail do convite, pode dar a um hóspede um link direto para a sua app ou portal. Este link direto deve ser específico do inquilino, o que significa que deve incluir um ID do inquilino ou domínio verificado para que o hóspede possa ser autenticado no seu inquilino, onde a app partilhada está localizada. Consulte [a experiência Redemption para o utilizador convidado.](redemption-experience.md) |
| Ao desenvolver uma aplicação, use o UserType para determinar a experiência do utilizador convidado  | Se está a desenvolver uma aplicação e pretende proporcionar experiências diferentes para utilizadores de inquilinos e utilizadores convidados, utilize a propriedade UserType. A alegação do UserType não está atualmente incluída no token. As aplicações devem utilizar a API do Microsoft Graph para consultar o diretório para o utilizador obter o seu UserType. |
| Altere a propriedade UserType *apenas* se a relação do utilizador com a organização mudar | Embora seja possível utilizar o PowerShell para converter a propriedade UserType para um utilizador de Membro para Convidado (e vice-versa), só deverá alterar esta propriedade se a relação do utilizador com a sua organização mudar. Ver [Propriedades de um utilizador convidado B2B.](user-properties.md)|

## <a name="next-steps"></a>Passos seguintes

[Gerir a partilha de B2B](delegate-invitations.md)