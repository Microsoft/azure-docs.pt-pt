---
title: Acesso condicional-bloquear autenticação herdada-Azure Active Directory
description: Criar uma política de acesso condicional personalizada para bloquear protocolos de autenticação herdados
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b992973beb7cb132075e47e104733d812dc06ca0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73151079"
---
# <a name="conditional-access-block-legacy-authentication"></a>Acesso condicional: bloquear autenticação herdada

Devido ao maior risco associado aos protocolos de autenticação herdados, a Microsoft recomenda que as organizações bloqueiem as solicitações de autenticação usando esses protocolos e exijam autenticação moderna.

## <a name="create-a-conditional-access-policy"></a>Criar uma política de acesso condicional

As etapas a seguir ajudarão a criar uma política de acesso condicional para bloquear as solicitações de autenticação herdadas.

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **Azure Active Directory** **acesso condicional** > .
1. Selecione **nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **atribuições**, selecione **usuários e grupos**
   1. Em **incluir**, selecione **todos os usuários**.
   1. Em **excluir**, selecione **usuários e grupos** e escolha as contas que devem manter a capacidade de usar a autenticação herdada. 
   1. Selecione **Done** (Concluído).
1. Em **aplicativos de nuvem ou ações** > **incluir**, selecione **todos os aplicativos de nuvem**.
   1. Se você precisar excluir aplicativos específicos de sua política, poderá selecioná-los na guia **excluir** em **selecionar aplicativos de nuvem excluídos** e escolher **selecionar**.
   1. Selecione **Done** (Concluído).
1. Em **condições** > **aplicativos cliente (versão prévia)** , defina **Configurar** como **Sim**.
   1. Marque apenas as caixas **aplicativos móveis e clientes de área de trabalho** > **outros clientes**.
   2. Selecione **Done** (Concluído).
1. Em **controles de acesso** > **Grant**, selecione **bloquear acesso**.
   1. Selecione **selecionar**.
1. Confirme suas configurações e defina **habilitar política** como **ativado**.
1. Selecione **criar** para criar para habilitar a política.

## <a name="next-steps"></a>Passos seguintes

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Simular comportamento de entrada usando a ferramenta de What If de acesso condicional](troubleshoot-conditional-access-what-if.md)
