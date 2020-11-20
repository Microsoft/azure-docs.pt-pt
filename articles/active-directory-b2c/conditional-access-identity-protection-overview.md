---
title: Proteção de Identidade e Acesso Condicional em Azure AD B2C
description: Saiba como a Proteção de Identidade lhe dá visibilidade em insusões de risco e deteções de risco. Descubra como e o Acesso Condicional permite-lhe impor políticas organizacionais baseadas em eventos de risco nos seus inquilinos Azure AD B2C.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4a71740c487896208a3da7bf35bb39899c56937
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952068"
---
# <a name="identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Proteção de Identidade e Acesso Condicional para Azure AD B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Reforçar a segurança do Azure Ative Directory B2C (Azure AD B2C) com Azure AD Identity Protection e Acesso Condicional. As funcionalidades de deteção de risco de Proteção de Identidade, incluindo utilizadores de risco e insuposições de risco, são automaticamente detetadas e exibidas no seu inquilino Azure AD B2C. Pode criar políticas de Acesso Condicional que utilizem estas deteções de risco para determinar ações e impor políticas organizacionais. Em conjunto, estas capacidades conferem aos proprietários de aplicações AZURE AD B2C um maior controlo sobre autenticações de risco e políticas de acesso.
  
Se já está familiarizado com [a Proteção de Identidade](../active-directory/identity-protection/overview-identity-protection.md) e Acesso [Condicional](../active-directory/conditional-access/overview.md) em AZure AD, usar estas capacidades com Azure AD B2C será uma experiência familiar, com as pequenas diferenças discutidas neste artigo.

![Acesso Condicional a um inquilino B2C](media/conditional-access-identity-protection-overview/conditional-access-b2c.png)

> [!NOTE]
> Para utilizar o Acesso Condicional, é necessário Azure AD B2C Premium P2.

## <a name="benefits-of-identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Benefícios da Proteção de Identidade e Acesso Condicional para Azure AD B2C  

Ao emparelhar políticas de acesso condicional com deteção de riscos de proteção de identidade, pode responder a autenticações arriscadas com a ação política adequada.

- **Ganhe um novo nível de visibilidade para os riscos de autenticação para as suas apps e para a sua base de clientes.** Com sinais de milhares de milhões de autenticações mensais em AD e Conta Microsoft, os algoritmos de deteção de risco passarão a sinalizar autenticações como de baixo, médio ou alto risco para o consumidor local ou para as autenticações do cidadão.
- **Resolver automaticamente os riscos configurando a sua própria autenticação adaptativa**. Para aplicações especificadas, pode exigir um conjunto específico de utilizadores para fornecer um segundo fator de autenticação, como na autenticação de vários fatores (MFA). Ou pode bloquear o acesso com base no nível de risco detetado. Tal como acontece com outras experiências Azure AD B2C, pode personalizar a experiência do utilizador final resultante com a voz, estilo e marca da sua organização. Também pode apresentar alternativas de mitigação se o utilizador não conseguir aceder.
- **Controle o acesso com base na localização, grupos e aplicativos.**O Acesso Condicional também pode ser usado para controlar situações não baseadas em risco. Por exemplo, pode exigir MFA para clientes que acedam a uma aplicação específica, ou bloquear o acesso a partir de geografias especificadas.
- **Integre com os fluxos de utilizador AD B2C Ad B2C e as políticas personalizadas do Quadro de Experiência de Identidade.** Utilize as suas experiências personalizadas existentes e adicione os controlos necessários para interagir com o Acesso Condicional. Também pode implementar cenários avançados para a concessão de acesso, como acesso baseado no conhecimento ou o seu próprio fornecedor de MFA preferido.

## <a name="feature-differences-and-limitations"></a>Diferenças e limitações de recursos

Proteção de Identidade e Acesso Condicional em Azure AD B2C geralmente funcionam da mesma forma que em Azure AD, com as seguintes exceções:

- O Centro de Segurança não está disponível no Azure AD B2C.

- A Proteção de Identidade e o Acesso Condicional não são suportados para fluxos de servidor ropc-servidor em inquilinos Azure AD B2C.

- Nos inquilinos Azure AD B2C, as deteções de risco de Proteção de Identidade estão disponíveis apenas para contas B2C locais, e não para identidades sociais como google ou Facebook.

- Nos inquilinos Azure AD B2C, está disponível um subconjunto de deteções de risco de Proteção de Identidade. Consulte [a Configuração da Proteção de Identidade](conditional-access-identity-protection-setup.md#set-up-identity-protection).

- A funcionalidade de conformidade do dispositivo de acesso condicional não está disponível nos inquilinos Azure AD B2C.


## <a name="integrate-conditional-access-with-user-flows-and-custom-policies"></a>Integrar acesso condicional com fluxos de utilizador e políticas personalizadas

No Azure AD B2C, pode desencadear condições de acesso condicional a partir de fluxos de utilizador incorporados. Também pode incorporar o Acesso Condicional em políticas personalizadas. Tal como acontece com outros aspetos do fluxo de utilizador B2C, as mensagens de experiência de utilizador final podem ser personalizadas de acordo com as alternativas de voz, marca e mitigação da sua organização. Ver [Definir um perfil técnico de acesso condicional.](conditional-access-technical-profile.md)

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Também pode gerir as políticas de Acesso Condicional em Azure AD B2C com a Microsoft Graph API. Para mais informações, consulte a [documentação de Acesso Condicional](../active-directory/conditional-access/overview.md) e a [referência do Microsoft Graph](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta.md).

## <a name="next-steps"></a>Passos seguintes

- [Configurar proteção de identidade e acesso condicional para Azure AD B2C](conditional-access-identity-protection-setup.md)
- [Saiba mais sobre proteção de identidade em Azure AD](../active-directory/identity-protection/overview-identity-protection.md)
- [Saiba mais sobre acesso condicional](../active-directory/conditional-access/overview.md)