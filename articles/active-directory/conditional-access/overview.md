---
title: O que é o acesso condicional no Azure Active Directory? | Microsoft Docs
description: Saiba como o acesso condicional no Azure Active Directory ajuda a implementar decisões de acesso automatizadas que não se baseiam apenas em quem tenta acessar um recurso, mas também como um recurso é acessado.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 02/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99d17b354e267d003e23e507ca190b951e3ed4a0
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608094"
---
# <a name="what-is-conditional-access"></a>O que é o Acesso Condicional?

A segurança é uma das principais preocupações para as organizações que utilizam a cloud. Um dos principais aspetos da segurança na cloud é a identidade e o acesso no que diz respeito a gerir os recursos na cloud. Num mundo “mobile-first, cloud-first”, os utilizadores podem aceder aos recursos da sua organização com diversos dispositivos e aplicações em qualquer local. Como resultado, limitar-se a concentrar-se apenas em quem pode aceder a um recurso já não é suficiente. Para controlar o equilíbrio entre segurança e produtividade, também tem de ter em conta de que forma é que se acede a um determinado recurso numa decisão de controlo de acesso. Com o acesso condicional do Azure Active Directory (Azure AD), você pode atender a esse requisito. O acesso condicional é uma funcionalidade de Azure Active Directory. Com o acesso condicional, você pode implementar decisões de controle de acesso automatizadas para acessar seus aplicativos de nuvem que se baseiam em condições.

As políticas de acesso condicional são impostas após a conclusão da autenticação de primeiro fator. Portanto, o acesso condicional não se destina como uma defesa de primeira linha para cenários como ataques de DoS (negação de serviço), mas pode utilizar sinais desses eventos (por exemplo, o nível de risco de entrada, o local da solicitação e assim por diante) para determinar o acesso.  

![Controlar](./media/overview/81.png)

Este artigo fornece uma visão geral conceitual do acesso condicional no Azure AD.

## <a name="common-scenarios"></a>Cenários comuns

Num mundo “mobile-first, cloud-first”, o Azure Active Directory proporciona o início de sessão único em dispositivos, aplicações e serviços, em qualquer local. Com a proliferação de dispositivos (incluindo dispositivos BYOD), do trabalho fora das redes das empresas e das aplicações SaaS de terceiros, as equipas de TI estão perante dois objetivos opostos:

- Capacitar os utilizadores a ser produtivos em qualquer local e em qualquer momento
- Proteger os recursos da empresa em todos os momentos

Usando políticas de acesso condicional, você pode aplicar os controles de acesso certos sob as condições necessárias. O acesso condicional do Azure AD fornece segurança adicional quando necessário e permanece fora do caminho do usuário quando não está.

A seguir estão algumas preocupações comuns de acesso que o acesso condicional pode ajudar com:

