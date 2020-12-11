---
title: O que é Acesso Condicional no Diretório Ativo Azure?
description: Saiba como o Acesso Condicional está no centro do novo plano de controlo de identidade.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4, azuread-video-2020
ms.openlocfilehash: 9c5f7684083ec533c8100785787572ebe4456bcc
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030251"
---
# <a name="what-is-conditional-access"></a>O que é o Acesso Condicional?

O moderno perímetro de segurança estende-se agora para além da rede de uma organização para incluir a identidade do utilizador e do dispositivo. As organizações podem utilizar estes sinais de identidade como parte das suas decisões de controlo de acesso. 

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-AD-Conditional-Access/player]

O Acesso Condicional é a ferramenta utilizada pelo Azure Ative Directory para reunir sinais, tomar decisões e impor políticas organizacionais. O Acesso Condicional está no centro do novo plano de controlo de identidade.

![Sinal condicional conceptual mais decisão de obter a aplicação](./media/overview/conditional-access-signal-decision-enforcement.png)

As políticas de Acesso Condicional na sua mais simples são declarações se-então, se um utilizador quiser aceder a um recurso, então deve completar uma ação. Exemplo: Um gestor de folha de pagamento quer aceder à aplicação de folha de pagamento e é obrigado a realizar a autenticação de vários fatores para aceder à sua conta.

Os administradores são confrontados com dois objetivos primários:

- Capacitar os utilizadores a ser produtivos em qualquer local e em qualquer momento
- Proteja os bens da organização

Ao utilizar as políticas de Acesso Condicional, pode aplicar os controlos de acesso certos quando necessário para manter a sua organização segura e ficar fora do caminho do seu utilizador quando não for necessário.

![Fluxo de processo de acesso condicional conceptual](./media/overview/conditional-access-overview-how-it-works.png)

> [!IMPORTANT]
> As políticas de acesso condicional são aplicadas após a autenticação do primeiro fator estar concluída. O Acesso Condicional não pretende ser a primeira linha de defesa de uma organização para cenários como ataques de negação de serviço (DoS), mas pode usar sinais destes eventos para determinar o acesso.

## <a name="common-signals"></a>Sinais comuns

Os sinais comuns que o Acesso Condicional pode ter em conta ao tomar uma decisão política incluem os seguintes sinais:

- Adesão a utilizadores ou grupos
   - As políticas podem ser direcionadas para utilizadores e grupos específicos que dão aos administradores um controlo fino sobre o acesso.
- Informações de localização IP
   - As organizações podem criar intervalos de endereços IP fidedignos que podem ser usados ao tomar decisões políticas. 
   - Os administradores podem especificar países/regiões inteiros gamas IP para bloquear ou permitir o tráfego de.
- Dispositivo
   - Os utilizadores com dispositivos de plataformas específicas ou marcados com um estado específico podem ser utilizados na aplicação de políticas de Acesso Condicional.
- Aplicação
   - Os utilizadores que tentam aceder a aplicações específicas podem desencadear diferentes políticas de Acesso Condicional. 
- Deteção de risco em tempo real e calculada
   - A integração de sinais com a Azure AD Identity Protection permite que as políticas de acesso condicional identifiquem comportamentos de acesso de risco. As políticas podem então forçar os utilizadores a realizar alterações de palavra-passe ou autenticação de vários fatores para reduzir o seu nível de risco ou ser bloqueados do acesso até que um administrador tome medidas manuais.
- Segurança da Aplicação Microsoft Cloud (MCAS)
   - Permite que o acesso e sessões de aplicações do utilizador sejam monitorizados e controlados em tempo real, aumentando a visibilidade e o controlo sobre o acesso e atividades realizadas dentro do seu ambiente na nuvem.

## <a name="common-decisions"></a>Decisões comuns

- Bloquear o acesso
   - Decisão mais restritiva
- Conceder acesso
   - Uma decisão menos restritiva, pode ainda exigir uma ou mais das seguintes opções:
      - Requerem autenticação de vários fatores
      - Exigir que o dispositivo seja marcado como conforme
      - Requera o dispositivo de ad AD híbrido Azure
      - Requera uma aplicação de cliente aprovada
      - Requerer a política de proteção de aplicações (pré-visualização)

## <a name="commonly-applied-policies"></a>Políticas geralmente aplicadas

Muitas organizações têm [preocupações de acesso comuns que as políticas de acesso condicional podem ajudar,](concept-conditional-access-policy-common.md) tais como:

- Exigir a autenticação de vários fatores para utilizadores com funções administrativas
- Exigir autenticação multi-factor para tarefas de gestão Azure
- Bloqueio de inscrições para utilizadores que tentam utilizar protocolos de autenticação de legados
- Exigir localizações fidedignas para o registo de autenticação multi-factor Azure AD
- Bloquear ou conceder acesso a partir de locais específicos
- Bloquear comportamentos de sinalização de risco
- Exigir dispositivos geridos pela organização para aplicações específicas

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

Os clientes com [licenças Microsoft 365 Business Premium](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) também têm acesso a funcionalidades de Acesso Condicional. 

[O risco de acesso](concept-conditional-access-conditions.md#sign-in-risk) requer acesso à [Proteção de Identidade](../identity-protection/overview-identity-protection.md)

## <a name="next-steps"></a>Passos seguintes

- [Construção de uma política de acesso condicional peça por peça](concept-conditional-access-policies.md)
- [Planear a implementação do Acesso Condicional](plan-conditional-access.md)
- [Saiba mais sobre proteção de identidade](../identity-protection/overview-identity-protection.md)
- [Saiba mais sobre a Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security)
- [Conheça o Microsoft Intune](/intune/index)
