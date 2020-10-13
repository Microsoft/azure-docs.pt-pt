---
title: Acesso Condicional - Requer MFA para todos os utilizadores - Azure Ative Directory
description: Crie uma política de acesso condicional personalizado para exigir que todos os utilizadores realizem a autenticação de vários fatores
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
ms.openlocfilehash: a45ce7bee04716612431effe77315d739f328dba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89049303"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>Acesso Condicional: Requer MFA para todos os utilizadores

Como refere Alex Weinert, o Diretor de Segurança de Identidade da Microsoft, menciona no seu blogue [Your Pa$$word não importa:](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)

> A tua senha não importa, mas a MFA importa! Com base nos nossos estudos, a sua conta é mais de 99,9% menos provável de ser comprometida se utilizar MFA.

A orientação neste artigo ajudará a sua organização a criar uma política de MFA equilibrada para o seu ambiente.

## <a name="user-exclusions"></a>Exclusões de utilizadores

As políticas de acesso condicional são ferramentas poderosas, recomendamos excluir as seguintes contas da sua política:

* **Acesso de emergência** ou contas **de break-glass** para evitar o bloqueio de conta em todo o inquilino. No cenário improvável de todos os administradores estarem bloqueados fora do seu inquilino, a sua conta administrativa de acesso de emergência pode ser usada para entrar no arrendatário tomar medidas para recuperar o acesso.
   * Mais informações podem ser encontradas no artigo, [Gerir contas de acesso de emergência em Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Contas de serviço** e **principais serviços**, como a Conta Azure AD Connect Sync. As contas de serviço são contas não interativas que não estão ligadas a nenhum utilizador em particular. Normalmente são utilizados por serviços back-end que permitem o acesso programático a aplicações, mas também são usados para iniciar seduções em sistemas para fins administrativos. Contas de serviço como estas devem ser excluídas, uma vez que o MFA não pode ser concluído programáticamente. As chamadas efetuadas pelos principais serviços não são bloqueadas pelo Acesso Condicional.
   * Se a sua organização tiver estas contas em uso em scripts ou código, considere substituí-las por [identidades geridas.](../managed-identities-azure-resources/overview.md) Como solução temporária, pode excluir estas contas específicas da política de base.

## <a name="application-exclusions"></a>Exclusões de aplicações

As organizações podem ter muitas aplicações em nuvem em uso. Nem todas essas aplicações podem requerer segurança igual. Por exemplo, a folha de pagamento e as candidaturas de atendimento podem requerer MFA, mas o refeitório provavelmente não. Os administradores podem optar por excluir aplicações específicas da sua política.

## <a name="create-a-conditional-access-policy"></a>Criar uma política de acesso condicional

Os seguintes passos ajudarão a criar uma política de acesso condicional para exigir que todos os utilizadores realizem a autenticação de vários fatores.

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue pelo Acesso Condicional de Segurança **do Diretório Ativo Azure**  >  **Security**  >  **Conditional Access**.
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**
   1. Em **Incluir**, selecione **Todos os utilizadores**
   1. Em **'Excluir',** selecione **Utilizadores e grupos** e escolha as contas de acesso de emergência ou break-glass da sua organização. 
   1. Selecione **Concluído**.
1. No **âmbito de aplicações ou ações cloud**  >  **Inclua**, selecione todas as **aplicações em nuvem**.
   1. Em **'Excluir'** selecione quaisquer aplicações que não exijam autenticação multi-factor.
1. **Em Condições As**  >  **aplicações do Cliente (Pré-visualização)**, em **Seleção das aplicações do cliente esta política será aplicável para** deixar todos os predefinidos selecionados e selecionar **Feito**.
1. Sob **controlos de acesso**  >  **Grant**, selecione Grant **access**, **Require multi-factor authentication**, e selecione **Select**.
1. Confirme as suas definições e defina **Ativar** a política para **on**.
1. Selecione **Criar** para criar para ativar a sua política.

### <a name="named-locations"></a>Localizações com nome

As organizações podem optar por incorporar locais de rede conhecidos como **locais nomeados** para as suas políticas de Acesso Condicional. Estas localizações nomeadas podem incluir redes IPv4 fidedignas como as de um escritório principal. Para obter mais informações sobre a configuração de locais nomeados, consulte o artigo [Qual é a condição de localização no Azure Ative Directory Conditional Access?](location-condition.md)

Na política de exemplo acima, uma organização pode optar por não exigir a autenticação de vários fatores se aceder a uma aplicação em nuvem a partir da sua rede corporativa. Neste caso, podem adicionar a seguinte configuração à política:

1. Em **Atribuições**, selecione **Locais de Condições**  >  **Locations**.
   1. Configurar **Sim.**
   1. Incluir **qualquer localização.**
   1. Excluir **todos os locais fidedignos.**
   1. Selecione **Concluído**.
1. Selecione **Concluído**.
1. **Guarde** as suas mudanças de política.

## <a name="next-steps"></a>Passos seguintes

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Determinar o impacto utilizando o modo de relatório de acesso condicional](howto-conditional-access-insights-reporting.md)

[Simular sinal no comportamento usando o acesso condicional E se a ferramenta](troubleshoot-conditional-access-what-if.md)
