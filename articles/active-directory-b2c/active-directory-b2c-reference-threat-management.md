---
title: Gerir as ameaças aos recursos e dados no Azure Active Directory B2C | Documentos da Microsoft
description: Saiba mais sobre técnicas de deteção e mitigação de ataques denial-of-service e ataques de palavra-passe no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f961e873bb230e4e6d7e14b03e2664b7f987974e
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508906"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Gerir as ameaças aos recursos e dados no Azure Active Directory B2C

O Azure Active Directory (Azure AD) B2C tem recursos incorporados que podem ajudar a proteger contra ameaças para os dados e recursos. Estas ameaças incluem ataques denial-of-service e ataques de palavra-passe. Ataques denial-of-service podem tornar recursos indisponível para usuários. Ataques de palavra-passe levam ao acesso não autorizado aos recursos. 

## <a name="denial-of-service-attacks"></a>Ataques denial-of-service

O Azure AD B2C faz a defesa contra ataques de inundação SYN através de um cookie SYN. O Azure AD B2C também protege contra ataques de negação de serviço através de limites para ligações e taxas.

## <a name="password-attacks"></a>Ataques de palavra-passe

Palavras-passe que são definidas por utilizadores têm de ser razoavelmente complexo. O Azure AD B2C tem técnicas de atenuação para ataques de palavra-passe. Atenuação inclui os ataques de palavra-passe de dicionário e ataques de força bruta a senhas. Ao utilizar vários sinais, o Azure AD B2C analisa a integridade dos pedidos. O Azure AD B2C foi concebido para inteligência diferenciar usuários de hackers e botnets. 

O Azure AD B2C utiliza uma estratégia sofisticada para bloquear as contas. As contas estão bloqueadas com base no IP do pedido e as palavras-passe que introduziu. A duração do bloqueio também aumenta com base na probabilidade que é um ataque. Depois de uma palavra-passe é experimentada 10 vezes sem êxito, ocorre um bloqueio de um minuto. Da próxima vez que um início de sessão for concluído com êxito depois da conta está desbloqueada, outro bloqueio de um minuto ocorre e continua para cada início de sessão sem êxito. Introduzir a mesma palavra-passe repetidamente não contabiliza como vários inícios de sessão sem êxito. 

Os períodos de 10 bloqueio primeiro são longo de um minuto. Os períodos de bloqueio de 10 a seguir são ligeiramente maiores e aumentam a duração depois de todos os períodos de bloqueio de 10. Repõe o contador de bloqueio para zero após um início de sessão com êxito, quando a conta não está bloqueada. Períodos de bloqueio podem sobreviver a várias até cinco horas. 

Atualmente, não é possível:

- Acionar um bloqueio com menos de 10 inícios de sessão falhados
- Obter uma lista de contas bloqueadas horizontalmente
- Configurar o bloqueio da política

Para obter mais informações, visite o [Microsoft Trust Center](https://www.microsoft.com/trustcenter/default.aspx).
