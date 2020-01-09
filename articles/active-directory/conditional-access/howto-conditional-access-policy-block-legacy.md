---
title: Acesso condicional-bloquear autenticação herdada-Azure Active Directory
description: Criar uma política de acesso condicional personalizada para bloquear protocolos de autenticação herdados
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: c40993df8033b9dbc49c81e8db2f9f01c6de37d9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424927"
---
# <a name="conditional-access-block-legacy-authentication"></a>Acesso condicional: bloquear autenticação herdada

Devido ao maior risco associado aos protocolos de autenticação herdados, a Microsoft recomenda que as organizações bloqueiem as solicitações de autenticação usando esses protocolos e exijam autenticação moderna.

## <a name="create-a-conditional-access-policy"></a>Criar uma política de acesso condicional

As etapas a seguir ajudarão a criar uma política de acesso condicional para bloquear as solicitações de autenticação herdadas.

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **Azure Active Directory** > **segurança** > **acesso condicional**.
1. Selecione **nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **atribuições**, selecione **usuários e grupos**
   1. Em **incluir**, selecione **todos os usuários**.
   1. Em **excluir**, selecione **usuários e grupos** e escolha as contas que devem manter a capacidade de usar a autenticação herdada. 
   1. Selecione **Done** (Concluído).
1. Em **aplicativos de nuvem ou ações** , selecione **todos os aplicativos de nuvem**.
   1. Selecione **Done** (Concluído).
1. Em **condições** > **aplicativos cliente (versão prévia)** , defina **Configurar** como **Sim**.
   1. Marque apenas as caixas **aplicativos móveis e clientes de área de trabalho** > **outros clientes**.
   1. Selecione **Done** (Concluído).
1. Em **controles de acesso** > **concessão**, selecione **bloquear acesso**.
   1. Selecione **Selecionar**.
1. Confirme suas configurações e defina **habilitar política** como **ativado**.
1. Selecione **criar** para criar para habilitar a política.

## <a name="next-steps"></a>Passos seguintes

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Determinar o impacto usando o modo somente relatório de acesso condicional](howto-conditional-access-report-only.md)

[Simular comportamento de entrada usando a ferramenta de What If de acesso condicional](troubleshoot-conditional-access-what-if.md)
