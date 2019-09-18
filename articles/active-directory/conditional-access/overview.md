---
title: O que é o acesso condicional no Azure Active Directory?
description: Saiba como o acesso condicional é o coração do novo plano de controle controlado por identidade.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/17/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0463ffad87d00421c2fcb5c8357406d5f692144
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71075342"
---
# <a name="what-is-conditional-access"></a>O que é o Acesso Condicional?

O perímetro de segurança moderno agora se estende além da rede de uma organização para incluir a identidade do usuário e do dispositivo. As organizações podem utilizar esses sinais de identidade como parte de suas decisões de controle de acesso. 

O acesso condicional é a ferramenta usada por Azure Active Directory para reunir sinais, tomar decisões e impor políticas organizacionais. O acesso condicional é o coração do novo plano de controle controlado por identidade.

![Sinal condicional conceitual mais decisão de obter a imposição](./media/overview/conditional-access-signal-decision-enforcement.png)

As políticas de acesso condicional em suas mais simples são instruções if-then, se um usuário quiser acessar um recurso, ele deverá concluir uma ação. Exemplo: Um gerente de folha de pagamento deseja acessar o aplicativo de folha de pagamento e é necessário para executar a autenticação multifator para acessá-lo.

Os administradores enfrentam dois objetivos principais:

- Capacitar os utilizadores a ser produtivos em qualquer local e em qualquer momento
- Proteger os ativos da organização

Usando políticas de acesso condicional, você pode aplicar os controles de acesso certos quando necessário para manter sua organização segura e ficar fora do caminho do usuário quando não for necessário.

![Fluxo do processo de acesso condicional conceitual](./media/overview/conditional-access-overview-how-it-works.png)

As políticas de acesso condicional são impostas após a conclusão da autenticação de primeiro fator. O acesso condicional não se destina à primeira linha de defesa de uma organização para cenários como ataques de DoS (negação de serviço), mas pode usar sinais desses eventos para determinar o acesso.

## <a name="common-signals"></a>Sinais comuns

Os sinais comuns que o acesso condicional pode levar em conta ao fazer uma decisão de política incluem os seguintes sinais:

- Associação de usuário ou grupo
   - As políticas podem ser direcionadas a usuários e grupos específicos, proporcionando aos administradores o controle refinado sobre o acesso.
- Informações de localização de IP
   - As organizações podem criar intervalos de endereços IP confiáveis que podem ser usados ao tomar decisões sobre a política. 
   - Os administradores podem especificar intervalos de IP de países inteiros para bloquear ou permitir o tráfego.
- Dispositivo
   - Os usuários com dispositivos de plataformas específicas ou marcados com um estado específico podem ser usados ao impor políticas de acesso condicional.
- Aplicação
   - Os usuários que tentarem acessar aplicativos específicos podem disparar diferentes políticas de acesso condicional. 
- Detecção de risco calculado e em tempo real
   - A integração de sinais com Azure AD Identity Protection permite que as políticas de acesso condicional identifiquem o comportamento de entrada arriscado. As políticas podem forçar os usuários a executar alterações de senha ou autenticação multifator para reduzir seu nível de risco ou ser impedido de acesso até que um administrador execute a ação manual.
- Microsoft Cloud App Security (MCAS)
   - Permite que o acesso e as sessões do aplicativo do usuário sejam monitorados e controlados em tempo real, aumentando a visibilidade e o controle sobre o acesso e as atividades executadas em seu ambiente de nuvem.

## <a name="common-decisions"></a>Decisões comuns

- Bloquear acesso
   - Decisão mais restritiva
- Conceder acesso
   - Decisão menos restritiva, ainda pode exigir uma ou mais das seguintes opções:
      - Exigir autenticação multifator
      - Exigir que o dispositivo seja marcado como em conformidade
      - Exigir dispositivo ingressado no Azure AD híbrido
      - Requer aplicação aprovada do cliente
      - Exigir política de proteção de aplicativo (visualização)

## <a name="commonly-applied-policies"></a>Políticas comumente aplicadas

Muitas organizações têm problemas comuns de acesso que as políticas de acesso condicional podem ajudar, como:

- Exigindo a autenticação multifator para usuários com funções administrativas
- Exigindo a autenticação multifator para tarefas de gerenciamento do Azure
- Bloqueando entradas para usuários tentando usar protocolos de autenticação herdados
- Exigindo locais confiáveis para o registro da autenticação multifator do Azure
- Bloqueando ou concedendo acesso de locais específicos
- Bloqueando comportamentos de entrada arriscados
- Exigindo dispositivos gerenciados pela organização para aplicativos específicos

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

Clientes com [licenças de Microsoft 365 Business](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) também têm acesso aos recursos de acesso condicional. 

## <a name="next-steps"></a>Passos seguintes

[Criando uma política de acesso condicional por parte](concept-conditional-access-policies.md)

Para saber como implementar o acesso condicional em seu ambiente, consulte [planejar sua implantação de acesso condicional no Azure Active Directory](plan-conditional-access.md).

[Saiba mais sobre a proteção de identidade](../identity-protection/overview-v2.md)

[Saiba mais sobre o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)

[Saiba mais sobre o Microsoft Intune](https://docs.microsoft.com/intune/index)