---
title: Determine a sua postura de segurança para colaboração externa com o Azure Ative Directory
description: Antes de poder executar um plano de segurança de acesso externo, tem de determinar o que está a tentar alcançar.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37c27e84f15a01a2d8832baae137518685de59a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98725446"
---
# <a name="determine-your-security-posture-for-external-access"></a>Determine a sua postura de segurança para acesso externo 

Ao considerar o acesso externo, terá de avaliar as necessidades de segurança e colaboração para a sua organização em geral, e dentro de cada cenário. A nível organizacional, considere a quantidade de controlo que precisa da sua equipa de TI para ter ao longo do dia-a-dia de colaboração. As organizações das indústrias regulamentadas podem exigir mais controlo das TI. Por exemplo, um empreiteiro de defesa pode ser obrigado a identificar e documentar positivamente cada utilizador externo, o seu acesso e a remoção de acessos. Este requisito pode estar em todos os acessos, ou em cenários ou cargas de trabalho específicos. Do outro lado do espectro, uma empresa de consultoria pode geralmente permitir que os utilizadores finais determinem os utilizadores externos com quem precisam de colaborar, dentro de certos trilhos de proteção de TI. 

![TI versus controlo do utilizador final da colaboração](media/secure-external-access/1-overall-control.png)

> [!NOTE]
> Um controlo excessivamente apertado da colaboração pode conduzir a orçamentos de TI mais elevados, à redução da produtividade e ao atraso nos resultados das empresas. Quando os canais oficiais de colaboração são vistos como demasiado onerosas, os utilizadores finais tendem a circular em it fornecendo sistemas para fazer o seu trabalho, por exemplo, enviando e-mails de documentos não protegidos.

## <a name="think-in-terms-of-scenarios"></a>Pense em termos de cenários

Em muitos casos, a TI pode delegar o acesso a parceiros, pelo menos em alguns cenários, ao mesmo tempo que fornece grades de guarda para segurança. Os trilhos de proteção de TI podem ajudar a garantir que a propriedade intelectual se mantenha segura, ao mesmo tempo que capacita os colaboradores a colaborar com os parceiros para que o trabalho seja feito.

Ao considerar os cenários dentro da sua organização, avalie a necessidade de acesso de colaboradores versus parceiros de negócios aos recursos. Um banco pode ter necessidades de conformidade que restringem o acesso a determinados recursos, como a informação da conta de utilizador, a um pequeno grupo de funcionários internos. Inversamente, o mesmo banco pode permitir o acesso delegado aos parceiros que trabalham numa campanha de marketing.

![continuidade da governação por cenário](media\secure-external-access\1-scenarios.png)

Em cada cenário, considere 

* a sensibilidade da informação em risco

* se você precisa restringir o que os parceiros podem ver sobre outros utilizadores

* o custo de uma violação vs o peso do controlo centralizado e do atrito do utilizador final

 Pode também começar com controlos geridos centralmente para cumprir os objetivos de conformidade e delegar o controlo aos utilizadores finais ao longo do tempo. Todos os modelos de gestão de acessos podem coexistir simultaneamente dentro de uma organização. 

A utilização de [credenciais geridas por parceiros](../external-identities/what-is-b2b.md) fornece à sua organização um sinal essencial que termina o acesso aos seus recursos assim que o utilizador externo perdeu o acesso aos recursos da sua própria empresa.

## <a name="goals-of-securing-external-access"></a>Objetivos de assegurar o acesso externo

Os objetivos do acesso governado por TI e delegados diferem.

**Os principais objetivos do acesso governado pelas TI são:**

* Cumprir metas de governação, regulação e conformidade (GRC). 

* Controle firmemente o acesso dos parceiros e o que os parceiros podem ver sobre utilizadores, grupos e outros parceiros dos membros.

**Os principais objetivos de delegar o acesso são:**

* Permitir que os empresários governem com quem colaboram, dentro dos constrangimentos de TI.

* Permitir que os parceiros de negócios solicitem o acesso com base nas regras definidas pelos empresários.

Seja qual for a sua organização e cenários, terá de o fazer: 

* **Controlar o acesso a aplicações, dados e conteúdos.** Isto pode ser realizado através de uma variedade de métodos, dependendo das suas versões de [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) e [Microsoft 365](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans). 

* **Reduza a superfície de ataque.** [Gestão privilegiada de identidade,](../privileged-identity-management/pim-configure.md) [prevenção de perda de dados (DLP)](/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) e [capacidades de encriptação](/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) reduzem a superfície de ataque.

* **Reveja regularmente a atividade e o registo de auditoria para confirmar a conformidade**. A TI pode delegar decisões de acesso aos empresários através da gestão de direitos, enquanto as revisões de acesso fornecem uma forma de confirmar periodicamente o acesso continuado. A classificação automatizada de dados com etiquetas de sensibilidade ajuda a automatizar a encriptação de conteúdos sensíveis, facilitando o cumprimento dos utilizadores finais dos colaboradores.

## <a name="next-steps"></a>Passos seguintes 

Consulte os seguintes artigos sobre a garantia do acesso externo aos recursos. Recomendamos que tome as ações na ordem listada.

1. [Determine a sua postura de segurança para acesso externo](1-secure-access-posture.md) (Está aqui.)

2. [Descubra o seu estado atual](2-secure-access-current-state.md)

3. [Criar um plano de governação](3-secure-access-plan.md)

4. [Utilize grupos para segurança](4-secure-access-groups.md)

5. [Transição para Azure AD B2B](5-secure-access-b2b.md)

6. [Acesso seguro com Gestão de Direitos](6-secure-access-entitlement-managment.md)

7. [Acesso seguro com políticas de acesso condicional](7-secure-access-conditional-access.md)

8. [Acesso seguro com etiquetas de sensibilidade](8-secure-access-sensitivity-labels.md)

9. [Acesso seguro a Microsoft Teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md)
 

