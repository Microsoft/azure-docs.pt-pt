---
title: Acesso condicional – exigir MFA para todos os usuários-Azure Active Directory
description: Criar uma política de acesso condicional personalizada para exigir que todos os usuários executem a autenticação multifator
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52faa2b6167606a46bf189d514a1eb314b443783
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424943"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>Acesso condicional: exigir MFA para todos os usuários

Como Alex Weinert, o diretório de segurança de identidade na Microsoft, menciona em sua postagem de blog [seu PA $ $Word não importa](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984):

> Sua senha não importa, mas a MFA faz! Com base em nossos estudos, sua conta tem mais de 99,9% menos provável de ser comprometida se você usar a MFA.

As diretrizes neste artigo ajudarão sua organização a criar uma política de MFA balanceada para seu ambiente.

## <a name="user-exclusions"></a>Exclusões de usuário

As políticas de acesso condicional são ferramentas poderosas, recomendamos excluir as seguintes contas da sua política:

* Contas de **acesso de emergência** ou de **vidro** para impedir o bloqueio de conta em todo o locatário. No cenário improvável, todos os administradores são bloqueados de seu locatário, sua conta administrativa de acesso de emergência pode ser usada para fazer logon no locatário. siga as etapas para recuperar o acesso.
   * Mais informações podem ser encontradas no artigo [gerenciar contas de acesso de emergência no Azure ad](../users-groups-roles/directory-emergency-access.md).
* **Contas de serviço** e **princípios de serviço**, como a conta de sincronização de Azure ad Connect. As contas de serviço são contas não interativas que não estão ligadas a nenhum usuário específico. Normalmente, eles são usados por serviços de back-end e permitem acesso programático a aplicativos. As contas de serviço devem ser excluídas, pois a MFA não pode ser concluída programaticamente.
   * Se sua organização tiver essas contas em uso em scripts ou código, considere substituí-las por [identidades gerenciadas](../managed-identities-azure-resources/overview.md). Como solução alternativa temporária, você pode excluir essas contas específicas da política de linha de base.

## <a name="application-exclusions"></a>Exclusões de aplicativo

As organizações podem ter muitos aplicativos de nuvem em uso. Nem todos esses aplicativos podem exigir segurança igual. Por exemplo, os aplicativos de folha de pagamento e de presença podem exigir MFA, mas a lanchonete provavelmente não. Os administradores podem optar por excluir aplicativos específicos de sua política.

## <a name="create-a-conditional-access-policy"></a>Criar uma política de acesso condicional

As etapas a seguir ajudarão a criar uma política de acesso condicional para exigir que as funções administrativas atribuídas executem a autenticação multifator.

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **Azure Active Directory** > **segurança** > **acesso condicional**.
1. Selecione **nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **atribuições**, selecione **usuários e grupos**
   1. Em **incluir**, selecione **todos os usuários**
   1. Em **excluir**, selecione **usuários e grupos** e escolha o acesso de emergência da sua organização ou contas de vidro. 
   1. Selecione **Done** (Concluído).
1. Em **aplicativos de nuvem ou ações** > **incluir**, selecione **todos os aplicativos de nuvem**.
   1. Em **excluir**, selecione qualquer aplicativo que não exija autenticação multifator.
1. Em **controles de acesso** > **concessão**, selecione **conceder acesso**, **exigir autenticação multifator**e selecione **selecionar**.
1. Confirme suas configurações e defina **habilitar política** como **ativado**.
1. Selecione **criar** para criar para habilitar a política.

### <a name="named-locations"></a>Localizações com nome

As organizações podem optar por incorporar locais de rede conhecidos conhecidos como **locais nomeados** a suas políticas de acesso condicional. Esses locais nomeados podem incluir redes IPv4 confiáveis como as de um local principal do Office. Para obter mais informações sobre como configurar locais nomeados, consulte o artigo [qual é a condição de local em Azure Active Directory acesso condicional?](location-condition.md)

Na política de exemplo acima, uma organização pode optar por não exigir a autenticação multifator se estiver acessando um aplicativo de nuvem de sua rede corporativa. Nesse caso, eles podem adicionar a seguinte configuração à política:

1. Em **atribuições**, selecione **condições** > **locais**.
   1. Configurar **Sim**.
   1. Inclua **qualquer local**.
   1. Exclua **todos os locais confiáveis**.
   1. Selecione **Done** (Concluído).
1. Selecione **Done** (Concluído).
1. **Salve** as alterações de política.

## <a name="next-steps"></a>Passos seguintes

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Determinar o impacto usando o modo somente relatório de acesso condicional](howto-conditional-access-report-only.md)

[Simular comportamento de entrada usando a ferramenta de What If de acesso condicional](troubleshoot-conditional-access-what-if.md)
