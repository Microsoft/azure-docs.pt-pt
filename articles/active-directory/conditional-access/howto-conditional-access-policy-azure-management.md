---
title: Acesso Condicional - Requer MFA para gestão Azure - Diretório Ativo Azure
description: Crie uma política de acesso condicional personalizado para exigir a autenticação de vários fatores para tarefas de gestão do Azure
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 717e81a1385b04d3152beac39105c56754c55c40
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89049286"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>Acesso Condicional: Requerem MFA para gestão do Azure

As organizações utilizam uma variedade de serviços Azure e gerem-nos a partir de ferramentas baseadas em Recursos Azure Como:

* Portal do Azure
* Azure PowerShell
* CLI do Azure

Estas ferramentas podem fornecer acesso altamente privilegiado a recursos, que podem alterar configurações em toda a subscrição, configurações de serviço e faturação de subscrição. Para proteger estes recursos privilegiados, a Microsoft recomenda que exija a autenticação de vários fatores para qualquer utilizador que aceda a estes recursos.

## <a name="user-exclusions"></a>Exclusões de utilizadores

As políticas de acesso condicional são ferramentas poderosas, recomendamos excluir as seguintes contas da sua política:

* **Acesso de emergência** ou contas **de break-glass** para evitar o bloqueio de conta em todo o inquilino. No cenário improvável de todos os administradores estarem bloqueados fora do seu inquilino, a sua conta administrativa de acesso de emergência pode ser usada para entrar no arrendatário tomar medidas para recuperar o acesso.
   * Mais informações podem ser encontradas no artigo, [Gerir contas de acesso de emergência em Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Contas de serviço** e **principais serviços**, como a Conta Azure AD Connect Sync. As contas de serviço são contas não interativas que não estão ligadas a nenhum utilizador em particular. Normalmente são utilizados por serviços back-end que permitem o acesso programático a aplicações, mas também são usados para iniciar seduções em sistemas para fins administrativos. Contas de serviço como estas devem ser excluídas, uma vez que o MFA não pode ser concluído programáticamente. As chamadas efetuadas pelos principais serviços não são bloqueadas pelo Acesso Condicional.
   * Se a sua organização tiver estas contas em uso em scripts ou código, considere substituí-las por [identidades geridas.](../managed-identities-azure-resources/overview.md) Como solução temporária, pode excluir estas contas específicas da política de base.

## <a name="create-a-conditional-access-policy"></a>Criar uma política de acesso condicional

Os seguintes passos ajudarão a criar uma política de Acesso Condicional para exigir que aqueles com acesso à aplicação [Microsoft Azure Management](concept-conditional-access-cloud-apps.md#microsoft-azure-management) realizem a autenticação multi-factor.

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue pelo Acesso Condicional de Segurança **do Diretório Ativo Azure**  >  **Security**  >  **Conditional Access**.
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**
   1. Em **Incluir**, selecione **Todos os utilizadores**.
   1. Em **'Excluir',** selecione **Utilizadores e grupos** e escolha as contas de acesso de emergência ou break-glass da sua organização. 
   1. Selecione **Concluído**.
1. No **âmbito das aplicações ou ações cloud**  >  **Inclua**, **selecione selecione apps,** escolha **a Gestão do Azure do Microsoft**e **selecione Select** then **Done**.
1. **Em Condições As**  >  **aplicações do Cliente (Pré-visualização)**, em **Seleção das aplicações do cliente esta política será aplicável para** deixar todos os predefinidos selecionados e selecionar **Feito**.
1. Sob **controlos de acesso**  >  **Grant**, selecione Grant **access**, **Require multi-factor authentication**, e selecione **Select**.
1. Confirme as suas definições e defina **Ativar** a política para **on**.
1. Selecione **Criar** para criar para ativar a sua política.

## <a name="next-steps"></a>Passos seguintes

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Determinar o impacto utilizando o modo de relatório de acesso condicional](howto-conditional-access-insights-reporting.md)

[Simular sinal no comportamento usando o acesso condicional E se a ferramenta](troubleshoot-conditional-access-what-if.md)
