---
title: O que é o Acesso Condicional no Diretório Ativo Azure?
description: Saiba como o Acesso Condicional está no centro do novo plano de controlo orientado por identidade.
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
ms.openlocfilehash: 7b044a4fd4e29bfe35abff7a4b36e5bae783328b
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671881"
---
# <a name="what-is-conditional-access"></a>O que é o Acesso Condicional?

O moderno perímetro de segurança estende-se agora para além da rede de uma organização para incluir a identidade do utilizador e do dispositivo. As organizações podem utilizar estes sinais de identidade como parte das suas decisões de controlo de acesso. 

O Acesso Condicional é a ferramenta utilizada pelo Azure Ative Directory para reunir sinais, tomar decisões e impor políticas organizacionais. O Acesso Condicional está no centro do novo plano de controlo orientado por identidade.

![Sinal condicional conceptual mais decisão de obter a aplicação da lei](./media/overview/conditional-access-signal-decision-enforcement.png)

As políticas de Acesso Condicional na sua mais simples são declarações se então, se um utilizador quer aceder a um recurso, então deve concluir uma ação. Exemplo: Um gestor de folha de pagamento quer aceder à aplicação de folha de pagamento e é obrigado a realizar a autenticação de vários fatores para aceder ao mesmo.

Os administradores são confrontados com dois objetivos primários:

- Capacitar os utilizadores a ser produtivos em qualquer local e em qualquer momento
- Proteja os bens da organização

Ao utilizar as políticas de Acesso Condicional, pode aplicar os controlos de acesso certos quando necessário para manter a sua organização segura e ficar fora do caminho do utilizador quando não for necessário.

![Fluxo de processo de acesso condicional conceptual](./media/overview/conditional-access-overview-how-it-works.png)

As políticas de Acesso Condicional são aplicadas após a autenticação do primeiro fator ter sido concluída. O Acesso Condicional não se destina a ser a primeira linha de defesa de uma organização para cenários como ataques de negação de serviço (DoS), mas pode usar sinais destes eventos para determinar o acesso.

## <a name="common-signals"></a>Sinais comuns

Os sinais comuns que o Acesso Condicional pode ter em conta na tomada de uma decisão política incluem os seguintes sinais:

- Membro do utilizador ou do grupo
   - As políticas podem ser direcionadas a utilizadores e grupos específicos que dêem aos administradores um controlo fino sobre o acesso.
- Informações de localização IP
   - As organizações podem criar gamas de endereços IP fidedignos que podem ser usadas na tomada de decisões políticas. 
   - Os administradores podem especificar gamas IP de países inteiros para bloquear ou permitir o tráfego a partir de.
- Dispositivo
   - Os utilizadores com dispositivos de plataformas específicas ou marcados com um estado específico podem ser utilizados na aplicação das políticas de Acesso Condicional.
- Aplicação
   - Os utilizadores que tentem aceder a aplicações específicas podem desencadear diferentes políticas de Acesso Condicional. 
- Deteção de risco em tempo real e calculada
   - A integração de sinais com a Proteção de Identidade Azure AD permite políticas de acesso condicional para identificar comportamentos de entrada de risco. As políticas podem então forçar os utilizadores a efetuar alterações de palavra-passe ou autenticação de vários fatores para reduzir o seu nível de risco ou ser bloqueado saem do acesso até que um administrador tome medidas manuais.
- Microsoft Cloud App Security (MCAS)
   - Permite que o acesso e as sessões de aplicação ao utilizador sejam monitorizados e controlados em tempo real, aumentando a visibilidade e o controlo sobre o acesso e as atividades realizadas dentro do seu ambiente na nuvem.

## <a name="common-decisions"></a>Decisões comuns

- Acesso ao bloco
   - Decisão mais restritiva
- Conceder acesso
   - Decisão menos restritiva, pode ainda exigir uma ou mais das seguintes opções:
      - Exigir autenticação de vários fatores
      - Exigir que o dispositivo seja marcado como conforme
      - Exigir dispositivo ad ad hybrid Azure
      - Requer aplicação aprovada do cliente
      - Exigir política de proteção de aplicativos (pré-visualização)

## <a name="commonly-applied-policies"></a>Políticas geralmente aplicadas

Muitas organizações têm preocupações comuns de acesso que as políticas de Acesso Condicional podem ajudar com tais:

- Exigir a autenticação de vários fatores para utilizadores com funções administrativas
- Exigir a autenticação de vários fatores para tarefas de gestão do Azure
- Bloqueio de inscrições para utilizadores que tentem usar protocolos de autenticação legado
- Exigindo localizações fidedignas para o registo de autenticação multi-factor Azure
- Bloquear ou conceder acesso a partir de locais específicos
- Bloquear comportamentos de inscrição arriscados
- Exigindo dispositivos geridos pela organização para aplicações específicas

## <a name="customer-case-studies"></a>Casos práticos de clientes

Descubra como outras organizações usam o Acesso Condicional Azure AD para definir e implementar decisões automatizadas de controlo de acesso. As seguintes histórias em destaque demonstram como estas necessidades dos clientes são satisfeitas.

* [A Wipro impulsiona a produtividade móvel com ferramentas de segurança na nuvem da Microsoft para melhorar os compromissos dos clientes.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) As políticas de Acesso Condicional na AD Azure permitiram à empresa partilhar documentos, recursos e aplicações com entidades externas de confiança--- que podem usar as suas próprias credenciais--- mantendo o controlo sobre os seus próprios dados corporativos.
* [Entrega limitada da Aramex - Empresa global de logística e transporte cria escritório ligado à nuvem com solução de gestão de identidade e acesso.](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en) Garantir um acesso seguro foi especialmente difícil com os funcionários remotos da Aramex. A empresa está agora a aplicar acesso condicional para permitir que estes funcionários remotos acedam às suas aplicações SaaS de fora da rede. A regra de Acesso Condicional decidirá se aplicará a Autenticação Multi-Factor, dando apenas às pessoas certas o acesso certo.

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

Os clientes com [licenças empresariais Microsoft 365](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) também têm acesso às funcionalidades de Acesso Condicional. 

## <a name="next-steps"></a>Passos seguintes

[Construção de uma política de acesso condicional peça por peça](concept-conditional-access-policies.md)

Para aprender a implementar o Acesso Condicional no seu ambiente, consulte [Planize a sua implementação de Acesso Condicional no Diretório Ativo Azure](plan-conditional-access.md).

[Conheça a Proteção de Identidade](../identity-protection/overview-v2.md)

[Saiba mais sobre o Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security)

[Saiba mais sobre o Microsoft Intune](/intune/index)