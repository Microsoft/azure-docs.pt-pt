---
title: Monitorizar a identidade e o acesso no Centro de Segurança do Azure | Microsoft Docs
description: Saiba como utilizar a capacidade de identidade e acesso no Centro de Segurança do Azure para monitorizar a atividade de acesso dos seus utilizadores e os problemas relacionados com identidades.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/16/2020
ms.author: memildin
ms.openlocfilehash: 152168bbadd5815659bc5f70c91bd2a28f5e049d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481964"
---
# <a name="monitor-identity-and-access"></a>Monitorizar a identidade e o acesso

> [!TIP]
> A partir de março de 2020, as recomendações de identidade e acesso do Azure Security Center estão incluídas em todas as subscrições no nível de preços gratuitos. Se tiver subscrições no nível livre, o seu Secure Score será afetado, uma vez que não foram previamente avaliados pela sua identidade e segurança de acesso. 

Quando o Security Center identifica potenciais vulnerabilidades de segurança, cria recomendações que o guiam através do processo de configuração dos controlos necessários para endurecer e proteger os seus recursos.

O perímetro de segurança evoluiu de um perímetro de rede para um perímetro de identidade. A segurança torna-se menos sobre defender a sua rede e mais sobre a defesa dos seus dados, bem como sobre a gestão da segurança das suas apps e utilizadores. Hoje em dia, com mais dados e mais aplicações a mudarem para a cloud, a identidade torna-se no novo perímetro.

Ao monitorizar as atividades identitárias, pode tomar ações proactivas antes de ocorrer um incidente, ou ações reativas para parar uma tentativa de ataque. Por exemplo, o Security Center pode sinalizar contas depreciadas (contas que já não são necessárias, e bloqueadas de assinar pelo Azure Ative Directory) para remoção. 

Exemplos de recomendações que pode ver na secção de segurança de **recursos de identidade e acesso** do Centro de Segurança Azure incluem:

- MFA deve ser ativado em contas com permissões do proprietário na sua subscrição
- Um máximo de 3 proprietários deve ser designado para a sua subscrição
- As contas externas com permissões de leitura devem ser removidas da sua subscrição
- As contas depreciadas devem ser removidas da sua subscrição

Para mais informações sobre estas recomendações, bem como uma lista completa das recomendações que pode ver aqui, consulte recomendações de [Identidade e Acesso.](recommendations-reference.md#recs-identity)

> [!NOTE]
> Se a sua subscrição tiver mais de 600 contas, o Security Center não poderá executar as recomendações de Identidade contra a sua subscrição. As recomendações que não são executadas estão listadas em "avaliações indisponíveis" abaixo.
O Security Center não pode executar as recomendações de Identidade contra os agentes administrativos de um parceiro de solução cloud (CSP).
>


Todas as recomendações de identidade e acesso estão disponíveis em dois controlos de segurança na página **recomendações:**

- Gerir acessos e permissões 
- Ativar MFA

![Os dois controlos de segurança com as recomendações relacionadas com identidade e acesso](media/security-center-identity-access/two-security-controls-for-identity-and-access.png)


## <a name="enable-multi-factor-authentication-mfa"></a>Ativar a autenticação de vários fatores (MFA)

Ativar o MFA requer [permissões de inquilinos do Azure Ative Directory (AD).](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

- Se tiver uma edição premium de AD, ative o MFA utilizando [acesso condicional.](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- Os utilizadores da edição gratuita de AD podem ativar falhas de **segurança** no Diretório Ativo do Azure, conforme descrito na [documentação ad,](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) mas a recomendação do Security Center para ativar o MFA continuará a aparecer.


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre recomendações aplicáveis a outros tipos de recursos Azure, consulte os seguintes artigos:

- [Proteger as máquinas e aplicações no Centro de Segurança do Azure](security-center-virtual-machine-protection.md)
- [Proteger a sua rede no Centro de Segurança do Azure](security-center-network-recommendations.md)
- [Proteger o seu serviço Azure SQL e dados no Azure Security Center](security-center-sql-service-recommendations.md)