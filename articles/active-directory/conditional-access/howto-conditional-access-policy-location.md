---
title: Acesso condicional-bloquear acesso por local-Azure Active Directory
description: Criar uma política de acesso condicional personalizada para bloquear o acesso a recursos por local IP
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
ms.openlocfilehash: 64b7add9ddb1734d763c46e93d6788ce21a2c321
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73150888"
---
# <a name="conditional-access-block-access-by-location"></a>Acesso condicional: bloquear o acesso por local

Com a condição de localização no acesso condicional, você pode controlar o acesso aos seus aplicativos de nuvem com base no local de rede de um usuário. A condição de localização é comumente usada para bloquear o acesso de países onde sua organização sabe que o tráfego não deveria vir do.

## <a name="define-locations"></a>Definir locais

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **Azure Active Directory** **acesso condicional** > .
1. Escolha **novo local**.
1. Dê um nome ao seu local.
1. Escolha **intervalos de IP** se você souber os intervalos de endereços IPv4 acessíveis externamente que compõem esse local ou **países/regiões**.
   1. Forneça os **intervalos de IP** ou selecione os **países/regiões** para o local que você está especificando.
      * Se você escolheu países/regiões, você pode opcionalmente optar por incluir áreas desconhecidas.
1. Escolha **salvar**

Mais informações sobre a condição de localização no acesso condicional podem ser encontradas no artigo, [qual é a condição de local em Azure Active Directory acesso condicional](location-condition.md)

## <a name="create-a-conditional-access-policy"></a>Criar uma política de acesso condicional

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **Azure Active Directory** **acesso condicional** > .
1. Selecione **nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **atribuições**, selecione **usuários e grupos**
   1. Em **incluir**, selecione **todos os usuários**.
   1. Selecione **Done** (Concluído).
1. Em **aplicativos de nuvem ou ações** > **incluir**, selecione **todos os aplicativos de nuvem**e selecione **concluído**.
1. Em **condições** > **local**.
   1. Defina **Configurar** como **Sim**
   1. **Incluir** **locais selecionados** de seleção
   1. Selecione o local bloqueado que você criou para sua organização.
   1. Clique em **selecionar** > **concluído** > **concluído**.
1. Em **controles de acesso** > **bloco**e selecione **selecionar**.
1. Confirme suas configurações e defina **habilitar política** como **ativado**.
1. Selecione **criar** para criar para habilitar a política.

## <a name="next-steps"></a>Passos seguintes

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Simular comportamento de entrada usando a ferramenta de What If de acesso condicional](troubleshoot-conditional-access-what-if.md)
