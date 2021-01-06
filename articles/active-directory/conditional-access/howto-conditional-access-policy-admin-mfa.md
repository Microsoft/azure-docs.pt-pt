---
title: Acesso Condicional - Requerem MFA para administradores - Diretório Ativo Azure
description: Crie uma política de acesso condicional personalizado para exigir que os administradores realizem a autenticação de vários fatores
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 08/03/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57826fcff03e79d5617c7eb69aac7d535d3c86f7
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97915713"
---
# <a name="conditional-access-require-mfa-for-administrators"></a>Acesso Condicional: Exigir MFA para administradores

As contas que lhes são atribuídas direitos administrativos são alvo de agressores. Exigir a autenticação de vários fatores (MFA) nessas contas é uma forma fácil de reduzir o risco de comprometer essas contas.

A Microsoft recomenda que exija MFA nas seguintes funções no mínimo:

* Administrador de Autenticação
* Administrador de faturação
* Administrador de acesso condicional
* Administrador do Exchange
* Administrador global
* Administrador da Helpdesk
* Administrador de palavras-passe
* Administrador privilegiado
* Administrador de segurança
* Administrador do SharePoint
* Administrador de utilizadores

As organizações podem optar por incluir ou excluir funções como entenderem.

## <a name="user-exclusions"></a>Exclusões de utilizadores

As políticas de acesso condicional são ferramentas poderosas, recomendamos excluir as seguintes contas da sua política:

* **Acesso de emergência** ou contas **de break-glass** para evitar o bloqueio de conta em todo o inquilino. No cenário improvável de todos os administradores estarem bloqueados fora do seu inquilino, a sua conta administrativa de acesso de emergência pode ser usada para entrar no arrendatário tomar medidas para recuperar o acesso.
   * Mais informações podem ser encontradas no artigo, [Gerir contas de acesso de emergência em Azure AD](../roles/security-emergency-access.md).
* **Contas de serviço** e **principais serviços**, como a Conta Azure AD Connect Sync. As contas de serviço são contas não interativas que não estão ligadas a nenhum utilizador em particular. Normalmente são utilizados por serviços back-end que permitem o acesso programático a aplicações, mas também são usados para iniciar seduções em sistemas para fins administrativos. Contas de serviço como estas devem ser excluídas, uma vez que o MFA não pode ser concluído programáticamente. As chamadas efetuadas pelos principais serviços não são bloqueadas pelo Acesso Condicional.
   * Se a sua organização tiver estas contas em uso em scripts ou código, considere substituí-las por [identidades geridas.](../managed-identities-azure-resources/overview.md) Como solução temporária, pode excluir estas contas específicas da política de base.

## <a name="create-a-conditional-access-policy"></a>Criar uma política de acesso condicional

As seguintes etapas ajudarão a criar uma política de acesso condicional para exigir que as funções administrativas atribuídas realizem a autenticação de vários fatores.

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue pelo Acesso Condicional de Segurança **do Diretório Ativo Azure**  >    >  .
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**
   1. Em **Incluir**, selecione **funções de diretório (pré-visualização)** e escolha as seguintes funções no mínimo:
      * Administrador de Autenticação
      * Administrador de faturação
      * Administrador de acesso condicional
      * Administrador do Exchange
      * Administrador global
      * Administrador da Helpdesk
      * Administrador de palavras-passe
      * Administrador de segurança
      * Administrador do SharePoint
      * Administrador de utilizadores
   
      > [!WARNING]
      > As políticas de Acesso Condicional não suportam os utilizadores atribuídos a uma função de diretório [a uma unidade administrativa](../roles/admin-units-assign-roles.md) ou a funções de diretórios dirigidas diretamente a um objeto, como através de [funções personalizadas](../roles/custom-create.md).

   1. Em **'Excluir',** selecione **Utilizadores e grupos** e escolha as contas de acesso de emergência ou break-glass da sua organização. 
   1. Selecione **Concluído**.
1. Em **aplicativos ou ações cloud**  >  **Inclua**, selecione todas as **aplicações em nuvem** e selecione **'Feito'.**
1. Em **Condições As**  >  **aplicações do Cliente**, altere o **Configurar** para Sim e em **Selecione as aplicações do cliente esta política será aplicável para** deixar todos os predefinidos selecionados e selecionar **Feito**. 
1. Sob **controlos de acesso**  >  **Grant**, selecione Grant **access**, **Require multi-factor authentication**, e selecione **Select**.
1. Confirme as suas definições e defina **Ativar** a política para **on**.
1. Selecione **Criar** para criar para ativar a sua política.

## <a name="next-steps"></a>Passos seguintes

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Determinar o impacto utilizando o modo de relatório de acesso condicional](howto-conditional-access-insights-reporting.md)

[Simular sinal no comportamento usando o acesso condicional E se a ferramenta](troubleshoot-conditional-access-what-if.md)