- **[Risco de entrada](conditions.md#sign-in-risk)** : Azure AD Identity Protection detecta os riscos de entrada. Como restringe o acesso se um risco de início de sessão detetado indicar um ator malicioso? E se quiser ter provas mais sólidas de que um início de sessão foi feito por um utilizador legítimo? E se as suas dúvidas forem tão fundamentadas para o levarem inclusivamente a impedir utilizadores específicos de aceder a uma aplicação?  
- **[Local de rede](location-condition.md)** : O Azure AD pode ser acessado de qualquer lugar. E se alguém tentar aceder numa localização de rede que não está sob o controlo do seu departamento de TI? A combinação de nome de utilizador e palavra-passe pode ser uma prova de identidade suficientemente forte para tentativas de acesso feitas a partir da rede da sua empresa. E se precisar de uma prova de identidade mais sólida para as tentativas de acesso que são iniciadas noutros países ou regiões do mundo inesperados? E se quiser até bloquear tentativas de acesso em determinadas localizações?  
- **[Gerenciamento de dispositivo](conditions.md#device-platforms)** : No Azure AD, os usuários podem acessar aplicativos de nuvem de uma ampla variedade de dispositivos, incluindo dispositivos móveis e também pessoais. E se quiser que as tentativas de acesso sejam feitas apenas com dispositivos geridos pelo seu departamento de TI? E se quiser inclusivamente impedir determinados tipos de dispositivos de aceder às aplicações na cloud no seu ambiente?
- **[Aplicativo cliente](conditions.md#client-apps)** : Hoje, você pode acessar muitos aplicativos de nuvem usando diferentes tipos de aplicativo, como aplicativos baseados na Web, aplicativos móveis ou aplicativos da área de trabalho. E se for feita uma tentativa de acesso com um tipo de aplicação cliente que origina problemas conhecidos? E se quiser que seja utilizado um dispositivo gerido pelo departamento de TI para determinados tipos de aplicações?

Essas perguntas e as respostas relacionadas representam cenários de acesso comuns para acesso condicional do Azure AD.
O acesso condicional é uma funcionalidade de Azure Active Directory que permite que você manipule cenários de acesso usando uma abordagem baseada em políticas.

> [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

## <a name="conditional-access-policies"></a>Políticas de acesso condicional

Uma política de acesso condicional é uma definição de um cenário de acesso usando o seguinte padrão:

![Controlar](./media/overview/10.png)


**When this happens** (Quando isto acontece) define o motivo para acionar a política. Esse motivo é caracterizado por um conjunto de condições que foram verificadas. No acesso condicional do Azure AD, as duas condições de atribuição desempenham uma função especial:

- **[Usuários](conditions.md#users-and-groups)** : Os usuários que executam uma tentativa de acesso (**quem**).
- **[Aplicativos de nuvem](conditions.md#cloud-apps-and-actions)** : Os destinos de uma tentativa de acesso (**o que**).

Essas duas condições são obrigatórias em uma política de acesso condicional. Para além dessas duas condições obrigatórias, também pode incluir outras que descrevem a forma como a tentativa de acesso é feita. Os exemplos mais comuns são utilizar dispositivos móveis ou localizações fora da rede da sua empresa. Para obter mais informações, consulte [condições em Azure Active Directory acesso condicional](conditions.md).

A combinação de condições com seus controles de acesso representa uma política de acesso condicional.

![Controlar](./media/overview/51.png)

Com o acesso condicional do Azure AD, você pode controlar como os usuários autorizados podem acessar seus aplicativos de nuvem. O objetivo de uma política de acesso condicional é impor controles de acesso adicionais em uma tentativa de acesso a um aplicativo de nuvem com base em como uma tentativa de acesso é executada.

Uma abordagem baseada em políticas para proteger o acesso às aplicações na cloud é começar a desenhar os requisitos das políticas para o seu ambiente com a estrutura descrita neste artigo sem se preocupar com a implementação técnica.

## <a name="azure-ad-conditional-access-and-federated-authentication"></a>Acesso condicional e autenticação federada do Azure AD

As políticas de acesso condicional funcionam diretamente com a [autenticação federada](../../security/fundamentals/choose-ad-authn.md#federated-authentication). Esse suporte inclui todas as condições e controles com suporte e a visibilidade de como a política é aplicada a entradas de usuário ativas usando o [relatório do Azure ad](../reports-monitoring/concept-sign-ins.md).

*Autenticação federada com o Azure AD* significa que um serviço de autenticação fidedigno processa a autenticação do utilizador no Azure AD. Um serviço de autenticação fidedigno é, por exemplo, o Active Directory Federation Services (AD FS) ou qualquer outro serviço de federação. Nesta configuração, a autenticação de utilizador principal é executada no serviço e, em seguida, o Azure AD é utilizado para iniciar sessão em aplicações individuais. O acesso condicional do Azure AD é aplicado antes de o acesso ser concedido ao aplicativo que o usuário está acessando. 

Quando a política de acesso condicional configurada requer a autenticação multifator, o Azure AD usa como padrão o Azure MFA. Se utilizar o serviço de federação para MFA, pode configurar o Azure AD para redirecionar para o serviço de federação quando for necessário MFA, ao definir `-SupportsMFA` como `$true` no [PowerShell](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings). Esta definição funciona para os serviços de autenticação federada que suportam o pedido de desafio MFA emitido pelo Azure AD com `wauth= http://schemas.microsoft.com/claims/multipleauthn`.

Depois de o utilizador ter iniciado sessão no serviço de autenticação federada, o Azure AD processa outros requisitos de política, como a conformidade do dispositivo ou uma aplicação aprovada.

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

Clientes com [licenças de Microsoft 365 Business](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) também têm acesso aos recursos de acesso condicional. 

## <a name="next-steps"></a>Passos Seguintes

Para saber como implementar o acesso condicional em seu ambiente, consulte [planejar sua implantação de acesso condicional no Azure Active Directory](plan-conditional-access.md).
